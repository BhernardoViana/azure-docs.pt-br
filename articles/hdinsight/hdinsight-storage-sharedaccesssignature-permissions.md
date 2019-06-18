---
title: Restringir o acesso usando Assinaturas de Acesso Compartilhado – HDInsight do Azure
description: Saiba como usar Assinaturas de Acesso Compartilhado para restringir o acesso do HDInsight a dados armazenados em blobs de armazenamento do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 7f7f6fe31afe35d9ccfd6ee33617bd7e4fbe46b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409554"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Usar Assinaturas de Acesso Compartilhado do Armazenamento do Azure para restringir o acesso a dados no HDInsight

O HDInsight tem acesso completo aos dados nas contas de Armazenamento do Azure associadas ao cluster. Você pode usar Assinaturas de Acesso Compartilhado no contêiner de blob para restringir o acesso aos dados. As Assinaturas de Acesso Compartilhado (SAS) são recursos das contas de armazenamento do Azure que permitem que você limite o acesso aos dados. Por exemplo, oferecendo acesso somente leitura aos dados.

> [!IMPORTANT]  
> Para uma solução que usa o Apache Ranger, considere usar HDInsight ingressado em domínio. Para obter mais informações, consulte o documento [Configurar HDInsight ingressado em domínio](./domain-joined/apache-domain-joined-configure.md).

> [!WARNING]  
> O HDInsight deve ter acesso completo ao armazenamento padrão para o cluster.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure.

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Um existente [contêiner de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Se estiver usando o PowerShell, será necessário o [Az módulo](https://docs.microsoft.com/powershell/azure/overview).

* Se desejam usar a CLI do Azure e você ainda não tiver instalado-lo, consulte [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Se usando [Python](https://www.python.org/downloads/), versão 2.7 ou superior.

* Se usando o C#, o Visual Studio deve ser a versão 2013 ou superior.

* O [esquema de URI](./hdinsight-hadoop-linux-information.md#URI-and-scheme) para sua conta de armazenamento. Isso seria `wasb://` para o armazenamento do Azure `abfs://` para o armazenamento do Azure Data Lake Gen2 ou `adl://` para Gen1 de armazenamento do Azure Data Lake. Se a transferência segura é habilitada para o armazenamento do Azure ou Data Lake armazenamento Gen2, o URI seria `wasbs://` ou `abfss://`, respectivamente Consulte também [transferência segura](../storage/common/storage-require-secure-transfer.md).

* Um cluster HDInsight existente para adicionar uma assinatura de acesso compartilhado para. Caso contrário, você poderá usar o Azure PowerShell para criar um cluster e adicionar uma Assinatura de Acesso Compartilhado durante a criação do cluster.

* Os arquivos de exemplo de [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Esse repositório contém os seguintes itens:

  * Um projeto do Visual Studio que pode criar um contêiner de armazenamento, a política armazenada e a SAS a ser usada com o HDInsight
  * Um script Python que pode criar um contêiner de armazenamento, a política armazenada e a SAS a ser usada com o HDInsight
  * Um script do PowerShell que pode criar um cluster HDInsight e configurá-lo para usar a SAS. Uma versão atualizada é usada mais adiante.
  * Um arquivo de exemplo: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>As Assinaturas de Acesso Compartilhado

Há duas formas de Assinaturas de Acesso Compartilhado:

* Ad hoc: a hora de início, o tempo de expiração e as permissões para a SAS são todos especificados no URI SAS.

* Política de acesso armazenada: uma política de acesso armazenada é definida em um contêiner de recurso, como um contêiner de blob. Uma política pode ser usada para gerenciar as restrições de uma ou mais assinaturas de acesso compartilhado. Quando você associa uma SAS a uma política de acesso armazenada, a SAS herda as restrições - a hora de início, a hora de expiração e as permissões - definidas para a política de acesso armazenada.

A diferença entre as duas formas é importante para um cenário fundamental: revogação. Uma SAS é uma URL, portanto qualquer pessoa que obtiver a SAS poderá usá-la, independentemente de quem a tiver solicitado em primeiro lugar. Se uma SAS for publicada publicamente, ela poderá ser usada por qualquer pessoa no mundo. Uma SAS distribuída será válida até que ocorra um destes quatro fatores:

1. A hora de expiração especificada na SAS é atingida.

2. O tempo de expiração especificado na política de acesso armazenada referenciada por SAS é atingido. Os seguintes cenários fazem com que o tempo de expiração seja atingido:

    * O intervalo de tempo esgotou-se.
    * A política de acesso armazenada foi modificada para ter um tempo de expiração no passado. Alterar o tempo de expiração é uma maneira de revogar a SAS.

3. A política de acesso armazenada referenciada pelas SAS é excluída, que é uma outra maneira de revogar a SAS. Se você recriar a política de acesso armazenado com o mesmo nome, todos os tokens SAS para a política anterior serão válidos (se o tempo de expiração na SAS não tiver passado). Se você estiver pretendendo revogar a SAS, use um nome diferente se recriar a política de acesso com uma hora de expiração no futuro.

4. A chave de conta usada para criar as SAS é regenerada. A regeneração da chave faz com que todos os aplicativos que usam a chave anterior falhem na autenticação. Atualize todos os componentes para a nova chave.

> [!IMPORTANT]  
> Um URI de assinatura de acesso compartilhado é associado com a chave de conta usada para criar a assinatura e a política de acesso armazenado associada (se houver). Se nenhuma política de acesso armazenado for especificada, a única maneira de revogar uma assinatura de acesso compartilhado é alterar a chave da conta.

É recomendável sempre usar políticas de acesso armazenadas. Ao usar políticas armazenadas, você pode revogar assinaturas ou estender a data de expiração conforme a necessidade. As etapas deste documento usam políticas de acesso armazenado para gerar a SAS.

Para saber mais sobre as Assinaturas de Acesso Compartilhado, consulte [Noções básicas sobre o modelo de SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Criar uma política armazenada e a SAS

Salve o token SAS que é produzido no final de cada método. O token será semelhante ao seguinte:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>Usando o PowerShell

Substitua `RESOURCEGROUP`, `STORAGEACCOUNT`, e `STORAGECONTAINER` com os valores apropriados para seu contêiner de armazenamento existente. Altere o diretório para `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` ou revisar a `-File` parâmetro para conter o caminho absoluto para `Set-AzStorageblobcontent`. Digite o seguinte comando do PowerShell:

```PowerShell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Usando a CLI do Azure

O uso de variáveis nesta seção se baseia em um ambiente do Windows. Pequenas variações serão necessárias para o bash ou em outros ambientes.

1. Substitua `STORAGEACCOUNT`, e `STORAGECONTAINER` com os valores apropriados para seu contêiner de armazenamento existente.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Defina a chave primária recuperada como uma variável para uso posterior. Substitua `PRIMARYKEY` recuperada com o valor na etapa anterior e, em seguida, insira o comando a seguir:

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Altere o diretório para `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` ou revisar a `--file` parâmetro para conter o caminho absoluto para `az storage blob upload`. Execute os comandos restantes:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>Usando Python

Abra o `SASToken.py` do arquivo e substitua `storage_account_name`, `storage_account_key`, e `storage_container_name` com os valores apropriados para seu contêiner de armazenamento existente e, em seguida, execute o script.

Talvez você precise executar `pip install --upgrade azure-storage` se você receber a mensagem de erro `ImportError: No module named azure.storage`.

### <a name="using-c"></a>Usando C#

1. Abra a solução no Visual Studio.

2. No Gerenciador de soluções, clique com botão direito no **SASExample** do projeto e selecione **propriedades**.

3. Escolha **Configurações** e adicione valores às seguintes entradas:

   * StorageConnectionString: A cadeia de conexão da conta de armazenamento para a qual você deseja criar uma política armazenada e uma SAS. O formato deve ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` onde `myaccount` é o nome de sua conta de armazenamento e `mykey` é a chave da conta de armazenamento.

   * ContainerName: O contêiner na conta de armazenamento para o qual você deseja restringir o acesso.

   * SASPolicyName: O nome a ser usado para a política armazenada que será criada.

   * FileToUpload: O caminho para um arquivo que é carregado no contêiner.

4. Execute o projeto. Salve o token da política SAS, o nome da conta de armazenamento e o nome do contêiner. Esses valores são usados ao associar a conta de armazenamento com seu cluster HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Usar a SAS com o HDInsight

Ao criar um cluster HDInsight, você deverá especificar uma conta de armazenamento primária e, opcionalmente, poderá especificar contas de armazenamento adicionais. Ambos os métodos de adição de armazenamento exigem acesso total às contas de armazenamento e aos contêineres usados.

Para usar uma Assinatura de Acesso Compartilhado para limitar o acesso a um contêiner, você deverá adicionar uma entrada personalizada para a configuração **core-site** do cluster. Você pode adicionar a entrada durante a criação do cluster usando o PowerShell ou após a criação de cluster usando o Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Criar um cluster que use a SAS

Substitua `CLUSTERNAME`, `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT`, `STORAGECONTAINER`, `STORAGEACCOUNT`, e `TOKEN` com os valores apropriados. Insira os comandos do PowerShell:

```powershell

$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey


# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext 

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig 

$config = $config | Add-AzHDInsightConfigValues `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Quando o nome de usuário ou a senha HTTP/s ou SSH forem solicitados, você deverá fornecer uma senha que atenda a estes critérios:
>
> * Deve ter pelo menos 10 caracteres.
> * Deve conter pelo menos um dígito.
> * Deve conter pelo menos um caractere não alfanumérico.
> * Deve conter pelo menos uma letra maiúscula ou minúscula.

Esse script demora um pouco para terminar, normalmente cerca de 15 minutos. Quando o script for concluído sem erros, o cluster terá sido criado.

### <a name="use-the-sas-with-an-existing-cluster"></a>Usar a SAS com um cluster existente

Se você tiver um cluster existente, você pode adicionar a SAS para o **core-site** configuração usando as seguintes etapas:

1. Abra a UI da Web do Ambari para seu cluster. O endereço para essa página é `https://YOURCLUSTERNAME.azurehdinsight.net`. Quando solicitado, faça a autenticação no cluster usando o nome do administrador (admin) e a senha usados na criação do cluster.

2. No lado esquerdo da interface do usuário da Web do Ambari, escolha **HDFS** e selecione a guia **Configurações** na metade da página.

3. Selecione a guia **Avançado** e então role até encontrar a seção **Core-site personalizado**.

4. Expanda a seção **Core-site personalizado**, role até o fim e escolha o link **Adicionar propriedade...** . Use os valores a seguir para os campos **Chave** e **Valor**:

   * **Chave**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **Valor**: A SAS retornada por um dos métodos executados anteriormente.

     Substitua `CONTAINERNAME` com o contêiner de nome é usado com o C# ou o aplicativo de SAS. Substitua `STORAGEACCOUNTNAME` com o nome de conta de armazenamento usado.

5. Clique no botão **Adicionar** para salvar essa chave e esse valor e clique no botão **Salvar** para salvar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("adicionando acesso de armazenamento SAS", por exemplo) e clique em **Salvar**.

    Clique em **OK** quando as alterações forem concluídas.

   > [!IMPORTANT]  
   > Você deverá reiniciar vários serviços antes que a alteração entre em vigor.

6. Na interface do usuário da Web do Ambari, selecione **HDFS** na lista à esquerda e, em seguida, selecione **Reiniciar Todos os Afetados** na lista suspensa **Ações de Serviço** à direita. Quando solicitado, selecione __Confirmar Reiniciar Tudo__.

    Repita esse processo para MapReduce2 e YARN.

7. Depois os serviços tiverem sido reiniciados, selecione cada uma e desabilite o modo de manutenção na lista suspensa **Ações de Serviço**.

## <a name="test-restricted-access"></a>Testar o acesso restrito

Use as etapas a seguir para verificar que você pode apenas ler e listar itens na conta de armazenamento SAS.

1. Conecte-se ao cluster. Substitua `CLUSTERNAME` com o nome do seu cluster e digite o seguinte comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Para listar o conteúdo do contêiner, use o seguinte comando no prompt:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Substitua `SASCONTAINER` com o nome do contêiner criado para a conta de armazenamento SAS. Substitua `SASACCOUNTNAME` com o nome da conta de armazenamento usada para a SAS.

    A lista inclui o arquivo carregado quando o contêiner e a SAS foram criados.

3. Use o comando a seguir para verificar se você pode ler o conteúdo do arquivo. Substitua os `SASCONTAINER` e `SASACCOUNTNAME` como na etapa anterior. Substitua `sample.log` com o nome do arquivo exibido no comando anterior:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Esse comando lista o conteúdo do arquivo.

4. Use o comando a seguir para baixar o arquivo para o sistema de arquivos local:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Esse comando baixa o arquivo para um arquivo local chamado **testfile.txt**.

5. Use o comando a seguir para carregar o arquivo local para um novo arquivo chamado **testupload.txt** no armazenamento SAS:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Você receberá uma mensagem semelhante ao texto a seguir:

        put: java.io.IOException

    Esse erro só ocorrerá porque o local do armazenamento é somente leitura+lista. Use o comando a para colocar os dados no armazenamento padrão do cluster, que pode ser gravável:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Dessa vez, a operação deverá ser concluída com êxito.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a adicionar armazenamento de acesso limitado ao seu cluster HDInsight, conheça outras maneiras de trabalhar com dados no cluster:

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

