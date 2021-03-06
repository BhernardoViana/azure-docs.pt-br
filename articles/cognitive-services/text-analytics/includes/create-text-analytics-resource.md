---
title: Criar um recurso de Análise de Texto de serviços cognitivas
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de Análise de Texto de Serviços Cognitivos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383417"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Criar um recurso de Análise de Texto de serviços cognitivas

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione **criar um recurso**e vá para **ia + Machine Learning** > **análise de texto**.
   Ou vá para [criar análise de texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |NOME|Insira um nome (2-64 caracteres).|
    |Assinatura|Selecione a assinatura apropriada.|
    |Local padrão|Selecione um local próximo.|
    |Tipo de preço| Insira **S**, o tipo de preço Standard.|
    |Grupo de recursos|Selecione um grupo de recursos disponível.|

1. Selecione **criar**e aguarde até que o recurso seja criado. Seu navegador redireciona automaticamente para a página de recursos recém-criada.
1. Coletar o `endpoint` configurado e uma chave de API:

    |Guia de recursos no portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Chaves**|Chave de API|Copie uma das duas chaves. É uma cadeia de caracteres alfanumérica de 32 caracteres sem espaços ou traços: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
