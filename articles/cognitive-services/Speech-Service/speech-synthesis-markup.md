---
title: Linguagem de marcação de síntese de fala (SSML) – serviço de fala
titleSuffix: Azure Cognitive Services
description: Usando o Speech Synthesis Markup Language para controlar pronúncia e prosódia em texto para fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 6ffa17010f874eeb82fe8f4c367f0a0ac429979b
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815510"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Linguagem de marcação de síntese de fala (SSML)

A linguagem de marcação de síntese de fala (SSML) é uma linguagem de marcação baseada em XML que permite aos desenvolvedores especificar como o texto de entrada é convertido em fala sintetizada usando o serviço de conversão de texto em fala. Em comparação com o texto sem formatação, o SSML permite que os desenvolvedores ajustem a densidade, a pronúncia, a taxa de fala, o volume e muito mais da saída de conversão de texto em fala. Pontuação normal, como pausar após um período ou usar o intonation correto quando uma frase termina com um ponto de interrogação é manipulada automaticamente.

A implementação do serviço de fala do SSML é baseada na [linguagem de marcação de síntese de fala da World Wide Web Consortium versão 1,0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Os caracteres chinês, japonês e coreano contam como dois caracteres para cobrança. Para saber mais, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Vozes Standard, neural e Custom

Escolha entre as vozes padrão e neural ou crie sua própria voz personalizada exclusiva para seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, e 5 vozes neurais estão disponíveis em 4 idiomas e localidades. Para obter uma lista completa de vozes (neurais e padrão), localidades e idiomas com suporte, consulte [suporte para idioma](language-support.md).

Para saber mais sobre as vozes padrão, neural e personalizada, consulte [visão geral de conversão de texto em fala](text-to-speech.md).

## <a name="special-characters"></a>Caracteres especiais

Ao usar o SSML para converter a fala de texto em sintetizado, tenha em mente que, assim como acontece com XML, caracteres especiais, como aspas, apóstrofos e colchetes devem ser ignorados. Para obter mais informações, consulte [linguagem XML (XML) 1,0: Apêndice D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Elementos SSML com suporte

Cada documento SSML é criado com elementos SSML (ou marcas). Esses elementos são usados para ajustar a densidade, Prosody, volume e muito mais. As seções a seguir detalham como cada elemento é usado e quando um elemento é obrigatório ou opcional.  

> [!IMPORTANT]
> Não se esqueça de usar aspas duplas em relação aos valores de atributo. Os padrões para um XML válido bem formado requer que os valores de atributo sejam colocados entre aspas duplas. Por exemplo, `<prosody volume="90">` é um elemento válido e bem formado, mas `<prosody volume=90>` não é. O SSML pode não reconhecer valores de atributo que não estão entre aspas.

## <a name="create-an-ssml-document"></a>Criar um documento SSML

`speak` é o elemento raiz e é **necessário** para todos os documentos SSML. O elemento `speak` contém informações importantes, como versão, idioma e definição de vocabulário de marcação.

**Sintaxe**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| version | Indica a versão da especificação SSML usada para interpretar a marcação do documento. A versão atual é 1,0. | obrigatórios |
| xml:lang | Especifica o idioma do documento raiz. O valor pode conter um código de idioma de duas letras minúsculas (por exemplo, **en**) ou o código de idioma e o país/região em maiúsculas (por exemplo, **en-US**). | obrigatórios |
| xmlns | Especifica o URI para o documento que define o vocabulário de marcação (os tipos de elementos e nomes de atributo) do documento SSML. O URI atual é https://www.w3.org/2001/10/synthesis. | obrigatórios |

## <a name="choose-a-voice-for-text-to-speech"></a>Escolha uma voz para conversão de texto em fala

O elemento `voice` é obrigatório. Ele é usado para especificar a voz que é usada para conversão de texto em fala.

**Sintaxe**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| Nome | Identifica a voz usada para saída de texto para fala. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech). | obrigatórios |

**Exemplo**

> [!NOTE]
> Este exemplo usa o `en-US-Jessa24kRUS` Voice. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Usar várias vozes

Dentro do elemento `speak`, você pode especificar várias vozes para a saída de conversão de texto em fala. Essas vozes podem estar em idiomas diferentes. Para cada voz, o texto deve ser encapsulado em um elemento `voice`.

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| Nome | Identifica a voz usada para saída de texto para fala. Para obter uma lista completa de vozes com suporte, consulte [suporte a idiomas](language-support.md#text-to-speech). | obrigatórios |

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Ajustar estilos de fala

> [!IMPORTANT]
> Este recurso funcionará apenas com vozes neurais.

Por padrão, o serviço de conversão de texto em fala sintetiza o texto usando um estilo de fala neutro para vozes padrão e neural. Com as vozes neurais, você pode ajustar o estilo de fala para Express cheerfulness, empatia ou sentimentos com o elemento `<mstts:express-as>`. Esse é um elemento opcional exclusivo para o serviço de fala.

Atualmente, os ajustes de estilo de fala têm suporte para essas vozes neurais:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

As alterações são aplicadas no nível da frase e o estilo varia de acordo com a voz. Se não houver suporte para um estilo, o serviço retornará a voz no estilo de fala neutro padrão.

**Sintaxe**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| type | Especifica o estilo de fala. Atualmente, os estilos de fala são específicos da voz. | Obrigatório se estiver ajustando o estilo de fala para uma voz neural. Se estiver usando `mstts:express-as`, o tipo deverá ser fornecido. Se um valor inválido for fornecido, esse elemento será ignorado. |

Use esta tabela para determinar quais estilos de fala têm suporte para cada voz neural.

| Voz | Type | Descrição |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Expressa uma emoção positiva e feliz |
| | type=`empathy` | Expressa uma noção de preocupar e compreensão |
| | type=`chat` | Fale em um tom casual e relaxado |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Expressa um tom formal, semelhante a difusões de notícias |
| | type=`sentiment` | Transmite uma mensagem ou uma história ao toque |

**Exemplo**

Este trecho de SSML ilustra como o elemento `<mstts:express-as>` é usado para alterar o estilo de fala para `cheerful`.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Adicionar ou remover uma quebra/pausa

Use o elemento `break` para inserir pausas (ou interrupções) entre palavras ou evitar pausas adicionadas automaticamente pelo serviço de conversão de texto em fala.

> [!NOTE]
> Use esse elemento para substituir o comportamento padrão de conversão de texto em fala (TTS) por uma palavra ou frase se a fala sintetizada para aquela palavra ou frase parecer innatural. Defina `strength` como `none` para evitar uma interrupção prosódico, que é automaticamente inserida pelo serviço de conversão de texto em fala.

**Sintaxe**

```xml
<break strength="string" />
<break time="string" />
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| segurança | Especifica a duração relativa de uma pausa usando um dos seguintes valores:<ul><li>Nenhum</li><li>x-fraco</li><li>baixas</li><li>médio (padrão)</li><li>fortes</li><li>x-Strong</li></ul> | Opcional |
| time | Especifica a duração absoluta de uma pausa em segundos ou milissegundos. Exemplos de valores válidos são 2S e 500 | Opcional |

| Força | Descrição |
|----------|-------------|
| Nenhum, ou se nenhum valor for fornecido | 0 ms |
| x-fraco | 250 ms |
| baixas | 500 ms |
| média | 750 ms |
| fortes | 1000 MS |
| x-Strong | 1250 MS |


**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Especificar parágrafos e frases

os elementos `p` e `s` são usados para denotar parágrafos e frases, respectivamente. Na ausência desses elementos, o serviço de conversão de texto em fala determina automaticamente a estrutura do documento SSML.

O elemento `p` pode conter texto e os seguintes elementos: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`e `s`.

O elemento `s` pode conter texto e os seguintes elementos: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`e `sub`.

**Sintaxe**

```XML
<p></p>
<s></s>
```

**Exemplo**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Usar fonemas para melhorar a pronúncia

O elemento `ph` é usado para a pronúncia fonética em documentos SSML. O elemento `ph` pode conter apenas texto, nenhum outro elemento. Sempre forneça uma fala legível por humanos como um fallback.

Os alfabetos fonéticos são compostos por telefones, que são compostos por letras, números ou caracteres, às vezes em combinação. Cada telefone descreve um som exclusivo de fala. Isso é diferente do alfabeto latino, em que qualquer letra pode representar vários sons falados. Considere as diferentes pronúncias da letra "c" nas palavras "colírio" e "cessação" ou as diferentes pronúncias da combinação de letras "th" nas palavras "coisas" e "essas".

**Sintaxe**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| alfabeto | Especifica o alfabeto fonético a ser usado ao resumir a pronúncia da cadeia de caracteres no atributo `ph`. A cadeia de caracteres que especifica o alfabeto deve ser especificada em letras minúsculas. A seguir estão os possíveis alfabetos que você pode especificar.<ul><li>IPA &ndash; alfabeto fonético internacional</li><li>&ndash; SAPI Speech API conjunto de telefone</li><li>Conjunto de telefone universal &ndash; ups</li></ul>O alfabeto aplica-se somente ao fonema no elemento. Para obter mais informações, consulte [referência de alfabeto fonético](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Opcional |
| espaço | Uma cadeia de caracteres que contém telefones que especificam a pronúncia da palavra no elemento `phoneme`. Se a cadeia de caracteres especificada contiver telefones não reconhecidos, o serviço de conversão de texto em fala (TTS) rejeitará todo o documento SSML e produzirá nenhuma saída de fala especificada no documento. | Necessário se estiver usando fonemas. |

**Exemplos**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Ajustar Prosody

O elemento `prosody` é usado para especificar alterações para pitch, Countour, intervalo, taxa, duração e volume para a saída de conversão de texto em fala. O elemento `prosody` pode conter texto e os seguintes elementos: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`e `s`.

Como os valores de atributo prosódico podem variar em um intervalo maior, o reconhecedor de fala interpreta os valores atribuídos como uma sugestão de como os valores reais de prosódico da voz selecionada devem ser. O serviço de conversão de texto em fala limita ou substitui valores que não têm suporte. Exemplos de valores sem suporte são um tom de 1 MHz ou um volume de 120.

**Sintaxe**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| zumbi | Indica o tom de linha de base para o texto. Você pode expressar o timbre como:<ul><li>Um valor absoluto, expresso como um número seguido por "Hz" (hertz). Por exemplo, 600Hz.</li><li>Um valor relativo, expresso como um número precedido por "+" ou "-" e seguido por "Hz" ou "St", que especifica um valor para alterar a densidade. Por exemplo: + 80Hz ou-2st. O "St" indica que a unidade de alteração é semitone, que é metade de um tom (uma metade) na escala diatonic padrão.</li><li>Um valor constante:<ul><li>x-baixo</li><li>baixa</li><li>média</li><li>alto</li><li>x-alto</li><li>padrão</li></ul></li></ul>. | Opcional |
| Contour | Não há suporte para contorno em vozes neurais. A delimitação representa as alterações em pitch para o conteúdo de fala como uma matriz de destinos em posições de tempo especificadas na saída de fala. Cada destino é definido por conjuntos de pares de parâmetros. Por exemplo: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>O primeiro valor em cada conjunto de parâmetros Especifica o local da alteração de timbre como uma porcentagem da duração do texto. O segundo valor especifica o valor para aumentar ou diminuir a densidade, usando um valor relativo ou um valor de enumeração para pitch (consulte `pitch`). | Opcional |
| range  | Um valor que representa o intervalo de timbre do texto. Você pode expressar `range` usando os mesmos valores absolutos, valores relativos ou valores de enumeração usados para descrever `pitch`. | Opcional |
| taxa  | Indica a taxa de fala do texto. Você pode expressar `rate` como:<ul><li>Um valor relativo, expresso como um número que atua como um multiplicador do padrão. Por exemplo, um valor de *1* resulta em nenhuma alteração na taxa. Um valor de *0,5* resulta em uma metade da taxa. Um valor de *3* resulta em uma viagem da taxa.</li><li>Um valor constante:<ul><li>x-lento</li><li>lento</li><li>média</li><li>rápido</li><li>x-rápido</li><li>padrão</li></ul></li></ul> | Opcional |
| duration  | O período de tempo que deve decorrer enquanto o serviço de síntese de fala (TTS) lê o texto, em segundos ou milissegundos. Por exemplo, *2s* ou *1800ms*. | Opcional |
| volume  | Indica o nível de volume da voz de fala. Você pode expressar o volume como:<ul><li>Um valor absoluto, expresso como um número no intervalo de 0,0 a 100,0, do mais *baixo* para o mais *alto*. Por exemplo, 75. O padrão é 100,0.</li><li>Um valor relativo, expresso como um número precedido por "+" ou "-" que especifica um valor para alterar o volume. Por exemplo, + 10 ou-5,5.</li><li>Um valor constante:<ul><li>silencioso</li><li>x-soft</li><li>pessoais</li><li>média</li><li>alto</li><li>x-alto</li><li>padrão</li></ul></li></ul> | Opcional |

### <a name="change-speaking-rate"></a>Alterar taxa de fala

A taxa de fala pode ser aplicada a vozes padrão na palavra ou no nível da sentença. Enquanto a taxa de fala só pode ser aplicada a vozes neurais no nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Alterar volume

As alterações de volume podem ser aplicadas a vozes padrão na palavra ou no nível da sentença. Enquanto as alterações de volume só podem ser aplicadas a vozes neurais no nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Alterar densidade

As alterações de timbre podem ser aplicadas a vozes padrão na palavra ou no nível da sentença. Enquanto as alterações de pitch só podem ser aplicadas a vozes neurais no nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Alterar contorno de densidade

> [!IMPORTANT]
> Não há suporte para alterações de delimitação de inclinação com vozes neurais.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>elemento digamos-como  

`say-as` é um elemento opcional que indica o tipo de conteúdo (como número ou data) do texto do elemento. Isso fornece orientação para o mecanismo de síntese de fala sobre como pronunciar o texto. 

**Sintaxe**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| interpretar como | Indica o tipo de conteúdo do texto do elemento. Para obter uma lista de tipos, consulte a tabela abaixo. | obrigatórios |
| format | Fornece informações adicionais sobre a formatação exata do texto do elemento para tipos de conteúdo que podem ter formatos ambíguos. O SSML define formatos para tipos de conteúdo que os usam (consulte a tabela abaixo). | Opcional |
| detalhes | Indica o nível de detalhe a ser falado. Por exemplo, esse atributo pode solicitar que o mecanismo de síntese de fala pronuncia as marcas de pontuação. Não há valores padrão definidos para `detail`. | Opcional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

A seguir estão os tipos de conteúdo com suporte para os atributos `interpret-as` e `format`. Inclua o atributo `format` somente se `interpret-as` estiver definido como data e hora.

| interpretar como | format | Interpretação |
|--------------|--------|----------------|
| endereço | | O texto é falado como um endereço. O mecanismo de síntese de fala pronuncia:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Como "Estou em 150th tribunal norte leste de Redmond, Washington." |
| Cardinal, número | | O texto é falado como um número cardinal. O mecanismo de síntese de fala pronuncia:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Como "há três alternativas". |
| caracteres, soletração | | O texto é falado como letras individuais (escritas). O mecanismo de síntese de fala pronuncia:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Como "T E S T". |
| date  | dmy, MDY, ymd, ydm, YM, My, MD, DM, d, m, y | O texto é falado como uma data. O atributo `format` especifica o formato da data (*d = dia, m = mês e y = ano*). O mecanismo de síntese de fala pronuncia:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Como "hoje é de outubro de nineteenth 2016." |
| dígitos, number_digit | | O texto é falado como uma sequência de dígitos individuais. O mecanismo de síntese de fala pronuncia:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Como "1 2 3 4 5 6 7 8 9". |
| fração | | O texto é falado como um número fracionário. O mecanismo de síntese de fala pronuncia:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Como "três oitavos de uma polegada". |
| ordinal  | | O texto é falado como um número ordinal. O mecanismo de síntese de fala pronuncia:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Como "Selecione a terceira opção". |
| telefone  | | O texto é falado como um número de telefone. O atributo `format` pode conter dígitos que representam um código de país. Por exemplo, "1" para o Estados Unidos ou "39" para a Itália. O mecanismo de síntese de fala pode usar essas informações para orientar sua pronúncia de um número de telefone. O número de telefone também pode incluir o código do país e, nesse caso, tem precedência sobre o código do país na `format`. O mecanismo de síntese de fala pronuncia:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Como "meu número é código de área 8 8 8 5 5 5 1 2 1 2." |
| time | hms12, hms24 | O texto é falado como uma hora. O atributo `format` especifica se a hora é especificada usando um relógio de 12 horas (hms12) ou um relógio de 24 horas (hms24). Use dois-pontos para separar números que representam horas, minutos e segundos. Estes são exemplos de tempo válidos: 12:35, 1:14:32, 08:15 e 02:50:45. O mecanismo de síntese de fala pronuncia:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Como "o treinamento faz parte de quatro A M". |

**Uso**

O elemento `say-as` pode conter apenas texto.

**Exemplo**

O mecanismo de síntese de fala fala o exemplo a seguir como "sua primeira solicitação foi para uma sala em outubro de nineteenth 20 10 com chegada antecipada às 12 35 P M."
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
    <p>
    Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
    on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
    </p>
</speak>
```


## <a name="add-recorded-audio"></a>Adicionar áudio gravado

`audio` é um elemento opcional que permite que você insira áudio MP3 em um documento SSML. O corpo do elemento de áudio pode conter uma marcação de texto sem formatação ou SSML que será falada se o arquivo de áudio estiver indisponível ou não puder ser tocado. Além disso, o elemento `audio` pode conter texto e os seguintes elementos: `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as`e `sub`.

Qualquer áudio incluído no documento SSML deve atender a estes requisitos:

* O MP3 deve ser hospedado em um ponto de extremidade HTTPS acessível pela Internet. O HTTPS é necessário e o domínio que hospeda o arquivo MP3 deve apresentar um certificado SSL válido e confiável.
* O MP3 deve ser um arquivo MP3 válido (MPEG v2).
* A taxa de bits deve ser de 48 kbps.
* A taxa de amostra deve ser de 16000 Hz.
* O tempo total combinado para todos os arquivos de texto e áudio em uma única resposta não pode exceder 90 (90) segundos.
* O MP3 não deve conter nenhuma informação confidencial ou específica do cliente.

**Sintaxe**

```xml
<audio src="string"/></audio>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| src | Especifica o local/URL do arquivo de áudio. | Necessário se estiver usando o elemento Audio em seu documento SSML. |

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>Adicionar áudio em segundo plano

O elemento `mstts:backgroundaudio` permite que você adicione áudio em segundo plano aos seus documentos de SSML (ou misture um arquivo de áudio com conversão de texto em fala). Com `mstts:backgroundaudio` você pode executar um loop em um arquivo de áudio em segundo plano, esmaecer no início da conversão de texto em fala e desaparecer no final da conversão de texto em fala.

Se o áudio de fundo fornecido for menor do que a conversão de texto em fala ou desaparecer, ele fará um loop. Se for maior do que a conversão de texto em fala, ele será interrompido quando o esmaecimento for concluído.

Somente um arquivo de áudio de segundo plano é permitido por documento SSML. No entanto, você pode intercalar `audio` marcas dentro do elemento `voice` para adicionar mais áudio ao seu documento SSML.

**Sintaxe**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atributos**

| Atributo | Descrição | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| src | Especifica o local/URL do arquivo de áudio de segundo plano. | Necessário se estiver usando áudio de fundo em seu documento SSML. |
| volume | Especifica o volume do arquivo de áudio de segundo plano. **Valores aceitos**: `0` a `100` inclusive. O valor padrão é `1`. | Opcional |
| FadeIn | Especifica a duração do áudio em segundo plano desaparecer em milissegundos. O valor padrão é `0`, que é o equivalente a sem fade in. **Valores aceitos**: `0` a `10000` inclusive.  | Opcional |
| FadeOut | Especifica a duração do esmaecimento de áudio em segundo plano em milissegundos. O valor padrão é `0`, que é o equivalente a sem desaparecer. **Valores aceitos**: `0` a `10000` inclusive.  | Opcional |

**Exemplo**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Próximos passos

* [Suporte de linguagem: vozes, localidades, idiomas](language-support.md)
