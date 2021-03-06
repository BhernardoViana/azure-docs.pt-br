---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 489ac7fa37c10a27de971151f0be35c647d2186f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116594"
---
As VMs (máquinas virtuais) otimizadas para HPC do Azure foram projetadas para fornecer desempenho de nível de liderança, escalabilidade MPI e eficiência de custo para uma variedade de aplicativos do mundo real.
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>Rede InfiniBand para HPC de grande escala
As VMs HBv2 apresentam 200 GB/s Mellanox HDR InfiniBand, enquanto as VMs HB e HC apresentam 100 GB/s Mellanox EDR InfiniBand. Cada um desses tipos de VM é conectado em uma árvore Fat sem bloqueio para desempenho de RDMA otimizado e consistente.

As VMs HBv2 dão suporte ao roteamento adaptável e ao transporte conectado dinâmico (DCT, em outros transportes Standard RC e UD). Esses recursos aprimoram o desempenho, a escalabilidade e a consistência do aplicativo e o uso deles é altamente recomendável.  

As VMs HBv2, HB e HC dão suporte a drivers Mellanox/OFED padrão. Assim, todos os verbos RDMA e tipos MPI têm suporte. Cada um desses tipos de VM também dá suporte ao descarregamento baseado em hardware de MPI coletivas para aumentar o desempenho do aplicativo.
 
As VMs da série H original apresentam 56 GB/s Mellanox FDR InfiniBand. Para aproveitar a interface InfiniBand na série H, os clientes devem implantar usando imagens oficialmente suportadas específicas para esse tipo de VM do Azure Marketplace. 


## <a name="hbv2-series"></a>Série HBv2
As VMs da série HBv2 são otimizadas para aplicativos orientados por largura de banda de memória, como dinâmica de fluidos, análise de elemento finito e simulação de reservatório. HBv2 VMs Feature 120 AMD EPYC 7742 núcleos de processador, 4 GB de RAM por núcleo de CPU e nenhum multithread simultâneo. Cada VM HBv2 fornece até 340 GB/s de largura de banda de memória e até 4 teraFLOPS de computação FP64. 

Armazenamento Premium: com suporte

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte a MPI | Armazenamento temporário (GB) | Discos de dados máximos | Máximo de NICs Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs | 120 | AMD EPYC 7742 | 480 | 350 | 2.45 | 2.45 | 3.4 | 200 | Todos | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>Série HB
As VMs de série HB são otimizadas para aplicativos orientados por largura de banda de memória, tais como dinâmica de fluidos, análise explícita de elementos limitados e modelagem de clima. HB VMs Feature 60 AMD EPYC 7551 núcleos de processador, 4 GB de RAM por núcleo de CPU e nenhum multithread simultâneo. Uma VM HB fornece até 260 GB/s de largura de banda de memória.  

ACU: 199-216

Armazenamento Premium: com suporte

Cache de Armazenamento Premium: com suporte

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte a MPI | Armazenamento temporário (GB) | Discos de dados máximos | Máximo de NICs Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2,0 | 2.55 | 2.55 | 100 | Todos | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>Série HC
As VMs da série HC são otimizadas para aplicativos orientados por computação densa, como análise implícita de elemento finito, Molecular Dynamics e química computacional. As VMs HC contam com 44 núcleos do processador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo de CPU e nenhum hyperthreading. A plataforma Intel Xeon Platinum dá suporte ao ecossistema avançado da Intel de ferramentas de software, como a Intel Math Kernel Library. 

ACU: 297-315

Armazenamento Premium: com suporte

Cache de Armazenamento Premium: com suporte


| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte a MPI | Armazenamento temporário (GB) | Discos de dados máximos | Máximo de NICs Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Todos | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>Série H
As VMs da série H são otimizadas para aplicativos orientados por altas frequências de CPU ou grandes requisitos de memória por núcleo. As VMs da série H apresentam 8 ou 16 núcleos de processador Intel Xeon E5 2667 v3, até 14 GB de RAM por núcleo de CPU e sem hyperthreading. Uma VM da série H fornece até 80 GB/s de largura de banda de memória.

ACU: 290-300

Armazenamento Premium: sem suporte

Cache de Armazenamento Premium: sem suporte

| Tamanho | vCPU | Processador | Memória (GB) | Largura de banda de memória GB/s | Frequência de CPU base (GHz) | Frequência de todos os núcleos (GHz, pico) | Frequência de núcleo único (GHz, pico) | Desempenho de RDMA (GB/s) | Suporte a MPI | Armazenamento temporário (GB) | Discos de dados máximos | Máximo de NICs Ethernet |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> para aplicativos MPI, a rede de back-end RDMA dedicada é habilitada pela rede InfiniBand FDR.

<br>
