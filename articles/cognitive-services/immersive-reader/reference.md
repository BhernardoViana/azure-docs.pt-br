---
title: Referência do SDK do leitor de imersão
titleSuffix: Azure Cognitive Services
description: O SDK do leitor de imersão é uma biblioteca JavaScript que permite integrar o leitor de imersão ao seu aplicativo Web.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 09244b634fa2603a7dc92af3c78d171f8d6bd9df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903102"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Guia de referência do SDK do leitor de imersão

O SDK do leitor de imersão é uma biblioteca JavaScript que permite integrar o leitor de imersão ao seu aplicativo Web.

## <a name="functions"></a>Funções

O SDK expõe as funções:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Inicia o leitor de imersão dentro de um `iframe` em seu aplicativo Web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

### <a name="parameters"></a>Parâmetros

| NOME | Digite | DESCRIÇÃO |
| ---- | ---- |------------ |
| `token` | cadeia de caracteres | O token de autenticação do Azure AD. Consulte o [How-to de autenticação do Azure ad](./azure-active-directory-authentication.md). |
| `subdomain` | cadeia de caracteres | O subdomínio personalizado do seu recurso de leitor de imersão no Azure. Consulte o [How-to de autenticação do Azure ad](./azure-active-directory-authentication.md). |
| `content` | [Conteúdo](#content) | Um objeto que contém o conteúdo a ser mostrado no leitor de imersão. |
| `options` | [Opções](#options) | Opções para configurar determinados comportamentos do leitor de imersão. Opcional. |

### <a name="returns"></a>Retornos

Retorna um `Promise<HTMLDivElement>`, que resolve quando o leitor de imersão é carregado. O `Promise` é resolvido para um elemento `div` cujo filho é um elemento `iframe` que contém a página do leitor de imersão.

### <a name="exceptions"></a>Exceções

O `Promise` retornado será rejeitado com um objeto [`Error`](#error) se o leitor de imersão não for carregado. Para obter mais informações, consulte os [códigos de erro](#error-codes).

## <a name="close"></a>fechar

Fecha o leitor de imersão.

Um exemplo de caso de uso para essa função é se o botão sair estiver oculto definindo ```hideExitButton: true``` em [Opções](#options). Em seguida, um botão diferente (por exemplo, seta para voltar do cabeçalho móvel) pode chamar essa ```close``` função quando ele é clicado.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Essa função define e atualiza os elementos do botão de leitura imersiva do documento. Se ```options.elements``` for fornecido, essa função processará botões dentro de ```options.elements```. Caso contrário, os botões serão renderizados dentro dos elementos do documento que têm a classe ```immersive-reader-button```.

Essa função é chamada automaticamente pelo SDK quando a janela é carregada.

Consulte [atributos opcionais](#optional-attributes) para obter mais opções de renderização.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parâmetros

| NOME | Digite | DESCRIÇÃO |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Opções para configurar determinados comportamentos da função renderButtons. Opcional. |

## <a name="types"></a>Tipos

### <a name="content"></a>Conteúdo

Contém o conteúdo a ser mostrado no leitor de imersão.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Codificação

Um único bloco de dados, que será passado para o conteúdo do leitor de imersão.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>Tipos MIME com suporte

| Tipo MIME | DESCRIÇÃO |
| --------- | ----------- |
| texto/sem formatação | Texto sem formatação. |
| texto/html | Conteúdo HTML. [Saiba mais](#html-support)|
| aplicativo/MathML + XML | MathML (matematica Markup Language). [Saiba mais](https://developer.mozilla.org/en-US/docs/Web/MathML).
| application/vnd. openxmlformats-officeDocument. WordprocessingML. Document | Documento de formato Microsoft Word. docx.

### <a name="html-support"></a>Suporte a HTML
| HTML | Conteúdo com suporte |
| --------- | ----------- |
| Estilos de fonte | Negrito, itálico, sublinhado, código, tachado, sobrescrito, subscrito |
| Listas não ordenadas | Disco, círculo, quadrado |
| Listas ordenadas | Decimal, superior-alfa, inferior-alfa, maiúsculo-Romano, minúsculo |
| Hiperlinks | Em breve |

Marcas sem suporte serão renderizadas comparativamente. Não há suporte para imagens e tabelas no momento.

### <a name="options"></a>Opções

Contém propriedades que configuram determinados comportamentos do leitor de imersão.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Opções para renderizar os botões de leitura imersiva.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Erro

Contém informações sobre o erro.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Códigos do Erro

| Código | DESCRIÇÃO |
| ---- | ----------- |
| BadArgument | O argumento fornecido é inválido, consulte `message` para obter detalhes. |
| Tempo limite | Falha ao carregar o leitor de imersão no tempo limite especificado. |
| TokenExpired | O token fornecido expirou. |
| Limitado | O limite de taxa de chamada foi excedido. |

## <a name="launching-the-immersive-reader"></a>Iniciando o leitor de imersão

O SDK fornece o estilo padrão para o botão iniciar o leitor de imersão. Use o atributo de classe `immersive-reader-button` para habilitar esse estilo.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Atributos opcionais

Use os atributos a seguir para configurar a aparência do botão.

| Atributo | DESCRIÇÃO |
| --------- | ----------- |
| `data-button-style` | Define o estilo do botão. Pode ser `icon`, `text` ou `iconAndText`. Usa `icon` como padrão. |
| `data-locale` | Define a localidade. Por exemplo, `en-US` ou `fr-FR`. O padrão é `en`em inglês. |
| `data-icon-px-size` | Define o tamanho do ícone em pixels. O padrão é 20px. |

## <a name="browser-support"></a>Suporte ao navegador

Use as versões mais recentes dos seguintes navegadores para obter a melhor experiência com o leitor de imersão.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Próximas etapas

* Explorar o [SDK da Leitura Avançada no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Início rápido: criar um aplicativo Web que inicia o leitor deC#imersão ()](./quickstart.md)
