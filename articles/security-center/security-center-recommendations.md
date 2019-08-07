---
title: Recomendações de segurança na central de segurança do Azure | Microsoft Docs
description: Este documento mostra como as recomendações na Central de Segurança do Azure ajudam a proteger os recursos do Azure e a cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 229b8949facae34a809c0789154a3b56264ee2c5
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779012"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recomendações de segurança na Central de Segurança do Azure 
Este tópico explica como exibir e entender as recomendações na central de segurança do Azure para ajudá-lo a proteger seus recursos do Azure.

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Este documento não é um guia passo a passo.
>

## <a name="what-are-security-recommendations"></a>O que são recomendações de segurança?

As recomendações são ações a serem executadas para proteger seus recursos.

A central de segurança analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, ele fornece recomendações sobre como removê-los.

Cada recomendação fornece:

- Uma breve descrição do que está sendo recomendado.
- As etapas de correção para executar a fim de implementar a recomendação. <!-- In some cases, one-click remediation is available. -->
- Quais recursos são necessários para executar a ação recomendada neles.
- O **impacto de Pontuação segura**, que é o valor que sua pontuação de segurança aumentará se você implementar essa recomendação.

## Recomendações de monitor<a name="monitor-recommendations"></a>

A central de segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. O bloco **recomendações** em **visão geral** mostra o número total de recomendações identificadas pela central de segurança.

![Visão geral da central de segurança](./media/security-center-recommendations/asc-overview.png)

1. Selecione o **bloco recomendações** em **visão geral**. A lista de **recomendações** é aberta.

      ![Exibir recomendações](./media/security-center-recommendations/view-recommendations.png)

    Você pode filtrar as recomendações. Para filtrar as recomendações, selecione **Filtro** na folha **Recomendações**. A folha **Filtro** é aberta e você seleciona os valores de gravidade e de estado que deseja ver.

   * **RECOMENDAÇÕES**: A recomendação.
   * **IMPACTO DE PONTUAÇÃO SEGURA**: Uma pontuação gerada pela central de segurança usando suas recomendações de segurança e aplicando algoritmos avançados para determinar a crucialidade de cada recomendação. Para obter mais informações, consulte [cálculo de Pontuação segura](security-center-secure-score.md#secure-score-calculation).
   * **RECURSO:** lista os recursos aos quais essa recomendação se aplica.
   * **BARRAS DE STATUS**:  descreve a gravidade dessa recomendação específica:
       * **Alta (vermelho)** : existe uma vulnerabilidade em um recurso significativo (como um aplicativo, uma VM ou um grupo de segurança de rede) e ela requer atenção.
       * **Médio (laranja)** : existe uma vulnerabilidade e etapas não críticas ou adicionais são necessárias para eliminá-la ou para concluir um processo.
       * **Baixa (azul)** : existe uma vulnerabilidade que deve ser resolvida, mas não exige atenção imediata. (Por padrão, não são apresentadas recomendações baixas, mas você pode filtrar as recomendações baixas caso deseje vê-las.) 
       * **Íntegro (verde)** :
       * **Não disponível (cinza)** :

1. Para exibir os detalhes de cada recomendação, clique na recomendação.

    ![Detalhes da recomendação](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Consulte [modelos de implantação clássicos e do Resource Manager](../azure-classic-rm.md) para recursos do Azure.
 
## <a name="next-steps"></a>Próximas etapas

Neste documento, você foi apresentado às recomendações de segurança da Central de Segurança. Para saber como corrigir as recomendações:

* [Corrigir recomendações](security-center-remediate-recommendations.md) – saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
