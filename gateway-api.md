## 1. Gateway API 기능 활성화
> Gateway API 기능은 GA되긴했으나 기존 Ingress와의 충돌 방지 및 리소스 절약을 위해 수동으로 켜야(Opt-in) 합니다.
```
$ oc patch featuregate cluster --type='merge' -p '{"spec": {"featureSet": "TechPreviewNoUpgrade"}}'
```

## 2. Service Mesh 3 Operator 설치
> OpenShift 4.20의 "Native Gateway API" 기능은 내부적으로 "Red Hat OpenShift Service Mesh v3 (Sail Operator)"를 엔진으로 사용하기 때문에 설치가 필요합니다.

## 3. Gateway API 배포
```
$ vi gatewayclass.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: openshift-default
spec:
  controllerName: openshift.io/gateway-controller/v1

$ oc create -f gatewayclass.yaml
$ oc get gatewayclass -n openshift-default
$ oc get deployment -n openshift-ingress
```

## 4. Gateway 생성
> 공유 Gateway를 쓸 경우 포트 중복 문제가 발생할 수 있기 때문에 서비스 별 전용 Gateway를 사용하는 방식으로 진행합니다. <br/>
> 각 서비스 네임스페이스에 Gateway를 배포할 경우 관리가 힘들어지기 때문에 Gateway 전용 네임스페이스를 이용합니다.
```
$ oc new-project gateway-ns
$ oc annotate namespace gateway-ns openshift.io/node-selector="node-role.kubernetes.io/router=" --overwrite
$ oc get namespace gateway-ns -o yaml
```

## 5. TCPRoute 생성
```
$ vi tcproute.yaml
apiVersion: gateway.networking.k8s.io/v1alpha2
kind: TCPRoute
metadata:
  name: echo-route
  namespace: tcp-app-ns      # TCP 서비스 파드가 있는 네임스페이스 지정
spec:
  parentRefs:
  - name: my-tcp-gateway
    namespace: gateway-ns    # Gateway가 있는 네임스페이스 지정
   namespace: gateway-ns     # Gateway가 있는 
  rules:
  - backendRefs:
    - name: tcp-echo-service
      port: 9000

$ oc create -f tcproute.yaml
```
