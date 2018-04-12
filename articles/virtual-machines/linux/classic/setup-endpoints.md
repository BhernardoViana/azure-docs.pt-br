---
title: Configurar pontos de extremidade em uma VM do Linux clássica | Microsoft Docs
description: Saiba como configurar pontos de extremidade para uma VM do Linux no portal do Azure para permitir a comunicação com uma máquina virtual do Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: 03cb90dcdcc7a7407898ddd8cbc30f1af0833676
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Como configurar pontos de extremidade em uma máquina virtual clássica do Linux no Azure
Todas as máquinas virtuais do Linux criadas no Azure usando o modelo de implantação clássico podem se comunicar automaticamente com outras máquinas virtuais no mesmo serviço de nuvem ou rede virtual por um canal de rede privada. No entanto, os computadores na Internet ou outras redes virtuais requerem pontos de extremidade para direcionar o tráfego de rede de entrada para uma máquina virtual. Este artigo também está disponível para [máquinas virtuais do Windows](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

No modelo de implantação **Resource Manager**, os pontos de extremidade são configurados usando **NSGs (Grupos de Segurança de Rede)**. Para saber mais, consulte [Abrir portas e pontos de extremidade](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ao criar uma máquina virtual Linux no portal do Azure, no geral, um ponto de extremidade para o SSH (Secure Shell) é criado para você automaticamente. Você pode configurar pontos de extremidade adicionais criando a máquina virtual ou posteriormente, conforme a necessidade.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Próximas etapas
* Também é possível criar um ponto de extremidade de VM usando a [Interface de Linha de Comando do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Execute o comando **azure vm endpoint create** .
* Se você criou uma máquina virtual no modelo de implantação do Gerenciador de Recursos, é possível usar a CLI do Azure no modo do Gerenciador de Recursos para [criar grupos de segurança de rede](../../../virtual-network/tutorial-filter-network-traffic-cli.md) a fim de controlar o tráfego para a VM.
