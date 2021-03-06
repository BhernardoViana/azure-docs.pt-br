---
title: Não é possível entrar no Zeppelin no Azure HDInsight
description: Não é possível entrar no Zeppelin no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: d4bb9e090b238eacec77f4c19bbf9afb3e09a912
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091055"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Cenário: Não é possível entrar no Apache Zeppelin no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Não é possível entrar no Apache Zeppelin depois de alterar a senha do ADDS no Active Directory.

## <a name="cause"></a>Causa

O usuário mencionado no `activeDirectoryRealm.systemUsername` `shiro_ini` do arquivo alterou a senha do Active Directory.

## <a name="resolution"></a>Resolução

1. Verifique se a senha alterada é a causa raiz, incluindo `activeDirectoryRealm.systemPassword = <new password>` a configuração Zeppelin `shiro_ini` em Ambari. Remova a `activeDirectoryRealm.hadoopSecurityCredentialPath` configuração. Abaixo está o local `shiro ini`do.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Se os usuários agora podem entrar no Zeppelin após a etapa 1, crie um `jceks` novo arquivo com a nova senha e substitua `activeDirectoryRealm.hadoopSecurityCredentialPath` o pelo novo arquivo.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte- [@AzureSupport](https://twitter.com/azuresupport) se com a conta de Microsoft Azure oficial para melhorar a experiência do cliente. Conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
