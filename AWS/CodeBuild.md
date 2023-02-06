# CodeBuild
코드를 Build 하여 배포할수있게 전환시켜주는 솔루션

# Build
여러 복잡한 코드들을 실행가능한 산출물로 (exe, 설치파일 등등) 만드는 과정
> - 소스코드를 바로 실행시킬 수 없는지?  
> -> 코드만 있다고 바로 동작할 수 있는것이 아니라 환경이 갖춰져야 실행시킬 수 있다. 모래깔린 운동장(코드 실행 환경)에서 수영(목적 동작)을 할 수는 없는것처럼. 코드를 동작시키기 위해 일련의 과정이 이미 정해져있고, 각 과정을 수행시키는 프로그램들도 존재한다. 빌드는 이 모든 과정을 통합하여 내포하고있다.

# CodeBuild 수행단계
1. [소스코드 생성](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-create-source-code-console.html)
2. [buildspec 파일 생성](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-create-build-spec-console.html)
3. [2개의 S3 버킷 생성](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-input-bucket-console.html)
4. [소스 코드 및 buildspec 파일 업로드](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-upload-source-code-console.html)
5. [빌드 프로젝트 생성](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-create-build-project-console.html)
6. [빌드 실행](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-run-build-console.html)
7. [요약된 빌드 정보 보기](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-monitor-build-console.html)
8. [자세한 빌드 정보 보기](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-build-log-console.html)
9. [빌드 출력 아티팩트 가져오기](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-output-console.html)
10. [S3 버킷 삭제](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-clean-up-console.html)
11. [마무리](https://docs.aws.amazon.com/ko_kr/codebuild/latest/userguide/getting-started-next-steps-console.html)

## 소스코드 생성
동작 수행을 위한 code 작성

## buildspec 파일 생성
```yml
# 빌드 사양 표준의 버전. 0.2가 최신버전
version: 0.2

# 빌드 수행 단계
phases:
  # 패키지 설치단계 (변수값 사용가능)
  install:
    # 실행환경 (선택)
    runtime-versions:
      java: corretto8
      python: 3.x
      ruby: "$MY_RUBY_VAR"
  # CodeBuild 전에 실행할 추가 명령어 (ECR 로그인, npm 설치 등)
  pre_build:
    # 
    commands:
      - echo Nothing to do in the pre_build phase...
  # CodeBuild 중에 실행할 추가 명령어
  build:
    commands:
      - echo Build started on `date`
      - mvn install
  # CodeBuild 실행 수에 실행할 추가 명령어 (JAR, WAR 패키징, Docker 이미지를 ECR에 푸시)
  post_build:
    commands:
      - echo Build completed on `date`
# 위치에 대한 정보 (테스트시 미작성)
artifacts:
  # 빌드 환경의 빌드 출력 결과물을 포함하는 위치
  files:
    - target/messageUtil-1.0.jar
```

## 2개의 S3 버킷 생성
> 버킷을 2개 생성하는 이유
> - 빌드 입력이 들어오는 위치 및 빌드 출력이 나가는 위치를 쉽게 확인할 수 있다고 함
> - 하나는 입력 버킷, 하나는 출력 버킷으로 사용
>   - Name : input-bucket, output-bucket

## 소스 코드 및 buildspec 파일 업로드
작성한 코드 압축 후 input-bucket에 zip파일 업로드

## 빌드 프로젝트 생성
- 빌드 환경 정의
  - Docker Image 설정
  - Input 받아올 S3 버킷 및 zip 파일명 설정
  - Output 버킷 설정

## 빌드 실행
![빌드 시작 버튼](../img/[AWS]%20Codebuild%20빌드시작%20버튼.png)
빌드 시작 버튼 클릭

## 요약된 빌드 정보 보기 ~
빌드 성공여부 (진행중, 성공, 실패 등등)

## S3 버킷 삭제
지속적인 요금발생 방지용 입출력 버킷 삭제