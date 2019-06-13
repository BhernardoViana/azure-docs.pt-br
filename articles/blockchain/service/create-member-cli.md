---
title: Criar um serviço Azure Blockchain usando a CLI do Azure
description: Use o serviço Azure Blockchain para criar um membro do blockchain usando a CLI do Azure.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: be5a8151f0de0a33db09194a7159aded6848c78a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416177"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Início Rápido: Criar um membro do blockchain do serviço Azure Blockchain usando a CLI do Azure

O serviço Azure Blockchain é uma plataforma de blockchain que pode ser usada para executar sua lógica de negócios em um contrato inteligente. Este Início Rápido mostra como começar por meio da criação de um membro do blockchain usando a CLI do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

Se você preferir instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0.51 ou posterior. Execute `az --version` para encontrar a versão. Caso precise instalá-la ou atualizá-la, confira [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>Criar um membro do blockchain

Crie um membro do blockchain no serviço Azure Blockchain que executa o protocolo de razão do Quorum em um novo consórcio. Há vários parâmetros e propriedades que você precisará passar. Substitua os parâmetros de exemplo por seus valores.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

| Parâmetro | DESCRIÇÃO |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Azure Blockchain são criados. Use o grupo de recursos criado na seção anterior.
| **name** | Um nome exclusivo que identifica o membro do blockchain do serviço Azure Blockchain. O nome é usado para o endereço do ponto de extremidade público. Por exemplo, `myblockchainmember.blockchain.azure.com`.
| **local** | Região do Azure em que o membro do blockchain é criado. Por exemplo, `eastus`. Escolha o local mais próximo para os usuários ou para outros aplicativos do Azure.
| **password** | A senha do nó de transação padrão do membro. Use a senha para a autenticação básica ao se conectar ao ponto de extremidade público do nó de transação padrão do membro do blockchain.
| **consortium** | Nome do consórcio a ser ingressado ou criado.
| **consortiumAccountPassword** | A senha da conta do consórcio também é conhecida como a senha da conta do membro. A senha da conta do membro é usada para criptografar a chave privada para a conta do Ethereum criada para o membro. Use a conta do membro e a senha da conta do membro para o gerenciamento do consórcio.
| **skuName** | Tipo de camada. Use S0 para Standard e B0 para Básico.

São necessários cerca de 10 minutos para criar o membro do blockchain e os recursos de suporte.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o membro do blockchain criado para o próximo Início Rápido ou tutorial. Quando eles não forem mais necessários, você poderá excluir os recursos excluindo o grupo de recursos `myResourceGroup` criado pelo serviço Azure Blockchain.

Execute o comando a seguir para remover o grupo de recursos e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um membro do blockchain, experimente um dos inícios rápidos de conexão do [Geth](connect-geth.md), do [MetaMask](connect-metamask.md) ou do [Truffle](connect-truffle.md).

> [!div class="nextstepaction"]
> [Usar o Truffle para se conectar a uma rede do serviço Azure Blockchain](connect-truffle.md)
