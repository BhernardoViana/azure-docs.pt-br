---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: fe6c6d461fd6b99ce2ce57ebdd61a3f3e961489c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155703"
---
## <a name="storage"></a>Armazenamento

|  |  |
|---------|---------|
| [SKUs permitidas para Contas de Armazenamento e Máquinas Virtuais](../articles/governance/policy/samples/allowed-skus-storage.md) | Exige que contas de armazenamento e máquinas virtuais usem SKUs aprovadas. Usa as políticas internas para garantir SKUs aprovadas. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de conta de armazenamento aprovadas. |
| [SKUs de conta de armazenamento permitidas](../articles/governance/policy/samples/allowed-storage-account-skus.md) | Exige que contas de armazenamento usem uma SKU aprovada. Especifique uma matriz de SKUs aprovados. |
| [Negar camadas de acesso esporádico para contas de armazenamento](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Proíbe o uso de camadas de acesso esporádico para contas de armazenamento de blobs.  |
| [Assegurar tráfego https somente para contas de armazenamento](../articles/governance/policy/samples/ensure-https-storage-account.md) | Exige que contas de armazenamento usem tráfego HTTPS.  |
| [Assegurar a criptografia de arquivo de armazenamento](../articles/governance/policy/samples/ensure-storage-file-encryption.md) | Exige que a criptografia de arquivo esteja habilitada para contas de armazenamento.  |
| [Exigir criptografia de conta de armazenamento](../articles/governance/policy/samples/require-storage-account-encryption.md) | Exige que a conta de armazenamento use criptografia de blob.  |