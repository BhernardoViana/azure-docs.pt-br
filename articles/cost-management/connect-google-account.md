---
title: Conecte uma conta do Google Cloud Platform ao Cloudyn no Azure | Microsoft Docs
description: Conecte uma conta do Google Cloud Platform para visualizar dados de custo e uso nos relatórios do Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 48f1afcbef873e1af4346199c00ee2fadb1ad858
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830235"
---
# <a name="connect-a-google-cloud-platform-account"></a>Conectar uma conta do Google Cloud Platform

Você pode conectar sua conta existente do Google Cloud Platform ao Cloudyn. Depois de conectar sua conta ao Cloudyn, os dados de custo e uso estão disponíveis nos relatórios do Cloudyn. Este artigo ajuda você a configurar e conectar a conta do Google ao Cloudyn.

> [!NOTE]
> O Google modificou a segurança de suas contas, o que impede que novas conexões sejam estabelecidas entre Cloudyn e Google. O Cloudyn continua coletando dados do Google para usuários que já possuem o Cloudyn conectado ao Google. No entanto, atualmente não é possível adicionar novas contas do Google ao Cloudyn. A equipe do Cloudyn não sabe quando o suporte para adicionar novas contas do Google ao Cloudyn será retomado. Removeremos esta observação quando o suporte for retomado.

## <a name="collect-project-information"></a>Coletar informações do projeto

Inicie coletando informações sobre seu projeto.

1. Entre no Console do Google Cloud Platform em [https://console.cloud.google.com](https://console.cloud.google.com).
2. Revise as informações do projeto que você deseja incluir no Cloudyn e observe o **nome do projeto** e o **ID do projeto**. Mantenha as informações acessíveis para as etapas posteriores.  
    ![Console do Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Se a cobrança não estiver habilitada e vinculada ao seu projeto, crie uma conta de cobrança. Para obter mais informações, consulte [Criar uma nova conta de cobrança](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account).

## <a name="enable-storage-bucket-billing-export"></a>Habilitar a exportação de cobrança de bucket

O Cloudyn recupera os dados de cobrança do Google de um bucket de armazenamento. Manter o **nome do Bucket** e **prefixo do relatório** informações à mão para uso posterior durante o registro do Cloudyn.

Usar o Google Cloud Storage para armazenar relatórios de uso incorre em taxas mínimas. Para obter mais informações, consulte [Preços do Armazenamento em Nuvem](https://cloud.google.com/storage/pricing).

1. Se você não habilitou a exportação de cobrança para um arquivo, siga as instruções em [Como habilitar a exportação de cobrança para um arquivo](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). É possível usar o formato de exportação de cobrança JSON ou CSV.
2. Caso contrário, no console do Google Cloud Platform, navegue para **Cobrança** > **Exportação de cobrança**. Anote o **Nome do Bucket** e o **Prefixo do Relatório** de cobrança.  
    ![Exportação de cobrança](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Habilitar APIs do Google Cloud Platform

Para coletar informações de uso e ativos, a Cloudyn precisa das seguintes APIs do Google Cloud Platform ativadas:

- API do BigQuery
- SQL do Google Cloud
- API do Google Cloud Datastore
- Google Cloud Storage
- API JSON do Google Cloud Storage
- API do Google Compute Engine

### <a name="enable-or-verify-apis"></a>Habilitar ou verificar APIs

1. No console do Google Cloud Platform, selecione o projeto que você deseja registrar na Cloudyn.
2. Navegue até **APIs e Serviços** > **Biblioteca**.
3. Use a pesquisa para localizar cada API listada anteriormente.
4. Para cada API, verifique se está indicada como **API habilitada**. Caso contrário, clique em **HABILITAR**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Adicionar uma conta do Google Cloud para o Cloudyn

1. Abra o portal da Cloudyn por meio do portal do Azure ou navegue até [https://azure.cloudyn.com](https://azure.cloudyn.com/) e entre.
2. Clique em **Configurações** (símbolo de engrenagem) e, em seguida, selecione **Contas do Cloud**.
3. Em **Gerenciamento de Contas**, selecione a guia **Contas do Google** e, em seguida, clique em **Adicionar novo +**.
4. Em **Nome da Conta do Google**, insira o endereço de email da conta de cobrança e clique em **Próximo**.
5. Na caixa de diálogo de autenticação do Google, selecione ou insira uma Conta do Google e, em seguida, **PERMITIR** cloudyn.com acessar sua conta.
6. Adicione as informações do projeto da solicitação anotada anteriormente. As informações incluem a **ID do Projeto**, o nome do **Projeto**, nome do bucket de **cobrança** e prefixo do relatório do **arquivo de cobrança** e, em seguida, clique em **Salvar**.  
    ![Adicionar projeto do Google](./media/connect-google-account/add-project.png)

Sua conta do Google aparecerá na lista de contas e deverá indicar **Autenticada**. Na conta, o nome e a ID do projeto do Google deverão aparecer e terem um símbolo de marca de seleção verde. O Status da Conta deverá indicar **Concluído**.

Dentro de algumas horas, os relatórios da Cloudyn mostram informações sobre custo e uso do Google.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Cloudyn, continue no tutorial [Revisar o uso e os custos](./tutorial-review-usage.md) do Cloudyn.
