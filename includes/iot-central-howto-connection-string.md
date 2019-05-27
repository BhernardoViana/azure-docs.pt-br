---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151976"
---
1. Use o `dps-keygen` utilitário de linha de comando para gerar uma cadeia de caracteres de conexão:

    Para instalar o [utilitário do gerador de chave](https://github.com/Azure/dps-keygen), execute o seguinte comando:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Para gerar uma cadeia de caracteres de conexão, execute o seguinte comando usando os detalhes de conexão que você anotou anteriormente:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Copie a cadeia de conexão do `dps-keygen` saída para uso em seu código de dispositivo.