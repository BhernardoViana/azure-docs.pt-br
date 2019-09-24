---
title: Exibir um repositório de conhecimento com o Gerenciador de Armazenamento – Azure Search
description: Exiba e analise um repositório de conhecimento do Azure Search com o Gerenciador de Armazenamento do portal do Azure.
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 6c90cec91e85f64397f70b015ffde15a2ea6deaf
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963113"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Exibir um repositório de conhecimento com o Gerenciador de Armazenamento

> [!Note]
> O armazenamento de dados de conhecimento está em versão prévia e não é destinado a uso em produção. A [API REST do Azure Search versão 2019-05-06 versão prévia](search-api-preview.md) fornece esse recurso. Não há suporte para SDK do .NET no momento.
>
Neste artigo, você aprenderá como se conectar e explorar um repositório de conhecimento usando o Gerenciador de Armazenamento no portal do Azure. Para criar o exemplo de repositório de conhecimento usado neste passo a passo, confira [Criar um repositório de conhecimento no portal do Azure](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Siga as etapas em [Criar um repositório de conhecimento no portal do Azure](knowledge-store-create-portal.md) para criar o repositório de conhecimento de exemplo usado neste passo a passo.

+ Também será necessário o nome da conta de armazenamento do Azure que você usou para criar o repositório de conhecimento, junto com sua chave de acesso do portal do Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Exibir, editar e consultar um repositório de conhecimento no Gerenciador de Armazenamento

1. No portal do Azure, abra a conta de armazenamento que você usou para criar o repositório de conhecimento.

1. No painel de navegação esquerdo da conta de armazenamento, clique em **Gerenciador de Armazenamento**.

1. Expanda a lista **TABELAS** para mostrar uma lista de projeções de tabela do Azure que foram criadas quando você executou o assistente de **Importação de Dados** em seus dados de exemplo de resenhas de hotéis.

Selecione qualquer tabela para exibir os dados aprimorados, incluindo pontuações de sentimento de frases-chave, dados de localização de latitude e longitude e muito mais.

   ![Exibir tabelas no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Exibir tabelas no Gerenciador de Armazenamento")

Para alterar o tipo de dados para qualquer valor de tabela ou para alterar valores individuais em sua tabela, clique em **Editar**. Quando você alterar o tipo de dados para qualquer coluna na linha de tabela, ele será aplicado a todas as linhas.

   ![Editar tabela no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Editar tabela no Gerenciador de Armazenamento")

Para executar consultas, clique em **Consultar** na barra de comandos e insira suas condições.  

   ![Consultar tabela no Gerenciador de Armazenamento](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Consultar tabela no Gerenciador de Armazenamento")

## <a name="clean-up"></a>Limpar

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite.

## <a name="next-steps"></a>Próximas etapas

Para saber como conectar esse repositório de conhecimento ao Power BI, confira o artigo a seguir.

> [!div class="nextstepaction"]
> [Conectar com o Power BI](knowledge-store-connect-power-bi.md)

Para saber como criar um repositório de conhecimento usando as APIs REST e o Postman, confira o artigo a seguir.  

> [!div class="nextstepaction"]
> [Criar um repositório de conhecimento no REST](knowledge-store-howto.md)
