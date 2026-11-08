# Aula 14 - Monitoring101: Observabilidade e Métricas

## Objetivos da Aula
Monitorar o uso de recursos (CPU e Memória) de Pods e Nós no Kubernetes utilizando o **Metrics Server** e dashboards visualizadores.

![Monitoramento de Métricas](./imagens/monitoring_architecture.png)

### 1. Habilitando o Metrics Server no Minikube
```bash
minikube addons enable metrics-server
```

### 2. Visualizando Métricas de Uso via CLI
Para verificar o consumo nos nós:
```bash
kubectl top nodes
```

Para verificar o consumo nos pods:
```bash
kubectl top pods
```
