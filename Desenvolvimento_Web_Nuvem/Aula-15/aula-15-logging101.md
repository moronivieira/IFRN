# Aula 15 - Logging101: Centralização de Logs

## Objetivos da Aula
Explorar padrões de agregação centralizada de logs em clusters Kubernetes utilizando **Fluentd**, **Fluent Bit** e a pilha **ELK** / **PLG**.

![Arquitetura de Logs](./imagens/logging_architecture.png)

### 1. Coleta de Logs de Pods via `kubectl`
```bash
kubectl logs -f <nome-do-pod>
kubectl logs -l app=web --all-containers=true
```

### 2. Arquitetura com Fluent Bit (DaemonSet)
O Fluent Bit roda como um DaemonSet em cada nó, coletando logs do diretório `/var/log/containers/` e enviando-os para um repositório central como o Elasticsearch ou Loki.
