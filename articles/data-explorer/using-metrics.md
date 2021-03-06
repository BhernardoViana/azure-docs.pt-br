---
title: Monitorar o desempenho, a integridade e o uso do Data Explorer do Azure com métricas
description: Saiba como usar as métricas de Data Explorer do Azure para monitorar o desempenho, a integridade e o uso do cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: f5b47a5ae9d13711233d0e4852ec487af7344622
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173783"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitorar o desempenho, a integridade e o uso do Data Explorer do Azure com métricas

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real de grandes volumes de streaming de dados de aplicativos, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. As métricas de Data Explorer do Azure fornecem indicadores-chave para a integridade e o desempenho dos recursos de cluster. Use as métricas detalhadas neste artigo para monitorar a integridade e o desempenho do cluster Data Explorer do Azure em seu cenário específico como métricas autônomas. Você também pode usar métricas como a base para os [painéis operacionais do Azure](/azure/azure-portal/azure-portal-dashboards) e [alertas do Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/).

* Crie um [cluster e um banco de dados](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="using-metrics"></a>Usando métricas

No cluster Data Explorer do Azure, selecione **métricas** para abrir o painel métricas e iniciar a análise no cluster.

![Selecione Métricas](media/using-metrics/select-metrics.png)

No painel de métricas:

![Painel de métricas](media/using-metrics/metrics-pane.png)

1. Para criar um gráfico de métrica, selecione nome da **métrica** e **agregação** relevante por métrica, conforme detalhado abaixo. Os seletores de **namespace de métrica** e de **recurso** são previamente selecionados para o cluster de data Explorer do Azure.

    **Métrica** | **Unidade** | **Agregação** | **Descrição da métrica**
    |---|---|---|---|
    | Utilização de cache | Porcentagem | AVG, Max, min | Porcentagem de recursos de cache alocados atualmente em uso pelo cluster. O cache refere-se ao tamanho do SSD alocado para a atividade do usuário de acordo com a política de cache definida. Uma utilização média de cache de 80% ou menos é um Estado sustentável para um cluster. Se a utilização média do cache estiver acima de 80%, o cluster deverá ser [expandido](manage-cluster-vertical-scaling.md) para um tipo de preço otimizado para armazenamento ou [escalado](manage-cluster-horizontal-scaling.md) horizontalmente para mais instâncias. Como alternativa, adapte a política de cache (menos dias no cache). Se a utilização do cache for superior a 100%, o tamanho dos dados a serem armazenados em cache, de acordo com a política de cache, será maior que o tamanho total do cache no cluster. |
    | CPU | Porcentagem | AVG, Max, min | Porcentagem de recursos de computação alocados atualmente em uso por computadores no cluster. Uma CPU média de 80% ou menos é sustentável para um cluster. O valor máximo da CPU é 100%, o que significa que não há recursos de computação adicionais para processar dados. Quando um cluster não estiver com um bom desempenho, verifique o valor máximo da CPU para determinar se há CPUs específicas bloqueadas. |
    | Eventos processados (para hubs de eventos) | Contagem | Max, min, Sum | Número total de eventos lidos dos hubs de eventos e processados pelo cluster. Os eventos são divididos em eventos rejeitados e eventos aceitos pelo mecanismo de cluster. |
    | Latência de ingestão | Segundos | AVG, Max, min | Latência de dados ingeridos, desde o momento em que os dados foram recebidos no cluster até que ele esteja pronto para consulta. O período de latência de ingestão depende do cenário de ingestão. |
    | Resultado da ingestão | Contagem | Contagem | Número total de operações de ingestão que falharam e tiveram êxito. Use **aplicar divisão** para criar buckets de resultados de êxito e de falha e analisar as dimensões (**valor** > **status**).|
    | Utilização de ingestão | Porcentagem | AVG, Max, min | Porcentagem de recursos reais usados para ingerir dados do total de recursos alocados, na política de capacidade, para executar a ingestão. A política de capacidade padrão não tem mais de 512 operações simultâneas de ingestão ou 75% dos recursos de cluster investidos na ingestão. A utilização média de ingestão de 80% ou menos é um Estado sustentável para um cluster. O valor máximo de utilização de ingestão é 100%, o que significa que toda a capacidade de ingestão de clusters é usada e uma fila de ingestão pode resultar. |
    | Volume de ingestão (em MB) | Contagem | Max, min, Sum | O tamanho total dos dados ingeridos para o cluster (em MB) antes da compactação. |
    | Keep Alive | Contagem | média | Controla a capacidade de resposta do cluster. Um cluster totalmente responsivo retorna o valor 1 e um cluster bloqueado ou desconectado retorna 0. |
    | Duração da consulta | Segundos | Count, AVG, min, Max, Sum | Tempo total até que os resultados da consulta sejam recebidos (não inclui latência de rede). |
    | | | |

    Informações adicionais sobre as [métricas do cluster data Explorer do Azure com suporte](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Selecione o botão **Adicionar métrica** para ver várias métricas plotadas no mesmo gráfico.
3. Selecione o botão **+ novo gráfico** para ver vários gráficos em uma exibição.
4. Use o seletor de tempo para alterar o intervalo de tempo (padrão: últimas 24 horas).
5. Use [ **Adicionar filtro** e **aplicar divisão** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) para métricas que têm dimensões.
6. Selecione **fixar no painel** para adicionar a configuração do gráfico aos painéis para que você possa exibi-lo novamente.
7. Defina **nova regra de alerta** para visualizar suas métricas usando os critérios definidos. A nova regra de alerta incluirá seu recurso de destino, métrica, divisões e dimensões de filtro do seu gráfico. Modifique essas configurações no [painel criação de regra de alerta](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Informações adicionais sobre como usar o [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Próximas etapas

* [Tutorial: ingestão e consulta de dados de monitoramento no Azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Monitorar operações de ingestão de Data Explorer do Azure usando logs de diagnóstico](/azure/data-explorer/using-diagnostic-logs)
* [Guia de início rápido: Consultar dados no Data Explorer do Azure](web-query-data.md)
