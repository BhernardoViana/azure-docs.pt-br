---
title: Visão geral de Azure Active Directory para desenvolvedores (v 1.0)
description: Este artigo fornece uma visão geral sobre como se conectar às contas corporativa e de estudante da Microsoft usando o ponto de extremidade v1.0 do Azure Active Directory e a plataforma.
services: active-directory
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e790728cbc7415532754d10773d466e9a0280422
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373900"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Visão geral de Azure Active Directory para desenvolvedores (v 1.0)

O Azure Active Directory (Azure AD) é um serviço de identidade de nuvem que permite aos desenvolvedores criar aplicativos que conectam com segurança qualquer usuário com uma conta corporativa ou de estudante da Microsoft. O Azure Active Directory oferece tanto suporte a desenvolvedores que criam aplicativos de linha de negócios (LOB) com locatário único quanto a desenvolvedores que buscam desenvolver aplicativos de multilocação. Além da conexão básica, o Azure Active Directory também permite que aplicativos chamem as APIs Microsoft como [Microsoft Graph](https://docs.microsoft.com/graph/overview) e as APIs personalizadas que são criadas na plataforma do Azure Active Directory. A documentação mostra como adicionar suporte ao Azure Active Directory para o seu aplicativo usando protocolos padrão como OAuth2.0 e OpenID Connect.

> [!NOTE]
> A maioria do conteúdo nessa página se concentra no ponto de extremidade v1.0 e na plataforma, que dá suporte somente a contas corporativas ou de estudante da Microsoft. Se deseja entrar em contas da Microsoft do tipo consumidor ou pessoal, veja mais informações no [ponto de extremidade v2.0 e na plataforma](v2-overview.md). O ponto de extremidade v2.0 oferece uma experiência de desenvolvedor unificada para aplicativos que desejam entrar em todas as identidades da Microsoft.

| | |
| --- | --- |
|[Noções básicas de autenticação](v1-authentication-scenarios.md) | Uma introdução à autenticação com o Azure AD. |
|[Tipos de aplicativos](app-types.md) | Uma visão geral dos cenários de autenticação com suporte no Azure AD. |
| | |

## <a name="get-started"></a>Comece agora

Os inícios rápidos e tutoriais da v1.0 orientam você durante a criação de um aplicativo em sua plataforma preferida usando o SDK da ADAL (Biblioteca de Autenticação do Azure AD). Veja os **Inícios rápidos da v1.0** e **Tutoriais da v1.0** na [plataforma de identidade da Microsoft (Azure Active Directory para desenvolvedores)](index.yml) para começar a usar.

## <a name="how-to-guides"></a>Guias de instruções

Veja os **Guias de instruções da v1.0** para obter informações detalhadas e instruções passo a passo das tarefas mais comuns no Azure AD.

## <a name="reference-topics"></a>Tópicos de referência

Os artigos a seguir fornecem informações detalhadas sobre as APIs, as mensagens de protocolo e os termos usados no Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Bibliotecas de autenticação (ADAL)](active-directory-authentication-libraries.md)   | Uma visão geral das bibliotecas e SDKs fornecidos pelo Azure AD. |
| [Exemplos de código](sample-v1-code.md)                                  | Uma lista de todos os exemplos de código do Azure AD. |
| [Glossário](developer-glossary.md)                                      | Terminologia e definições de palavras usadas em toda esta documentação. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
