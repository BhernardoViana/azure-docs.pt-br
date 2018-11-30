---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: ef6d5d22f70d5fff38f90b457a7c945ab59fc67c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52330690"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as assinaturas do Barramento de Serviço?
Os tópicos e assinaturas do Barramento de Serviço dão suporte a um modelo de comunicação de mensagens de *publicação/assinatura* . Durante o uso de tópicos e assinaturas, os componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, eles trocam mensagens por meio de um tópico, que atua como um intermediário.

![Conceitos de tópico](./media/howto-service-bus-topics/sb-topics-01.png)

Em contraste com filas do Barramento de Serviço, em que cada mensagem é processada por um único consumidor, tópicos e assinaturas fornecem uma forma de comunicação de "um para muitos", usando um padrão de publicação/assinatura. É possível registrar várias assinaturas para um tópico. Quando uma mensagem é enviada a um tópico, é disponibilizada para cada assinatura para ser manipulada/processada de forma independente.

Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens enviadas para o tópico. Opcionalmente, é possível registrar as regras de filtro para um tópico em uma base por assinatura. Regras de filtro permitem que você filtre ou restrinja quais mensagens para um tópico são recebidas por quais assinaturas de tópico.

As assinaturas e os tópicos do Barramento de Serviço permitem o dimensionamento e o processamento de muitos usuários e aplicativos.

## <a name="create-a-namespace"></a>Criar um namespace
Para começar a usar as assinaturas e os tópicos do Barramento de Serviço no Azure, primeiro crie um *namespace de serviço*. Um namespace fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo.

Para criar um namespace:

1. Entre no [Portal do Azure][Azure portal].
2. No painel de navegação esquerdo do portal, clique em **Criar um recurso**, depois em **Integração Corporativa** e em **Barramento de Serviço**.
3. Na caixa de diálogo **Criar um namespace** , digite um nome de namespace. O sistema imediatamente verifica para ver se o nome está disponível.
4. Depois de verificar se o nome do namespace está disponível, escolha o tipo de preço (Básico, Standard ou Premium).
5. No campo **Assinatura** , escolha uma assinatura do Azure na qual criar o namespace.
6. No campo **Grupo de Recursos**, escolha um grupo de recursos existente no qual o namespace reside, ou então crie um novo.      
7. Em **Localização**, escolha o país ou região no qual o namespace deve ser hospedado.
   
    ![Criar um namespace][create-namespace]
8. Selecione o botão **Criar**. Agora, o sistema cria o seu namespace e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

### <a name="obtain-the-credentials"></a>Obter as credenciais
1. Na lista de namespaces, clique no nome do namespace recém-criado.
2. No painel **Namespace do Barramento de Serviço**, clique em **Políticas de acesso compartilhado**.
3. No painel **Políticas de acesso compartilhado**, clique em **RootManageSharedAccessKey**.
   
    ![informações de conexão][connection-info]
4. No painel **Política: RootManageSharedAccessKey**, clique no botão copiar ao lado da **Chave primária da cadeia de conexão** para copiar a cadeia de conexão na área de transferência para uso posterior.
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


