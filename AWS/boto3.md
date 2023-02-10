# Boto3
  
Python으로 AWS 솔루션들을 제어하기 위한 파이썬 라이브러리 (AWS SDK, SDK - Software Development Kit = 개발도구)
AWS에 접근하기 위한 low-level의 객체지향 API를 제공하는 특징이 있다




# low-level
> Close to the hardware. It refers to writing software that drives the hardware directly without going through a software translation layer and its associated overhead. An operating system can provide both low- and high-level programming interfaces (APIs). A low-level API lets programmers access more intricate hardware features but requires more coding. In contrast, a high-level API may be capable of performing extensive processing with one function call from the application.  
> 출처 - https://www.pcmag.com/encyclopedia/term/low-level-interface

- 요약) 하드웨어에 직접 접근하는 수준




## 초기 S3 연결 방법

1. Client 생성 
```python
import boto3
#  create low-level client
s3_client = boto3.client('s3') # 's3'는 service name (string)


# config, Proxy 포함
import boto3
from botocore.config import Config

proxy_definitions = {
    'http': 'http://proxy.amazon.com:6502',
    'https': 'https://proxy.amazon.org:2010'
}

# 필요에 맞춰 설정
my_config = Config(
    region_name = 'us-west-2', # 리전
    signature_version = 'v4', # 서명 버전, default : v4, 만료 기간 7일 이상 : v2
    retries = { # 재시도 설정값
        'max_attempts': 10,
        'mode': 'standard'
    },
	proxies=proxy_definitions # 프록시 설정
)

client = boto3.client('s3', config=my_config)
```




2. 버킷 접근용 URL 생성
```python
import logging
import boto3
from botocore.exceptions import ClientError


# method_parameters = {
						'Bucket': bucket_name, # 접근할 버켓명
						'Key': object_name # 접근할 파일
					  }

 def create_presigned_url_expanded(client_method_name, method_parameters=None,
                                  expiration=3600, http_method=None):
    """Generate a presigned URL to invoke an S3.Client method

    Not all the client methods provided in the AWS Python SDK are supported.

    :param client_method_name: Name of the S3.Client method, e.g., 'list_buckets'
    :param method_parameters: Dictionary of parameters to send to the method
    :param expiration: Time in seconds for the presigned URL to remain valid
    :param http_method: HTTP method to use (GET, etc.)
    :return: Presigned URL as string. If error, returns None.
    """

    # Generate a presigned URL for the S3 client method
    s3_client = boto3.client('s3')
    try:
        response = s3_client.generate_presigned_url(ClientMethod=client_method_name, # 호출명
                                                    Params=method_parameters, # client_method_name 으로 보낼 파라미터 (dict 형태)
                                                    ExpiresIn=expiration, # url 유지기간 (최대 1주일)
                                                    HttpMethod=http_method) # HTTP 메서드인 GET, POST, PUT, DELETE 등 방식이 있지만, boto3에서는 자동으로 골라주기 때문에 굳이 넣지 않는걸 권장
    except ClientError as e:
        logging.error(e)
        return None

    # The response contains the presigned URL
    return response # 접속 가능 URL 반환
```

# 간단 용어 정리

- 객체
  - S3에 저장된 데이터 하나 하나. 개별 파일  
- 버킷
    - 객체가 파일이라면 버킷은 연관된 객체들을 그룹핑한 최상위 디렉토리. 버킷 단위로 지역 지정, 버킷에 포함된 모든 객체에 대해서 일괄적 인증 및 접속 제한 가능
