---
title: Governança de identidade - Azure Active Directory | Microsoft Docs
description: Governança de identidade de diretório Active Directory do Azure permite que você equilibre a necessidade de sua organização de segurança e produtividade dos funcionários com os processos e visibilidade.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/29/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b4f1563aa0437cd45c297b95a83119318a24624
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109586"
---
# <a name="what-is-azure-ad-identity-governance"></a>O que é controle de identidade do AD do Azure?

Governança de identidade do Azure AD (Active Directory do Azure) permite que você equilibre a necessidade de sua organização de segurança e produtividade dos funcionários com os processos e visibilidade. Ela fornece recursos para garantir que os usuários certos tenham o acesso certo aos recursos certos, e permite que você proteja, monitore e audite o acesso a ativos críticos, ao mesmo tempo garantindo a produtividade dos funcionários.  

Governança de identidade fornecem às organizações a capacidade de fazer as seguintes tarefas entre os funcionários, parceiros de negócios e fornecedores e serviços e aplicativos:

- Controlar o ciclo de vida de identidade
- Controlar o ciclo de vida de acesso
- Administração segura

Especificamente, destina-se a ajudar as organizações a abordar estas quatro perguntas importantes:

- Quais usuários devem ter acesso a quais recursos?
- O que esses usuários estão fazendo com esse acesso?
- Existem controles organizacionais em vigor para gerenciar o acesso?
- Auditores podem verificar se os controles estão funcionando?

## <a name="identity-lifecycle"></a>Ciclo de vida de identidade

Governança de identidade ajuda as organizações a atingir um equilíbrio entre *produtividade* -quão rapidamente uma pessoa pode ter acesso aos recursos que precisa, como quando entrarem em minha organização? E *segurança* – Como o acesso deve mudar ao longo do tempo, como devido a alterações ao status de emprego da pessoa?  Gerenciamento de ciclo de vida de identidade é a base para governança de identidade e governança efetiva em grande escala requer modernizar a infraestrutura de gerenciamento do ciclo de vida de identidade para aplicativos.

![Ciclo de vida de identidade](./media/identity-governance-overview/identity-lifecycle.png)

Para muitas organizações, o ciclo de vida de identidade para funcionários está vinculado à representação daquele usuário em um sistema HCM (gerenciamento de capital humano).  O Azure AD Premium mantém automaticamente as identidades do usuário para as pessoas representadas no Workday tanto no Active Directory quanto no Azure Active Directory, conforme descrito no [tutorial de provisionamento de entrada do Workday (versão prévia)](../saas-apps/workday-inbound-tutorial.md).  O Azure AD Premium também inclui o [Microsoft Identity Manager](/microsoft-identity-manager/), que pode importar registros de sistemas HCM locais como SAP, Oracle eBusiness e Oracle PeopleSoft.

Cada vez mais, os cenários exigem a colaboração com pessoas de fora da sua organização. A colaboração [B2B do Azure AD](/azure/active-directory/b2b/) permite compartilhar com segurança aplicativos e serviços da sua organização com usuários convidados e parceiros externos de qualquer organização, ao mesmo tempo mantendo o controle sobre seus próprios dados corporativos.

## <a name="access-lifecycle"></a>Ciclo de vida de acesso

As organizações precisam de um processo para gerenciar o acesso além do que foi inicialmente provisionado para um usuário quando a identidade do usuário foi criada.  Além disso, organizações empresariais precisam ser capazes de dimensionar com eficiência para poderem desenvolver e impor política de acesso e controles continuamente.

![Ciclo de vida de acesso](./media/identity-governance-overview/access-lifecycle.png)

Em geral, a IT delega as decisões de aprovação de acesso aos tomadores de decisão empresariais.  Além disso, a IT pode envolver os próprios usuários.  Por exemplo, os usuários que acessam os dados confidenciais do cliente no aplicativo de marketing da empresa na Europa precisam conhecer as políticas da empresa. Usuários convidados podem não estar cientes dos requisitos de manipulação para os dados em uma organização para a qual foram convidados.

As organizações podem automatizar o processo de ciclo de vida de acesso por meio de tecnologias como [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md), junto com o provisionamento de usuário para [aplicativos SaaS](../saas-apps/tutorial-list.md) ou [aplicativos integrados ao SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  As organizações também podem controlar quais [usuários convidados têm acesso a aplicativos locais](../b2b/hybrid-cloud-to-on-premises.md).  Esses direitos de acesso podem então ser revisados regularmente usando [revisões de acesso do Azure AD](access-reviews-overview.md) recorrentes.

Quando um usuário tenta acessar a aplicativos, o Azure AD impõe [acesso condicional](/azure/active-directory/conditional-access/) políticas. Por exemplo, políticas de acesso condicional podem incluir exibindo uma [termos de uso](../conditional-access/terms-of-use.md) e [garantir que o usuário concordou com esses termos](../conditional-access/require-tou.md) antes de poder acessar um aplicativo.

## <a name="privileged-access-lifecycle"></a>Ciclo de vida de acesso privilegiado

Historicamente, o acesso privilegiado tem sido descrito por outros fornecedores como um recurso separado do controle de identidade. No entanto, na Microsoft, acreditamos que regem o acesso privilegiado é uma parte importante da governança de identidade – especialmente considerando o potencial para uso indevido associado a esses direitos podem fazer com que uma organização de administrador. Funcionários, fornecedores e prestadores de serviço que assumem direitos administrativos precisam ser controlados.

![Ciclo de vida de acesso privilegiado](./media/identity-governance-overview/privileged-access-lifecycle.png)

O Azure AD PIM (Privileged Identity Management) fornece controles adicionais adaptados para proteger direitos de acesso para recursos, entre o Azure AD, o Azure e outros serviços Online da Microsoft.  O acesso just-in-time e a função alterar recursos fornecidos pelo Azure AD PIM, além de acesso condicional e autenticação multifator de alerta, que fornecem que um conjunto abrangente de controles de governança para ajudar a proteger recursos da empresa (diretório, O Office 365 e funções de recurso do Azure). Assim como acontece com outras formas de acesso, as organizações podem usar revisões de acesso para configurar a recertificação recorrente de acesso para todos os usuários nas funções de administrador.

## <a name="getting-started"></a>Introdução

Embora não haja nenhuma solução perfeita ou recomendação para todos os clientes, as configurações a seguir fornecem um guia para que políticas de linha de base, a Microsoft recomenda que você siga para garantir uma força de trabalho mais segura e produtiva.

- [Configurações de acesso à identidade e ao dispositivo](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Protegendo o acesso privilegiado](../users-groups-roles/directory-admin-roles-secure.md)

Você também pode consultar a guia de introdução da **governança de identidade** no portal do Azure para começar a usar o gerenciamento de direitos, acessar revisões, Privileged Identity Management e termos de uso.

![Guia de introdução de governança de identidade](./media/identity-governance-overview/getting-started.png)

## <a name="next-steps"></a>Próximas etapas

- [O que é gerenciamento de direitos do AD do Azure? (versão prévia)](entitlement-management-overview.md)
- [Quais são as revisões de acesso do Azure AD?](access-reviews-overview.md)
- [O que é o Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [O que posso fazer com os Termos de uso?](active-directory-tou.md)
