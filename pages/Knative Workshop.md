- # 소개
	- [[EKS]] 환경에 [[Knative]]와 [[Kong API Gateway]]를 설치하고, [[Kubernetes]] 기반의 [[Serverless]] 환경을 구성해봅니다.
## 구성 요소
	- [Elastic Kubernetes Service]([[EKS]])
	- [[Knative]]
	- [[Kong API Gateway]]
	- [[cert-manager]]
## 진행 항목
	- [EKS 서비스를 이용하여 Kubernetes Cluster 를 생성합니다.](((62245794-37bc-4873-ad2d-46e368a28a51)))
	- [[Knative]], [[Istio]] 와 [[Kong API Gateway]] 서비스를 설치합니다.
		- [Knative 설치](((62245414-02aa-4334-8bc7-03249feafd15)))
		- [Istio 설치](((62245470-b84f-4e37-981a-1a35f9ea6236)))
		- [Kong API Gateway 설치](((6224516f-3447-4c51-b36d-d4a83fc31437)))
	- [[Istio ingress gateway]] 대신 [[Kong API Gateway]] 를 사용하도록 설정합니다.
		- [Knative에서 Kong API Gateway 사용하도록 설정](((62246b4e-f8ce-4aed-ab73-70debe5e68a2)))
	- AWS [[Route53]] 서비스을 이용하여 와일드카드 도메인에 [[Knative Serving]] 서비스를
	  연결합니다.
		- [Route53 설정]([[Knative를 위한 Route53 설정]])
	- ((624454f0-0400-4730-b199-0aa03c9e129f))
	- [[cert-manager]] 를 이용하여 [[https 인증서]]를 자동으로 발급받을 수 있도록 합니다.
		- LATER cert-manager 설치
		- LATER letsencrypt-prod ClusterIssuer 추가
	- [[Enabling auto-TLS certs]]
	- ((624454f0-2cf8-44e9-b0a7-2c712ded230f))
- 업데이트 예정
	- [[Knative Serving]] 을 이용하여 private 서비스를 배포하고, 
	  [[Kong API Gateway]] 를 이용하여 host / path 기반의 [[Routing]] 을 구성합니다.
	- [[Knative Serving]] 을 이용하여 배포된 Ingress Rule 에 [[Kong Plugin]] 을 적용할 수 있도록
	  합니다.
- # Cookbook
	- [[Github Event]]로 도커 이미지를 자동으로 생성해봅니다.
	- [[Slack]] 에 Daily Summary 메시지를 전송하는 Cronjob Event 를 생성해봅니다.