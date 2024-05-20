1. Развернуть кластер в yandex cloud.
2. Клонировать репо.
3. Развернуть object storage, создать сервисный аккаунт, сгенерить ключи, добавить ключи в values loki и s3
4. Установить helm репозитории:
```bash
helm repo add yandex-s3 https://yandex-cloud.github.io/k8s-csi-s3/charts
helm repo add grafana https://grafana.github.io/helm-charts
```
5. Установить s3 csi:
```bash
helm install --namespace provisioner --valuesn kubernetes-logging/s3/values.yaml csi-s3 yandex-s3/csi-s3 --create-namespace 
```
6. Установить графана:
```bash
helm -n monitoring install --values kubernetes-logging/grafana/values.yaml grafana grafana/grafana --create-namespace 
```
7. Установить loki:
```bash
helm -n monitoring install --values kubernetes-logging/loki/values.yaml loki grafana/loki 
```
8. Установить promtail
```bash
helm -n monitoring install --values kubernetes-logging/promtail/values.yaml promtail grafana/promtail
```
9. Добавить datasource loki в графану.

Выводы комманд:

```bash
 kubectl get node -o wide --show-labels
NAME         STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP       OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME     LABELS
k8s-node-1   Ready    <none>   10m   v1.27.3   10.10.0.25    84.201.173.28     Ubuntu 20.04.6 LTS   5.4.0-174-generic   containerd://1.6.28   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=standard-v1,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/zone=ru-central1-a,kubernetes.io/arch=amd64,kubernetes.io/hostname=k8s-node-1,kubernetes.io/os=linux,node-role=infra,node.kubernetes.io/instance-type=standard-v1,node.kubernetes.io/kube-proxy-ds-ready=true,node.kubernetes.io/masq-agent-ds-ready=true,node.kubernetes.io/node-problem-detector-ds-ready=true,topology.kubernetes.io/zone=ru-central1-a,yandex.cloud/node-group-id=cat9h47pg4q8j532obbr,yandex.cloud/pci-topology=k8s,yandex.cloud/preemptible=false
k8s-node-2   Ready    <none>   10m   v1.27.3   10.10.0.31    84.201.174.93     Ubuntu 20.04.6 LTS   5.4.0-174-generic   containerd://1.6.28   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=standard-v1,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/zone=ru-central1-a,kubernetes.io/arch=amd64,kubernetes.io/hostname=k8s-node-2,kubernetes.io/os=linux,node-role=infra,node.kubernetes.io/instance-type=standard-v1,node.kubernetes.io/kube-proxy-ds-ready=true,node.kubernetes.io/masq-agent-ds-ready=true,node.kubernetes.io/node-problem-detector-ds-ready=true,topology.kubernetes.io/zone=ru-central1-a,yandex.cloud/node-group-id=cat9h47pg4q8j532obbr,yandex.cloud/pci-topology=k8s,yandex.cloud/preemptible=false
k8s-node-3   Ready    <none>   10m   v1.27.3   10.10.0.33    178.154.222.182   Ubuntu 20.04.6 LTS   5.4.0-174-generic   containerd://1.6.28   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=standard-v1,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/zone=ru-central1-a,kubernetes.io/arch=amd64,kubernetes.io/hostname=k8s-node-3,kubernetes.io/os=linux,node.kubernetes.io/instance-type=standard-v1,node.kubernetes.io/kube-proxy-ds-ready=true,node.kubernetes.io/masq-agent-ds-ready=true,node.kubernetes.io/node-problem-detector-ds-ready=true,topology.kubernetes.io/zone=ru-central1-a,yandex.cloud/node-group-id=cat9h47pg4q8j532obbr,yandex.cloud/pci-topology=k8s,yandex.cloud/preemptible=false
k8s-node-4   Ready    <none>   10m   v1.27.3   10.10.0.12    84.201.128.196    Ubuntu 20.04.6 LTS   5.4.0-174-generic   containerd://1.6.28   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=standard-v1,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/zone=ru-central1-a,kubernetes.io/arch=amd64,kubernetes.io/hostname=k8s-node-4,kubernetes.io/os=linux,node.kubernetes.io/instance-type=standard-v1,node.kubernetes.io/kube-proxy-ds-ready=true,node.kubernetes.io/masq-agent-ds-ready=true,node.kubernetes.io/node-problem-detector-ds-ready=true,topology.kubernetes.io/zone=ru-central1-a,yandex.cloud/node-group-id=cat9h47pg4q8j532obbr,yandex.cloud/pci-topology=k8s,yandex.cloud/preemptible=false
```

```bash
 $ kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints
NAME         TAINTS
k8s-node-1   [map[effect:NoSchedule key:node-role value:infra]]
k8s-node-2   [map[effect:NoSchedule key:node-role value:infra]]
k8s-node-3   <none>
k8s-node-4   <none>
```

![Alt text](loki.png?raw=true "Title")