---
title: Qual é a ferramenta “e se” de acesso condicional do Azure Active Directory?
description: Saiba como é possível reconhecer o impacto das políticas de acesso condicional no ambiente.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2018
ms.author: joflore
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6271c7a99e82fb1fff2815167b0149c74212f260
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521866"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access"></a>Qual é a ferramenta “e se” de acesso condicional do Azure Active Directory?

[Acesso condicional](../active-directory-conditional-access-azure-portal.md) é uma funcionalidade do Azure Active Directory (Azure AD) que permite controlar como os usuários autorizados acessam seus aplicativos de nuvem. Como você sabe o que esperar das políticas de acesso condicional em seu ambiente? Para responder essa pergunta, você pode usar o **a ferramenta de acesso condicional “e se”**.

Este artigo explica como você pode usar essa ferramenta para testar as políticas de acesso condicional.

## <a name="what-it-is"></a>O que é

A **ferramenta de políticas “e se” de acesso condicional** permite que você entenda o impacto de suas políticas de acesso condicional em seu ambiente. Em vez de testar as suas políticas executando várias entradas manualmente, essa ferramenta permite que você avalie um logon simulado de um usuário. A simulação calcula o impacto que esse logon tem em suas políticas e gera um relatório de simulação. O relatório não só lista as políticas de acesso condicional aplicadas, como também as [políticas clássicas](policy-migration.md#classic-policies), se elas existirem.    

As ferramentas “e se” também fornecem uma maneira de determinar rapidamente as políticas que se aplicam a um usuário específico. Você pode usar as informações, por exemplo, se você precisar solucionar um problema.  

## <a name="how-it-works"></a>Como ele funciona

Na **ferramenta “e se” de acesso condicional**, primeiro você precisa configurar as defições do cenário de logon que você deseja simular. Essas configurações incluem:

- O usuário que você deseja testar 

- Os aplicativos de nuvem que o usuário tente acessar

- As condições sob as quais o acesso para configurar os aplicativos de nuvem são realizadas
     
Como uma próxima etapa, você pode iniciar uma simulação que avalia suas configurações. Somente as políticas que estão habilitadas fazem parte de uma execução de avaliação.


Terminada a avaliação, a ferramenta gera um relatório das políticas afetadas.



## <a name="running-the-tool"></a>A execução da ferramenta

Você pode encontrar a ferramenta **e se** na página **[Acesso condicional - políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** no portal do Azure.

Para iniciar a ferramenta, na barra de ferramentas na parte superior da lista de políticas, clique em **E se**.

![E se](./media/what-if-tool/01.png)

Antes de executar uma avaliação, você deve configurar as definições.

## <a name="settings"></a>Configurações

Esta seção fornece informações sobre as configurações de execução de simulação.

![E se](./media/what-if-tool/02.png)


### <a name="user"></a>Usuário

Você só pode selecionar um usuário. Este é o único campo obrigatório.

### <a name="cloud-apps"></a>Aplicativos na nuvem

O padrão para essa configuração é **Todos os aplicativos de nuvem**. A configuração padrão executa uma avaliação de todas as políticas disponíveis em seu ambiente. Você pode restringir o escopo para políticas que afetem aplicativos de nuvem específicos.


### <a name="ip-address"></a>Endereço IP

O endereço IP é um único endereço IPv4 para imitar a [condição local](location-condition.md). O endereço representa o endereço de internet do dispositivo usado pelo usuário para fazer acesso. Você pode verificar o endereço IP de um dispositivo, por exemplo, navegando até [Qual é o meu endereço IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Plataformas de dispositivo

Essa configuração imita a [condição de plataformas de dispositivo](conditions.md#device-platforms) e representa o equivalente a **todas as plataformas (incluindo plataformas sem suporte)**. 
### <a name="client-apps"></a>Aplicativos cliente

Essa configuração imita a [condição de aplicativos do cliente](conditions.md#client-apps).
Por padrão, essa configuração faz com que uma avaliação de todas as políticas com **navegador** ou **aplicativos móveis e clientes de desktop** sejam cada ou ambos selecionados. Ela também detecta políticas que impõem o **Exchange ActiveSync (EAS)**. Você pode reduzir essa configuração selecionando:

- **Navegador** para avaliar todas as políticas com menos **Navegador** selecionado. 

- **Aplicativos móveis e clientes de desktop** avaliando todas as políticas com pelo menos **aplicativos móveis e clientes de desktop** selecionado. 


### <a name="sign-in-risk"></a>Risco de entrada

Essa configuração imita a [condição de risco de logon](conditions.md#sign-in-risk).   


## <a name="evaluation"></a>Avaliação 

Inicie uma avaliação clicando em **E se**. O resultado da avaliação fornece um relatório que consiste em: 

![E se](./media/what-if-tool/03.png)

- Um indicador de se as políticas clássicas existem no seu ambiente
- Políticas que se aplicam ao usuário
- Políticas que não se aplicam ao usuário


Se [políticas clássicas](policy-migration.md#classic-policies) existirem para os aplicativos de nuvem selecionados, um indicador é apresentado a você. Clicando no indicador, você é redirecionado para a página de políticas clássicas. Na página de políticas clássicas, você pode migrar uma política clássica ou simplesmente desabilitá-la. Você pode retornar para o resultado da avaliação fechando essa página.

Na lista de políticas que se aplicam ao usuário selecionado, você também pode localizar uma lista de [controles de concessão](controls.md#grant-controls) e controles de [sessão](controls.md#session-controls) que o seu usuário deve atender.

Na lista de políticas que não se aplicam ao usuário, você pode também encontrar os motivos pelos quais essas políticas não se aplicam. Para cada política listada, o motivo representa a primeira condição que não foi atendida. Um motivo possível para uma política que não é aplicada é uma política desabilitada por não ter sido melhor avaliada.   



## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de acesso condicional, consulte [Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md).

- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](best-practices.md). 

- Se você desejar migrar políticas clássicas, consulte [Migrar políticas clássicas no portal do Azure](policy-migration.md)  
