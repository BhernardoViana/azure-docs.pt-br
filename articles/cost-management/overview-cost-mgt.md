---
title: Visão geral do Gerenciamento de Custos do Azure | Microsoft Docs
description: O Azure Cost Management é uma solução de gerenciamento de custos que ajuda a monitorar e controlar os gastos do Azure e otimizar o uso de recursos.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/21/2018
ms.topic: overview
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 470802c1119c1c367dcd72f782dfc1036f75f6db
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035939"
---
# <a name="what-is-azure-cost-management"></a>O que é o Gerenciamento de Custo do Azure?

O gerenciamento de custos é o processo no qual você efetivamente planeja e controla os custos envolvidos em seus negócios. As tarefas de gerenciamento de custos são normalmente executadas por equipes de finanças, gerenciamento e aplicativos. O Azure Cost Management ajuda as organizações com o Microsoft Enterprise Agreements (EA) a planejar com o custo em mente. Também ajuda a analisar os custos de maneira eficaz e a tomar medidas para otimizar os gastos na nuvem. Para saber mais sobre como abordar o gerenciamento de custos como uma organização, consulte o artigo [práticas recomendadas do Azure Cost Management](cost-mgt-best-practices.md).

Embora relacionado, o faturamento difere do gerenciamento de custos. O faturamento é o processo de faturar os clientes por bens ou serviços e gerenciar o relacionamento comercial.  As equipes de suprimento e finanças geralmente realizam tarefas de faturamento.

O gerenciamento de custos mostra os padrões de custo e uso da organização com análises avançadas. Os relatórios no Gerenciamento de Custos mostram o custo, o uso, a instância reservada e o uso do Benefício Híbrido do Azure do Azure. Coletivamente, os relatórios mostram seus custos internos e externos de uso e os encargos do Microsoft Azure Marketplace. Outros encargos, como compras de reserva, suporte e impostos ainda não são mostrados nos relatórios. Os relatórios ajudam você a entender seus gastos e uso de recursos e podem ajudar a encontrar anomalias de gastos. A análise preditiva também está disponível. O Gerenciamento de Custos usa grupos de gerenciamento, orçamentos e recomendações do Azure para mostrar claramente como suas despesas são organizadas e como você pode reduzir custos.

Você pode usar o portal do Azure ou várias APIs para automação de exportação para integrar dados de custo com sistemas e processos externos. A exportação automática de dados de faturamento e relatórios agendados também estão disponíveis.

## <a name="plan-and-control-expenses"></a>Plano e controle de despesas

As formas como o Gerenciamento de Custos ajuda você a planejar e controlar seus custos incluem: Análise de custos, orçamentos, recomendações e exportação de dados de gerenciamento de custos.

Você usa a análise de custos para explorar e analisar seus custos organizacionais. Você pode visualizar os custos agregados por organização para entender onde os custos são acumulados e para identificar tendências de gastos. E você pode ver os custos acumulados ao longo do tempo para estimar tendências de custo mensais, trimestrais ou mesmo anuais em comparação a um orçamento.

Os orçamentos ajudam você a planejar e cumprir a responsabilidade financeira em sua organização. Eles ajudam a evitar que limites ou limites de custo sejam superados. Os orçamentos também podem ajudar você a informar os outros sobre seus gastos para gerenciar os custos de forma proativa. E com eles, você pode ver como os gastos progridem com o tempo.

As recomendações mostram como você pode otimizar e melhorar a eficiência identificando recursos ociosos e subutilizados. Ou, eles podem mostrar opções de recursos menos dispendiosas. Quando você age de acordo com as recomendações, você muda a maneira como usa seus recursos para economizar dinheiro. Para agir, você primeiro visualiza as recomendações de otimização de custos para visualizar possíveis ineficiências de uso. Em seguida, você age em uma recomendação para modificar o uso de recursos do Azure para uma opção mais econômica. Em seguida, você verifica a ação para garantir que a alteração realizada seja bem-sucedida.

Se você usar sistemas externos para acessar ou revisar dados de gerenciamento de custos, poderá exportar facilmente os dados do Azure. E você pode definir uma exportação agendada diariamente no formato CSV e armazenar os arquivos de dados no armazenamento do Azure. Então, você pode acessar os dados do seu sistema externo.

## <a name="consider-cloudyn"></a>Considere o Cloudyn

[Cloudyn](overview.md) é um serviço do Azure relacionado ao Gerenciamento de Custos. Com o Cloudyn, você pode rastrear o uso e os gastos da nuvem para os recursos do Azure. Ele também suporta outros provedores de nuvem, incluindo AWS e Google. Os relatórios do painel fáceis de entender ajudam com a alocação de custos e showbacks/estornos também. Atualmente, o Gerenciamento de Custos não suporta showback / chargeback ou outros provedores de serviços de nuvem. No entanto, o Cloudyn é uma opção que _dá_ suporte a eles. Atualmente, o Gerenciamento de Custos suporta apenas as contas do EA do Azure. Embora não ofereça suporte a contas individuais ou Pay-As-You-Go ou contas do Microsoft Cloud Service Provider, a Cloudyn faz isso. Se você tiver uma dessas contas, poderá usar o Cloudyn para ajudar a gerenciar seus custos.

## <a name="additional-azure-tools"></a>Ferramentas do Azure adicionais

O Azure tem outras ferramentas que não fazem parte do conjunto de recursos do Azure Cost Management. No entanto, eles têm um papel importante no processo de gerenciamento de custos. Para saber mais sobre essas ferramentas, consulte os links a seguir.

- [Calculadora de preço do Azure](https://azure.microsoft.com/pricing/calculator/): use essa ferramenta para estimar seus custos iniciais de nuvem.
- [Azure Migrate](../migrate/migrate-overview.md) - Avalie sua carga de trabalho atual do data center para obter insights sobre o que é necessário de uma solução de substituição do Azure.
- [Azure Advisor](../advisor/advisor-overview.md) - Identifique VMs não usadas e receba recomendações sobre compras de instâncias reservadas do Azure.
- [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) - Use suas licenças atuais do Windows Server ou do SQL Server no local para VMs no Azure para salvar.


## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com o Gerenciamento de custos, a próxima etapa é começar a usar o serviço.

- Comece a usar o Azure Cost Management para [analisar os custos](quick-acm-cost-analysis.md).
- Você também pode ler mais sobre [práticas recomendadas de gerenciamento de custos do Azure](cost-mgt-best-practices.md).
