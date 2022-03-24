- Knative란?
  heading:: true
	- TODO 내용 추가
- Knative 설치
  id:: 62245414-02aa-4334-8bc7-03249feafd15
  heading:: true
	- Operator 설치
	  heading:: true
		- 공식 문서의 [Installing using the Operator](https://knative.dev/docs/install/operator/knative-with-operators) 내용을 참고하세요.
		- ```sh
		  kubectl apply -f https://github.com/knative/operator/releases/download/knative-v1.2.0/operator.yaml
		  ```
		- Knative Operator 는 `default` namespace 에 설치됩니다.
		  아래 명령으로 현재 context 의 기본 namespace 를 `default`로 설정합니다.
			- ```sh
			  kubectl config set-context --current --namespace=default
			  ```
		- `knative-operator`가 정상 설치되었는지 확인합니다.
			- ```sh
			  kubectl get deployment knative-operator
			  ```
		- 정상 설치시, 아래와 같이 deployment가 `Ready` status로 출력됩니다.
			- ```text
			  NAME               READY   UP-TO-DATE   AVAILABLE   AGE
			  knative-operator   1/1     1            1           19h
			  ```
	- `knative-serving` Custom Resource 설치
	  heading:: true
		- `knative-serving` namespace 및 `KnativeServing` custom resource 를 생성합니다.
		  ```bash
		  cat < EOF | kubectl apply -f -
		  apiVersion: v1
		  kind: Namespace
		  metadata:
		    name: knative-serving
		  ---
		  apiVersion: operator.knative.dev/v1alpha1
		  kind: KnativeServing
		  metadata:
		    name: knative-serving
		    namespace: knative-serving
		  EOF
		  ```
- Knative 에서 [[Kong API Gateway]] 사용하도록 설정
  id:: 62246b4e-f8ce-4aed-ab73-70debe5e68a2
  heading:: true
	- Knative 의 기본 Ingress 는 Istio Ingress Gateway 입니다. 아래 과정을 통해 기본 Ingress 로 Kong 을 사용하도록 변경합니다.
	- [공식 문서](https://docs.konghq.com/kubernetes-ingress-controller/2.2.x/guides/using-kong-with-knative/)를 참고하세요.
	- `knative-serving`의 `config-network` ConfigMap 을 수정하여, `"ingress.class": "kong"` 항목을 추가합니다.
		- ```
		  kubectl patch configmap/config-network \
		    --namespace knative-serving \
		      --type merge \
		        --patch '{"data":{"ingress.class":"kong"}}'
		  ```
	- `nip.io` 또는 `sslip.io` 도메인을 사용하여 로컬 개발 환경의 domain 을 설정하려면, 아래 과정을 수행합니다.
	  collapsed:: true
		- `kong-proxy` 서비스의 `EXTERNAL-IP`를 확인합니다.
			- ```
			  kubectl get service kong-proxy -n kong
			  NAME         TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)                      AGE
			  kong-proxy   LoadBalancer   10.63.248.154   35.247.39.83   80:30345/TCP,443:31872/TCP   53m
			  ```
		- 아래는 `EXTERNAL-IP`가 `35.247.39.83`인 경우입니다.
		  collapsed:: true
			- ```
			  echo '
			  apiVersion: v1
			  kind: ConfigMap
			  metadata:
			    name: config-domain
			    namespace: knative-serving
			    labels:
			      serving.knative.dev/release: v1.1.0
			  data:
			    35.247.39.83.nip.io: ""
			  ' | kubectl apply -f -
			  configmap/config-domain configured
			  ```
	- DNS 에 custom domain 을 설정한 경우, `example.com: ""` 형태로 해당 도메인을 입력하면 됩니다.
		- ```
		  echo '
		  apiVersion: v1
		  kind: ConfigMap
		  metadata:
		    name: config-domain
		    namespace: knative-serving
		    labels:
		      serving.knative.dev/release: v1.1.0
		  data:
		    example.com: ""
		  ' | kubectl apply -f -
		  configmap/config-domain configured
		  ```
	- Kservice 배포 및 정상 작동 테스트
		- 아래 방법으로 `helloworld-go` kservice 를 배포하고 확인합니다.
			- ```
			  echo "
			  apiVersion: serving.knative.dev/v1
			  kind: Service
			  metadata:
			    name: helloworld-go
			    namespace: default
			  spec:
			    template:
			      spec:
			        containers:
			          - image: gcr.io/knative-samples/helloworld-go
			            env:
			              - name: TARGET
			                value: Go Sample v1
			  " | kubectl apply -f -
			  ```
		- 정상 작동하면, 아래와 같은 출력을 얻을 수 있습니다. 도메인을 세팅한 경우, `<your-ip>.nip.io` 대신 `example.com` 처럼 등록한 도메인을 사용하세요.
			- ```
			  curl -v http://helloworld-go.default.<your-ip>.nip.io
			  HTTP/1.1 200 OK
			  Content-Type: text/plain; charset=utf-8
			  Content-Length: 20
			  Connection: keep-alive
			  X-Kong-Upstream-Latency: 2723
			  X-Kong-Proxy-Latency: 0
			  Via: kong/1.4.3
			  
			  Hello Go Sample v1!
			  ```
-