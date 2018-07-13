---
title: Criar uma VM do Linux no Azure usando um modelo | Microsoft Docs
description: Como usar a CLI do Azure para criar uma VM do Linux de um modelo do Resource Manager
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e6b431ee55ee73b4f5a69471cca3cc16270198c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930232"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Como criar uma máquina virtual do Linux com os modelos do Azure Resource Manager
Este artigo mostra como implantar rapidamente uma VM (máquina virtual) do Linux com a CLI do Azure e modelos do Azure Resource Manager. 


## <a name="templates-overview"></a>Visão geral de modelos
Os modelos do Azure Resource Manager são arquivos JSON que definem a infraestrutura e a configuração de sua solução do Azure. Usando um modelo, você pode implantar a solução repetidamente em todo seu ciclo de vida e com a confiança de que seus recursos serão implantados em um estado consistente. Para saber mais sobre o formato do modelo e como construí-o, veja [Criar seu primeiro modelo do Azure Resource Manager](../../azure-resource-manager/resource-manager-create-first-template.md). Para exibir a sintaxe JSON para os tipos de recursos, consulte [Definir recursos nos modelos do Azure Resource Manager](/azure/templates/).


## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar uma máquina virtual. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroupVM* na região *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
O exemplo a seguir cria uma VM [neste modelo do Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) com [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Somente a autenticação SSH tem permissão. Mediante solicitação, forneça o valor de sua própria chave pública SSH, como o conteúdo de *~/.ssh/id_rsa.pub*. Se você precisar criar um par de chaves SSH, confira [Como criar um par de chaves SSH para VMs Linux no Azure](mac-create-ssh-keys.md).

```azurecli
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

No exemplo anterior, você especificou um modelo armazenado no GitHub. Também é possível baixar ou criar um modelo e especificar o caminho local com o parâmetro `--template-file`.


## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual
Para enviar por SSH à sua VM, obtenha o endereço IP público com [az vm show](/cli/azure/vm#az-vm-show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name sshvm \
    --show-details \
    --query publicIps \
    --output tsv
```

Depois, você pode enviar por SSH para sua VM, como de costume. Forneça seu próprio endereço IP público do comando anterior:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma VM básica do Linux. Para obter mais modelos do Resource Manager que incluem estruturas de aplicativo ou criam ambientes mais complexos, procure a [Galeria de modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/).