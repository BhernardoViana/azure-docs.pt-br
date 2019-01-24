---
title: Usar a Grade de Eventos do Azure para automatizar o redimensionamento de imagens carregadas | Microsoft Docs
description: A Grade de Eventos do Azure pode ser disparada em carregamentos de blob no Armazenamento do Azure. Você pode usar isso para enviar os arquivos de imagem carregados no Armazenamento do Azure para outros serviços, como o Azure Functions, para redimensionamento e outras melhorias.
services: event-grid, functions
author: spelluru
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/19/2019
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 4a7e6189914728fac24e51f3b2dee66cc0bd8a05
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463704"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Tutorial: Automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos

A [Grade de Eventos do Azure](overview.md) é um serviço de eventos para a nuvem. A Grade de Eventos permite criar assinaturas em eventos gerados pelos serviços do Azure ou por recursos de terceiros.  

Este tutorial é a segunda parte de uma série de tutoriais sobre o Armazenamento. Ele estende o [tutorial anterior sobre o Armazenamento][previous-tutorial] para adicionar a geração automática de miniaturas sem servidor usando a Grade de Eventos do Azure e o Azure Functions. A Grade de Eventos permite que o [Azure Functions](../azure-functions/functions-overview.md) responda aos eventos do [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md) e gere miniaturas das imagens carregadas. Uma assinatura de evento é criada no evento de criação do armazenamento de Blobs. Quando um blob é adicionado a um contêiner de armazenamento de Blobs específico, um ponto de extremidade de função é chamado. Os dados passados para a associação de função da Grade de Eventos são usados para acessar o blob e gerar a imagem em miniatura.

Use a CLI do Azure e o portal do Azure para adicionar a funcionalidade de redimensionamento a um aplicativo de upload de imagens existente.

![Aplicativo Web publicado no navegador Microsoft Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta geral do Armazenamento do Azure
> * Implantar o código sem servidor usando o Azure Functions
> * Criar uma assinatura de evento do armazenamento de Blobs na Grade de Eventos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

É necessário concluir o tutorial de Armazenamento de Blobs anterior: [Carregar dados de imagem na nuvem com o Armazenamento do Azure][previous-tutorial].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Se você não tiver registrado o provedor de recursos da Grade de Eventos em sua assinatura, verifique se que ele está registrado.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este tutorial exigirá a CLI do Azure versão 2.0.14 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

Se você não estiver usando o Cloud Shell, primeiro você deve entrar usando `az login`.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

O Azure Functions exige uma conta de armazenamento geral. Crie uma conta de armazenamento geral separada no grupo de recursos usando o comando [az storage account create](/cli/azure/storage/account#az-storage-account-create).

Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. 

No comando a seguir, substitua seu próprio nome global exclusivo da conta de armazenamento quando o espaço reservado `<general_storage_account>` for exibido. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Criar um aplicativo de funções  

Você deve ter um aplicativo de funções para hospedar a execução da função. O aplicativo de funções fornece um ambiente para execução sem servidor do seu código de função. Crie um aplicativo de funções ao usar o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

No comando a seguir, substitua seu próprio nome exclusivo do aplicativo de funções em que o espaço reservado `<function_app>` é exibido. O nome do aplicativo de funções é usado como domínio DNS padrão para o aplicativo de funções, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. Para `<general_storage_account>`, substitua o nome da conta de armazenamento geral criada.

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

Agora você deve configurar o aplicativo de função para se conectar à conta de armazenamento de Blob criada no [tutorial anterior][previous-tutorial].

## <a name="configure-the-function-app"></a>Configurar o aplicativo de funções

A função precisa da cadeia de conexão para se conectar à conta de armazenamento de blobs. O código de função que você vai implantar no Azure na etapa a seguir procura a cadeia de conexão na configuração do aplicativo myblobstorage_STORAGE, além de procurar o nome do contêiner de imagem em miniatura na configuração de aplicativo myContainerName. Obtenha a cadeia de conexão com o comando [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Defina as configurações de aplicativo com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

Nos comados da CLI a seguir, `<blob_storage_account>` é o nome da conta de armazenamento de Blobs criado no tutorial anterior.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbnails FUNCTIONS_EXTENSION_VERSION=~2
```

A configuração `FUNCTIONS_EXTENSION_VERSION=~2` faz com que o aplicativo de funções execute na versão 2.x do Azure Functions Runtime.

Agora você pode implantar um projeto de código de função nesse aplicativo de funções.

## <a name="deploy-the-function-code"></a>Implantar o código de função 

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

O redimensionamento do exemplo do script C# (.csx) está disponível no [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Implante esse projeto de código do Functions no aplicativo de funções usando o comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config). 

No comando a seguir, `<function_app>` é o nome do aplicativo de funções criado anteriormente.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
A função de redimensionamento do exemplo Node.js está disponível no [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node). Implante esse projeto de código do Functions no aplicativo de funções usando o comando [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config).

No comando a seguir, `<function_app>` é o nome do aplicativo de funções criado anteriormente.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node
```
---

A função de redimensionamento de imagem é disparada por solicitações HTTP enviadas a ele a partir do serviço de Grade de Eventos. Você informa à Grade de Eventos que você deseja obter essas notificações na URL da função criando uma assinatura de evento. Neste tutorial, você assina eventos criados de blob.

Os dados passados para a função da notificação de Grade de Eventos incluem a URL do blob. Essa URL, por sua vez, é passada para a associação de entrada para obter a imagem carregada a partir do armazenamento de Blob. A função gera uma imagem em miniatura e grava o fluxo resultante em um contêiner separado no armazenamento de Blobs. 

Este projeto usa `EventGridTrigger` para o tipo de gatilho. O uso do gatilho de Grade de Eventos é recomendado em gatilhos HTTP genéricos. A Grade de Eventos valida automaticamente os gatilhos de Função da Grade de Eventos. Com os gatilhos HTTP genéricos, você deve implementar a [resposta de validação](security-authentication.md#webhook-event-delivery).

Para saber mais sobre essa função, consulte os [arquivos function.json e run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/imageresizerfunc).
 
O código de projeto de função é implantado diretamente no repositório público de exemplo. Para saber mais sobre as opções de implantação para o Azure Functions, consulte [Implantação contínua para o Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Criar uma assinatura de evento

Uma assinatura de evento indica quais eventos gerados pelo provedor você deseja que sejam enviados para um ponto de extremidade específico. Nesse caso, o ponto de extremidade é exposto pela função. Use as seguintes etapas para criar uma assinatura de evento que envia notificações à sua função no portal do Azure: 

1. No [portal do Azure](https://portal.azure.com), clique na seta no canto inferior esquerdo para expandir todos os serviços, digite *functions* no campo **Filtro** e, em seguida, escolha **Aplicativos de Funções**. 

    ![Navegar para os Aplicativos de Funções no portal do Azure](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Expanda o aplicativo de funções, escolha a função **imageresizerfunc** e, em seguida, selecione **Adicionar assinatura da Grade de Eventos**.

    ![Navegar para os Aplicativos de Funções no portal do Azure](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Use as configurações da assinatura de evento, conforme especificado na tabela.
    
    ![Criar a assinatura de evento com base na função no portal do Azure](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Configuração      | Valor sugerido  | DESCRIÇÃO                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Tipo de tópico** |  Contas de armazenamento | Escolha o provedor de eventos da conta de Armazenamento. | 
    | **Assinatura** | Sua assinatura do Azure | Por padrão, sua assinatura atual do Azure é selecionada.   |
    | **Grupo de recursos** | myResourceGroup | Selecione **Usar existente** e escolha o grupo de recursos que está sendo usado neste tutorial.  |
    | **Recurso** |  Sua conta de armazenamento de Blobs |  Escolha a conta de armazenamento de Blobs criada. |
    | **Tipos de evento** | Blob criado | Desmarque todos os tipos que não sejam **Blob criado**. Somente os tipos de evento `Microsoft.Storage.BlobCreated` são passados para a função.| 
    | **Tipo de assinante** |  gerado automaticamente |  Pré-definidas como Web Hook. |
    | **Ponto de extremidade do assinante** | gerado automaticamente | Use a URL de ponto de extremidade gerada para você. | 
    | **Nome** | imageresizersub | Nome que identifica a nova assinatura de evento. | 
4. *Opcional:* Caso você precise criar contêineres adicionais no mesmo armazenamento de blobs para outras finalidades no futuro, pode usar os recursos **Filtragem de assunto** da guia **Filtros** para direcionamento mais granular de eventos de blob para garantir que seu aplicativo de função seja chamado somente quando blobs são adicionados ao contêiner **imagens** especificamente. 
5. Clique em **Criar** para adicionar a assinatura de evento. Isso cria uma assinatura de evento que dispara `imageresizerfunc` quando um blob é adicionado ao contêiner de *imagens*. A função redimensiona as imagens e adiciona-as ao contêiner de *miniaturas*.

Agora que os serviços de back-end estão configurados, teste a funcionalidade de redimensionamento da imagem no aplicativo Web de exemplo. 

## <a name="test-the-sample-app"></a>Testar o aplicativo de exemplo

Para testar o redimensionamento de imagem no aplicativo Web, navegue para a URL do aplicativo publicado. A URL padrão do aplicativo Web é `https://<web_app>.azurewebsites.net`.

Clique na região **Carregar fotos** para selecionar e carregar um arquivo. Também arraste uma foto para essa região. 

Observe que, depois que a imagem carregada desaparece, uma cópia da imagem carregada é exibida no carrossel **Miniaturas geradas**. Essa imagem foi redimensionada pela função, adicionada ao contêiner de *miniaturas* e baixada pelo cliente Web.

![Aplicativo Web publicado no navegador Microsoft Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma conta geral do Armazenamento do Azure
> * Implantar o código sem servidor usando o Azure Functions
> * Criar uma assinatura de evento do armazenamento de Blobs na Grade de Eventos

Avance até a terceira parte da série de tutoriais sobre o Armazenamento para saber como proteger o acesso à conta de armazenamento.

> [!div class="nextstepaction"]
> [Proteger o acesso aos dados de um aplicativo na nuvem](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Para saber mais sobre a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure](overview.md). 
+ Para experimentar outro tutorial que mostra o Azure Functions, consulte [Criar uma função que é integrada aos Aplicativos Lógicos do Azure](../azure-functions/functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
