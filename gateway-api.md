## 1. Gateway API 기능 활성화
> Gateway API 기능은 GA되긴했으나 기존 Ingress와의 충돌 방지 및 리소스 절약을 위해 수동으로 켜야(Opt-in) 합니다.
```
$ oc patch featuregate cluster --type='merge' -p '{"spec": {"featureSet": "TechPreviewNoUpgrade"}}'
```

## 2. Gateway API 배포
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
