# Aula 01 - Pods101: Fundamentos de Pods no Kubernetes

## Objetivos da Aula
Nesta aula prática, você aprenderá as operações fundamentais para trabalhar com a menor unidade implantável no Kubernetes: o **Pod**.

<p align="center">
  <img src="https://kubernetes.io/images/docs/pod.svg" alt="Texto Alternativo" width="30%">
</p>


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

### 7. Gerar um manifesto de Pod
```kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml```


### 8. Análise o código do arquivo YAML
```apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
```

### 9. Altere a imagem para uma imagem para o redis
```apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: redis
```

### 10. Inspecione o Pod

```kubectl describe pod nginx```

Visualize os seguintes itens:
- Pod status
- Container details
- Node information
- IP address
- Events

### 11. Visualize os logs do Pod

```kubectl logs nginx```


### 12. Encerrando e Excluindo o Pod
Para remover os Pods criados durante esta aula:
```bash
kubectl delete pod meu-nginx pod-duplo
```

### 13. Comandos básicos para Pods no Kubernetes
Criar ou atualizar um Pod
```kubectl create -f [FILENAME]```

Deletar o Pod
```kubectl delete -f FILENAME```

Para visulizar pods em um namespace específico
```kubectl get pod <name> --namespace```
