---
title: 'Tutorial: Moderar imagens de produto de comércio eletrônico – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Configure um aplicativo para analisar e classificar imagens de produto com rótulos especificados (usando a Pesquisa Visual Computacional e a Visão Personalizada do Azure) e marque imagens indesejáveis para serem examinadas posteriormente (usando o Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: pafarley
ms.openlocfilehash: 209fb3bba2b5462caad53d809c46eba0ebf4d836
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547891"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Tutorial: Moderar imagens de produto de comércio eletrônico com o Azure Content Moderator

Neste tutorial, você aprenderá a usar os Serviços Cognitivos do Azure, incluindo o Content Moderator, para classificar e moderar com eficiência imagens de produto em um cenário de comércio eletrônico. Você usará a Pesquisa Visual Computacional e a Visão Personalizada para aplicar várias marcas (rótulos) a imagens e, em seguida, criará uma análise de equipe, que combina as tecnologias baseadas em aprendizado de máquina do Content Moderator com as equipes de análise humana para fornecer um sistema de moderação inteligente.

Este tutorial mostra como:

> [!div class="checklist"]
> * Inscrever-se no Content Moderator e criar uma equipe de análise.
> * Use o API de imagem do Content Moderator para verificar possível conteúdo adulto e obsceno.
> * Usar o serviço de Pesquisa Visual Computacional para examinar se há algum conteúdo com celebridades (ou outras marcas detectáveis pela Pesquisa Visual Computacional).
> * Usar o Serviço de Visão Personalizada para examinar a presença de bandeiras, brinquedos e canetas (ou outras marcas personalizadas).
> * Apresentar os resultados do exame combinados para revisão humana e tomada de decisão final.

O código de exemplo completo está disponível no repositório [Amostras de Moderação de Catálogo de Comércio Eletrônico](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) no GitHub.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura do Content Moderator. Siga as instruções descritas em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço Content Moderator e obter sua chave.
- Uma chave de assinatura da Pesquisa Visual Computacional (mesmas instruções acima).
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/).
- Um conjunto de imagens para cada rótulo que será usado pelo classificador da Visão Personalizada (nesse caso, brinquedos, canetas e bandeiras dos EUA).

## <a name="create-a-review-team"></a>Criar uma equipe de análise

Veja o início rápido [Familiarize-se com o Content Moderator](quick-start.md) para obter instruções sobre como se inscrever na [ferramenta de Análise do Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e criar uma equipe de análise. Anote o valor da **ID da Equipe** na página **Credenciais**.

## <a name="create-custom-moderation-tags"></a>Criar marcas de moderação personalizadas

Em seguida, crie marcas personalizadas na ferramenta de Análise (veja o artigo [Marcas](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) caso precise obter ajuda com esse processo). Nesse caso, adicionaremos as seguintes marcas: **celebridade**, **EUA**, **bandeira**, **brinquedo** e **caneta**. Observe que nem todas as marcas precisam ser categorias detectáveis na Pesquisa Visual Computacional (como **celebridade**); você pode adicionar suas próprias marcas personalizadas, desde que treine o classificador da Visão Personalizada para detectá-las mais tarde.

![Configurar marcas personalizadas](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Criar um projeto do Visual Studio

1. No Visual Studio, abra o diálogo Novo Projeto. Expanda **Instalado**, **Visual C#** e, em seguida, selecione **Aplicativo de console (.NET Framework)**.
1. Nomeie o aplicativo **EcommerceModeration** e, em seguida, clique em **OK**.
1. Se estiver adicionando esse projeto a uma solução existente, selecione esse projeto como o único projeto de inicialização.

Este tutorial realçará o código fundamental para o projeto, mas não abordará todas as linhas de código necessárias. Copie o conteúdo completo de _Program.cs_ no projeto de exemplo ([Amostras de Moderação de Catálogo de Comércio Eletrônico](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) para o arquivo _Program.cs_ do novo projeto. Em seguida, siga as próximas seções para saber mais sobre como funciona o projeto e como usá-lo por conta própria.

## <a name="define-api-keys-and-endpoints"></a>Definir chaves de API e pontos de extremidade

Conforme mencionado acima, este tutorial usa três serviços cognitivos; portanto, ele exige três chaves e pontos de extremidade de API correspondentes. Confira os seguintes campos na classe **Program**: 

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Você precisará atualizar os campos `___Key` com os valores das chaves de assinatura (você obterá a `CustomVisionKey` posteriormente) e talvez precise alterar os campos `___Uri`, de modo que eles contenham os identificadores de região corretos. Preencha a parte `YOURTEAMID` do campo `ReviewUri` com a ID da equipe de análise criada anteriormente. Você preencherá a parte final do campo `CustomVisionUri` mais tarde.

## <a name="primary-method-calls"></a>Principais chamadas de método

Confira o código a seguir no método **Main**, que executa um loop por uma lista de URLs de imagem. Ele analisa cada imagem com os três serviços diferentes, registra as marcas aplicadas na matriz **ReviewTags** e, em seguida, cria uma análise para moderadores humanos (envia as imagens para a ferramenta de Análise do Content Moderator). Você explorará esses métodos nas seções a seguir. Observe que aqui, se desejar, você poderá controlar quais imagens são enviadas para análise, usando a matriz **ReviewTags** em uma instrução condicional para verificar quais marcas foram aplicadas.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Método EvaluateAdultRacy

Confira o método **EvaluateAdultRacy** na classe **Program**. Esse método usa uma URL de imagem e uma matriz de pares chave-valor como parâmetros. Ele chama uma API de Imagem do Content Moderator (usando a REST) para obter as pontuações de conteúdo para Adulto e conteúdo Sexual da imagem. Se a pontuação for maior que 0,4 (o intervalo é de 0 a 1), ele definirá o valor correspondente na matriz **ReviewTags** como **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecustomvisiontags-method"></a>Método EvaluateCustomVisionTags

O próximo método usa uma URL de imagem e suas informações de assinatura da Pesquisa Visual Computacional e analisa na imagem a presença de celebridades. Se uma ou mais celebridades forem encontradas, ele definirá o valor correspondente na matriz **ReviewTags** como **True**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Método EvaluateCustomVisionTags

Em seguida, confira o método **EvaluateCustomVisionTags**, que classifica os produtos reais – nesse caso, bandeiras, brinquedos e canetas. Siga as instruções do guia [Como criar um classificador](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) guia para criar seu próprio classificador de imagem personalizada, a fim de detectar a presença de bandeiras, brinquedos e canetas (ou qualquer coisa que você escolheu como marcas personalizadas) nas imagens.

![Página da Web da Visão Personalizada com imagens de treinamento de canetas, brinquedos e bandeiras](images/tutorial-ecommerce-custom-vision.PNG)

Depois de treinar o classificador, obtenha a chave de previsão e a URL de ponto de extremidade de previsão (confira [Obter a URL e a chave de previsão](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) caso precise de ajuda para recuperá-las) e atribua esses valores para aos campos `CustomVisionKey` e `CustomVisionUri`, respectivamente. O método usa esses valores para consultar o classificador. Se o classificador encontrar uma ou mais das marcas personalizadas na imagem, esse método definirá os valores correspondentes na matriz **ReviewTags** como **True**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Criar análises para a ferramenta de Análise

Nas seções anteriores, você observou os métodos que examinam se há conteúdo para adulto e conteúdo sexual nas imagens recebidas (Content Moderator), com celebridades (Pesquisa Visual Computacional) e vários outros objetos (Visão Personalizada). Em seguida, confira o método **CreateReview**, que carrega as imagens, com todas as suas marcas aplicadas (passadas como _Metadados_), à ferramenta de Análise do Content Moderator, de modo que possam estar disponíveis para análise humana. 

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

As imagens serão exibidas na guia Análise da [ferramenta de Análise do Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Captura de tela da ferramenta de Análise do Content Moderator com várias imagens e suas marcas realçadas](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Enviar uma lista de imagens de teste

Como você pode ver no método **Main**, esse programa procura um diretório "C:Test" com um arquivo _Urls.txt_ que contém uma lista de URLs de imagem. Crie um arquivo e um diretório desse tipo ou altere o caminho para que ele aponte para o arquivo de texto e popule esse arquivo com as URLs de imagens que deseja testar.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Execute o programa

Se você seguiu todas as etapas acima, o programa deve processar cada imagem (consultando em todos os três serviços suas marcas relevantes) e, em seguida, carregar as imagens com as informações de marca na ferramenta de Análise do Content Moderator.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurará um programa para analisar as imagens de produto com a finalidade de marcá-las por tipo de produto e permitir que uma equipe de revisão tome decisões informadas sobre a moderação de conteúdo. Em seguida, saiba mais sobre os detalhes da moderação de imagem.

> [!div class="nextstepaction"]
> [Examinar imagens moderadas](./review-tool-user-guide/review-moderated-images.md)