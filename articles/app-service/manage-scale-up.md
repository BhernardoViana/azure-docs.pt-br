---
title: Escalar verticalmente os recursos e as capacidades – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como escalar verticalmente um aplicativo no Serviço de Aplicativo do Azure a fim de adicionar capacidade e recursos.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/19/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: cebd6e5c120bdaa5e7dd5b681366e07087cd3498
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069185"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Escalar verticalmente um aplicativo no serviço Azure App

Este artigo mostra como dimensionar seu aplicativo no Serviço de Aplicativo do Azure. Há dois fluxos de trabalho para dimensionamento, escalar verticalmente e escalar horizontalmente, e este artigo explica o fluxo de trabalho de escala vertical.

* [Escalar verticalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenha mais CPU, memória, espaço em disco e recursos extras, como VMs (máquinas de virtuais) dedicadas, domínios personalizados e certificados, slots de preparo, dimensionamento automático e muito mais. Você escala verticalmente alterando o tipo de preço do plano do Serviço de Aplicativo ao qual seu aplicativo pertence.
* [Escalar horizontalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): aumenta o número de instâncias de VM que executam seu aplicativo.
  Você pode escalar horizontalmente até 20 instâncias, dependendo de seu tipo de preço. [Ambientes do Serviço de Aplicativo](environment/intro.md) no tipo de preço **Isolado** aumentam ainda mais sua contagem de expansão para 100 instâncias. Para saber mais sobre a escala horizontal, consulte [Escalar a contagem de instâncias manualmente ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md). Lá, você descobre como usar o dimensionamento automático, que serve para dimensionar automaticamente a contagem de instâncias com base em regras e agendamentos predefinidos.

As configurações de escala terão apenas alguns segundos para serem aplicadas e afetam todos os aplicativos em seu [plano do Serviço de Aplicativo](../app-service/overview-hosting-plans.md).
Elas não exigem a alteração do código ou a reimplantação do aplicativo.

Para obter informações sobre os preços e recursos de planos individuais do Serviço de Aplicativo, consulte [Detalhes de Preços dos Serviços de Aplicativo](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Antes de mudar um Plano do Serviço de Aplicativo do tipo **Gratuito** , é necessário remover os [limites de gastos](https://azure.microsoft.com/pricing/spending-limits/) em vigor para sua Assinatura do Azure. Para exibir ou alterar as opções de sua assinatura do serviço de aplicativo Microsoft Azure, consulte [Microsoft Azure assinaturas][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Escale verticalmente seu tipo de preço

> [!NOTE]
> Para expandir para o tipo de preço **PremiumV2**, consulte [Configurar o tipo de preço PremiumV2 para o Serviço de Aplicativo](app-service-configure-premium-tier.md).
>

1. No seu navegador, abra o [portal do Azure][portal].

1. Na página do aplicativo do serviço de aplicativo, no menu à esquerda, selecione **escalar verticalmente (plano do serviço de aplicativo)** .
   
3. Escolha sua camada e, em seguida, selecione **aplicar**. Selecione as diferentes categorias (por exemplo, **produção**) e **Veja também opções adicionais** para mostrar mais camadas.
   
    ![Navegue para escalar verticalmente seu aplicativo do Azure.][ChooseWHP]

    Quando a operação for concluída, você verá um pop-up de notificação com uma marca de seleção de êxito verde.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Escalar recursos relacionados
Se o seu aplicativo depender de outros serviços, como o Banco de Dados SQL do Azure ou o Armazenamento do Azure, você poderá expandir esses recursos separadamente. Esses recursos não são gerenciados pelo Plano do Serviço de Aplicativo.

1. Na página **visão geral** do seu aplicativo, selecione o link **grupo de recursos** .
   
    ![Escale verticalmente os recursos relacionados de seu aplicativo do Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Na parte **Resumo** da página **grupo de recursos** , selecione um recurso que você deseja dimensionar. A captura de tela a seguir mostra um recurso de banco de dados SQL.
   
    ![Navegue até a página do grupo de recursos para expandir seu aplicativo do Azure](./media/web-sites-scale/ResourceGroup.png)

    Para escalar verticalmente o recurso relacionado, consulte a documentação para o tipo de recurso específico. Por exemplo, para escalar verticalmente um único banco de dados SQL, consulte [dimensionar recursos de banco de dados único no banco de dados SQL do Azure](../sql-database/sql-database-single-database-scale.md). Para escalar verticalmente um recurso de banco de dados do Azure para MySQL, consulte [dimensionar recursos do MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparar tipos de preço

Para obter informações detalhadas, tais como os tamanhos de VM para cada tipo de preço, consulte [Detalhes de preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service).

Para uma tabela de limites de serviço, cotas, restrições e recursos com suporte em cada tipo de preço, consulte [Limites do Serviço de Aplicativo](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Mais recursos

Para assistir a vídeos sobre o dimensionamento de aplicativos do Serviço de Aplicativo, consulte os seguintes recursos:
 
* [Quando dimensionar Sites do Azure - com Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
* [Dimensionamento automático de Sites do Azure, CPU ou programado - com Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
* [Como dimensionar sites do Azure - com Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
