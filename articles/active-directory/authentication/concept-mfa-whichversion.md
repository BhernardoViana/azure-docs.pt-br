---
title: Escolha entre a nuvem ou servidor do Azure MFA | Microsoft Docs
description: Escolha a solução de segurança de autenticação multifator ideal para você perguntando o que estou tentando proteger e onde os meus usuários estão localizados.  Em seguida, escolha nuvem, Servidor MFA ou AD FS.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 002e473f66ea861d9257e308aaafacd420095108
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Escolha a solução Autenticação Multifator para você
Como há vários tipos de MFA (Autenticação Multifator do Azure), precisamos responder a algumas perguntas para descobrir qual versão é a correta para usar.  Essas perguntas são:

* [O que estou tentando proteger](#what-am-i-trying-to-secure)
* [Onde os usuários estão localizados](#where-are-the-users-located)
* [De quais recursos preciso?](#what-features-do-i-need)

As seções a seguir fornecem orientações sobre como determinar a cada uma delas.

## <a name="what-am-i-trying-to-secure"></a>O que estou tentando proteger?
Para determinar a solução de verificação em duas etapas correta, precisamos saber o que você está tentando proteger com um segundo método de autenticação.  É um aplicativo no Azure?  Ou um sistema de acesso remoto?  Ao determinar o que estamos tentando proteger, passaremos saber onde a Autenticação Multifator precisa ser habilitada.  

| O que você está tentando proteger | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Aplicativos primários da Microsoft |● |● |
| Aplicativos SaaS da Galeria de Aplicativos |● |  |
| Aplicativos Web publicados por meio da Proxy de Aplicativo do Azure AD |● |  |
| Aplicativos IIS não publicados por meio da Proxy de aplicativo do Azure AD | |● |
| Acesso remoto, como VPN, RDG | ● | ● |

## <a name="where-are-the-users-located"></a>Onde os usuários estão localizados
Em seguida, ver onde se encontram nossos usuários ajuda a determinar a solução correta a ser usada, seja na nuvem ou no local, usando o servidor MFA.

| Local do usuário | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD e AD local usando federação com AD FS |● |● |
| Azure AD e AD local usando o DirSync, o Azure AD Sync, o Azure AD Connect - sem sincronização de senha |● |● |
| Azure AD e AD local usando o DirSync, o Azure AD Sync, o Azure AD Connect - com sincronização de senha |● | |
| Active Directory local | |● |

## <a name="what-features-do-i-need"></a>De quais recursos preciso?
A tabela a seguir compara os recursos disponíveis com a Autenticação Multifator na nuvem e o Servidor de Autenticação Multifator.

| Recurso | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Notificação de aplicativo móvel como um segundo fator | ● | ● |
| Código de verificação de aplicativo móvel como um segundo fator | ● | ● |
| Chamada telefônica como um segundo fator | ● | ● |
| SMS unidirecional como segundo fator | ● | ● |
| SMS bidirecional como segundo fator | | ● (Preterido)| 
| Tokens de hardware como segundo fator | | ● |
| Senhas de aplicativos para clientes do Office 365 que não oferecem suporte a MFA | ● | |
| Controle do administrador sobre métodos de autenticação | ● | ● |
| Modo PIN | | ● |
| Alerta de fraude |● | ● |
| Relatórios de MFA |● | ● |
| Desvio único | | ● |
| Saudações personalizadas para chamadas telefônicas | ● | ● |
| ID do chamador personalizável para chamadas telefônicas | ● | ● |
| IPs confiáveis | ● | ● |
| Lembrar MFA para dispositivos confiáveis | ● | |
| Acesso condicional | ● | ● |
| Cache |  | ● |

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe a diferença entre a Autenticação Multifator do Microsoft Azure na nuvem ou no Servidor MFA local, é hora de configurar e usar a Autenticação Multifator do Microsoft Azure. **Selecione o ícone que representa seu cenário.**

<center>

[![MFA na nuvem](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ ![Servidor MFA](./media/concept-mfa-whichversion/server2.png)](../../multi-factor-authentication/multi-factor-authentication-get-started-server.md) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;  </center>
