---
title: Exemplos de CLI do Azure - Windows | Microsoft Docs
description: Exemplos de CLI do Azure - Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4068bee0db04207c90344f3588c4e117b650a2f4
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Exemplos de CLI do Azure para máquinas virtuais do Windows

A tabela a seguir inclui links para scripts bash criados usando a CLI do Azure que implanta máquinas virtuais do Windows.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma máquina virtual do Windows com configuração mínima. |
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um grupo de recursos, a máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Cria várias máquinas virtuais em uma configuração altamente disponíveis e de balanceamento de carga. |
| [Criar uma máquina virtual e executar o script de configuração](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma máquina virtual e usa a Extensão de Script Personalizado do Azure para instalar o IIS. |
| [Criar uma máquina virtual e executar a configuração da DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma máquina virtual e usa a extensão DSC (Configuração de Estado de Desejado) do Azure para instalar o IIS. |
|**Máquinas virtuais de rede**||
| [Proteger o tráfego de rede entre as máquinas virtuais](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Cria duas máquinas virtuais, todos os recursos relacionados e grupos de segurança de rede (NSG) internos e externos. |
|**Proteger máquinas virtuais**||
| [Criptografar uma VM e discos de dados](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Cria um Azure Key Vault, uma chave de criptografia e entidade de serviço e, em seguida, criptografa uma VM. |
|**Monitorar máquinas virtuais**||
| [Monitorar uma VM com o Operations Management Suite](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Cria uma máquina virtual, instala o agente do Operations Management Suite e registra a VM em um espaço de trabalho do OMS.  |
| | |
