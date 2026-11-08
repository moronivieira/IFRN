# Aula 06 - Services101: Abstração e Exposição de Rede

## Objetivos da Aula
Expor aplicações web externamente ou internamente no cluster utilizando os tipos de **Services**: `ClusterIP`, `NodePort`, `LoadBalancer` e `Headless`.

![Tipos de Services](./imagens/services_architecture.png)

### 1. Criando um Service do tipo ClusterIP (Interno)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: meu-servico-interno
spec:
  type: ClusterIP
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 80
```

### 2. Expondo com NodePort (Acesso Externo no Minikube)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: meu-servico-nodeport
spec:
  type: NodePort
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

Obtenha a URL de acesso no Minikube:
```bash
minikube service meu-servico-nodeport --url
```

### 3. Service Discovery e DNS Interno
Dentro de qualquer Pod no cluster, os serviços podem ser acessados via DNS no formato:
`<nome-do-servico>.<namespace>.svc.cluster.local`
