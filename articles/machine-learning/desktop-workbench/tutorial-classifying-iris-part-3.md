---
title: Implantar um tutorial de modelo para os serviços do Azure Machine Learning
description: Este tutorial completo mostra como usar os serviços do Azure Machine Learning de ponta a ponta. Essa é a parte três e discute o modelo de implantação.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/13/2018
ms.openlocfilehash: 0b56b15af7c573304db9a1b6e6e9f37453a63458
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-3-classify-iris-deploy-a-model"></a>Tutorial 3: Classificar Iris – implantar um modelo
O Azure Machine Learning (versão prévia) é uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas de dados profissionais. Os cientistas de dados podem usá-los para preparar dados, desenvolver experimentos e implantar modelos em escala de nuvem.

Este tutorial é a **terceira e última parte de uma série**. Nessa parte do tutorial, você usará o Azure Machine Learning (versão prévia) para:

> [!div class="checklist"]
> * Localizar o arquivo de modelo.
> * Gerar um script de pontuação e um arquivo de esquema.
> * Preparar o ambiente.
> * Criar um serviço Web em tempo real.
> * Executar o serviço Web em tempo real.
> * Examinar os dados de blob de saída. 

Este tutorial usa o [Conjunto de dados de flor de íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal. 

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, você precisará:
- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
- Uma conta de experimentação e o Azure Machine Learning Workbench instalado, conforme descrito neste [início rápido](../service/quickstart-installation.md)
- O modelo de classificação de [Tutorial parte 2](tutorial-classifying-iris-part-2.md)
- Um mecanismo do Docker instalado e em execução localmente

## <a name="download-the-model-pickle-file"></a>Baixe o arquivo de pickle do modelo
Na parte anterior do tutorial, o script **iris_sklearn.py** foi executado localmente no Machine Learning Workbench. Essa ação serializou o modelo de regressão logística usando o popular pacote de serialização de objeto do Python [pickle](https://docs.python.org/3/library/pickle.html). 

1. Abra o aplicativo Machine Learning Workbench. Em seguida, abra o projeto **myIris** criado na parte anterior da série de tutoriais.

2. Após abrir o projeto, selecione o botão **Arquivos** (ícone de pasta) no painel à esquerda para abrir a ista de arquivos na sua pasta de projetos.

3. Selecione o arquivo **iris_sklearn.py**. O código Python abre em uma nova guia do editor de texto dentro do workbench.

4. Examine o arquivo **iris_sklearn.py** para ver onde o arquivo pickle foi gerado. Selecione Control + F para abrir a caixa de diálogo **Localizar** e, em seguida, localize a palavra **pickle** no código Python.

   Este trecho de código mostra como o arquivo de saída processado pelo pickle foi gerado. O arquivo pickle de saída é denominado **model.pkl** no disco. 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. Localize o arquivo de modelo pickle nos arquivos de saída de uma execução anterior.
   
   Quando você executou o script **iris_sklearn.py**, o arquivo de modelo foi gravado na pasta **outputs** com o nome **model.pkl**. Essa pasta existe no ambiente de execução em que você opta por executar o script e não na pasta local do projeto. 
   
   a. Para localizar o arquivo, selecione o botão **Execuções** (ícone de relógio) na barra de ferramentas para abrir a lista de **Todas as Execuções**. 

   b. A guia **Todas as Execuções** será aberta. Na tabela de execuções, selecione uma das execuções recentes em que o destino era **local** e o nome do script era **iris_sklearn.py**. 

   c. O painel **Propriedades da Execução** será aberto. No canto superior direito do painel, observe a seção **Saídas**.

   d. Para baixar o arquivo pickle, selecione a caixa de seleção ao lado do arquivo **model.pkl** e, em seguida, selecione **Baixar**. Salve o arquivo na raiz da pasta do projeto. O arquivo é necessário nas etapas posteriores.

   ![Baixe o arquivo de pickle](media/tutorial-classifying-iris/download_model.png)

   Leia mais sobre a pasta `outputs` em [Como ler e gravar arquivos de dados grandes](how-to-read-write-files.md).

## <a name="get-the-scoring-script-and-schema-files"></a>Gerar um script de pontuação e arquivos de esquema
Para implantar o serviço Web junto com o arquivo de modelo, você também precisa de um script de pontuação. Opcionalmente, é necessário um esquema para os dados de entrada do serviço Web. O script de pontuação carrega o arquivo **model.pkl** da pasta atual e usa-o para gerar novas previsões.

1. Abra o aplicativo Machine Learning Workbench. Em seguida, abra o projeto **myIris** criado na parte anterior da série de tutoriais.

2. Após abrir o projeto, selecione o botão **Arquivos** (ícone de pasta) no painel à esquerda para abrir a ista de arquivos na sua pasta de projetos.

3. Selecione o arquivo **iris_score.py**. O script de Python é aberto. Esse arquivo é usado como o arquivo de pontuação.

   ![Arquivo de pontuação](media/tutorial-classifying-iris/model_data_collection.png)

4. Para obter o arquivo de esquema, execute o script. Selecione o ambiente **local** e o script **score_iris.py** na barra de comandos e, em seguida, selecione **Executar**. 

   Esse script cria um arquivo JSON na seção **Saídas**, que captura o esquema de dados de entrada exigido pelo modelo.

6. Observe o painel **Trabalhos** no lado direito do **Painel do Projeto**. Aguarde que a versão mais recente do trabalho **score_iris.py** exiba o status verde **Concluído**. Em seguida, selecione o hiperlink **score_iris.py** da execução de trabalho mais recente para ver os detalhes da execução. 

7. Na página **Propriedades de Execução**, na seção **Saídas**, selecione o arquivo recém-criado **service_schema.json**. Marque a caixa de seleção ao lado do nome de arquivo e, em seguida, selecione **Baixar**. Salve o arquivo na pasta raiz do projeto.

8. Retorne à guia anterior onde você abriu o script **score_iris.py**. Ao usar a coleta de dados, é possível capturar previsões e entradas de modelo do serviço Web. As etapas a seguir são de especial interesse para a coleta de dados.

9. Examine o código na parte superior do arquivo, que importa a classe **ModelDataCollector**, pois ela contém a funcionalidade de coleta de dados de modelo:

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. Examine as seguintes linhas de código na função **init()**, que instancia **ModelDataCollector**:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
    ```

11. Examine as seguintes linhas de código na função **run(input_df)**, já que ela coleta dados de entrada e de previsão:

    ```python
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

Agora você está pronto para preparar o ambiente para operacionalizar o modelo.

## <a name="prepare-to-operationalize-locally"></a>Preparar para operacionalizar localmente
Use a implantação de _modo local_ para executar em contêineres do Docker no computador local.

Você pode usar o _modo local_ para desenvolvimento e teste. O mecanismo do Docker deve ser executado localmente para concluir as etapas a seguir para operacionalizar o modelo. Você pode usar o sinalizador `-h` no final de cada comando para mostrar a mensagem de ajuda correspondente.

>[!NOTE]
>Se você não tem o mecanismo do Docker localmente, você ainda pode prosseguir criando um cluster no Azure para implantação. Mantenha o cluster para reutilização, ou exclua-o após o tutorial, para que você não incorra em encargos contínuos.

>[!NOTE]
>Serviços Web implantados localmente não aparecem na lista do Portal do Azure de serviços. Eles serão executados no Docker no computador local.

1. Abra a interface de linha de comando (CLI) do Azure.
   No aplicativo Machine Learning Workbench, no menu **Arquivo**, selecione **Abrir o prompt de comando**.

   O prompt de linha de comando é aberto em sua localização atual da pasta do projeto **c:\temp\myIris>**.


2. Verifique se o provedor de recursos do Azure **Microsoft.ContainerRegistry** está registrado em sua assinatura. Você deve registrar este provedor de recursos antes de criar um ambiente na etapa 3. Você pode verificar se ele já está registrado usando o seguinte comando:
   ``` 
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table 
   ``` 

   Você deve ver uma saída como a abaixo: 
   ```
   Provider                                  Status 
   --------                                  ------
   Microsoft.Authorization                   Registered 
   Microsoft.ContainerRegistry               Registered 
   microsoft.insights                        Registered 
   Microsoft.MachineLearningExperimentation  Registered 
   ... 
   ```
   
   Se o **Microsoft.ContainerRegistry** não estiver registrado, você pode registrá-lo usando o seguinte comando:
   ``` 
   az provider register --namespace Microsoft.ContainerRegistry 
   ```
   O registro pode levar alguns minutos. Você pode verificar seu status usando o comando **az provider list** anterior ou o comando a seguir:
   ``` 
   az provider show -n Microsoft.ContainerRegistry 
   ``` 

   A terceira linha da saída exibe **"registrationState": "Registrar"**. Aguarde alguns minutos e repita o comando **show** até que a saída exiba **"registrationState": "Registrado"**.

   >[!NOTE] 
   Se você estiver implantando em um cluster ACS, precisará registrar o provedor de recursos **Microsoft.ContainerService** também usando exatamente a mesma abordagem.

3. Crie o ambiente. Você deve executar essa etapa uma vez para cada ambiente. Por exemplo, executá-la uma vez para o ambiente de desenvolvimento e uma vez para produção. Use o _modo local_ para esse primeiro ambiente. Você pode tentar usar a opção `-c` ou `--cluster` no comando a seguir para configurar um ambiente no _modo de cluster_ posteriormente.

   O seguinte comando de instalação exige que você tenha acesso de Colaborador à assinatura. Se não tiver, precisará pelo menos do acesso Colaborador para o grupo de recursos no qual está implantando. Nesse último caso, precisará especificar o nome do grupo de recursos como parte do comando setup usando o sinalizador `-g`. 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   Siga as instruções na tela para provisionar uma conta de armazenamento para armazenar imagens do Docker, um registro de contêiner do Azure que lista as imagens do Docker e uma conta do Azure Application Insights para coletar telemetria. Se você usar a opção `-c`, o comando criará um cluster do serviço de contêiner adicionalmente.
   
   O nome do cluster é uma maneira de identificar o ambiente. O local deve ser o mesmo que o local da conta de Gerenciamento de Modelos criada no portal do Azure.

   Para verificar se o ambiente foi instalado com êxito, use o seguinte comando para verificar o status:

   ```azurecli
   az ml env show -n <deployment environment name> -g <existing resource group name>
   ```

   Verifique se o "Estado de Provisionamento" tem o valor "Êxito", conforme mostrado, antes de configurar o ambiente na etapa 5:

   ![Estado de Provisionamento](media/tutorial-classifying-iris/provisioning_state.png)
 
3. Se você não criou uma conta de Gerenciamento de Modelo anteriormente neste tutorial, faça isso agora. Essa é uma configuração que só precisa ser realizada uma vez.
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
4. Configurar a conta de Gerenciamento de Modelos.
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

5. Defina o ambiente.

   Depois que a configuração for concluída, use o comando a seguir para definir as variáveis de ambiente necessárias para operacionalizar o ambiente. Use o mesmo nome de ambiente que você usou anteriormente na etapa 3. Use o mesmo nome de grupo de recursos que foi enviado na janela de comando quando o processo de configuração foi concluído.

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

6. Para verificar se você configurou corretamente seu ambiente operacionalizado para a implantação de serviço Web local, digite o seguinte comando:

   ```azurecli
   az ml env show
   ```

Agora você está pronto para criar o serviço Web em tempo real.

>[!NOTE]
>Você pode reutilizar sua conta de Gerenciamento de Modelos e o ambiente para implantações de serviço Web posteriores. Você não precisa criá-los para cada serviço Web. Uma conta ou um ambiente pode ter vários serviços Web associados a ele.

## <a name="create-a-real-time-web-service-in-one-command"></a>Criar um serviço Web em tempo real em um comando
1. Use o comando a seguir para criar um serviço Web em tempo real:

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   Esse comando gera uma ID de serviço Web que você pode usar mais tarde.

   As opções a seguir são usadas com o comando **az ml service create realtime**:

   * `-f`: o nome do arquivo de script de pontuação.

   * `--model-file`: o arquivo de modelo. Nesse caso, é o arquivo pickled model.pkl.

   * `-s`: o esquema do serviço. Isso foi gerado em uma etapa anterior, executando o script **score_iris.py** localmente.

   * `-n`: o nome do aplicativo, que deve ter só letras minúsculas.

   * `-r`: o tempo de execução do modelo. Nesse caso, é um modelo de Python. Os tempos de execução válidos são `python` e `spark-py`.

   * `--collect-model-data true`: isso habilita a coleta de dados.

   * `-c`: caminho para o arquivo de dependências conda onde os pacotes adicionais são especificados.

   >[!IMPORTANT]
   >O nome do serviço, que também é o novo nome de imagem do Docker, deverá ser composto somente de letras minúsculas. Caso contrário, você obterá um erro. 

2. Quando você executar o comando, o modelo e o arquivo de pontuação serão carregados para a conta de armazenamento que você criou como parte da configuração do ambiente. O processo de implantação cria uma imagem do Docker com o modelo, o esquema e o arquivo de pontuação e envia-o por push para o registro de contêiner do Azure: **\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**. 

   O comando efetua pull dessa imagem localmente para seu computador e inicia um contêiner do Docker com base nessa imagem. Se seu ambiente estiver configurado no modo de cluster, o contêiner do Docker será implantado no cluster Kubernetes dos Serviços de Nuvem do Azure em vez disso.

   Como parte da implantação, um ponto de extremidade REST HTTP para o serviço Web é criado no computador local. Depois de alguns minutos, o comando deve terminar com uma mensagem de êxito. O serviço Web está pronto para a ação!

3. Você pode ver o contêiner do Docker em execução usando o comando **docker ps**:

   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Criar um serviço Web em tempo real usando comandos separados
Como uma alternativa para o comando **az ml service create realtime** mostrado anteriormente, você também pode executar as etapas separadamente. 

Primeiro, registre o modelo. Em seguida, gere o manifesto, compile a imagem do Docker e crie o serviço Web. Essa abordagem passo a passo fornece mais flexibilidade em cada etapa. Além disso, você pode reutilizar entidades geradas nas etapas anteriores e recompilar entidades apenas quando necessário.

1. Registre o modelo, fornecendo o nome do arquivo pickle.

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   Este comando gera uma ID de modelo.

2. Criar um manifesto.

   Para criar um manifesto, use o comando a seguir e forneça a saída de ID de modelo da etapa anterior:

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   Este comando gera uma ID de manifesto.

3. Crie uma imagem do Docker.

   Para criar uma imagem do Docker, use o comando a seguir e forneça a saída do valor da ID de manifesto da etapa anterior. Você também pode incluir as dependências de conda usando a alternância `-c`.

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
   ```
   Este comando gera uma ID de imagem do Docker.
   
4. Criar o serviço.

   Para criar um serviço, use o comando a seguir e forneça a saída de ID de imagem da etapa anterior:

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   Este comando gera uma ID de serviço Web.

Agora você está pronto para executar o serviço Web.

## <a name="run-the-real-time-web-service"></a>Executar o serviço Web em tempo real

Para testar o serviço Web **irisapp** em execução, use um registro codificado por JSON contendo uma matriz de quatro números aleatórios.

1. O serviço Web inclui dados de exemplo. Ao executar em modo local, você pode chamar o comando **az ml service usage realtime**. Essa chamada recupera um exemplo de comando de execução que você pode usar para testar o serviço. A chamada também recupera a URL de pontuação que você poderá usar para incorporar o serviço em seu próprio aplicativo personalizado.

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. Para testar o serviço, execute o comando de execução de serviço retornado:
    
   ```azurecli
   az ml service run realtime -i <web service ID> -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```

   A saída é **“Iris-setosa”**, que é a classe prevista. (O seu resultado pode ser diferente.) 

## <a name="view-the-collected-data-in-azure-blob-storage"></a>Exiba os dados coletados no Armazenamento de Blobs do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Localize todas as suas contas de armazenamento. Para isso, selecione **Todos os serviços**.

3. Na caixa de pesquisa, insira **Contas de armazenamento** e, em seguida, selecione Enter.

4. Na caixa de pesquisa **Contas de armazenamento**, selecione o recurso de **Conta de armazenamento** correspondente ao seu ambiente. 

   > [!TIP]
   > Para determinar qual conta de armazenamento está em uso:
   > 1. Abra o Machine Learning Workbench.
   > 2. Selecione o projeto no qual você está trabalhando.
   > 3. Abra um prompt de linha de comando no menu **Arquivo**.
   > 4. No prompt da linha de comando, insira `az ml env show -v` e verifique o valor *storage_account*. Esse é o nome da sua conta de armazenamento.

5. Depois que o painel **Conta de armazenamento** abrir, selecione **Blobs** na seção **Serviços**. Localize o contêiner nomeado **modeldata**. 
 
   Se você não vir todos os dados, você precisará aguardar até 10 minutos após a primeira solicitação de serviço Web para ver a propagação dos dados para a conta de armazenamento.

   Os dados fluem para blobs com o seguinte caminho de contêiner:

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. Você pode consumir esses dados no armazenamento de Blobs do Azure. Há uma variedade de ferramentas que usam tanto o software da Microsoft quanto ferramentas de software livre, como:

   * Machine Learning: abra o arquivo CSV, adicionando o arquivo CSV como uma fonte de dados.

   * Excel: abra os arquivos CSV diários como uma planilha.

   * [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/): crie gráficos contendo dados extraídos por pull de dados CSV em blobs.

   * [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): carregue dados CSV em uma tabela Hive e execute consultas SQL diretamente nos blobs.

   * [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): crie um DataFrame com uma grande porção de dados CSV.

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas
Nessa terceira parte da série de tutoriais, você aprendeu a usar os o Machine Learning para:
> [!div class="checklist"]
> * Localizar o arquivo de modelo.
> * Gerar um script de pontuação e um arquivo de esquema.
> * Preparar o ambiente.
> * Criar um serviço Web em tempo real.
> * Executar o serviço Web em tempo real.
> * Examinar os dados de blob de saída. 

Você executou com sucesso um script de treinamento em vários ambientes de computação. Você também criou, serializou e operacionalizou um modelo por meio de um serviço Web baseado em Docker. 

Você agora está pronto para realizar preparação de dados avançada:
> [!div class="nextstepaction"]
> [Tutorial 4 - Preparação de dados avançada](tutorial-bikeshare-dataprep.md)
