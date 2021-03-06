---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: mhopkins
ms.custom: include file
ms.openlocfilehash: 7e6bdf8f2cede2d55163932bfe8ab58e9ee96e0f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422010"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar suas credenciais no Portal do Azure

Quando o aplicativo de exemplo faz uma solicitação para o Armazenamento do Azure, ele precisa ser autorizado. Para autenticar uma solicitação, adicione suas credenciais da conta de armazenamento ao aplicativo como uma cadeia de conexão. Exiba suas credenciais da conta de armazenamento seguindo estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Localize sua cadeia de conexão.
3. Na seção **Configurações** da visão geral da conta de armazenamento, selecione **Chaves de acesso**. Aqui, você pode ver suas chaves de acesso da conta, bem como a cadeia de conexão completa para cada chave.
4. Encontre o valor da **Cadeia de conexão** em **key1** e selecione o botão **Copiar** para copiar a cadeia de conexão. Você adicionará o valor de cadeia de conexão para uma variável de ambiente na próxima etapa.

    ![Captura de tela mostrando como copiar uma cadeia de conexão do portal do Azure](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

Depois de copiar a cadeia de conexão, grave-a em uma nova variável de ambiente no computador local que executa o aplicativo. Para definir a variável de ambiente, abra uma janela de console e siga as instruções do seu sistema operacional. Substitua `<yourconnectionstring>` pela cadeia de conexão real.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Após adicionar a variável de ambiente no Windows, é necessário iniciar uma nova instância da janela de comando.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Reiniciar programas

Depois de adicionar a variável de ambiente, reinicie todos os programas em execução que precisarem ler a variável de ambiente. Por exemplo, reinicie o seu ambiente ou editor de desenvolvimento antes de continuar.
