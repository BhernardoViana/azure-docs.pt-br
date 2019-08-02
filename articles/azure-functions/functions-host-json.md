---
title: Referência host.json para o Azure Functions 2.x
description: Documentação de referência do arquivo host.json do Azure Functions com o tempo de execução v2.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 4688cf6fb82eb8f726205d54d0c852fd3daf8dfb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564792"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Referência host.json para o Azure Functions 2.x  

> [!div class="op_single_selector" title1="Selecione a versão do Azure Functions tempo de execução que você está usando: "]
> * [Versão 1](functions-host-json-v1.md)
> * [Versão 2](functions-host-json.md)

O arquivo de metadados *host.json* contém opções de configuração global que afetam todas as funções de um aplicativo de funções. Este artigo lista as configurações disponíveis para o tempo de execução v2.  

> [!NOTE]
> Este artigo serve para o Azure Functions 2.x.  Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md).

Outras opções de configuração de aplicativo de funções são gerenciadas nas [configurações de aplicativo](functions-app-settings.md).

Algumas configurações host.json são usadas apenas quando executadas localmente no arquivo [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>Arquivo host.json de exemplo

Os seguintes arquivos *host.json* de exemplo têm todas as opções possíveis especificadas.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "managedDependency": {
        "enabled": true
    }
}
```

As seções seguintes deste artigo explicam cada propriedade de nível superior. Todas são opcionais, a menos que seja indicado o contrário.

## <a name="aggregator"></a>agregador

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Essa configuração é a filha de [Registro em log](#logging).

Controla o [recurso de amostragem no Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

> [!NOTE]
> A amostragem de log pode fazer com que algumas execuções não apareçam na folha do Monitor do Application Insights.

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|isEnabled|true|Habilita ou desabilita a amostragem.| 
|maxTelemetryItemsPerSecond|5|O limite em que a amostragem começa.| 

## <a name="cosmosdb"></a>cosmosDb

A definição de configuração pode ser encontrada em [Associações e gatilhos do Cosmos DB](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

A definição de configuração pode ser encontrada em [Associações para Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

As definições de configuração podem ser encontradas em [Associações e gatilhos do Hub de Eventos](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>extensions

Propriedade que retorna um objeto que contém todas as configurações específicas de associação, como [http](#http) e [eventHub](#eventhub).

## <a name="functions"></a>funções

Uma lista de funções que o host de trabalho executa. Uma matriz vazia significa que todas as funções serão executadas. Para uso somente quando [em execução localmente](functions-run-local.md). Em aplicativos de funções no Azure, você deve seguir as etapas em [Como desabilitar funções no Azure Functions](disable-function.md) para desabilitar funções específicas em vez de usar essa configuração.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica a duração do tempo limite para todas as funções. Ele segue o formato de cadeia de caracteres TimeSpan. Em um plano de Consumo sem servidor, o intervalo válido é de 1 segundo a 10 minutos e o valor padrão é 5 minutos.  
Em um plano dedicado (serviço de aplicativo), não há nenhum limite geral e o padrão depende da versão de tempo de execução: 
+ Versão 1. x: o padrão é *NULL*, o que indica que não há tempo limite.   
+ Versão 2. x: o valor padrão é 30 minutos. Um valor de `-1` indica execução não vinculada.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Definições de configuração para [monitor de integridade de Host](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|enabled|true|Especifica se o recurso está habilitado. | 
|healthCheckInterval|10 segundos|O intervalo de tempo entre as verificações de integridade em segundo plano. | 
|healthCheckWindow|2 minutos|Uma janela de tempo deslizante usada em conjunto com a configuração `healthCheckThreshold`.| 
|healthCheckThreshold|6|Número máximo de vezes que a verificação de integridade pode falhar antes de uma reciclagem de host ser iniciada.| 
|counterThreshold|0.80|O limite no qual um contador de desempenho será considerado não íntegro.| 

## <a name="http"></a>http

As definições de configuração podem ser encontradas em [Associações e gatilhos HTTP](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>registro em log

Controla os comportamentos de registro em log do aplicativo de funções, incluindo o Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Define qual nível de log de arquivos está habilitado.  As opções são: `never`, `always` e `debugOnly`. |
|logLevel|N/D|Objeto que define a filtragem da categoria de log para funções no aplicativo. A versão 2.x segue o layout do ASP.NET Core para filtragem de categoria de log. Isso permite filtrar o registro para funções específicas. Para obter mais informações, consulte [Filtragem de logs](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) na documentação do ASP.NET Core. |
|console|N/D| A configuração de log do [console](#console). |
|applicationInsights|N/D| A configuração [applicationInsights](#applicationinsights). |

## <a name="console"></a>console

Essa configuração é a filha de [Registro em log](#logging). Ela controla o log do console quando não está no modo de depuração.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|isEnabled|false|Habilita ou desabilita o log de console.| 

## <a name="queues"></a>filas

As definições de configuração podem ser encontradas em [Associações e gatilhos da fila de armazenamento](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

A definição de configuração pode ser encontrada em [Associações e gatilhos do SendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

A definição de configuração pode ser encontrada em [Associações e gatilhos do Barramento de Serviço](functions-bindings-service-bus.md#host-json).

## <a name="singleton"></a>singleton

Parâmetro de configuração para o comportamento de bloqueio de Singleton. Para obter mais informações, consulte [Problema com o GitHub referente ao suporte de singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|lockPeriod|00:00:15|O período em que ocorrem os bloqueios de nível de função. Os bloqueios têm renovação automática.| 
|listenerLockPeriod|00:01:00|O período em que ocorrem os bloqueios de ouvinte.| 
|listenerLockRecoveryPollingInterval|00:01:00|O intervalo de tempo usado para a recuperação do bloqueio de ouvinte caso não tenha sido possível adquirir um bloqueio de ouvinte durante a inicialização.| 
|lockAcquisitionTimeout|00:01:00|A quantidade máxima de tempo em que o tempo de execução tenta adquirir um bloqueio.| 
|lockAcquisitionPollingInterval|N/D|O intervalo entre as tentativas de aquisição de bloqueio.| 

## <a name="version"></a>versão

A cadeia de caracteres de versão `"version": "2.0"` é necessária para um aplicativo de funções que segmente o tempo de execução v2.

## <a name="watchdirectories"></a>watchDirectories

Um conjunto de [diretórios de código compartilhado](functions-reference-csharp.md#watched-directories) que devem ser monitorados quanto a alterações.  Garante que, quando o código nesses diretórios é alterado, as alterações sejam coletadas pelas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="manageddependency"></a>managedDependency

A dependência gerenciada é um recurso de visualização que atualmente só tem suporte com funções baseadas no PowerShell. Ele permite que as dependências sejam gerenciadas automaticamente pelo serviço. Quando a propriedade Enabled é definida como true, o arquivo [requirements. psd1](functions-reference-powershell.md#dependency-management) será processado. As dependências serão atualizadas quando qualquer versão secundária for lançada.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como atualizar o arquivo host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consulte as configurações globais em variáveis de ambiente](functions-app-settings.md)
