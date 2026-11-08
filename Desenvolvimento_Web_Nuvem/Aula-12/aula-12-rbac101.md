# Aula 12 - RBAC101: Segurança e Controle de Acesso

## Objetivos da Aula
Implementar o **Controle de Acesso Baseado em Funções (RBAC)** utilizando `ServiceAccount`, `Role`, `ClusterRole`, `RoleBinding` e `ClusterRoleBinding`.

![Estrutura RBAC](./imagens/rbac_diagram.png)

### 1. Criando uma ServiceAccount
```bash
kubectl create serviceaccount dev-user
```

### 2. Definindo as Permissões com uma Role
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

### 3. Associando a Role à ServiceAccount (RoleBinding)
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: default
subjects:
- kind: ServiceAccount
  name: dev-user
  namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
