---
title: Escalar verticalmente automaticamente unidade de produtividade do Hub de Eventos do Azure | Microsoft Docs
description: Habilitar Inflação automática em um namespace para escalar verticalmente automaticamente as unidades de produtividade.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 32f99b43a37277e70d209f1f315dcb398c2b5931
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004785"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Escalar verticalmente automaticamente unidade de produtividade do Hub de Eventos do Azure

Hubs de Eventos do Azure é uma plataforma de streaming de dados altamente escalonável. Assim, o uso dos Hubs de Eventos geralmente aumenta após o início do uso do serviço. Tal uso exige o aumento das [unidades de produtividade](event-hubs-features.md#throughput-units) predeterminadas para dimensionar os Hubs de Eventos e manipular taxas de transferência maiores. O recurso **inflar automaticamente** dos Hubs de Eventos escala verticalmente automaticamente aumentando o número de unidades de taxa de transferência para atender às necessidades de uso. O aumento de unidades de taxa de transferência evita cenários de limitação, nos quais:

* As taxas de entrada de dados excedem as unidades de taxa de transferência definidas.
* As taxas de solicitação de saída de dados excedem as unidades de taxa de transferência definidas.

## <a name="how-auto-inflate-works"></a>Como o Inflar automaticamente funciona

O tráfego dos Hubs de Eventos é controlado por [unidades de produtividade](event-hubs-features.md#throughput-units). Uma única unidade de taxa de transferência permite o ingresso de 1 MB por segundo e duas vezes essa quantidade de saída. Hubs de evento Standard podem ser configurados com 1 a 20 unidades de produtividade. Inflar automaticamente permite que você comece pequeno, com o mínimo de unidades de produtividade necessárias que você escolher. O recurso então dimensiona automaticamente para o limite máximo de unidades de produtividade que você precisa, dependendo do aumento de seu tráfego. O Inflar automaticamente oferece os seguintes benefícios:

- Um mecanismo eficiente de colocação em escala para começar pequeno e escalar verticalmente conforme o crescimento.
- Dimensione automaticamente para o limite superior especificado sem problemas de limitação.
- Mais controle sobre a colocação em escala, já que você controla quando e quanto dimensionar.

## <a name="enable-auto-inflate-on-a-namespace"></a>Habilitar o Inflar automaticamente em um namespace

Você pode habilitar ou desabilitar o recurso Inflação automática em um namespace dos Hubs de Eventos usando qualquer um dos seguintes métodos:

- O [Portal do Azure](https://portal.azure.com).
- Um [modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

### <a name="enable-auto-inflate-through-the-portal"></a>Habilitar Inflar automaticamente por meio do Portal

Você pode habilitar o recurso Inflação automática durante a criação de um namespace dos Hubs de Eventos:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Com essa opção habilitada, você pode começar pequeno em suas unidades de produtividade e escalar verticalmente à medida que suas necessidades de seu uso aumentam. O limite superior para inflação não afeta imediatamente os preços, que dependem do número de unidades de produtividade usadas por hora.

Você também pode habilitar o recurso Inflação automática usando a opção **Dimensionar** no painel de configurações no portal:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Habilitar Inflar automaticamente usando um modelo do Azure Resource Manager

Você pode habilitar o Inflar automaticamente durante uma implantação de modelo do Azure Resource Manager. Por exemplo, defina a propriedade `isAutoInflateEnabled` como **true** e defina `maximumThroughputUnits` como 10. Por exemplo:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Para ver o modelo completo, consulte o modelo [Criar namespace de Hubs de Eventos e habilitar inflar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) no GitHub.

## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Eventos](event-hubs-what-is-event-hubs.md)

