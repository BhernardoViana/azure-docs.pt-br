---
title: Acesso condicional – exigir MFA para todos os usuários-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para exigir que todos os usuários executem a autenticação multifator
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0d22e2e6478c265ba9219ae4df5d5ddb34d481
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803879"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Acesso condicional: exigir MFA para todos os usuários

Como Alex Weinert, o diretório de segurança de identidade na Microsoft, menciona em sua postagem de blog [seu PA $ $Word não importa](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> Sua senha não importa, mas a MFA faz! Com base em nossos estudos, sua conta tem mais de 99,9% menos provável de ser comprometida se você usar a MFA.

As diretrizes neste artigo ajudarão sua organização a criar uma política de MFA balanceada para seu ambiente.

## <a name="user-exclusions"></a>Exclusões de usuário

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* Contas de **acesso de emergência** ou de **vidro** para impedir o bloqueio de conta em todo o locatário. No cenário improvável, todos os administradores são bloqueados de seu locatário, sua conta administrativa de acesso de emergência pode ser usada para fazer logon no locatário. siga as etapas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo [gerenciar contas de acesso de emergência no Azure ad](../users-groups-roles/directory-emergency-access.md).
* **Contas de serviço** e **princípios de serviço**, como a conta de sincronização de Azure ad Connect. As contas de serviço são contas não interativas que não estão ligadas a nenhum usuário específico. Normalmente, eles são usados por serviços de back-end e permitem acesso programático a aplicativos. As contas de serviço devem ser excluídas, pois a MFA não pode ser concluída programaticamente.
   * Se sua empresa tiver essas contas em uso em scripts ou código, considere substituí-las [por identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como solução alternativa temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="application-exclusions"></a>Exclusões de aplicativo

As organizações podem ter muitos aplicativos de nuvem em uso. Nem todos esses aplicativos podem exigir segurança igual. Por exemplo, os aplicativos de folha de pagamento e de presença podem exigir MFA, mas a lanchonete provavelmente não. Os administradores podem optar por excluir aplicativos específicos de sua política.

## <a name="create-a-conditional-access-policy"></a>Criar política de Acesso Condicional

As etapas a seguir ajudarão a criar uma política de acesso condicional para exigir que as funções administrativas atribuídas executem a autenticação multifator.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **segurança** > **acesso condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **atribuições**, selecione **usuários e grupos**
   1. Em **incluir**, selecione **todos os usuários**
   1. Em **excluir**, selecione **usuários e grupos** e escolha o acesso de emergência da sua organização ou contas de vidro. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações** > **incluir**, selecione **todos os aplicativos de nuvem**.
   1. Em **excluir**, selecione qualquer aplicativo que não exija autenticação multifator.
1. Em **controles de acesso** > **concessão**, selecione **conceder acesso**, **exigir autenticação multifator**e selecione **selecionar**.
1. Confirme suas configurações e defina **habilitar política** como **ativado**.
1. Selecione **criar** para criar para habilitar a política.

## <a name="next-steps"></a>Próximos passos

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)
