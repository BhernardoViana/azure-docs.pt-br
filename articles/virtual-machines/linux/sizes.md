---
title: Tamanhos da VM Linux no Azure | Microsoft Docs
description: Lista os tamanhos diferentes disponíveis de máquinas virtuais do Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 5ee3d29ceada238c5e4a633b501f63ed307ba76e
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900860"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Tamanhos das máquinas virtuais do Linux no Azure
Este artigo descreve os tamanhos e as opções disponíveis de máquinas virtuais do Azure que você pode usar para executar seus aplicativos Linux e cargas de trabalho. Ele também fornece considerações de implantação a serem observadas ao planejar o uso desses recursos. Este artigo também está disponível para [máquinas virtuais do Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| Tipo                     | Tamanhos           |    DESCRIÇÃO       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Propósito geral](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2  | Relação equilibrada de CPU/memória. Ideal para teste e desenvolvimento, bancos de dados pequenos a médios e servidores Web de tráfego baixo a médio. |
| [Computação otimizada](sizes-compute.md)        | Fsv2, Fs, F             | Alta relação de CPU/memória. Boa para servidores web de tráfego médio, dispositivos de rede, processos de lote e servidores de aplicativo.        |
| [Memória otimizada](sizes-memory.md)         | Esv3, Ev3, M, GS, G, DSv2, Dv2  | Alta relação de memória/CPU. Ótima para servidores de banco de dados relacionais, caches médios a grandes e análises na memória.                 |
| [Armazenamento otimizado](sizes-storage.md)        | Ls                | Alta taxa de transferência de disco e de E/S. Ideal para Big Data, SQL e bancos de dados NoSQL.                                                         |
| [GPU](sizes-gpu.md)            | NV, NC, NCv2, NCv3, ND            | Máquinas virtuais especializadas direcionadas para edição de vídeo e renderização gráfica pesada, assim como inferência e treinamento do modelo (ND) com aprendizado profundo. Disponível com uma ou várias GPUs.       |
| [Computação de alto desempenho](sizes-hpc.md) | H       | Nossas máquinas virtuais de CPU mais rápidas e potentes com adaptadores de rede de alta taxa de transferência (RDMA) opcionais. 

<br>

- Para obter informações sobre os preços dos vários tamanhos, consulte [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Para ver a disponibilidade de tamanhos de VM nas regiões do Azure, confira [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).
- Para ver os limites gerais em VMs do Azure, consulte [Limites de assinatura e serviços do Azure, cotas e restrições](../../azure-subscription-service-limits.md).
- Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.


## <a name="rest-api"></a>API REST

Para obter informações sobre como usar a API REST para consulta de tamanhos de VM, confira o seguinte:

- [Listar os tamanhos de máquina virtual disponíveis para redimensionamento](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Listar os tamanhos de máquina virtual disponíveis para uma assinatura](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall)
- [Listar os tamanhos de máquina virtual disponíveis em um conjunto de disponibilidade](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.

## <a name="benchmark-scores"></a>Pontuações de parâmetro de comparação

Saiba mais sobre desempenho de computação para VMs do Linux usando as [pontuações de parâmetro de comparação CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os diferentes tamanhos de VM que estão disponíveis:
- [Propósito geral](sizes-general.md)
- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- Verifique a página [Geração anterior](sizes-previous-gen.md) para Standard A, Dv1 (D1-4 e D11-14 v1) e série A8-A11



