- 사용할 도메인(ex: `example.com`)에 대한 host zone 을 추가합니다.
- 사용할 이름 규칙에 대해 [[Kong API Gateway]]의 서비스에 의해 생성된 AWS Application Loadbalancer(이하 ALB) 로 연결하는 A Record Alias 를 설정합니다.
	- ```sh
	  kubectl get svc -n kong
	  NAME                 TYPE           CLUSTER-IP       EXTERNAL-IP                                  PORT(S)                      AGE
	  ingress-kong-proxy   LoadBalancer   10.100.101.158   a12345678.ap-northeast-2.elb.amazonaws.com   80:31170/TCP,443:30472/TCP   2d7h
	  ```
	- 위와 같은 경우, `<service-name>.<namespace>.kn.example.com`을 서비스 주소 형식으로 사용하려면, `*.kn.example.com`에 대한 A Record Alias 를 생성하고 region: ap-northeast-2 내의 `a12345678`에 해당하는 ALB로 연결합니다.