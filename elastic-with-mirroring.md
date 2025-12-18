> 해당 문서는 Cluster Logging 오퍼레이터와 ECK 오퍼레이터가 이미 미러링되어 준비된 환경을 기준으로 작성합니다.

# 1. Cluster Logging 오퍼레이터 및 ECK 오퍼레이터 설치
> OpenShift 콘솔의 GUI 화면을 이용하여 Operator 설치

# 2. elasticsearch 컨테이너 이미지 반입
> ECK 오퍼레이터 버전에 따라 elasticsearch 이미지 버전 결정
> nexus의 도커 레지스트리에 elasticsearch 이미지를 푸시할 경로는 ocp-operators-mirror/eck로 가정.
```
$ podman pull docker.elastic.io/elasticsearch/elasticsearch:9.2.0
$ podman tag docker.elastic.io/elasticsearch/elasticsearch:9.2.0 bastion-nexus.kscada.kdneri.com:5000/ocp-operators-mirror/elasticsearch/elasticsearch:9.2.0
$ podman push bastion-nexus.kscada.kdneri.com:5000/ocp-operators-mirror/elasticsearch/elasticsearch:9.2.0
$ curl -u <id>:<pw> https://bastion-nexus.kscada.kdneri.com:5000/v2/_catalog
```

# 3. elasticsearch 컨테이너 이미지 활용을 위한 미러링
```
$ vi idms-elastic.yaml
apiVersion: config.openshift.io/v1
kind: ImageDigestMirrorSet
metadata:
  name: elastic-mirror
spec:
  imageDigestMirrors:
  - mirrors:
    - bastion-nexus.kscada.kdneri.com:5000/ocp-operators-mirror/elasticsearch
    source: docker.elastic.co/elasticsearch
```
```
$ vi itms-elastic.yaml
apiVersion: config.openshift.io/v1
kind: ImageTagMirrorSet
metadata:
  name: elastic-mirror
spec:
  imageTagMirrors:
  - mirrors:
    - bastion-nexus.kscada.kdneri.com:5000/ocp-operators-mirror/elasticsearch
    source: docker.elastic.co/elasticsearch/elasticsearch
```
```
$ oc apply -f idms-elastic.yaml
$ oc apply -f itms-elastic.yaml
$ watch oc get node,mcp
```

# 4. elasticsearch cluster 배포
```
$ vi elasticsearch.yaml

```






