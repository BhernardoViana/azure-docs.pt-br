---
title: Integração e entrega contínuas no Azure Data Factory
description: Aprenda a usar integração e entrega contínuas para mover os pipelines do Data Factory de um ambiente (desenvolvimento, teste, produção) para outro.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 7c5c1e91e97087bf28b03629659e5194f67c22b3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680025"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Integração e entrega contínua (CI / CD) no Azure Data Factory

## <a name="overview"></a>Visão geral

Integração Contínua é a prática de testar cada alteração feita em sua base de código automaticamente e o mais cedo possível. A entrega contínua segue os testes que ocorrem durante a integração contínua e envia por push as alterações para um sistema de preparo ou de produção.

Em Azure Data Factory, a integração contínua & entrega significa mover Data Factory pipelines de um ambiente (desenvolvimento, teste, produção) para outro. Para fazer a integração contínua & entrega, você pode usar a integração do Data Factory UX com modelos de Azure Resource Manager. O Data Factory UX pode gerar um modelo do Resource Manager a partir do menu suspenso do **modelo ARM** . Quando você seleciona **Exportar modelo ARM**, o portal gera o modelo do Resource Manager para o data factory e um arquivo de configuração que inclui todas as suas cadeias de conexão e outros parâmetros. Em seguida, você precisa criar um arquivo de configuração para cada ambiente (desenvolvimento, teste, produção). O arquivo de modelo do Resource Manager principal permanece o mesmo em todos os ambientes.

Para ver uma introdução de nove minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>Ciclo de vida da integração contínua

Abaixo está um exemplo de visão geral do ciclo de vida de integração e entrega contínua em um Azure data Factory configurado com Azure Repos git. Para obter mais informações sobre como configurar um repositório git, consulte [controle do código-fonte em Azure data Factory](source-control.md).

1.  Um data factory de desenvolvimento é criado e configurado com Azure Repos git, em que todos os desenvolvedores têm permissão para criar Data Factory recursos como pipelines e conjuntos de valores.

1.  À medida que os desenvolvedores fazem alterações na ramificação de recursos, eles depuram suas execuções de pipeline com suas alterações mais recentes. Para obter mais informações sobre como depurar uma execução de pipeline, consulte [desenvolvimento iterativo e depuração com Azure data Factory](iterative-development-debugging.md).

1.  Depois que os desenvolvedores estão satisfeitos com suas alterações, eles criam uma solicitação de pull de sua ramificação de recursos para o Branch mestre ou de colaboração para que suas alterações sejam revisadas por colegas.

1.  Depois que a solicitação de pull é aprovada e as alterações são mescladas no Branch mestre, elas podem publicar na fábrica de desenvolvimento.

1.  Quando a equipe estiver pronta para implantar as alterações na fábrica de testes e, em seguida, na fábrica de produção, elas exportarão o modelo do Resource Manager do Branch mestre.

1.  O modelo exportado do Resource Manager é implantado com arquivos de parâmetro diferentes na fábrica de teste e na fábrica de produção.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Criar um modelo do Resource Manager para cada ambiente

No menu suspenso **modelo do ARM** , selecione **Exportar modelo do ARM** para exportar o modelo do Resource Manager para seu data Factory no ambiente de desenvolvimento.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Em suas fábricas de dados de teste e de produção, selecione **Importar modelo de ARM**. Essa ação leva você até o Portal do Azure, onde você pode importar o modelo exportado. Selecione **criar seu próprio modelo no editor** para abrir o editor de modelos do Resource Manager.

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

Clique em **carregar arquivo** e selecione o modelo do Resource Manager gerado.

![](media/continuous-integration-deployment/continuous-integration-image4.png)

No painel configurações, insira os valores de configuração, como credenciais de serviço vinculado. Depois de terminar, clique em **comprar** para implantar o modelo do Resource Manager.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Cadeias de conexão

Informações sobre como configurar cadeias de conexão podem ser encontradas no artigo de cada conector. Por exemplo, para o Banco de Dados SQL do Azure, consulte [Copiar dados para ou do Banco de Dados SQL do Azure usando o Azure Data Factory](connector-azure-sql-database.md). Para verificar uma cadeia de conexão, você pode abrir a exibição de código para o recurso no Data Factory UX. Na exibição de código, a parte de chave de conta ou senha da cadeia de conexão é removida. Para abrir a visualização de código, selecione o ícone destacado na captura de tela a seguir.

![Abra a visualização de código para ver a string de conexão](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatize a integração contínua com os lançamentos do Azure Pipelines

Veja abaixo um guia para configurar uma versão Azure Pipelines, que automatiza a implantação de um data factory em vários ambientes.

![Diagrama de integração contínua com os pipelines do Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisitos

-   Uma assinatura do Azure vinculada a Team Foundation Server ou Azure Repos usando o [ponto de extremidade de serviço do Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Um Data Factory configurado com Azure Repos integração com o git.

-   Um [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) que contém os segredos para cada ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Configurar um lançamento do Azure Pipelines

1.  Na [experiência do usuário do Azure DevOps](https://dev.azure.com/), abra o projeto configurado com seu data Factory.

1.  No lado esquerdo da página, clique em **pipelines** e selecione **versões**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecione **novo pipeline** ou, se você tiver pipelines existentes, **novos**e, em seguida, **novo pipeline de lançamento**.

1.  Selecione o modelo de **trabalho vazio** .

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  No campo **nome do estágio** , insira o nome do seu ambiente.

1.  Selecione **Adicionar um artefato**e selecione o mesmo repositório configurado com sua data Factory. Escolha `adf_publish` como a branch padrão com a versão padrão mais recente.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Adicione uma tarefa de Implantação do Azure Resource Manager:

    a.  No modo de exibição de estágio, clique no link **Exibir tarefas do estágio** .

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Crie uma nova tarefa. Pesquise **implantação do grupo de recursos do Azure**e clique em **Adicionar**.

    c.  Na tarefa de Implantação, escolha a assinatura, o grupo de recursos e o local para o Data Factory de destino e forneça credenciais se necessário.

    d.  Na lista suspensa ação, selecione **criar ou atualizar grupo de recursos**.

    e.  Selecione **...** no campo **Modelo**. Procure a Azure Resource Manager modelo criar por meio da etapa **Importar modelo do ARM** em [criar um modelo do Resource Manager para cada ambiente](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment). Procure esse arquivo na pasta `<FactoryName>` do `adf_publish` branch.

    f.  Selecione **...** no **campo parâmetros do modelo.** para escolher o arquivo de parâmetros. Escolha o arquivo correto, dependendo de se você criou uma cópia ou se está usando o arquivo padrão *ARMTemplateParametersForFactory.json*.

    g.  Selecione **...** ao lado do campo **Substituir parâmetros de modelo** e preencha as informações do Data Factory de destino. Para credenciais provenientes do Key Vault, insira o nome do segredo entre aspas duplas. Por exemplo, se o nome do segredo for `cred1`, insira `"$(cred1)"`para seu valor.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. Selecione o modo de Implantação **Incremental**.

    > [!WARNING]
    > Se você selecionar o modo de implantação **completo** , os recursos existentes poderão ser excluídos, incluindo todos os recursos no grupo de recursos de destino que não estão definidos no modelo do Resource Manager.

1.  Salve o pipeline de lançamento.

1. Para disparar uma versão, clique em **criar versão**

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Obter segredos de Azure Key Vault

Se você tiver segredos para passar um modelo de Azure Resource Manager, é recomendável usar Azure Key Vault com a versão Azure Pipelines.

Há duas maneiras de lidar com os segredos:

1.  Adicione os segredos ao arquivo de parâmetros. Para obter mais informações, consulte [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Crie uma cópia do arquivo de parâmetros que é carregado para o branch de publicação e defina os valores dos parâmetros que você deseja obter do cofre de chaves com o seguinte formato:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   Quando você usa esse método, o segredo é extraído do cofre de chaves automaticamente.

    -   O arquivo de parâmetros também deve estar no branch de publicação.

1.  Adicione uma [tarefa do Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes da Implantação do Azure Resource Manager descrita na seção anterior:

    -   Selecione a guia **Tarefas** guia, crie uma nova tarefa, procure **Azure Key Vault** e adicione-o.

    -   Na tarefa do Key Vault, escolha a assinatura na qual você criou o cofre de chaves, forneça as credenciais se necessário e, em seguida, escolha o cofre de chaves.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Conceder permissões ao agente do Azure Pipelines

A tarefa Azure Key Vault poderá falhar com um erro de acesso negado se as permissões adequadas não estiverem presentes. Baixe os logs da versão e localize o arquivo `.ps1` com o comando para conceder permissões ao agente do Azure Pipelines. Você pode executar o comando diretamente ou pode copiar a ID de entidade de segurança do arquivo e adicionar a política de acesso manualmente no Portal do Azure. **Obter** e **listar** são as permissões mínimas necessárias.

### <a name="update-active-triggers"></a>Atualizar gatilhos ativos

A implantação poderá falhar se você tentar atualizar gatilhos ativos. Para atualizar os gatilhos ativos, você precisa interrompê-los manualmente e iniciá-los após a implantação. Você pode fazer isso por meio de uma tarefa do Azure PowerShell.

1.  Na guia tarefas da versão, adicione uma tarefa **do Azure PowerShell** .

1.  Escolha **Azure Resource Manager** como tipo de conexão e selecione sua assinatura.

1.  Escolha **Script Embutido** como tipo de script e, em seguida, forneça seu código. O exemplo abaixo interrompe os gatilhos:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Você pode seguir etapas semelhantes (com a função `Start-AzDataFactoryV2Trigger`) para reiniciar os gatilhos após a implantação.

> [!IMPORTANT]
> Em cenários de integração e implementação contínuos, o tipo de runtime de integração em diferentes ambientes deve ser o mesmo. Por exemplo, se você tiver um *Runtime de integração (IR) auto-hospedado* no ambiente de desenvolvimento, o mesmo IR deve ser do tipo *Auto-Hospedado* em outros ambientes, como teste e produção Além disso. Da mesma forma, se você estiver compartilhando tempos de execução de integração em vários estágios, será necessário configurar os Integration Runtimes como *Linked Self-Hosted* em todos os ambientes, como desenvolvimento, teste e produção.

#### <a name="sample-prepostdeployment-script"></a>Exemplo de script de pré/pós-implantação

Veja abaixo um script de exemplo para parar os gatilhos antes da implantação e reiniciar os gatilhos posteriormente. O script também inclui código para excluir recursos que foram removidos. Para instalar a versão mais recente do Azure PowerShell, consulte [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Usar parâmetros personalizados com o modelo do Resource Manager

Se você estiver no modo GIT, poderá substituir as propriedades padrão em seu modelo do Resource Manager para definir propriedades que são parametrizadas no modelo e propriedades que são embutidas em código. Talvez você queira substituir o modelo de parametrização padrão nesses cenários:

* Você usa CI/CD automatizado e deseja alterar algumas propriedades durante a implantação do Resource Manager, mas as propriedades não são parametrizadas por padrão.
* Sua fábrica é tão grande que o modelo padrão do Resource Manager é inválido porque ele tem mais do que os parâmetros máximos permitidos (256).

Sob essas condições, para substituir o modelo de parametrização padrão, crie um arquivo chamado *ARM-template-Parameters-Definition. JSON* na pasta raiz do repositório. O nome do arquivo deve corresponder exatamente. Data Factory tenta ler esse arquivo de qualquer ramificação que você esteja no portal de Azure Data Factory, não apenas da ramificação de colaboração. Você pode criar ou editar o arquivo de um Branch privado, no qual você pode testar suas alterações usando o **modelo de ARM de exportação** na interface do usuário. Em seguida, você pode mesclar o arquivo no Branch de colaboração. Se nenhum arquivo for encontrado, o modelo padrão será usado.


### <a name="syntax-of-a-custom-parameters-file"></a>Sintaxe de um arquivo de parâmetros personalizados

Aqui estão algumas diretrizes para usar ao criar o arquivo de parâmetros personalizados. O arquivo consiste em uma seção para cada tipo de entidade: gatilho, pipeline, serviço vinculado, conjunto de serviços, tempo de execução de integração e assim por diante.
* Insira o caminho da propriedade sob o tipo de entidade relevante.
* Ao definir um nome de propriedade como '\*' ', você indica que deseja parametrizar todas as propriedades abaixo dele (somente até o primeiro nível, não recursivamente). Você também pode fornecer qualquer exceção a isso.
* Quando você define o valor de uma propriedade como uma string, você indica que deseja parametrizar a propriedade. Use o formato `<action>:<name>:<stype>`.
   *  `<action>` pode ser um dos seguintes caracteres:
      * `=` significa manter o valor atual como o valor padrão para o parâmetro.
      * `-` significa não manter o valor padrão para o parâmetro.
      * `|` é um caso especial para segredos de Azure Key Vault para cadeias de conexão ou chaves.
   * `<name>` é o nome do parâmetro. Se estiver em branco, ele usará o nome da propriedade. Se o valor começar com um `-` caractere, o nome será reduzido. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seria reduzida para `AzureStorage1_connectionString`.
   * `<stype>` é o tipo de parâmetro. Se `<stype>` estiver em branco, o tipo padrão será `string`. Valores com suporte: `string`, `bool`, `number`, `object`e `securestring`.
* Ao especificar uma matriz no arquivo de definição, você indica que a propriedade correspondente no modelo é uma matriz. Data Factory itera através de todos os objetos na matriz usando a definição especificada no objeto Integration Runtime da matriz. O segundo objeto, uma cadeia de caracteres, torna-se o nome da propriedade, que é usada como o nome do parâmetro para cada iteração.
* Não é possível ter uma definição específica para uma instância de recurso. Qualquer definição se aplica a todos os recursos desse tipo.
* Por padrão, todas as cadeias de caracteres seguras, como segredos de Key Vault e cadeias de caracteres seguras, como cadeias de conexão, chaves e tokens, são parametrizadas.
 
### <a name="sample-parameterization-template"></a>Modelo de parametrização de exemplo

Veja abaixo um exemplo de como um modelo de parametrização pode ser:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Abaixo está uma explicação de como o modelo acima é construído, dividido por tipo de recurso.

#### <a name="pipelines"></a>Pipelines
    
* Qualquer propriedade no caminho Activities/typeproperties/waitTimeInSeconds é parametrizada. Qualquer atividade em um pipeline que tenha uma propriedade de nível de código chamada `waitTimeInSeconds` (por exemplo, a atividade `Wait`) é parametrizada como um número, com um nome padrão. Mas ele não terá um valor padrão no modelo do Resource Manager. Será uma entrada obrigatória durante a implantação do Gerenciador de recursos.
* Da mesma forma, uma propriedade chamada `headers` (por exemplo, em uma atividade de `Web`) é parametrizada com o tipo `object` (JObject). Ele tem um valor padrão, que é o mesmo valor que na fábrica de origem.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Todas as propriedades no caminho `typeProperties` são parametrizadas com seus respectivos valores padrão. Por exemplo, há duas propriedades em Propriedades de tipo **IntegrationRuntimes** : `computeProperties` e `ssisProperties`. Ambos os tipos de propriedade são criados com seus respectivos valores e tipos padrão (objeto).

#### <a name="triggers"></a>Gatilhos

* Em `typeProperties`, duas propriedades são parametrizadas. O primeiro é `maxConcurrency`, que é especificado para ter um valor padrão e é do tipo`string`. Ele tem o nome de parâmetro padrão de `<entityName>_properties_typeProperties_maxConcurrency`.
* A propriedade `recurrence` também é parametrizada. Sob ele, todas as propriedades nesse nível são especificadas para serem parametrizadas como cadeias de caracteres, com valores padrão e nomes de parâmetro. Uma exceção é a propriedade `interval`, que é parametrizada como um tipo numérico, e com o nome do parâmetro sufixado com `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Da mesma forma, a propriedade `freq` é uma cadeia de caracteres e é parametrizada como uma cadeia de caracteres. No entanto, a propriedade `freq` é parametrizada sem um valor padrão. O nome é reduzido e sufixado. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Os serviços vinculados são exclusivos. Como os serviços vinculados e os conjuntos de linhas têm uma ampla gama de tipos, você pode fornecer personalização específica de tipo. Neste exemplo, todos os serviços vinculados do tipo `AzureDataLakeStore`, um modelo específico será aplicado e, para todos os outros (via \*), um modelo diferente será aplicado.
* A propriedade `connectionString` será parametrizada como um valor de `securestring`, não terá um valor padrão e terá um nome de parâmetro abreviado com `connectionString`.
* A propriedade `secretAccessKey` é uma `AzureKeyVaultSecret` (por exemplo, em um `AmazonS3` serviço vinculado). Ele é parametrizado automaticamente como um Azure Key Vault segredo e buscado a partir do cofre de chaves configurado. Você também pode parametrizar o cofre de chaves em si.

#### <a name="datasets"></a>Conjuntos de dados

* Embora a personalização específica de tipo esteja disponível para conjuntos de valores, a configuração pode ser fornecida sem ter explicitamente uma configuração de nível \*. No exemplo acima, todas as propriedades de DataSet em `typeProperties` são parametrizadas.

### <a name="default-parameterization-template"></a>Modelo de parametrização padrão

Veja abaixo o modelo de parametrização padrão atual. Se você só precisa adicionar um ou alguns parâmetros, editar isso diretamente pode ser útil, pois você não perderá a estrutura de parametrização existente.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

Veja abaixo um exemplo de como adicionar um único valor ao modelo de parametrização padrão. Só queremos adicionar uma ID de cluster interativo do databricks interativo para um serviço vinculado do databricks ao arquivo de parâmetros. Observe que o arquivo abaixo é o mesmo que o arquivo acima, exceto pelo `existingClusterId` incluído no campo de propriedades de `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Modelos Vinculados do Resource Manager

Se você configurou CI/CD (integração e implantação contínuas) para suas fábricas de dados, poderá encontrar os limites do modelo de Azure Resource Manager à medida que sua fábrica aumentar. Um exemplo de limite é o número máximo de recursos em um modelo do Resource Manager. Para acomodar grandes fábricas, juntamente com a geração do modelo completo do Resource Manager para uma fábrica, Data Factory agora gera modelos vinculados do Resource Manager. Com esse recurso, toda a carga de fábrica é dividida em vários arquivos, de forma que você não fique em limites.

Se você tiver configurado o Git, os modelos vinculados serão gerados e salvos junto com os modelos completos do Resource Manager na ramificação `adf_publish` em uma nova pasta chamada `linkedTemplates`.

![Pasta de modelos vinculados do Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Os modelos vinculados do Resource Manager geralmente têm um modelo mestre e um conjunto de modelos filho vinculados ao mestre. O modelo pai é chamado `ArmTemplate_master.json`, e os modelos filho são nomeados com o padrão `ArmTemplate_0.json`, `ArmTemplate_1.json` e assim por diante. Para usar modelos vinculados em vez do modelo completo do Resource Manager, atualize sua tarefa de CI/CD para apontar para `ArmTemplate_master.json` em vez de `ArmTemplateForFactory.json` (o modelo completo do Resource Manager). O Resource Manager também requer que você carregue os modelos vinculados em uma conta de armazenamento para que eles possam ser acessados pelo Azure durante a implantação. Para obter mais informações, consulte [Implantar Modelos ARM Vinculados com VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Lembre-se de adicionar os scripts do Data Factory no pipeline de CI/CD antes e depois a tarefa de implantação.

Se você não tiver o Git configurado, os modelos vinculados são acessíveis por meio do gesto **Exportar modelo ARM**.

## <a name="hot-fix-production-branch"></a>Branch de produção de correção automática

Se você implantar uma fábrica na produção e perceber que há um bug que precisa ser corrigido imediatamente, mas não é possível implantar o Branch de colaboração atual, talvez seja necessário implantar uma correção automática. Essa abordagem é conhecida como engenharia de correção rápida ou QFE. 

1.  No Azure DevOps, vá para a versão que foi implantada para produção e localize a última confirmação implantada.

2.  Na mensagem de confirmação, obtenha a ID de confirmação da ramificação de colaboração.

3.  Crie uma nova ramificação de Hot Fix a partir dessa confirmação.

4.  Vá para o Azure Data Factory UX e alterne para essa ramificação.

5.  Usando o Azure Data Factory UX, corrija o bug. Teste suas alterações.

6.  Depois que a correção tiver sido verificada, clique em **Exportar modelo do ARM** para obter o modelo do Gerenciador de recursos de correção automática.

7.  Faça check-in manual dessa compilação para a ramificação adf_publish.

8.  Se você configurou o pipeline de liberação para disparar automaticamente com base em adf_publish check-ins, uma nova versão será iniciada automaticamente. Caso contrário, enfileirar manualmente uma versão.

9.  Implante a versão de Hot Fix para as fábricas de teste e produção. Esta versão contém a carga de produção anterior mais a correção feita na etapa 5.

10. Adicione as alterações da ramificação Hot-Fix para desenvolvimento para que as versões posteriores não sejam executadas no mesmo bug.

## <a name="best-practices-for-cicd"></a>Práticas recomendadas para CI/CD

Se você estiver usando a integração de Git com seu data factory e tiver um pipeline de CI/CD que mova as alterações de Desenvolvimento para Teste e então para Produção, recomendamos as seguintes melhores práticas:

-   **Integração do Git**. Você só precisa configurar seu data factory de desenvolvimento com a integração do git. As alterações no teste e na produção são implantadas por meio de CI/CD e não precisam de integração com o git.

-   **Script de CI/CD do Data Factory**. Antes da etapa de implantação do Gerenciador de recursos no CI/CD, determinadas tarefas são necessárias, como parar/iniciar de gatilhos e limpeza. É recomendável usar scripts do PowerShell antes e depois da implantação. Para obter mais informações, consulte [Atualizar gatilhos ativos](#update-active-triggers). 

-   **Integration Runtimes e compartilhamento**. Os tempos de execução de integração não são alterados com frequência e são semelhantes em todos os estágios em seu CI/CD. Como resultado, o Data Factory espera que você tenha o mesmo nome e o mesmo tipo de Integration Runtimes em todos os estágios de CI/CD. Se você estiver procurando compartilhar tempos de execução de integração em todos os estágios, considere usar uma fábrica ternário apenas para conter os tempos de execução de integração compartilhados. Você pode usar essa fábrica compartilhada em todos os seus ambientes como um tipo de tempo de execução de integração vinculado.

-   **Key Vault**. Ao usar Azure Key Vault serviços vinculados, você pode aproveitar ainda mais as vantagens dele mantendo cofres de chaves separados para ambientes diferentes. Você também pode configurar níveis de permissão separados para cada um deles. Por exemplo, talvez você não queira que os membros da equipe tenham permissões para os segredos de produção. Se você seguir essa abordagem, é recomendável manter os mesmos nomes de segredo em todos os estágios. Se você mantiver os mesmos nomes, não precisará alterar seus modelos do Resource Manager em ambientes de CI/CD, pois a única coisa que muda é o nome do cofre de chaves, que é um dos parâmetros do modelo do Resource Manager.

## <a name="unsupported-features"></a>Recursos sem suporte

- Por design _, o ADF não permite_ confirmações de seleção de Cherry ou publicação seletiva de recursos. As publicações incluirão **todas** as alterações feitas no data Factory

    - As entidades do data Factory dependem umas das outras, por exemplo, os gatilhos dependem de pipelines, os pipelines dependem de conjuntos de dados e outros pipelines, etc. A publicação seletiva de um subconjunto de recursos _pode_ levar a comportamentos e erros inesperados
    - Em raras ocasiões em que a publicação seletiva é necessária, você pode considerar uma correção automática. Para obter mais informações, consulte [Branch de produção de correção automática](#hot-fix-production-branch)

-   Não é possível publicar de branches particulares

-   A partir de agora, você não pode hospedar projetos no bitbucket
