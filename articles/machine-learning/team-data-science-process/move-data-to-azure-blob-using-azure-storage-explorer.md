---
title: Mover os dados de armazenamento de Blob com o Gerenciador de Armazenamento do Microsoft Azure – Processo de Ciência de Dados de Equipe
description: Saiba como usar Gerenciador de Armazenamento do Azure para carregar e baixar dados do armazenamento de BLOBs do Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 880b81c07aeed7359dfe35e1361a20ecb11e27dd
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053952"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mover dados de e para o Armazenamento de Blobs do Azure usando o Gerenciador de Armazenamento do Azure
O Gerenciador de Armazenamento do Azure é um ferramenta gratuita da Microsoft que possibilita o trabalho com dados do Armazenamento do Azure no Windows, MacOS e Linux. Este tópico descreve como usá-lo para carregar e baixar os dados do armazenamento de blobs do Azure. A ferramenta pode ser baixada em [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se você estiver usando a VM configurada com os scripts fornecidos pelas [Máquinas virtuais de ciências de dados no Azure](virtual-machines.md), o Gerenciador de armazenamento do Azure já estará instalado na VM.
> 
> [!NOTE]
> Para obter uma introdução completa ao armazenamento de blobs do Azure, consulte [Noções básicas de Blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [Serviço de Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você tenha uma assinatura, uma conta de armazenamento do Azure e a chave de armazenamento correspondente dessa conta. Antes de carregar/baixar os dados, você deve conhecer o nome e a chave da sua conta do Armazenamento do Azure. 

* Para configurar uma assinatura do Azure, consulte [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e para obter informações sobre conta e chave, consulte [Sobre contas do armazenamento do Azure](../../storage/common/storage-create-storage-account.md). Anote a chave de acesso para sua conta de armazenamento, pois você precisa dessa chave para conectar-se à conta com a ferramenta Gerenciador de Armazenamento do Azure.
* A ferramenta Gerenciador de Armazenamento do Azure pode ser baixada em [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com/). Aceite os padrões durante a instalação.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Usar o Gerenciador de Armazenamento do Azure
As etapas a seguir mostram como carregar/baixar os dados usando o Gerenciador de Armazenamento do Azure. 

1. Inicie o Gerenciador de Armazenamento do Microsoft Azure.
2. Para abrir o assistente **Entrar em sua conta...** , selecione o ícone **Configurações de conta do Azure**, depois **Adicionar uma conta** e insira suas credenciais. ![Adicionar uma conta de armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Para abrir o assistente **Conectar-se ao Armazenamento do Azure**, selecione o ícone **Conectar-se ao Armazenamento do Azure**. ![Clique em “Conectar ao armazenamento do Azure"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Insira a chave de acesso da sua conta de armazenamento do Azure no assistente **Conectar-se ao Armazenamento do Azure** e, em seguida, **Avançar**. ![Insira a chave de acesso da conta de armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Insira o nome da conta de armazenamento na caixa **Nome da conta** e selecione **Avançar**. ![Anexar um armazenamento externo](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A conta de armazenamento adicionada deve estar listada. Para criar um contêiner de blob em uma conta de armazenamento, clique com o botão direito no nó **Contêineres de Blob** na conta, selecione **Criar Contêiner de Blob** e insira um nome.
7. Para carregar dados em um contêiner, selecione o contêiner de destino e clique no botão **Carregar**.![Contas de Armazenamento](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Clique em **...** à direita da caixa **Arquivos**, selecione um vários arquivos para carregar no sistema de arquivos e clique em **Carregar** para começar a carregar os arquivos.![Carregar os arquivos](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Para baixar os dados, selecione o blob no contêiner correspondente para baixar e clique em **Download**. ![Baixar arquivos](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

