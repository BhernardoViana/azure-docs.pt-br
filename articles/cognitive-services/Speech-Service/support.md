---
title: Opções de Ajuda e suporte
titlesuffix: Azure Cognitive Services
description: Como obter ajuda e suporte para perguntas e problemas ao criar aplicativos que se integram com o Serviço de Fala
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 9e975bbdcec2b45d67f910ac1a83a71ed252ebda
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606271"
---
# <a name="support-and-help-options"></a>Opções de Ajuda e suporte

Você apenas começando a explorar a funcionalidade dos serviços de fala? Você está implementando um novo recurso para o seu aplicativo? Aqui estão sugestões onde você pode obter ajuda como desenvolvedor.

> [!div class="checklist"]
> * Mantenha-se informado sobre novos desenvolvimentos dos *Serviços Cognitivos do Azure*, ou localizar as notícias mais recentes relacionadas ao *Serviço de Fala*.
> * Notas de versão contêm informações para todas as versões.
> * Pesquise para ver se o problema foi discutido pela comunidade, ou se a documentação existente para o recurso que você deseja implementar já existe.
> * Se você não conseguir encontrar uma resposta satisfatória, faça uma pergunta *Stack Overflow*.
> * Se você encontrar um problema com um dos exemplos no GitHub, gere um problema *GitHub*.
> * Pesquisar uma solução no *fórum UserVoice*.

## <a name="stay-informed"></a>Continue informado

Notícias sobre os serviços Cognitivos são coletadas no [blog de Serviços Cognitivos](https://azure.microsoft.com/blog/topics/cognitive-services/). Para obter as informações mais recentes sobre os serviços de fala, acompanhar os [blog de serviços de fala](https://azure.microsoft.com/blog/tag/speech-service/).

## <a name="release-notes"></a>Notas de versão

O [notas de versão](https://aka.ms/csspeech/whatsnew) são atualizados assim que uma nova versão está disponível. As notas contêm informações sobre novos recursos, aprimoramentos e correções de bugs.

## <a name="search"></a>Search

Você pode encontrar a resposta de que precisa na documentação, as amostras ou respostas para as perguntas do [Stack Overflow](https://www.stackoverflow.com) ou nas amostras.

### <a name="scoped-search"></a>Pesquisa com Escopo

Para resultados mais rápidos, especifique sua pesquisa para o Stack Overflow, documentação e amostras de código usando a seguinte consulta em seu [mecanismo de pesquisa favorito](https://bing.com):

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples)
```

No campo *{Seus Termos de Pesquisa}* estão as palavras-chave de pesquisa.

## <a name="create-an-azure-support-request"></a>Como criar uma solicitação de suporte do Azure

Os clientes do Azure podem criar e gerenciar solicitações de suporte no portal do Azure.

* [Portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
* [Portal do Azure para o governo dos Estados Unidos](https://portal.azure.us)

## <a name="post-a-question-to-stack-overflow"></a>Postar uma pergunta no Stack Overflow

O Stack Overflow é o canal preferido para o desenvolvimento de questões relacionadas. É onde os membros da comunidade e membros da equipe da Microsoft estão diretamente envolvidos em ajudá-lo a resolver seus problemas.

Se não conseguir encontrar uma resposta para seu problema por meio da pesquisa, envie uma nova pergunta ao Stack Overflow. Use uma das seguintes tags ao formular a pergunta:

|Componente/Área  |tags  |
|---------|---------|
|Reconhecimento de fala |[[microsoft-cognitivas + fala em texto]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+speech-to-text)|
|Síntese de fala |[[microsoft-cognitivas + texto em fala]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+text-to-speech)|
|Tradução de Fala |[[microsoft-cognitivas + conversão]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+translation)|
|Intenção de fala |[[microsoft-cognitivas + luis]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+luis)|
|SDK de fala geral |[[microsoft-cognitivas + microsoft-fala-api]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+microsoft-speech-api)|
| Assistentes de voz-first Virtual (versão prévia) | [[microsoft-cognitive+virtual-assistant+botframework]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+virtual-assistant+botframework) |

> [!TIP]
> As seguintes postagens do Stack Overflow contêm dicas sobre as perguntas de formulário e adicione o código-fonte. As diretrizes a seguir podem ajudar a aumentar as chances de os membros da comunidade avaliarem e responderem à sua pergunta rapidamente:  
> * [Como fazer uma boa pergunta?](https://stackoverflow.com/help/how-to-ask)
> * [Como criar um exemplo mínimo, completo e verificável](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Criar um problema do GitHub

Normalmente, os exemplos são lançados como código-fonte aberto. Para perguntas e problemas, crie uma *problema* no respectivo repositório do GitHub. Você pode enviar uma solicitação de pull, também. A lista a seguir contém links para os repositórios de exemplo:

* [SDK da fala](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues)
* [SDK de Dispositivos de Fala](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/issues)

Você pode criar um relatório de bug, solicitação de recurso, ou faça uma pergunta geral e compartilhar as melhores práticas. Para relatórios de bugs, siga o modelo fornecido:

**Descrever o bug**

Uma descrição clara e concisa sobre o que o bug é.

**Para reproduzir**

Etapas para reproduzir o comportamento:
1. ...
2. ...

**Comportamento esperado**

Descrição clara e concisa do que você esperava que acontecesse.

**Versão da SDK de Reconhecimento de Fala dos serviços Cognitivos**

Qual versão do SDK você está usando.

**Sistema Operacional da Plataforma e Linguagem de Programação**

 - Sistema operacional: [por exemplo, Windows, Linux, Android, iOS,...] - seja específico
 - Hardware - x64, x86, ARM,...
 - Navegador [por exemplo, Chrome, Safari] (se aplicável) – seja específico

**Contexto adicional**

 - Mensagens de erro, informações de log, rastreamento ...
 - Se você relatar um erro para uma interação de serviço específica, relate a SessionId e a hora (incluindo fuso horário) dos incidentes relatados. A SessionId é informada em todos os retornos de chamada/eventos que você receber.
 - Quaisquer outras informações adicionais


## <a name="uservoice-forum"></a>Fórum do UserVoice

Compartilhe suas ideias para criar Serviços Cognitivos e as APIs acompanhantes funcionam melhor para os aplicativos desenvolvidos. Use nossa crescente Base de conhecimento para encontrar respostas para perguntas comuns sobre:

[UserVoice](https://cognitive.uservoice.com/)
