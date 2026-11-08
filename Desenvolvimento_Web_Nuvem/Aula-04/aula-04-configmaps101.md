# Aula 04 - ConfigMaps101: Gerenciando Configurações Desacopladas

## Objetivos da Aula
Injetar parâmetros de configuração e arquivos externos em contêineres utilizando **ConfigMaps**.

![Funcionamento de ConfigMaps](./imagens/configmaps_diagram.png)

### 1. Criando um ConfigMap a partir de Valores diretos
```bash
kubectl create configmap app-config   --from-literal=AMBIENTE=producao   --from-literal=PORTA=8080
```

### 2. Inspecionando o ConfigMap Criado
```bash
kubectl get configmap app-config -o yaml
```

### 3. Consumindo ConfigMap como Variáveis de Ambiente no Pod
Crie o arquivo `pod-configmap.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-com-config
spec:
  containers:
  - name: app
    image: alpine
    command: ["/bin/sh", "-c", "env && sleep 3600"]
    envFrom:
    - configMapRef:
        name: app-config
```

Execute e verifique os logs:
```bash
kubectl apply -f pod-configmap.yaml
kubectl logs pod-com-config | grep AMBIENTE
```

### 4. Limpeza
```bash
kubectl delete pod pod-com-config
kubectl delete configmap app-config
```
