---
title: Introdução ao Azure Advisor | Microsoft Docs
description: Introdução ao Azure Advisor.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: cd64515beabec43a5209d62dccf2b55b21702c16
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-azure-advisor"></a>Introdução ao Azure Advisor

Saiba como acessar o Assistente por meio do Portal do Azure e obter e implementar recomendações.

## <a name="get-advisor-recommendations"></a>Obter recomendações do Advisor

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No painel esquerdo, clique em **Assistente**.  Se você não visualizar o Assistente no painel esquerdo, clique em **Todos os serviços**.  No painel do menu de serviço, em **Monitoramento e Gerenciamento**, clique em **Assistente**.
 O painel Assistente é exibido.

   ![Acessar o Azure Advisor usando o Portal do Azure](./media/advisor-get-started/advisor-portal-menu.png) 

4. O painel Assistente exibirá um resumo das suas recomendações para todas as assinaturas selecionadas.  Você pode escolher as assinaturas para as quais você deseja que as recomendações sejam exibidas usando a lista suspensa de filtro de assinatura.

5. Para obter recomendações para uma categoria específica, clique em uma das guias: **Alta Disponibilidade**, **Segurança**, **Desempenho** ou **Custo**. 

  ![Painel do Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Obter detalhes da recomendação do Assistente e implementar uma recomendação

Você pode selecionar uma recomendação do Assistente para exibir detalhes adicionais – como as ações de recomendação e os recursos afetados – e para implementar a solução para a recomendação.  

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2. Selecione uma categoria de recomendação para exibir a lista de recomendações nessa categoria ou selecione a guia **Todas** para exibir todas as suas recomendações.

3. Clique em uma recomendação que você deseja examinar com detalhes.

4. Examine as informações sobre a recomendação e os recursos aos quais a recomendação se aplica.

5. Clique na **Ação Recomendada** para implementar a recomendação.

## <a name="filter-advisor-recommendations"></a>Filtrar recomendações do Assistente

Você pode filtrar as recomendações para fazer drill down até que é mais importante para você.  Você pode filtrar por assinatura, por tipo de recurso ou por status de recomendação.  

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2.  Use listas suspensas no painel do Assistente para filtrar por assinatura, por tipo de recurso ou por status de recomendação.

    ![Critérios do filtro de pesquisa do Assistente](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-advisor-recommendations"></a>Adiar ou ignorar as recomendações do Assistente

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2. Navegue até a recomendação que você deseja adiar ou ignorar.

3. Clique na recomendação.

4. Clique em **Adiar**. 

5. Especifique um período de tempo de adiamento ou selecione **Nunca** para ignorar a recomendação.

## <a name="exclude-subscriptions-or-resource-groups-from-advisor"></a>Excluir assinaturas ou grupos de recursos do Assistente

Você pode ter grupos de recursos ou assinaturas para os quais você não deseja receber recomendações do Assistente, assim como recursos de 'teste'.  Você pode configurar o Assistente para gerar recomendações apenas para grupos de recursos e assinaturas específicas.

> [!NOTE]
> Para incluir ou excluir uma assinatura ou grupo de recursos do Assistente, você deve ser um Proprietário de assinatura.  Se você não tiver as permissões necessárias para uma assinatura ou grupo de recursos, a opção para incluí-la ou excluí-la estará desabilitada na interface do usuário.

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2. Clique em **Configurar** na barra de ações.

3. Desmarque quaisquer assinaturas ou grupos de recursos para os quais você não deseja receber recomendações do Assistente.

    ![Exemplo de configuração de recursos do Assistente](./media/advisor-get-started/advisor-configure-resources.png)

4. Clique no botão **Aplicar**.

## <a name="configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation"></a>Configurar a regra de média de utilização da CPU para a recomendação de máquina virtual de baixa utilização

O Assistente monitora o uso da máquina virtual durante 14 dias e, depois, identifica as máquinas virtuais com baixa utilização. As máquinas virtuais cuja média de utilização da CPU é de 5% ou menos e cujo uso de rede é de 7 MB ou menos durante quatro ou mais dias são consideradas máquinas virtuais de baixa utilização.

Se você deseja ser mais agressivo na identificação de máquinas virtuais de baixo uso, você pode ajustar a regra de média de utilização da CPU para cálculo por assinatura.  A regra de média de utilização da CPU pode ser definida para 5%, 10%, 15% ou 20%.

> [!NOTE]
> Para ajustar a regra de média de utilização da CPU para identificar as máquinas virtuais de baixo uso, você deve ser um *Proprietário* da assinatura.  Se você não tiver as permissões necessárias para uma assinatura ou grupo de recursos, a opção para incluí-la ou excluí-la estará desabilitada na interface do usuário. 

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2. Clique em **Configurar** na barra de ações.

3. Clique na guia **Regras**.

4. Selecione as assinaturas cuja regra de média de utilização da CPU você gostaria de ajustar e, em seguida, clique em **Editar**.

5. Selecione o valor da média de utilização da CPU desejado e, em seguida, clique em **Aplicar**.

6. Clique em **Atualizar recomendações** para atualizar suas recomendações existentes para usar a nova regra de média de utilização da CPU. 

   ![Exemplo de configuração de regras de recomendação do Assistente](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-your-advisor-recommendations"></a>Baixar as recomendações do Assistente

O Assistente permite que você baixe um resumo das suas recomendações.  Você pode baixar suas recomendações como um arquivo PDF ou um arquivo CSV.  Baixar suas recomendações permite que você compartilhe os dados de recomendação facilmente com seus colegas ou execute sua própria análise sobre eles.

1. Entre no [Portal do Azure](https://portal.azure.com) e, em seguida, abra o [Assistente](https://aka.ms/azureadvisordashboard).

2. Clique em **Baixar como CSV** ou **Baixar como PDF** na barra de ações.

A opção de download respeita eventuais filtros que você tenha aplicado no painel do Assistente.  Se você selecionar a opção de download ao exibir uma categoria de recomendação específica ou uma recomendação, o resumo baixado só incluirá informações para essa categoria ou recomendação. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Assistente, consulte:
* [Introdução ao Azure Advisor](advisor-overview.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
-  [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de custo do Advisor](advisor-performance-recommendations.md)
