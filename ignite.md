> 아래 내용은 Gemini가 가이드 해준 설치 방법입니다. 아직 테스트를 수행하지 않았습니다.

## 1. DeliveryHero 레포 추가
```
$ helm repo add deliveryhero https://charts.deliveryhero.io/
$ helm repo update
```

## 2. 차트 다운로드 및 압축 해제 (최신 버전 기준)
> --untar 옵션을 쓰면 압축이 풀린 디렉토리 상태로 다운로드됩니다.
```
$ helm pull deliveryhero/ignite-kube --untar
```

## 3. values.yaml 수정
```
$ vi ignite-kube/values.yaml
image:
  # 아래 내용은 미러레지스트리 및 idms/itms 사용하는 방식으로 수정 필요
  # 예: registry.example.com/my-project/ignite
  repository: <내부-컨테이너-레지스트리-주소>/ignite
  tag: 2.16.0
  pullPolicy: IfNotPresent

# (선택) 서비스 어카운트 이름 고정 (나중에 권한 줄 때 편함)
serviceAccount:
  create: true
  name: "my-ignite-sa"

# 스토리지 클래스 지정 (OCP 환경에 맞게 필수 수정)
persistence:
  enabled: true
  size: 10Gi
  storageClass: "gp3-csi"  # 이부분은 수동 프로비저닝 방식으로 수정 필요

# ... 나머지 설정 유지 혹은 수정
```

## 4. 차트 패키징 (ignite-kube 디렉토리를 tgz로 압축)
helm package ignite-kube/
> 결과: ignite-kube-x.x.x.tgz 파일 생성됨

## 5. ChartMuseum으로 업로드 (curl 사용)
> <chart-museum-server-ip> 부분을 실제 IP로 변경하세요.
curl --data-binary "@ignite-kube-2.16.0.tgz" http://<chart-museum-server-ip>:8080/api/charts
