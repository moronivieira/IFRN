# Aula 11 - Gateway API 101: Nova Geração de Roteamento

## Objetivos da Aula
Explorar a evolução do Ingress no Kubernetes através da **Gateway API**, utilizando recursos declarativos como `GatewayClass`, `Gateway` e `HTTPRoute`.

![Arquitetura Gateway API](./imagens/gateway_api_architecture.png)

### 1. Principais Recursos
* **GatewayClass**: Define a infraestrutura do controlador de Gateway.
* **Gateway**: Define onde o tráfego é recebido (portas, protocolos, certificados).
* **HTTPRoute**: Define as regras de roteamento HTTP para os serviços de backend.

### 2. Exemplo de HTTPRoute com Divisão de Tráfego (Canary Release)
```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: rota-canary
spec:
  parentRefs:
  - name: meu-gateway
  rules:
  - backendRefs:
    - name: servico-v1
      port: 80
      weight: 90
    - name: servico-v2
      port: 80
      weight: 10
```
