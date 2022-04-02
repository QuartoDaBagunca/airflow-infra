### CRIANDO CLUSTER EKS FORMA SIMPLES

<br>


#### Instalar o eksctl pra criação dos recursos
- Baixando e descompactando o eksctl
- Após o download é necessário mover a pasta descompactada para o diretório bin
- Na execução do comando `eksctl version` se o retorno for um número de versão como o do exemplo abaixo, a instalação foi bem sucedida
>

```bash
    curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
    sudo mv /tmp/eksctl /usr/local/bin
    eksctl version
```
```bash
    0.90.0
```

#### Criação de cluster e recurços necessários na AWS
*Através do comando create cluster o eksctl, além do cluster, cria um diversidades de recursos que serão necessários, como VPCs, serviços autoscale e até mesmo os nós doc cluster*
- No comando abaixo temos:
1. Comando de criação
2. Parâmetro com a versão do kubernetes
3. O parâmetro nodegroup-name se refer ao tipo de recurso que formará o cluster, no caso, estou usando linux-nodes (outra opção seria o fargate por exemplo)
4. Parâmetro com o tipo de máquina dos nós
5. Quantidade de nó
6. A região onde os recursos serão criados 

- Por ultimo, o comando para exclusão, não só do cluster, mas todos os recursos criados no comando anterior

```bash
    eksctl create cluster \
    --version 1.19 \
    --name k8s-airflow-eks2 \
    --nodegroup-name linux-nodes \
    --node-type t2.medium \
    --nodes 3 \
    --region us-east-1
```
```bash
    eksctl delete cluster --region=us-east-1 --name=k8s-airflow-eks2
```
> O comando delete cluster tambem garante que recusos que são criados posteriormente vinculados ao cluster, loadBalancer por exemplo, sejam igualmente deletados.