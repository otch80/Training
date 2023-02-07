# 람다란?

- 람다 바이블 (내용 출처)   
=> https://www.44bits.io/ko/keyword/aws-lambda


> 정의
> -
> - 서버를 프로비저닝 또는 관리하지 않고도 실제로 모든 유형의 애플리케이션 또는 백엔드 서비스에 대한 코드를 실행할 수 있는 <U>이벤트 중심</U>의 **서버리스** 컴퓨팅 서비스 
> - 코드실행 서비스 (.py 파일을 올리면 이벤트에 맞춰 실행시켜줌)


> 서버를 프로비저닝 하다  
> -
> - [Red Hat] 필요한 리소스를 기반으로 네트워크에서 사용될 서버를 설정하는 프로세스  
> -> 어떤 서버를 쓸지 직접 설정하는 과정  
> -> 서버리스 그 자체 (내가 직접 서버를 구축하는 과정이 필요없고 함수로만 결과물을 내보자, 제공해주는 서비스 이용)


> 이벤트 중심의 컴퓨팅 서비스
> -
> - 이벤트 => 사용자가 키보드를 입력하던 크롬을 실행시키던 등 컴퓨터에서 발생하는 모든 신호를 뜻함. 이벤트가 발생하면 미리 정해둔 다른 동작를 수행하기 위한 트리거로 사용할 수 있음
> - 컴퓨팅 서비스 => 돈을 내면 원하는 만큼의 성능을 가진 컴퓨터에 접근할 수 있게 해주겠다


> 특징
> -
> - 비용산정 방식 : 1ms (EC2는 초 단위 비용산정)
> - 지원 언어 : JavaScript, Python, Ruby, Node.js, Java, .NET, Go 등 (리눅스에서 실행 가능한 임의 바이너리도 포함)
> - 컴퓨팅 자원
>   - 128 ~ 3,008MB 사이 (64MB 단위 설정가능, 1,792MB 메모리의 경우 1vCPU와 동일)


> 람다 호출방식
> - 클라우드와치 이벤트 (시간(크론잡), 다른 서비스 이벤트)
> - S3 객체 이벤트 (객체 변경 이벤트 - 파일추가, 변경 등)
> - 외부 이벤트 (이벤트브릿지 - 데이터촉, 뉴렐릭, 페이저듀티, 젠데스크 등)
> - API 게이트웨이 (REST API, HTTP API)


> 호출 이후 동작방식
> 


람다 (2014.11)