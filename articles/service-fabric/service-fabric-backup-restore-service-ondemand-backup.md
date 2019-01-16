---
title: Backup sob demanda no Azure Service Fabric | Microsoft Docs
description: Use o recurso de backup e restauração no Service Fabric para fazer backup dos dados do seu aplicativo conforme a necessidade.
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 8a276f26367e66f55b8fc10dbcba2429dc2e5450
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062684"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Backup sob demanda no Azure Service Fabric

Você pode fazer backup de dados de Reliable Stateful Services e Reliable Actors para lidar com cenários de desastre ou perda de dados.

O Azure Service Fabric é equipado com recursos para [backup periódico dos dados](service-fabric-backuprestoreservice-quickstart-azurecluster.md) e backup dos dados conforme a necessidade. O backup sob demanda é útil, pois ele protege contra a _perda_/_corrupção de dados_ causadas por alterações planejadas em seu ambiente ou serviço subjacente.

Os recursos de backup sob demanda são úteis para capturar o estado dos serviços antes de você acionar manualmente uma operação de serviço ou ambiente de serviço. Por exemplo, se você fizer uma alteração nos binários de serviço ao atualizar ou fazer o downgrade do serviço. Nesse caso, o backup sob demanda pode ajudar a proteger os dados contra corrupção por bugs no código do aplicativo.

## <a name="triggering-on-demand-backup"></a>Disparar um backup sob demanda

O backup sob demanda exige detalhes de armazenamento para carregar arquivos de backup. Especifique o local do backup sob demanda, na política de backup periódica ou em uma solicitação de backup sob demanda.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>Backup sob demanda para o armazenamento especificado por uma política de backup periódica

Você pode configurar a política de backup periódica para usar uma partição de um Reliable Stateful Service ou Reliable Actor para backup adicional sob demanda para armazenamento.

O caso a seguir é a continuação do cenário em [Habilitar o backup periódico para o serviço confiável com estado e Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). Nesse caso, você permite que uma política de backup use uma partição e que um backup ocorra em uma frequência definida no Armazenamento do Microsoft Azure.

Use a API [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) para configurar o acionamento do backup sob demanda para a ID de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Use a API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) para ativar o acompanhamento para o [progresso do backup sob demanda](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Backup sob demanda para o armazenamento especificado

Você pode solicitar o backup sob demanda para uma partição de um Reliable Stateful Service ou Reliable Actor. Forneça as informações de armazenamento como parte da solicitação de backup sob demanda.

Use a API [BackupPartition](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) para configurar o acionamento do backup sob demanda para a ID de partição `974bd92a-b395-4631-8a7f-53bd4ae9cf22`. Inclua as seguintes informações do Armazenamento do Microsoft Azure:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Você pode usar a API [GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) para configurar o acompanhamento para o [progresso do backup sob demanda](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

## <a name="tracking-on-demand-backup-progress"></a>Rastreamento do progresso de backup sob demanda

Uma partição de um serviço Confiável com Estado ou Reliable Actor aceita apenas uma solicitação de backup sob demanda por vez. Outra solicitação pode ser aceita apenas depois da conclusão da solicitação de backup sob demanda atual.

Partições diferentes podem acionar solicitações de backup sob demanda ao mesmo tempo.

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Solicitações de backup sob demanda podem estar nos seguintes estados:

- **Aceito**: backup iniciado na partição e em andamento.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Sucesso**, **Falha** ou **Tempo Limite**: Um backup sob demanda solicitado pode estar concluído com qualquer um dos seguintes estados:
  - **Êxito**: um estado de backup como _Sucesso_ indica que o backup foi realizado com sucesso no estado da partição. A resposta fornece _BackupEpoch_ e _BackupLSN_ para a partição e a hora em UTC.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Falha**: um estado de backup como _Falha_ indica que ocorreu uma falha durante o backup do estado da partição. A causa da falha é indicada na resposta.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Tempo Limite**: um estado de backup como _Tempo Limite_ indica que o backup do estado da partição não pôde ser criado em um determinado período de tempo. O valor do tempo limite padrão é de 10 minutos. Inicie uma nova solicitação de backup sob demanda com [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) maior neste cenário.
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Próximas etapas

- [Noções básicas sobre a configuração de backup periódico](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [Referência da API REST BackupRestore](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
