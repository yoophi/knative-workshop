- Istio 설치
  id:: 62245470-b84f-4e37-981a-1a35f9ea6236
  heading:: true
	- 공식 문서의 [Installing Istio for Knative](https://knative.dev/docs/install/serving/installing-istio/) 항목을 참고하세요.
		- Istio 설치시, **without sidecar injection** 옵션으로 설치합니다.
	- Istio 공식 문서의 [Install with Istioctl](https://istio.io/latest/docs/setup/install/istioctl/) 항목을
	  참고하여 `istioctl` 파일을 다운로드 받고,
	  `istioctl` 명령행 유틸리티를 `PATH`에 설정하고 실행해줍니다.
	- ```sh
	  curl -L https://istio.io/downloadIstio | sh -
	  cd istio-1.13.0
	  export PATH=$PWD/bin:$PATH
	  ```
	- 아래 명령은 `default` 프로필을 이용해 **without sidecar injection** 방식으로
	  Istio 를 설치합니다.
	- ```
	  istioctl install -y	
	  ```
- 참고
  heading:: true
	- Istio 설치시 mTLS feature 를 사용하려면 [공식문서](https://knative.dev/docs/install/serving/installing-istio/)의 Using Istio mTLS feature 항목을 참고하세요.