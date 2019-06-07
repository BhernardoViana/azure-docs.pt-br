---
title: Configurar dispositivos para proxies de rede - Azure IoT Edge | Microsoft Docs
description: Como configurar o tempo de execução do Azure IoT Edge e quaisquer módulos de IoT Edge voltados para a Internet para comunicação por meio de um servidor proxy.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1c0da1a768b894f543b9089643622c31d6a8758d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730158"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy

Os dispositivos IoT Edge enviam solicitações HTTPS para se comunicarem com o Hub IoT. Se o seu dispositivo estiver conectado a uma rede que usa um servidor proxy, você precisará configurar o tempo de execução do IoT Edge para se comunicar através do servidor. Os servidores proxy também podem afetar os módulos individuais do IoT Edge se eles fizerem solicitações HTTP ou HTTPS que não são roteadas pelo hub do IoT Edge. 

Este artigo explica as quatro etapas a seguir para configurar e gerenciar um dispositivo IoT Edge em um servidor proxy: 

1. **Instale o tempo de execução do IoT Edge em seu dispositivo.**

   Os scripts de instalação do IoT Edge pull pacotes e arquivos da internet, portanto, seu dispositivo precisa para se comunicar por meio do servidor proxy para fazer essas solicitações. Para obter etapas detalhadas, consulte o [instalar o tempo de execução por meio de um proxy](#install-the-runtime-through-a-proxy) seção deste artigo. Para dispositivos Windows, o script de instalação também fornece um [instalação Offline](how-to-install-iot-edge-windows.md#offline-installation) opção. 

   Esta etapa é um processo único executado no dispositivo do IoT Edge quando você primeiro configurá-lo. As mesmas conexões também são necessárias quando você atualiza o tempo de execução do IoT Edge. 

2. **Configure o daemon do Docker e o daemon do IoT Edge em seu dispositivo.**

   IoT Edge usa dois daemons no dispositivo, que precisam fazer solicitações da web por meio do servidor proxy. O daemon do IoT Edge é responsável pela comunicação com o IoT Hub. O daemon Moby é responsável pelo gerenciamento de contêiner, portanto, se comunica com registros de contêiner. Para obter etapas detalhadas, consulte o [configurar os daemons](#configure-the-daemons) seção deste artigo. 

   Esta etapa é um processo único executado no dispositivo do IoT Edge quando você primeiro configurá-lo.

3. **Configure as propriedades do agente do IoT Edge no arquivo config. YAML em seu dispositivo.**

   O daemon do IoT Edge inicia o módulo edgeAgent inicialmente, mas, em seguida, o módulo de edgeAgent é responsável por recuperar o manifesto de implantação do IoT Hub e iniciar todos os outros módulos. Para o agente do IoT Edge tornar a conexão inicial ao IoT Hub, configure as variáveis de ambiente do módulo edgeAgent manualmente no próprio dispositivo. Após a conexão inicial, você pode configurar o módulo edgeAgent remotamente. Para obter etapas detalhadas, consulte o [configurar o agente do IoT Edge](#configure-the-iot-edge-agent) seção deste artigo.

   Esta etapa é um processo único executado no dispositivo do IoT Edge quando você primeiro configurá-lo.

4. **Para todas as implantações futuras do módulo, defina variáveis de ambiente de qualquer módulo se comunicando por meio do proxy.**

   Depois que o dispositivo IoT Edge é configurado e conectado ao IoT Hub por meio do servidor proxy, você precisa manter a conexão em todas as implantações futuras do módulo. Para obter etapas detalhadas, consulte o [configurar manifestos de implantação](#configure-deployment-manifests) seção deste artigo. 

   Esta etapa é um processo contínuo executado remotamente, de modo que cada nova atualização de módulo ou implantação mantém a capacidade do dispositivo para se comunicar por meio do servidor proxy. 

## <a name="know-your-proxy-url"></a>Saber a sua URL do proxy

Antes de começar qualquer uma das etapas neste artigo, você precisa saber a URL do proxy.

Os URLs proxy usam o seguinte formato: **protocolo**: // **proxy_host**: **proxy_port**.

* O **protocolo** é HTTP ou HTTPS. O daemon do Docker pode ser usado com qualquer protocolo, dependendo das configurações do registro do contêiner, mas o daemon do IoT Edge e os contêineres de tempo de execução devem sempre usar HTTPS.

* O **proxy_host** é um endereço para o servidor proxy. Se o servidor proxy requer autenticação, você pode fornecer suas credenciais como parte do host do proxy com o seguinte formato: **usuário**:**senha**\@**proxy_host** .

* O **proxy_port** é a porta de rede em que o proxy responde ao tráfego de rede.

## <a name="install-the-runtime-through-a-proxy"></a>Instalar o tempo de execução por meio de um proxy

Se seu dispositivo IoT Edge é executado no Windows ou Linux, você precisará acessar os pacotes de instalação por meio do servidor proxy. Dependendo do sistema operacional, siga as etapas para instalar o tempo de execução do IoT Edge por meio de um servidor proxy. 

### <a name="linux"></a>Linux

Se você estiver instalando o tempo de execução do IoT Edge em um dispositivo Linux, configure o gerenciador de pacotes para percorrer seu servidor proxy para acessar o pacote de instalação. Por exemplo, [Configurar o apt-get para usar um proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Depois que o gerenciador de pacotes estiver configurado, siga as instruções em [Instalar tempo de execução do Azure IoT Edge no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) ou [Instalar o tempo de execução do Azure IoT Edge no Linux (x64)](how-to-install-iot-edge-linux.md) como de costume.

### <a name="windows"></a>Windows

Se você estiver instalando o tempo de execução do IoT Edge em um dispositivo Windows, você precisará passar pelo servidor proxy duas vezes. A primeira conexão baixa o arquivo de script do instalador, e a segunda conexão é durante a instalação para baixar os componentes necessários. Você pode configurar informações de proxy nas configurações do Windows, ou incluir suas informações de proxy diretamente nos comandos do PowerShell. 

As etapas a seguir demonstram um exemplo de uma instalação do windows usando o `-proxy` argumento:

1. O comando de Invoke-WebRequest precisa de informações de proxy para acessar o script do instalador. Em seguida, o comando implantar IoTEdge precisa das informações de proxy para baixar os arquivos de instalação. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. O comando inicializar IoTEdge não precisa passar pelo servidor proxy, portanto, a segunda etapa requer somente informações de proxy para Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Para obter mais informações sobre parâmetros de proxy, consulte [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Para obter mais informações sobre opções de instalação do Windows, incluindo a instalação offline, consulte [tempo de execução de instalar o Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Configurar os daemons

IoT Edge se baseia em dois daemons em execução no dispositivo do IoT Edge. O daemon Moby faz solicitações da web para imagens de contêiner de pull de registros de contêiner. O daemon do IoT Edge faz solicitações da Web para se comunicar com o Hub IoT.

O Moby e os daemons do IoT Edge precisam ser configurados para usar o servidor proxy para a funcionalidade do dispositivo em andamento. Esta etapa é feita no dispositivo IoT Edge durante a instalação inicial do dispositivo. 

### <a name="moby-daemon"></a>Daemon Moby

Como Moby é criado no Docker, consulte a documentação do Docker para configurar o daemon Moby com variáveis de ambiente. A maioria dos registros de contêiner (incluindo DockerHub e Azure Container Registries) oferece suporte a solicitações HTTPS, portanto, o parâmetro que você deve definir é **HTTPS_PROXY**. Se você está obtendo imagens de um registro que não dão suporte ao protocolo TLS (TLS), você deve definir o parâmetro **HTTP_PROXY**. 

Escolha o artigo que se aplica ao seu sistema operacional do dispositivo IoT Edge: 

* [Configurar o daemon do Docker no Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * O daemon Moby em dispositivos Linux mantém o nome do Docker.
* [Configurar o daemon do Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * O daemon Moby em dispositivos do Windows é chamado iotedge moby. Os nomes são diferentes porque é possível executar a área de trabalho do Docker e Moby em paralelo em um dispositivo do Windows. 

### <a name="iot-edge-daemon"></a>Daemon do IoT Edge

O daemon do IoT Edge é configurado de maneira semelhante para o daemon Moby. Use as etapas a seguir para definir uma variável de ambiente para o serviço, com base em seu sistema operacional. 

O daemon do IoT Edge sempre usa HTTPS para enviar solicitações para o IoT Hub.

#### <a name="linux"></a>Linux

Abra um editor no terminal para configurar o daemon do IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Insira o texto a seguir, substituindo  **\<URL do proxy >** com seu endereço do servidor proxy e a porta. Em seguida, salve e saia. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Atualize o gerenciador de serviços para pegar a nova configuração do IoT Edge.

```bash
sudo systemctl daemon-reload
```

Reinicie o IoT Edge para que as alterações entrem em vigor.

```bash
sudo systemctl restart iotedge
```

Verifique se sua variável de ambiente foi criada e se a nova configuração foi carregada. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Abra uma janela do PowerShell como administrador e execute o seguinte comando para editar o registro com a nova variável de ambiente. Substitua **\<URL do proxy>** pelo endereço e porta do servidor proxy. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Reinicie o IoT Edge para que as alterações entrem em vigor.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Configurar o dispositivo IoT Edge

O agente IoT Edge é o primeiro módulo a ser iniciado em qualquer dispositivo IoT Edge. É iniciado pela primeira vez com base nas informações do arquivo config.yaml do IoT Edge. Em seguida, o agente do IoT Edge se conecta ao Hub IoT para recuperar os manifestos de implantação, que declaram quais outros módulos devem ser implantados no dispositivo.

Esta etapa é feita uma vez no dispositivo IoT Edge durante a instalação inicial do dispositivo. 

1. Abra o arquivo config.yaml no seu dispositivo IoT Edge. Nos sistemas Linux, esse arquivo está localizado em **/etc/iotedge/config.yaml**. Nos sistemas Windows, esse arquivo está localizado em **C: \ ProgramData \ iotedge \ config.yaml**. O arquivo de configuração é protegido, você precisa de privilégios administrativos para acessá-lo. Em sistemas Linux, use o `sudo` comando antes de abrir o arquivo em seu editor de texto preferido. No Windows, abra um editor de texto como bloco de notas como administrador e, em seguida, abra o arquivo. 

2. No arquivo config.yaml, localize a seção **Especificação do módulo Edge Agent**. A definição do agente IoT Edge inclui um parâmetro **env**, no qual é possível incluir variáveis de ambiente. 

3. Remova as chaves que são espaços reservados para o parâmetro env e inclua a nova variável em uma nova linha. Lembre-se de que os recuos em YAML são dois espaços. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. O tempo de execução do IoT Edge usa o AMQP por padrão para falar com o IoT Hub. Alguns servidores proxy bloqueiam as portas AMQP. Se for esse o caso, você também precisará configurar o edgeAgent para usar o AMQP em WebSocket. Adicione uma segunda variável de ambiente.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![definição de edgeAgent com variáveis de ambiente](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Salve as alterações no config. YAML e feche o editor. Reinicie o IoT Edge para que as alterações entrem em vigor. 

   * Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Configurar manifestos de implantação  

Depois que o dispositivo IoT Edge estiver configurado para funcionar com o servidor proxy, continue a declarar as variáveis de ambiente em todos os manifestos de implantação futuros. Você pode editar os manifestos de implantação usando o Assistente do portal do Azure ou por meio da edição de uma implantação do arquivo de manifesto do JSON. 

Sempre configure os dois módulos de tempo de execução: edgeAgent e edgeHub para se comunicar por meio do servidor proxy para que eles possam manter uma conexão com o Hub IoT. Se você remover as informações de proxy do módulo edgeAgent, é a única maneira de restabelecer a conexão, editando o arquivo config. YAML no dispositivo, conforme descrito na seção anterior. 

Outros módulos do IoT Edge que se conectam à internet devem ser configurados para se comunicar por meio do servidor proxy, muito. No entanto, os módulos que roteiam as suas mensagens por meio do edgeHub ou que se comunicam somente com outros módulos no dispositivo não precisam dos detalhes do servidor proxy. 

Esta etapa está em andamento durante a vida útil do dispositivo IoT Edge. 

### <a name="azure-portal"></a>Portal do Azure

Quando você usa o assistente **Configurar módulos** para criar implantações para dispositivos IoT Edge, cada módulo tem uma seção **Variáveis de Ambiente** que você pode usar para configurar conexões do servidor proxy. 

Para configurar o agente do IoT Edge e os módulos do hub do IoT Edge, selecione **Definir configurações avançadas do Edge Runtime** na primeira etapa do assistente. 

![Definir configurações avançadas de tempo de execução do Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Adicione a variável de ambiente **https_proxy** às definições do módulo de agente IoT Edge e do hub do IoT Edge. Se você incluiu a variável de ambiente **UpstreamProtocol** no arquivo config.yaml em seu dispositivo IoT Edge, inclua isso na definição do módulo do agente do IoT Edge também. 

![Defina a variável de ambiente https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Todos os outros módulos adicionados a um manifesto de implantação seguem o mesmo padrão. Na página em que você define o nome e a imagem do módulo, há uma seção de variáveis de ambiente.

### <a name="json-deployment-manifest-files"></a>Arquivos de manifesto de implantação de JSON

Se você criar implantações para dispositivos IoT Edge usando os modelos no Visual Studio Code ou criando manualmente arquivos JSON, poderá adicionar as variáveis de ambiente diretamente a cada definição de módulo. 

Use o seguinte formato JSON: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Com as variáveis de ambiente incluídas, sua definição de módulo deve ser semelhante ao seguinte exemplo edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Se você incluiu a variável de ambiente **UpstreamProtocol** no arquivo confige.yaml em seu dispositivo IoT Edge, inclua isso na definição do módulo do agente do IoT Edge também. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as funções do tempo de execução do [IoT Edge](iot-edge-runtime.md).

Solucione problemas de erros de instalação e configuração com [problemas comuns e resoluções para o Azure IoT Edge](troubleshoot.md)

