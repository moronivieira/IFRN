# Aula 13 - Network Policies101: Segurança de Rede

## Objetivos da Aula
Proteger a comunicação entre Pods dentro do cluster criando políticas de isolamento de rede (**NetworkPolicies**).

![Network Policies](./imagens/network_policy_diagram.png)

### 1. Bloqueando Todo o Tráfego de Entrada por Padrão (Deny All Ingress)
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

### 2. Permitindo Tráfego Apenas de Pods Específicos
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: permitir-frontend-para-backend
spec:
  podSelector:
    matchLabels:
      role: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
```
