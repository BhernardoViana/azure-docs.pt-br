---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116750"
---
A configuração `ApplicationInsights` permite que você adicione suporte a dados telemétricos do [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) para seu contêiner. O Application Insights fornece um monitoramento detalhado do seu contêiner. Você pode monitorar facilmente seu contêiner quanto a disponibilidade, desempenho e uso. Você pode identificar e diagnosticar erros em seu contêiner rapidamente.

A tabela a seguir descreve as definições de configuração com suporte sob o `ApplicationInsights` seção.

|Obrigatório| NOME | Tipo de dados | DESCRIÇÃO |
|--|------|-----------|-------------|
|Não| `InstrumentationKey` | string | A chave de instrumentação da instância do Application Insights para o qual telemetria de dados para o contêiner são enviados. Para obter mais informações, consulte [Application Insights para ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Exemplo:<br>`InstrumentationKey=123456789`|

