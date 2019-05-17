---
title: Como preencher campos específicos para um aplicativo personalizado | Microsoft Docs
description: Orientações sobre como preencher campos específicos ao registrar um aplicativo personalizado com o Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8b93f26080229e980b680c157f59db4edf33e7a
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545493"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Como preencher campos específicos para um aplicativo personalizado

Este artigo oferece uma breve descrição de todos os campos disponíveis no formulário de registro do aplicativo no [portal do Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrar um novo aplicativo

-   Para registrar um novo aplicativo, navegue até o [portal do Azure](https://portal.azure.com).

-   No painel de navegação esquerdo, clique em **Azure Active Directory.**

-   Escolha **Registros do aplicativo** e clique em **Adicionar**.

-   Isso abre o formulário de registro do aplicativo.

## <a name="fields-in-the-application-registration-form"></a>Campos do formulário de registro do aplicativo


| Campo            | DESCRIÇÃO                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| NOME             | O nome do aplicativo. Ele deve ter um mínimo de quatro caracteres.                |
| Tipo de Aplicativo | **Aplicativo Web/API Web**: Um aplicativo que representa um aplicativo Web, uma API Web, ou ambos 
| |**Nativo**: Um aplicativo que pode ser instalado no dispositivo ou no computador de um usuário           |
| URL de entrada      | A URL em que os usuários podem entrar para usar o aplicativo                                  |

Após você preencher os campos acima, o aplicativo é registrado no portal do Azure e você é redirecionado para a página do aplicativo. O botão **Configurações** no painel do aplicativo abre a página Configurações, que tem mais campos para você personalizar seu aplicativo. A tabela a seguir descreve todos os campos na página Configurações. Observe que você verá apenas um subconjunto desses campos, dependendo tipo de aplicativo que você criou, um aplicativo Web ou um aplicativo nativo.

| Campo           | Descrição                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID do aplicativo  | Quando você registra um aplicativo, o Azure AD atribui a seu aplicativo uma ID de aplicativo. A ID do aplicativo pode ser usada para identificar exclusivamente o aplicativo nas solicitações de autenticação no Azure AD, além de acessar recursos, como a API do Graph.                                                          |
| URI de ID do aplicativo      | Deve ser um URI exclusivo, geralmente com o formato **https://&lt;locatário\_nome&gt;/&lt;aplicativo\_nome&gt;.** Ele é usado durante o fluxo de concessão de autorização como um identificador exclusivo para especificar o recurso para o qual o token deve ser emitido. Ele também se torna a declaração "aud" no token de acesso emitido. |
| Carregar novo logotipo | Pode ser usado para carregar um logotipo para seu aplicativo. O logotipo deve estar no formato .bmp, .jpg ou .png e o tamanho do arquivo deve ser inferior a 100 KB. As dimensões da imagem devem ser 215x215 pixels, com as dimensões da imagem central de 94x94 pixels.                                                       |
| URL da home page   | É a URL de logon especificada durante o registro do aplicativo.                                                                                                                                                                                                                                              |
| URL de logoff      | É a URL de logoff único. O Azure AD envia uma solicitação de logoff para essa URL quando o usuário limpa a sessão com o Azure AD usando qualquer outro aplicativo registrado.                                                                                                                                       |
| Multilocatário  | Esta opção especifica se o aplicativo pode ser usado por vários locatários. Normalmente, isso significa que organizações externas podem usar seu aplicativo registrando-o em seu locatário e concedendo acesso aos dados da organização.                                                                   |
| URLs de resposta      | As URLs de resposta são pontos de extremidade em que o Azure AD retorna tokens que seu aplicativo solicita.                                                                                                                                                                                                          |
| URIs de Redirecionamento   | Para aplicativos nativos, é para onde o usuário é enviado após uma autorização bem-sucedida. O Azure AD verifica se o URI de redirecionamento que seu aplicativo fornece na solicitação OAuth 2.0 corresponde a um dos valores registrados no portal.                                                            |
| simétricas            | Você pode criar chaves para acessar programaticamente APIs Web protegidas pelo Azure AD sem interação do usuário. Na página \*\*Chaves\*\*, insira uma descrição da chave e a data de validade e salve para gerar a chave. Certifique-se de salvá-la em algum lugar seguro, pois você não poderá acessá-la depois.             |

## <a name="next-steps"></a>Próximas etapas
[Gerenciando aplicativos com o Azure Active Directory](../manage-apps/what-is-application-management.md)
