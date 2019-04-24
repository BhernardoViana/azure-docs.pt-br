---
title: 'PowerShell e CLI: Habilitar TDE do SQL – com o Azure Key Vault – Bring Your Own Key – Banco de Dados SQL do Azure| Microsoft Docs'
description: Saiba como configurar um Data Warehouse e Banco de Dados SQL do Azure para começar a usar a TDE (Transparent Data Encryption) para criptografia em repouso usando PowerShell ou CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: c42c6175512105de38a29be260c370851e152137
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60330866"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell e CLI: Habilitar Transparent Data Encryption com chave gerenciada pelo cliente do Azure Key Vault

Este artigo descreve como usar uma chave do Azure Key Vault para TDE (Transparent Data Encryption) em um Banco de Dados SQL ou Data Warehouse. Para saber mais sobre a integração da TDE com o Azure Key Vault - Suporte a Bring Your Own Key (BYOK), visite [TDE com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Pré-requisitos para PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
- [Recomendado, mas opcional] Tenha um HSM (Módulo de Segurança de Hardware) ou armazenamento de chave local para criar uma cópia local do material da chave do Protetor de TDE.
- Você deve ter o Azure PowerShell instalado e em execução. 
- Crie um Azure Key Vault e Chave para usar na TDE.
  - [Instruções do PowerShell do Key Vault](../key-vault/key-vault-overview.md)
  - [Instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - O cofre de chaves deve ter a seguinte propriedade a ser usada para TDE:
  - [exclusão reversível](../key-vault/key-vault-ovw-soft-delete.md)
  - [Como usar a exclusão reversível do Key Vault com o PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- A chave deve ter os seguintes atributos a serem usados para TDE:
   - Sem data de validade
   - Não desabilitado
   - Capaz de realizar operações *get*, *codificar chave*, *decodificar chave*

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Etapa 1. Atribuir uma identidade do Azure AD ao servidor 

Se houver um servidor existente, use o seguinte para adicionar uma identidade do Azure AD ao servidor:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Se você estiver criando um servidor, use o [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet com a marca-Identity para adicionar uma identidade do AD do Azure durante a criação do servidor:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Etapa 2. Conceder permissões do Key Vault ao servidor

Use o [AzKeyVaultAccessPolicy conjunto](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet para conceder acesso ao servidor para a chave de cofre antes de usar uma chave dele para a TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Etapa 3. Adicionar a chave do Key Vault ao servidor e definir o Protetor de TDE

- Use o [AzSqlServerKeyVaultKey adicionar](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet para adicionar a chave do Key Vault para o servidor.
- Use o [AzSqlServerTransparentDataEncryptionProtector conjunto](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet para definir a chave como o protetor de TDE para todos os recursos de servidor.
- Use o [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet para confirmar que o protetor de TDE foi configurado como pretendido.

> [!Note]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.
> 

>[!Tip]
>Um exemplo de KeyId do Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Etapa 4. Ativar TDE 

Use o [AzSqlDatabaseTransparentDataEncryption conjunto](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet para ativar a TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Agora, o banco de dados ou data warehouse possui a TDE habilitada com uma chave de criptografia no Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Etapa 5. Verificar o estado de criptografia e a atividade de criptografia

Use o [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) para obter o estado de criptografia e o [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) para verificar o progresso de criptografia para um banco de dados ou depósito de dados.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Outros cmdlets do PowerShell úteis

- Use o [AzSqlDatabaseTransparentDataEncryption conjunto](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet para desligar a TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Use o [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet para retornar a lista de chaves do Key Vault adicionadas ao servidor.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Use o [AzSqlServerKeyVaultKey remover](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) para remover uma chave de Cofre de chaves do servidor.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>solução de problemas

Se um problema ocorrer, verifique o seguinte:
- Se o Cofre de chaves não for encontrado, verifique se você está na assinatura correta usando a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet.

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Se a nova chave não puder ser adicionada ao servidor, ou a nova chave não puder ser atualizada como o protetor de TDE, verifique o seguinte:
   - A chave não deve ter uma data de validade
   - A chave deve ter as operações *get*, *codificar chave* e *decodificar chave* habilitadas.

## <a name="next-steps"></a>Próximos passos

- Saiba como girar o Protetor de TDE de um servidor para atender aos requisitos de segurança: [Girar o protetor de Transparent Data Encryption usando o PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Em caso de risco à segurança, saiba como remover um Protetor de TDE potencialmente comprometido: [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Pré-requisitos para CLI

- É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
- [Recomendado, mas opcional] Tenha um HSM (Módulo de Segurança de Hardware) ou armazenamento de chave local para criar uma cópia local do material da chave do Protetor de TDE.
- Interface de Linha de Comando versão 2.0 ou posterior. Para instalar a última versão e conectá-la à sua assinatura do Azure, consulte [Instalar e configurar a Interface de Linha de Comando 2.0 de plataforma cruzada do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
- Crie um Azure Key Vault e Chave para usar na TDE.
  - [Gerenciar o Key Vault usando CLI 2.0](../key-vault/key-vault-manage-with-cli2.md)
  - [Instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - O cofre de chaves deve ter a seguinte propriedade a ser usada para TDE:
  - [exclusão reversível](../key-vault/key-vault-ovw-soft-delete.md)
  - [Como usar a exclusão reversível do Key Vault com a CLI](../key-vault/key-vault-soft-delete-cli.md) 
- A chave deve ter os seguintes atributos a serem usados para TDE:
   - Sem data de validade
   - Não desabilitado
   - Capaz de realizar operações *get*, *codificar chave*, *decodificar chave*
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>Etapa 1. Criar um servidor com uma identidade do Azure AD
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Impeça que a "principalID" crie o servidor; a ID de objeto usada para atribuir permissões de cofre de chaves na próxima etapa
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>Etapa 2. Conceder permissões do Key Vault ao servidor SQL lógico
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Salve o URI da chave ou a keyID da nova chave para a próxima etapa, por exemplo: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Etapa 3. Adicionar a chave do Key Vault ao servidor e definir o Protetor de TDE
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.
> 

  
## <a name="step-4-turn-on-tde"></a>Etapa 4. Ativar TDE 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Agora, o banco de dados ou data warehouse possui a TDE habilitada com uma chave de criptografia gerenciada pelo cliente no Azure Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Etapa 5. Verificar o estado de criptografia e a atividade de criptografia

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>Referências da CLI do SQL

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

