---
title: 'Como: Adicionar validações a parâmetros de comando personalizados (visualização)'
titleSuffix: Azure Cognitive Services
description: Neste artigo, adicione validações aos parâmetros de comando personalizado
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 64e092405686caca7baeaf58f19d577a3f80e169
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506929"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Como: Adicionar validações a parâmetros de comando personalizados (visualização)

Neste artigo, você aprenderá a adicionar validações aos parâmetros e solicitar a correção.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter concluído as etapas nos seguintes artigos:

- [Início rápido: criar um comando personalizado (versão prévia)](./quickstart-custom-speech-commands-create-new.md)
- [Início rápido: criar um comando personalizado com parâmetros (versão prévia)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Criar um comando settemperaturas

Para demonstrar validações, vamos criar um novo comando, permitindo que o usuário defina a temperatura.

1. Abra o aplicativo de comandos personalizados criado anteriormente no [Speech Studio](https://speech.microsoft.com/)
1. Criar um novo comando **Settemperatura**
1. Adicionar um parâmetro para a temperatura de destino
1. Adicionar uma validação para o parâmetro de temperatura
   > [!div class="mx-imgBorder"]
   > ![adicionar uma validação de intervalo](media/custom-speech-commands/validations-add-temperature.png)

   | Configuração           | Valor sugerido                                          | DESCRIÇÃO                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Nome              | Temperatura                                              | Um nome descritivo para o parâmetro de comando                                                    |
   | Obrigatório          | true                                                     | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando |
   | Modelo de resposta | "Qual temperatura você deseja?"                       | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido                              |
   | Tipo              | Número                                                   | O tipo de parâmetro, como número, Cadeia de caracteres ou data e hora                                      |
   | Validação        | Valor mínimo: 60, valor máximo: 80                             | Para parâmetros numéricos, o intervalo de valores permitido para o parâmetro                              |
   | Modelo de resposta | "Desculpe, só posso definir entre 60 e 80 graus"        | Solicitar um valor atualizado se a validação falhar                                       |

1. Adicionar algumas frases de exemplo

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Adicionar uma regra de conclusão para confirmar o resultado

   | Configuração    | Valor sugerido                                         | DESCRIÇÃO                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra  | Mensagem de confirmação                                    | Um nome que descreve a finalidade da regra          |
   | Condições | Parâmetro necessário-temperatura                        | Condições que determinam quando a regra pode ser executada    |
   | Ações    | SpeechResponse-"Ok, configurando para {temperatura} graus" | A ação a ser tomada quando a condição da regra for verdadeira |

> [!TIP]
> Este exemplo usa uma resposta de fala para confirmar o resultado. Para obter exemplos sobre como concluir o comando com uma ação de cliente, consulte: [como realizar comandos no cliente com o SDK de fala (versão prévia)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Experimentar

Selecione o painel de teste e tente algumas interações.

- Entrada: defina a temperatura como 72 graus
- Saída: "Ok, definindo como 72 graus"

- Entrada: defina a temperatura como 45 graus
- Saída: "Desculpe, só posso definir entre 60 e 80 graus"
- Entrada: torne-o 72 graus
- Saída: "Ok, definindo como 72 graus"
