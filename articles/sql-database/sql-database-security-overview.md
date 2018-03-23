---
title: Visão geral de segurança do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre a segurança do SQL Server e Banco de Dados SQL do Azure, incluindo as diferenças entre a nuvem e o SQL Server local.
services: sql-database
documentationcenter: ''
author: giladm
manager: shaik
ms.reviewer: carlrab
editor: ''
ms.assetid: a012bb85-7fb4-4fde-a2fc-cf426c0a56bb
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: 41051944af863c4c50595ea843e2adf3513b3a12
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="securing-your-sql-database"></a>Protegendo o Banco de Dados SQL

Este artigo apresenta os conceitos básicos da proteção da camada de dados de um aplicativo usando o Banco de Dados SQL do Azure. Em particular, este artigo mostra uma introdução aos recursos de proteção de dados, de controle de acesso e de monitoramento proativo. 

Para obter uma visão geral completa dos recursos de segurança disponíveis em todas as versões do SQL, confira a [Central de Segurança do mecanismo de banco de dados do SQL Server e Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/bb510589). Outras informações também estão disponíveis no [White paper técnico sobre segurança e o Banco de Dados SQL do Azure](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="protect-data"></a>Proteger dados

### <a name="encryption"></a>Criptografia
O Banco de Dados SQL protege seus dados fornecendo criptografia para os dados em movimento com o [protocolo TLS](https://support.microsoft.com/kb/3135244), para os dados em repouso com a [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) e para os dados em uso com o [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx). 

> [!IMPORTANT]
>Todas as conexões do Banco de Dados SQL do Azure exigem criptografia (SSL/TLS) todo o tempo que os dados estiverem "em trânsito", entrando e saindo do banco de dados. Na cadeia de conexão do seu aplicativo, você deve especificar os parâmetros para criptografar a conexão, e *não* para confiar no certificado do servidor (esse processo será automático se você copiar a cadeia de conexão fora do Portal do Azure); caso contrário, a conexão não verifica a identidade do servidor e está sujeita a ataques "man-in-the-middle". Para o driver do ADO.NET, por exemplo, esses parâmetros da cadeia de conexão são **Encrypt=True** e **TrustServerCertificate=False**. 

Para outras maneiras de criptografar seus dados, considere:

* [Criptografia no nível de célula](https://msdn.microsoft.com/library/ms179331.aspx) para criptografar colunas específicas ou até mesmo células de dados com diferentes chaves de criptografia.
* Se você precisar de um Módulo de Segurança de Hardware ou do gerenciamento central de sua hierarquia de chaves de criptografia, considere o uso do [cofre da chave do Azure com o SQL Server em uma VM do Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

### <a name="data-discovery--classification"></a>Descoberta e Classificação de Dados
A Descoberta e Classificação de Dados (atualmente em versão prévia) fornece recursos internos avançados no Banco de dados SQL do Azure para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos de dados. Descobrir e classificar seus dados confidenciais mais importantes (negócios/financeiros, serviços de saúde, informações de identificação pessoal etc.) pode desempenhar uma função essencial na estatura de proteção das informações organizacionais. Pode servir como infraestrutura para:

- Vários cenários de segurança, como monitoramento (auditoria) e alertas sobre acesso anômalo a dados confidenciais.
- Controlar o acesso e proteção da segurança de bancos de dados contendo dados altamente confidenciais.
- Ajudar a cumprir os padrões de privacidade de dados e os requisitos de conformidade a normas.

Para obter mais informações, consulte [Introdução à Descoberta e Classificação de Dados do BD SQL](sql-database-data-discovery-and-classification.md). 

## <a name="control-access"></a>Controlar o acesso
O Banco de Dados SQL protege seus dados limitando o acesso ao banco de dados usando regras de firewall, mecanismos de autenticação que exigem que os usuários comprovem sua identidade e autorização para dados por meio de permissões e associações de função, bem como por meio de segurança em nível de linha e mascaramento de dados dinâmicos. Para uma discussão sobre o uso de recursos de controle de acesso no Banco de Dados SQL, veja [Controlar o acesso](sql-database-control-access.md).

> [!IMPORTANT]
> O gerenciamento de bancos de dados e de servidores lógicos no Azure é controlado por atribuições de função da sua conta de usuário do portal. Para saber mais sobre esse artigo, confira [Controle de acesso baseado em função no Portal do Azure](../active-directory/role-based-access-control-what-is.md).
>

### <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall
Para ajudar a proteger seus dados, os firewalls impedem todo acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão usando [regras de firewall](sql-database-firewall-configure.md). O firewall concede acesso aos bancos de dados com base no endereço IP de origem de cada solicitação.

### <a name="authentication"></a>Autenticação
A autenticação do banco de dados SQL refere-se a como você comprova sua identidade durante a conexão com o banco de dados. O Banco de Dados SQL dá suporte a dois tipos de autenticação:

* **Autenticação do SQL**, que usa um nome de usuário e senha. Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados, ou "dbo". 
* **Autenticação do Azure Active Directory**, que usa identidades gerenciadas pelo Azure Active Directory e que tem suporte para domínios gerenciados e integrados. Use autenticação do Active Directory (segurança integrada) [sempre que possível](https://msdn.microsoft.com/library/ms144284.aspx). Se desejar usar a Autenticação do Active Directory do Azure, você deve criar outro administrador de servidor chamado “admin do AD do Azure”, que tenha permissão para administrar usuários e grupos do AD do Azure. Este administrador também pode executar todas as operações executadas por um administrador de servidor comum. Veja [Conectando-se ao Banco de Dados SQL usando a Autenticação do Active Directory do Azure](sql-database-aad-authentication.md) para ver um passo a passo de como criar um administrador do Azure AD para habilitar a Autenticação do Active Directory do Azure.

### <a name="authorization"></a>Autorização
A autorização refere-se ao que um usuário pode fazer em um Banco de Dados SQL do Azure, e isso é controlado pelas associações à função de banco de dados e pelas permissões no nível do objeto da sua conta de usuário. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. A conta de administrador do servidor com a qual você está se conectando é um membro de db_owner, que tem autoridade para realizar qualquer tarefa no banco de dados. Salve essa conta para implantar atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para se conectar do aplicativo ao banco de dados com os privilégios mínimos necessários para seu aplicativo.

### <a name="row-level-security"></a>Segurança em nível de linha
A Segurança em Nível de Linha permite aos clientes controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário que executa uma consulta (por exemplo, uma associação de grupo ou um contexto de execução). Para saber mais, confira [Segurança em nível de linha](https://msdn.microsoft.com/library/dn765131).

### <a name="dynamic-data-masking"></a>Mascaramento de dados dinâmicos 
A máscara de dados dinâmicos do Banco de Dados SQL limita a exposição de dados confidenciais através do mascaramento dos dados para usuários sem privilégios. O mascaramento de dados dinâmicos detecta de forma automática os dados potencialmente confidenciais no Banco de Dados SQL do Azure e fornece recomendações viáveis para mascarar esses campos, com impacto mínimo sobre a camada de aplicativo. Funciona ao ocultar os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designado, enquanto os dados no banco de dados não são alterados. Para saber mais, confira [Introdução ao mascaramento de dados dinâmicos do Banco de Dados SQL](sql-database-dynamic-data-masking-get-started.md) que pode ser usado para limitar a exposição de dados confidenciais.

## <a name="proactive-monitoring"></a>Monitoramento proativo
O Banco de Dados SQL protege seus dados, fornecendo a auditoria e recursos de detecção de ameaças. 

### <a name="auditing"></a>Auditoria
A Auditoria do Banco de Dados SQL rastreia as atividades de banco de dados e ajuda a manter a conformidade normativa, registrando eventos de banco de dados em um log de auditoria em sua conta de Armazenamento do Azure. A auditoria permite que você compreenda as atividades do banco de dados em andamento, bem como analisar e investigar a atividade de histórico para identificar possíveis ameaças ou suspeitas de violações de segurança e de abuso. Para saber mais, confira [Introdução à Auditoria do Banco de Dados SQL](sql-database-auditing.md).  

### <a name="threat-detection"></a>Detecção de ameaças
A Detecção de Ameaças complementa a auditoria, fornecendo uma camada adicional de inteligência de segurança criada para o serviço Banco de Dados SQL que detecta tentativas incomuns e potencialmente perigosas de acessar ou explorar bancos de dados. Você é alertado sobre atividades suspeitas, vulnerabilidades potenciais, ataques de injeção de SQL, bem como padrões de acesso do banco de dados anormais. Os alertas da Detecção de Ameaças podem ser exibidos no [Azure Security Center](https://azure.microsoft.com/services/security-center/) e fornecem detalhes de atividades suspeitas e recomendam ação de como investigar e atenuar a ameaça. A Detecção de Ameaças custa US$ 15/servidor/mês. É gratuito nos primeiros 60 dias. Para obter mais informações, confira [Introdução à Detecção de Ameaças do Banco de Dados SQL](sql-database-threat-detection.md).
 
## <a name="compliance"></a>Conformidade
Além dos recursos e funcionalidades acima, que podem ajudar seu aplicativo a atender a vários requisitos de segurança, o Banco de Dados SQL do Azure também participa de auditorias regulares e foi certificado em relação a vários padrões de conformidade. Para saber mais, confira a [Central de Confiabilidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/), onde é possível encontrar a lista mais atual de [certificações de conformidade do Banco de Dados SQL](https://azure.microsoft.com/support/trust-center/services/).

## <a name="next-steps"></a>Próximas etapas

- Para uma discussão sobre o uso de recursos de controle de acesso no Banco de Dados SQL, veja [Controlar o acesso](sql-database-control-access.md).
- Para uma discussão sobre auditoria de banco de dados, consulte [Auditoria de Banco de Dados SQL](sql-database-auditing.md).
- Para uma discussão sobre detecção de ameaças, consulte [Detecção de ameaças do Banco de Dados SQL](sql-database-threat-detection.md).
