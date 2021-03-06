---
title: Métricas de Azure Monitor para o gateway de aplicativo
description: Saiba como usar métricas para monitorar o desempenho do gateway de aplicativo
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: f0937ee53e66cb1bf0c5d6b55a8dde045570e924
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309824"
---
# <a name="metrics-for-application-gateway"></a>Métricas para o gateway de aplicativo

O gateway de aplicativo publica pontos de dados, chamados métricas, para [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para o desempenho do seu gateway de aplicativo e instâncias de back-end. Essas métricas são valores numéricos em um conjunto ordenado de dados de série temporal que descrevem algum aspecto do seu gateway de aplicativo em um determinado momento. Se houver solicitações que fluem pelo gateway de aplicativo, ele medirá e enviará suas métricas em intervalos de 60 segundos. Se não houver nenhuma solicitação fluindo pelo gateway de aplicativo ou nenhum dado para uma métrica, a métrica não será relatada. Para obter mais informações, consulte [Métricas do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v2

### <a name="timing-metrics"></a>Métricas de tempo

As métricas a seguir relacionadas ao tempo da solicitação e resposta estão disponíveis. Ao analisar essas métricas, você pode determinar se a lentidão no aplicativo ocorre devido à WAN, ao gateway de aplicativo, à rede entre o gateway de aplicativo e o back-end ou o desempenho do aplicativo.

- **RTT do cliente**

  Tempo médio de ida e volta entre clientes e o gateway de aplicativo. Essa métrica indica quanto tempo leva para estabelecer conexões e retornar confirmações.

- **Tempo total do gateway de aplicativo**

  Tempo médio que leva para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como a média do intervalo desde o momento em que o gateway de aplicativo recebe o primeiro byte de uma solicitação HTTP até o momento em que a operação de envio de resposta é concluída. É importante observar que isso geralmente inclui o tempo de processamento do gateway de aplicativo, o tempo que os pacotes de solicitação e resposta estão viajando pela rede e o tempo que o servidor back-end levou para responder.

- **Tempo de conexão de back-end**

  Tempo gasto estabelecendo uma conexão com um servidor de back-end. 

- **Tempo de resposta do primeiro byte do back-end**

  Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor back-end e o recebimento do primeiro byte do cabeçalho de resposta, aproximar tempo de processamento do servidor back-end

- **Tempo de resposta do último byte do back-end**

  Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do último byte do corpo da resposta

### <a name="application-gateway-metrics"></a>Métricas do gateway de aplicativo

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Bytes recebidos**

   Contagem de bytes recebidos pelo gateway de aplicativo dos clientes

- **Bytes enviados**

   Contagem de bytes enviados pelo gateway de aplicativo para os clientes

- **Protocolo TLS do cliente**

   Contagem de solicitações TLS e não TLS iniciadas pelo cliente que estabeleceram a conexão com o gateway de aplicativo. Para exibir a distribuição de protocolo TLS, filtre pelo protocolo TLS de dimensão.

- **Unidades de capacidade atuais**

   Contagem de unidades de capacidade consumidas. As unidades de capacidade medem o custo com base no consumo que é cobrado além do custo fixo. Há três determinantes para unidade de computação de unidade de capacidade, conexões persistentes e taxa de transferência. Cada unidade de capacidade é composta por, no máximo: 1 unidade de computação, ou 2500 conexões persistentes ou taxa de transferência de 2,22 Mbps.

- **Unidades de computação atuais**

   Contagem de capacidade de processador consumida. Fatores que afetam a unidade de computação são conexões TLS/s, computações de regravação de URL e processamento de regra WAF. 

- **Conexões atuais**

   Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo

- **Solicitações com falha**

   Contagem de solicitações com falha que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.


- **Status da resposta**

   Status de resposta HTTP retornado pelo gateway de aplicativo. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Taxa de transferência**

   Número de bytes por segundo atendidos pelo Gateway de Aplicativo

- **Total de solicitações**

   Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Regras correspondentes do firewall do aplicativo Web**

- **Regras disparadas por firewall do aplicativo Web**

### <a name="backend-metrics"></a>Métricas de back-end

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Status da resposta de back-end**

  Contagem de códigos de status de resposta HTTP retornados pelos back-ends. Isso não inclui nenhum código de resposta gerado pelo gateway de aplicativo. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Contagem de hosts íntegros**

  O número de back-ends que são determinados íntegros pela investigação de integridade. É possível filtrar por pool de back-end para mostrar hosts íntegros/não íntegros em um pool de back-end específico.

- **Contagem de hosts não íntegros**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar hosts não íntegros em um pool de back-end específico.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Métricas com suporte do SKU do gateway de aplicativo v1

### <a name="application-gateway-metrics"></a>Métricas do gateway de aplicativo

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Conexões atuais**

  Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo

- **Solicitações com falha**

  Contagem de solicitações com falha que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Status da resposta**

  Status de resposta HTTP retornado pelo gateway de aplicativo. A distribuição do código de status de resposta pode ser adicionalmente categorizada para mostrar as respostas nas categorias 2xx, 3xx, 4xx e 5xx.

- **Taxa de transferência**

  Número de bytes por segundo atendidos pelo Gateway de Aplicativo

- **Total de solicitações**

  Contagem de solicitações bem-sucedidas que o gateway de aplicativo serviu. A contagem de solicitações pode ser filtrada para mostrar a contagem por cada/pool de back-end específico-combinação de configurações de http.

- **Regras correspondentes do firewall do aplicativo Web**

- **Regras disparadas por firewall do aplicativo Web**

### <a name="backend-metrics"></a>Métricas de back-end

Para o Gateway de Aplicativo, as seguintes métricas estão disponíveis:

- **Contagem de hosts íntegros**

  O número de back-ends que são determinados íntegros pela investigação de integridade. É possível filtrar por pool de back-end para mostrar hosts íntegros/não íntegros em um pool de back-end específico.

- **Contagem de hosts não íntegros**

  O número de back-ends que são determinados não íntegros pela investigação de integridade. Você pode filtrar em uma base de pool por back-end para mostrar hosts não íntegros em um pool de back-end específico.

## <a name="metrics-visualization"></a>Visualização de métricas

Navegue até um gateway de aplicativo, em **monitoramento** selecione **métricas**. Para exibir os valores disponíveis, selecione a lista suspensa **MÉTRICA**.

Na imagem a seguir, você pode ver um exemplo com três métricas exibidas para os últimos 30 minutos:

[![](media/application-gateway-diagnostics/figure5.png "Exibição de métrica")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Para ver uma lista atual de métricas, consulte [Métricas com suporte no Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Regras de alerta sobre métricas

Você pode iniciar as regras de alerta com base nas métricas de um recurso. Por exemplo, um alerta poderá chamar um webhook ou enviar um email para um administrador se a vazão de dados do gateway de aplicativo estiver acima, abaixo ou no limite durante um período especificado.

O seguinte exemplo orientará você pela criação de uma regra de alerta que envia um email para um administrador após um limite de vazão de dados ter sido violado:

1. Selecione **adicionar alerta de métrica** para abrir a página **Adicionar regra** . Você também pode acessar essa página na página métricas.

   ![Botão “Adicionar alerta de métrica”][6]

2. Na página **Adicionar regra** , preencha as seções nome, condição e notificar e selecione **OK**.

   * No seletor **Condição**, selecione um dos quatro valores: **Maior que**, **Maior ou igual a**, **Menor que**, ou **Menor ou igual a**.

   * No seletor **Período**, selecione um período de cinco minutos a seis horas.

   * Se você selecionar **Proprietários, colaboradores e leitores de email**, o email poderá ser dinâmico com base nos usuários que têm acesso a esse recurso. Caso contrário, você poderá fornecer uma lista separada por vírgula de usuários na caixa **Emails de administrador adicionais**.

   ![Página Adicionar regra][7]

Se o limite for violado, um email semelhante ao mostrado na seguinte imagem chegará:

![Email para o limite violado][8]

Uma lista de alertas é exibida após a criação de um alerta de métrica. Ela fornece uma visão geral de todas as regras de alerta.

![Lista de alertas e regras][9]

Para saber mais sobre notificações de alerta, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Para entender mais sobre webhooks e como eles podem ser usados com alertas, consulte [Configurar um webhook em um alerta de métrica do Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Próximas etapas

* Visualize o contador e os logs de eventos com os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).
* Postagem no blog [Visualize your Azure Activity Log with Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizar o log de atividades do Azure com o Power BI).
* Postagem no blog [View and analyze Azure Activity Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Exibir e analisar os Logs de Atividades do Azure no Power BI e muito mais).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
