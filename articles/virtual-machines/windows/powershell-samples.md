---
title: Amostras do PowerShell de máquina virtual do Azure | Microsoft Docs
description: Amostras do PowerShell de máquina virtual do Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 31dbb8485232d1b446469135b5bda0c8c7884190
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-virtual-machine-powershell-samples"></a>Amostras do PowerShell de máquina virtual do Azure

A tabela a seguir inclui links para amostras de scripts do PowerShell que criam e gerenciam máquinas virtuais Windows.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar rapidamente uma máquina virtual](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um grupo de recursos, máquina virtual e todos os recursos relacionados, com o mínimo de prompts.|
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um grupo de recursos, a máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria várias máquinas virtuais em uma configuração altamente disponíveis e de balanceamento de carga.|
| [Criar uma máquina virtual e executar o script de configuração](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual e usa a Extensão de Script Personalizado do Azure para instalar o IIS. |
| [Criar uma máquina virtual e executar a configuração da DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual e usa a extensão DSC (Configuração de Estado de Desejado) do Azure para instalar o IIS. |
| [Carregar um VHD e criar VMs](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Carrega um arquivo VHD local para o Azure, cria uma imagem do VHD e então cria uma VM dessa imagem. |
| [Criar VM por meio de um disco do sistema operacional gerenciado](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual anexando um disco gerenciado existente como disco do sistema operacional. |
| [Criar uma VM de um instantâneo](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual de um instantâneo criando primeiro um disco gerenciado de instantâneo e, em seguida, anexando o novo disco gerenciado como disco do sistema operacional. |
|**Gerenciar armazenamento**||
| [Criar um disco gerenciado de um VHD na mesma assinatura ou em uma diferente](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um disco gerenciado com base em um VHD especializado como um disco do sistema operacional ou com base em um VHD de dados como um disco de dados na mesma assinatura ou em uma diferente.  |
| [Criar um disco gerenciado com base em um instantâneo](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um disco gerenciado com base em um instantâneo. |
| [Copiar um disco gerenciado para a mesma assinatura ou outra assinatura](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Copia um disco gerenciado para a mesma assinatura ou outra assinatura, mas na mesma região do disco gerenciado pai. 
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exporta um instantâneo gerenciado como VHD para uma conta de armazenamento em outra região. |
| [Criar um instantâneo de um VHD](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um instantâneo de um VHD para criar vários discos gerenciados idênticos do instantâneo em um curto período.  |
| [Copiar um instantâneo para a mesma assinatura ou outra assinatura](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Copia um instantâneo para a mesma assinatura ou outra assinatura, mas na mesma região do instantâneo pai. |
|**Proteger máquinas virtuais**||
| [Criptografar uma VM e discos de dados](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Cria um Azure Key Vault, uma chave de criptografia e entidade de serviço e, em seguida, criptografa uma VM. |
|**Monitorar máquinas virtuais**||
| [Monitorar uma VM com o Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria uma máquina virtual, instala o agente do Operations Management Suite e registra a VM em um espaço de trabalho do OMS.  |
| | |

