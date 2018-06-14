---
title: Versão prévia do gerenciamento de unidades administrativas no Azure Active Directory
description: Usando unidades administrativas para delegação mais granular de permissões no Active Directory do Azure
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.component: users-groups-roles
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 4fa93e4bf64e01b44b0826b2182b125004c49609
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157475"
---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Gerenciamento de unidades administrativas no Azure AD – visualização pública
Este artigo descreve as unidades administrativas – um novo contêiner de recursos do Azure Active Directory que pode ser usado para delegar permissões administrativas e aplicar políticas a subconjuntos de usuários. No Active Directory do Azure, unidades administrativas permitem aos administradores centrais delegar permissões para administradores regionais ou definir a diretiva em um nível granular.

Isso é útil em organizações com divisões independentes, por exemplo, uma grande universidade que é composta de muitas escolas independentes (Faculdade de Administração, Faculdade de Engenharia e assim por diante) que são independentes umas das outras. Essas divisões têm seus próprios administradores de TI que controlam o acesso, gerenciam usuários e definem políticas especificamente para sua divisão. Os administradores centrais desejam poder conceder essas divisões permissões de administradores entre os usuários em suas divisões específicas. Mais especificamente, usando esse exemplo, um administrador central pode, por exemplo, criar uma unidade administrativa de uma faculdade específica (Faculdade de Administração) e preenchê-la com somente os usuários da Faculdade de Negócios. Um administrador central pode incluir a equipe de TI da Faculdade de Negócios em uma função com escopo definido, em outras palavras, concede à equipe de TI da Faculdade de Administração permissões administrativas do Business somente para a unidade administrativa da Faculdade de Administração.

> [!IMPORTANT]
> Para usar as Unidades Administrativas, o administrador no escopo da Unidade Administrativa possui uma licença do Microsoft Azure Active Directory Premium e do Azure Active Directory Basic para todos os usuários da Unidade Administrativa. Para saber mais, consulte [Introdução ao AD Premium do Azure](active-directory-get-started-premium.md).
>


Do ponto de vista do administrador central, uma unidade administrativa é um objeto de diretório que pode ser criado e populado com recursos. **Nesta versão de pré-visualização, esses recursos podem ser somente os usuários.** Após criada e populada, a unidade administrativa pode ser usada como um escopo para restringir a permissão concedida somente para os recursos contidos na unidade administrativa.

## <a name="managing-administrative-units"></a>Gerenciando unidades administrativas
Nesta versão de visualização, você pode criar e gerenciar unidades administrativas usando os cmdlets do Módulo do Active Directory do Azure para Windows PowerShell. Para saber mais sobre como fazer isso, consulte [Trabalhando com unidades administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Para obter mais informações sobre os requisitos de software e a instalação do módulo do Azure AD, além de informações sobre os cmdlets do Módulo do Azure AD para gerenciar unidades administrativas, incluindo sintaxe, descrições de parâmetros e exemplos, consulte [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Próximas etapas
[Edições do Active Directory do Azure](active-directory-whatis.md)
