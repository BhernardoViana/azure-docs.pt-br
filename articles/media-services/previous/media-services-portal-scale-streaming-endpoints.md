---
title: Dimensionar pontos de extremidade de streaming com o portal do Azure | Microsoft Docs
description: Este tutorial orienta você pelas etapas de dimensionar pontos de extremidade de streaming usando o portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: f2a14f2622d78a4222a8518172eb1ce8ed9e6637
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Dimensionar pontos de extremidade de streaming com o portal do Azure
## <a name="overview"></a>Visão geral

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Os pontos de extremidade de streaming **Premium** são adequados para cargas de trabalho avançadas, fornecendo capacidade de largura de banda escalonável e dedicada. Os clientes que têm um ponto de extremidade de streaming **Premium**, por padrão, obtêm uma US (Unidade de Streaming). O ponto de extremidade de streaming pode ser dimensionado adicionando USs. Cada SU fornece uma capacidade de largura de banda adicional para o aplicativo. Para saber mais sobre tipos de ponto de extremidade de streaming e configuração de CDN, confira o tópico [Visão geral do Ponto de Extremidade do Streaming](media-services-streaming-endpoints-overview.md).
 
Este tópico mostra como dimensionar um ponto de extremidade de streaming.

Para saber mais sobre os detalhes de preços, consulte [Detalhes de preços dos Serviços de Mídia](http://go.microsoft.com/fwlink/?LinkId=275107).

## <a name="scale-streaming-endpoints"></a>Dimensionar pontos de extremidade de streaming

Para alterar o número de unidades de streaming, faça o seguinte:

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia do Azure.
2. Na janela **Configurações**, selecione **Pontos de extremidade de streaming**.
3. Clique no ponto de extremidade de streaming que você deseja dimensionar. 

    > [!NOTE] 
    > É possível dimensionar apenas pontos de extremidade de streaming **Premium**.

4. Mova o controle deslizante para especificar o número de unidades de streaming.

    ![ponto de extremidade de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

