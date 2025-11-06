## 1. 헬름 차트 다운로드 및 설정변경
```
$ helm pull bitnami/zookeeper --untar
$ vi zookeeper/values.yaml
global:
  security:
    allowInsecureImages: true  ## 수정

image:
  registry: bastion.ocp419.test:5001  ## 수정
  repository: zookeeper/zookeeper ## 수정
  tag: 3.9.3 ## 수정
  pullPolicy: IfNotPresent

replicaCount: 3  ## 수정

$ helm package zookeeper
```

## 2. pv 생성
```
$ mkdir -p /nfs/zookeeper/{pv1,pv2,pv3}
$ chmod 777 -R /nfs/zookeeper
$ vi /etc/exports
/nfs/zookeeper/pv1 *(no_root_squash,rw)
/nfs/zookeeper/pv2 *(no_root_squash,rw)
/nfs/zookeeper/pv3 *(no_root_squash,rw)
$ exportfs -r
$ showmount -e

$ vi zookeeper-pv1.yaml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: zookeeper-1
spec:
  capacity:
    storage: 8Gi
  nfs:
    server: 192.168.10.31
    path: /nfs/zookeeper/pv1
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  volumeMode: Filesystem
$ vi zookeeper-pv2.yaml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: zookeeper-2
spec:
  capacity:
    storage: 8Gi
  nfs:
    server: 192.168.10.31
    path: /nfs/zookeeper/pv2
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  volumeMode: Filesystem
$ vi zookeeper-pv3.yaml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: zookeeper-3
spec:
  capacity:
    storage: 8Gi
  nfs:
    server: 192.168.10.31
    path: /nfs/zookeeper/pv3
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  volumeMode: Filesystem
$ oc create -f zookeeper-pv1.yaml
$ oc create -f zookeeper-pv2.yaml
$ oc create -f zookeeper-pv3.yaml
```

## 3. helm 차트로 배포
```
$ oc new-project zookeeper
$ oc image mirror docker.io/bitnamilegacy/zookeeper:3.9.3-debian-12-r22 bastion.ocp419.test:5001/zookeeper/zookeeper:3.9.3

$ helm install my-zookeeper zookeeper-13.8.7.tgz
```




## 참고. 프라이빗 레포 사용
```
$ docker run --rm -it -p 8080:8080 -v $(pwd):/charts chartmuseum/chartmuseum:latest
$ helm repo add my-private-repo http://<chart-museum-server-ip>:8080
$ helm repo update

$ helm install my-zookeeper my-private-repo/<chart>
```
