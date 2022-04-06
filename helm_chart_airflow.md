## APLICAÇÃO BINAMI/AIRFLOW USANDO HELM
<br>

### 1 - Criando objeto secret
<br>

- Criando a secrete no seu repositório que usaremos pra criação das variáveis de ambiente da AWS no container

```bash
     kubectl create secret generic pasecret --from-literal=AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID --from-literal=AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
 ```

 >  Lembrar de ver na documentação do repositório, como setar as variáveis de ambiente *https://artifacthub.io/packages/helm/bitnami/airflow*

### 2 - Instalando aplicação com bitnami/airflow
<br>

- Criando o namespace
- Instalando aplicação para criação da primeira release

```bash
    kubectl create namespace dev
    helm install airflow bitnami/airflow --namespace dev --debug
```

>   Concluida a instalação, a mensagem a seguir será retornada no terminal

```bash
    1.  Create a port-forward to the service:

        kubectl port-forward --namespace dev svc/airflow 8080:8080 &
        echo "Airflow URL: http://127.0.0.1:8080"

    2. Open a browser and access Airflow using the obtained URL.

    3. Get your Airflow login credentials by running:

        export AIRFLOW_PASSWORD=$(kubectl get secret --namespace "dev" airflow -o jsonpath="{.data.airflow-password}" | base64 --decode)
        echo User:     user
        echo Password: $AIRFLOW_PASSWORD
```

> É só seguir as instruções retornados no console para: 1 - Fazer o portfoard do cluster na maquina local, 2 - Acessa o console do airflow, 3 - Pegar as credeciais de login

### SETAR NO ARQUIVO DE VALORES O REPOSITÓRIO
<br>

- Alterar o arquivo values para setar o repositorio das dags

```yaml
    repositories:
      - repository: "https://github.com/QuartoDaBagunca/dags.git"
        ## Branch from repository to checkout
        ##
        branch: "main"
        ## An unique identifier for repository, must be unique for each repository
        ##
        name: ""
 ```

 ```bash
 helm upgrade airflow bitnami/airflow --namespace dev --values values_git.yaml
 ```

 >  Notase que no comando acima eu uso um arquivo de nome diferente do values.yaml original. Isso porque eu optei por salvar sa alterações em outra arquivo, deixando o values com os valores originais