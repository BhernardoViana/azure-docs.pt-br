---
title: 'Início Rápido: Criar uma primeira instância de Pesquisa Personalizada do Bing | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Use este artigo para criar uma instância personalizada do Bing que pode pesquisar os domínios e as páginas da Web que você definir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 6949824f598194456837544526223b823dcfc3e5
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273339"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Início Rápido: Criar a primeira instância da Pesquisa Personalizada do Bing

Para usar a Pesquisa Personalizada do Bing, você precisa criar uma instância de pesquisa personalizada que defina o modo de exibição ou a fatia da web. Essa instância conterá os domínios públicos, os sites e as páginas da Web que você deseja pesquisar, juntamente com os ajustes de classificação que possa querer. 

Para criar a instância, use o [portal da Pesquisa Personalizada do Bing](https://customsearch.ai). 

![Uma imagem do portal de Pesquisa Personalizada do Bing](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Criar uma instância de pesquisa personalizada

Para criar uma instância de Pesquisa Personalizada do Bing:

1. Clique em **Começar** na página da Web [portal de Pesquisa Personalizada do Bing](https://customsearch.ai) e entre com sua conta Microsoft.

2. Clique em **Nova Instância** e insira um nome descritivo. Você pode alterar o nome da sua instância a qualquer momento.
 
3. Na guia **Ativo** em **Experiência de Pesquisa**, insira a URL de um ou mais sites que você quer incluir em sua pesquisa. 

    > [!NOTE]
    > Instâncias de Pesquisa Personalizada do Bing retornarão apenas os resultados de domínios e páginas da Web públicos e indexados pelo Bing.

4. Você pode usar o lado direito do portal de Pesquisa Personalizada do Bing para inserir uma consulta e examinar os resultados da pesquisa retornados por sua instância de pesquisa. Se nenhum resultado for retornado, tente inserir uma URL diferente.  

5. Clique em **Publicar** para publicar suas alterações no ambiente de produção e atualizar os pontos de extremidade da instância.

6.  Clique na guia **Produção** e em **Pontos de Extremidade**, copie sua **ID de Configuração Personalizada**. Você precisará dessa ID para chamar a API de Pesquisa Personalizada acrescentando-a ao parâmetro de consulta `customconfig=` em suas chamadas.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: Chamar o ponto de extremidade da Pesquisa Personalizada do Bing](./call-endpoint-csharp.md)
