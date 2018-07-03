---
title: Analisar o uso de dados no Log Analytics | Microsoft Docs
description: Use o painel Uso e custo estimado no Log Analytics para avaliar a quantidade de dados que é enviada para o Log Analytics e identificar o que pode causar o aumento imprevisto.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/19/2018
ms.author: magoedte
ms.openlocfilehash: 9a9c898cf0f2e0b1387bbc2ac18b5009838d138b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317296"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analisar o uso de dados no Log Analytics
O Log Analytics inclui informações sobre a quantidade de dados coletados, quais fontes enviaram os dados e os tipos diferentes de dados enviados.  Use o painel **Uso do Log Analytics** para revisar e analisar o uso dos dados. O painel mostra quantos dados são coletados por cada solução e quantos dados são enviados pelos seus computadores.

## <a name="understand-the-usage-dashboard"></a>Entender o painel Uso
O painel **Uso de Log Analytics** exibe as seguintes informações:

- Volume de dados
    - Volume de dados ao longo do tempo (com base em seu escopo de tempo atual)
    - Volume de dados por solução
    - Dados não associados a um computador
- Computadores
    - Computadores enviando dados
    - Computadores sem dados nas últimas 24 horas
- Ofertas
    - Nós Insight e de Análise
    - Nós de automação e controle
    - Nós de segurança  
- Desempenho
    - Tempo necessário para coletar e indexar dados  
- Lista de consultas

![Painel Uso e custo](./media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>
)

### <a name="to-work-with-usage-data"></a>Para trabalhar com dados de uso
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.<br><br> ![Portal do Azure](./media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. Na lista de espaços de trabalho do Log Analytics, selecione um espaço de trabalho.
4. Selecione **Uso e custos estimados** na lista no painel esquerdo.
5. No painel **Uso e custos estimados**, você pode modificar o intervalo de tempo selecionando **Tempo: últimas 24 horas** e alterando o intervalo de tempo.<br><br> ![intervalo de tempo](./media/log-analytics-usage/usage-time-filter-01.png)<br><br>
6. Exiba as folhas de categoria de uso que mostram as áreas de seu interesse. Escolha uma folha e clique em um item para exibir mais detalhes em [Pesquisa de Log](log-analytics-log-searches.md).<br><br> ![KPI de uso de dados de exemplo](media/log-analytics-usage/data-volume-kpi-01.png)<br><br>
7. No painel de Pesquisa de Log, examine os resultados que são retornados da pesquisa.<br><br> ![pesquisa de log de uso de exemplo](./media/log-analytics-usage/usage-log-search-01.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Criar um alerta quando a coleta de dados for maior que a esperada
Este artigo descreve como criar um alerta quando:
- O volume de dados excede uma quantidade definida.
- A previsão do volume de dados excede uma quantidade definida.

Os alertas do Azure oferecem suporte a [alertas de log](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) que usam consultas de pesquisa. 

A consulta abaixo tem um resultado quando há mais de 100 GB de dados coletados nas últimas 24 horas:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

A consulta abaixo usa uma fórmula simples para prever quando mais de 100 GB de dados serão enviados em um mesmo dia: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Para receber um alerta em caso de volume de dados diferente, altere o número 100 nas consultas para o número de GB que deve disparar um alerta.

Use as etapas descritas em [criar um novo alerta de log](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) para ser notificado quando a coleta de dados for maior que a esperada.

Ao criar o alerta para a primeira consulta, quando há mais de 100 GB de dados em 24 horas, defina:  

- **Definir condição de alerta** especifica seu espaço de trabalho do Log Analytics como o destino do recurso.
- **Critérios de alerta** especificam o seguinte:
   - **Nome do sinal** seleciona **Pesquisa de registro personalizada**
   - **Consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **Lógica de alerta** é **Baseada em** *número de resultados* e **Condição** é *maior* que um **Limite**  de *0*
   - **Período de tempo** de *1440* minutos e **Frequência de alerta** a cada *60* minutos uma vez que os dados de uso são atualizados somente uma vez por hora.
- **Definir os detalhes do alerta** especifica o seguinte:
   - O **Nome** como *Volume de dados maior que 100 GB em 24 horas*
   - A **Gravidade** como *Aviso*

Especifique um já existente ou crie um novo [Grupo de ação](../monitoring-and-diagnostics/monitoring-action-groups.md) para que, quando o alerta de log corresponder aos critérios, você seja notificado.

Ao criar o alerta para a segunda consulta, quando existe a previsão de que haverá mais de 100 GB de dados em 24 horas, defina:

- **Definir condição de alerta** especifica seu espaço de trabalho do Log Analytics como o destino do recurso.
- **Critérios de alerta** especificam o seguinte:
   - **Nome do sinal** seleciona **Pesquisa de registro personalizada**
   - **Consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Lógica de alerta** é **Baseada em** *número de resultados* e **Condição** é *maior* que um **Limite**  de *0*
   - **Período de tempo** de *180* minutos e **Frequência de alerta** a cada *60* minutos uma vez que os dados de uso são atualizados somente uma vez por hora.
- **Definir os detalhes do alerta** especifica o seguinte:
   - O **Nome** como *Previsão de volume de dados maior que 100 GB em 24 horas*
   - A **Gravidade** como *Aviso*

Especifique um já existente ou crie um novo [Grupo de ação](../monitoring-and-diagnostics/monitoring-action-groups.md) para que, quando o alerta de log corresponder aos critérios, você seja notificado.

Ao receber um alerta, use as etapas na seção a seguir para solucionar problemas relativos ao uso acima do esperado.

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Solução de problemas por uso acima do esperado
O painel Uso ajuda a identificar por que o uso (e, portanto, o custo) é maior do que o esperado.

O uso aumenta devido a uma ou mais das seguintes causas:
- Os dados enviados para o Log Analytics estão acima do esperado
- A quantidade de nós enviando dados para o Log Analytics está acima da esperada

### <a name="check-if-there-is-more-data-than-expected"></a>Verificar se há mais dados do que é esperado 
Há duas seções principais da página de uso que ajudam a identificar o que está causando o excesso de coleta de dados.

O gráfico *Volume de dados ao longo do tempo* mostra o volume total de dados enviados e os computadores que enviam a maioria dos dados. O gráfico na parte superior permite que você veja se o uso geral dos dados está aumentando, permanece estável ou está diminuindo. A lista de computadores mostra os 10 computadores que estão enviando a maioria dos dados.

O gráfico *Volume de dados por solução* mostra o volume de dados enviados por cada solução e as soluções que enviam mais dados. O gráfico na parte superior mostra o volume total de dados enviados por cada solução ao longo do tempo. Essas informações permitem que você identifique se uma solução está enviando mais dados, a mesma quantidade ou menos dados ao longo do tempo. A lista de soluções mostra as 10 soluções que estão enviando a maioria dos dados. 

Esses dois gráficos mostram todos os dados. Alguns dados são faturáveis, e outros são gratuitos. Para se concentrar apenas em dados que faturáveis, modifique a consulta na página de pesquisa para incluir `IsBillable=true`.  

![gráficos de volume de dados](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

Examine o gráfico *Volume de dados ao longo do tempo*. Para ver as soluções e os tipos de dados que estão enviando a maioria dos dados para um computador específico, clique no nome do computador. Clique no nome do primeiro computador da lista.

Na captura de tela a seguir, o tipo de dados *Gerenciamento de logs/Desempenho* está enviando a maioria dos dados para o computador.<br><br> ![Volume de dados para um computador](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)<br><br>

Em seguida, volte para o painel *Uso* e examine o gráfico *Volume de dados por solução*. Para ver os computadores que enviam a maioria dos dados para uma solução, clique no nome da solução na lista. Clique no nome da primeira solução da lista. 

A captura de tela a seguir confirma que o computador *mycon* está enviando a maioria dos dados para a solução Gerenciamento de Logs.<br><br> ![volume de dados para uma solução](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)<br><br>

Se necessário, execute análises adicionais para identificar os volumes grandes dentro de uma solução ou tipo de dados. As consultas de exemplo incluem:

+ Solução de **Segurança**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Solução do **Gerenciamento de log**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo de dados de **Desempenho**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo de dados de **Evento**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo de dados **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo de dados **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

Use as etapas a seguir para reduzir o volume de logs coletados:

| Origem do alto volume de dados | Como reduzir o volume de dados |
| -------------------------- | ------------------------- |
| Eventos de segurança            | Selecione [eventos de segurança mínima ou comuns](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/) <br> Alterar a política de auditoria de segurança para coletar somente eventos necessários. Em particular, examine a necessidade para coletar eventos para <br> - [auditoria de plataforma de filtragem](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [auditoria de registro](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [auditoria de sistema de arquivos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [auditoria de objeto kernel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [auditoria de manipulação de identificador](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - auditoria de armazenamento removível |
| contadores de desempenho       | Altere a [configuração do contador de desempenho](log-analytics-data-sources-performance-counters.md) para: <br> - Reduzir a frequência de coleta <br> - Reduzir o número de contadores de desempenho |
| Logs de eventos                 | Altere a [configuração de log de eventos](log-analytics-data-sources-windows-events.md) para: <br> - Reduzir o número de logs de eventos coletados <br> - Coletar somente níveis de eventos necessários. Por exemplo, não colete eventos de nível *informações* |
| syslog                     | Altere a [configuração do syslog](log-analytics-data-sources-syslog.md) para: <br> - Reduzir o número de instalações coletadas <br> - Coletar somente níveis de eventos necessários. Por exemplo, não coletar eventos de nível *Informações* e *Depurar* |
| AzureDiagnostics           | Altere a coleção de logs do recurso para: <br> - Reduzir o número de logs de envio de recursos para o Log Analytics <br> - Coletar somente os logs necessários |
| Dados da solução de computadores que não precisam da solução | Use [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md) para coletar dados somente dos grupos de computadores necessários. |

### <a name="check-if-there-are-more-nodes-than-expected"></a>Verifique se há mais nós do que o esperado
Se você estiver usando o tipo de preço *por nó (OMS)*, será cobrado com base no número de nós e soluções que usou. Você pode ver quantos nós de cada oferta estão em uso na seção *ofertas* do painel de uso.<br><br> ![painel de uso](./media/log-analytics-usage/log-analytics-usage-offerings.png)<br><br>

Clique em **Ver tudo...** para exibir a lista completa de computadores que estão enviando dados para a oferta selecionada.

Use [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md) para coletar dados somente dos grupos de computadores necessários.

## <a name="next-steps"></a>Próximas etapas
* Confira [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para aprender a usar a linguagem de pesquisa. Você pode usar consultas de pesquisa para executar outras análises nos dados de uso.
* Use as etapas descritas em [criar um alerta de log](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) para ser notificado quando um critério de pesquisa for atendido.
* Use [direcionamento de solução](../operations-management-suite/operations-management-suite-solution-targeting.md) para coletar dados somente dos grupos de computadores necessários.
* Para configurar uma política de coleta de eventos efetivamente segura, analise [Política de filtragem da Central de Segurança do Azure](../security-center/security-center-enable-data-collection.md).
* Altere a [configuração do contador de desempenho](log-analytics-data-sources-performance-counters.md).
* Para modificar as configurações de coleta de eventos, analise [configuração de log de eventos](log-analytics-data-sources-windows-events.md).
* Para modificar as configurações de coleta de syslog, analise [configuração de syslog](log-analytics-data-sources-syslog.md).
