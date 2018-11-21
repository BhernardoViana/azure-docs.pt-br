---
title: Latências de relatórios do Azure Active Directory | Microsoft Docs
description: Saiba quanto tempo leva para que os eventos de relatório sejam exibidos no seu portal do Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e5ceae2959f79c677f5b89c0c3f0a487f92ad1c6
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623152"
---
# <a name="azure-active-directory-reporting-latencies"></a>Latências de relatórios do Azure Active Directory

Latência é o tempo que os dados de relatórios do Azure AD (Azure Active Directory) levam para serem exibidos no [portal do Azure](https://portal.azure.com). Este artigo lista a latência esperada para os diferentes tipos de relatórios. 

## <a name="activity-reports"></a>Relatórios de atividades

Há dois tipos de relatórios de atividade:

- [Entradas](concept-sign-ins.md) – Fornece informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário
- [Logs de auditoria](concept-audit-logs.md) - Fornece informações de atividade do sistema sobre usuários e grupos, aplicativos gerenciados e atividades de diretório

A tabela a seguir lista as informações de latência para relatórios de atividade. 

> [!NOTE]
> **Latência (95º percentil)** refere-se ao tempo em que 95% dos logs serão relatados, e **Latência (99º percentil)** refere-se ao tempo em que 99% dos logs serão relatados. 
>

| Relatório | Latência (95º percentil) |Latência (99º percentil)|
| :-- | --- | --- | 
| Logs de auditoria | 2 minutos  | 5 min  |
| Entradas | 2 minutos  | 5 min |

## <a name="security-reports"></a>Relatórios de segurança

Há dois tipos de relatórios de segurança:

- [Entradas arriscadas](concept-risky-sign-ins.md) - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. 
- [Usuários sinalizados para riscos](concept-user-at-risk.md) - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. 

A tabela a seguir lista as informações de latência para relatórios de segurança.

| Relatório | Mínimo | Média | Máximo |
| :-- | --- | --- | --- |
| Usuários em risco          | 5 minutos   | 15 minutos  | 2 horas  |
| Entradas de risco         | 5 minutos   | 15 minutos  | 2 horas  |

## <a name="risk-events"></a>Eventos de risco

O Azure AD usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar ações suspeitas relacionadas às contas do usuário. Cada ação suspeita detectada é armazenada em um registro chamado **evento de risco**.

A tabela a seguir lista as informações de latência para eventos de risco.

| Relatório | Mínimo | Média | Máximo |
| :-- | --- | --- | --- |
| Entradas de endereços IP anônimos |5 minutos |15 minutos |2 horas |
| Entradas de locais desconhecidos |5 minutos |15 minutos |2 horas |
| Usuários com credenciais vazadas |2 horas |4 horas |8 horas |
| Viagem impossível a locais atípicos |5 minutos |1 hora |8 horas  |
| Entradas de dispositivos infectados |2 horas |4 horas |8 horas  |
| Entradas de endereços IP com atividade suspeita |2 horas |4 horas |8 horas  |


## <a name="next-steps"></a>Próximas etapas

* [Visão geral dos relatórios do Azure AD](overview-reports.md)
* [Acesso programático aos relatórios do Azure AD](concept-reporting-api.md)
* [Eventos de risco do Azure Active Directory](concept-risk-events.md)
