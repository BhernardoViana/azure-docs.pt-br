---
title: Cmdlets do PowerShell do Azure AD para relatórios | Microsoft Docs
description: Referência dos cmdlets do PowerShell do Azure AD para relatórios.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f962cd9bc8c975ccaef90f61f20eea4cf1e4935e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014330"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Cmdlets do PowerShell do Azure AD para relatórios

> [!NOTE] 
> Atualmente, esses cmdlets do PowerShell funcionam apenas com o módulo de [visualização do Azure ad](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) . Observe que o módulo de visualização não é sugerido para uso em produção. 

Com os relatórios do Azure Active Directory (Azure AD), você pode obter detalhes sobre as atividades em todas as operações de gravação em sua direção (logs de auditoria) e dados de autenticação (logs de entrada). Embora as informações estejam disponíveis usando o MS API do Graph, agora você pode recuperar os mesmos dados usando os cmdlets do PowerShell do Azure AD para relatórios.

Este artigo fornece uma visão geral dos cmdlets do PowerShell a serem usados para logs de auditoria e logs de entrada.

## <a name="audit-logs"></a>Logs de auditoria

Os [logs de auditoria](concept-audit-logs.md) fornecem rastreamento por meio de logs para todas as alterações feitas por vários recursos no Azure AD. Exemplos de logs de auditoria incluem alterações feitas em quaisquer recursos no Azure AD, como adicionar ou remover usuários, aplicativos, grupos, funções e políticas.

Você obtém acesso aos logs de auditoria usando o cmdlet ' Get-AzureADAuditDirectoryLogs.


| Cenário                      | Comando do PowerShell |
| :--                           | :--                |
| Nome de exibição do aplicativo      | Get-AzureADAuditDirectoryLogs-filtrar "initiatedBy/app/displayName EQ ' sincronização de nuvem do Azure AD '" |
| Categoria                      | Get-AzureADAuditDirectoryLogs-filtro "category EQ ' Gerenciamento de aplicativos '" |
| Data e hora da atividade            | Get-AzureADAuditDirectoryLogs-filtro "activityDateTime gt 2019-04-18" |
| Todos os itens acima              | Get-AzureADAuditDirectoryLogs-filtre "initiatedBy/app/displayName EQ ' Azure AD Cloud Sync ' e Category EQ ' Gerenciamento de aplicativos ' e activityDateTime gt 2019-04-18"|


A imagem a seguir mostra um exemplo desse comando. 

![Botão "Resumo de dados"](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Logs de entrada

Os logs de [entradas](concept-sign-ins.md) fornecem informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.

Você obtém acesso aos logs de entrada usando o cmdlet ' Get-AzureADAuditSignInLogs.


| Cenário                      | Comando do PowerShell |
| :--                           | :--                |
| Nome de exibição do usuário             | Get-AzureADAuditSignInLogs-filtro "UserDisplayName EQ ' Timothy Perkins '" |
| Criar data e hora              | Get-AzureADAuditSignInLogs-Filter "createdDateTime gt 2019-04-18T17:30:00.0 Z" (tudo desde 5:30 PM em 4/18) |
| Status                        | Get-AzureADAuditSignInLogs-Filter "status/errorCode EQ 50105" |
| Nome de exibição do aplicativo      | Get-AzureADAuditSignInLogs-filtrar "appDisplayName EQ ' StoreFrontStudio [WSFED habilitado] '" |
| Todos os itens acima              | Get-AzureADAuditSignInLogs-filtro "UserDisplayName EQ ' Timothy Perkins ' e status/errorCode ne 0 e appDisplayName EQ ' StoreFrontStudio [WSFED Enabled] '" |


A imagem a seguir mostra um exemplo desse comando. 

![Botão "Resumo de dados"](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos relatórios do Azure AD](overview-reports.md).
- [Relatório de logs de auditoria](concept-audit-logs.md). 
- [Acesso programático aos relatórios do Azure AD](concept-reporting-api.md)
