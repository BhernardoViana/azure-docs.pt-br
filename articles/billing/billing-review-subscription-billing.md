---
title: Revise os dados de faturamento da assinatura do Azure com a API REST | Microsoft Docs
description: Aprenda a usar as APIs REST do Azure para revisar os detalhes de faturamento da assinatura.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: erikre
ms.openlocfilehash: 15725989ef786f94421eddf647f101e3e73633fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370808"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Revise o faturamento de assinatura usando APIs REST

As APIs de relatórios do Azure ajudam você a revisar e gerenciar seus custos do Azure.

Os filtros ajudam a personalizar os resultados para atender às suas necessidades.

Aqui, você aprende a usar uma API REST para retornar detalhes de faturamento da assinatura em um determinado período.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Criar a solicitação

O parâmetro `{subscriptionID}` é obrigatório e identifica a assinatura de destino.

O parâmetro `{billingPeriod}` é obrigatório e especifica um período de faturamento [atual](https://docs.microsoft.com/rest/api/billing/billingperiods/get#billingperiod).

Os parâmetros `${startDate}` e `${endDate}` são obrigatórios para este exemplo, mas opcionais para o terminal. Eles especificam o intervalo de datas como sequências na forma de AAAA-MM-DD (exemplos: `'20180501'` e `'20180615'`).

Os cabeçalhos a seguir são necessários:

|Cabeçalho da solicitação|DESCRIÇÃO|
|--------------------|-----------------|
|*Tipo de Conteúdo:*|Obrigatório. Defina como `application/json`.|
|*Autorização:*|Obrigatório. Defina como um `Bearer` [token de acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |

## <a name="response"></a>Response

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de custos detalhados para sua conta.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Cada item no **valor** representa detalhes sobre o uso de um serviço:

|Propriedade de resposta|DESCRIÇÃO|
|----------------|----------|
|**subscriptionGuid** | ID exclusivo global da assinatura. |
|**startDate** | Data em que o uso foi iniciado. |
|**endDate** | Data em que o uso foi encerrado. |
|**useageQuantity** | Quantidade utilizada. |
|**billableQuantity** | Quantidade faturada de fato. |
|**pretaxCost** | Custo faturado, antes dos impostos aplicáveis. |
|**meterDetails** | Informações detalhadas sobre o uso. |
|**nextLink**| Quando definido, especifica um URL para a próxima "página" de detalhes. Em branco quando a página é a última. |

Este exemplo é abreviado; consulte [Listar detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod) para obter uma descrição completa de cada campo de resposta.

Outros códigos de status indicam condições de erro. Nestes casos, o objeto de resposta explica por que a solicitação falhou.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas
- Revise a [visão geral de relatórios corporativos](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Investigue [API REST do Enterprise Billing](https://docs.microsoft.com/rest/api/billing/)
- [Iniciar com a API REST do Azure](https://docs.microsoft.com/rest/api/azure/)
