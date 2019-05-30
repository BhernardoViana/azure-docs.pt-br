---
title: Início rápido do Node.js para fluxos de dispositivos do Hub IoT do Azure para SSH/RDP (versão prévia) | Microsoft Docs
description: Neste início rápido, você executará um aplicativo Node.js de exemplo que atua como proxy para habilitar cenários de SSH/RDP em fluxos de dispositivos do Hub IoT.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: c4f994638420819da41a355d679cb03785b94a3a
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832276"
---
# <a name="quickstart-sshrdp-over-an-iot-hub-device-stream-using-a-nodejs-proxy-application-preview"></a>Início Rápido: SSH/RDP em fluxos de dispositivos do Hub IoT usando o aplicativo proxy Node.js (versão prévia)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Atualmente, o Hub IoT do Microsoft Azure dá suporte a fluxos de dispositivos como uma [versão prévia do recurso](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os [fluxos de dispositivos do Hub IoT](./iot-hub-device-streams-overview.md) permitem que aplicativos de serviço e dispositivo se comuniquem de maneira segura e simples para o firewall. Este guia de início rápido descreve a execução de um aplicativo proxy Node.js no lado do serviço para habilitar o tráfego SSH e RDP para envio ao dispositivo por um fluxo de dispositivos. Confira [Exemplo de proxy local para o SSH ou RDP](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) para ter uma visão geral da configuração. Durante a versão prévia pública, o SDK do Node.js dá suporte somente a fluxos de dispositivos no lado do serviço. Consequentemente, este guia de início rápido contém apenas instruções para executar o proxy local do serviço. Você deve executar um dos seguintes inícios rápidos de proxy locais do dispositivo:  

   * [SSH/RDP em fluxos de dispositivos do Hub IoT usando o aplicativo proxy em C](./quickstart-device-streams-proxy-c.md)

   * [SSH/RDP em fluxos de dispositivos do Hub IoT usando o aplicativo proxy em C#](./quickstart-device-streams-proxy-csharp.md)

Primeiro, descreveremos a configuração para SSH (com a porta 22). Em seguida, descreveremos como modificar a configuração para RDP (que usa a porta 3389). Como os fluxos de dispositivos são independentes de protocolo e de aplicativo, a mesma amostra pode ser modificada para acomodar outros tipos de tráfego do aplicativo para clientes/servidores (normalmente, alterando a porta de comunicação).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Atualmente, a versão prévia dos fluxos de dispositivos só é compatível com os Hubs IoT criados nas seguintes regiões:

   * **Centro dos EUA**

   * **EUA Central EUAP**

Para executar o aplicativo local do serviço neste início rápido, você precisa do Node.js v10.x.x ou posterior no computador de desenvolvimento.

* Baixe o Node.js para várias plataformas em [nodejs.org](https://nodejs.org).

Você pode verificar a versão atual do Node.js no computador de desenvolvimento usando o seguinte comando:

```
node --version
```

* Execute o comando a seguir para adicionar a Extensão do Microsoft Azure IoT para a CLI do Azure à instância do Cloud Shell. A Extensão de IoT adiciona comandos específicos do Hub IoT, do IoT Edge e do DPS (Serviço de Provisionamento de Dispositivos) no IoT à CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

* Caso ainda não tenha feito isso, faça o download do projeto de exemplo do Node.js do https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Se tiver concluído o [Início Rápido: enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar esta etapa.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Se tiver concluído o [Início Rápido: enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md), pode ignorar esta etapa.

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyDevice**: Esse é o nome fornecido para o dispositivo registrado. Use MyDevice, conforme mostrado. Se você escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Você também precisa de uma *cadeia de conexão de serviço* para permitir que aplicativos de back-end se conectem ao seu hub IoT e recuperem mensagens. O comando abaixo recupera a cadeia de conexão de serviço para o hub IoT:

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Anote o valor retornado, que tem esta aparência:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH para um dispositivo por fluxos de dispositivos

Nesta seção, você estabelecerá um fluxo de ponta a ponta para encapsular o tráfego SSH.

### <a name="run-the-device-local-proxy"></a>Executar o proxy no local do dispositivo

Como mencionado anteriormente, o SDK do Node.js do Hub IoT dá suporte somente a fluxos de dispositivos no lado do serviço. Para o aplicativo local do dispositivo, use um dos programas de proxy de dispositivo disponíveis em um dos seguintes inícios rápidos:

   * [SSH/RDP em fluxos de dispositivos do Hub IoT usando aplicativos do proxy C](./quickstart-device-streams-proxy-c.md)

   * [SSH/RDP em fluxos de dispositivos do Hub IoT usando aplicativos do proxy em C#](./quickstart-device-streams-proxy-csharp.md) 

Verifique se o proxy local do dispositivo está em execução antes de prosseguir para a próxima etapa.

### <a name="run-the-service-local-proxy"></a>Executar o proxy local do serviço

Supondo que o [proxy de local do dispositivo](#run-the-device-local-proxy) está em execução, siga as etapas abaixo para executar o proxy local do serviço escrito em Node.js.

* forneça suas credenciais de serviço, a identificação do dispositivo de destino no qual o daemon SSH é executado e o número da porta para o proxy em execução no dispositivo como variáveis de ambiente.

```
# In Linux
export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
export STREAMING_TARGET_DEVICE="MyDevice"
export PROXY_PORT=2222

# In Windows
SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
SET STREAMING_TARGET_DEVICE=MyDevice
SET PROXY_PORT=2222
```

  Altere os valores acima para que coincidam com a cadeia de conexão e de ID do dispositivo.

* Navegue até `Quickstarts/device-streams-service` na pasta de projeto descompactada e execute o proxy do local de serviço.

```
cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

# Install the preview service SDK, and other dependencies
npm install azure-iothub@streams-preview
npm install

# Run the service-local proxy application
node proxy.js
```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH para o dispositivo por fluxos de dispositivos

No Linux, execute o SSH usando `ssh $USER@localhost -p 2222` em um terminal. No Windows, use seu cliente SSH favorito (por exemplo, PuTTY).

Saída do console no local do serviço após o estabelecimento da sessão SSH (o proxy do local de serviço escuta na porta 2222):

![Saída do terminal SSH](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Saída do console do programa do cliente SSH (o cliente SSH se comunica com o daemon SSH conectando-se à porta 22 na qual o proxy do local de serviço está escutando):

![Saída do cliente SSH](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP para o dispositivo por fluxos de dispositivos

Agora, utilize o programa do cliente RDP e se conecte ao proxy de serviço na porta 2222 (uma porta disponível arbitrária que você escolheu anteriormente).

> [!NOTE]
> Certifique-se de que o proxy do dispositivo foi configurado corretamente para RDP e configurado com a porta RDP 3389.

![O cliente RDP se conecta ao proxy do local de serviço](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você configurou um hub IoT, registrou um dispositivo e implantou um programa de proxy de serviço para habilitar RDP e SSH para um dispositivo IoT. O tráfego RDP e SSH será encapsulado por meio de um fluxo de dispositivo através do Hub IoT. Isso elimina a necessidade de conectividade direta com o dispositivo.

Use os links abaixo para saber mais sobre fluxos de dispositivos:

> [!div class="nextstepaction"]
> [Visão geral dos fluxos de dispositivos](./iot-hub-device-streams-overview.md)
