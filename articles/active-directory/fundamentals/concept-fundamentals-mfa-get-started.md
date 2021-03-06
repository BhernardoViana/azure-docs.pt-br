---
title: Habilitar a autenticação multifator para sua organização-Azure Active Directory
description: Habilitar o Azure MFA para sua organização com base em sua licença
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe0d79f228ee2b84c00a35d4836cbda6a4847a42
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932444"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Habilitar a autenticação multifator para sua organização

Há várias maneiras de habilitar a MFA (autenticação multifator) do Azure para seus usuários de Azure Active Directory (AD) com base nas licenças que sua organização possui. 

![Investigar sinais e impor a MFA, se necessário](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Com base em nossos estudos, sua conta tem mais de 99,9% menos provável de ser comprometida se você usar a MFA.

Então, como a sua organização ativa a autenticação multifator, mesmo gratuitamente, antes de se tornar uma estatística?

## <a name="free-option"></a>Opção gratuita

Os clientes que estão utilizando os benefícios gratuitos do Azure AD podem usar os [padrões de segurança](../fundamentals/concept-fundamentals-security-defaults.md) para habilitar a autenticação multifator em seu ambiente.

## <a name="office-365"></a>Office 365

Para clientes com o Office 365, há duas opções:

- Os [padrões de segurança](concept-fundamentals-security-defaults.md) podem ser habilitados por meio do Azure ad para proteger todos os seus usuários com a autenticação multifator do Azure.
- Se sua organização exigir mais granularidade no fornecimento da autenticação multifator, suas licenças do Office incluirão recursos [de MFA por usuário](../authentication/howto-mfa-userstates.md) . A MFA por usuário é habilitada e imposta em cada usuário individualmente por administradores.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Para clientes com Azure AD Premium P1 ou licenças semelhantes que incluem essa funcionalidade, como Enterprise Mobility + Security E3, Microsoft 365 F1 ou Microsoft 365 E3: 

A recomendação é usar [políticas de acesso condicional](../conditional-access/concept-conditional-access-policy-common.md) para obter a melhor experiência do usuário.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Para clientes com Azure AD Premium P2 ou licenças semelhantes que incluem essa funcionalidade, como Enterprise Mobility + Security E5 ou Microsoft 365 E5: 

A recomendação é usar [políticas de acesso condicional](../conditional-access/concept-conditional-access-policy-common.md) junto com as políticas de risco de [proteção de identidade](../identity-protection/overview-v2.md) para obter a melhor experiência de usuário e flexibilidade de imposição.

## <a name="authentication-methods"></a>Métodos de autenticação

|   | Padrões de segurança | Todos os outros métodos |
| --- | --- | --- |
| Notificação pelo aplicativo móvel | X | X |
| O código de verificação do aplicativo móvel ou token de hardware |   | X |
| Mensagem de texto para telefone |   | X |
| Ligue para o telefone |   | X |
| Senhas do aplicativo |   | X * * |

\* * As senhas de aplicativo só estarão disponíveis no MFA por usuário com cenários de autenticação herdados somente se habilitadas pelos administradores.

## <a name="next-steps"></a>Próximos passos

[Página de preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
