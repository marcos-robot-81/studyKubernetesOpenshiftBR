> **Nota:** Este guia é um resumo e não está completo. É altamente aconselhável que você consulte também a documentação oficial do [Minikube](https://minikube.sigs.k8s.io/docs/) e do [kubectl](https://kubernetes.io/docs/reference/kubectl/).

### 🚀 Comandos Minikube

- **Verifica o status atual do cluster Minikube** (ex: se está rodando ou parado):
  ```bash
  minikube status 
  ```

- **Inicia o cluster do Minikube** caso ele esteja desligado:
  ```bash
  minikube start
  ```

### ☸️ Comandos kubectl

#### Exibir a configuração de um deployment em formato YAML
```bash
kubectl get deployment [nome-do-deployment] -o yaml
```

#### Ver a descrição detalhada e os eventos de um deployment
```bash
kubectl describe deployment [nome-do-deployment]
```

#### Listar a quantidade e o status dos pods em execução no namespace atual
```bash
kubectl get pods
```

#### Escalar uma aplicação (aumentar ou diminuir o número de réplicas)
```bash
kubectl scale deployment [nome-da-aplicação] --replicas=[quantidade]

# Exemplo prático:
kubectl scale deployment do100-hello --replicas=3
```

#### Excluir recursos do cluster (como pods, deployments ou services)
```bash
kubectl delete [tipo-do-recurso] [nome-do-recurso]

# Exemplos práticos:
kubectl delete pod do100-hello-67463728374-sdefv
kubectl delete deployment do100-versioned-hello
```

#### Editar a configuração de um recurso diretamente no cluster
Abre o manifesto YAML do recurso no editor de texto padrão do sistema para alterações dinâmicas ("on-the-fly").
```bash
kubectl edit [tipo-do-recurso] [nome-do-recurso]

# Exemplo prático:
kubectl edit deployment do100-hello
```

#### Editar um recurso utilizando um editor de texto específico (ex: nano)
```bash
KUBE_EDITOR="nano" kubectl edit deployment do100-hello
```

#### Expor um deployment para a rede (Criar um Service)
```bash
kubectl expose deployment [nome-do-deployment] --port 80 --target-port 8080
```

#### Aplicar ou Criar recursos a partir de um arquivo de manifesto (YAML)
O `apply` é recomendado por ser declarativo (cria ou atualiza), enquanto o `create` falha se o recurso já existir.
```bash
kubectl apply -f [caminho/do/arquivo/deployment.yml]
kubectl create -f ./service.yml
```

#### Criar um pod interativo e temporário (Troubleshooting de rede)
Cria um pod descartável (opção `--rm`) com acesso ao shell interativo (opção `-it`) para investigar o cluster por dentro.
```bash
kubectl run -n default curl -it --rm --image=registry.access.redhat.com/ubi8/ubi-minimal -- sh
```
