- 공식 문서
	- <https://knative.dev/docs/serving/using-auto-tls/>
- 아래 요소들이 설정되어 있다고 가정하고 진행합니다.
	- [[cert-manager]]가 설치되어 있습니다.
	- 사용할 도메인에 대한 `ClusterIssuer`로 `letsencrypt-prod` CR 이 설정되어 있습니다.
- 설치
	- 인증서 생성 요청을 감시하도록 설정
		- 아래 설정으로 명시적으로 제외한 namespace 외의 모든 namespace 에 대한 인증서 생성 요청을 처리할 수 있도록 합니다.
		- `networking.knative.dev/disableWildcardCert: true` 라는 `label`이 설정된 namespace는 제외합니다.
			- ```sh
			  kubectl patch --namespace knative-serving configmap config-network \
			    -p '{"data": {"namespace-wildcard-cert-selector": "{\"matchExpressions\": [{\"key\":\"networking.knative.dev/disableWildcardCert\", \"operator\": \"NotIn\", \"values\":[\"true\"]}]}"}}'
			  ```
	- 인증서 발급에 사용할 ClusterIssuer 설정
		- `config-certmanager` ConfigMap을 생성하고. 인증서 생성에 사용할 ClusterIssuer를 명시합니다.
			- ```sh
			  kubectl edit configmap config-certmanager --namespace knative-serving
			  ```
			- 아래와 같은 항목을 추가합니다.
			- ```yaml
			  data:
			    issuerRef: |
			      kind: ClusterIssuer
			      name: letsencrypt-prod
			  ```
			- 전체 ConfigMap은 아래와 비슷하게 됩니다.
			- ```yaml
			  apiVersion: v1
			  kind: ConfigMap
			  metadata:
			    name: config-certmanager
			    namespace: knative-serving
			    labels:
			      networking.knative.dev/certificate-provider: cert-manager
			  data:
			    issuerRef: |
			      kind: ClusterIssuer
			      name: letsencrypt-prod
			  ```
	- 인증서 자동 발급을 위한 Auto-TLS 설정
		- ```sh
		  kubectl edit configmap config-network --namespace knative-serving
		  ```
		- 아래와 같은 항목을 추가합니다.
		- ```yaml
		  data:
		    auto-tls: Enabled
		  ```
		- 전체 ConfigMap은 아래와 비슷하게 됩니다.
		- ```yaml
		  apiVersion: v1
		  kind: ConfigMap
		  metadata:
		    name: config-network
		    namespace: knative-serving
		  data:
		     ...
		     auto-tls: Enabled
		     ...
		  ```
	- `https` redirect 설정
		- `http-protocol` 값을 아래 중 하나로 설정하여, `http` 트래픽을 서비스할 것인지, `https`로 redirect 할 것인지 설정합니다.
			- Enabled:
				- Serve HTTP traffic.
				- `http` 프로토콜도 사용 가능.
			- Disabled:
				- Rejects all HTTP traffic.
				- `http` 연결 거부. (`https`로 redirect 하지 않음)
			- Redirected:
				- Responds to HTTP request with a 302 redirect to ask the clients to use HTTPS.
				- 302 redirect header 를 이용하여 `https`로 redirect
		- ```sh
		  kubectl edit configmap config-network --namespace knative-serving
		  ```
		- 아래와 같은 항목을 추가합니다.
		- ```yaml
		  data:
		    http-protocol: Redirected
		  ```
- 테스트
	- 아래 명령으로 Kservice를 생성하고,
	- ```sh
	  kubectl apply -f https://raw.githubusercontent.com/knative/docs/main/docs/serving/autoscaling/autoscale-go/service.yaml
	  ```
	- 서비스의 상태를 조회하면 아래와 같이 `https://`로 시작하는 URL이 표시됩니다.
	- ```sh
	  kubectl get kservice -A
	  NAME               URL                                           LATESTCREATED            LATESTREADY              READY   REASON
	  autoscale-go       https://autoscale-go.default.{custom-domain}   autoscale-go-6jf85 autoscale-go-6jf85       True  
	  ```
- 강제로 `http` 프로토콜을 사용하도록 설정하는 법
	- `networking.knative.dev/disable-auto-tls: "true"` annotations 을 추가합니다.
	- ```yaml
	   apiVersion: serving.knative.dev/v1
	   kind: Service
	   metadata:
	     annotations:
	      ...
	       networking.knative.dev/disable-auto-tls: "true"
	      ...
	  ```