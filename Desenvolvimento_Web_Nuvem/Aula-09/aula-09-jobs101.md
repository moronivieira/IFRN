# Aula 09 - Jobs101: Processamento em Lote no Kubernetes

## Objetivos da Aula
Executar tarefas pontuais que devem rodar até a conclusão (*Batch Processing*) através de **Jobs** e **CronJobs**.

![Execução de Jobs](./imagens/jobs_diagram.png)

### 1. Criando um Job Simples
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: calculo-pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```

### 2. Verificando a Conclusão do Job
```bash
kubectl apply -f job.yaml
kubectl get jobs
kubectl logs job/calculo-pi
```
