---
title: 'Tutorial: Examinando enunciados de ponto de extremidade – LUIS'
titleSuffix: Azure Cognitive Services
description: Melhore as previsões de aplicativo verificando ou corrigindo os enunciados recebidos pelo ponto de extremidade HTTP do LUIS sobre os quais o LUIS não tem certeza. Alguns enunciados podem ser verificados quanto à intenção e outros quanto à entidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 0a4d2a3345ce4f69d4492d1a782b778b1ee3bf4c
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895654"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Tutorial: Corrigir previsões incertas examinando os enunciados de ponto de extremidade
Neste tutorial, melhore as previsões de aplicativo verificando ou corrigindo os enunciados recebidos pelo ponto de extremidade HTTP do LUIS sobre os quais o LUIS não tem certeza. Você deve examinar os enunciados de ponto de extremidade como uma parte regular da sua manutenção agendada do LUIS.

Esse processo de revisão permite que o LUIS aprenda seu domínio de aplicativo. O LUIS seleciona os enunciados que aparecem na lista de análise. Esta lista é:

* Específica para o aplicativo.
* Destina-se a melhorar a precisão da previsão do aplicativo.
* Deve ser revisada regularmente.

Ao revisar os enunciados de ponto de extremidade, você verifica ou corrige a intenção prevista do enunciado.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar o aplicativo de exemplo
> * Examinar declarações de ponto de extremidade
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importar o aplicativo de exemplo

Use as etapas a seguir para importar um aplicativo.

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json).

1. No [portal LUIS em versão prévia](https://preview.luis-ai), importe o arquivo .json para um novo aplicativo.

1. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `review`.

    > [!TIP]
    > É uma prática recomendada clonar para uma nova versão antes de modificar o aplicativo. Quando terminar com uma versão, exporte-a (como um arquivo .json ou .lu) e faça check-in do arquivo no controle do código-fonte.


1. Para treinar o aplicativo, selecione **Treinar**.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicar o aplicativo para acessá-lo por meio do ponto de extremidade HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Adicionar enunciados no ponto de extremidade

Neste aplicativo, você tem intenções e entidades, mas não tem nenhum uso de ponto de extremidade. Esse uso de ponto de extremidade é necessário para melhorar o aplicativo com a análise do enunciado do ponto de extremidade.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Use o ponto de extremidade para adicionar os enunciados a seguir.

    |Enunciado do ponto de extremidade|Intenção alinhada|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    Há um único pool de enunciados para analisar, independentemente de qual versão está ativamente editando ou qual versão do aplicativo foi publicada no ponto de extremidade.

## <a name="review-endpoint-utterances"></a>Examinar declarações de ponto de extremidade

Examine os enunciados do ponto de extremidade para uma intenção corretamente alinhada. Embora haja um único pool de declarações para examinar em todas as versões, o processo de alinhamento correto da intenção adiciona o enunciado de exemplo somente ao _modelo ativo_ atual.

1. Na seção **Build** do portal, selecione **Examinar enunciados do ponto de extremidade** no painel de navegação esquerdo. A lista é filtrada para a intenção **ApplyForJob**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do botão Examinar enunciados de ponto de extremidade na barra de navegação à esquerda](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)

    Este enunciado, `I'm looking for a job with Natural Language Processing`, não está na intenção correta.

1.  Para alinhar esse enunciado, na linha do enunciado, selecione a **Intenção Alinhada** de `GetJobInformation`. Adicione o enunciado alterado ao aplicativo selecionando a marca de seleção.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do botão Examinar enunciados de ponto de extremidade na barra de navegação à esquerda](./media/luis-tutorial-review-endpoint-utterances/select-correct-aligned-intent-for-endpoint-utterance.png)

    Examine os enunciados restantes nesta intenção, corrigindo a intenção alinhada conforme necessário. Use a tabela de enunciado inicial neste tutorial para exibir a intenção alinhada.

    A lista **Examinar enunciados de ponto de extremidade** não deve mais ter esses enunciados corrigidos. Se mais enunciados aparecerem, continue trabalhando na lista, corrigindo as intenções alinhadas até que a lista esteja vazia.

    Qualquer correção da rotulação de entidade é feita depois que a intenção é alinhada, na página Detalhes da intenção.

1. Treine e publique o aplicativo novamente.

## <a name="get-intent-prediction-from-endpoint"></a>Obter a previsão de intenção do ponto de extremidade

Para verificar se os enunciados de exemplo alinhados corretamente melhoraram a previsão do aplicativo, tente um enunciado próximo ao enunciado corrigido.

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Vá até o final da URL no endereço e insira `Are there any natural language processing jobs in my department right now?`. O último parâmetro de querystring é `q`, o enunciado **consulta**.

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.903607249
                },
                "EmployeeFeedback": {
                    "score": 0.0312187821
                },
                "ApplyForJob": {
                    "score": 0.0230276529
                },
                "MoveEmployee": {
                    "score": 0.008322801
                },
                "Utilities.Stop": {
                    "score": 0.004480808
                },
                "FindForm": {
                    "score": 0.00425248267
                },
                "Utilities.StartOver": {
                    "score": 0.004224336
                },
                "Utilities.Help": {
                    "score": 0.00373591436
                },
                "None": {
                    "score": 0.0034621188
                },
                "Utilities.Cancel": {
                    "score": 0.00230977475
                },
                "Utilities.Confirm": {
                    "score": 0.00112078607
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2019-12-05 23:23:53"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
   ```

   Agora que os enunciados de incerteza estão alinhados corretamente, a intenção correta foi prevista com uma **pontuação alta**.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>A revisão pode ser substituída pela adição de mais enunciados?
Você deve estar imaginando por que não adicionar mais exemplos de enunciado. Qual é o objetivo de examinar os enunciados de ponto de extremidade? Em um aplicativo do mundo real LUIS, os enunciados de ponto de extremidade são de usuários com um arranjo e escolha de palavras que você ainda não usou. Se você tivesse usado o mesmo arranjo e escolha de palavras, a previsão original teria tido um percentual mais alto.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Por que a intenção mais bem pontuada está na lista de enunciados?
Alguns enunciados de ponto de extremidade terão uma pontuação de previsão alta na lista de análise. Ainda assim é necessário examinar esses enunciados. Eles estão na lista porque a intenção com a segunda pontuação mais alta tinha uma pontuação muito próxima da intenção com a maior pontuação. Você deseja cerca de 15% de diferença entre as duas primeiras intenções.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você analisou enunciados enviados no ponto de extremidade sobre os quais o LUIS não tinha certeza. Depois que esses enunciados forem verificados e movidos para as intenções corretas como enunciados de exemplo, o LUIS melhorará a precisão da previsão.

> [!div class="nextstepaction"]
> [Saiba como usar padrões](luis-tutorial-pattern.md)
