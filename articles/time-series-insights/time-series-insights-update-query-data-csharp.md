---
title: Consultar dados de um ambiente de visualização de C# Azure Time Series insights usando código | Microsoft Docs
description: Este artigo descreve como consultar dados de um ambiente do Azure Time Series Insights codificando um aplicativo personalizado escrito na linguagem C# (C-sharp) do .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: 8fdfd39527ccd298b78ef2c4c895d1f667d8d33b
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677610"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>Consultar dados do ambiente de visualização de Azure Time Series Insights usandoC#

Este C# exemplo demonstra como consultar dados do ambiente de visualização Azure Time Series insights.

O exemplo mostra vários exemplos básicos do uso da API de consulta:

1. Como uma etapa de preparação, obtenha o token de acesso usando a API do Azure Active Directory. Passe esse token no cabeçalho `Authorization` de cada solicitação de API de Consulta. Para configurar aplicativos não interativos, veja [Autenticação e autorização](time-series-insights-authentication-and-authorization.md). Além disso, certifique-se de que todas as constantes definidas no início do exemplo estão definidas corretamente.
1. A lista de ambientes aos quais o usuário tem acesso é obtida. Um dos ambientes é escolhido como o ambiente de interesse e outros dados são consultados para esse ambiente.
1. Como um exemplo de solicitação HTTPS, os dados de disponibilidade são solicitados para o ambiente de interesse.
1. Como um exemplo de solicitação do soquete Web, os dados de eventos agregados são solicitados para o ambiente de interesse. Os dados são solicitados para todo o intervalo de tempo de disponibilidade.

> [!NOTE]
> Esse código de exemplo também está disponível [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)em.

## <a name="c-example"></a>Exemplo de C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> O exemplo de código acima pode ser executado sem alterar os valores de ambiente padrão.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre consultas, leia a [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/preview-query).

- Leia como [conectar um aplicativo JavaScript de página única](tutorial-create-tsi-sample-spa.md) para Time Series insights.