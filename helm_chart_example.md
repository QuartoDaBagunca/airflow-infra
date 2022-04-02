## APLICAÇÃO NO KUBERNETES USANDO HELM
<br>

### 1 - Setando repositório e criando arquivo values
<br>

- Adicionando o chart do ingress-nginx no repositório do helm local
- Atualizando alteração no repositório helm 
- Inspecionando valores do chart e salvando no arquivo values.yaml
<br>

```bash
    helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
    helm repo update
    helm inspect values ingress-nginx/ingress-nginx > values.yaml
```
>   Comandos acima são usam o chart do ingress-nginx como modelo e, mudando o repositório, outro chart pode ser configurado de forma semelhante

<br>

### 2 - Instalando/Aplicando propriedades do repositório 
*Com o repositório configurado, implantamos os objetos compoem a aplicação no cluster kubernetes default*

<br>

- Comando que instala a plicação no cluster

```bash
    helm install meu-ingress-controller ingress-nginx/ingress-nginx
```
>   Os objetos contemplados na instação são: 1 `replicaset`, 1 `deployment`, 2 `services`: (LoadBalancer e ClusterIP) e, na configuração padrão dos values do chart, apenas 1 `pod` é criado

<br>

### 3 - Separando aplicação do namespace default
<br>

- Criando o namespace
- Desinstalando aplicação do namespace default
- Recriando ela no novo namespace
- Conferindo releases no namespace default
- Conferindo releases no novo namespace

```bash
    kubectl create namespace nginx
    helm uninstall meu-ingress-controller
    helm install meu-ingress-controller ingress-nginx/ingress-nginx --namespace nginx
    helm list
    helm list -n nginx
```

<br>

### 4 - Alterando valores do chart
<br>

- La no arquivo value eu alterei a quantidade de pods a serem criados mundando o parâmetro `replicaCount` de 1 pra 3
```yaml
    controller:
    replicaCount: 3
```

- Uma vez salvo a alteração (no meu caso, salvei em um arquivo de nome values_set_replicas.yaml pra não alterar o arquivo anterior), implementamos as alterações através do comando abaixo
- Ao retornarmos os objetos agora, devemos ter 3 pods ao invés de 1
```bash
    helm upgrade meu-ingress-controller ingress-nginx/ingress-nginx --namespace nginx --values values_set_replicas.yaml
    kubectl get all -n nginx
```
```
    NAME                                                                  READY   STATUS    RESTARTS   AGE
    pod/meu-ingress-controller-ingress-nginx-controller-79d659954866lnv   1/1     Running   0          35s
    pod/meu-ingress-controller-ingress-nginx-controller-79d6599548bxl2n   1/1     Running   0          35s
    pod/meu-ingress-controller-ingress-nginx-controller-79d6599548n567s   1/1     Running   0          8m3s
```
>   No comando acima eu criei um novo arquivo values dei o nome values_set_replicas, mas se fosse alterado o arquivo values salvo anteriormente, o mesmo poderia ser usado no comando

<br>

### 5 - Usando o time travell no helm
<br>

- Com o comando rollback voltamos a versão, nesse caso, para a release com 1 pod apenas
- Podemos ver o histórico das releases através do comando history

```bash
    helm rollback meu-ingress-controller -n nginx
    helm history meu-ingress-controller -n nginx
```

<br>

### 6 - Setando manualmente os valores do chart
<br>

- Aplicando novo valor pra o campo replicaCount
- Ao vermos o histórico temos mais uma release
- No ultimo comando voltamos, não pra versão anterior, mas pra versão 2 do nosso chart

```bash
    helm upgrade meu-ingress-controller ingress-nginx/ingress-nginx --namespace nginx --set controller.replicaCount=2
    helm history meu-ingress-controller -n nginx
    helm rollback meu-ingress-controller 2 -n nginx
```

```yaml
    controller:
    replicaCount: 2
```
>   Nesse caso ao invés de alterarmos o arquivo yaml pra sertar o valor do replicaCount, fazemos diretamente no comando upgrade