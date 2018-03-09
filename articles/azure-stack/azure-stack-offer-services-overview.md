---
title: "Oferta de serviços na pilha do Azure | Microsoft Docs"
description: "Como um operador de nuvem, você pode oferecer serviços para seus usuários."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 5b117a9b174f5d2419ff596cc90436e4b9d21645
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Visão geral da oferta de serviços na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

[A pilha do Microsoft Azure](azure-stack-poc.md) é uma plataforma de nuvem híbrida que lhe permite oferecer serviços do data center. Como um provedor de serviço, você pode oferecer serviços para seus locatários. Em uma empresa ou órgão do governo, você pode oferecer serviços locais para seus funcionários. Os serviços que você pode entregar incluem, mas não estão limitados a:

- Plataforma como um serviço (PaaS) de serviços, como serviços de aplicativos, aplicativos de API, funções de API, SQL, MySQL.

Também é possível combinar os serviços para integrar e compilar soluções complexas para usuários diferentes.

Para fornecer esses serviços para seus usuários, você deve criar [planos, ofertas e cotas](azure-stack-plan-offer-quota-overview.md). Os usuários podem assinar, em seguida, suas ofertas para usar os serviços.

## <a name="plan-your-service-offers"></a>Planejar suas ofertas de serviço

Quando você estiver planejando suas ofertas, tenha os seguintes pontos em mente:

**Ofertas de avaliação**: você pode usar as ofertas de avaliação para atrair novos usuários, que podem, em seguida, atualizar para os serviços adicionais. Para criar uma oferta de avaliação, crie um pequeno [plano base](azure-stack-plan-offer-quota-overview.md#base-plan) com um plano de complemento maior opcional.

**Planejamento de capacidade**: você pode se preocupar sobre usuários que pegue grandes quantidades de recursos e a sobrecarga do sistema para todos os usuários. Para ajudar o desempenho, você pode [configurar seus planos com cotas](azure-stack-plan-offer-quota-overview.md#plans) para o uso de extremidade.

**Delegado provedores**: você pode conceder a outros usuários a capacidade de criar ofertas em seu ambiente. Por exemplo, se você for um provedor de serviço, você pode [delegar](azure-stack-delegated-provider.md) essa capacidade de seu revendedores. Ou, se você tiver uma organização, você pode delegar a outras divisões/subsidiárias.

## <a name="next-steps"></a>Próximas etapas
[Criar uma oferta na pilha do Azure](azure-stack-create-offer.md)

