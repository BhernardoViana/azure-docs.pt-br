---
title: Propriedades do módulo agente e Hub gêmeos-Azure IoT Edge
description: Revise as propriedades específicas e os respectivos valores para os módulos gêmeos edgeAgent e edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 731c51894126a6de75c9fc25e4e7bdb3dfa4dd03
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665790"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Propriedades do agente do IoT Edge e dos gêmeos de módulo do hub do IoT Edge

O agente do IoT Edge e hub do IoT Edge são dois módulos que compõem o runtime do IoT Edge. Para obter mais informações sobre quais funções cada módulo executa, consulte [Entender o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md). 

Este artigo fornece as propriedades desejadas e as propriedades relatadas dos módulos gêmeos de runtime. Para obter mais informações sobre como implantar módulos em dispositivos IoT Edge, consulte [saiba como implantar módulos e estabelecer rotas no IOT Edge](module-composition.md).

Um módulo de r inclui: 

* **Propriedades desejadas**. O back-end da solução pode definir as propriedades desejadas e o módulo pode lê-las. O módulo também pode receber notificações de alterações nas propriedades desejadas. As propriedades desejadas são usadas junto com as propriedades relatadas para sincronizar a configuração ou as condições do módulo.

* **Propriedades reportadas**. O módulo pode definir as propriedades relatadas e o back-end da solução pode lê-las e consultá-las. As propriedades relatadas são usadas junto com as propriedades desejadas para sincronizar a configuração ou as condições do módulo. 

## <a name="edgeagent-desired-properties"></a>Propriedades desejadas do EdgeAgent

O gêmeo do módulo para o agente do IoT Edge se chama `$edgeAgent` e coordena as comunicações entre o agente do IoT Edge em execução em um dispositivo e o Hub IoT. As propriedades desejadas são definidas durante a aplicação de um manifesto de implantação em um dispositivo específico como parte de uma implantação de dispositivo único ou em escala. 

| Propriedade | Descrição | obrigatórios |
| -------- | ----------- | -------- |
| schemaVersion | Tem que ser "1.0" | SIM |
| runtime.type | Tem que ser "docker" | SIM |
| runtime.settings.minDockerVersion | Definido para a versão mínima do Docker necessária para o manifesto de implantação | SIM |
| runtime.settings.loggingOptions | Um JSON em cadeias de caracteres que contém as opções de registro para o contêiner do agente do IoT Edge. [Opções de registro em log do Docker](https://docs.docker.com/engine/admin/logging/overview/) | Não |
| runtime.settings.registryCredentials<br>.{registryId}.username | O nome de usuário do registro de contêiner. Para registro de contêiner do Azure, o nome de usuário geralmente é o nome do registro.<br><br> Credenciais de registro são necessárias para as imagens de módulo que não são públicas. | Não |
| runtime.settings.registryCredentials<br>.{registryId}.password | A senha para o registro de contêiner. | Não |
| runtime.settings.registryCredentials<br>.{registryId}.address | O endereço do registro de contêineres. Para Registro de Contêiner do Azure, o endereço é geralmente *{nome do registro}.azurecr.io*. | Não |  
| systemModules.edgeAgent.type | Tem que ser "docker" | SIM |
| systemModules.edgeAgent.settings.image | O URI da imagem do agente do IoT Edge. Atualmente, o agente do IoT Edge não é capaz de se atualizar. | SIM |
| systemModules.edgeAgent.settings<br>.createOptions | Um JSON em cadeias de caracteres que contém as opções para a criação do contêiner do agente do IoT Edge. [Opções de criação do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeAgent.configuration.id | A ID da implantação que implantou este módulo. | O Hub IoT define essa propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| systemModules.edgeHub.type | Tem que ser "docker" | SIM |
| systemModules.edgeHub.status | Deve estar "em execução" | SIM |
| systemModules.edgeHub.restartPolicy | Deve estar "sempre" | SIM |
| systemModules.edgeHub.settings.image | O URI da imagem do hub do IoT Edge. | SIM |
| systemModules.edgeHub.settings<br>.createOptions | Um JSON em cadeias de caracteres que contém as opções para a criação do contêiner do hub do IoT Edge. [Opções de criação do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| systemModules.edgeHub.configuration.id | A ID da implantação que implantou este módulo. | O Hub IoT define essa propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |
| modules.{moduleId}.version | Uma cadeia definida pelo usuário que representa a versão desse módulo. | SIM |
| modules.{moduleId}.type | Tem que ser "docker" | SIM |
| modules.{moduleId}.status | {"running" \| "stopped"} | SIM |
| modules.{moduleId}.restartPolicy | {"nunca" \| "em caso de falha" \| "não íntegro" \| "sempre"} | SIM |
| módulos. {ModuleID}. imagePullPolicy | {"on-Create" \| "Never"} | Não |
| modules.{moduleId}.settings.image | O URI para a imagem do módulo. | SIM |
| modules.{moduleId}.settings.createOptions | Um JSON em cadeias de caracteres que contém as opções para a criação do contêiner do módulo. [Opções de criação do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Não |
| modules.{moduleId}.configuration.id | A ID da implantação que implantou este módulo. | O Hub IoT define essa propriedade quando o manifesto é aplicado usando uma implantação. Não faz parte de um manifesto de implantação. |

## <a name="edgeagent-reported-properties"></a>Propriedades relatadas do EdgeAgent

As propriedades relatadas pelo agente do IoT Edge incluem três partes de informação principais:

1. O status do aplicativo das propriedades desejadas vistas pela última vez;
2. O status dos módulos em execução no momento no dispositivo, conforme relatado pelo agente do IoT Edge; e
3. Uma cópia das propriedades desejadas atualmente em execução no dispositivo.

Essa última informação, uma cópia das propriedades desejadas atuais, é útil para informar se o dispositivo aplicou as propriedades desejadas mais recentes ou ainda está executando um manifesto de implantação anterior.

> [!NOTE]
> As propriedades relatadas do agente do IoT Edge são úteis porque podem ser consultadas com a [linguagem de consulta do Hub IoT](../iot-hub/iot-hub-devguide-query-language.md) para investigar o status das implantações em grande escala. Para saber mais sobre como usar as propriedades de agente do IoT Edge para status, confira [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md).

A tabela a seguir não inclui as informações que são copiadas das propriedades desejadas.

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo agente do IoT Edge. |
| lastDesiredStatus.code | Esse código de status refere-se às últimas propriedades desejadas vistas pelo agente de IoT Edge. Valores permitidos: `200` Êxito, `400` Configuração inválida, `412` Versão do esquema inválido, `417` As propriedades desejadas estão vazias, `500` Falha |
| lastDesiredStatus.description | A descrição do status |
| deviceHealth | `healthy` se o status do runtime de todos os módulos for `running` ou `stopped`, caso contrário, `unhealthy` |
| configurationHealth.{deploymentId}.health | `healthy` se o status do runtime de todos os módulos definido pela implantação {deploymentId} for `running` ou `stopped`, caso contrário, `unhealthy` |
| runtime.platform.OS | Relatório do sistema operacional em execução no dispositivo |
| runtime.platform.architecture | Relatório da arquitetura do CPU do dispositivo |
| systemModules.edgeAgent.runtimeStatus | O status relatado do agente do IoT Edge: {"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | Descrição em texto do status relatado do agente do IoT Edge. |
| systemModules.edgeHub.runtimeStatus | Status do hub do IoT Edge: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Descrição de texto do status do hub do IoT Edge se não estiver íntegro. |
| systemModules.edgeHub.exitCode | O código de saída relatado pelo contêiner do Hub IoT Edge se o contêiner for encerrado |
| systemModules.edgeHub.startTimeUtc | Hora em que o hub do IoT Edge foi iniciado pela última vez |
| systemModules.edgeHub.lastExitTimeUtc | Hora em que o hub do IoT Edge foi encerrado pela última vez |
| systemModules.edgeHub.lastRestartTimeUtc | Hora em que o hub do IoT Edge foi reiniciado pela última vez |
| systemModules.edgeHub.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinicialização. |
| modules.{moduleId}.runtimeStatus | Status do módulo: { "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | Texto de descrição do status do módulo se não estiver íntegro. |
| modules.{moduleId}.exitCode | O código de saída relatado pelo contêiner do módulo se o contêiner for encerrado |
| modules.{moduleId}.startTimeUtc | Hora em que o módulo foi iniciado pela última vez |
| modules.{moduleId}.lastExitTimeUtc | Hora em que o módulo foi encerrado pela última vez |
| modules.{moduleId}.lastRestartTimeUtc | Hora em que o módulo foi reiniciado pela última vez |
| modules.{moduleId}.restartCount | Número de vezes que este módulo foi reiniciado como parte da política de reinicialização. |

## <a name="edgehub-desired-properties"></a>Propriedades desejadas do EdgeHub

O gêmeo do módulo para o hub do IoT Edge se chama `$edgeHub` e coordena as comunicações entre o hub do IoT Edge em execução em um dispositivo e o Hub IoT. As propriedades desejadas são definidas durante a aplicação de um manifesto de implantação em um dispositivo específico como parte de uma implantação de dispositivo único ou em escala. 

| Propriedade | Descrição | Necessárias no manifesto de implantação |
| -------- | ----------- | -------- |
| schemaVersion | Tem que ser "1.0" | SIM |
| routes.{routeName} | Uma cadeia de caracteres que representa uma rota do hub do IoT Edge. Para obter mais informações, consulte [declarar rotas](module-composition.md#declare-routes). | O elemento `routes` pode estar presente mas vazio. |
| storeAndForwardConfiguration.timeToLiveSecs | O tempo em segundos que IoT Edge Hub mantém mensagens se desconectado dos pontos de extremidade de roteamento, seja o Hub IoT ou um módulo local. O valor pode ser qualquer inteiro positivo. | SIM |

## <a name="edgehub-reported-properties"></a>Propriedades relatadas do EdgeHub

| Propriedade | Descrição |
| -------- | ----------- |
| lastDesiredVersion | Este inteiro refere-se à última versão das propriedades desejadas processadas pelo hub do IoT Edge. |
| lastDesiredStatus.code | O código de status referente às últimas propriedades desejadas vistas pelo hub de IoT Edge. Valores permitidos: `200` Êxito, `400` Configuração inválida, `500` Falha |
| lastDesiredStatus.description | Texto descrição do status. |
| clients.{device or moduleId}.status | O status de conectividade do dispositivo ou do módulo. Valores possíveis {"connected" \| "disconnected"}. Somente as identidades de módulo podem estar em estado desconectado. Os dispositivos downstream conectados ao hub do IoT Edge aparecem somente quando conectados. |
| clients.{device or moduleId}.lastConnectTime | Última vez em que o dispositivo ou módulo está conectado. |
| clients.{device or moduleId}.lastDisconnectTime | Última vez em que o dispositivo ou módulo foi desconectado. |

## <a name="next-steps"></a>Próximos passos

Para saber como utilizar essas propriedades para compilar manifestos de implantação, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).
