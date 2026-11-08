# Aula 03 - Deployment101: Gerenciamento do Ciclo de Vida da Aplicação

## Objetivos da Aula
Aprender a gerenciar a implantação de aplicações com **Deployments**, lidando com atualizações sem indisponibilidade (*Rolling Updates*) e reversões (*Rollbacks*).

![Fluxo do Deployment](./imagens/deployment_flow.png)

### 1. Criando um Deployment
Crie o manifesto `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx:1.20
        ports:
        - containerPort: 80
```

Aplique a implantação:
```bash
kubectl apply -f deployment.yaml --record
kubectl get deployments
```

### 2. Atualizando a Versão da Aplicação (Rolling Update)
Atualize a imagem do Nginx da versão `1.20` para a `1.21`:
```bash
kubectl set image deployment/web-app nginx=nginx:1.21 --record
kubectl rollout status deployment/web-app
```

### 3. Inspecionando o Histórico de Implantação
```bash
kubectl rollout history deployment/web-app
```

### 4. Revertendo uma Atualização (Rollback)
Caso uma nova versão apresente problemas, reverta para a versão estável anterior:
```bash
kubectl rollout undo deployment/web-app
```

### 5. Exclusão dos Recursos
```bash
kubectl delete deployment web-app
```
