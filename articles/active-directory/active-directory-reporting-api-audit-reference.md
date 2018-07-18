---
title: Referência à API de auditoria do Azure Active Directory | Microsoft Docs
description: Como começar a usar a API de auditoria do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e620a7f488e51a60bff6943135831eea0d12816d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158040"
---
# <a name="azure-active-directory-audit-api-reference"></a>Referência à API de auditoria do Azure Active Directory

> [!TIP] 
> Confira a nova API do Microsoft Graph para [relatórios](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit), que substituirá finalmente essa API. 


Este artigo faz parte de uma coleção de artigos sobre a API de relatório do Azure Active Directory (Azure AD). Os relatórios do Azure AD fornecem uma API que permite a você acessar dados de auditoria usando código ou ferramentas relacionadas.
O escopo deste artigo é fornecer informações de referência sobre a **API de auditoria**.

Consulte:

* [Logs de auditoria](active-directory-reporting-azure-portal.md#activity-reports) para obter mais informações conceituais

* [Introdução à API de relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md) para saber mais sobre a API de relatório.


Para:

- Perguntas frequentes, leia as [Perguntas Frequentes](active-directory-reporting-faq.md) 

- Problemas, [registre um tíquete de suporte](active-directory-troubleshooting-support-howto.md) 


## <a name="who-can-access-the-data"></a>Quem pode acessar os dados?
* Usuários na função de Administrador de segurança ou Leitor de segurança
* Administradores globais
* Qualquer aplicativo que tenha autorização para acessar a API (a autorização do aplicativo pode ser definida somente com base na permissão de Administrador Global)

## <a name="prerequisites"></a>pré-requisitos
Para acessar esse relatório por meio da API de relatórios, você deve ter:

* Um [Azure Active Directory gratuito ou uma edição melhor](active-directory-whatis.md)
* Concluído os [pré-requisitos para acessar a API de relatório do Azure AD](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Como acessar a API
Você pode acessar essa API por meio de [Graph Explorer](https://graphexplorer2.cloudapp.net) ou usando programaticamente, por exemplo, o PowerShell. Use o caractere de acento grave para "escapar" o caractere $ aa fim de garantir que o PowerShell possa interpretar a sintaxe do filtro OData usada nas chamadas REST do AAD Graph. O caractere de acento grave serve como [caractere de escape do PowerShell](https://technet.microsoft.com/library/hh847755.aspx), permitindo que o PowerShell faça uma interpretação literal do caractere $ e evite confundi-lo como um nome de variável do PowerShell (por exemplo: $filter).

O foco deste artigo está no Graph Explorer. Para obter um exemplo do PowerShell, consulte [scripts do PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>Ponto de extremidade de API

Você pode acessar essa API usando o seguinte URI:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Não há nenhum limite para o número de registros retornados pela API de auditoria do Azure AD (usando a paginação de OData). Para os limites de retenção dos dados de relatório, confira [Políticas de Retenção de Relatório](active-directory-reporting-retention.md).

Essa chamada retorna os dados em lotes. Cada lote tem no máximo 1000 registros. Para obter o próximo lote de registros, use o link **Próximo**. Obtenha as informações de token de omissão do primeiro conjunto de registros retornados. O token skip estará no final do conjunto de resultados.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtros com suporte

Você pode reduzir o número de registros retornados por uma chamada de API com um filtro.  
Para dados relacionados à API de conexão, os filtros a seguir recebem suporte:

* **$top=\<número de registros a serem retornados\>** – para limitar o número de registros retornados. Esta é uma operação cara. Não use esse filtro se você quiser retornar milhares de objetos.     
* **$filter=\<sua instrução de filtro\>** – para especificar, com base nos campos de filtro com suporte, o tipo de registro com o qual você se preocupa

## <a name="supported-filter-fields-and-operators"></a>Campos de filtro e operadores com suporte
Para especificar o tipo de registro com o qual você se preocupa, compile uma instrução de filtro com um ou uma combinação dos campos de filtro a seguir:

* [activityDate](#activitydate) – define uma data ou intervalo de datas
* [categoria](#category) – define a categoria pela qual você deseja filtrar.
* [activityStatus](#activitystatus) – define o status de uma atividade
* [activityType](#activitytype) – define o tipo de uma atividade
* [activity](#activity) – Define a atividade como uma cadeia de caracteres  
* [actor/name](#actorname) – define o ator na forma de nome do ator
* [actor/objectid](#actorobjectid) – Define o ator na forma da ID do ator   
* [ator/upn](#actorupn) – define o ator na forma de nome UPN do ator 
* [target/name](#targetname) – define o destino na forma de nome do ator
* [target/objectid](#targetobjectid) – Define o destino na forma de ID do destino  
* [target/upn](#targetupn) – Define o ator na forma de UPN (nome principal de usuário) do ator   

- - -
### <a name="activitydate"></a>activityDate
**Operadores com suporte**: eq, ge, le, gt, lt

**Exemplo**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**Observações**:

datetime deve estar no formato UTC

- - -
### <a name="category"></a>categoria

**Valores com suporte**:

| Categoria                         | Valor     |
| :--                              | ---       |
| Diretório principal                   | Diretório |
| Gerenciamento de senhas de auto-atendimento | SSPR      |
| Gerenciamento de grupos de autoatendimento    | SSGM      |
| Provisionamento de conta de usuário             | Sincronizar      |
| Substituição de senha automática      | Substituição de senha automática |
| Identity Protection              | IdentityProtection |
| Usuários Convidados                    | Usuários Convidados |
| Serviço MIM                      | Serviço MIM |



**Operadores com suporte**: eq

**Exemplo**:

    $filter=category eq 'SSPR'
- - -
### <a name="activitystatus"></a>activityStatus

**Valores com suporte**:

| Status da Atividade | Valor |
| :--             | ---   |
| Sucesso         | 0     |
| Failure         | - 1   |

**Operadores com suporte**: eq

**Exemplo**:

    $filter=activityStatus eq -1    

---
### <a name="activitytype"></a>activityType
**Operadores com suporte**: eq

**Exemplo**:

    $filter=activityType eq 'User'    

**Observações**:

diferencia maiúsculas de minúsculas

- - -
### <a name="activity"></a>activity
**Suporte para operadores**: eq, contains, startsWith

**Exemplo**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**Observações**:

diferencia maiúsculas de minúsculas

- - -
### <a name="actorname"></a>actor/name
**Suporte para operadores**: eq, contains, startsWith

**Exemplo**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**Observações**:

não diferenciam maiúsculas de minúsculas

- - -
### <a name="actorobjectid"></a>actor/objectid
**Operadores com suporte**: eq

**Exemplo**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    


- - -
### <a name="targetname"></a>target/name
**Suporte para operadores**: eq, contains, startsWith

**Exemplo**:

    $filter=targets/any(t: t/name eq 'some name')    

**Observações**:

Não diferenciam maiúsculas de minúsculas

- - -
### <a name="targetupn"></a>target/upn
**Suporte para operadores**: eq, startsWith

**Exemplo**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**Observações**:

* Não diferenciam maiúsculas de minúsculas
* Adicione o namespace completo ao consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

- - -
### <a name="targetobjectid"></a>target/objectid
**Operadores com suporte**: eq

**Exemplo**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>ator/upn
**Suporte para operadores**: eq, startsWith

**Exemplo**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**Observações**:

* Não diferenciam maiúsculas de minúsculas 
* Adicione o namespace completo ao consultar Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

- - -
## <a name="next-steps"></a>Próximas etapas

- Quer ver exemplos para atividades do sistema filtradas? Confira os [exemplos de API de auditoria do Azure Active Directory](active-directory-reporting-api-audit-samples.md).

- Quer saber mais sobre a API de relatório do Azure AD? Confira [Introdução à API de relatório do Azure Active Directory](active-directory-reporting-api-getting-started.md).

