---
title: Como usar retornos de chamada da sessão com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar retornos de chamada da sessão com um modelo de Aprendiz de Conversa.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 656d028082d56b8f094e83363e5189b163581c53
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703957"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Como usar retornos de chamada da sessão com um modelo de Aprendiz de Conversa

Este tutorial apresenta as sessões, como elas são manipuladas e onSessionStart do Conversation Learner e onSessionEnd dos retornos de chamada.

## <a name="video"></a>Vídeo

[![Visualização de Tutorial de Retornos de Chamada de Sessão](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Requisitos
O tutorial requer que o bot "tutorialSessionCallbacks.ts" esteja em execução.

    npm run tutorial-session-callbacks

## <a name="details"></a>Detalhes
Ele apresenta o conceito de uma sessão, como as sessões são lidadas por padrão e como você pode substituir esse comportamento.

No Conversation Learner, uma sessão representa uma troca ininterrupta e interativa com o bot. As sessões pode ter vários voltas, mas programaticamente ser encerradas se ficarem inativas por mais de trinta minutos.  Consulte a página de ajuda em "Limites” para informações sobre como mudar a duração de tempo limite da sessão padrão.

Esse período de inatividade fará com que o bot crie uma nova sessão e redefina a rede neural recorrente para seu estado inicial. Todos os valores de entidade serão limpo por padrão. Esse comportamento padrão de limpar valores de entidade pode ser alterado conforme mostrado abaixo.

### <a name="load-the-demo-model"></a>Carregar o Modelo de Demonstração

Na interface do usuário da Web, clique em "Importar Tutoriais" e selecione o modelo de nome "Tutorial-13-LogDialogs".

### <a name="code-for-the-callbacks"></a>Código para retornos de chamada

O código de exemplo para dois retornos de chamada do modelo podem ser encontrados em: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: este método define a entidade BotName.
- OnSessionEndCallback: é possível especificar o que você quer preservar. Isso limpará todas as entidades, exceto o nome de usuário e o telefone.

Cada retorno de chamada é opcional.

### <a name="actions"></a>Ações

Quatro ações são definidas no Modelo. As Ações existentes são exibidas na exibição de grade para "Ações"

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Criando uma ação do final da sessão (para invocação de retorno de chamada)

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. Selecione "ENDSESSION" para o "Tipo de entidade".
3. No campo "Dados...", digite "Concluído"
4. Clique no botão “Criar”.

### <a name="edit-an-existing-action"></a>Editar uma ação existente

1. Selecione "$UserName, você está no $UserLocation" Ação da exibição de grade.
2. Desmarque a caixa de seleção "Aguardar Resposta".
3. Clique no botão "Salvar".

### <a name="chaining-actions"></a>Encadeando ações

1. No painel esquerdo, clique em "Treinar Diálogos" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "oi".
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta "Oi, Eu sou a Botty. Qual é o seu nome?"
5. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Lars"
6. Selecione a resposta "Oi Lars. Qual é o seu número de telefone?"
7. No painel de chat, onde está escrito "Digite sua mensagem...", digite "555-555-5555"
8. Clique no botão "Ações de Pontuação".
9. Selecione a resposta, "Você pode dizer para Botty a sua localização, Lars?"
10. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Seattle"
11. Clique no botão "Ações de Pontuação".
12. Selecione a resposta "Lars, você está em Seattle"
13. Selecione a resposta: "Concluído"
14. Clique no botão "Salvar".

### <a name="testing-the-model"></a>Testar o modelo

1. No painel esquerdo, clique em "Registrar Diálogos" e, em seguida, no botão "Novo Registro de Diálogo".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "oi"
3. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Lars"
4. No painel de chat, onde está escrito "Digite sua mensagem...", digite "555-555-5555"
5. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Seattle"
    - Agora todos os valores de entidade, excluindo o local devem ter sido preservados.
6. No painel de chat, onde está escrito "Digite sua mensagem...", digite "oi"
7. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Detroit"
8. Clique no botão de "Tempo limite da sessão".
    - Clicar nesse botão exercita a resposta do bot para períodos longos de inatividade
9. Clique no botão "OK".
10. Clique no botão "Teste Concluído".

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chamadas de API](./14-api-calls.md)
