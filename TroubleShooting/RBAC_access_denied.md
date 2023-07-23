# RBAC: access denied

### InferenceService url로 host 요청을 보내는 과정에서 RBAC: access decied ERROR가 발생
### Istio 내에서 해당 namespace가 서비스 및 워크로드에 대한 접근 권한을 차단
### 따라서 Authorization Policy를 정의하는 yaml 파일을 생성 후, 클러스터 내에 적용하면 namespace의 요청이 허용

    apiVersion: security.istio.io/v1beta1
    kind: AuthorizationPolicy
    metadata:
      name: allow-all
      namespace: kubeflow-user-example-com
    spec:
     rules:
     - {}

    
