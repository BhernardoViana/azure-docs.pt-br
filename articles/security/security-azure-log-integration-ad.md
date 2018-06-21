---
title: Integração de Log do Azure com os logs de auditoria do Azure Active Directory | Microsoft Docs
description: Saiba como instalar o serviço de Integração de Logs do Azure e integre os registros de logs de auditoria do Azure
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 06/07/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 07299b960747528dde8dcefc70055442c2150486
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235984"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrar o Azure Active Directory a logs de auditoria

Eventos de auditoria do Azure AD (Azure Active Directory) ajudam a identificar ações privilegiadas que ocorreram no Azure Active Directory. Você pode ver os tipos de eventos que você pode acompanhar examinando os [eventos de relatório de auditoria do Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).


>[!IMPORTANT]
> O recurso Integração de log do Azure será preterido em 01/06/2019. Os downloads de AzLog serão desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer, veja a postagem [Usar o monitor do Azure para a integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Etapas para integrar os logs de auditoria do Azure Active Directory

> [!NOTE]
> Antes de experimentar as etapas neste artigo, você deve examinar o artigo [Introdução](security-azure-log-integration-get-started.md) e concluir as etapas relevantes dele.

1. Abra o prompt de comando e execute este comando:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Execute este comando: 
 
   ``azlog createazureid``

   Esse comando solicitará o logon do Azure. O comando cria uma entidade de serviço do Azure Active Directory em locatários do Azure AD que hospedam as assinaturas do Azure nas quais o usuário conectado é um administrador, um coadministrador ou um proprietário. O comando falhará se o usuário conectado for apenas um usuário convidado no locatário do Azure AD. A autenticação do Azure é feita por meio do AD do Azure. A criação de uma entidade de serviço para a Integração de Logs do Azure criará a identidade do Azure AD que receberá o acesso de leitura das assinaturas do Azure.

3. Execute o seguinte comando para fornecer sua ID de locatário. Você precisa ser membro da função de administrador de locatário para executar o comando.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Exemplo:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Verifique as seguintes pastas para confirmar se os arquivos JSON do Log de Auditoria do Azure Active Directory são criados nelas:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

O vídeo a seguir demonstra as etapas abordadas neste artigo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Para obter instruções específicas sobre como colocar as informações nos arquivos JSON em seu SIEM (sistema de gerenciamento de eventos e informações de segurança), contate o fornecedor do SIEM.

Ajuda da comunidade está disponível por meio de [Fórum do MSDN de integração de Log do Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este fórum permite que as pessoas na comunidade de Integração de Log do Azure deem suporte umas às outras com perguntas, respostas, dicas e truques. Além disso, a equipe de Integração de Logs do Azure monitora esse fórum e ajuda sempre que possível.

Você também pode abrir uma [solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md). Selecione **Integração de Log** como o serviço para o qual você está solicitando suporte.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Integração de Log do Azure, veja:

* [Integração de Logs do Microsoft Azure para os logs do Azure](https://www.microsoft.com/download/details.aspx?id=53324): a página Centro de Download oferece os detalhes, os requisitos de sistema e as instruções de instalação da Integração de Logs do Azure.
* [Introdução à Integração de Logs do Azure](security-azure-log-integration-overview.md): este documento apresenta a Integração de Logs do Azure, seus principais recursos e como ele funciona.
* [Perguntas frequentes sobre a Integração do Log do Azure](security-azure-log-integration-faq.md): este artigo responde perguntas sobre a Integração do Log do Azure.
* [Novos recursos para o Diagnóstico do Azure e para os Logs de Auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): esta postagem de blog apresenta os logs de auditoria do Azure e outros recursos que ajudam você a obter ideias sobre as operações de seus recursos do Azure.
