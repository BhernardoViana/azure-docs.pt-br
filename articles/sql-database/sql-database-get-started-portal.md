---
title: 'Portal do Azure: Criar um banco de dados SQL | Microsoft Docs'
description: Crie um servidor lógico, uma regra de firewall no nível do servidor e um banco de dados do Banco de Dados SQL no portal do Azure e consulte-o.
keywords: tutorial do banco de dados SQL, criar um banco de dados SQL
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.topic: quickstart
ms.date: 03/23/2018
ms.author: carlrab
ms.openlocfilehash: b02c69f34c72fc89a4ae81fc1feb3641a7c8b06d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Criar um Banco de Dados SQL do Azure no portal do Azure

Este tutorial de início rápido percorre como criar um banco de dados SQL no Azure. O Banco de Dados SQL do Azure é uma oferta de "Banco de Dados como Serviço" que permite executar e dimensionar os bancos de dados do SQL Server altamente disponíveis na nuvem. Este guia rápido mostra como começar criando um banco de dados SQL com o portal do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Criar um banco de dados SQL

Um banco de dados SQL do Azure é criado com um conjunto definido de [recursos de computação e armazenamento](sql-database-service-tiers.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e em um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md).

Execute estas etapas para criar um Banco de Dados SQL que contém os dados de exemplo do Adventure Works LT.

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e selecione **Criar** em **Banco de Dados SQL** na página **Novo**.

   ![criar database-1](./media/sql-database-get-started-portal/create-database-1.png)

3. Preencha o formulário do Banco de Dados SQL com as informações abaixo, conforme mostrado na imagem anterior:   

   | Configuração       | Valor sugerido | DESCRIÇÃO |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do banco de dados** | mySampleDatabase | Para ver os nomes do banco de dados válidos, consulte [Identificadores do Banco de Dados](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Assinatura** | Sua assinatura  | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos**  | myResourceGroup | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Selecionar fonte** | Exemplo (AdventureWorksLT) | Carrega o esquema AdventureWorksLT e os dados no novo banco de dados |

   > [!IMPORTANT]
   > Você deve selecionar o banco de dados de exemplo neste formulário porque ele é usado no restante deste início rápido.
   >

4. Em **Servidor**, clique em **Definir configurações obrigatórias** e preencha o formulário do SQL Server (servidor lógico) com as informações abaixo, conforme mostrado na imagem a seguir:   

   | Configuração       | Valor sugerido | DESCRIÇÃO |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do servidor** | Qualquer nome exclusivo globalmente | Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Logon de administrador do servidor** | Qualquer nome válido | Para ver os nomes de logon válidos, consulte [Identificadores do Banco de Dados](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). |
   | **Senha** | Qualquer senha válida | Sua senha deve ter, pelo menos, 8 caracteres e deve conter caracteres de três das seguintes categorias: caracteres com letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos. |
   | **Assinatura** | Sua assinatura | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos** | myResourceGroup | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Localidade** | Qualquer local válido | Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). |

   > [!IMPORTANT]
   > O logon de administrador do servidor e a senha que você especificar aqui são necessárias para fazer logon no servidor e em seus bancos de dados mais tarde neste início rápido. Lembre-se ou registre essas informações para o uso posterior.
   >  

   ![criar database-server](./media/sql-database-get-started-portal/create-database-server.png)

5. Quando tiver concluído o formulário, clique em **Selecionar**.

6. Clique em **Tipo de preço** para especificar a camada de serviço, o número de DTUs e a quantidade de armazenamento. Explore as opções para a quantidade de DTUs e o armazenamento disponível em cada camada de serviço.

   > [!IMPORTANT]
   > \* Tamanhos de armazenamento maiores que a quantidade de armazenamento incluída estão em versão prévia e aplicam-se custos extras. Para obter detalhes, confira [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
   >
   >\* Na camada Premium, mais de 1 TB de armazenamento está disponível atualmente nas seguintes regiões: Leste da Austrália, Sudeste da Austrália, Sul do Brasil, Central do Canadá, Leste do Canadá, Centro dos EUA, França Central, Centro da Alemanha, Leste do Japão, Oeste do Japão, Coreia Central, Centro-Norte dos EUA, Europa Setentrional, Centro-Sul dos EUA, Sudeste Asiático, Sul do Reino Unido, Oeste do Reino Unido, Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia e Europa Ocidental. Consulte [Limitações atuais de P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   >

7. Para este tutorial de início rápido, selecione a camada de serviço **Standard** e use o controle deslizante para selecionar **10 DTUs (S0)** e **1** GB de armazenamento.

   ![Criar database-s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. Aceite os termos da versão prévia para usar a opção **Armazenamento Complementar**.

   > [!IMPORTANT]
   > \* Tamanhos de armazenamento maiores que a quantidade de armazenamento incluída estão em versão prévia e aplicam-se custos extras. Para obter detalhes, confira [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
   >
   >\* Na camada Premium, mais de 1 TB de armazenamento está disponível atualmente nas seguintes regiões: Sul do Brasil, Central do Canadá, Leste do Canadá, Centro dos EUA, França Central, Centro da Alemanha, Leste do Japão, Oeste do Japão, Coreia Central, Centro-Norte dos EUA, Europa Setentrional, Centro-Sul dos EUA, Sudeste Asiático, Sul do Reino Unido, Oeste do Reino Unido, Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia e Europa Ocidental. Consulte [Limitações atuais de P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   >

9. Depois de selecionar a camada de servidor, o número de DTUs e a quantidade de armazenamento, clique em **Aplicar**.  

10. Agora que você concluiu o formulário do Banco de Dados SQL, clique em **Criar** para provisionar o banco de dados. O provisionamento demora alguns minutos.

11. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

     ![notificação](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

O serviço do Banco de Dados SQL cria um firewall no nível do servidor impedindo que os aplicativos e ferramentas externos conectem o servidor ou os bancos de dados no servidor, a menos que uma regra de firewall seja criada para abrir o firewall para endereços IP específicos. Execute estas etapas a fim de criar uma [regra de firewall no nível do servidor do Banco de Dados SQL](sql-database-firewall-configure.md) para o endereço IP do seu cliente e habilitar a conectividade externa por meio do firewall do Banco de Dados SQL somente para seu endereço IP.

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isto acontecer, você não poderá conectar o servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

1. Depois da implantação ser concluída, clique em **Bancos de dados SQL** no menu à esquerda, depois, clique em **mySampleDatabase** na página **Bancos de dados SQL**. A página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver-20170824.database.windows.net**) e fornece opções para configurações adicionais.

2. Copie esse nome do servidor totalmente qualificado para se conectar ao servidor e a seus bancos de dados nos próximos guias de início rápido.

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png)

3. Clique em **Definir o firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta.

   ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

5. Clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para a porta de abertura 1433 de seu endereço IP atual no servidor lógico.

6. Clique em **OK**, em seguida, feche a página **Configurações do Firewall**.

Agora, você pode conectar o servidor do Banco de Dados SQL e seus bancos de dados usando o SQL Server Management Studio ou outra ferramenta de sua escolha neste endereço IP usando a conta do administrador do servidor criada anteriormente.

> [!IMPORTANT]
> Por padrão, o acesso através do firewall do Banco de Dados SQL está habilitado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desabilitar todos os serviços do Azure.
>

## <a name="query-the-sql-database"></a>Consultar o Banco de Dados SQL

Agora que você criou um banco de dados de exemplo no Azure, usaremos a ferramenta de consulta interna no portal do Azure para confirmar que você pode conectar o banco de dados e consultar os dados.

1. Na página de Banco de Dados SQL do seu banco de dados, clique em **Editor de consultas (versão prévia)** no menu do lado esquerdo e, em seguida, clique em **Login**.

   ![logon](./media/sql-database-get-started-portal/query-editor-login.png)

2. Selecione a autenticação do SQL Server, forneça as informações de logon necessárias e, em seguida, clique em **OK** para fazer logon.

3. Depois de autenticado como **ServerAdmin**, digite a consulta a seguir no painel do editor de consulta.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Clique em **Executar** e reveja os resultados da consulta no painel **Resultados**.

   ![resultados do editor de consultas](./media/sql-database-get-started-portal/query-editor-results.png)

5. Feche a página **Editor de consulta** e clique em **OK** para descartar suas edições não salvas.

## <a name="clean-up-resources"></a>Limpar recursos

Salvar esses recursos se você deseja ir para as [Próximas etapas](#next-steps) e saiba como se conectar e consultar o banco de dados usando vários métodos diferentes. No entanto, se você deseja excluir os recursos criados neste início rápido, use as etapas a seguir.


1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e clique em **myResourceGroup**.
2. Em sua página de grupo de recursos, clique em **Excluir**, digite **myResourceGroup** na caixa de texto e clique **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Agora que você tem um banco de dados, é possível [se conectar e consultar](sql-database-connect-query.md) usando uma das suas ferramentas ou linguagens favoritas. 
- Para saber como projetar seu primeiro banco de dados, criar tabelas e inserir dados, consulte um destes tutoriais:
 - [Projetar seu primeiro banco de dados SQL do Azure usando o SSMS](sql-database-design-first-database.md)
  - [Criar um banco de dados SQL do Azure e conectar-se com C# e o ADO.NET](sql-database-design-first-database-csharp.md)
