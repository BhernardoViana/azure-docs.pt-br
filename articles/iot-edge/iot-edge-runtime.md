---
title: Saiba como o tempo de execução gerencia dispositivos – Azure IoT Edge | Microsoft Docs
description: Saiba como os módulos, segurança, comunicação e emissão de relatórios em seus dispositivos são gerenciados pelo tempo de execução do Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 2b5f44471187f4031642fd674381e672453b7197
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884254"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Reconhecer o tempo de execução do Azure IoT Edge e sua arquitetura

O tempo de execução do IoT Edge é uma coleção de programas que transforma um dispositivo em um dispositivo IoT Edge. Coletivamente, os IoT Edge os componentes de tempo de execução permitem que os dispositivos IoT Edge recebam código para serem executados na borda e se comuniquem com os resultados. 

O tempo de execução do IoT Edge executa as seguintes funções em dispositivos IoT Edge:

* Instala e atualiza as cargas de trabalho no dispositivo.
* Mantém os padrões de segurança do Azure IoT Edge no dispositivo.
* Verifique se os [módulos IOT Edge](iot-edge-modules.md) estão sempre em execução.
* Relata a integridade do módulo à nuvem para monitoramento remoto.
* Facilitar a comunicação entre dispositivos de folha downstream e dispositivos IoT Edge.
* Facilitar a comunicação entre módulos no dispositivo IoT Edge.
* Facilite a comunicação entre o IoT Edge dispositivo e a nuvem.

![O tempo de execução comunica insights e integridade de módulo para o Hub IoT](./media/iot-edge-runtime/Pipeline.png)

As responsabilidades do tempo de execução do IoT Edge se enquadram em duas categorias: gerenciamento de comunicação e de módulo. Essas duas funções são executadas por dois componentes que fazem parte do tempo de execução de IoT Edge. O *Hub de IOT Edge* é responsável pela comunicação, enquanto o *agente de IOT Edge* implanta e monitora os módulos. 

Tanto o hub do IoT Edge quanto o agente do IoT Edge são módulos, assim como qualquer outro módulo em execução em um dispositivo IoT Edge. 

## <a name="iot-edge-hub"></a>Hub do IoT Edge

O hub do IoT Edge é um dos dois módulos que compõem o tempo de execução do Azure IoT Edge. Ele atua como um proxy local para o Hub IoT expondo os mesmos pontos de extremidade de protocolo que o Hub IoT. Essa consistência significa que os clientes (sejam dispositivos ou módulos) podem se conectar no tempo de execução do IoT Edge como faria para no Hub IoT. 

>[!NOTE]
> IoT Edge Hub dá suporte a clientes que se conectam usando MQTT ou AMQP. Ele não oferece suporte a clientes que usam HTTP. 

O hub do IoT Edge não é uma versão completa do Hub IoT executado localmente. Há algumas coisas que o hub do IoT Edge delega para o Hub IoT silenciosamente. Por exemplo, o hub do IoT Edge encaminha solicitações de autenticação para o Hub IoT quando um dispositivo tenta se conectar. Depois que a primeira conexão é estabelecida, as informações de segurança são armazenadas em cache localmente pelo hub do IoT Edge. São permitidas conexões subsequentes desse dispositivo sem ele precisar se autenticar na nuvem. 

Para reduzir a largura de banda que a solução IoT Edge usa, o hub do IoT Edge otimiza quantas conexões reais são feitas com a nuvem. O hub do IoT Edge usa conexões lógicas de clientes como módulos ou dispositivos de folha e combina-os em uma única conexão física com a nuvem. Os detalhes desse processo são transparentes para o restante da solução. Os clientes pensam que têm sua própria conexão para a nuvem, mesmo que estejam todos sendo enviados pela mesma conexão. 

![O hub do IoT Edge é um gateway entre os dispositivos físicos e o Hub IoT](./media/iot-edge-runtime/Gateway.png)

O hub do IoT Edge pode determinar se ele está conectado ao Hub IoT. Se a conexão for perdida, o hub do IoT Edge salvará mensagens ou as atualizações duplicadas localmente. Depois que uma conexão é restabelecida, ela sincroniza todos os dados. A localização usada para esse cache temporário é determinado por uma propriedade do gêmeo do módulo do hub do IoT Edge. O tamanho do cache não tem limite e aumentará até atingir toda a capacidade de armazenamento do dispositivo. Para obter mais informações, consulte [recursos offline](offline-capabilities.md).

### <a name="module-communication"></a>Comunicação do módulo

O hub do IoT Edge facilita a comunicação de módulo para módulo. O uso do hub do IoT Edge como um agente de mensagem mantém os módulos independentes uns dos outros. Os módulos precisam apenas especificar as entradas nas quais eles aceitam mensagens e as saídas nas quais eles gravam mensagens. Um desenvolvedor de soluções pode reunir essas entradas e saídas para que os módulos processem dados na ordem específica para essa solução. 

![O hub do IoT Edge facilita a comunicação de módulo para módulo](./media/iot-edge-runtime/module-endpoints.png)

Para enviar dados ao hub do IoT Edge, um módulo chamará o método SendEventAsync. O primeiro argumento especifica em qual saída enviar a mensagem. O pseudocódigo a seguir envia uma mensagem em **Saída1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Para receber uma mensagem, registre um retorno de chamada que processa as mensagens recebidas em uma entrada específica. O pseudocódigo a seguir registra a função messageProcessor a ser usada para processar todas as mensagens recebidas em **entrada1**:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência de API para seu idioma preferencial do SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C and Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), or [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

O desenvolvedor da solução é responsável por especificar as regras que determinam como o hub do IoT Edge passa mensagens entre os módulos. As regras de roteamento são definidas na nuvem e propagadas para o hub do IoT Edge no seu dispositivo gêmeo. A mesma sintaxe para rotas do Hub IoT é usada para definir rotas entre módulos no Azure IoT Edge. Para obter mais informações, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).   

![As rotas entre módulos passam pelo hub do IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agente do IoT Edge

O agente do IoT Edge é o outro módulo que compõe o tempo de execução do Azure IoT Edge. Ele é responsável por instanciar módulos, fazendo com que continuem a funcionar, e indicar o status dos módulos para o Hub IoT. Assim como qualquer outro módulo, o agente do IoT Edge usa seu módulo gêmeo para armazenar esses dados de configuração. 

O [daemon de segurança do IoT Edge](iot-edge-security-manager.md) inicia o agente do IoT Edge na inicialização do dispositivo. O agente recupera seu módulo gêmeo do Hub IoT e inspeciona o manifesto de implantação. O manifesto de implantação é um arquivo JSON que declara os módulos que precisam ser iniciados. 

Cada item no manifesto de implantação contém informações específicas sobre um módulo e é usado pelo agente o IoT Edge para controlar o ciclo de vida do módulo. Estas são algumas das propriedades mais interessantes: 

* **Settings.Image**: a imagem de contêiner que o agente do IoT Edge usa para iniciar o módulo. O agente do IoT Edge deverá ser configurado com as credenciais para o registro de contêiner se a imagem estiver protegida por senha. As credenciais para o registro de contêiner pode ser configurado remotamente usando o manifesto de implantação ou no próprio dispositivo IoT Edge atualizando o `config.yaml` arquivo na pasta de programa do IoT Edge.
* **Settings.** – uma cadeia de caracteres que é passada diretamente para o daemon de contêiner Moby ao iniciar o contêiner de um módulo. A adição de opções nessa propriedade permite configurações avançadas como encaminhamento de porta ou montagem de volumes no contêiner de um módulo.  
* **status** – o estado no qual o agente do IoT Edge coloca o módulo. Normalmente, esse valor é definido como *em execução* , pois a maioria das pessoas deseja que o agente de IOT Edge inicie imediatamente todos os módulos no dispositivo. No entanto, você pode especificar o estado inicial de um módulo para ser interrompido e aguardar para mandar o agente do IoT Edge iniciar um módulo. O agente do IoT Edge relata o status de cada módulo para a nuvem nas propriedades relatadas. Uma diferença entre a propriedade desejada e a propriedade relatada é um indicador de um dispositivo com comportamento inadequado. Os status com suporte são:
   * Baixando
   * Em execução
   * Não Íntegro
   * Falhou
   * Parado
* **restartPolicy** – como o agente do IoT Edge reinicia um módulo. Os valores possíveis incluem:
   * `never`– O agente de IoT Edge nunca reinicia o módulo.
   * `on-failure`-Se o módulo falhar, o agente de IoT Edge o reiniciará. Se o módulo é desligado corretamente, o agente do IoT Edge não o reinicia.
   * `on-unhealthy`-Se o módulo falhar ou for considerado não íntegro, o agente de IoT Edge o reiniciará.
   * `always`-Se o módulo falhar, for considerado não íntegro ou for desligado de alguma forma, o agente de IoT Edge o reiniciará. 

O agente do IoT Edge envia a resposta de tempo de execução para o Hub IoT. Aqui está uma lista das possíveis respostas:
  * 200 - OK
  * 400 - A configuração de implantação está malformada ou inválida.
  * 417-o dispositivo não tem um conjunto de configuração de implantação.
  * 412 - A versão do esquema na configuração de implantação é inválida.
  * 406 – o dispositivo do IoT Edge está offline ou não está enviando relatórios de status.
  * 500 – ocorreu um erro no tempo de execução do IoT Edge.

Para obter mais informações, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).   

### <a name="security"></a>Segurança

O agente do IoT Edge desempenha um papel fundamental na segurança de um dispositivo IoT Edge. Por exemplo, ele executa ações como verificar a imagem de um módulo antes de iniciá-lo. 

Para obter mais informações sobre a estrutura de segurança do Azure IoT Edge, leia sobre o [Gerenciador de segurança do IOT Edge](iot-edge-security-manager.md).

## <a name="next-steps"></a>Próximas etapas

[Entenda os módulos do Azure IoT Edge](iot-edge-modules.md)
