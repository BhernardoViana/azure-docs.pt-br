---
title: Configurar a camada PremiumV2 para o Serviço de Aplicativo do Azure | Microsoft Docs
description: Aprenda a melhorar o desempenho para seu aplicativo Web, móvel e de API no Serviço de Aplicativo do Azure, escalonando para o novo tipo de preço PremiumV2.
keywords: serviço de aplicativo, serviço de aplicativo do azure, escala, escalonável, plano de serviço de aplicativo, custo de serviço de aplicativo
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 4c157ed905b7dc48c886b26987c164ef9a47f3c3
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714554"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configurar a camada PremiumV2 para o Serviço de Aplicativo do Azure

O novo tipo de preço **PremiumV2** fornece processadores mais rápidos, armazenamento SSD e dobra o índice memória/núcleo dos tipos de preço existentes. Com a vantagem de ter um melhor desempenho, você pode economizar dinheiro executando seus aplicativos em menos instâncias. Neste artigo, você aprenderá a criar um aplicativo na camada **PremiumV2** ou expandir para uma camada **PremiumV2**.

## <a name="prerequisites"></a>pré-requisitos

Para expandir um aplicativo Web para **PremiumV2**, você precisa ter um aplicativo Web no Serviço de Aplicativo do Azure que é executado em um tipo de preço inferior a **PremiumV2**.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilidade de PremiumV2

O tipo de preço PremiumV2 está atualmente disponível para o Serviço de Aplicativo somente em _Windows_. Contêineres do Linux ainda não têm suporte.

O PremiumV2 já está disponível na maioria das regiões do Azure e está em expansão. Para ver se ele está disponível em sua região, execute o comando da CLI do Azure a seguir no [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

Se você receber um erro durante a criação do aplicativo ou criação de Plano do Serviço de Aplicativo, **PremiumV2** provavelmente não estará disponível para sua região de escolha.

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Criar um aplicativo no tipo de preço PremiumV2

O tipo de preço de um aplicativo do Serviço de Aplicativo é definido no [Plano do Serviço de Aplicativo](azure-web-sites-web-hosting-plans-in-depth-overview.md) em que ele é executado. Você pode criar um Plano do Serviço de Aplicativo individualmente ou como parte da criação de um aplicativo Web.

Ao configurar o Plano do Serviço de Aplicativo no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, selecione **Tipo de preço**. 

Selecione **produção**, em seguida, selecione **P1V2**, **P2V2**, ou **P3V2**, em seguida, clique em **aplicar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se você não vê **P1V2**, **P2V2** e **P3V2** como opções, isso significa que **PremiumV2** não está disponível em sua região de preferência ou que você está configurando um Plano do Serviço de Aplicativo do Linux, que não dá suporte ao **PremiumV2**.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Expandir um aplicativo existente para o tipo de preço PremiumV2

Antes de escalonar um aplicativo existente para o tipo de preço **PremiumV2**, verifique se **PremiumV2** está disponível em sua região. Para obter informações, consulte [Disponibilidade do PremiumV2](#availability). Se ele não estiver disponível em sua região, veja [Expandir de uma região sem suporte](#unsupported).

Dependendo do seu ambiente de hospedagem, a expansão poderá exigir etapas adicionais. 

No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, abra a página do seu aplicativo do Serviço de Aplicativo.

No painel de navegação à esquerda da página do seu aplicativo do Serviço de Aplicativo, selecione **Expandir (Plano do Serviço de Aplicativo)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecione **produção**, em seguida, selecione **P1V2**, **P2V2**, ou **P3V2**, em seguida, clique em **aplicar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se a operação for concluída com êxito, a página de visão geral do aplicativo mostrará que ele está no tipo de preço **PremiumV2**.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se você obtiver um erro

Alguns planos do Serviço de Aplicativo não podem expandir para o tipo de preço PremiumV2. Se a operação de expansão apresentar um erro, será necessário um novo Plano do Serviço de Aplicativo para seu aplicativo.

Crie um Plano do Serviço de Aplicativo do _Windows_ no mesmo grupo de recursos e região que seu aplicativo do Serviço de Aplicativo existente. Siga as etapas em [Criar um aplicativo no tipo de preço PremiumV2](#create) para defini-lo no tipo de preço **PremiumV2**. Se quiser, use a mesma configuração de expansão de seu plano do Serviço de Aplicativo existente (número de instâncias, dimensionamento automático e assim por diante).

Abra sua página de aplicativo do Serviço de Aplicativo novamente. No painel de navegação à esquerda do seu Serviço de Aplicativo, selecione **Alterar Plano do Serviço de Aplicativo**.

![](media/app-service-configure-premium-tier/change-plan.png)

Selecione o plano do Serviço de Aplicativo que você criou.

![](media/app-service-configure-premium-tier/select-plan.png)

Quando a operação de alteração for concluída, o aplicativo estará sendo executado no tipo de preço **PremiumV2**.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>Expandir de uma região sem suporte

Se o aplicativo for executado em uma região em que **PremiumV2** ainda não estiver disponível, você poderá mover o aplicativo para uma região diferente para aproveitar os benefícios do **PremiumV2**. Você tem duas opções:

- Crie um aplicativo no novo plano **PremiumV2** e então reimplante o código do aplicativo. Siga as etapas em [Criar um aplicativo no tipo de preço PremiumV2](#create) para defini-lo no tipo de preço **PremiumV2**. Se desejado, use a mesma configuração de expansão do seu Plano do Serviço de Aplicativo existente (número de instâncias, dimensionamento automático e assim por diante).
- Se seu aplicativo já é executado em uma camada **Premium** existente, você pode clonar o seu aplicativo com todas as configurações do aplicativo, cadeias de conexão e configuração de implantação.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Na página **Clonar aplicativo**, você pode criar um plano do Serviço de Aplicativo na região desejada e especificar as configurações que deseja clonar.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar a criação do aplicativo no tipo de preço **PremiumV2** com scripts, usando a [CLI do Azure](/cli/azure/install-azure-cli) ou o [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O comando a seguir cria um Plano de Serviço de Aplicativo em _P1V2_. Você pode executá-lo no Cloud Shell. As opções para `--sku` são P1V2, _P2V2_ e _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

O comando a seguir cria um Plano de Serviço de Aplicativo em _P1V2_. As opções para `-WorkerSize` são _Pequeno_, _Médio_ e _Grande_.

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Mais recursos

[Expandir um aplicativo no Azure](web-sites-scale.md)  
[Escalonar a contagem de instâncias manualmente ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md)