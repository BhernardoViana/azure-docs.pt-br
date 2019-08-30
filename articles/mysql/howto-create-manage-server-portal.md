---
title: Criar e gerenciar um servidor de Banco de Dados do Azure para MySQL usando o Portal do Azure
description: Este artigo descreve como você pode criar rapidamente um novo servidor de banco de dados do Azure para MySQL e gerenciar o servidor usando o portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: fdcb302d3a14b02ea86fb92c8dbf822ef3f42177
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142243"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Criar e gerenciar um servidor de Banco de Dados do Azure para MySQL usando o Portal do Azure
Este tópico descreve como criar rapidamente um novo servidor de Banco de Dados do Azure para MySQL. Também inclui informações sobre como gerenciar o servidor usando o Portal do Azure. O gerenciamento do servidor inclui exibir detalhes do servidor e banco de dados, redefinir a senha, dimensionar recursos e excluir o servidor.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure
Faça logon no [Portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Criar um Banco de Dados do Azure para o servidor MySQL
Execute essas etapas para criar um Banco de Dados do Azure para MySQL nomeado “mydemoserver.”

1. Clique no botão **Criar um recurso**, localizado no canto superior esquerdo do portal do Azure.

2. Na página Novo, selecione **Banco de Dados** e, em seguida, na página Banco de Dados, selecione **Banco de Dados do Azure para MySQL**.

    > Um Banco de Dados do Azure para MySQL é criado com um conjunto definido de recursos de [computação e armazenamento](./concepts-pricing-tiers.md). O banco de dados é criado dentro de um grupo de recursos do Azure e em um servidor de Banco de Dados do Azure para MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Preencha o formulário do Banco de Dados do Azure para MySQL usando as seguintes informações:

    | **Campo de Formulário** | **Descrição do Campo** |
    |----------------|-----------------------|
    | *Nome do servidor* | mydemoserver (o nome do servidor é globalmente exclusivo) |
    | *Assinatura* | mysubscription (selecione no menu suspenso) |
    | *Grupo de recursos* | myresourcegroup (crie um novo grupo de recursos ou use um existente) |
    | *Selecionar fonte* | Em branco (crie um servidor MySQL em branco) |
    | *Logon de administrador do servidor* | myadmin (nome de conta do administrador de configuração) |
    | *Senha* | definir a senha da conta do administrador |
    | *Confirmar senha* | confirmar senha da conta do administrador |
    | *Localidade* | Sudeste Asiático (selecione entre a Europa Setentrional e o Oeste dos EUA) |
    | *Versão* | 5.7 (escolha uma versão do servidor de Banco de Dados do Azure para MySQL) |

   ![create-new-server](./media/howto-create-manage-server-portal/form-field.png)

4. Clique em **Tipo de preço** para especificar o nível de desempenho e a camada de serviço para o novo servidor. Selecione a guia **Uso Geral**. *Gen 5*, *2 vCores*, *5 GB*, e *7 dias* são os valores padrão para **Geração de Computação**, **vCore**, **Armazenamento** e **Período de Retenção de Backup**. Você pode deixar esses controles deslizantes como estão. Para habilitar os backups do servidor em armazenamento com redundância geográfica, selecione **Redundância Geográfica** das **Opções de Redundância de Backup**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Clique em **Criar** para provisionar o servidor. O provisionamento demora alguns minutos.

    > Selecione a opção **Fixar no painel** para permitir o controle fácil das suas implantações.

## <a name="update-an-azure-database-for-mysql-server"></a>Atualizar um servidor de Banco de Dados do Azure para MySQL
Depois que o novo servidor tiver sido provisionado, o usuário terá várias opções para configurar o servidor existente, incluindo a redefinição da senha do administrador, a alteração do tipo de preço e a expansão ou redução vertical do servidor, alterando vCore ou armazenamento

### <a name="change-the-administrator-user-password"></a>Alterar a senha de usuário do administrador
1. Na **Visão Geral** do servidor, clique em **Redefinir Senha** para mostrar a janela de redefinição de senha.

   ![visão geral](./media/howto-create-manage-server-portal/overview.png)

2. Insira uma nova senha e confirme a senha na janela, conforme mostrado:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Clique em **OK** para salvar a nova senha.

### <a name="change-the-pricing-tier"></a>Alterar o tipo de preço
> [!NOTE]
> O dimensionamento só tem suporte de Uso Geral para camadas de serviço com otimização de memória e vice-versa. Observe que a alteração de e para o tipo de preço básico após a criação do servidor não tem suporte no banco de dados do Azure para MySQL.
> 
1. Clique em **Tipo de Preço**, localizado em **Configurações**.
2. Selecione o **tipo de preço** para o qual você deseja alterar.

    ![alterar-camada de preços](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. Clique em **OK** para salvar as alterações. 

### <a name="scale-vcores-updown"></a>Escalar verticalmente/reduzir verticalmente o vCores

1. Clique em **Tipo de Preço**, localizado em **Configurações**.

2. Altere a configuração **vCore**, movendo o controle deslizante para o valor desejado.

    ![scale-compute](./media/howto-create-manage-server-portal/scale-compute.png)

3. Clique em **OK** para salvar as alterações.

### <a name="scale-storage-up"></a>Escalar verticalmente o armazenamento

1. Clique em **Tipo de Preço**, localizado em **Configurações**.

2. Altere o **Armazenamento**, movendo o controle deslizante para o valor desejado.

    ![scale-storage](./media/howto-create-manage-server-portal/scale-storage.png)

3. Clique em **OK** para salvar as alterações.

## <a name="delete-an-azure-database-for-mysql-server"></a>Excluir um servidor de Banco de Dados do Azure para MySQL

1. Na **Visão Geral** do servidor, clique no botão **Excluir** para abrir o prompt de confirmação de exclusão.

    ![delete](./media/howto-create-manage-server-portal/delete.png)

2. Digite o nome do servidor na caixa de entrada para confirmação dupla.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Clique no botão **Excluir** para confirmar a exclusão do servidor. Aguarde até o pop-up "Servidor MySQL excluído com êxito" ser exibido na barra de notificação.

## <a name="list-the-azure-database-for-mysql-databases"></a>Listar os bancos de dados do Banco de Dados do Azure para MySQL
Na **Visão Geral** do servidor, role para baixo até visualizar o bloco do banco de dados na parte inferior. Todos os bancos de dados no servidor estão listados na tabela.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Mostrar detalhes de um servidor de Banco de Dados do Azure para MySQL
Clique em **Propriedades**, localizado em **Configurações** para exibir informações detalhadas sobre o servidor.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Próximas etapas

[Início Rápido: Criar Banco de Dados do Azure para MySQL usando o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)