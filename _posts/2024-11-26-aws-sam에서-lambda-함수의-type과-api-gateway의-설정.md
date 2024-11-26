---
title: AWS SAM에서 Lambda 함수의 Type과 API Gateway의 설정
description: Lambda 함수의 이벤트로 API를 지정하면, SAM이 자동으로 API Gateway를 생성하고 설정합니다.
author: dev-thug
date: 2024-11-26 15:14
categories:
  - AWS SAM
tags:
  - Lambda
  - APIGateway
pin: true
math: true
mermaid: true
---
**SAM 템플릿에서 Lambda 함수를 정의할 때,** Events **섹션에 API Gateway 이벤트를 추가하면 SAM이 자동으로 API Gateway를 생성합니다.** 이는 SAM이 제공하는 편의 기능으로, 별도의 API Gateway 리소스를 명시적으로 정의하지 않아도 Lambda 함수와 연동된 API를 손쉽게 만들 수 있습니다.

**SAM 템플릿에서 Lambda 함수를 정의하는 예제**
```yaml
Resources:
  HelloWorldFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: hello_world/
      Handler: app.lambda_handler
      Runtime: python3.9
      Architectures:
		- x86_64
      Events:
        HelloWorld:
          Type: Api
          Properties:
            Path: /hello
            Method: get
```
위 템플릿에서:
- HelloWorldFunction은 Lambda 함수를 정의하고 있습니다.
- Events 섹션에서 HelloWorld 이벤트가 정의되어 있으며, Type이 Api로 설정되어 있습니다.
- Properties에는 Path와 Method가 지정되어 있습니다.

**SAM의 동작 방식:**

1. **API Gateway 리소스 생성**: Type: Api로 정의된 이벤트를 감지하여, SAM은 자동으로 API Gateway REST API를 생성합니다.
2. **경로 및 메서드 설정**: Properties에 지정된 Path (/hello)와 Method (get)에 따라 API의 엔드포인트를 구성합니다.
3. **통합 설정**: 생성된 API Gateway 엔드포인트와 Lambda 함수를 연결합니다. 이를 통해 해당 경로로 HTTP 요청이 들어오면 Lambda 함수가 실행됩니다.
4. **IAM 권한 부여**: API Gateway가 Lambda 함수를 호출할 수 있도록 필요한 권한을 설정합니다.

**즉,** SAM 템플릿에서 Lambda 함수의 이벤트로 API를 지정하면, SAM이 자동으로 API Gateway 리소스를 생성하고 필요한 설정을 모두 처리해 줍니다. 개발자는 복잡한 API Gateway 설정을 직접 할 필요 없이 간단한 정의만으로 Lambda 함수와 연동된 API를 만들 수 있습니다.


---

**추가 정보**

- **명시적인 API Gateway 정의**: 만약 API Gateway에 대해 더 상세한 설정이 필요하다면, AWS::Serverless::Api 리소스를 직접 정의할 수 있습니다. 이를 통해 인증, 캐싱, 사용자 지정 도메인 등 고급 설정을 적용할 수 있습니다.
```yaml
Resources:
  MyApi:
    Type: AWS::Serverless::Api
    Properties:
      StageName: Prod
      EndpointConfiguration: REGIONAL
      # 추가 설정 가능

  HelloWorldFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: hello_world/
      Handler: app.lambda_handler
      Runtime: python3.9
      Events:
        HelloWorld:
          Type: Api
          Properties:
            RestApiId: !Ref MyApi
            Path: /hello
            Method: get
```
- **SAM의 편의성**: SAM은 서버리스 애플리케이션 개발을 단순화하기 위해 반복적이고 보일러플레이트한 설정을 자동화합니다. 이를 통해 개발자는 비즈니스 로직에 더 집중할 수 있습니다.


---

**요약**
- **Lambda 함수의 이벤트로 API를 지정하면, SAM이 자동으로 API Gateway를 생성하고 설정합니다.**
- **별도의 API Gateway 리소스를 정의하지 않아도 Lambda 함수와 연동된 API 엔드포인트를 사용할 수 있습니다.**
- **더 복잡한 설정이 필요한 경우,** AWS::Serverless::Api **리소스를 사용하여 API Gateway를 명시적으로 정의할 수 있습니다.**