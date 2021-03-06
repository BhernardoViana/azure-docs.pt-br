---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 17b7626f79d7d356e3e8f3440e4a6526f2df776d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68593274"
---
O modelo de projeto do Azure Functions no Visual Studio cria um projeto que pode ser publicado em um aplicativo de funções no Azure. Você pode usar um aplicativo de funções para as funções do grupo como uma unidade lógica para gerenciamento, implantação e compartilhamento de recursos.

1. No Visual Studio, no menu **Arquivo**, selecione **Novo** > **Projeto**.

1. Na caixa de diálogo **Criar um projeto**, pesquise `functions`, escolha o modelo do **Azure Functions** e selecione **Avançar**.

1. Insira um nome para seu projeto e selecione **Criar**. O nome do aplicativo de funções deve ser válido como um namespace do C# e, portanto, não use outros caracteres não alfanuméricos, hífens ou sublinhados.

1. Em **Criar um aplicativo Azure Functions**, use as opções a seguir:

    + **Azure Functions v2 (.NET Core)** 1
    + **Gatilho HTTP**
    + **Conta de Armazenamento**: **Emulador de armazenamento**
    + **Nível de autorização**: **Anônimo** 

    | Opção      | Valor sugerido  | DESCRIÇÃO                      |
    | ------------ |  ------- |----------------------------------------- |
    | Tempo de execução do Functions | **Azure Functions 2.x <br />(.NET Core)** | Essa configuração cria um projeto de função que usa o tempo de execução versão 2.x do Azure Functions, o qual dá suporte a .NET Core. O Azure Functions 1.x dá suporte ao .NET Framework. Para obter mais informações, consulte [Direcionar o tempo de execução do Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | Modelo de função | **Gatilho HTTP** | Essa configuração cria uma função acionada por uma solicitação HTTP. |
    | **Conta de armazenamento**  | **Emulador de armazenamento** | Um gatilho HTTP não usa a conexão da conta de Armazenamento do Microsoft Azure. Todos os outros tipos de gatilho exigem uma cadeia de conexão de conta de armazenamento válida. Como o Functions requer uma conta de armazenamento, uma é atribuída ou criada quando você publica seu projeto no Azure. |
    | **Nível de autorização** | **Anônimo** | A função criada pode ser acionada por qualquer cliente sem fornecer uma chave. Essa configuração de autorização torna fácil testar a nova função. Para obter mais informações sobre chaves e autorização, consulte [Chaves de autorização](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) no [associações HTTP e webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Verifique se você definiu o **Nível de autorização** como `Anonymous`. Se você escolher o nível padrão de `Function`, é necessário que você apresente a [chave da função](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) em solicitações para acessar seu ponto de extremidade da função.
    
4. Selecione **Criar** para criar o projeto de função e a função disparada por HTTP.
