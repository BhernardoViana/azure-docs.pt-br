---
title: Esquema de eventos de hubs de eventos da Grade de Eventos do Azure
description: Descreve as propriedades que são fornecidas para eventos de hubs de evento com a Grade de Eventos do Azure
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 6fac70a8837245e6d60a5a61db8a22ab718d4b49
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Esquema de eventos da Grade de Eventos do Azure para hubs de eventos

Este artigo fornece as propriedades e o esquema para eventos de hubs de evento. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

### <a name="available-event-types"></a>Tipos de evento disponíveis

Hubs de evento emite o **Microsoft.EventHub.CaptureFileCreated** quando um arquivo de captura é criado.

## <a name="example-event"></a>Exemplo de evento

Este exemplo de evento mostra o esquema de um evento dos Hubs de Eventos gerado quando o recurso Capturar armazena um arquivo: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de evento de hub de eventos. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| fileUrl | string | O caminho para o arquivo de captura. |
| FileType | string | O tipo de arquivo para o arquivo de captura. |
| partitionId | string | A ID de fragmento. |
| sizeInBytes | inteiro | Tamanho do arquivo. |
| eventCount | inteiro | O número de eventos no arquivo. |
| firstSequenceNumber | inteiro | O menor número de sequência da fila. |
| lastSequenceNumber | inteiro | O último número de sequência da fila. |
| firstEnqueueTime | string | A primeira vez da fila. |
| lastEnqueueTime | string | A última vez da fila. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
* Para obter informações sobre como manipular eventos de hubs de evento, consulte [fluxo de dados grandes em um data warehouse](event-grid-event-hubs-integration.md).