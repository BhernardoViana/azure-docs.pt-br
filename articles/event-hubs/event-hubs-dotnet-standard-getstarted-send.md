---
title: Enviar eventos para Hubs de Eventos do Azure usando .NET Standard | Microsoft Docs
description: Começar a enviar eventos para Hubs de Eventos no .NET Standard
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: sethm
ms.openlocfilehash: f59f88d47bfcb3e761f509a3d87c6d068f44e0db
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
ms.locfileid: "28985197"
---
# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-standard"></a>Introdução ao envio de mensagens para os Hubs de Eventos do Azure no .NET Standard

> [!NOTE]
> Este exemplo está disponível em [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender).

Este tutorial mostra como escrever um aplicativo do console do .NET Core que envia um conjunto de mensagens para um hub de eventos. É possível executar a solução do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) no estado em que se encontra, substituindo as cadeias de caracteres `EhConnectionString` e `EhEntityPath` pelos valores do seu hub de eventos. Se preferir, é possível seguir as etapas deste tutorial para criar sua própria solução.

## <a name="prerequisites"></a>pré-requisitos

* [Microsoft Visual Studio 2015 ou 2017](http://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2017, mas também há suporte para o Visual Studio 2015.
* [Ferramentas do .NET Core do Visual Studio 2015 ou 2017](http://www.microsoft.com/net/core).
* Uma assinatura do Azure.
* Um namespace do hub de eventos.

Para enviar mensagens para um hub de eventos, este tutorial usa o Visual Studio para escrever um aplicativo de console do C#.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um namespace de Hubs de Eventos e um hub de eventos

A primeira etapa é usar o [Portal do Azure](https://portal.azure.com) para criar um namespace para o tipo do hub de eventos e obter as credenciais de gerenciamento que o aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento descrito [neste artigo](event-hubs-create.md) e, em seguida, continue com as próximas etapas.

## <a name="create-a-console-application"></a>Criar um aplicativo de console

Inicie o Visual Studio. No menu **Arquivo**, clique em **Novo** e em **Projeto**. Crie um aplicativo de console do .NET Core.

![Novo Projeto][1]

## <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Evento

Adicione o pacote NuGet [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) de biblioteca do .NET Standard ao projeto seguindo estas etapas: 

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
2. Clique na guia **Procurar**, pesquise por "Microsoft.Azure.EventHubs" e selecione o pacote **Microsoft.Azure.EventHubs**. Clique em **Instalar** para concluir a instalação e feche essa caixa de diálogo.

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Escrever um código para enviar mensagens ao hub de eventos

1. Adicione as instruções `using` a seguir na parte superior do arquivo Program.cs:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Adicione constantes à classe `Program` para a cadeia de conexão de Hubs de Eventos e o caminho da entidade (nome do hub de eventos individual). Substitua os espaços reservados nos colchetes pelos valores adequados que foram obtidos na criação do hub de eventos. Verifique se `{Event Hubs connection string}` é a cadeia de conexão de nível de namespace e não a cadeia de hub de eventos. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Adicione um novo método chamado `MainAsync` à classe `Program`, conforme demonstrado a seguir:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Adicione um novo método chamado `SendMessagesToEventHub` à classe `Program`, conforme demonstrado a seguir:

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Adicione o código a seguir ao método `Main` na classe `Program`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Program.cs deve ficar assim.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but this simple scenario
                // uses the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Execute o programa e certifique-se de que não existem erros.

Parabéns! Agora você enviou mensagens para um hub de eventos.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os Hubs de Eventos nos seguintes links:

* [Receber eventos de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Visão geral de Hubs de Evento](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcore.png
