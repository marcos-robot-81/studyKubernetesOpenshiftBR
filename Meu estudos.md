> **Nota:** Este guia é um resumo e não está completo. É altamente aconselhável que você consulte também a documentação oficial do [Minikube](https://minikube.sigs.k8s.io/docs/) e do [kubectl](https://kubernetes.io/docs/reference/kubectl/).

### 🚀 Comandos Minikube

- **Pré-requisito:** Instale o Minikube e depois um gerenciador de máquinas virtuais (Virtual Machine Manager) para iniciar o ambiente e utilizar o `kubectl`.

- **Verifica o status atual do cluster Minikube** (por exemplo, se está rodando ou parado):
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

Como você pode ver, o comando `kubectl get` pode trazer informações dos pods e de outros recursos do Kubernetes. Outro comando útil é o `kubectl describe`. Para mais informações, consulte a documentação do `kubectl`.

#### Escalar uma aplicação (aumentar ou diminuir o número de réplicas)
```bash
kubectl scale deployment [nome-da-aplicação] --replicas=[quantidade]

# Exemplo prático:
kubectl scale deployment do100-hello --replicas=3
```

#### Comando edit

O comando `kubectl edit` permite modificar recursos que já estão implementados.

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

#### Aplicar ou criar recursos a partir de um arquivo de manifesto (YAML)
O `apply` é recomendado por ser declarativo (cria ou atualiza), enquanto o `create` falha se o recurso já existir.
```bash
kubectl apply -f [caminho/do/arquivo/deployment.yml]
kubectl create -f ./service.yml
```

#### Criar um pod interativo e temporário (troubleshooting de rede)
Cria um pod descartável (opção `--rm`) com acesso ao shell interativo (opção `-it`) para investigar o cluster por dentro.
```bash
kubectl run -n default curl -it --rm --image=registry.access.redhat.com/ubi8/ubi-minimal -- sh
```

#### Apply
O comando `kubectl apply` é usado para aplicar uma configuração YAML.

```bash
kubectl apply -f [./local-do-arquivo.yaml]
```

---

### Limitação de recursos do Kubernetes por cotas

As limitações de recursos estão no arquivo YAML em `resources` e é importante verificar a documentação para outras configurações:
```yaml
        resources:
          requests:
            cpu: "120m"
            memory: "20Mi"
```

Aqui você pode ver como é a limitação de CPU e RAM. Essa limitação é aplicada ao cluster como um todo e não a cada contêiner individualmente.

---
### Sonda de verificação

Uma sonda verifica se há algum erro no contêiner.

#### Verificações de execução de contêineres
As verificações de execução de contêineres são ideais em cenários onde você precisa determinar o status do contêiner com base no código de saída de um processo ou script de shell em execução no contêiner.
Ao usar verificações de execução de contêiner, o Kubernetes executa um comando dentro do contêiner.
A conclusão da verificação com um status 0 é considerada um sucesso. Todos os outros códigos de status são considerados uma falha.
O exemplo a seguir demonstra como implementar uma verificação de execução de contêiner:
```yml
livenessProbe:
  exec:
    command:
      - cat
      - /tmp/health
  initialDelaySeconds: 15
  timeoutSeconds: 1
```

Para configurar sondas em uma implantação, edite a definição de recursos da implantação. Para fazer isso, você pode usar os comandos `kubectl edit` ou `kubectl patch`.
O exemplo a seguir inclui a adição de uma sonda a uma definição de recurso de implantação usando o comando `kubectl edit`.
```yml
kubectl edit deployment mydeployment
apiVersion: apps/v1
kind: Deployment
  ...contents omitted...
spec:
  ...contents omitted...
  template:
  ...contents omitted...
  spec:
    containers:
      - image: quay.io/redhattraining/do100-versioned-hello:v1.0
        name: do100-versioned-hello
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /healthz
            port: 8080
            scheme: HTTP
        periodSeconds: 10
        successThreshold: 1
        timeoutSeconds: 1
```

#### Parâmetros de sondas

```yml
    failureThreshold:    \\ limite de falhas
    initialDelaySeconds: \\ atraso inicial em segundos
    periodSeconds:       \\ período em segundos
    successThreshold:    \\ limiar de sucesso
    timeoutSeconds:      \\ tempo limite em segundos
```

#### Segredos (Secrets) e Mapas de Configuração (ConfigMaps)

Recursos secretos são usados para armazenar informações confidenciais, como senhas, chaves e tokens.
Como desenvolvedor, é importante criar segredos para evitar comprometer credenciais e outras informações sensíveis em sua aplicação.

Você também pode configurar a implantação para referenciar o ConfigMap e os recursos secretos.
Em seguida, o Kubernetes reimplementa automaticamente o aplicativo e disponibiliza os dados para o contêiner.

Os dados são armazenados em um recurso secreto usando codificação base64. Quando os dados de um segredo são injetados em um contêiner, eles são decodificados e montados como um arquivo ou injetados como variáveis de ambiente dentro do contêiner.
```bash
kubectl create configmap config_map_name \
  --from-literal key1=value1 \
  --from-literal key2=value2
```

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconf
data:
  key1: value1
  key2: value2
```

Tudo está dentro do diretório `app-config`.

Para definir uma variável de ambiente no Linux:
```bash
KEY="valor"
echo "$KEY"
```

Criando um ConfigMap:

```bash
kubectl create configmap appconfmap --from-literal APP_MSG="Test Message"
```

Injetando as variáveis de ambiente a partir do ConfigMap:

```bash
kubectl set env deployment app-config --from=configmap/appconfmap
```

O Secret é parecido com o ConfigMap, mas é usado para armazenar dados sensíveis, enquanto o ConfigMap é usado para configurações gerais de aplicações.

Criando um segredo:

```bash
kubectl create secret generic appconffilesec --from-file ./app-config/app/myapp.sec
```

Conectando o segredo ao aplicativo:

```bash
kubectl patch deployment app-config --patch-file ./secret.yml
```

#### Como apagar tudo

```bash
kubectl delete all,ingress --all
```

Para deletar a configuração:

Pegue a configuração com `get` e depois delete:
```bash
kubectl get configmap,secret

kubectl delete configmap appconfmap
kubectl delete secrets appconffilesec
```

### Estratégia de implementação

Estratégia de implementação Blue-Green e A/B

#### O que é a estratégia de implementação verde e azul

Vamos imaginar que temos duas implementações idênticas, verde e azul. Inicialmente todo o tráfego está sendo direcionado para a implementação verde. Depois que atualizamos a azul, passamos todo o tráfego para ela. Esse é um exemplo simples de implementação verde e azul (Blue-Green).

#### A/B

Teremos um conjunto de pods, alguns na versão v1 e outros na v2, e vamos distribuir o fluxo de requisições entre eles. Isso é útil porque podemos testar a v2 à medida que ela é implementada.
