---
title: Tornar disponível para os usuários do Azure pilha da web e aplicativos de API | Microsoft Docs
description: Tutorial para instalar o provedor de recursos do serviço de aplicativo e criar oferece que dar aos usuários de pilha do Azure, a capacidade de criar a web e aplicativos de API.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 0171dba639e480a04cdd1c7f23d546d01121fb42
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247391"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Tutorial: disponibilizar web e aplicativos de API para os usuários de pilha do Azure

Como um administrador de nuvem de pilha do Azure, você pode criar ofertas que permitem aos usuários (locatários) criarem aplicativos web e funções do Azure e de API. Dando a seus usuários acesso a esses aplicativos sob demanda, com base em nuvem, você pode salvá-los, tempo e recursos.

Para configurar isso, você irá:

> [!div class="checklist"]
> * Implantar o provedor de recursos do serviço de aplicativo
> * Criar uma oferta
> * Testar a oferta

## <a name="deploy-the-app-service-resource-provider"></a>Implantar o provedor de recursos do serviço de aplicativo

1. [Preparar o host do Kit de desenvolvimento de pilha do Azure](azure-stack-app-service-before-you-get-started.md). Isso inclui a implantação do provedor de recursos do SQL Server, é necessário para a criação de alguns aplicativos.
2. [Baixe os scripts do instalador e auxiliar](azure-stack-app-service-deploy.md).
3. [Execute o script de auxiliar para criar certificados necessários](azure-stack-app-service-deploy.md).
4. [Instalar o provedor de recursos do serviço de aplicativo](azure-stack-app-service-deploy.md) (levará algumas horas para instalar e para todas as funções de trabalho sejam exibidos.)
5. [Validar a instalação](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Criar uma oferta

Por exemplo, você pode criar uma oferta que permite aos usuários criar sistemas de gerenciamento de conteúdo da web DNN. Ele requer o serviço do SQL Server que você tiver habilitado ao instalar o provedor de recursos do SQL Server.

1.  [Definir uma cota de](azure-stack-setting-quotas.md) e nomeie-o *AppServiceQuota*. Selecione **Microsoft** para o **Namespace** campo.
2.  [Criar um plano de](azure-stack-create-plan.md). Nomeie- *TestAppServicePlan*, selecione o **Microsoft.SQL** service e o **cota de serviço de aplicativo** cota.

    > [!NOTE]
    > Para permitir que os usuários criem outros aplicativos, outros serviços podem ser necessárias no plano. Por exemplo, funções do Azure requer o **Microsoft** serviço no plano, enquanto o Wordpress requer **Microsoft.MySQL**.

3.  [Criar uma oferta](azure-stack-create-offer.md), nomeie-o **TestAppServiceOffer** e selecione o **TestAppServicePlan** plano.

## <a name="test-the-offer"></a>Testar a oferta

Agora que você implantou o provedor de recursos do serviço de aplicativo e criar uma oferta, você pode entrar como um usuário, assinar a oferta e criar um aplicativo.

Neste exemplo, vamos criar um sistema de gerenciamento de conteúdo DNN plataforma. Primeiro, crie um banco de dados SQL e, em seguida, o aplicativo web DNN.

### <a name="subscribe-to-the-offer"></a>Assinar a oferta

1. Entre portal do Azure pilha (https://portal.local.azurestack.external) como um locatário.
2. Selecione **obter uma assinatura** >, insira **TestAppServiceSubscription** em **nome de exibição** > **selecione uma oferta**  >  **TestAppServiceOffer** > **criar**.

### <a name="create-a-sql-database"></a>Criar um banco de dados SQL

1. Selecione **+**  >  **dados + armazenamento** > **banco de dados SQL**.
2. Mantenha os valores padrão, exceto para os seguintes campos:

    - **Nome do banco de dados**: DNNdb
    - **Tamanho máximo em MB**: 100
    - **Assinatura**: TestAppServiceOffer
    - **Grupo de recursos**: DNN RG

3. Selecione **configurações de logon**, insira as credenciais para o banco de dados e, em seguida, selecione **Okey**. Você usará essas credenciais posteriormente neste tutorial.
4. Em **SKU** > Selecionar a SKU de SQL que você criou para o servidor de hospedagem SQL > e, em seguida, selecione **Okey**.
5. Clique em **Criar**.

### <a name="create-a-dnn-app"></a>Criar um aplicativo DNN

1. Selecione **+**  >  **ver todos os** > **visualização da plataforma DNN** > **criar** .
2. Digite *DNNapp* em **nome do aplicativo** e selecione **TestAppServiceOffer** em **assinatura**.
3. Selecione **definir as configurações necessárias** > **criar novo** > Insira um **plano de serviço de aplicativo** nome.
4. Selecione **preço** > **F1 livre** > **selecione** > **Okey**.
5. Selecione **banco de dados** e insira as credenciais para o banco de dados SQL criado anteriormente.
6. Clique em **Criar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Implantar o provedor de recursos do serviço de aplicativo
> * Criar uma oferta
> * Testar a oferta

Avançar para o próximo tutorial para aprender como:

> [!div class="nextstepaction"]
> [Implantar aplicativos do Azure e o Azure pilha](user/azure-stack-solution-pipeline.md)
