# Aula 07 - StatefulSets101: Aplicações com Estado

## Objetivos da Aula
Gerenciar aplicações com estado (*Stateful Applications*), como bancos de dados, mantendo identidades de rede fixas e persistência de dados individualizada usando **StatefulSets**.

![StatefulSets vs Deployments](./imagens/statefulset_diagram.png)

### 1. Criando um Headless Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-headless
  labels:
    app: nginx-stateful
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx-stateful
```

### 2. Criando o StatefulSet
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web-stateful
spec:
  serviceName: "nginx-headless"
  replicas: 3
  selector:
    matchLabels:
      app: nginx-stateful
  template:
    metadata:
      labels:
        app: nginx-stateful
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```
Observe a ordem sequencial de inicialização dos pods: `web-stateful-0`, `web-stateful-1`, `web-stateful-2`.
