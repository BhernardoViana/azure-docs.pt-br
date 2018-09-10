---
title: Decodificar mensagens AS2 – Aplicativo Lógico do Azure | Microsoft Docs
description: Decodificar mensagens AS com o Aplicativo Lógico do Azure e o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 08/08/2018
ms.openlocfilehash: 06ffa6bddc1340ad548f9baf30eba65ba503bf73
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128274"
---
# <a name="decode-as2-messages-with-azure-logic-apps-and-enterprise-integration-pack"></a>Decodificar mensagens AS2 com o Aplicativo Lógico do Azure e o Enterprise Integration Pack 

Para estabelecer confiabilidade e segurança ao transmitir mensagens, use o conector Decodificar mensagens AS2. Esse conector fornece a assinatura digital, descriptografia e confirmações por meio de MDN (notificações de disposição de mensagem).

## <a name="before-you-start"></a>Antes de começar

Veja os itens necessários:

* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já esteja definida e associada à sua assinatura do Azure. Você precisa ter uma conta de integração para usar o conector Decodificar mensagem AS2.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Um [contrato AS2](logic-apps-enterprise-integration-as2.md) que já está definido em sua conta de integração

## <a name="decode-as2-messages"></a>Decodificar mensagens AS2

1. [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. O conector Decodificar AS2 não possui gatilhos. Você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho Solicitação. No Designer de Aplicativo Lógico, adicione um gatilho e uma ação ao aplicativo lógico.

3.  Na caixa de pesquisa, digite "AS2" como filtro. Selecione **AS2 – Decodificar Mensagem AS2**.
   
    ![Procure "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Se você não criou conexões à sua conta de integração previamente, terá que criá-las agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar.
   
    ![Criar conexão de integração](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da Conexão * |Digite um nome para a conexão. |
    | Uma conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

5.  Quando terminar, os detalhes de conexão devem ser semelhantes a este exemplo. Para concluir a criação da sua conexão, escolha **Criar**.

    ![detalhes de conexão de integração](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. Depois que a conexão é criada conforme mostrado neste exemplo, selecione **Corpo** e **Cabeçalhos** da saídas Solicitação.
   
    ![conexão de integração criada](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Por exemplo: 

    ![Selecione Corpo e Cabeçalhos de saídas de Solicitação](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 


## <a name="as2-decoder-details"></a>Detalhes do decodificador AS2

O conector Decodificar AS2 executa estas tarefas: 

* Processa cabeçalhos HTTP/AS2
* Verifica a assinatura (se configurado)
* Descriptografa as mensagens (se configurado)
* Descompacta as mensagens (se configurado)
* Verificar e não permitir duplicatas de ID de mensagem (se configuradas)
* Reconcilia um MDN recebido com a mensagem de saída original
* Atualiza e correlaciona os registros no banco de dados de não repúdio
* Grava os registros para o relatório de status do AS2
* O conteúdo da carga de saída é codificado na base64
* Determina se um MDN é necessário e se ele deve ser síncrono ou assíncrono com base na configuração no contrato do AS2
* Gera um MDN síncrono ou assíncrono (com base nas configurações do contrato)
* Define as propriedades e os tokens de correlação no MDN


  > [!NOTE]
  > Se você usar o Azure Key Vault para o gerenciamento de certificado, certifique-se de que você configurar as chaves para permitir que o **descriptografar** operação.
  > Caso contrário, a decodificação de AS2 falhará.
  >
  > ![Descriptografias de cofre de chaves](media/logic-apps-enterprise-integration-as2-decode/keyvault1.png)

## <a name="try-this-sample"></a>Experimente este exemplo

Para tentar implantar um aplicativo lógico totalmente operacional e o cenário de AS2 de exemplo, confira o[modelo e cenário de aplicativo lógico AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Exibir o swagger
Consulte os [detalhes do Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 

