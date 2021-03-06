---
title: Executar o Azure IoT Edge em máquinas virtuais do Ubuntu | Microsoft Docs
description: Instruções de configuração do Azure IoT Edge em Máquinas Virtuais do Ubuntu 16.04 do Microsoft Azure Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: gregman
ms.openlocfilehash: fec39a9e788debcd9c3ac707a0431e268d87ed35
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146189"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Executar Azure IoT Edge em Máquinas Virtuais do Ubuntu

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O runtime pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o runtime do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele.

Para saber mais sobre como funciona o tempo de execução do IoT Edge e quais componentes estão incluídos, veja [Entender o tempo de execução do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para executar o tempo de execução do Azure IoT Edge em uma Máquina Virtual do Ubuntu 16.04 usando o [Azure IoT Edge na oferta do Azure Marketplace do Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) pré-configurado. 

Na primeira inicialização, o Azure IoT Edge na VM do Ubuntu pré-instala a última versão do runtime do Azure IoT Edge. Isso também inclui um script para definir a cadeia de conexão e reiniciar o runtime, que pode ser disparado remotamente por meio do portal da VM do Azure ou da linha de comando do Azure, permitindo configurar e conectar facilmente o dispositivo do IoT Edge sem iniciar uma sessão SSH ou área de trabalho remota. Esse script aguardará para definir a cadeia de conexão até que o cliente do IoT Edge esteja totalmente instalado para que não seja necessário incorporá-lo à automação.

## <a name="deploy-from-the-azure-marketplace"></a>Implantar do Azure Marketplace
1.  Navegue até a oferta do Marketplace [Azure IoT Edge no Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) ou pesquise "Azure IoT Edge no Ubuntu" no [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Selecione **OBTER AGORA** e, em seguida, **Continuar** na próxima caixa de diálogo.
3.  Quando estiver no portal do Azure, selecione **Criar** e siga o assistente para implantar a VM. 
    *   Se for a primeira vez que você irá experimentar uma VM, será mais fácil usar uma senha e habilitar o SSH no menu da porta de entrada pública. 
    *   Se você tiver uma carga de trabalho com muitos recursos, atualize o tamanho da máquina virtual, adicionando mais CPUs e/ou memória.
4.  Depois que a máquina virtual for implantada, configure-a para conectar ao Hub IoT:
    1.  Copie a cadeia de conexão do dispositivo de seu dispositivo IoT Edge criado no Hub IoT (você pode seguir a [cadeia de conexão recuperar o no procedimento portal do Azure](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal) se você não estiver familiarizado com esse processo)
    1.  Selecionar o recurso de máquina virtual recém-criada no portal do Azure e abrir a opção **executar comando**
    1.  Selecionar a opção **RunShellScript**
    1.  Execute o script abaixo através da janela de comando com a cadeia de conexão do dispositivo: `/etc/iotedge/configedge.sh "{device_connection_string}"`
    1.  Selecionar **Executar**
    1.  Aguarde alguns instantes e a tela deverá exibir uma mensagem de êxito, indicando que a cadeia de conexão foi configurada com êxito.


## <a name="deploy-from-the-azure-portal"></a>Implantar do portal do Azure
No portal do Azure, pesquise "Azure IoT Edge" e selecione **Ubuntu Server 16.04 LTS + tempo de execução do Azure IoT Edge** para iniciar o fluxo de trabalho de criação de VM. Em seguida, conclua as etapas 3 e 4 nas instruções "Implantar do Azure Marketplace" acima.

## <a name="deploy-from-azure-cli"></a>Implantar da CLI do Azure

1. Se você estiver usando a CLI do Azure na área de trabalho, comece fazendo logon:

   ```azurecli-interactive
   az login
   ```
    
1. Se você tiver várias assinaturas, selecione a assinatura que gostaria de usar:
   1. Liste suas assinaturas:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Copie o campo SubscriptionId da assinatura que você deseja usar.

   1. Defina sua assinatura de trabalho com a ID que você acabou de copiar:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Crie um novo grupo de recursos (ou especifique um existente nas próximas etapas):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. Aceite os termos de uso da máquina virtual. Se você quiser revisar os termos primeiro, siga as etapas em [implantar do Azure Marketplace](#deploy-from-the-azure-marketplace).

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   ```

1. Crie uma nova máquina virtual:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

1. Defina a cadeia de conexão do dispositivo (você pode seguir a cadeia de conexão [de recuperação com o procedimento CLI do Azure](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli) se não estiver familiarizado com esse processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Se você quiser SSH nessa VM após a instalação, use o publicIpAddress com o comando: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>Próximos passos

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do IoT Edge sendo instalado corretamente, confira a página de [solução de problemas](troubleshoot.md) .

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o daemon de segurança e o tempo de execução do IoT Edge](how-to-update-iot-edge.md).

Se você quiser abrir portas para acessar a VM por meio de SSH ou de outras conexões de entrada, consulte a documentação da máquina virtual do Azure [para abrir portas e pontos de extremidade em uma VM do Linux](../virtual-machines/linux/nsg-quickstart.md)
