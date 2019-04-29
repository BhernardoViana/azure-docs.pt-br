---
title: Criar clusters Apache Hadoop usando modelos – Azure HDInsight
description: Aprenda a criar clusters para o HDInsight usando modelos do Resource Manager
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 73402421a87d2cf14719ff34201890ea96c90519
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097395"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Criar clusters Apache Hadoop no HDInsight usando modelos do Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Neste artigo, você aprenderá várias maneiras de criar clusters do Azure HDInsight usando modelos do Azure Resource Manager. Para saber mais, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-template-deploy.md). Para aprender sobre outros recursos e outras ferramentas de criação de cluster, clique no seletor de guia na parte superior dessa página ou consulte [Métodos de criação de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para seguir as instruções neste artigo, você precisará de:

* Uma [assinatura do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* O Azure PowerShell e / ou o CLI Clássico do Azure.

### <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos
Um modelo do Resource Manager torna mais fácil de criar os seguintes recursos para seu aplicativo em uma única operação coordenada:
* Clusters HDInsight e seus recursos dependentes (como a conta de armazenamento padrão).
* Outros recursos (como o Banco de Dados SQL do Azure para usar o [Apache Sqoop](https://sqoop.apache.org/)).

No modelo, você deve definir os recursos que são necessários para o aplicativo. Você também pode especificar parâmetros de implantação para inserir valores para ambientes diferentes. O modelo consiste em JSON e expressões que você pode usar para criar valores para sua implantação.

Você pode encontrar exemplos de modelo do HDInsight em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Use o [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) de plataforma cruzada com a [extensão do Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) ou um editor de texto para salvar o modelo em um arquivo da estação de trabalho. 

Para obter mais informações sobre modelos do Resource Manager, consulte os seguintes artigos e exemplos:

* [Criar modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Implantar um aplicativo com o modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md)
* Referência de modelo de [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Gerar modelos

O Resource Manager permite que você exporte um modelo do Resource Manager dos recursos existentes em sua assinatura usando diferentes ferramentas. Você pode usar esse modelo gerado para saber mais sobre a sintaxe do modelo ou automatizar a reimplantação de sua solução, conforme o necessário. Para obter mais informações, consulte [exportar modelos](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="deploy-using-the-portal"></a>Implantar usando o portal

Você pode implantar um modelo do Resource Manager usando o portal do Azure. Para obter mais informações, consulte [Implantar recursos de modelo personalizado](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Implantar usando o PowerShell

Você pode implantar um modelo do Resource Manager usando o Azure PowerShell. Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e do Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [implantar modelo do Resource Manager privado com o token SAS e o Azure PowerShell](../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Implantar usando a CLI do Azure

Você pode implantar um modelo do Resource Manager usando CLI clássica. Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e da CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) e [implantar modelo do Resource Manager privado com o token SAS e a CLI do Azure](../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Implantar usando a API REST
Você pode implantar um modelo do Resource Manager usando a API REST. Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Implantação com o Visual Studio
 Use o Visual Studio para criar um projeto do grupo de recursos e implantá-lo ao Azure por meio da interface do usuário. Selecione o tipo de recursos a serem incluídos em seu projeto. Esses recursos são adicionados automaticamente ao modelo do Resource Manager. O projeto também fornece um script do PowerShell para implantar o modelo.

Para obter uma introdução ao uso do Visual Studio com grupos de recursos, veja [Criando e implantando grupos de recursos do Azure por meio do Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="troubleshoot"></a>Solução de problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Próximos passos
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* Para HDInsight mais modelos relacionados, consulte [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Para obter um exemplo de como implantar recursos por meio da biblioteca de cliente do .NET, veja [Implantar recursos usando bibliotecas do .NET e um modelo](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para obter um exemplo detalhado de implantação de um aplicativo, confira [Provisionar e implantar microsserviços de forma previsível no Azure](../app-service/deploy-complex-application-predictably.md).
* Para obter orientação sobre como implantar a solução em ambientes diferentes, confira [Ambientes de desenvolvimento e de teste no Microsoft Azure](../solution-dev-test-environments.md).
* Para saber mais sobre as seções do modelo do Azure Resource Manager, veja [Criando modelos](../azure-resource-manager/resource-group-authoring-templates.md).
* Para obter uma lista das funções que podem ser usadas em um modelo do Azure Resource Manager, veja [Funções do modelo](../azure-resource-manager/resource-group-template-functions.md).
