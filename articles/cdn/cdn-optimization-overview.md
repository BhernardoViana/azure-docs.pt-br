---
title: Otimizar a distribuição de conteúdo do Azure para seu cenário
description: Como otimizar a distribuição de conteúdo para cenários específicos
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: rli
ms.openlocfilehash: de748f7fa33b0250b1572dd5ae55cddf15003a0e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="optimize-azure-content-delivery-for-your-scenario"></a>Otimizar a distribuição de conteúdo do Azure para seu cenário

Quando você distribui conteúdo para um público global grande, é essencial garantir a distribuição otimizada do conteúdo. A Rede de Distribuição de Conteúdo (CDN) do Azure pode otimizar a experiência de distribuição de acordo com o tipo de conteúdo existente. O conteúdo pode ser um site, uma transmissão ao vivo, um vídeo ou um arquivo grande para download. Quando você cria um ponto de extremidade CDN, você especifica um cenário na opção **Otimizado para**. Sua escolha determina qual otimização é aplicada ao conteúdo distribuído do ponto de extremidade CDN.

As opções de otimização foram projetadas para usar os comportamentos de melhores práticas, a fim de melhorar o desempenho de distribuição de conteúdo e obter um melhor descarregamento da origem. Suas escolhas de cenário afetam o desempenho modificando as configurações de cache parcial, o agrupamento de objeto e a política de repetição de falha da origem. 

Este artigo fornece uma visão geral dos diversos recursos de otimização e quando eles devem ser usados. Para obter mais informações sobre os recursos e as limitações, consulte os respectivos artigos em cada tipo de otimização individual.

> [!NOTE]
> As opções **Otimizado para** podem variar de acordo com o provedor selecionado. Os provedores de CDN aplicam a melhoria de maneiras diferentes, dependendo do cenário. 

## <a name="provider-options"></a>Opções de provedor

**A CDN do Azure Standard da Microsoft** é compatível com as otimizações a seguir:

* [Entrega na Web geral](#general-web-delivery). Essa otimização também é usada para streaming de mídia e download de arquivos grandes.


**A CDN Standard do Azure da Verizon** e a **CDN Premium do Azure da Verizon** são compatíveis com as otimizações a seguir:

* [Entrega na Web geral](#general-web-delivery). Essa otimização também é usada para streaming de mídia e download de arquivos grandes.

* [Aceleração de site dinâmica](#dynamic-site-acceleration) 


**A CDN do Azure Standard da Akamai** é compatível com as otimizações a seguir:

* [Entrega na Web geral ](#general-web-delivery) 

* [Streaming de mídia geral](#general-media-streaming)

* [Streaming de mídia de vídeo sob demanda](#video-on-demand-media-streaming)

* [Download de arquivo grande](#large-file-download)

* [Aceleração de site dinâmica](#dynamic-site-acceleration) 

A Microsoft recomenda testar as variações de desempenho entre diferentes provedores para escolher o provedor ideal para a distribuição.

## <a name="select-and-configure-optimization-types"></a>Selecionar e configurar os tipos de otimização

Para criar um novo ponto de extremidade, selecione um tipo de otimização que melhor corresponda ao cenário e ao tipo de conteúdo que você deseja que seja distribuído pelo ponto de extremidade. **Distribuição na Web geral** é a seleção padrão. Para pontos de extremidade existentes da **Rede de Distribuição de Conteúdo do Azure da Akamai**, é possível atualizar a opção de otimização a qualquer momento. Essa alteração não interrompe a distribuição da CDN do Azure. 

1. Em um perfil da **CDN Standard do Azure da Akamai**, selecione um ponto de extremidade.

    ![Seleção de ponto de extremidade ](./media/cdn-optimization-overview/01_Akamai.png)

2. Em CONFIGURAÇÕES, selecione **Otimização**. Em seguida, selecione um tipo na lista suspensa **Otimizado para**.

    ![Otimização e seleção de tipo](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Otimização para cenários específicos

Você pode otimizar o ponto de extremidade CDN para um desses cenários. 

### <a name="general-web-delivery"></a>Distribuição na Web geral

A distribuição na Web geral é a opção mais comum de otimização. Ela foi projetada para otimização de conteúdo da Web geral, como páginas da Web e aplicativos Web. Essa otimização também pode ser usada para downloads de arquivo e vídeo.

Um site típico contém conteúdo estático e dinâmico. O conteúdo estático inclui imagens, bibliotecas JavaScript e folhas de estilo que podem ser armazenadas em cache e distribuídas para diferentes usuários. O conteúdo dinâmico é personalizado para um usuário individual, como itens de notícias adaptados a um perfil do usuário. O conteúdo dinâmico não é armazenado em cache, pois é exclusivo para cada usuário, como o conteúdo do carrinho de compras. A distribuição na Web geral pode otimizar todo o site. 

> [!NOTE]
> Se você usar a **CDN Standard do Azure da Akamai**, talvez deseje usar essa otimização caso o tamanho médio do arquivo for menor que 10 MB. Se o tamanho médio do arquivo for maior que 10 MB, selecione **Download de arquivos grandes** na lista suspensa **Otimizado para**.

### <a name="general-media-streaming"></a>Streaming de mídia geral

Se você precisar usar o ponto de extremidade para transmissão ao vivo e streaming de vídeo por demanda, recomendamos a otimização de streaming de mídia geral.

O streaming de mídia é sensível ao tempo, pois os pacotes que chegam tarde no cliente podem causar uma experiência de exibição degradada, como buffer frequente do conteúdo de vídeo. A otimização de streaming de mídia reduz a latência da distribuição de conteúdo de mídia e fornece uma experiência de streaming suave para os usuários. 

Esse cenário é comum para clientes do serviço de mídia do Azure. Quando você usa os serviços de mídia do Azure, você pode obter um ponto de extremidade de streaming, que pode ser usado para streaming sob demanda e ao vivo. Neste cenário, os clientes não precisam alternar para outro ponto de extremidade quando alteram de streaming sob demanda para ao vivo. A otimização de streaming de mídia geral dá suporte a esse tipo de cenário.

Para **a CDN Standard do Azure da Microsoft**, **a CDN Standard do Azure da Verizon**, e  **CDN Standard do Azure da Verizon**, use o tipo de otimização de entrega da web geral para entregar conteúdo de mídia de streaming geral.

Para obter mais informações sobre a otimização de streaming de mídia, consulte [Otimização de streaming de mídia](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Streaming de mídia de vídeo por demanda

A otimização de streaming de mídia de vídeo por demanda melhora o conteúdo de streaming de vídeo por demanda. Se você usar um ponto de extremidade para streaming de vídeo por demanda, talvez você deseje usar essa opção.

Para **a CDN Standard do Azure da Microsoft**, **a CDN Standard do Azure da Verizon**, e  **CDN Standard do Azure da Verizon**, use o tipo de otimização de entrega da web geral para entregar conteúdo de mídia de vídeo sob demanda.

Para obter mais informações sobre a otimização de streaming de mídia, consulte [Otimização de streaming de mídia](cdn-media-streaming-optimization.md).

> [!NOTE]
> Se o ponto de extremidade da CDN fornecer principalmente o conteúdo de vídeo por demanda, use esse tipo de otimização. A principal diferença entre essa otimização e a otimização de streaming de mídia geral é o tempo limite de repetição de conexão. O tempo limite é muito menor para trabalhar com cenários de transmissão ao vivo.

### <a name="large-file-download"></a>Download de arquivos grandes

Para a **CDN Standard do Azure da Akamai**, os downloads de arquivos grandes são otimizados para conteúdo de mais de 10 MB. Se o tamanho médio do arquivo for menor que 10 MB, use uma entrega de web geral. Se os tamanhos médios dos arquivos forem consistentemente maiores que 10 MB, talvez seja mais eficiente criar um ponto de extremidade separado para arquivos grandes. Por exemplo, atualizações de firmware ou software geralmente são arquivos grandes. Para entregar arquivos maiores do que 1,8 GB, a otimização de download de arquivo grande é necessária.

Para **a CDN Standard do Azure da Microsoft**, **a CDN Standard do Azure da Verizon**, e  **CDN Standard do Azure da Verizon**, use o tipo de otimização de entrega da web geral para entregar conteúdo de download de arquivo grande. Não há nenhuma limitação no tamanho de download do arquivo.

Para saber mais sobre a otimização de arquivos grandes, consulte [Otimização de arquivos grandes](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Aceleração de site dinâmica

 Aceleração de site dinâmico está disponível para os perfis **CDN Standard do Azure da Akamai**, **CDN Standard do Az da Verizon**, e **CDN Premium da Verizon**. Essa otimização envolve um valor adicional para ser usado; para obter mais informações, consulte [Preço da Rede de Distribuição de Conteúdo](https://azure.microsoft.com/pricing/details/cdn/).

A aceleração de site dinâmica inclui várias técnicas que trazem vantagens para a latência e o desempenho do conteúdo dinâmico. As técnicas incluem otimização de rota e de rede, otimização de TCP e muito mais. 

Use essa otimização para acelerar um aplicativo Web que inclui várias respostas que não são armazenáveis em cache. Entre os exemplos estão resultados da pesquisa, transações de check-out ou dados em tempo real. Continue usando as principais funcionalidades de cache CDN para dados estáticos. 

Para obter mais informações sobre aceleração de site dinâmico, consulte [Aceleração de site dinâmico](cdn-dynamic-site-acceleration.md).



