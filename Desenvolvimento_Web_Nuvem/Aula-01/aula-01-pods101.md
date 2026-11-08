# Aula 01 - Pods101: Fundamentos de Pods no Kubernetes

## Objetivos da Aula
Nesta aula prática, você aprenderá as operações fundamentais para trabalhar com a menor unidade implantável no Kubernetes: o **Pod**.

![Arquitetura de Pods](https://kubernetes.io/images/docs/pod.svg){width=50%}

### 1. Criando o seu Primeiro Pod Nginx
Crie um Pod executando a imagem oficial do Nginx utilizando o utilitário `kubectl`:

```bash
kubectl run meu-nginx --image=nginx:alpine --port=80
```

Para verificar o status do Pod recém-criado:
```bash
kubectl get pods
```

### 2. Inspecionando os Detalhes do Pod
Obtenha informações detalhadas sobre eventos, IP atribuído e status do contêiner:
```bash
kubectl describe pod meu-nginx
```

Para obter a saída completa em formato JSON:
```bash
kubectl get pod meu-nginx -o json
```

### 3. Identificando o Nó de Execução
Para verificar em qual nó do cluster o Pod está alocado:
```bash
kubectl get pods -o wide
```

### 4. Executando Comandos dentro do Pod (`exec`)
Abra um shell interativo no contêiner do Pod:
```bash
kubectl exec -it meu-nginx -- /bin/sh
```
Dentro do contêiner, verifique o servidor web:
```bash
curl localhost
exit
```

### 5. Criando um Pod com Múltiplos Contêineres (Sidecar Pattern)
Crie um arquivo manifesto `pod-multicontainer.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-duplo
  labels:
    app: web-logger
spec:
  containers:
  - name: web-server
    image: nginx:alpine
    ports:
    - containerPort: 80
  - name: log-sidecar
    image: busybox
    command: ["/bin/sh", "-c", "while true; do date >> /var/log/app.log; sleep 5; done"]
```

Aplique o manifesto:
```bash
kubectl apply -f pod-multicontainer.yaml
```

### 6. Rótulos (Labels) e Seletores (Selectors)
Filtre os Pods com base em suas rotulagens:
```bash
kubectl get pods -l app=web-logger
```

### 7. Encerrando e Excluindo o Pod
Para remover os Pods criados durante esta aula:
```bash
kubectl delete pod meu-nginx pod-duplo
```
