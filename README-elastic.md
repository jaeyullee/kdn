# elasticsearch operator 설치

## 1. elasticsearch operator CRD 구성 yaml 준비
```
$ mkdir elasticsearch-operator
$ cd elasticsearch-operator

$ wget https://download.elastic.co/downloads/eck/3.1.0/crds.yaml
$ wget https://download.elastic.co/downloads/eck/3.1.0/operator.yaml

$ vi operator.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: elastic-operator
...
spec:
  ...
  spec:
    ...
    containers:
    - image: docker.io/elastic/eck-operator:3.1.0    ### 미러 레지스트리 주소로 변경
...
```

## 2. elasticsearch operator 이미지 준비
```
$ oc image mirror docker.io/elastic/eck-operator:3.1.0 bastion.ocp416.test:5002/elastic/eck-operator:3.1.0
$ oc image mirror docker.elastic.co/elasticsearch/elasticsearch:9.1.2 bastion.ocp416.test:5002/elastic/elasticsearch:9.1.2
```

## 3. elasticsearch operator 배포
```
$ oc create -f crds.yaml
$ oc apply -f operator.yaml

$ oc get pod -n elastic-system
```

## 4. elasticsearch 용 PV 준비
```
$ mkdir /nfs/elastic

$ vi /etc/exports
/nfs/elastic *(rw,sync,no_root_squash)

$ exports -a
$ showmount -e

$ vi elastic-pv.yaml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: elastic
spec:
  capacity:
    storage: 1Gi
  nfs:
    server: 192.168.10.21  # nfs 서버 IP
    path: /nfs/elastic
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  volumeMode: Filesystem

$ oc create -f elastic-pv.yaml
```

## 5. elasticsearch CRD 수정
```
$ oc edit elasticsearches.elasticsearch.k8s.elastic.co elasticsearch-sample
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: elasticsearch-sample
  namespace: elastic
spec:
  image: bastion.ocp416.test:5002/elastic/elasticsearch:9.1.2  # 미러 레지스트리 이미지 주소로 추가
```

## 6-1. elasticsearch 워크로드 배포 (단일 노드 elasticsearch)
```
$ vi elasticsearch.yaml
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: elasticsearch-sample
spec:
  version: 9.1.2
  nodeSets:
  - name: default
    count: 1
    config:
      node.store.allow_mmap: false
---
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: elasticsearch-sample
spec:
  tls:
    termination: passthrough
    insecureEdgeTerminationPolicy: Redirect
  to:
    kind: Service
    name: elasticsearch-sample-es-http

$ oc create -f elasticsearch.yaml
```

## 6-2. elasticsearch 워크로드 배포 (클러스터링)
> PV는 테스트 편의성을 위해 임의로 nfs 타입으로 작성했습니다. Block 스토리지 사용을 권장합니다.
```
$ vi elastic-pv-1.yaml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: elastic-pv-1
spec:
  capacity:
    storage: 5Gi
  nfs:
    server: 192.168.10.21
    path: /nfs/elastic1
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  volumeMode: Filesystem

$ vi elastic-pv-2.yaml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: elastic-pv-2
spec:
  capacity:
    storage: 5Gi
  nfs:
    server: 192.168.10.21
    path: /nfs/elastic2
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  volumeMode: Filesystem

$ vi elastic-pv-3.yaml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: elastic-pv-3
spec:
  capacity:
    storage: 5Gi
  nfs:
    server: 192.168.10.21
    path: /nfs/elastic3
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  volumeMode: Filesystem

$ oc create -f elastic-pv-1.yaml
$ oc create -f elastic-pv-2.yaml
$ oc create -f elastic-pv-3.yaml

$ vi elastic-cluster.yaml
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: elasticsearch-rwo-cluster-manual-pvc
spec:
  version: 9.1.2
  nodeSets:
  - name: es-node-1
    count: 1
    podTemplate:
      spec:
        nodeSelector:
          kubernetes.io/hostname: "worker-node-1"
    volumeClaim:
      claimName: elastic-pv-1
  - name: es-node-2
    count: 1
    podTemplate:
      spec:
        nodeSelector:
          kubernetes.io/hostname: "worker-node-2"
    volumeClaim:
      claimName: elastic-pv-2
  - name: es-node-3
    count: 1
    podTemplate:
      spec:
        nodeSelector:
          kubernetes.io/hostname: "worker-node-3"
    volumeClaim:
      claimName: elastic-pv-3

$ oc create -f elastic-cluster.yaml
```
