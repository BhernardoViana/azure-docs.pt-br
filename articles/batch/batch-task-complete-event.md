---
title: Evento de conclusão de tarefa – Azure | Microsoft Docs
description: Referência de evento de tarefa de lote concluída.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 085ca1d007371c3afd7246078369475da38f9b9f
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258254"
---
# <a name="task-complete-event"></a>Evento de conclusão de tarefa

 Esse evento é emitido quando uma tarefa é concluída, independentemente do código de saída. Esse evento pode ser usado para determinar a duração de uma tarefa, em que ela foi executada e se foi repetida.


 O exemplo a seguir mostra o corpo de um evento de conclusão de tarefa.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Nome do elemento|Tipo|Observações|
|------------------|----------|-----------|
|`jobId`|Cadeia|A ID do trabalho que contém a tarefa.|
|`id`|Cadeia|A ID da tarefa.|
|`taskType`|Cadeia|O tipo de tarefa. Pode ser “JobManager” indicando que é uma tarefa do gerenciador de trabalhos ou “Usuário”, indicando que não é uma tarefa do gerenciador de trabalhos. Esse evento não é emitido para tarefas de preparação, lançamento ou inicialização de trabalho.|
|`systemTaskVersion`|Int32|Esse é o contador interno de repetição de uma tarefa. Internamente, o serviço em lotes pode repetir uma tarefa para contabilizar problemas transitórios. Esses problemas podem incluir erros internos de agendamento ou tentativa de recuperar nós de computação em estado inválido.|
|[`nodeInfo`](#nodeInfo)|Tipo Complexo|Contém informações sobre o nó de computação em que a tarefa é executada.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Tipo Complexo|Especifica que a tarefa é uma tarefa com várias instâncias que precisa de vários nós de computação.  Consulte [`multiInstanceSettings`](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) para obter detalhes.|
|[`constraints`](#constraints)|Tipo Complexo|As restrições de execução aplicáveis a essa tarefa.|
|[`executionInfo`](#executionInfo)|Tipo Complexo|Contém informações sobre a execução da tarefa.|

###  <a name="nodeInfo"></a> nodeInfo

|Nome do elemento|Tipo|Observações|
|------------------|----------|-----------|
|`poolId`|Cadeia|A ID do pool no qual a tarefa foi executada.|
|`nodeId`|Cadeia|A ID do nó no qual a tarefa foi executada.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Nome do elemento|Tipo|Observações|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|O número de nós de computação que a tarefa precisa.|

###  <a name="constraints"></a> restrições

|Nome do elemento|Tipo|Observações|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|O número máximo de vezes que a tarefa pode ser repetida. O serviço em lotes repetirá uma tarefa se seu código de saída for diferente de zero.<br /><br /> Observe que esse valor controla especificamente o número de tentativas. O serviço em lotes tentará a tarefa uma vez e, em seguida, pode tentar novamente até esse limite. Por exemplo, se a contagem máxima de repetição for 3, o lote tentará uma tarefa até 4 vezes (uma tentativa inicial e 3 repetições).<br /><br /> Se a contagem máxima de repetição for 0, o serviço em lote não tentará repetir a tarefas.<br /><br /> Se a contagem máxima de repetição for -1, o serviço em lotes repetirá as tarefas ilimitadamente.<br /><br /> O valor padrão é 0 (sem novas tentativas).|

###  <a name="executionInfo"></a> executionInfo

|Nome do elemento|Tipo|Observações|
|------------------|----------|-----------|
|`startTime`|DateTime|A hora em que a tarefa começou a ser executada. “Em execução” corresponde ao estado de **execução**, portanto, se a tarefa especificar arquivos de recursos ou pacotes de aplicativos, a hora de início refletirá a hora na qual a tarefa começou a baixar ou implantar esses arquivos ou pacotes.  Se a tarefa foi reiniciada ou repetida, esta é a última vez em que a tarefa começou a ser executada.|
|`endTime`|DateTime|A hora e conclusão da tarefa.|
|`exitCode`|Int32|O código de saída da tarefa.|
|`retryCount`|Int32|O número de vezes que a tarefa foi repetida pelo serviço em lotes. A tarefa será repetida se a saída tiver um código de saída diferente de zero, até a MaxTaskRetryCount especificada.|
|`requeueCount`|Int32|O número de vezes que a tarefa foi colocada em fila novamente pelo serviço em lotes, como resultado de uma solicitação de usuário.<br /><br /> Quando o usuário remove nós de um pool (redimensionando ou reduzindo o pool) ou quando o trabalho é desabilitado, o usuário pode especificar que as tarefas em execução nos nós sejam colocadas novamente na fila de execução. Essa contagem controla quantas vezes a tarefa foi colocada novamente em fila por esses motivos.|
