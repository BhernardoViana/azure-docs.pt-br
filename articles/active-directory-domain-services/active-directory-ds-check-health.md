---
title: Azure AD Domain Services - Verificar a integridade do domínio gerenciado | Microsoft Docs
description: Verifique a integridade do domínio gerenciado usando a página de integridade no portal do Azure.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: 5be6a2c36dfec8c8543f6dcfe252ce01b4c28e50
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34587231"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Verificar a integridade de um domínio gerenciado do Azure AD Domain Services

## <a name="overview-of-the-health-page"></a>Visão geral da página de integridade
Usando a página de integridade no portal do Azure, você consegue se manter atualizado sobre o que está acontecendo no domínio gerenciado. Este artigo explica os elementos da página de integridade.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Como exibir a integridade do domínio gerenciado
1. Navegue até a página [Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Clique no domínio cuja integridade você deseja exibir.
3. No painel de navegação à esquerda, clique em **Integridade**.

A figura a seguir ilustra um exemplo de página de integridade: ![Página de integridade de exemplo](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> A integridade do domínio gerenciado é avaliada a cada hora. Depois de fazer alterações no domínio gerenciado, aguarde o próximo ciclo de avaliação para exibir a integridade atualizada do domínio gerenciado. O carimbo de data/hora "Avaliado pela última vez" no canto superior direito mostra quando a integridade do domínio gerenciado foi avaliada pela última vez.
>

### <a name="status-of-your-managed-domain"></a>Status do domínio gerenciado
O status no canto superior direito da página de integridade indica a integridade geral do domínio gerenciado. O status é levado em conta em todos os alertas existentes no domínio. Você também pode exibir o status do domínio da página de visão geral do Azure AD Domain Services.

| Status | ícone | Explicação |
| --- | :----: | --- |
| Executando | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | O domínio gerenciado está funcionando normalmente e não tem alertas críticos ou de aviso. Este domínio pode ter alertas informativos. |
| Precisa de atenção (aviso) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | Não existem alertas críticos no domínio gerenciado, mas há um ou mais alertas de aviso que precisam ser resolvidos. |
| Precisa de atenção (crítico) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | Há um ou mais alertas críticos no seu domínio gerenciado. Você também pode ter alertas de aviso e/ou informativos. |
| Implantando | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | O domínio está no processo de implantação. |

## <a name="monitors"></a>Monitores
Monitores são aspectos do domínio gerenciado que o Azure AD Domain Services monitora regularmente. A melhor maneira de manter os monitores em estado íntegro é resolver todos alertas ativos do domínio gerenciado.

O Azure AD Domain Services atualmente monitora o seguinte:
 - Backup
 - Sincronização com o Azure AD

### <a name="the-backup-monitor"></a>O monitor de “backup”
Ele monitora se backups regulares do domínio gerenciado estão sendo realizados. A seguinte tabela explica o que esperar na coluna de detalhes do monitor de backup:

| Valor de detalhes | Explicação |
| --- | --- |
|“Backup jamais realizado” | Este estado é normal para um domínio gerenciado recém-criado. Em geral, o primeiro backup será criado 24 horas depois do domínio gerenciado ser provisionado. Se o domínio gerenciado não for recém-criado ou você vir esse estado por um tempo anormal, [entre em contato com o suporte](active-directory-ds-contact-us.md). |
| Último backup feito de 1 a 14 dias atrás | Em geral, esse valor é esperado para o monitor de backup. |
| O último backup foi feito há mais de 14 dias. | Qualquer tempo superior a duas semanas costuma ser muito tempo desde o último backup. Os alertas críticos ativos podem impedir que o backup do domínio gerenciado seja feito regularmente. Primeiro, resolva todos os alertas ativos do domínio gerenciado e, se o problema persistir, [entre em contato com o suporte](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>A sincronização com o monitor do Azure AD
A Microsoft monitora com que frequência o domínio gerenciado é sincronizado com o Azure Active Directory. O número de objetos (usuários e grupos) e o número de alterações feitas no diretório do Azure AD, desde a última sincronização, podem afetar quanto tempo um período de sincronização pode demorar. Se o domínio gerenciado tiver sido sincronizado pela última vez há mais de três dias, [entre em contato com o suporte](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Alertas
Os alertas são gerados para problemas no domínio gerenciado que precisam ser resolvidos para que o Azure AD Domain Services seja executado. Cada alerta explica o problema e fornece uma URL de resolução que descreve as etapas específicas para resolver o problema. Para exibir todos os alertas e as resoluções, visite o artigo [Solucionar problemas de alertas](active-directory-ds-troubleshoot-alerts.md).

### <a name="alert-severity"></a>Severidade do alerta
Os alertas são categorizados em três níveis de severidade diferentes: crítico, aviso e informativo.

 * **Alertas críticos** são problemas que afetam gravemente o domínio gerenciado. Esses alertas devem ser resolvidos imediatamente, porque a Microsoft não pode monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado. 
 * **Alertas de aviso** notificam você de problemas que podem afetar o domínio gerenciado no futuro. Esses alertas oferecem recomendações para proteger o domínio gerenciado.
 * **Alertas informativos** são notificações que não estão afetando negativamente o domínio. Os alertas informativos foram projetados para que você fique por dentro do que está acontecendo no domínio e no Azure AD Domain Services.

## <a name="next-steps"></a>Próximas etapas
- [Resolver alertas no domínio gerenciado](active-directory-ds-troubleshoot-alerts.md)
- [Ler mais sobre o Azure AD Domain Services](active-directory-ds-overview.md)
- [Entrar em contato com a equipe do produto](active-directory-ds-contact-us.md)
