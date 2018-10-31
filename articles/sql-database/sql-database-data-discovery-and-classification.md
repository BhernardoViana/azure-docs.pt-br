---
title: Descoberta e Classificação de Dados do Banco de Dados SQL do Azure | Microsoft Docs
description: Descoberta e Classificação de Dados do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 32b60a53581a0f372a7d994cfa260ebd7bcb27b2
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467034"
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Descoberta e Classificação de Dados do Banco de Dados SQL do Azure

A Descoberta e Classificação de Dados (atualmente em versão prévia) fornece recursos internos avançados no Banco de Dados SQL do Microsoft Azure para **descobrir**, **classificar**, **rotular** & **proteger** os dados confidenciais em seus bancos de dados.
Descobrir e classificar seus dados mais confidenciais (negócios, financeiros, atendimento à saúde, PII [informações de identificação pessoal] etc.) pode desempenhar uma função essencial para a estatura de proteção das informações organizacionais. Pode servir como infraestrutura para:

- Ajudar a cumprir os padrões de privacidade de dados e os requisitos de conformidade a normas.
- Vários cenários de segurança, como monitoramento (auditoria) e alertas sobre acesso anômalo a dados confidenciais.
- Controlar o acesso e proteção da segurança de bancos de dados contendo dados altamente confidenciais.

A Descoberta e Classificação de Dados faz parte da oferta [ATP](sql-advanced-threat-protection.md) (Proteção Avançada contra Ameaças) ao SQL, um pacote unificado de funcionalidades avançadas de segurança do SQL. A Descoberta e Classificação de Dados pode ser acessada e gerenciada por meio do portal central da ATP do SQL.

> [!NOTE]
> Este documento está relacionado apenas ao Banco de Dados SQL do Azure. Para o SQL Server (no local), consulte [Descoberta e Classificação de Dados SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>O que é Descoberta e Classificação de Dados?

A Descoberta e Classificação de Dados apresenta um conjunto de serviços avançados e novos recursos SQL, formando um novo paradigma de Proteção de Informações do SQL, visando proteger os dados e não apenas o banco de dados:

- **Descoberta e recomendações**

  O mecanismo de classificação examina o banco de dados e identifica colunas contendo dados potencialmente confidenciais. Em seguida, fornece uma maneira fácil de revisar e aplicar as recomendações de classificação apropriadas por meio do Portal do Azure.

- **Rotulação**

  Os rótulos de classificação de confidencialidade podem ser marcados persistentemente em colunas usando novos atributos de metadados de classificação introduzidos no SQL Engine. Esses metadados podem ser utilizados para cenários de proteção e auditoria baseada em confidencialidade.

- **Sensibilidade de conjunto de resultados de consulta**

  A confidencialidade do conjunto de resultados da consulta é calculada em tempo real para fins de auditoria.

- **Visibilidade**

  O estado da classificação do banco de dados pode ser visualizado em um painel detalhado no portal. Além disso, você pode fazer o download de um relatório (no formato Excel) para ser usado para fins de conformidade e auditoria, bem como outras necessidades.

## <a id="subheading-2"></a>Descobrir, classificar e rotular colunas confidenciais

A seção a seguir descreve as etapas para descobrir, classificar e rotular colunas contendo dados confidenciais no banco de dados, bem como visualizar o estado de classificação atual do banco de dados e exportar relatórios.

A classificação inclui dois atributos de metadados:

- Rótulos – Os principais atributos de classificação, usados para definir o nível de confidencialidade dos dados armazenados na coluna.  
- Tipos de informações – Fornece granularidade adicional ao tipo de dados armazenados na coluna.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definir e personalizar sua taxonomia de classificação

A Descoberta e Classificação de Dados SQL vem com um conjunto interno de rótulos de confidencialidade e um conjunto interno de tipos de informações e lógica de descoberta. Agora, você tem a possibilidade de personalizar esta taxonomia e definir um conjunto e uma classificação de constructos de classificação especificamente para seu ambiente.

A definição e a personalização de sua taxonomia de classificação são feitas em um local central para todo o seu locatário do Azure. O local é a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro), como parte de sua Política de Segurança. Somente alguém com direitos administrativos no grupo de gerenciamento raiz do Locatário pode executar essa tarefa.

Como parte do gerenciamento de políticas da Proteção de Informações, você pode definir rótulos personalizados, classificá-los e associá-los a um conjunto selecionado de tipos de informações. Você também pode adicionar seus próprios tipos de informações personalizadas e configurá-los com padrões de cadeia de caracteres, que são adicionados à lógica de descoberta para identificar esse tipo de dados em seus bancos de dados.
Saiba mais sobre como personalizar e gerenciar sua política no [Guia de instruções sobre política da Proteção de Informações](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Depois que a política de todo o locatário tiver sido definida, você poderá continuar com a classificação de bancos de dados individuais usando sua política personalizada.

## <a name="classify-your-sql-database"></a>Classificar o Banco de Dados SQL

1. Vá para o [Portal do Azure](https://portal.azure.com).

2. Navegue para **Proteção Avançada contra Ameaças** no cabeçalho Segurança do painel do Banco de Dados SQL do Azure. Clique nessa opção para habilitar a Proteção Avançada contra Ameaças e, em seguida, clique no cartão **Descoberta e classificação de dados (versão prévia)**.

   ![Examinar um banco de dados](./media/sql-data-discovery-and-classification/data_classification.png)

3. A guia **Visão geral** inclui um resumo do estado de classificação atual do banco de dados, incluindo uma lista detalhada de todas as colunas classificadas, que você também pode filtrar para exibir apenas partes específicas do esquema, tipos de informações e rótulos. Se ainda não classificou nenhuma coluna, [pule para a etapa 5](#step-5).

   ![Resumo do estado atual de classificação](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Para fazer o download de um relatório no formato Excel, clique na opção **Exportar** no menu superior da janela.

   ![Exportar para o Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Para começar a classificar os dados, clique na guia **Classificação** na parte superior da janela.

    ![Classificar os dados](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. O mecanismo de classificação explora o banco de dados para colunas contendo dados potencialmente confidenciais e fornece uma lista de **classificações de colunas recomendadas**. Para visualizar e aplicar recomendações de classificação:

   - Para visualizar a lista das classificações de colunas recomendadas, clique no painel de recomendações na parte inferior da janela:

      ![Classificar os dados](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Revise a lista de recomendações – para aceitar uma recomendação de uma coluna específica, marque a caixa de seleção na coluna esquerda da linha relevante. Também é possível marcar *todas as recomendações* aceitas, marcando a caixa de seleção no cabeçalho da tabela de recomendações.

       ![Examinar a lista de recomendação](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Para aplicar as recomendações selecionadas, clique no botão azul **Aceitar recomendações selecionadas**.

      ![Aplicar recomendações](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Como alternativa, também é possível **classificar manualmente** as colunas ou, além disso, fazer a classificação baseada em recomendação:

   - Clique em **Adicionar classificação** no menu superior da janela.

      ![Adicionar a classificação manualmente](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - Na janela de contexto que abre, selecione o esquema > tabela > coluna que deseja classificar e o tipo de informação e o rótulo de confidencialidade. Em seguida, clique no botão azul **Adicionar classificação** na parte inferior da janela de contexto.

      ![Selecionar a coluna a ser classificada](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Para completar sua classificação e rotular (marcar) de maneira persistente as colunas do banco de dados com os novos metadados de classificação, clique em **Salvar** no menu superior da janela.

   ![Salvar](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Auditoria de acesso aos dados confidenciais

Um aspecto importante do paradigma da proteção de informações é a capacidade de monitorar o acesso a dados confidenciais. A [Auditoria do Banco de Dados SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) foi aprimorada para incluir um novo campo no log de auditoria chamado *data_sensitivity_information*, que registra a classificações de confidencialidade (rótulos) dos dados reais que foram retornados pela consulta.

![Log de auditoria](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Classificação automatizada/programática

Você pode usar o T-SQL para adicionar/remover classificações de coluna, bem como para recuperar todas as classificações para o banco de dados inteiro.

> [!NOTE]
> Ao usar o T-SQL para gerenciar rótulos, não há nenhuma validação de que os rótulos adicionados a uma coluna existem na política de proteção de informações organizacionais (o conjunto de rótulos exibido nas recomendações do portal). Portanto, cabe a você validar isto.

- Adicione/atualize a classificação de uma ou mais colunas: [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Remova a classificação de uma ou mais colunas: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Exiba todas as classificações no banco de dados: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

Também é possível usar APIs REST para gerenciar programaticamente as classificações. As APIs REST publicadas dão suporte às seguintes operações:

- [Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) - Cria ou atualiza o rótulo de confidencialidade de uma determinada coluna
- [Excluir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) - Exclui o rótulo de confidencialidade de uma determinada coluna
- [Obter](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get)  - Obtém o rótulo de confidencialidade de uma determinada coluna
- [Listar por banco de dados](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listbydatabase) - Obtém os rótulos de confidencialidade de um determinado banco de dados

## <a id="subheading-5"></a>Próximas etapas

- Saiba mais sobre a [Proteção Avançada contra Ameaças do SQL](sql-advanced-threat-protection.md).
- Considere configurar a [Auditoria do Banco de Dados SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) para monitorar e auditar o acesso aos seus dados confidenciais classificados.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
