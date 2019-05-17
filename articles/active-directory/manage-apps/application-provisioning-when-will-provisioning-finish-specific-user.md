---
title: Descubra quando um usuário específico será capaz de acessar um aplicativo | Microsoft Docs
description: Como saber quando um usuário extremamente importante é capaz de acessar um aplicativo que você configurou para o provisionamento do usuário com o Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a78a311771395f7c2113f03d5137d59999d7a7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783863"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Descubra quando um usuário específico será capaz de acessar um aplicativo
Ao usar o provisionamento automático de usuário com um aplicativo, o Azure AD automaticamente provisionar e atualizar contas de usuário em um aplicativo com base em coisas como [atribuição de usuário e grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) em um intervalo de tempo agendado regularmente, normalmente a cada 10 minutos.

## <a name="how-long-does-it-take"></a>Quanto tempo demora?

O tempo necessário para que um determinado usuário seja provisionado depende principalmente se uma sincronização "completa" inicial já ocorreu.

A primeira sincronização entre o Azure AD e um aplicativo pode levar de 20 minutos até várias horas, dependendo do tamanho do diretório do Azure AD e o número de usuários no escopo para provisionamento. 

Sincronizações subsequentes após a sincronização inicial ser mais rápido (por exemplo, em 10 minutos), como o serviço de provisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial, melhorando o desempenho de sincronizações subsequentes.

## <a name="how-to-check-the-status-of-a-user"></a>Como verificar o status de um usuário

Para ver o status de provisionamento para um usuário selecionado, consulte os logs de auditoria no Azure AD.

Os logs de auditoria de provisionamento podem ser acessados no portal do Azure, na guia **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt; Logs de auditoria**. Filtre os logs na categoria de **provisionamento de conta**, para ver apenas os eventos de provisionamento para aquele aplicativo. Você pode procurar por usuários com base na "ID correspondente" que foi configurado para eles nos mapeamentos de atributo. 

Por exemplo, se você configurou o "nome UPN" ou "endereço de email" como o atributo correspondente no lado do Azure AD e o usuário não sendo provisionado tem um valor de "audrey@contoso.com", em seguida, pesquise os logs de auditoria para "audrey@contoso.com" e reveja as entradas retornadas.

Os logs de auditoria de provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo:

* Consultando o Azure AD para usuários atribuídos que estão no escopo de provisionamento
* Consultando o aplicativo de destino para a existência desses usuários
* Comparando objetos de usuário entre o sistema
* Adicionar, atualizar ou desabilitar a conta de usuário no sistema de destino com base na comparação

## <a name="next-steps"></a>Próximas etapas
[Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)''
