# Aula 05 - Scheduler101: Agendamento Avançado de Pods

## Objetivos da Aula
Compreender como o **kube-scheduler** seleciona os nós para alocação de Pods e aprender a influenciar essa decisão usando `nodeSelector`, `nodeAffinity`, `taints` e `tolerations`.

![Scheduler no Kubernetes](./imagens/scheduler_workflow.png)

### 1. Utilizando `nodeSelector`
Rotule um nó no Minikube:
```bash
kubectl label nodes minikube tipo=gpu
```

Crie um Pod especificando o seletor:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-processamento
spec:
  containers:
  - name: app
    image: nginx:alpine
  nodeSelector:
    tipo: gpu
```

### 2. Trabalhando com Taints e Tolerations
Adicione um Taint a um nó para repelir Pods sem autorização expressa:
```bash
kubectl taint nodes minikube ambiente=critico:NoSchedule
```

Adicione a `toleration` correspondente no Pod para permitir o agendamento:
```yaml
spec:
  tolerations:
  - key: "ambiente"
    operator: "Equal"
    value: "critico"
    effect: "NoSchedule"
```

Remova o Taint ao finalizar:
```bash
kubectl taint nodes minikube ambiente=critico:NoSchedule-
```
