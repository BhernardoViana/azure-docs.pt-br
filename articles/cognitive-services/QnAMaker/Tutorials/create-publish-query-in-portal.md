---
title: Criar, publicar e responder no QnA Maker
titleSuffix: Azure Cognitive Services
description: Crie uma base de dados de conhecimento com perguntas e respostas de perguntas frequentes públicas baseadas na Web. Salve, treine e publique a base de dados de conhecimento. Depois que a base de dados de conhecimento for publicada, envie uma pergunta e receba uma resposta com um comando do cURL. Em seguida, crie um bot e teste o bot com a mesma pergunta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a80a815d4a1a892b5258aef1c1fc7ef4ab881fe7
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65594155"
---
# <a name="tutorial-from-qna-maker-portal-create-a-knowledge-base"></a>Tutorial: No portal do QnA Maker, crie uma base de dados de conhecimento

Crie uma base de dados de conhecimento com perguntas e respostas de perguntas frequentes públicas baseadas na Web. Salve, treine e publique a base de dados de conhecimento. Depois que a base de dados de conhecimento for publicada, envie uma pergunta e receba uma resposta com um comando do cURL. Em seguida, crie um bot e teste o bot com a mesma pergunta. 

Neste tutorial, você aprenderá como: 

> [!div class="checklist"]
> * Criar uma base de dados de conhecimento no portal do QnA Maker
> * Revisar, salvar e treinar a base de dados de conhecimento
> * Publicar a base de dados de conhecimento
> * Usar cURL para consultar a base de dados de conhecimento
> * Criar um bot
> 
> [!NOTE]
> A versão programática deste tutorial está disponível com uma solução completa [ **Azure-Samples/cognitive-services-qnamaker-csharp** no repositório do GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md) existente. 

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento 

1. Entre no portal do [QnA Maker](https://www.qnamaker.ai). 

1. Selecione **Criar uma base de dados de conhecimento** no menu superior.

    ![Etapa 1 do processo de criação da base de dados de conhecimento](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Ignore a primeira etapa, pois você usará o serviço QnA Maker existente. 

1. Na próxima etapa, selecione as configurações existentes:  

    |Configuração|Finalidade|
    |--|--|
    |ID do Diretório do Microsoft Azure|Sua _ID de diretório do Microsoft Azure_ está associada à conta que você usa para entrar no portal do Azure e no portal do QnA Maker. |
    |Nome da assinatura do Azure|Sua conta de cobrança em que o recurso QnA Maker foi criado.|
    |Serviço QnA do Azure|O recurso existente do QnA Maker.|

    ![Etapa 2 do processo de criação da base de dados de conhecimento](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Na próxima etapa, insira o nome da base de dados de conhecimento, `My Tutorial kb`.

    ![Etapa 3 do processo de criação da base de dados de conhecimento](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Na próxima etapa, preencha a base de dados de conhecimento com as seguintes configurações:  

    |Nome da configuração|Valor da configuração|Finalidade|
    |--|--|--|
    |URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |O conteúdo das Perguntas frequentes nessa URL é formatado com um ponto de interrogação seguido de uma resposta. O QnA Maker pode interpretar esse formato para extrair perguntas e as respostas associadas.|
    |Arquivo |_não usado neste tutorial_|Isso carrega arquivos para perguntas e respostas. |
    |Personalidade do bate-papo|Amigável|Isso oferece uma [personalidade](../Concepts/best-practices.md#chit-chat) casual e amigável para perguntas e respostas comuns. Você pode editar essas perguntas e respostas mais tarde. |

    ![Etapa 4 do processo de criação da base de dados de conhecimento](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Selecione **Criar sua KB** para concluir o processo de criação.

    ![Etapa 5 do processo de criação da base de dados de conhecimento](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>Revisar a base de dados de conhecimento, salvar e treinar

1. Examine as perguntas e respostas. A primeira página é de perguntas e respostas da URL. 

    ![Salvar e treinar](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. Selecione a última página de perguntas e respostas na parte inferior da tabela. A página mostra as perguntas e respostas da Personalidade do bate-papo. 

1. Na barra de ferramentas acima da lista de perguntas e respostas, selecione o ícone **Opções de exibição** e selecione **Mostrar metadados**. Isso mostra as marcas de metadados para cada pergunta e resposta. As perguntas de bate-papo têm os metadados **editorial: bate-papo** já definidos. Esses metadados são retornados para o aplicativo cliente com a resposta selecionada. O aplicativo cliente, como um chatbot, pode usar esses metadados filtrados para determinar outros processamentos ou interações com o usuário.

    ![![View Metadata tags](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png#lightbox)

1. Selecione **Salvar e treinar** na barra de menus superior.

## <a name="publish-to-get-kb-endpoints"></a>Publicar para obter pontos de extremidade da base de dados de conhecimento

Selecione o botão **Publicar** no menu superior. Quando você estiver na página de publicação, selecione **Publicar**, ao lado do botão **Cancelar**.

![Publicar](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Depois que a base de dados de conhecimento é publicada, o ponto de extremidade é exibido

![Publicar configurações de ponto de extremidade da página](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Não feche essa página **Publicar**; você a usará para criar um bot mais adiante no tutorial. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Usar o cURL para consultar uma resposta de perguntas frequentes

1. Selecione a guia **cURL**. 

    ![Comando de cURL](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Copie o texto da guia **cURL** e execute em um terminal ou linha de comando habilitado para cURL. O valor do cabeçalho de autorização inclui o texto `Endpoint` com um espaço à direita e, em seguida, a chave.

1. Substitua `<Your question>` por `How large can my KB be?`. Isso parece a pergunta `How large a knowledge base can I create?`, mas não exatamente. O QnA Maker aplica processamento de linguagem natural para determinar se as duas perguntas são iguais.     

1. Execute o comando do cURL e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    O QnA Maker tem alguma certeza, com pontuação de 42,81%.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Usar o cURL para consultar uma resposta de bate-papo

1. No terminal habilitado para o cURL, substitua `How large can my KB be?` por uma instrução de fim de conversa do bot feita pelo usuário, como `Thank you`.   

1. Execute o comando do cURL e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Como a pergunta `Thank you` correspondeu exatamente a uma pergunta do bate-papo, o QnA Maker está completamente seguro com a pontuação de 100. O QnA Maker também retornou todas as perguntas relacionadas e a propriedade de metadados que contém as informações de marca de metadados de Bate-papo.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Usar o cURL para consultar a resposta padrão

Todas as perguntas que não tenham resposta com grau de confiança do QnA Maker recebem a resposta padrão. Esta resposta é configurada no portal do Azure. 

1. No terminal habilitado para cURL, substitua `Thank you` por `x`. 

1. Execute o comando do cURL e receba a resposta JSON, incluindo a pontuação e a resposta. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    O QnA Maker retornou uma pontuação igual a `0`, o que significa nenhuma confiança, mas também retornou a resposta padrão. 

## <a name="create-a-knowledge-base-bot"></a>Criar um bot da base de dados de conhecimento

Para obter mais informações, confira [Criar um chatbot com esta base de dados de conhecimento](create-qna-bot.md).

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de trabalhar com o bot da base de dados de conhecimento, remova o grupo de recursos, `my-tutorial-rg`, para remover todos os recursos do Azure criados no processo do bot.

Quando terminar de trabalhar com a base de dados de conhecimento, no portal do QnA Maker, selecione **Minhas bases de dados de conhecimento**, selecione a base de dados de conhecimento **Minha KB de tutorial** e, em seguida, o ícone Excluir na extremidade direita da linha.  

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os formatos de arquivo com suporte, confira [Fontes de dados compatíveis](../Concepts/data-sources-supported.md). 

Saiba mais sobre [personalidades](../Concepts/best-practices.md#chit-chat) de bate-papo.

Para obter mais informações sobre a resposta padrão, consulte [Nenhuma correspondência encontrada](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Criar um chatbot com esta base de dados de conhecimento](create-qna-bot.md)