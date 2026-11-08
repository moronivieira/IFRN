# Aula 16 - Autoscalers101: Dimensionamento Automático

## Objetivos da Aula
Configurar o **Horizontal Pod Autoscaler (HPA)** para ajustar automaticamente a quantidade de réplicas de uma aplicação de acordo com a carga de CPU/Memória.

![Funcionamento do HPA](./imagens/hpa_architecture.png)

### 1. Criando um Deployment com Limites de Recursos
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-escalavel
spec:
  replicas: 1
  selector:
    matchLabels:
      app: escalavel
  template:
    metadata:
      labels:
        app: escalavel
    spec:
      containers:
      - name: php-apache
        image: registry.k8s.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 200m
```

### 2. Configurando o HPA
```bash
kubectl autoscale deployment app-escalavel --cpu-percent=50 --min=1 --max=10
kubectl get hpa
```
