# Aula 10 - Ingress101: Roteamento de Camada 7

## Objetivos da Aula
Configurar um **Ingress Controller** para gerenciar o tráfego HTTP/HTTPS externo e o roteamento baseado em caminhos e domínios no Minikube.

![Arquitetura de Ingress](./imagens/ingress_architecture.png)

### 1. Habilitando o Ingress no Minikube
```bash
minikube addons enable ingress
```

### 2. Definindo a Regra de Ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: meu-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: meuapp.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: meu-servico-nodeport
            port:
              number: 80
```

### 3. Testando o Acesso
Adicione a entrada ao `/etc/hosts` com o IP obtido via `minikube ip` e faça o acesso:
```bash
curl http://meuapp.local
```
