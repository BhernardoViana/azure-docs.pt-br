---
title: Usar métricas para monitorar o IoT Hub do Azure | Microsoft Docs
description: Como usar as métricas do Hub IoT do Azure para avaliar e monitorar a integridade geral dos seus hubs IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: 948cdb2ab1af3fe93566497186c025f7f8f39b2e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877087"
---
# <a name="understand-iot-hub-metrics"></a>Entender as métricas de Hub IoT

As métricas do Hub IoT fornecem dados melhores sobre o estado dos recursos IoT do Azure na sua assinatura do Azure. As métricas Hub IoT permitem avaliar a integridade geral do serviço do Hub IoT e dos dispositivos conectados a ele. As estatísticas voltadas para o usuário são importantes porque elas ajudam você a ver o que está acontecendo com o Hub IoT e com os problemas de causa raiz sem precisar contatar o suporte do Azure.

As métricas são habilitadas por padrão. Você pode exibir as métricas do Hub IoT no portal do Azure.

> [!NOTE]
> Você pode usar as métricas do Hub IoT para exibir informações sobre dispositivos IoT Plug and Play conectados ao seu hub IoT. Os dispositivos IoT Plug and Play fazem parte da [Visualização pública do iot plug and Play](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Como exibir métricas de IoT Hub

1. Crie um Hub IoT. Você pode encontrar instruções sobre como criar um hub IoT na [enviar telemetria de um dispositivo ao IoT Hub](quickstart-send-telemetry-dotnet.md) guia.

2. Abra a folha do seu Hub IoT. A partir daí, clique em **métricas**.
   
    ![Captura de tela mostrando onde a opção de métricas está na página de recursos do IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. Folha de métricas, você pode exibir as métricas para o hub IoT e criar exibições personalizadas de suas métricas. 
   
    ![Captura de tela mostrando a página de métricas para o IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Você pode optar por enviar seus dados de métricas para um ponto de extremidade de Hubs de Eventos ou uma conta do Armazenamento do Azure clicando em **Configurações de Diagnóstico** e, em seguida, **Adicionar configuração de diagnóstico**

   ![Captura de tela mostrando onde o botão de configurações de diagnóstico é](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métricas do Hub IoT e como usá-las

O Hub IoT fornece várias métricas que dão uma visão geral da integridade do hub e o número total de dispositivos conectados. É possível combinar informações de várias métricas para ter uma ideia mais ampla do estado do Hub IoT. A tabela a seguir descreve as métricas que cada Hub IoT rastreia e como cada métrica se relaciona com o status geral do Hub IoT.

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|DESCRIÇÃO|Dimensões|
|---|---|---|---|---|---|
|d2c<br>.telemetry<br>.ingress.<br>allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|Sem dimensões|
|d2c<br>.telemetry<br>.ingress<br>.success|Mensagens de telemetria enviadas|Count|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|Sem dimensões|
|c2d<br>.commands<br>.egress<br>.complete<br>.success|Comandos concluídos|Contagem|Total|Número de comandos da nuvem para o dispositivo concluídos com sucesso pelo dispositivo|Sem dimensões|
|c2d<br>.commands<br>.egress<br>.abandon<br>.success|Comandos abandonados|Contagem|Total|Número de comandos da nuvem para o dispositivo abandonados pelo dispositivo|Sem dimensões|
|c2d<br>.commands<br>.egress<br>.reject<br>.success|Comandos rejeitados|Contagem|Total|Número de comandos da nuvem para o dispositivo rejeitados pelo dispositivo|Sem dimensões|
|dispositivos<br>.totalDevices|Total de dispositivos (preterido)|Count|Total|Número de dispositivos registrados para o seu hub IoT|Sem dimensões|
|dispositivos<br>.connectedDevices<br>.allProtocol|Dispositivos conectados (preteridos) |Count|Total|Número de dispositivos registrados ao seu hub IoT|Sem dimensões|
|d2c<br>.telemetry<br>.egress<br>.success|Roteamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem é encaminhada para vários pontos de extremidade, esse valor aumenta em um para cada entrega bem-sucedida. Se uma mensagem é entregue ao mesmo ponto de extremidade várias vezes, esse valor aumenta em um para cada entrega bem-sucedida.|Sem dimensões|
|d2c<br>.telemetry<br>.egress<br>.dropped|Roteamento: mensagens de telemetria removidas |Count|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido à inatividade dos pontos de extremidade. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens removidas não são entregues.|Sem dimensões|
|d2c<br>.telemetry<br>.egress<br>.orphaned|Roteamento: mensagens de telemetria órfãs |Count|Total|O número de vezes que as mensagens ficaram órfãos do roteamento do Hub IoT porque não correspondiam a nenhuma regra de roteamento (incluindo a regra de fallback). |Sem dimensões|
|d2c<br>.telemetry<br>.egress<br>.invalid|Roteamento: mensagens de telemetria incompatíveis|Count|Total|O número de vezes que o roteamento do Hub IoT não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui novas tentativas.|Sem dimensões|
|d2c<br>.telemetry<br>.egress<br>.fallback|Roteamento: mensagens entregues ao fallback|Count|Total|O número de vezes que o roteamento do Hub IoT entregou mensagens ao ponto de extremidade associado à rota de fallback.|Sem dimensões|
|d2c<br>.endpoints<br>.egress<br>.eventHubs|Roteamento: mensagens entregues ao Hub de Eventos|Count|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do Hub de Eventos.|Sem dimensões|
|d2c<br>.endpoints<br>.latency<br>.eventHubs|Roteamento: latência de mensagem para o Hub de Eventos|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem em um ponto de extremidade do Hub de Eventos.|Sem dimensões|
|d2c<br>.endpoints<br>.egress<br>.serviceBusQueues|Roteamento: mensagens entregues à Fila do Barramento de Serviço|Count|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade da fila do Barramento de Serviço.|Sem dimensões|
|d2c<br>.endpoints<br>.latency<br>.serviceBusQueues|Roteamento: latência de mensagem para a Fila do Barramento de Serviço|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do Barramento de Serviço.|Sem dimensões|
|d2c<br>.endpoints<br>.egress<br>.serviceBusTopics|Roteamento: mensagens entregues ao Tópico do Barramento de Serviço|Count|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do tópico do Barramento de Serviço.|Sem dimensões|
|d2c<br>.endpoints<br>.latency<br>.serviceBusTopics|Roteamento: latência de mensagem para o Tópico do Barramento de Serviço|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade do tópico do Barramento de Serviço.|Sem dimensões|
|d2c<br>.endpoints<br>.egress<br>.builtIn<br>.events|Roteamento: mensagens entregues a mensagens/eventos|Count|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens ao ponto de extremidade interno (mensagens/eventos). Essa métrica só começa a funcionar quando o roteamento está https://aka.ms/iotrouting) habilitado (para o Hub IOT.|Sem dimensões|
|d2c<br>.endpoints<br>.latency<br>.builtIn.events|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos). Essa métrica só começa a funcionar quando o roteamento está https://aka.ms/iotrouting) habilitado (para o Hub IOT.|Sem dimensões|
|d2c<br>.endpoints<br>.egress<br>.storage|Roteamento: mensagens entregues ao armazenamento|Count|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c<br>.endpoints<br>.latency<br>.storage|Roteamento: latência de mensagem para armazenamento|Milissegundos|Average|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Sem dimensões|
|d2c<br>.endpoints<br>.egress<br>.storage<br>.bytes|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregue aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c<br>.endpoints<br>.egress<br>.storage<br>.blobs|Roteamento: blobs entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou blobs aos pontos de extremidade de armazenamento.|Sem dimensões|
|EventGridDeliveries|Entregas da grade de eventos (visualização)|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento https://aka.ms/ioteventgrid) (. Para ver a origem das solicitações, use a dimensão EventType.|Resultado, EventType|
|EventGridLatency|Latência da grade de eventos (versão prévia)|Milissegundos|Average|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|d2c<br>.twin<br>.read<br>.success|Leituras de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c<br>.twin<br>.read<br>.failure|Leituras de gêmeos dos dispositivos com falhas|Count|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo com falhas.|Sem dimensões|
|d2c<br>.twin<br>.read<br>.size|Tamanho da resposta das leituras de gêmeos dos dispositivos|Bytes|Average|A média, o mínimo e o máximo de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c<br>.twin<br>.update<br>.success|Atualizações de gêmeos dos dispositivos bem-sucedidas|Count|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c<br>.twin<br>.update<br>.failure|Atualizações de gêmeos dos dispositivos com falhas|Count|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo.|Sem dimensões|
|d2c<br>.twin<br>.update<br>.size|Tamanho das atualizações de gêmeos dos dispositivos|Bytes|Average|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|c2d<br>.methods<br>.success|Invocações de método diretas bem-sucedidas|Count|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Sem dimensões|
|c2d<br>.methods<br>.failure|Invocações de método diretas com falhas|Count|Total|A contagem de todas as chamadas de método diretas com falhas.|Sem dimensões|
|c2d<br>.methods<br>.requestSize|Tamanho da solicitação das invocações de método diretas|Bytes|Average|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Sem dimensões|
|c2d<br>.methods<br>.responseSize|Tamanho da resposta das invocações de método diretas|Bytes|Average|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Sem dimensões|
|c2d<br>.twin<br>.read<br>.success|Leituras de gêmeos de back-end bem-sucedidas|Count|Total|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Sem dimensões|
|c2d<br>.twin<br>.read<br>.failure|Leituras de gêmeos de back-end com falhas|Count|Total|A contagem de todas as falhas de leituras de gêmeos iniciadas pelo back-end.|Sem dimensões|
|c2d<br>.twin<br>.read<br>.size|Tamanho da resposta das leituras de gêmeos de back-end|Bytes|Average|A média, o mínimo e o máximo de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Sem dimensões|
|c2d<br>.twin<br>.update<br>.success|Atualizações de gêmeos de back-end bem-sucedidas|Count|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Sem dimensões|
|c2d<br>.twin<br>.update<br>.failure|Atualizações de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de atualizações de gêmeos iniciadas pelo back-end.|Sem dimensões|
|c2d<br>.twin<br>.update<br>.size|Tamanho das atualizações de gêmeos de back-end|Bytes|Average|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Sem dimensões|
|twinQueries<br>.success|Consultas de gêmeos bem-sucedidas|Count|Total|A contagem de todas as consultas de gêmeos bem-sucedidas.|Sem dimensões|
|twinQueries<br>.failure|Consultas de gêmeos com falhas|Count|Total|A contagem de todas as consultas de gêmeos com falhas.|Sem dimensões|
|twinQueries<br>.resultSize|Tamanho do resultado das consultas de gêmeos|Bytes|Average|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de gêmeos bem-sucedidas.|Sem dimensões|
|trabalhos<br>.createTwinUpdateJob<br>.success|Criações de trabalhos de atualização de gêmeos bem-sucedidas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos bem-sucedidos.|Sem dimensões|
|trabalhos<br>.createTwinUpdateJob<br>.failure|Criações de trabalhos de atualização de gêmeos com falhas|Count|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos com falhas.|Sem dimensões|
|trabalhos<br>.createDirectMethodJob<br>.success|Criações de trabalhos de invocação de método bem-sucedidas|Count|Total|A contagem de todas as criações de trabalhos de invocação de método direto bem-sucedidas.|Sem dimensões|
|trabalhos<br>.createDirectMethodJob<br>.failure|Criações de trabalhos de invocação de método com falhas|Count|Total|A contagem de todas as criações de trabalhos de invocação de método direto com falhas.|Sem dimensões|
|trabalhos<br>.listJobs<br>.success|Chamadas para listar trabalhos bem-sucedidas|Count|Total|A contagem de todas as chamadas para listar trabalhos bem-sucedidas.|Sem dimensões|
|trabalhos<br>.listJobs<br>.failure|Chamadas para listar trabalhos com falhas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos com falhas.|Sem dimensões|
|trabalhos<br>.cancelJob<br>.success|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho bem-sucedidas.|Sem dimensões|
|trabalhos<br>.cancelJob<br>.failure|Cancelamentos de trabalho com falhas|Count|Total|A contagem de todas as chamadas para cancelar um trabalho com falhas.|Sem dimensões|
|trabalhos<br>.queryJobs<br>.success|Consultas de trabalho bem-sucedidas|Count|Total|A contagem de todas as chamadas para consultas de trabalho bem-sucedidas.|Sem dimensões|
|trabalhos<br>.queryJobs<br>.failure|Consultas de trabalho com falhas|Count|Total|A contagem de todas as chamadas para consultas de trabalho com falhas.|Sem dimensões|
|trabalhos<br>.completed|Trabalhos concluídos|Count|Total|A contagem de todos os trabalhos concluídos.|Sem dimensões|
|trabalhos<br>.failed|Trabalhos com falha|Count|Total|A contagem de todos os trabalhos com falha.|Sem dimensões|
|d2c<br>.telemetry<br>.ingress<br>.sendThrottle|Número de erros de limitação|Contagem|Total|Número de erros de limitação devido a limitações da taxa de transferência do dispositivo|Sem dimensões|
|dailyMessage<br>QuotaUsed|Número total de mensagens usadas|Count|Average|Número total de mensagens utilizadas hoje. Este é um valor cumulativo que é reiniciado para zero em 00:00 UTC diariamente.|Sem dimensões|
|deviceDataUsage|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Sem dimensões|
|totalDeviceCount|Total de dispositivos (versão prévia)|Count|Average|Número de dispositivos registrados para o seu hub IoT|Sem dimensões|
|connected<br>DeviceCount|Dispositivos conectados (versão prévia)|Count|Average|Número de dispositivos registrados ao seu hub IoT|Sem dimensões|
|configurações|Métricas de configuração|Contagem|Total|Métricas para operações de configuração|Sem dimensões|

## <a name="next-steps"></a>Próximas etapas

Depois da visão geral das métricas do Hub IoT, acesse este link para saber mais sobre o gerenciamento do Hub IoT do Azure:

* [Monitoramento de operações](iot-hub-operations-monitoring.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)

* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
