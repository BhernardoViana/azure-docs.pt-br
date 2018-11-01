---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 30b5ae499d29b8b78b5852074362841ac1ceb49f
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134230"
---
1. Entre no [Portal do Azure].
2. Selecione **+NOVO** > **Web + Celular** > **Aplicativo Móvel** e, em seguida, forneça um nome para o back-end dos Aplicativos Móveis.
3. Em **Grupo de Recursos**, selecione um grupo de recursos existente ou crie um novo(usando o mesmo nome do seu aplicativo). 
4. Para o **Plano do Serviço de Aplicativo**, o plano padrão (na [camada Standard](https://azure.microsoft.com/pricing/details/app-service/)) está selecionado. Você também pode selecionar um plano diferente ou [criar um novo](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   As configurações de plano de Serviço de Aplicativo determinam o [local, os recursos, o custo e os recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados ao seu aplicativo. Para saber mais sobre os Planos do Serviço de Aplicativo e como criar um novo plano em um tipo de preço e em seu local desejado, confira [Visão geral detalhada dos Planos do Serviço de Aplicativo do Azure](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
   
5. Selecione **Criar**. Essa etapa cria o back-end dos Aplicativos Móveis. 
6. No painel **Configurações** do novo back-end dos Aplicativos Móveis, selecione **Início Rápido** > sua plataforma de aplicativo cliente > **Conectar um banco de dados**. 
   
   ![Seleções para conectar um banco de dados](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. No painel **Adicionar conexão de dados**, selecione **Banco de Dados SQL** > **Criar um novo banco de dados**. Insira o nome do banco de dados, escolha um tipo de preço e selecione **Servidor**. Você pode reutilizar este novo banco de dados. Se já tiver um banco de dados no mesmo local, você poderá optar por **Usar um banco de dados existente**. Não é recomendável usar um banco de dados em um local diferente, devido a custos de largura de banda e maior latência.
   
   ![Selecionar um banco de dados](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. No painel **Novo servidor**, insira um nome do servidor exclusivo na caixa **Nome do servidor**, forneça um logon e senha, selecione **Permitir que os serviços do Azure acessem o servidor** e selecione **OK**. Essa etapa cria o novo banco de dados.
9. Retorne ao painel **Adicionar conexão de dados**, selecione **Cadeia de conexão**, insira os valores de logon e senha para o banco de dados e selecione **OK**. 

   Antes de continuar, aguarde alguns minutos para que o banco de dados seja implantado com êxito.

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/
