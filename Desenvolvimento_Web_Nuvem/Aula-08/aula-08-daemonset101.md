# Aula 08 - DaemonSet101: Agentes e Serviços de Infraestrutura

## Objetivos da Aula
Garantir que uma cópia de um Pod seja executada em **todos os nós** (ou em nós selecionados) do cluster usando **DaemonSets**, ideal para agentes de log e monitoramento.

![Funcionamento do DaemonSet](./imagens/daemonset_architecture.png)

### 1. Criando um DaemonSet para Coleta de Logs
Crie o manifesto `daemonset.yaml`:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
spec:
  selector:
    matchLabels:
      name: fluentd-logging
  template:
    metadata:
      labels:
        name: fluentd-logging
    spec:
      containers:
      - name: fluentd
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
```

### 2. Verificando os Pods do DaemonSet
```bash
kubectl apply -f daemonset.yaml
kubectl get daemonsets
kubectl get pods -o wide
```
