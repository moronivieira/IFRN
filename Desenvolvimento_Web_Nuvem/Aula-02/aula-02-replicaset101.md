# Aula 02 - ReplicaSet101: Escalonamento e Alta Disponibilidade

## Objetivos da Aula
Entender o funcionamento dos **ReplicaSets**, responsáveis por garantir que um número especificado de réplicas de um Pod permaneça em execução continuamente.

![Arquitetura do ReplicaSet](./imagens/replicaset_architecture.png)

### 1. Criando o manifesto do ReplicaSet
Crie o arquivo `replicaset.yaml`:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: frontend
spec:
  replicas: 4
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - containerPort: 80
```

Aplique no cluster Minikube:
```bash
kubectl apply -f replicaset.yaml
kubectl get replicaset
kubectl get pods
```

### 2. Testando a Autocura (Self-Healing)
Remova manualmente um dos Pods controlados pelo ReplicaSet para observar sua recriação automática:
```bash
POD_NAME=$(kubectl get pods -l app=frontend -o jsonpath='{.items[0].metadata.name}')
kubectl delete pod $POD_NAME
kubectl get pods
```

### 3. Alterando o Escalonamento (Scaling)
Diminua ou aumente o número de réplicas via linha de comando:
```bash
kubectl scale replicaset nginx-replicaset --replicas=6
kubectl get pods
```

### 4. Limpeza de Recursos
```bash
kubectl delete rs nginx-replicaset
```
