- 설치
  heading:: true
  id:: 6224516f-3447-4c51-b36d-d4a83fc31437
	- [공식 문서 - Install on Kubernetes](https://docs.konghq.com/gateway/2.8.x/install-and-run/kubernetes/)를 참고하세요.
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
- Network LoadBalancer 에 도메인 연결
  heading:: true