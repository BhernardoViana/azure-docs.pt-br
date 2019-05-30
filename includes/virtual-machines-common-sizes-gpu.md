---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 11/14/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: c849aadcd0fdc8f8ba071e3db5572e9e4c590e92
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391467"
---
Os tamanhos de VM otimizados para GPU são máquinas virtuais especializadas disponíveis com um ou vários GPUs NVIDIA. Esses tamanhos são projetados para cargas de trabalho de visualização e com muita computação e muitos gráficos. Este artigo fornece informações sobre o número e o tipo de GPUs, vCPUs, discos de dados e NICs. A taxa de transferência de armazenamento e a largura de banda de rede também são incluídos para cada tamanho neste agrupamento. 

* **Os tamanhos NC, NCv2, NCv3, ND e NDv2** são otimizados para aplicativos e algoritmos intensivos em computação e de uso intenso de rede. Alguns exemplos são aplicativos baseados em CUDA e OpenCL e simulações, AI e Aprendizagem Profunda. A série NCv3 concentra-se em cargas de trabalho de computação de alto desempenho com a tecnologia das GPUs Tesla V100 da NVIDIA.  A série ND concentra-se em cenários de treinamento e inferência para aprendizado profundo. Ela usa a GPU Tesla P40 da NVIDIA.

* A **série NC** apresenta um processador de 2,60 GHz Intel Xeon® E5-2690 v3.

* Os tamanhos **NCSv3**, **NCSv2** e **ND** apresentam um processador Intel Xeon® E5-2690 v4 de 2,60 GHz.
                      
* **NV e NVv3** tamanhos são otimizados e projetados para visualização remota, streaming, jogos, codificação e cenários de VDI usando estruturas como OpenGL e DirectX.  Essas VMs são apoiadas pela GPU Tesla M60 da NVIDIA.


## <a name="nc-series"></a>Série NC

Armazenamento Premium:  Sem suporte

Cache de armazenamento Premium:  Sem suporte

VMs da série NC têm a tecnologia do cartão [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf). Os usuários podem processar os dados mais rapidamente aproveitando o CUDA para aplicativos de exploração de energia, simulações de falhas, renderização de traçados de raio, aprendizado profundo e muito mais. A configuração NC24r fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas.


| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = metade de um cartão K80.

*Compatível com RDMA

## <a name="ncv2-series"></a>Série NCv2

Armazenamento Premium:  Com suporte

Cache de armazenamento Premium:  Com suporte

VMs da série NCv2 têm a tecnologia de GPUs [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/). Essas GPUs podem fornecer desempenho computacional 2 vezes maior que da série NC. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho HPC tradicionais como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Monte Carlo, entre outros. A configuração NC24rs v2 fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas.

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (principal) em sua assinatura vem inicialmente definida como 0 em cada região. [Solicitar um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | ---  | --- |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 64 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = um cartão P100.

*Compatível com RDMA

## <a name="ncv3-series"></a>Série NCv3

Armazenamento Premium:  Com suporte

Cache de armazenamento Premium:  Com suporte

VMs da série NCv3 têm a tecnologia de GPUs [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/). Essas GPUs podem fornecer desempenho computacional 1,5 vezes maior da série NCv2. Os clientes podem aproveitar essas GPUs atualizadas para cargas de trabalho HPC tradicionais como modelagem de reservatório, sequenciamento de DNA, análise de proteína, simulações de Monte Carlo, entre outros. A configuração NC24rs v3 fornece um adaptador de rede de alta taxa de transferência e baixa latência, otimizado para cargas de trabalho de computação paralela firmemente acopladas.

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (principal) em sua assinatura vem inicialmente definida como 0 em cada região. [Solicitar um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 64 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = um cartão V100.

*Compatível com RDMA

## <a name="ndv2-series-preview"></a>Série NDv2 (visualização)


Armazenamento Premium:  Com suporte

Cache de armazenamento Premium:  Com suporte

Infiniband: Sem suporte


A máquina virtual da série NDv2 é uma nova adição à família de GPUs projetada para as necessidades das cargas de trabalho de HPC, AI e aprendizado de máquina. Ele é alimentado por 8 GPUs NVIDIA Tesla V100 NVLINK interconectadas e 40 núcleos Intel Skylake e 672 GiB de memória do sistema. A instância NDv2 fornece excelente desempenho FP32 e FP64 para cargas de trabalho HPC e AI utilizando Cuda, TensorFlow, Pytorch, Caffe e outras estruturas.

[Inscreva-se e tenha acesso a essas máquinas durante a visualização](https://aka.ms/ndv2signup).
<br>


| Tamanho              | vCPU | GPU              | Memória  | NICs (Máx.) | Máx. tamanho do disco           | Máx. de discos de dados (1023 GB cada) | Largura de banda de rede máxima | 
|-------------------|------|------------------|---------|------------|--------------------------|--------------------------------|-----------------------|
| Standard_ND40s_v2 | 40   | 8 V100 (NVLink) | 672 GiB | 8          | Temporário 1344 / 2948XIO | 32                             | 24000 Mbps           |

## <a name="nd-series"></a>Série ND

Armazenamento Premium:  Com suporte

Cache de armazenamento Premium:  Com suporte

As máquinas virtuais da série ND são uma nova adição à família de GPU projetada para cargas de trabalho AI e Deep Learning. Elas oferecem um desempenho excelente para treinamento e Inferência. Instâncias de ND também têm a tecnologia das GPUs [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf). Essas instâncias oferecem um desempenho excelente para operações de ponto flutuante de precisão simples, para cargas de trabalho de AI que utilizam o Cognitive Toolkit, o TensorFlow, o Caffe e outras estruturas. A série ND também oferece um tamanho de memória de GPU muito maior (24 GB), permitindo usar modelos de rede neural muito maiores. Como a série NC, a série ND oferece uma configuração com uma baixa latência secundária, uma rede com alta taxa de transferência por meio de RDMA e a conectividade InfiniBand, permitindo executar trabalhos de grande escala que abrangem várias GPUs.

> [!IMPORTANT]
> Para essa família de tamanho, a cota de vCPU (principal) por região em sua assinatura vem inicialmente definida como 0. [Solicitar um aumento de cota de vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) para essa família em uma [região disponível](https://azure.microsoft.com/regions/services/).
>

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 24 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 48 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 96 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 96 | 32 | 8 |

1 GPU = um cartão de P40.

*Compatível com RDMA

## <a name="nv-series"></a>Série NV

Armazenamento Premium:  Sem suporte

Cache de armazenamento Premium:  Sem suporte

As máquinas virtuais da série NV têm a tecnologia das GPUs [ Tesla M60 da NVIDIA ](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e NVIDIA GRID para aplicativos acelerados de área de trabalho e áreas de trabalho virtuais, em que os clientes podem visualizar seus dados ou simulações. Os usuários podem visualizar seus fluxos de trabalho com uso intensivo de gráficos em instâncias NV para obter capacidade gráfica superior, além de executar cargas de trabalho de precisão única, como codificação e renderização. 

Cada GPU em instâncias NV vem com uma licença GRID. Esta licença oferece flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único usuário ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs | Estações de trabalho virtuais | Aplicativos virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de um cartão M60.

## <a name="nvv3-series-preview-sup1sup"></a>Série NVv3 (visualização) <sup>1</sup>

Armazenamento Premium:  Com suporte

Cache de armazenamento Premium:  Com suporte

As máquinas virtuais de série NVv3 são ativadas pelos [Tesla M60 da NVIDIA](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID tecnologia com Intel Broadwell CPUs. Essas máquinas virtuais são direcionadas para aplicativos com gráficos acelerados por GPU e áreas de trabalho virtuais em que os clientes querem visualizar dados, simular resultados para exibição, trabalhar no CAD ou renderizar e transmitir conteúdo por streaming. Além disso, essas máquinas virtuais podem executar cargas de trabalho de precisão simples, como codificação e renderização. As máquinas virtuais de NVv3 dão suporte ao armazenamento Premium e vêm com o dobro de memória do sistema (RAM) em relação ao seu predecessor série NV.  

Cada GPU em instâncias de NVv3 vem com uma licença GRID. Esta licença oferece flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único usuário ou 25 usuários simultâneos podem se conectar à VM para um cenário de aplicativo virtual.

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | GPU | Memória da GPU: GiB | Discos de dados máximos | Máximo de NICs | Estações de trabalho virtuais | Aplicativos virtuais | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 | 12 | 112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV24s_v3 | 24 | 224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV48s_v3 | 48 | 448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

1 GPU = metade de um cartão M60.

<sup>1</sup> da VM NVv3-series apresentam tecnologia Intel® Hyper-Threading

 
