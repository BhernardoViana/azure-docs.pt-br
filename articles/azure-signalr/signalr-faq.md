---
title: Perguntas frequentes sobre o Serviço do Azure SignalR
description: Perguntas frequentes sobre o Serviço do Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 12/23/2018
ms.author: zhshang
ms.openlocfilehash: 6b3ddf7d8069e689231b9dcb6f0f074e84052511
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663257"
---
# <a name="azure-signalr-service-faq"></a>Perguntas frequentes sobre o Serviço do Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>O Serviço do Azure SignalR está pronto para uso em produção?

Sim.
Para nosso comunicado de disponibilidade geral, confira [Serviço do Azure SignalR agora disponível](https://azure.microsoft.com/en-us/blog/azure-signalr-service-now-generally-available/). 

O [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) é totalmente compatível.

O suporte para ASP.NET SignalR ainda está na *versão prévia pública*. Aqui está um [exemplo de código](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>A Conexão de cliente é fechada com a mensagem de erro "Nenhum servidor disponível". O que isso significa?

Esse erro ocorre somente quando os clientes estão enviando mensagens para o Serviço do SignalR.

Se você não tiver nenhum servidor de aplicativos e usar apenas a API REST do Serviço do SignalR, esse comportamento ocorrerá **por padrão**.
Na arquitetura sem servidor, as conexões de cliente estão no modo de **ESCUTA** e não enviarão mensagens para o Serviço do SignalR.
Leia mais sobre a [API REST](./signalr-quickstart-rest-api.md).

Se você tem servidores de aplicativos, essa mensagem de erro significa que nenhum servidor de aplicativos está conectado à instância do Serviço do SignalR.

As possíveis causas são:
- Nenhum servidor de aplicativos está conectado ao Serviço do SignalR. Verifique os logs do servidor de aplicativos para erros de conexão possíveis. Esse caso é raro na configuração de alta disponibilidade com mais de um servidor de aplicativos.
- Há problemas de conectividade com instâncias do Serviço do SignalR. Esse problema é temporário e será recuperado automaticamente.
Se ele persistir por mais de uma hora, [abra um problema no GitHub](https://github.com/Azure/azure-signalr/issues/new) ou [crie uma solicitação de suporte no Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando há vários servidores de aplicativos, as mensagens de cliente são enviadas a todos os servidores ou apenas a um deles?

Trata-se de um mapeamento de um para um entre cliente e servidor de aplicativos. Mensagens de um cliente sempre são enviadas para o mesmo servidor de aplicativos.

O mapeamento entre cliente e servidor de aplicativos será mantido até que o cliente ou o servidor de aplicativos desconecte.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se um dos meus servidores de aplicativos estiver inativo, como poderei encontrá-lo e ser notificado?

O Serviço do SignalR monitora pulsações de servidores de aplicativos.
Se as pulsações não forem recebidas por um período de tempo especificado, o servidor de aplicativos será considerado offline. Todas as conexões de cliente mapeadas para esse servidor de aplicativos serão desconectadas.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Por que meu `IUserIdProvider` personalizado lança uma exceção ao alternar do SDK do ASP.NET Core SignalR para o SDK do Serviço do Azure SignalR?

O parâmetro `HubConnectionContext context` é diferente entre o SDK do ASP.NET Core SignalR e o SDK do Serviço do Azure SignalR quando `IUserIdProvider` é chamado.

No ASP.NET Core SignalR, `HubConnectionContext context` é o contexto da conexão de cliente física com os valores válidos para todas as propriedades.

No SDK de Serviço do Azure SignalR, `HubConnectionContext context` é o contexto de conexão de cliente lógica. A conexão de cliente física está conectada à instância do Serviço do SignalR, portanto, apenas um número limitado de propriedades é fornecido.

Por enquanto, apenas `HubConnectionContext.GetHttpContext()` e `HubConnectionContext.User` estão disponíveis para acesso.
Você pode verificar o código-fonte [aqui](https://github.com/Azure/azure-signalr/blob/kevinzha/faq/src/Microsoft.Azure.SignalR/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Posso configurar os transportes disponíveis no Serviço do SignalR configurando-o no lado do servidor, com o ASP.NET Core SignalR? Por exemplo, desabilitar o transporte de WebSocket?

 Não.

O Serviço do Azure SignalR fornece todos os três transportes compatíveis por padrão com o ASP.NET Core SignalR. Ele não é configurável. O Serviço do SignalR cuidará das conexões e transportes para todas as conexões de cliente.

Você pode configurar os transportes do lado do cliente, conforme documentado [aqui](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports).
