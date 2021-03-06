---
title: Adicionar outras contas de armazenamento do Azure ao HDInsight
description: Saiba como adicionar outras contas de armazenamento do Azure a um cluster existente do HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: e29041942157e720cce3414f7b6e6904667c1894
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665483"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Adicionar outras contas de armazenamento ao HDInsight

Saiba como usar ações de script para adicionar mais *contas* de armazenamento do Azure ao HDInsight. As etapas neste documento adicionam uma *conta* de armazenamento a um cluster HDInsight baseado em Linux existente. Este artigo se aplica a *contas* de armazenamento (não a conta de armazenamento de cluster padrão) e não a armazenamento adicional, como [Azure data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) e [Azure data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> As informações neste documento são sobre como adicionar mais contas de armazenamento a um cluster depois que ele tiver sido criado. Para saber mais sobre como adicionar contas de armazenamento durante a criação do cluster, veja [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e mais](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nome e chave da conta de armazenamento. Consulte [gerenciar configurações de conta de armazenamento no portal do Azure](../storage/common/storage-account-manage.md).
* [Nome do cluster em maiúscula corretamente](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name).
* Se estiver usando o PowerShell, você precisará do módulo AZ.  Consulte [visão geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* Se você ainda não instalou o CLI do Azure, consulte [CLI (interface de linha de comando) do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).
* Se estiver usando o bash ou um prompt de comando do Windows, você também precisará de **JQ**, um processador JSON de linha de comando.  Veja [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/). Para o bash no Ubuntu no Windows 10, consulte [Guia de instalação do subsistema do Windows para Linux para Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).

## <a name="how-it-works"></a>Como ele funciona

Este script usa os seguintes parâmetros:

* __Nome da conta de armazenamento do Azure__: o nome da conta de armazenamento a ser adicionada ao cluster do HDInsight. Após a execução do script, o HDInsight pode ler e gravar dados armazenados nessa conta de armazenamento.

* __Chave da conta de armazenamento do Azure__: uma chave que concede acesso à conta de armazenamento.

* __-p__ (opcional): se especificado, a chave não é criptografada e armazenada no arquivo Core-site. xml como texto sem formatação.

Durante o processamento, o script executa as ações a seguir:

* Se a conta de armazenamento já existir na configuração de core-site.xml do cluster, o script será encerrado e nenhuma ação será executada.

* Verifica se a conta de armazenamento existe e se pode ser acessada usando a chave.

* Criptografa a chave usando a credencial do cluster.

* Adiciona a conta de armazenamento ao arquivo core-site.xml.

* Pare e reinicie o [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), e [HDFS do Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) serviços. Interromper e iniciar esses serviços permite que eles usem a nova conta de armazenamento.

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento em um local diferente do cluster HDInsight.

## <a name="the-script"></a>O script

__Local do script__: [https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__Requisitos__: o script deve ser aplicado nos __nós de cabeçalho__. Você não precisa marcar esse script como __persistente__, pois ele atualiza diretamente a configuração do Ambari para o cluster.

## <a name="to-use-the-script"></a>Para usar o script

Esse script pode ser usado no Azure PowerShell, CLI do Azure ou no portal do Azure.

### <a name="powershell"></a>PowerShell

Usando [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction). Substitua `CLUSTERNAME`, `ACCOUNTNAME`e `ACCOUNTKEY` pelos valores apropriados.

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>CLI do Azure

Usando [AZ hdinsight script-Action execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute).  Substitua `CLUSTERNAME`, `RESOURCEGROUP`, `ACCOUNTNAME`e `ACCOUNTKEY` pelos valores apropriados.

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Portal do Azure

Consulte [aplicar uma ação de script a um cluster em execução](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster).

## <a name="known-issues"></a>Problemas conhecidos

### <a name="storage-firewall"></a>Firewall de armazenamento

Se você optar por proteger sua conta de armazenamento com as restrições de **redes virtuais e firewalls** em **redes selecionadas**, certifique-se de habilitar a exceção **permitir serviços confiáveis da Microsoft...** para que o HDInsight possa acessar seu armazenamento considerar.

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>Contas de armazenamento não exibidas no Portal do Azure ou nas ferramentas

Ao exibir o cluster HDInsight no portal do Azure, a seleção da entrada __contas de armazenamento__ em __Propriedades__ não exibe as contas de armazenamento adicionadas por meio dessa ação de script. Azure PowerShell e CLI do Azure não exibem também a conta de armazenamento adicional.

As informações de armazenamento não são exibidas porque o script modifica apenas a configuração core-site.xml para o cluster. Essas informações não são usadas ao recuperar as informações de cluster usando as APIs de gerenciamento do Azure.

Para exibir informações da conta de armazenamento adicionadas ao cluster usando esse script, use a API REST do Ambari. Use os seguintes comandos para recuperar essas informações para seu cluster:

### <a name="powershell"></a>PowerShell

Substitua `CLUSTERNAME` pelo nome de cluster em maiúsculas e minúsculas. Substitua `ACCOUNTNAME` pelos nomes reais. Quando solicitado, insira a senha de logon do cluster.

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash

Substitua `CLUSTERNAME` pelo nome de cluster em maiúsculas e minúsculas. Substitua `PASSWORD` pela senha de administrador do cluster. Substitua `STORAGEACCOUNT` pelo nome da conta de armazenamento real.

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

Substitua `CLUSTERNAME` pelo nome do cluster em ambos os outros scripts corretamente. Primeiro, identifique a versão de configuração do serviço em uso digitando o comando a seguir:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

Substitua `ACCOUNTNAME` pelo nome da conta de armazenamento real. Em seguida, substitua `4` pela versão de configuração de serviço real e insira o comando:

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

As informações que retornam desse comando são semelhantes ao texto a seguir:

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

Esse texto é um exemplo de uma chave criptografada, que é usada para acessar a conta de armazenamento.

### <a name="unable-to-access-storage-after-changing-key"></a>Não é possível acessar o armazenamento após a alteração da chave

Se você alterar a chave de uma conta de armazenamento, o HDInsight não poderá mais acessar a conta de armazenamento. O HDInsight usa uma cópia em cache da chave no core-site.xml do cluster. Essa cópia armazenada em cache deve ser atualizada para corresponder à nova chave.

Executar a ação de script novamente __não__ atualiza a chave, pois o script verifica se já existe alguma entrada para a conta de armazenamento. Se uma entrada já existir, ela não fará nenhuma alteração.

Para contornar esse problema, remova a entrada existente da conta de armazenamento. Use as seguintes etapas para remover a entrada existente:

> [!IMPORTANT]  
> Não há suporte para a rotação da chave de armazenamento para a conta de armazenamento primária anexada a um cluster.

1. Em um navegador da Web, abra a interface de usuário na Web do Ambari para seu cluster do HDInsight. O URI é `https://CLUSTERNAME.azurehdinsight.net`. Substitua `CLUSTERNAME` pelo nome do cluster.

    Quando receber a solicitação, insira o usuário e a senha de logon HTTP para seu cluster.

2. Na lista de serviços à esquerda da página, selecione __HDFS__. Em seguida, selecione a guia __Configurações__ no centro da página.

3. No campo __Filtrar...__ , insira um valor de __fs.azure.account__. Isso retorna entradas para quaisquer outras contas de armazenamento que foram adicionadas ao cluster. Há dois tipos de entradas; __keyprovider__ e __key__. Ambas contêm o nome da conta de armazenamento como parte do nome da chave.

    Veja a seguir exemplos de entradas para uma conta de armazenamento chamada __mystorage__:

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. Depois de identificar as chaves para a conta de armazenamento que você precisa remover, use o ícone vermelho '-' à direita da entrada para excluí-la. Depois, use o botão __Salvar__ para salvar as alterações.

5. Depois que as alterações forem salvas, use a ação de script para adicionar a conta de armazenamento e o novo valor de chaves ao cluster.

### <a name="poor-performance"></a>Desempenho ruim

Se a conta de armazenamento estiver em uma região diferente do cluster do HDInsight, você poderá enfrentar um desempenho ruim. O acesso a dados em uma região diferente envia o tráfego de rede para fora do data center regional do Azure e para a internet pública, o que pode introduzir latência.

### <a name="additional-charges"></a>Custos adicionais

Se a conta de armazenamento estiver em uma região diferente do cluster do HDInsight, você poderá observar encargos adicionais em sua cobrança do Azure. Um encargo de saída é aplicado quando os dados saem de um data center regional. Esse encargo se aplica mesmo se o tráfego for destinado a outro data center do Azure em uma região diferente.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a adicionar mais contas de armazenamento a um cluster HDInsight existente. Para saber mais sobre as ações de script, confira [Personalizar clusters HDInsight com base em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)
