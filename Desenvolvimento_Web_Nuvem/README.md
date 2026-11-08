# Desenvolvimento Web para a Nuvem — IFRN Currais Novos

# Desenvolvimento Web para a Nuvem — IFRN Campus Currais Novos
## Etapa 2: Orquestração de Contêineres com Kubernetes (Laboratório Minikube)

Este repositório contém o material didático traduzido, estruturado e adaptado para as aulas práticas de Kubernetes no laboratório do IFRN Campus Currais Novos.

---

### 📚 Sumário das Aulas

| Aula | Tópico | Descrição do Conteúdo | Arquivo |
| :---: | :--- | :--- | :--- |
| **Aula 01** | Pods101 | Conceito de Pod, criação com Nginx, inspeção JSON, `exec` e múltiplos contêineres | [`aula-01-pods101.md`](./Aula01/aula-01-pods101.md) |
| **Aula 02** | ReplicaSet101 | Escalonamento, tolerância a falhas, seletores de rótulos e gerenciamento de réplicas | [`aula-02-replicaset101.md`](./Aula-02/aula-02-replicaset101.md) |
| **Aula 03** | Deployment101 | Atualizações contínuas (*Rolling Updates*), revisões e *Rollbacks* sem indisponibilidade | [`aula-03-deployment101.md`](./Aula-03/aula-03-deployment101.md) |
| **Aula 04** | ConfigMaps101 | Injeção de configurações em tempo de execução via Variáveis de Ambiente e Volumes | [`aula-04-configmaps101.md`](./Aula-04/aula-04-configmaps101.md) |
| **Aula 05** | Scheduler101 | Agendamento avançado de Pods: NodeAffinity, Taints, Tolerations e Seletores de Nós | [`aula-05-scheduler101.md`](./Aula-05/aula-05-scheduler101.md) |
| **Aula 06** | Services101 | Exposição de aplicações: ClusterIP, NodePort, LoadBalancer, Headless e Discovery | [`aula-06-services101.md`](./Aula-06/aula-06-services101.md) |
| **Aula 07** | StatefulSets101 | Gerenciamento de aplicações com estado, identidades de rede estáveis e Volumes PVC | [`aula-07-statefulsets101.md`](./Aula-07/aula-07-statefulsets101.md) |
| **Aula 08** | DaemonSet101 | Execução de agentes em cada nó do cluster para monitoramento e coleta de logs | [`aula-08-daemonset101.md`](./Aula-08/aula-08-daemonset101.md) |
| **Aula 09** | Jobs101 | Execução de tarefas em lote (*Batch Processing*) e processamento paralelo com filas | [`aula-09-jobs101.md`](./Aula-09/aula-09-jobs101.md) |
| **Aula 10** | Ingress101 | Roteamento HTTP/HTTPS de Camada 7, regras de domínio e Ingress Controller Nginx | [`aula-10-ingress101.md`](./Aula-10/aula-10-ingress101.md) |
| **Aula 11** | Gateway API 101 | A evolução do Ingress: GatewayClass, Gateway, HTTPRoute e divisão de tráfego | [`aula-11-gateway-api-101.md`](./Aula-11/aula-11-gateway-api-101.md) |
| **Aula 12** | RBAC101 | Segurança e Controle de Acesso Baseado em Funções: ServiceAccounts, Roles e RoleBindings | [`aula-12-rbac101.md`](./Aula-12/aula-12-rbac101.md) |
| **Aula 13** | Network Policies101 | Isolamento de rede de microsserviços via regras de Ingress/Egress e Seletores | [`aula-13-network-policies101.md`](./Aula-13/aula-13-network-policies101.md) |
| **Aula 14** | Monitoring101 | Coleta de métricas do cluster com Metrics Server, Prometheus e dashboards Grafana | [`aula-14-monitoring101.md`](./Aula-14/aula-14-monitoring101.md) |
| **Aula 15** | Logging101 | Arquiteturas de agregação de logs com Fluentd, Fluent Bit e a pilha ELK | [`aula-15-logging101.md`](./Aula-15/aula-15-logging101.md) |
| **Aula 16** | Autoscalers101 | Dimensionamento automático de Pods (HPA) e infraestrutura de nós no Kubernetes | [`aula-16-autoscalers101.md`](./Aula-16/aula-16-autoscalers101.md) |
| **Aula 17** | Helm101 | Gerenciamento de pacotes Kubernetes: instalação, criação e repositórios de Charts | [`aula-17-helm101.md`](./Aula-17/aula-17-helm101.md) |

---

### 💻 Pré-requisitos para o Laboratório (Minikube)
Certifique-se de que o Minikube esteja iniciado no terminal antes de executar os laboratórios:
```bash
minikube start --driver=docker
kubectl get nodes
```
