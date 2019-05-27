---
title: Quais são os portões programáveis em campo FPGA (arranjo)
titleSuffix: Azure Machine Learning service
description: Saiba como acelerar modelos e redes neurais profundas com FPGAs no Azure. Este artigo fornece uma introdução à FPGA (matrizes de porta programáveis no campo) e como o Serviço do Azure Machine Learning fornece IA (inteligência artificial) em tempo real quando você implanta seu modelo em uma FPGA do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 1a690ea350ea98589e9134cd6f401c6ac3c58083
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851019"
---
# <a name="what-are-field-programmable-gate-arrays-fpga"></a>Quais são os portões programáveis em campo FPGA (arranjo)

Este artigo fornece uma introdução à FPGA (matrizes de porta programáveis no campo) e como o Serviço do Azure Machine Learning fornece IA (inteligência artificial) em tempo real quando você implanta seu modelo em uma FPGA do Azure.

As FPGAs contêm uma matriz de blocos lógicos programáveis e uma hierarquia de interconexões reconfiguráveis. As interconexões permitem que esses blocos sejam configurados de várias maneiras após a fabricação. Comparado a outros chips, as FPGAs fornecem uma combinação de programação e desempenho.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. CPU, GPU e ASIC

O diagrama e a tabela a seguir mostram uma comparação das FPGAs com outros processadores.

![Diagrama de comparação da FPGA do Serviço do Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processador||Descrição|
|---|:-------:|------|
|Circuitos integrados específicos do aplicativo|ASICs|Circuitos personalizados, como as Unidades de Processador (TPU) do TensorFlow da Google, fornecem a maior eficiência. Elas não podem ser reconfiguradas conforme suas necessidades mudam.|
|Matrizes de portas programáveis em campo|FPGAs|As FPGAs, como aquelas disponíveis no Azure, fornecem desempenho aproximado ao dos ASICs. Eles também são flexíveis e reconfiguráveis ao longo do tempo, a fim de implementar a nova lógica.|
|Unidades de processamento gráfico|GPUs|Uma opção popular para cálculos de IA. As GPUs oferecem funcionalidades de processamento paralelo, tornando-as mais rápidas na renderização de imagens comparado às CPUs.|
|Unidades de processamento central|CPUs|Processadores de uso geral cujo desempenho não é ideal para processamento de elementos gráficos e vídeo.|

FPGAs no Azure são baseados em dispositivos FPGA da Intel, que os cientistas e desenvolvedores usam para acelerar os cálculos de inteligência Artificial em tempo real. Essa arquitetura habilitada para FPGA oferece desempenho, flexibilidade e escala e está disponível no Azure.

FPGAs possibilitam a atingir as solicitações de baixa latência para inferência de tipos em tempo real (ou o modelo de pontuação). As solicitações assíncronas (envio em lote) não são necessárias. O envio em lote pode causar latência, porque mais dados precisam ser processados. Implementações de unidades de processamento neural não exigem o envio em lote; Portanto, a latência pode ser muitas vezes menor, em comparação com processadores de CPU e GPU.

### <a name="reconfigurable-power"></a>Potência reconfigurável
Reconfigure as FPGAs para diferentes tipos de modelos de machine learning. Essa flexibilidade torna mais fácil acelerar os aplicativos baseados na precisão numérica mais ideal e o modelo de memória que está sendo usado. Como as FPGAs são reconfiguráveis, você pode ficar atualizado com os requisitos dos algoritmos de IA em rápida mudança.

### <a name="whats-supported-on-azure"></a>O que tem suporte no Azure
O Microsoft Azure é o maior investimento de nuvem do mundo em FPGAs. FPGAs no Azure dá suporte a:

+ Cenários de classificação e reconhecimento de imagens
+ Implantação do TensorFlow
+ DNNs: ResNet 50, ResNet 152, VGG-16, SSD-VGG e DenseNet-121
+ Hardware de FPGA da Intel 

Usando essa arquitetura de hardware habilitado para FPGA, as redes neurais treinadas são executadas rapidamente e com uma latência mais baixa. Azure pode paralelizar previamente treinada de redes neurais profundas (DNN) em FPGAs para escalar horizontalmente seu serviço. As DNNs podem ser pré-treinadas, como um transformador de recursos profundo para transferência de aprendizado, ou ajustadas com pesos atualizados.

### <a name="scenarios-and-applications"></a>Cenários e aplicativos

FPGAs do Azure são integrados com o Azure Machine Learning. A Microsoft usa FPGAs para avaliação de DNN, classificação da Pesquisa do Bing e aceleração de SDN (rede definida por software) para reduzir a latência durante a liberação de CPUs para outras tarefas.

Os cenários a seguir usam FPGAs:
+ [Sistema automatizado de inspeção ótica](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapeamento de cobertura de terra](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Realizar a implantação em FPGAs no Azure

Para criar um serviço de reconhecimento de imagem no Azure, use DNNs compatíveis como um recurso para implantação em FPGAs do Azure:

1. Use o [SDK do Azure Machine Learning para Python](https://aka.ms/aml-sdk) para criar uma definição de serviço. Uma definição de serviço é um arquivo que descreve um pipeline de grafos (entrada, recursos e classificador) com base no TensorFlow. O comando de implantação compacta automaticamente a definição e os grafos em um arquivo zip e carrega o zip no Armazenamento de Blobs do Azure. A DNN já foi implantada para executar em FPGA.

1. Registre o modelo usando o SDK com o arquivo zip no Armazenamento de Blobs do Azure.

1. Implante o serviço com o modelo registrado usando o SDK.

Para começar a implantar modelos DNN treinados em FPGAs na nuvem do Azure, confira [Implantar um modelo como um serviço Web em uma FPGA](how-to-deploy-fpga-web-service.md).


## <a name="next-steps"></a>Próximas etapas

Confira estes vídeos e blogs:

+ [Hardware da Hiperescala: ML em escala no Azure + FPGA: Build 2018 (vídeo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [Por dentro da nuvem configurável baseada em FPGA da Microsoft (vídeo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Projeto Brainwave para IA em tempo real: home page do projeto](https://www.microsoft.com/research/project/project-brainwave/)
