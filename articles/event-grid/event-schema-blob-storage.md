---
title: Esquema de evento de armazenamento de blob de grade de eventos do Azure
description: Descreve as propriedades que são fornecidas para eventos de armazenamento de BLOBs com a grade de eventos do Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 0ab81d3c1d4c68827cf1569bf4a22c3311fe355d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555829"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Esquema de evento da grade de eventos do Azure para armazenamento de BLOBs

Este artigo fornece as propriedades e o esquema para eventos de armazenamento de BLOBs. Para obter uma introdução aos esquemas de evento, consulte [esquema de evento da grade de eventos do Azure](event-schema.md).

Para obter uma lista de exemplos de scripts e tutoriais, consulte [fonte do evento de armazenamento](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Lista de eventos para APIs REST de BLOB

Esses eventos são disparados quando um cliente cria, substitui ou exclui um blob chamando APIs REST de BLOB.

 |Nome do evento |Descrição|
 |----------|-----------|
 |**Microsoft. Storage. BlobCreated** |Disparado quando um blob é criado ou substituído. <br>Especificamente, esse evento é disparado quando os clientes usam as operações `PutBlob`, `PutBlockList` ou `CopyBlob` que estão disponíveis na API REST do blob.   |
 |**Microsoft. Storage. BlobDeleted** |Disparado quando um blob é excluído. <br>Especificamente, esse evento é disparado quando os clientes chamam a operação de `DeleteBlob` que está disponível na API REST do blob. |

> [!NOTE]
> Se você quiser garantir que o evento **Microsoft. Storage. BlobCreated** seja disparado somente quando um blob de blocos for completamente confirmado, filtre o evento para as chamadas de API REST `CopyBlob`, `PutBlob` e `PutBlockList`. Essas chamadas de API disparam o evento **Microsoft. Storage. BlobCreated** somente depois que os dados são totalmente confirmados em um blob de blocos. Para saber como criar um filtro, consulte [filtrar eventos para a grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista de eventos para as APIs REST do Azure Data Lake Storage Gen 2

Esses eventos serão disparados se você habilitar um namespace hierárquico na conta de armazenamento e os clientes chamarem Azure Data Lake Storage Gen2 APIs REST.

|Nome do evento|Descrição|
|----------|-----------|
|**Microsoft. Storage. BlobCreated** | Disparado quando um blob é criado ou substituído. <br>Especificamente, esse evento é disparado quando os clientes usam as operações `CreateFile` e `FlushWithClose` que estão disponíveis na API REST do Azure Data Lake Storage Gen2. |
|**Microsoft. Storage. BlobDeleted** |Disparado quando um blob é excluído. <br>Especificamente, esse evento também é disparado quando os clientes chamam a operação de `DeleteFile` que está disponível na API REST do Azure Data Lake Storage Gen2. |
|**Microsoft. Storage. BlobRenamed**|Disparado quando um blob é renomeado. <br>Especificamente, esse evento é disparado quando os clientes usam a operação `RenameFile` que está disponível na API REST do Azure Data Lake Storage Gen2.|
|**Microsoft. Storage. DirectoryCreated**|Disparado quando um diretório é criado. <br>Especificamente, esse evento é disparado quando os clientes usam a operação `CreateDirectory` que está disponível na API REST do Azure Data Lake Storage Gen2.|
|**Microsoft. Storage. DirectoryRenamed**|Disparado quando um diretório é renomeado. <br>Especificamente, esse evento é disparado quando os clientes usam a operação `RenameDirectory` que está disponível na API REST do Azure Data Lake Storage Gen2.|
|**Microsoft. Storage. DirectoryDeleted**|Disparado quando um diretório é excluído. <br>Especificamente, esse evento é disparado quando os clientes usam a operação `DeleteDirectory` que está disponível na API REST do Azure Data Lake Storage Gen2.|

> [!NOTE]
> Se você quiser garantir que o evento **Microsoft. Storage. BlobCreated** seja disparado somente quando um blob de blocos for completamente confirmado, filtre o evento para a chamada à API REST do `FlushWithClose`. Essa chamada à API dispara o evento **Microsoft. Storage. BlobCreated** somente depois que os dados são totalmente confirmados em um blob de blocos. Para saber como criar um filtro, consulte [filtrar eventos para a grade de eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta de evento

Quando um evento é disparado, o serviço de grade de eventos envia dados sobre esse evento para o ponto de extremidade de assinatura.

Esta seção contém um exemplo de como os dados seriam para cada evento de armazenamento de BLOBs.

### <a name="microsoftstorageblobcreated-event"></a>Evento Microsoft. Storage. BlobCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Evento Microsoft. Storage. BlobCreated (Data Lake Storage Gen2)

Se a conta de armazenamento de BLOBs tiver um namespace hierárquico, os dados se assemelharão ao exemplo anterior com a exceção dessas alterações:

* A chave de `dataVersion` é definida com um valor de `2`.

* A chave de `data.api` é definida como a cadeia de caracteres `CreateFile` ou `FlushWithClose`.

* A chave de `contentOffset` está incluída no conjunto de dados.

> [!NOTE]
> Se os aplicativos usarem a operação `PutBlockList` para carregar um novo BLOB na conta, os dados não conterão essas alterações.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Evento Microsoft. Storage. BlobDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Evento Microsoft. Storage. BlobDeleted (Data Lake Storage Gen2)

Se a conta de armazenamento de BLOBs tiver um namespace hierárquico, os dados se assemelharão ao exemplo anterior com a exceção dessas alterações:

* A chave de `dataVersion` é definida com um valor de `2`.

* A chave de `data.api` é definida como a cadeia de caracteres `DeleteFile`.

* A chave de `url` contém o caminho `dfs.core.windows.net`.

> [!NOTE]
> Se os aplicativos usarem a operação `DeleteBlob` para excluir um blob da conta, os dados não conterão essas alterações.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Evento Microsoft. Storage. BlobRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Evento Microsoft. Storage. DirectoryCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Evento Microsoft. Storage. DirectoryRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Evento Microsoft. Storage. DirectoryDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| Tópico | cadeia de caracteres | Caminho completo do recurso para a origem do evento. Este campo não é gravável. A grade de eventos fornece esse valor. |
| subject | cadeia de caracteres | Caminho definido pelo Publicador para a entidade do evento. |
| EventType | cadeia de caracteres | Um dos tipos de evento registrados para essa origem do evento. |
| eventTime | cadeia de caracteres | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | cadeia de caracteres | Identificador exclusivo do evento. |
| data | objeto | Dados de evento de armazenamento de BLOBs. |
| versão de | cadeia de caracteres | A versão do esquema do objeto de dados. O Publicador define a versão do esquema. |
| metadataVersion | cadeia de caracteres | A versão do esquema dos metadados do evento. A grade de eventos define o esquema das propriedades de nível superior. A grade de eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| API | cadeia de caracteres | A operação que disparou o evento. |
| clientRequestId | cadeia de caracteres | uma ID de solicitação fornecida pelo cliente para a operação da API de armazenamento. Essa ID pode ser usada para correlacionar os logs de diagnóstico do armazenamento do Azure usando o campo "Client-Request-ID" nos logs e pode ser fornecida em solicitações de cliente usando o cabeçalho "x-MS-Client-Request-ID". Consulte [formato de log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | cadeia de caracteres | ID da solicitação gerada pelo serviço para a operação da API de armazenamento. Pode ser usado para correlacionar os logs de diagnóstico do armazenamento do Azure usando o campo "solicitação-ID-cabeçalho" nos logs e é retornado de iniciar chamada à API no cabeçalho ' x-MS-Request-ID '. Consulte [formato de log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | cadeia de caracteres | O valor que você pode usar para executar operações condicionalmente. |
| contentType | cadeia de caracteres | O tipo de conteúdo especificado para o blob. |
| contentLength | valores | O tamanho do blob em bytes. |
| BlobType | cadeia de caracteres | O tipo de BLOB. Os valores válidos são "BlockBlob" ou "PageBlob". |
| contentOffset | Automática | O deslocamento em bytes de uma operação de gravação realizada no ponto em que o aplicativo de gatilho de evento concluiu a gravação no arquivo. <br>Aparece somente para eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico.|
| destinationUrl |cadeia de caracteres | A URL do arquivo que existirá após a conclusão da operação. Por exemplo, se um arquivo for renomeado, a propriedade `destinationUrl` conterá a URL do novo nome de arquivo. <br>Aparece somente para eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico.|
| sourceUrl |cadeia de caracteres | A URL do arquivo que existe antes da operação. Por exemplo, se um arquivo for renomeado, o `sourceUrl` conterá a URL do nome do arquivo original antes da operação de renomeação. <br>Aparece somente para eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico. |
| URL | cadeia de caracteres | O caminho para o blob. <br>Se o cliente usar uma API REST de BLOB, a URL terá essa estrutura: *\<storage-Account-name \>. blob.core.windows.net/\<container-name \> / \<file-name \>* . <br>Se o cliente usar uma API REST Data Lake Storage, a URL terá essa estrutura: *\<storage-Account-name \>. dfs.core.windows.net/\<file-System-name \> / \<file-name \>* . |
| recursiva | cadeia de caracteres | `True` executar a operação em todos os diretórios filho; caso contrário `False`. <br>Aparece somente para eventos disparados em contas de armazenamento de BLOBs que têm um namespace hierárquico. |
| Sequencer | cadeia de caracteres | Um valor de cadeia de caracteres opaco que representa a sequência lógica de eventos para qualquer nome de blob específico.  Os usuários podem usar a comparação de cadeias de caracteres padrão para entender a sequência relativa de dois eventos no mesmo nome de BLOB. |
| storageDiagnostics | objeto | Dados de diagnóstico eventualmente incluídos pelo serviço de armazenamento do Azure. Quando presente, deve ser ignorado por consumidores de evento. |

## <a name="next-steps"></a>Próximos passos

* Para obter uma introdução à grade de eventos do Azure, consulte [o que é a grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).
* Para obter uma introdução ao trabalho com eventos de armazenamento de BLOBs, consulte [rotear eventos de armazenamento de BLOBs-CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
