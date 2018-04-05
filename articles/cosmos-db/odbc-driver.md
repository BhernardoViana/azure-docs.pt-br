---
title: Conectar ao Azure Cosmos DB usando ferramentas de análise de BI | Microsoft Docs
description: Saiba como usar o driver ODBC do Azure Cosmos DB para criar tabelas e exibições, de forma que os dados normalizados possam ser exibidos no software de BI e análise de dados.
keywords: odbc, driver odbc
services: cosmos-db
author: mimig1
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 9967f4e5-4b71-4cd7-8324-221a8c789e6b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 03/22/2018
ms.author: mimig
ms.openlocfilehash: 445acafeef67027712826f644afaa1784569b30d
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2018
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Conectar ao Azure Cosmos DB usando ferramentas de análise de BI com o driver ODBC

O driver ODBC do Azure Cosmos DB permite que você se conecte ao Azure Cosmos DB usando ferramentas de análise de BI, como SQL Server Integration Services, Power BI Desktop e Tableau, de forma que você possa analisar e criar visualizações dos dados do Cosmos DB nessas soluções.

O driver ODBC do Azure Cosmos DB está em conformidade com o ODBC 3.8 e dá suporte à sintaxe ANSI SQL-92. O driver oferece recursos avançados para ajudá-lo a renormalizar dados no Azure Cosmos DB. Usando o driver, você pode representar dados no Azure Cosmos DB como tabelas e exibições. O driver permite que você execute operações de SQL em tabelas e exibições incluindo o agrupamento por consultas, inserções, atualizações e exclusões.

## <a name="why-do-i-need-to-normalize-my-data"></a>Por que preciso normalizar meus dados?
O Azure Cosmos DB é um banco de dados sem esquemas e, portanto, permite o desenvolvimento rápido de aplicativos permitindo que aplicativos iterem seu modelo de dados em tempo real e não o limitem a um esquema rígido. Um único banco de dados do Azure Cosmos DB pode conter documentos JSON de várias estruturas. Isso é ótimo para o método RAD, mas quando você deseja analisar e criar relatórios de dados usando ferramentas de BI e análise de dados, os dados geralmente precisam ser nivelados e aderir a um esquema específico.

Esse é o ponto em que o driver ODBC entra. Ao usar o driver ODBC, você pode agora renormalizar dados no Azure Cosmos DB em tabelas e visualizações ajustadas às suas necessidades de relatórios e análise de dados. Os esquemas renormalizados não têm impacto sobre os dados subjacentes e não confinam os desenvolvedores para aderir a eles, eles simplesmente permitem utilizar ferramentas em conformidade com ODBC para acessar os dados. Agora seu banco de dados do Azure Cosmos DB não será apenas um favorito de sua equipe de desenvolvimento, mas seus analistas de dados vão adorá-lo também.

Vamos começar com o driver ODBC.

## <a id="install"></a>Etapa 1: Instalar o driver ODBC do Azure Cosmos DB

1. Baixe os drivers para seu ambiente:

    | Instalador | Sistemas operacionais com suporte| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64) para Windows de 64 bits| Versões de 64 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/documentdb-odbc-32x64) para 32 bits no Windows de 64 bits| Versões de 64 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 e Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32) para Windows de 32 bits|Versões de 32 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows XP e Windows Vista.|

    Execute o arquivo msi localmente, que inicia o **Assistente de Instalação do Driver ODBC do Microsoft Azure Cosmos DB**. 
2. Conclua o assistente de instalação usando a entrada padrão para instalar o driver ODBC.
3. Abra o aplicativo **Administrador de Fonte de Dados ODBC** no computador, você pode fazer isso digitando **Fontes de dados ODBC** na caixa de pesquisa do Windows. 
    Confirme se o driver foi instalado clicando na guia **Drivers** e garantindo que o **Driver ODBC do Microsoft Azure Cosmos DB** está listado.

    ![Administrador de fonte de dados ODBC do Azure Cosmos DB](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Etapa 2: Conectar ao banco de dados do Azure Cosmos DB

1. Depois de [Instalar o driver ODBC do Azure Cosmos DB](#install), na janela **Administrador de Fonte de Dados ODBC**, clique em **Adicionar**. Você pode criar um DSN de usuário ou de sistema. Neste exemplo, estamos criando um DSN de usuário.
2. Na janela **Criar Nova Fonte de Dados**, selecione **Driver ODBC do Microsoft Azure Cosmos DB** e clique em **Concluir**.
3. Na janela **Instalação de SDN do Driver ODBC do Azure Cosmos DB**, preencha o seguinte: 

    ![Janela Configuração de DSN do Driver ODBC do Azure Cosmos DB](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nome da Fonte de Dados**: seu próprio nome amigável para o DSN do ODBC. Esse nome é exclusivo à sua conta do Azure Cosmos DB; portanto, nomeie-a corretamente se você tiver várias contas.
    - **Descrição**: uma breve descrição da fonte de dados.
    - **Host**: URI de sua conta do Azure Cosmos DB. Você pode recuperar isso na página Chaves do Azure Cosmos DB no portal do Azure, conforme mostra a captura de tela a seguir. 
    - **Chave de Acesso**: a chave primária ou secundária, somente leitura ou gravação da página Chaves do Azure Cosmos DB no portal do Azure, conforme mostra a captura de tela a seguir. É recomendável que você use a chave somente leitura se o DSN for usado para relatórios e processamento de dados somente leitura.
    ![Página Chaves do Azure Cosmos DB](./media/odbc-driver/odbc-driver-keys.png)
    - **Criptografar a Tecla de Acesso para**: selecione a melhor opção com base nos usuários deste computador. 
4. Clique no botão **Testar** para ter certeza de que você pode se conectar à sua conta do Azure Cosmos DB. 
5. Clique em **Opções Avançadas de** e defina os seguintes valores:
    - **Consistência de Consulta**: selecione o [nível de consistência](consistency-levels.md) para as operações. O padrão é Sessão.
    - **Número de tentativas**: insira o número de vezes para tentar novamente uma operação se a solicitação inicial não for concluída devido à limitação.
    - **Arquivo de Esquema**: você tem várias opções aqui.
        - Por padrão, deixando a essa entrada como está (vazia), o driver examina os dados da primeira página de todas as coleções para determinar o esquema de cada coleção. Isso é conhecido como Mapeamento de Coleção. Sem um arquivo de esquema definido, o driver deve executar o exame para cada sessão de driver e pode resultar em um tempo de inicialização mais alto de um aplicativo usando o DSN. É recomendável sempre associar um arquivo de esquema para um DSN.
        - Se você já tiver um arquivo de esquema (possivelmente um criado usando o [Editor de Esquema](#schema-editor)), poderá clicar em **Procurar**, navegar até o arquivo, clicar em **Salvar** e clicar em **OK**.
        - Se você desejar criar um novo esquema, clique em **OK** e clique em **Editor de Esquema** na janela principal. Em seguida, vá para as informações do [Editor de Esquema](#schema-editor). Ao criar o novo arquivo de esquema, não se esqueça de voltar para a janela **Opções Avançadas** para incluir o arquivo de esquema recém-criado.

6. Depois de concluir e fechar a janela **Instalação de DSN do Driver ODBC do Azure Cosmos DB**, o novo DSN de Usuário é adicionado à guia DSN de Usuário.

    ![Novo DSN ODBC do Azure Cosmos DB na guia DSN de Usuário](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Etapa 3: Criar uma definição de esquema usando o método de mapeamento de coleção

Há dois tipos de métodos de amostragem que você pode usar: **mapeamento de coleção** ou **delimitadores de tabela**. Uma sessão de amostragem pode utilizar ambos os métodos de amostragem, mas cada coleta pode usar apenas um método de amostragem específico. As etapas a seguir criam um esquema para os dados em uma ou mais coleções usando o método de mapeamento de coleção. Esse método de amostragem recupera os dados na página de uma coleção para determinar a estrutura dos dados. Ele transpõe uma coleção para uma tabela no lado do ODBC. Esse método de amostragem é eficiente e rápido quando os dados em uma coleção são homogêneos. Se uma coleção contiver o tipo heterogêneo de dados, será recomendável usar o [método de mapeamento de delimitadores de tabela](#table-mapping) uma vez que ele fornece um método de amostragem mais robusto para determinar as estruturas de dados na coleção. 

1. Depois de concluir as etapas 1 a 4, em [Conectar-se ao seu banco de dados do Azure Cosmos DB](#connect), clique em **Editor de Esquema** na janela **Instalação de DSN do Driver ODBC do Azure Cosmos DB**.

    ![Botão do editor de esquema na janela Instalação de DSN do Driver ODBC do Azure Cosmos DB](./media/odbc-driver/odbc-driver-schema-editor.png)
2. Na janela **Editor de Esquema**, clique em **Criar Novo**.
    A janela **Gerar Esquema** exibe todas as coleções na conta do Azure Cosmos DB. 
3. Selecione uma ou mais coleções para obter o exemplo e clique em **Exemplo**. 
4. Na guia **Modo de Exibição de Design**, o banco de dados, o esquema e a tabela são representados. Na exibição de tabela, o exame mostra o conjunto de propriedades associado aos nomes de coluna (Nome SQL, Nome da Fonte etc.).
    Para cada coluna, você pode modificar o nome SQL, o tipo SQL, o comprimento SQL (se aplicável), a escala (se aplicável), a precisão (se aplicável) e o que permite valor nulo da coluna.
    - Você pode definir **Ocultar Coluna** como **true** se deseja excluir a coluna dos resultados da consulta. As colunas marcadas com Ocultar Coluna = true não são retornadas para seleção e projeção, embora ainda façam parte do esquema. Por exemplo, você pode ocultar todas as propriedades obrigatórias do sistema do Azure Cosmos DB que começam com “_”.
    - A coluna **id** é o único campo que não pode ser oculto porque ele é usado como a chave primária no esquema normalizado. 
5. Quando você terminar de definir o esquema, clique em **Arquivo** | **Salvar**, navegue até o diretório para salvar o esquema e clique em **Salvar**.

    Se, no futuro, você desejar usar esse esquema com um DSN, abra a janela Instalação de DSN do Driver ODBC do Azure Cosmos DB (por meio do Administrador da Fonte de Dados ODBC), clique em Opções Avançadas e, na caixa Arquivo de Esquema, navegue para o esquema salvo. Salvar um arquivo de esquema em um DSN existente modifica a conexão DSN de escopo para os dados e a estrutura definidos pelo esquema.

## <a id="table-mapping"></a>Etapa 4: Criar uma definição de esquema usando o método de mapeamento de delimitadores de tabela

Há dois tipos de métodos de amostragem que você pode usar: **mapeamento de coleção** ou **delimitadores de tabela**. Uma sessão de amostragem pode utilizar ambos os métodos de amostragem, mas cada coleta pode usar apenas um método de amostragem específico. 

As etapas a seguir criam um esquema para os dados em uma ou mais coleções usando o método de mapeamento de **delimitadores de tabela**. É recomendável que você use esse método de amostragem quando suas coleções contiverem tipos heterogêneos de dados. Você pode usar esse método para definir o escopo de amostragem para um conjunto de atributos e seus valores correspondentes. Por exemplo, se um documento contém uma propriedade "Type", você pode definir o escopo de amostragem para os valores dessa propriedade. O resultado final da amostragem seria um conjunto de tabelas para cada um dos valores de Type especificados. Por exemplo, Type = Carro gerará uma tabela Carro, enquanto Type = Avião gerará uma tabela Avião.

1. Depois de concluir as etapas 1 a 4, em [Conectar-se ao seu banco de dados do Azure Cosmos DB](#connect), clique em **Editor de Esquema** na janela Instalação de DSN do Driver ODBC do Azure Cosmos DB.
2. Na janela **Editor de Esquema**, clique em **Criar Novo**.
    A janela **Gerar Esquema** exibe todas as coleções na conta do Azure Cosmos DB. 
3. Selecione uma coleção na guia **Exibição de Exemplo**, na coluna **Definição do Mapeamento** da coleção, clique em **Editar**. Em seguida, na janela **Definição do Mapeamento**, selecione o método **Delimitadores de Tabela**. Faremos o seguinte:

    a. Na caixa **Atributos**, digite o nome de uma propriedade de delimitador. Esta é uma propriedade do documento para a qual você deseja definir o escopo de amostragem, por exemplo, Cidade, e pressione Enter. 

    b. Se você quiser definir o escopo de amostragem para determinados valores para o atributo que acabou de inserir, selecione o atributo na caixa de seleção e insira um valor na caixa **Valor**, por exemplo, Seattle, e pressione Enter. Você pode continuar a adicionar vários valores para atributos. Apenas certifique-se de que o atributo correto esteja selecionado quando estiver inserindo valores.

    Por exemplo, se você incluir um valor de **Atributos** de Cidade e desejar limitar sua tabela para incluir apenas linhas com um valor de cidade de Nova York e Dubai, insira Cidade na caixa Atributos e Nova York e Dubai na caixa **Valores**.
4. Clique em **OK**. 
5. Depois de concluir as definições de mapeamento das coleções para as quais deseja realizar a amostragem, na janela **Editor de Esquema**, clique em **Exemplo**.
     Para cada coluna, você pode modificar o nome SQL, o tipo SQL, o comprimento SQL (se aplicável), a escala (se aplicável), a precisão (se aplicável) e o que permite valor nulo da coluna.
    - Você pode definir **Ocultar Coluna** como **true** se deseja excluir a coluna dos resultados da consulta. As colunas marcadas com Ocultar Coluna = true não são retornadas para seleção e projeção, embora ainda façam parte do esquema. Por exemplo, você pode ocultar todas as propriedades obrigatórias do sistema do Azure Cosmos DB que começam com `_`.
    - A coluna **id** é o único campo que não pode ser oculto porque ele é usado como a chave primária no esquema normalizado. 
6. Quando você terminar de definir o esquema, clique em **Arquivo** | **Salvar**, navegue até o diretório para salvar o esquema e clique em **Salvar**.
7. Na janela **Instalação de DSN do Driver ODBC do Azure Cosmos DB**, clique em **Opções Avançadas**. Em seguida, na caixa **Arquivo de Esquema**, navegue até o arquivo de esquema salvo e clique em **OK**. Clique em **OK** novamente para salvar o DSN. Isso salva o esquema que você criou no DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Opcional) Configurar a conexão de servidor vinculado

Você pode consultar o Azure Cosmos DB do SQL Server Management Studio (SSMS), configurando uma conexão de servidor vinculado.

1. Crie uma fonte de dados do sistema, conforme descrito em [etapa 2](#connect), por exemplo, denominado `SDS Name`.
2. [Instalar o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).
3. No editor de consulta do SSMS, crie um objeto de servidor vinculado `DEMOCOSMOS` da fonte de dados com os seguintes comandos. Substitua `DEMOCOSMOS` com o nome do seu servidor vinculado e `SDS Name` com o nome da sua fonte de dados do sistema.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Para ver o novo nome de servidor vinculado, atualize a lista de servidores vinculados.

![Servidor Vinculado em SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Consulta de banco de dados vinculado

Para consultar o banco de dados vinculado, insira uma consulta do SSMS. Neste exemplo, a consulta seleciona da tabela na coleção denominada `customers`:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Executar a consulta. O resultado deve ter esta aparência:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> O servidor do Cosmos DB vinculado não oferece suporte a nomes de quatro partes. Será retornado um erro semelhante à seguinte mensagem:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Opcional) Criando exibições
Você pode definir e criar exibições como parte do processo de amostragem. Essas exibições são equivalentes às exibições do SQL. Eles são somente leitura e são o escopo definido pelas seleções e projeções do SQL do Azure Cosmos DB. 

Para criar uma exibição para seus dados, na janela **Editor de Esquema**, na coluna **Definições de Exibição**, clique em **Adicionar** na linha da coleção para a qual realizar a amostragem. Em seguida, na janela **Definições de Exibição**, faça o seguinte:
1. Clique em **Novo**, insira um nome para exibição, por exemplo, EmployeesfromSeattleView e, em seguida, clique em **OK**.
2. Na janela **Editar exibição**, insira uma consulta do Azure Cosmos DB. Ela deve ser uma consulta SQL do Azure Cosmos DB, por exemplo, `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”`. Depois, clique em **OK**.

Você pode criar quantas exibições desejar. Depois de definir as exibições, você pode realizar a amostragem dos dados. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Etapa 5: Exibir os dados em ferramentas de BI como Power BI Desktop

Você pode usar o novo DSN para conectar o DocumentADB com qualquer ferramenta em conformidade com ODBC, essa etapa simplesmente mostra como se conectar ao Power BI Desktop e criar uma visualização do Power BI.

1. Abra o Power BI Desktop.
2. Clique em **Obter Dados**.
3. Na janela **Obter Dados**, clique em **Outro** | **ODBC** | **Conectar**.
4. Na janela **Do ODBC**, selecione o nome da fonte de dados que você criou e clique em **OK**. Você pode deixar as entradas de **Opções Avançadas** em branco.
5. Na janela **Acesse uma fonte de dados usando um driver ODBC**, selecione **Padrão ou Personalizada** e clique em **Conectar**. Você não precisa incluir as **Propriedades de cadeia de conexão de credencial**.
6. Na janela **Navegador**, no painel esquerdo, expanda o banco de dados, o esquema e selecione a tabela. O painel de resultados inclui os dados usando o esquema que você criou.
7. Para visualizar os dados no Power BI Desktop, marque a caixa na frente do nome da tabela e clique em **Carregar**.
8. No Power BI Desktop, na extrema esquerda, selecione a guia Dados ![Guia Dados no Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) para confirmar se os dados foram importados.
9. Agora você pode criar elementos visuais usando o Power BI clicando na guia Relatório ![guia Relatório no Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), em **Visual Novo** e personalizando o bloco. Para obter mais informações sobre como criar visualizações no Power BI Desktop, consulte [Tipos de visualização no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>solução de problemas

Se você receber o seguinte erro, verifique se os valores de **Host** e **Tecla de Acesso** copiados do Portal do Azure na [Etapa 2](#connect) estão corretos e tente novamente. Use os botões de cópia à direita dos valores de **Host** e **Tecla de Acesso** no Portal do Azure para copiar os valores sem erros.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure Cosmos DB, confira [Bem-vindo ao Azure Cosmos DB](introduction.md).
