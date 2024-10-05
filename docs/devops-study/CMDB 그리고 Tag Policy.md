##### CMDB 는 Configuration Management Database 이다.
CMDB(구성 관리 데이터베이스)는 IT 조직에서 사용하는 하드웨어, 소프트웨어, 네트워크 등의 구성요소와 그들 간의 관계를 저장하고 관리하는 중앙 집중식 데이터베이스다. 

##### CI(Configuration Items)
구성항목 이하 CI(Configuration Items)는 CMDB의 기초 구성 요소이다.
CI는 라우터, 서버, 애플리케이션, 가상머신, 컨테이너 또는 포트폴리오와 같은 논리적 구성 등 구성 관리 항목을 나타낸다.

##### CMDB의 주요기능
1. **정보 통합(Federation)**: 여러 IT 시스템의 데이터를 통합하여 360도 전방위적 인사이트를 제공한다.
2. **관계 매핑(Mapping & Visualization)**: CI 간의 관계를 시각화하여 서비스 맵을 생성한다.
3. **변경 관리(Synchronization)**: 구성 요소의 변경사항을 추적하고 영향 분석을 지원한다.
4. **문제 관리(Incident management)**: 문제 해결 시 관련 CI를 빠르게 식별하여 해결 시간을 단축한다.
5. **자산 관리(Asset Management)**: IT자산의 수명주기를 추적하고 관리한다.

##### CMDB의 이점
1. **의사결정 개선**: 정확하고 최신의 IT 환경 정보를 제공하여 더 나은 의사결정을 지원한다.
2. **운영 효율성 향상**: 인시던트 해결, 변경 관리, 문제관리 프로세스를 개선한다.
3. **규정 준수 및 보안 강화**: IT 자산의 전체 현황을 파악하여 컴플라이언스와 보안 관리를 용이하게 한다.
4. **서비스 품질 향상**: IT 서비스의 안정성과 가용성을 높여 고객 만족도를 개선한다.

##### CMDB가 사용되는 분야
- ITSM(IT Service Management)
- CSPM(Cloud Security Posture Management)

##### CMDB 구현시 고려 사항
1. 데이터 정확성: 지속적인 데이터 업데이트와 검증이 필요하다.
2. 통합: 다른 IT 관리 도구들과의 원활한 통합이 중요하다.
3. 지속적인 관리: CMDB는 지속적인 유지보수와 개선이 필요한 동적인 시스템이다.

CMDB 관련 참고자료:
[https://www.atlassian.com/ko/itsm/it-asset-management/cmdb]()
https://www.simplilearn.com/why-cmdb-itil-article
https://ikcoo.tistory.com/220
https://www.servicenow.com/products/it-operations-management/what-is-cmdb.html
https://www.servicenow.com/kr/products/servicenow-platform/configuration-management-database.html

CMDB 구축에 대한 단계까지는 아직 단계적으로 준비되지 않아 그전에 AWS 태깅을 다뤄보고자 한다.
CMDB 구축에 대한 Reference https://blog.invgate.com/how-to-build-a-cmdb
> [!NOTE]
> CMDB를 구축하는 것은 조직에서는 중장기적 목표를 가지고, 구성원간의 합의 및 노력이 필요하다. 
> 그전에 앞서 이용중에 있는 AWS 환경안에서 CMDB 구현의 토대가 될 수 있는 AWS 태깅을 통해 기본 초석을 마련해보자.
> 

## Tag Policy

 현재 우리 회사 AWS의 인프라 태깅 규칙은 없다. 
단순히 Name 에 제한적 컨벤션을 가지고, 눈으로 식별하기 좋게 만들지만, 통일 되지 않은 경우도 많고, 개발자 개개인 별로 취향에 따라 정하는 경향도 있다.
또한, 다수의 개발자가 인프라를 개별적으로 생성하므로, 프로세스 통일이라던지, 합의된 규칙 적용등이 이루어지기 어려운 형태로 관리 중이다. 
 다만, 아직은 인프라 규모가 크지 않고, 대규모의 컨테이너 기반한 시스템 보다는 다수의 Lambda와 CloudFront 배포들이 존재하고, ECS 기반한 인프라 배포는 적다. 최근들어, LLM 플랫폼을 개발해서 운영하는 환경에서는 Kubernetes 를 사용한 배포가 이루어지고 있는데, 이 또한 아직은 정확한 추적이 안되고 있다고 전달 받았다.
  또한. 빌링서비스를 사용하고 있는 지금도, 루트 계정을 통한 특정 name 기준 까지의 파악은 되어도, 실제적으로 세부적인 원하는 항목까지의 접근이 안되고 있다. 더불어, 관계 맵핑은??

그렇다면, 이번 공부를 통해 어떻게 Tag Policy를 정하고 어떤 방향으로 작성해 나갈지 정책을 정해보고자 한다.

앞서 현재 상황대로 보자면, 우리가 쓰고 있는 태그는 환경에 영향을 받고 서비스 명칭을 증명하는 정도의 네이밍 규칙으로 Name 태그만 쓰고 있었다.
 예시이므로 서비스 명은 가칭으로 하겠다.
 
 먼저  기본적인 VPC의 Name Tag는 
```
{
  "Key" : "Name",
  "Value" : "service-prod"
}
```
subnet은 
```
{
  "Key" : "Name"
  "Value" : "service-prod-pub-1"
},
{
  "Key" : "Name"
  "Value" : "service-prod-pub-2"
},
{
  "Key" : "Name"
  "Value" : "service-prod-pri-1"
},
{
  "Key" : "Name"
  "Value" : "service-prod-pri-2"
},
{
  "Key" : "Name"
  "Value" : "service-prod-db-1"
},
{
  "Key" : "Name"
  "Value" : "service-prod-db-2"
},
```
Routing Table은
```
{
  "Key" : "Name"
  "Value" : "service-prod-rt-pri"
},
{
  "Key" : "Name"
  "Value" : "service-prod-rt-pub"
}
```
Internet Gateway
```
{
  "Key" : "Name"
  "Value" : "service-prod-igw"
}
```
Nat Gateway
```
{
  "Key" : "Name"
  "Value" : "service-prod-nat"
}
```
ElasticIP
```
{
  "Key" : "Name"
  "Value" : "service-prod-nat"
}
```
EC2-bastion
```
{
  "Key" : "Name"
  "Value" : "service-prod-bastion"
}
```
EC2 Volume
```
{
  "Key" : "Name"
  "Value" : "service-prod-bastion"
}
```
Security Group(너무 많으니 Bastion Host만)
```
{
  "Key" : "Name"
  "Value" : "service-prod-bastion-sg"
}
```
모든 서비스 나열은 개선에서 해결하고, 우선 여기까지 보면 이미 네이밍을 나열만 했는데도 이미 문제가 보인다.
네이밍이 중복된(물론 실제 device ID 정보로는 갈리지만 이 조차 태그에는 없다.) 경우가 존재한다.
물론, 단순히 이정도의 단순한 구조상에서는 해당부분은 큰 문제가 아닐 수 있다. 

하지만, 장기적으로 인프라가 늘어나고 복잡해지면 어떻게 될까. 시간을 거슬러 올라 파악한다면, 흐릿해질 확률이 높을 것이다.

> [!NOTE]
> AWS Taging Best Practice를 참고해보면 어떻게 인프라에 대한 태그 기준을 잡고 진행 할지 예시를 알 수 있다.
> [https://docs.aws.amazon.com/ko_kr/whitepapers/latest/tagging-best-practices/tagging-best-practices.html]()

![[Pasted image 20240927152158.png]]리소스 이름에 대한 구조화된 접근 방식

문서를 참고하면, 태그를 구성하는 방식에 대한 소개 및 기준 등을 안내 해주며, 이후 태그 폴리시 정책으로 적용하는 방법 및 전략을 가이드 한다.

##### 태깅 분류의 조건

사례를 토대로 예측해보건데, 태그를 분류하는 관점에 따라 다양한 태그를 생성 후 관리해 볼 수 있겠다.

###### 필수 태그
1. Name: 리소스 고유 식별자이며 AWS에서는 기본제공 태그 명이다.
	ex) 'Name = book-server-01'
2. Environment: 리소스가 속한 환경.
	ex) 'Environment = production' 
3. Application: 리소스가 지원하는 애플리케이션
	ex) 'Application = customer-portal'
4. Owner : 리소스 담당자 또는 팀
	ex) 'Owner = web-team'
5. CostCenter: 비용 할당을 위한 비용관리자 정보
	ex) 'CostCenter = rnd-dept'
6. ResourceType: 장비 형태
	ex) 'ResourceType = ec2-instance '
	

###### 운영 관련 태그
1. AutoShutdown: 자동종료 여부
	ex) 'AutoShutdown =  no'
2. Compliance: 준수해야할 규정
	ex) 'Compliance = waf'
3. Version: 애플리케이션 또는 구성  버전
	ex) 'Version = 1.0.0'
4. Criticality: 비즈니스 중요도
	ex) 'Criticality = high'

###### CMDB를 만든다면
1. ConfigItem : CMDB CI 구성 ID
	ex) 'ConfigItem = CI0000000'
2. ServiceLevel : 서비스 수준 계약
	ex) 'ServiceLevel = silver'
3. BackupFrequency: 백업 주기
	ex) 'BackupFrequency = daily'
4. MaintenanceWindow: 유지보수 시간
	ex) 'MaintenanceWindow = sun-00:00~04:00'
5. DataClassification: 데이터 등급
	ex) 'DataClassification = confidential'

일단 과제 목표로는 위의 모든 조건을 수용하는 것은 어렵고, 우선적으로 상시 운영중인 서비스 하나를 기술 후 나머지 부분에 대한 고민을 해보고자 한다.

우선 Name으로만 운영하던 전체 서비스 들에 대해 기본 태그 기준을 접목해보고, 어떤 정책으로 확장해 나갈지 검토해봐야 겠다.

#### 단계를 정리해보자면 다음과 같다.
##### 1단계 : 기본 태그 정의
 - Name, Environment, Project, Application, DeviceType
##### 2단계: 태그값 표준화
- Environment
	- prod, dev
- Project
	- community-project 등 실제 프로젝트 네임
- Application
	- Application 명 (요부분은 조금 더 세분화 해볼 수 있는 포인트 같다.)
	- 일단은 fastapi-server-uvicorn
- DeviceType
	- ec2, VPC, 등등 AWS 인프라 종류 타입 
```
{
  "Name": "community-backend-1",
  "Environment": "prod",
  "Project": "community-project",
  "Application": "fastapi-server-uvicorn",
  "DeviceType": "ec2"
}
```
##### 3단계: 태그 적용 규칙 수립
1. 모든 리소스는  반드시 위의 예시인 기본 태그를 가져야 함.
2. 태그 값은 소문자로 작성.
3. 태그의 단어 분할은 하이픈을 사용.
##### 4단계: 비용 관리 태그 및 운영 태그 추가
1. CostCenter: 비용 센터 코드
2. Owner: 리소스 담당자 또는 팀
3. Backup: 백업 주기
4. MaintenanceWindow: 유지보수 시간

이후 자동화 / 정책 문서화 및 팀내 공유 / 모니터링 및 개선 과 같은 단계들이 있다.


