# Oracle에서 사용하는 Sort 알고리즘

> A. Oracle 에서 공식적으로 밝힌 내용은 없다. Open Source를 분석하거나 관련 전문가의 분석글을 통해 알려진 정보들이 대부분이다. (애초에 이런걸 궁금해하는 사람이 전세계적으로 거의 없는 듯 하다)


> - Oracle 10g R2 이전
>   - Insertion Sort (Group By를 사용하면 자동 정렬)
> - Oracle 10g R2 이후
>   - Hash Based (Group By를 사용해도 자동 정렬되지 않음)
>   - 'Oracle 10g R2 New Feature - New In-Memory Sor 알고리즘' 이 많은 양의 데이터를 정렬할 경우 기존방식보다 월등한 성능을 낸다고 함

> 기존 방식의 알고리즘을 사용하는 방법
> -
> 1. Optimizer Mode를 Rule일 경우 발생 안함
> 2. OPTIMIZER_FEATURES_ENABLE를 10.1로 함
> 3. init.ora "-gby_hash_aggregation_enabled"="FALSE 로 변경함


> 의문
> -
> - 기존의 Group By 를 반드시 써야하는 이유가 있을까? Group By 와 Order By 를 같이 쓰면 비효율 적이지 않을까?
>   - Group By, Order By를 함께 사용해도 Sort는 1번만 발생
>   - Radix sort를 사용한다는 자료도 존재
>       - Radix Sort
>           - 버킷 기반 동작
>           - 맨 끝자리 부터 맨 앞자리 까지 각 자리수 크기 비교 (stack 방식)
>           - https://lktprogrammer.tistory.com/48



> Insertion sort 채택 이유 
> -
> - 안정성  
> 삽입 정렬은 비교 기반 정렬 알고리즘 (선택 정렬, 힙 정렬 등) 에서 가장 적은 공간복잡도 O(1) 을 가짐  



> Insertion sort 에서 다른 알고리즘으로 변경된 이유
> -
> 삽입 정렬은 최악의 경우 (역순으로 정렬이 되어 있는 경우) O(N^2) 의 시간복잡도를 가짐  
> 가장 빠른 퀵 정렬을 채택하려 했으나, 불안정 (이미 정렬되어 있는 경우 O(N^2))  
> 결과적으로 안정적인 속도를 내기 위한 Hybrid 형태의 정렬 알고리즘 채택


- 결국 Oracle 에서 sort 발전이 이뤄진 가장 큰 이유는 안정성 ( 어떠한 상황에서도 최상의 퍼포먼스를 수행 - Radix O(nw)  // w : 자릿수 ) 때문이다.


> Q. 내부 프로세스 동작 과정
> -
> A. PGA 메모리에 Sort Area 를 할당해 정렬 수행
메모리 정렬 (in-memory Sort = Internal Sort)
디스크 정렬 (To-dist Sort = External Sort)




> PGA
> -
> - 독립 메모리
>   - Process | Program | Private Global Area
>   - 데이터베이스에 접속하는 모든 유저에게 할당되는 각각의 서버 프로세스가 독자적으로 사용하는 오라클 메모리 영역
>   - 독립적인 공간이기 때문에 Latch 메커니즘이 필요 없어서 SGA 영역보다 훨씬 빠르다
> - 정렬 공간 (Sort Area) 
>   - Order By 또는 Group BY 등의 정렬을 수행하기 위한 공간  
>   - 메모리 정렬 - 정렬 공간에서만 정렬이 완료
>   - 디스크 정렬 - 정렬 공간 메모리 부족으로 디스크 이용
> - 세션 정보 (Session Information)
>   - 서버 프로세스에 의해 추출된 결과 값을 전달하기 위해 필요한 유저 프로세스의 세션 정보 저장
> - 커서 상태 정보 (Cursor State)
>   - 해당 SQL의 Parsing 정보가 저장되어 있는 주소 저장
> - 변수 저장 정보 (Stack Space)
>   - SQL 문장에 Blind Variable를 사용했을 경우 해당 바인드 변수를 저장하는 공간
> - 관리
>   - WORKAREA_SIZE_POLICY
>   - MANUAL
>       - SORT_AREA_SIZE (정렬 공간에 대한 크기를 설정 파라미터)를 이용하여 정렬 공간을 설정
>       - AUTO
>           - PGA_AGGREGATE_TARGET (모든 세션의 PGA 크기의 합을 설정) 를 이용하여 PGA 크기 설정 (Default)
> - 디스크 정렬
>   - PGA 에서 정렬된 중간 결과집합을 Temp 테이블 스페이스의 Temp 세그먼트에 임시 저장
>   - Temp 영역에서 Sort Run 생성이 완료되면 다시 Sort Area 로 Merge
>       - Sort Run
>           - Sort Area가 찰 때마다 Temp 영역에 저장해 둔 중간 단계의 집합
> - SGA
>   - 공유 메모리 
>       - System Global Area
>       - 모든 사용자가 공유 가능하여 사용
> - Latch
>   - SGA 내부의 데이터 구조에 적용되는 리소스 보호 메커니즘
>   - 해당 프로세스가 Latch 를 획득해야 SGA 에 접근할 수 있다 (Latch 없으면 조회 및 가공 등 모든 행위 불가)
>   - 단일 명령어 메모리 구조에 대한 접근을 제어, Dead-Lock 이 발생하지 않도록 구현
>   - 0 ~ 13 Level 을 가짐, 각 Level 은 우선순위를 표현 (선점형)
>       - 1-Level  의 Latch 를 보유한 프로세스는 다음 작업을 위하여 반드시 2 이상의  Latch를  요구
>       - 1-Level 의 프로세스가 0-Level  의  Latch 가  필요하면  dead lock을  피하기  위하여  No  Wait  모드로  Latch  획득을  시도하고,  이  시도가  실패를  하게  되면 latch 를  해지한  후  No  wait  모드로  획득을  시도
>       - Queue 처럼 보이지만 Latch 는 순서를 보장하지 않는다.
>       - 기본적으로 exclusive 모드로 동작 (하나의 세션만 Lock 획득 가능, Shared 모드일 때는 여러 세션이 동시에 Lock 획득 가능)
> - Oracle Process Memory = SGA + PGA 합계
>   - 대용량 Hash join을 하거나 정렬 작업을 하면 PGA 사용이 증가



> Referrence
> -
> - https://hellodba.com/reader.php?ID=185&lang=EN
> - https://boeok.tistory.com/entry/
> - PGAProgram-Global-Area%EC%9D%98-%EA%B0%9C%EB%85%90
> - https://jungmina.com/795
> - http://www.dba-oracle.com/t_oracle_sorting.htm
> - https://oracle-randolf.blogspot.com/2011/01/hash-aggregation.html
> - https://jonathanlewis.wordpress.com/2008/11/25/sas-bug/