# Aula 17 - Helm101: Gerenciador de Pacotes para Kubernetes

## Objetivos da Aula
Utilizar o **Helm**, o gerenciador de pacotes para Kubernetes, para simplificar a implantação e a manutenção de aplicações complexas via Charts.

![Arquitetura do Helm](./imagens/helm_architecture.png)

### 1. Instalando e Adicionando Repositórios Helm
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

### 2. Instalando um Chart (Exemplo: Redis)
```bash
helm install meu-redis bitnami/redis
```

### 3. Listando e Desinstalando Releases
```bash
helm list
helm uninstall meu-redis
```
