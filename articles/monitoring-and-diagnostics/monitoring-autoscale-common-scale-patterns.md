---
title: Visão geral dos padrões comuns de dimensionamento automático
description: Conheça alguns dos padrões comuns para fazer o dimensionamento automático de seu recurso no Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 84727ec3694f64d40ad002a248a255df9074d7f4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263252"
---
# <a name="overview-of-common-autoscale-patterns"></a>Visão geral dos padrões comuns de dimensionamento automático
Este artigo descreve alguns dos padrões comuns para dimensionar seu recurso no Azure.

O dimensionamento automático do Azure Monitor se aplica somente aos VMSS (Conjuntos de Dimensionamento da Máquina Virtual), aos serviços de nuvem, aos planos do Serviço de Aplicativo e aos ambientes do Serviço de Aplicativo. 

# <a name="lets-get-started"></a>Vamos começar

Este artigo pressupõe que você esteja familiarizado com o dimensionamento automático. Você pode [começar a dimensionar seu recurso aqui][1]. A seguir, temos alguns dos padrões de dimensionamento comuns.

## <a name="scale-based-on-cpu"></a>Dimensionamento com base na CPU

Você tem um aplicativo Web (/VMSS/função de serviço de nuvem) e 

- Você deseja escalar/reduzir horizontalmente com base na CPU.
- Você quer garantir também que haja um número mínimo de instâncias. 
- Além disso, você deseja garantir que haja um limite máximo para o número de instâncias para as quais o dimensionamento pode ser feito.

![Dimensionamento com base na CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Dimensionamento diferente em finais de semana e dias da semana

Você tem um aplicativo Web (/VMSS/função de serviço de nuvem) e

- Você deseja três instâncias por padrão (em dias da semana)
- Você não espera o tráfego nos fins de semana e, portanto, deseja reduzir verticalmente para uma instância nos fins de semana.

![Dimensionamento diferente em finais de semana e dias da semana][3]

## <a name="scale-differently-during-holidays"></a>Dimensionamento diferente durante feriados

Você tem um aplicativo Web (/VMSS/função de serviço de nuvem) e 

- Por padrão, você deseja reduzir/escalar verticalmente com base no uso da CPU
- No entanto, durante o período de férias (ou em dias específicos que são importantes para sua empresa), você deseja substituir os padrões e ter mais capacidade à sua disposição.

![Dimensionamento diferente em feriados][4]

## <a name="scale-based-on-custom-metric"></a>Dimensionamento baseado em métrica personalizada

Você tem um front-end da Web e uma camada de API que se comunica com o back-end. 

- Você deseja dimensionar a camada de API com base em eventos personalizados no front-end (exemplo: você quer dimensionar seu processo de check-out com base no número de itens no carrinho de compras)

![Dimensionamento baseado em métrica personalizada][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png