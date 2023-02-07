# Glue


> Glue 정의
> - 
> - 서버리스 기반의 데이터 통합 서비스
> - 트리거 기반(이벤트, 스케줄 등) 으로 작업 (ETL, 오케스트레이션 등) 수행
> - API 작업을 호출하여 데이터를 변환하고, 런타임 로그를 생성하고, 작업 로직을 저장하고, 작업 실행을 모니터링하는데 도움이 되는 알림을 생성한다. (출처 : AWS)


> 동작과정
> -
> - ETL
>   - 데이터 카탈로그를 기반으로 크롤러 정의
>   - 어디서 무엇을 가져와서 어떤 작업을 하고, 결과를 어디에 저장할지 설정한다 
> - 트리거 기반 동작
>   - 스케줄 혹은 이벤트 발생 시 AWS Glue의 Apache Spark 환경에서 코드 실행


> 데이터베이스 카탈로그
> -
> 데이터베이스의 개체들에 대한 정의를 담고 있는 <U>**메타데이터**</U>들로 구성된 데이터베이스 내의 인스턴스. 일반적으로 DBMS는 특정 데이터베이스의 구조를 파악하기 위해여 데이터의 타입이나 포맷과 같은 정보를 데이터베이스 카탈로그로부터 얻는다. (출처 : 위키백과)  
> -> DB 정보
> - 사용 이유  
>   데이터를 이동하지 않고도 여러 AWS 데이터 세트 전체에서 신속하게 데이터를 검색할 수 있다. 일단 데이터가 카탈로그에 저장되면 Amazon Athena, Amazon EMR 및 Amazon Redshift Spectrum에서 즉시 검색 및 쿼리에 데이터를 사용할 수 있다. (출처 : AWS)


> Glue 만 사용해서 모델링을 할 수 있는가
> 

> 참고
> - https://docs.aws.amazon.com/ko_kr/glue/latest/dg/how-it-works.html
> - https://docs.aws.amazon.com/ko_kr/glue/latest/dg/components-key-concepts.html
> - https://www.redhat.com/ko/topics/automation/what-is-orchestration
> - 