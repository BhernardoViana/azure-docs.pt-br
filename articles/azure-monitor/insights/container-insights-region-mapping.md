---
title: Azure Monitor para mapeamentos de região de contêineres
description: Este artigo descreve os mapeamentos de região com suporte entre Azure Monitor para contêineres, espaço de trabalho Log Analytics e métricas personalizadas.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.openlocfilehash: f22c62dddf2e38fa2c9471ce98b49a8aa32390b3
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554028"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapeamentos de região com suporte pelo Azure Monitor para contêineres

 Ao habilitar Azure Monitor para contêineres, somente determinadas regiões têm suporte para vincular um espaço de trabalho Log Analytics e um cluster AKS e coletar métricas personalizadas enviadas para Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapeamentos com suporte do Log Analytics Workspace

Os recursos de cluster AKS ou Log Analytics espaço de trabalho podem residir em outras regiões e a tabela a seguir mostra nossos mapeamentos.

|**Região de cluster AKS** | **Região do espaço de trabalho Log Analytics** |
|-----------------------|------------------------------------|
|**Oriental** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Austrália** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Pacífico Asiático** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brasil** | |
|BrazilSouth | SouthCentralUS |
|**Canadá** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**Índia** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japão** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Coréia** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**EUA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|Lesteus<sup>1</sup>|
|US Gov - Virgínia |US Gov - Virgínia |

<sup>1</sup> devido a restrições de capacidade, a região não está disponível durante a criação de novos recursos. Isso inclui um espaço de trabalho Log Analytics. No entanto, os recursos vinculados preexistentes na região devem continuar funcionando.

## <a name="custom-metrics-supported-regions"></a>Regiões com suporte para métricas personalizadas

A coleta de métricas dos nós de clusters AKS (serviços Kubernetess do Azure) e pods tem suporte para publicação como métricas personalizadas somente nas seguintes [regiões do Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Próximos passos

Para começar a monitorar o cluster do AKS, examine [como habilitar o Azure monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento.  
