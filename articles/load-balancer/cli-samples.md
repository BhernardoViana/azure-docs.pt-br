---
title: Exemplos da CLI do Azure para o Balanceador de Carga
titleSuffix: Azure Load Balancer
description: Exemplos de CLI do Azure
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: f35007bc3c47aeec9bcdd8a418983b95f6f20ad6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225428"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Exemplos da CLI do Azure para o Balanceador de Carga

A tabela a seguir inclui links para bash scripts criados usando a CLI do Azure.

| | |
|-|-|
| [Balancear o tráfego de VMs para alta disponibilidade](./scripts/load-balancer-linux-cli-sample-nlb.md) | Cria várias máquinas virtuais em uma configuração altamente disponíveis e de balanceamento de carga. |
| [VMs com balanceamento de carga entre zonas de disponibilidade](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Cria três VMs em diferentes zonas de disponibilidade dentro de uma região e o Standard Load Balancer com um endereço IP de front-end com redundância de zona. Essa configuração de balanceador de carga ajuda a proteger seus aplicativos e dados contra a falha ou perda improvável de um datacenter inteiro. |
|[VMs com balanceamento de carga em uma zona específica de disponibilidade](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Cria três VMs, um Standard Load Balancer com o IP de front-end zonal que ajuda a alinhar o caminho de dados e os recursos em uma única zona de uma determinada região.|
| [Balanceamento de carga de vários sites em VMs](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Cria duas VMs com várias configurações de IP, associadas a um conjunto de disponibilidade do Azure, acessível por meio de um Azure Load Balancer. |
| | |

