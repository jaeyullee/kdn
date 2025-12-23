> 전제조건 1) 넥서스 5000, 5001 포트 각각 사용하는 레지스트리 구성
> 전제조건 2) 5000 포트 사용하는 레지스트리 : 기 설치된 오퍼레이터 및 OCP 미러 레지스트리 역할
> 전제조건 3) 5001 포트 사용하는 레지스트리 : 업그레이드를 위한 미러 레지스트리 역할
> 해당 문서에서는 web-terminal 오퍼레이터만 업그레이드 하는 케이스를 정리합니다.

### ** 미러 레지스트리
```
# curl -u 'admin:redhat1!' -X GET https://ocp-registry.kscada.kdneri.com:5000/v2/_catalog | jq .
{
  "repositories": [
    "ocp4/openshift/release",
    "ocp4/openshift/release-images",
    "olm-certified/elastic/eck",
    "olm-certified/elastic/eck-operator",
    "olm-certified/redhat/certified-operator-index",
    "olm-redhat/devworkspace/devworkspace-operator-bundle",
    "olm-redhat/devworkspace/devworkspace-project-clone-rhel9",
    "olm-redhat/devworkspace/devworkspace-rhel9-operator",
    "olm-redhat/network-observability/network-observability-console-plugin-compat-rhel9",
    "olm-redhat/network-observability/network-observability-console-plugin-rhel9",
    "olm-redhat/network-observability/network-observability-ebpf-agent-rhel9",
    "olm-redhat/network-observability/network-observability-flowlogs-pipeline-rhel9",
    "olm-redhat/network-observability/network-observability-operator-bundle",
    "olm-redhat/network-observability/network-observability-rhel9-operator",
    "olm-redhat/openshift-gitops-1/argo-rollouts-rhel8",
    "olm-redhat/openshift-gitops-1/argocd-agent-rhel8",
    "olm-redhat/openshift-gitops-1/argocd-extensions-rhel8",
    "olm-redhat/openshift-gitops-1/argocd-rhel8",
    "olm-redhat/openshift-gitops-1/console-plugin-rhel8",
    "olm-redhat/openshift-gitops-1/dex-rhel8",
    "olm-redhat/openshift-gitops-1/gitops-operator-bundle",
    "olm-redhat/openshift-gitops-1/gitops-rhel8",
    "olm-redhat/openshift-gitops-1/gitops-rhel8-operator",
    "olm-redhat/openshift-gitops-1/must-gather-rhel8",
    "olm-redhat/openshift-logging/cluster-logging-operator-bundle",
    "olm-redhat/openshift-logging/cluster-logging-rhel9-operator",
    "olm-redhat/openshift-logging/log-file-metric-exporter-rhel9",
    "olm-redhat/openshift-logging/logging-loki-rhel9",
    "olm-redhat/openshift-logging/loki-operator-bundle",
    "olm-redhat/openshift-logging/loki-rhel9-operator",
    "olm-redhat/openshift-logging/lokistack-gateway-rhel9",
    "olm-redhat/openshift-logging/opa-openshift-rhel9",
    "olm-redhat/openshift-logging/vector-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-cache-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-chains-controller-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-cli-tkn-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-console-plugin-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-controller-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-entrypoint-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-events-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-git-init-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-hub-api-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-hub-db-migration-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-hub-ui-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-manual-approval-gate-controller-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-manual-approval-gate-webhook-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-nop-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-opc-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-operator-bundle",
    "olm-redhat/openshift-pipelines/pipelines-operator-proxy-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-operator-webhook-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-pipelines-as-code-cli-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-pipelines-as-code-controller-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-pipelines-as-code-watcher-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-pipelines-as-code-webhook-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-pruner-controller-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-resolvers-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-results-api-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-results-retention-policy-agent-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-results-watcher-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-rhel9-operator",
    "olm-redhat/openshift-pipelines/pipelines-serve-tkn-cli-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-sidecarlogresults-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-triggers-controller-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-triggers-core-interceptors-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-triggers-eventlistenersink-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-triggers-webhook-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-webhook-rhel9",
    "olm-redhat/openshift-pipelines/pipelines-workingdirinit-rhel9",
    "olm-redhat/openshift-serverless-1/kn-client-kn-rhel8",
    "olm-redhat/openshift-update-service/cincinnati-operator-bundle",
    "olm-redhat/openshift-update-service/openshift-update-service-rhel8",
    "olm-redhat/openshift-update-service/openshift-update-service-rhel8-operator",
    "olm-redhat/openshift4/kubernetes-nmstate-operator-bundle",
    "olm-redhat/openshift4/kubernetes-nmstate-rhel9-operator",
    "olm-redhat/openshift4/nmstate-console-plugin-rhel9",
    "olm-redhat/openshift4/ose-haproxy-router",
    "olm-redhat/openshift4/ose-kube-rbac-proxy",
    "olm-redhat/openshift4/ose-kube-rbac-proxy-rhel9",
    "olm-redhat/openshift4/ose-kubernetes-nmstate-handler-rhel9",
    "olm-redhat/redhat/redhat-operator-index",
    "olm-redhat/rh-sso-7/sso76-openshift-rhel8",
    "olm-redhat/rhbk/keycloak-operator-bundle",
    "olm-redhat/rhbk/keycloak-rhel9",
    "olm-redhat/rhbk/keycloak-rhel9-operator",
    "olm-redhat/rhel9/buildah",
    "olm-redhat/rhel9/postgresql-13",
    "olm-redhat/rhel9/redis-7",
    "olm-redhat/rhel9/skopeo",
    "olm-redhat/source-to-image/source-to-image-rhel9",
    "olm-redhat/ubi9/openjdk-17",
    "olm-redhat/ubi9/ubi-minimal",
    "olm-redhat/web-terminal/web-terminal-exec-rhel9",
    "olm-redhat/web-terminal/web-terminal-operator-bundle",
    "olm-redhat/web-terminal/web-terminal-rhel9-operator",
    "olm-redhat/web-terminal/web-terminal-tooling-rhel9",
    "olm-redhat/workload-availability/node-healthcheck-must-gather-rhel9",
    "olm-redhat/workload-availability/node-healthcheck-operator-bundle",
    "olm-redhat/workload-availability/node-healthcheck-rhel9-operator",
    "olm-redhat/workload-availability/node-maintenance-operator-bundle",
    "olm-redhat/workload-availability/node-maintenance-rhel9-operator",
    "olm-redhat/workload-availability/node-remediation-console-rhel9",
    "olm-redhat/workload-availability/self-node-remediation-operator-bundle",
    "olm-redhat/workload-availability/self-node-remediation-rhel9-operator",
    "openshift-logging/eventrouter-rhel9",
    "openshift/graph-data",
    "oss/elasticsearch/elasticsearch",
    "rhel9/support-tools"
  ]
}
```
> ocp4 : OCP 설치용 미러 이미지 저장 경로
> olm-certified : Certified Operator 미러 이미지 저장 경로
> olm-redhat : Redhat Operator 미러 이미지 저장 경로
> openshift-logging, rhel9 : OCP 관리 툴 미러 이미지 저장 경로
> openshift : OSUS를 위한 미러 이미지 저장 경로
> oss : oss 용 컨테이너 이미지 저장 경로


# 1. 오퍼레이터 업그레이드 패스 확인
```
$ opm render registry.redhat.io/redhat/redhat-operator-index:v4.20 \
| jq 'select(.package == "web-terminal" and .schema == "olm.channel")
| { ChannelName: .name, UpgradeGraph: [.entries[] | { Bundle: .name, Replaces: .replaces, Skips: .skips, SkipRange: .skipRange }] }'
{
  "ChannelName": "fast",
  "UpgradeGraph": [
    {
      "Bundle": "web-terminal.v1.10.0",
      "Replaces": null,
      "Skips": null,
      "SkipRange": null
    },
...(중략)
    {
      "Bundle": "web-terminal.v1.14.0",
      "Replaces": "web-terminal.v1.13.0",
      "Skips": null,
      "SkipRange": null
    },
    {
      "Bundle": "web-terminal.v1.15.0",
      "Replaces": "web-terminal.v1.14.0",
      "Skips": null,
      "SkipRange": null
    }
  ]
}
```
> oc mirror 명령어로는 skip 관련 정보 확인이 불가능하여 opm 명령어로 확인
```
$ oc mirror list operators --catalog registry.redhat.io/redhat/redhat-operator-index:v4.20 --package web-terminal
NAME          DISPLAY NAME  DEFAULT CHANNEL
web-terminal                fast

PACKAGE       CHANNEL  HEAD
web-terminal  fast     web-terminal.v1.15.0
```












