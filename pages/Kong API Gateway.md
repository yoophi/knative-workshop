- 설치
  heading:: true
  id:: 6224516f-3447-4c51-b36d-d4a83fc31437
	- [공식 문서 - Install on Kubernetes](https://docs.konghq.com/gateway/2.8.x/install-and-run/kubernetes/)를 참고하세요.
	- Install on Kubernetes
		- `kong` namespace 생성
			- ```
			  ## Kong Gateway (OSS) on Kubernetes native
			  $ kubectl apply -f https://bit.ly/kong-ingress-dbless
			  ```
		- deploy
			- ```
			  ## Kong Gateway (OSS) on Kubernetes native
			  kubectl apply -f https://bit.ly/kong-ingress-dbless
			  ```
		- check the install status:
			- ```
			  kubectl get pods -n kong
			  ```
	- Install on Kubernetes using Helm
		- ```sh
		  helm repo add kong https://charts.konghq.com
		  ```
		- ```sh
		  helm repo update
		  ```
		- ```sh
		  ## Kong Gateway (OSS)
		  helm install ingress kong/kong \
		    --set ingressController.installCRDs=false
		  ```
		- 특정 docker image 를 사용한다면, 아래처럼 해당 이미지를 명시합니다.
			- ```sh
			  helm install ingress kong/kong \
			    --set image.repository=custom.repo/kong \
			    --set image.tag=2.7 \
			    --set ingressController.installCRDs=false
			  ```
	- 비고
		- Knative 의 ingress 로 [[Kong API Gateway]]를 사용하기 위해서는 `IngressClass` CR이 설정되어 있어야 합니다. 아래 명령으로 `IngressClass` 가 등록되는지 확인합니다.
			- ```sh
			  $ kubectl get ingressclass
			  NAME   CONTROLLER                      PARAMETERS   AGE
			  kong   konghq.com/ingress-controller   <none>       2d7h
			  ```
-
- Network LoadBalancer 에 도메인 연결
  heading:: true