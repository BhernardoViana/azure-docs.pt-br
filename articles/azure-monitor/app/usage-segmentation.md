---
title: Análise de usuário, sessão e evento no Aplicativo Azure insights
description: Análise demográfica dos usuários de seu aplicativo Web.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 88dccd3a33c4a6e64de30ef7e7df4723a17f0c44
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930627"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Análise de usuários, sessões e eventos no Application Insights

Descubra quando as pessoas usam seu aplicativo Web, em quais páginas elas estão mais interessadas, onde os usuários estão localizados e quais navegadores e sistemas operacionais eles usam. Analisar a telemetria de negócios e de uso com o [Application Insights do Azure](../../azure-monitor/app/app-insights-overview.md).

![Captura de tela de Usuários do Application Insights](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Comece agora

Caso ainda não veja dados nas folhas de usuários, sessões ou eventos no portal do Application Insights, [veja como começar a usar as ferramentas de uso](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>A ferramenta de segmentação de Usuários, Sessões e Eventos

Três das folhas de uso usam a mesma ferramenta para dividir a telemetria do seu aplicativo Web em três perspectivas diferentes. Filtrando e dividindo os dados, você pode descobrir informações sobre o uso relativo de diferentes páginas e recursos.

* **Ferramenta de Usuários**: quantas pessoas usaram seu aplicativo e seus recursos.  Os usuários são contados usando IDs anônimas armazenadas em cookies do navegador. Uma única pessoa que usar diferentes navegadores ou computadores será contada como mais de um usuário.
* **Ferramenta de Sessões**: quantas sessões de atividade do usuário incluíram determinadas páginas e recursos de seu aplicativo. Uma sessão é contada após meia hora de inatividade do usuário ou após 24 horas contínuas de uso.
* **Ferramenta de Eventos**: com que frequência determinadas páginas e recursos de seu aplicativo são usados. Uma exibição de página é contada quando um navegador carrega uma página do seu aplicativo, desde que você a tenha [instrumentado](../../azure-monitor/app/javascript.md). 

    Um evento personalizado representa uma ocorrência de algo que esteja acontecendo em seu aplicativo, geralmente uma interação do usuário, como um clique de botão ou a conclusão de uma tarefa. Insira o código em seu aplicativo para [gerar eventos personalizados](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Consultas a determinados usuários

Explore diferentes grupos de usuários, ajustando as opções de consulta na parte superior da ferramenta de Usuários:

* Mostrar: Escolha um coorte de usuários para analisar.
* Quem usou: escolha exibições de página e eventos personalizados.
* Durante: escolha um intervalo de tempo.
* Por: escolha como compartimentar os dados, seja segundo um período ou segundo outra propriedade, como navegador ou cidade.
* Dividido por: escolha uma propriedade segundo a qual o segmento ou os dados deverão ser divididos. 
* Adicionar filtros: limite a consulta a determinados usuários, sessões ou eventos com base em suas propriedades, como navegador ou cidade. 
 
## <a name="saving-and-sharing-reports"></a>Salvar e compartilhar relatórios 
Você pode salvar relatórios de Usuários, de forma privada na seção Meus Relatórios ou de forma compartilhada com quem tiver acesso a esse recurso do Application Insights na seção Relatórios Compartilhados.

Para compartilhar um link para um relatório de Usuários, Sessões ou Eventos; clique em **Compartilhar** na barra de ferramentas e, em seguida, copie o link.

Para compartilhar uma cópia dos dados em um relatório de Usuários, Sessões ou Eventos; clique em **Compartilhar** na barra de ferramentas e, em seguida, clique no **ícone do Word** para criar um documento do Word com os dados. Ou então, clique no **ícone do Word** acima do gráfico principal.

## <a name="meet-your-users"></a>Conheça seus usuários

A seção **Conheça seus usuários** mostra informações sobre cinco usuários de exemplo que correspondem à consulta atual. Considerar e explorar os comportamentos de indivíduos, além de agregados, pode fornecer informações sobre como as pessoas realmente usam seu aplicativo.

## <a name="next-steps"></a>Próximos passos

- Para habilitar as experiências de uso, comece enviando [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já envia eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários utilizam o seu serviço.
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Fluxos de Usuário](usage-flows.md)
    - [Pastas de trabalho](../../azure-monitor/app/usage-workbooks.md)
    - [Adicionar contexto de usuário](usage-send-user-context.md)