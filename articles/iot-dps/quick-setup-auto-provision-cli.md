---
title: Configurar o Provisionamento de Dispositivos usando a CLI do Azure | Microsoft Docs
description: Início Rápido do Azure – Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure usando a CLI do Azure
author: bryanla
ms.author: bryanla
ms.date: 02/26/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 2cf611e12402b22587faa83fefc4651e7307c41c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38482128"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este Início Rápido detalha o uso da CLI do Azure para criar um Hub IoT e um Serviço de Provisionamento de Dispositivos no Hub IoT e vincular os dois serviços. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> O Hub IoT e o serviço de provisionamento criado neste início rápido serão detectáveis publicamente como pontos de extremidade DNS. Evite usar informações confidenciais se você decidir alterar os nomes usados nesses recursos.
>


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *my-sample-resource-group* na localização *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> O exemplo cria o grupo de recursos localizado no Oeste dos EUA. Você pode exibir uma lista dos locais disponíveis executando o comando `az account list-locations -o table`.
>
>

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Crie um hub IoT com o comando [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create). 

O exemplo a seguir cria um hub IoT denominado *my-sample-hub* no local *westus*.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Criar um serviço de provisionamento

Crie um serviço de provisionamento com o comando [az iot dps create](/cli/azure/iot/dps#az_iot_dps_create). 

O exemplo a seguir cria um serviço de provisionamento denominado *my-sample-dps* no local *westus*.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> O exemplo cria o serviço de provisionamento no local Oeste dos EUA. Você pode exibir uma lista de locais disponíveis executando o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou acessando a página [Status do Azure](https://azure.microsoft.com/status/) e pesquisando por "Serviço de Provisionamento de Dispositivos". Nos comandos, os locais podem ser especificados em formato de uma ou várias palavras, por exemplo, westus, West US, WEST US, etc. O valor não diferencia maiúsculas de minúsculas. Se você usar o formato de várias palavras para especificar o local, coloque o valor entre aspas, por exemplo, `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Obter a cadeia de conexão para o hub IoT

Você precisa da cadeia de conexão do seu hub IoT para vinculá-lo ao serviço de provisionamento de dispositivos. Use o comando [az iot hub show-connection-string](/cli/azure/iot/hub#az_iot_hub_show_connection_string) para obter a cadeia de conexão e use a saída para definir uma variável que será usada quando for vincular os dois recursos. 

O exemplo a seguir define a variável *hubConnectionString* como o valor da cadeia de conexão para a chave primária da política *iothubowner* do hub. Você pode especificar uma política diferente com o parâmetro `--policy-name`. O comando usa as opções [consulta](/cli/azure/query-azure-cli) e [saída](/cli/azure/format-output-azure-cli#tsv-output-format) da CLI do Azure para extrair a cadeia de conexão da saída do comando.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Você pode usar o comando `echo` para ver a cadeia de conexão.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Esses dois comandos são válidos para um host executado em Bash. Se você estiver usando um shell CMD/Windows local ou um host do PowerShell, precisará modificar os comandos para usar a sintaxe correta do ambiente.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Vincular o Hub IoT ao serviço de provisionamento

Vincule o hub IoT e o serviço de provisionamento ao comando [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az_iot_dps_linked_hub_create). 

O exemplo a seguir vincula um hub IoT denominado *my-sample-hub* no local *westus* e um serviço de provisionamento de dispositivos denominado *my-sample-dps*. Ele usa a cadeia de conexão para *my-sample-hub* armazenada na variável *hubConnectionString* na etapa anterior.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Verificar o serviço de provisionamento

Obtenha os detalhes do seu serviço de provisionamento com o comando [az iot dps show](/cli/azure/iot/dps#az_iot_dps_show).

O exemplo a seguir obtém os detalhes de um serviço de provisionamento denominado *my-sample-dps*. O hub IoT vinculado é mostrado na coleção *properties.iotHubs*.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Limpar recursos

Outros Guias de Início Rápido na coleção aproveitam este Guia de Início Rápido. Se você planeja continuar trabalhando com Guias de Início Rápido ou tutoriais subsequentes, não limpe os recursos criados neste Guia de Início Rápido. Se você não planeja continuar, pode usar os comandos a seguir para excluir o serviço de provisionamento, o hub IoT ou o grupo de recursos e todos os seus recursos.

Para excluir o serviço de provisionamento, execute o comando [az iot dps delete](/cli/azure/iot/dps#az_iot_dps_delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Para excluir o hub IoT, execute o comando [az iot hub delete](/cli/azure/iot/hub#az_iot_hub_delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Para excluir um grupo de recursos e todos os seus recursos, execute o comando [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Próximas etapas

Neste Guia de Início Rápido, você implantou um Hub IoT e uma instância do Serviço de Provisionamento de Dispositivos, e vinculou os dois recursos. Para aprender a usar essa configuração a fim de provisionar um dispositivo simulado, prossiga para o Guia de Início Rápido de criação de dispositivo simulado.

> [!div class="nextstepaction"]
> [Guia de Início Rápido para criar dispositivo simulado](./quick-create-simulated-device.md)

