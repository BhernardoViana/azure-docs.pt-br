---
title: Consultar dados de um ambiente GA usando C# Azure Time Series insights de código | Microsoft Docs
description: Saiba como consultar dados de um ambiente de Azure Time Series Insights usando um aplicativo personalizado gravado no C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3729bedf7591ffecc558b88660486f7e336fa717
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705905"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Consultar dados do ambiente Azure Time Series Insights GA usandoC#

Este C# exemplo demonstra como consultar dados do ambiente Azure Time Series insights ga.

O exemplo mostra vários exemplos básicos do uso da API de consulta:

1. Como uma etapa de preparação, obtenha o token de acesso usando a API do Azure Active Directory. Passe esse token no cabeçalho `Authorization` de cada solicitação de API de Consulta. Para configurar aplicativos não interativos, veja [Autenticação e autorização](time-series-insights-authentication-and-authorization.md). Além disso, certifique-se de que todas as constantes definidas no início do exemplo estão definidas corretamente.
1. A lista de ambientes aos quais o usuário tem acesso é obtida. Um dos ambientes é escolhido como o ambiente de interesse e outros dados são consultados para esse ambiente.
1. Como um exemplo de solicitação HTTPS, os dados de disponibilidade são solicitados para o ambiente de interesse.
1. Como um exemplo de solicitação do soquete Web, os dados de eventos agregados são solicitados para o ambiente de interesse. Os dados são solicitados para todo o intervalo de tempo de disponibilidade.

> [!NOTE]
> O código de exemplo está disponível em [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Dependências do projeto

Adicione os pacotes NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` e `Newtonsoft.Json`.

## <a name="c-example"></a>Exemplo de C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre consultas, leia a [referência de API de consulta](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Leia como [conectar um aplicativo JavaScript usando o SDK do cliente](https://github.com/microsoft/tsiclient) para Time Series insights.