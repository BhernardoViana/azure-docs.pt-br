---
title: Amostra de script da CLI do Azure – Copiar (mover) um instantâneo de um disco gerenciado para a mesma ou outra assinatura com CLI
description: Amostra de script da CLI do Azure – Copiar (mover) um instantâneo de um disco gerenciado para a mesma ou outra assinatura com CLI
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 23886256e405f57c5a581356b4212498779ae6e5
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038250"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Copiar um instantâneo de um disco gerenciado para a mesma assinatura ou outra assinatura com a CLI

Esse script copia um instantâneo de um disco gerenciado para a mesma assinatura ou outra assinatura. Use este script para os cenários a seguir:

1. Migre uma captura de tela no armazenamento Premium (Premium_LRS) para o armazenamento padrão (Standard_LRS ou Standard_ZRS) para reduzir os custos.
1. Migre um instantâneo do armazenamento com redundância local (Premium_LRS, Standard_LRS) para o armazenamento com redundância de zona (Standard_ZRS) para se beneficiar da maior confiabilidade do armazenamento ZRS.
1. Mova um instantâneo para uma assinatura diferente na mesma região para retenção mais longa.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um instantâneo na assinatura de destino usando a ID do instantâneo de origem. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Obtém todas as propriedades de um instantâneo usando o nome e as propriedades do grupo de recursos do instantâneo. A propriedade de ID é usada para copiar o instantâneo para uma assinatura diferente.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Copia um instantâneo criando um instantâneo na assinatura diferente usando a Id e o nome do instantâneo pai.  |

## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual com base em um instantâneo](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
