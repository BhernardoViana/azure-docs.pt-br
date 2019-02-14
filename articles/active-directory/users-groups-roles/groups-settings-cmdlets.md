---
title: Definir configurações de grupo usando o PowerShell - Azure Active Directory | Microsoft Docs
description: Como gerenciar as configurações de grupos usando cmdlets do Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: e838af0ac8e9cfd1d42b768fa68ec7d9f46386c6
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512220"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Cmdlets do Azure Active Directory para definir configurações de grupo
Este artigo contém instruções sobre como usar cmdlets do PowerShell do Azure Active Directory (Azure AD) para criar e atualizar grupos. Este conteúdo aplica-se somente aos Grupos do Office 365 (às vezes chamados de grupos unificados). 

> [!IMPORTANT]
> Algumas configurações exigem uma licença do Azure Active Directory Premium P1. Para obter mais informações, consulte a tabela [Configurações de modelo](#template-settings).

Para saber mais sobre como evitar que usuários não administradores criem grupos de segurança, defina `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` conforme descrito em [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

As configurações de Grupos do Office 365 são definidas usando um objeto Settings e um SettingsTemplate. Inicialmente, você não verá objetos Settings no seu diretório, pois o diretório foi configurado com as definições padrão. Para alterar as configurações padrão, você deve criar um novo objeto de configurações usando um modelo de configurações. Modelos de configurações são definidos pela Microsoft. Há vários modelos de configurações diferentes. Para definir as configuração de grupo do Office 365 para seu diretório, use o modelo chamado "Group.Unified". Para definir as configurações de grupo do Office 365 em um único grupo, use o modelo chamado "Group.Unified.Guest". Esse modelo é usado para gerenciar o acesso de convidado a um grupo do Office 365. 

Os cmdlets fazem parte do Módulo do Azure Active Directory PowerShell V2. Para obter instruções de como baixar e instalar o módulo no seu computador, confira o artigo [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/) (PowerShell Versão 2 do Azure Active Directory). Você pode instalar a versão 2 do módulo da [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Recuperar um valor de configurações específico
Se você souber o nome da configuração que deseja recuperar, será possível usar o cmdlet abaixo para recuperar o valor atual das configurações. Neste exemplo, recuperaremos o valor de uma configuração chamada "UsageGuidelinesUrl". Você pode ler que mais sobre configurações de diretório e seus nomes mais abaixo neste artigo.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Criar configurações no nível do diretório
Essas etapas criam configurações no nível de diretório, que são aplicáveis a todos os Grupos do Office 365 no diretório. O cmdlet Get-AzureADDirectorySettingTemplate está disponível apenas no [módulo de versão prévia do Azure AD PowerShell para Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. Nos cmdlets DirectorySettings, você precisa especificar a ID de SettingsTemplate que deseja usar. Se você não souber essa ID, esse cmdlet retornará a lista com todos os modelos de configurações:
  
  ```powershell
  Get-AzureADDirectorySettingTemplate
  ```
  Essa chamada do cmdlet retorna todos os modelos disponíveis:
  
  ```powershell
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Para adicionar uma URL de diretrizes de uso, primeiro você precisa obter o objeto SettingsTemplate que define o valor de URL da diretriz de uso; ou seja, o modelo de Group.Unified:
  
  ```powershell
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Em seguida, crie um novo objeto de configurações com base no modelo:
  
  ```powershell
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Então, atualize o valor de diretriz de uso:
  
  ```powershell
  $setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
  ```  
5. Por fim, aplique as configurações:
  
  ```powershell
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Após a conclusão bem-sucedida, o cmdlet retornará a ID do novo objeto de configuração:

  ```powershell
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

## <a name="template-settings"></a>Configurações de modelo
Aqui estão as configurações definidas no Group.Unified SettingsTemplate. A menos que indicado o contrário, esses recursos exigem uma licença do Azure Active Directory Premium P1. 

| **Configuração** | **Descrição** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Digite: BOOLEAN<li>Padrão: True |O sinalizador indicando se a criação do Grupo do Office 365 é permitida no diretório pelos usuários não administradores. Essa configuração não requer uma licença do Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Digite: Cadeia de caracteres<li>Padrão: “” |GUID do grupo de segurança para o qual os membros podem criar Grupos do Office 365 mesmo quando EnableGroupCreation == falso. |
|  <ul><li>UsageGuidelinesUrl<li>Digite: Cadeia de caracteres<li>Padrão: “” |Um link para as Diretrizes de Uso do Grupo. |
|  <ul><li>ClassificationDescriptions<li>Digite: Cadeia de caracteres<li>Padrão: “” | Uma lista delimitada por vírgulas de descrições de classificação. O valor de ClassificationDescriptions somente é válido neste formato:
  $setting[“ClassificationDescriptions”] ="Classification:Description,Classification:Description", onde Classification corresponde às cadeia de caracteres no ClassificationList.|
|  <ul><li>DefaultClassification<li>Digite: Cadeia de caracteres<li>Padrão: “” | A classificação que deve ser usada como a classificação padrão para um grupo, se nenhuma for especificada.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Digite: Cadeia de caracteres<li>Padrão: “” | Cadeia de caracteres de um comprimento máximo de 64 caracteres que define a convenção de nomenclatura configurada para Grupos do Office 365. Para obter mais informações, confira [Impor uma política de nomenclatura para grupos do Office 365](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Digite: Cadeia de caracteres<li>Padrão: “” | Cadeia de caracteres de frases separadas por vírgulas que os usuários não serão autorizados a usar em nomes de grupos ou alias. Para obter mais informações, confira [Impor uma política de nomenclatura para grupos do Office 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Digite: BOOLEAN<li>Padrão: “False” | Não use
|  <ul><li>AllowGuestsToBeGroupOwner<li>Digite: BOOLEAN<li>Padrão: Falso | Booliano que indica se um usuário convidado pode ser um proprietário de grupos. |
|  <ul><li>AllowGuestsToAccessGroups<li>Digite: BOOLEAN<li>Padrão: True | Booliano que indica se um usuário convidado pode ter acesso ao conteúdo dos Grupos do Office 365.  Essa configuração não requer uma licença do Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Digite: Cadeia de caracteres<li>Padrão: “” | A url de um link para as diretrizes de uso do convidado. |
|  <ul><li>AllowToAddGuests<li>Digite: BOOLEAN<li>Padrão: True | Um booliano que indica se há permissão para adicionar convidados a este diretório.|
|  <ul><li>ClassificationList<li>Digite: Cadeia de caracteres<li>Padrão: “” |Uma lista delimitada por vírgulas de valores de classificação válidos que podem ser aplicados aos Grupos do Office 365. |

## <a name="read-settings-at-the-directory-level"></a>Ler configurações no nível do diretório
Estas etapas leem configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.

1. Leia todas as configurações existentes do diretório:
  ```powershell
  Get-AzureADDirectorySetting -All $True
  ```
  Esse cmdlet retorna uma lista de todas as configurações de diretório:
  ```powershell
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Leia todas as configurações para um grupo específico:
  ```powershell
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Leia todos os valores de configurações de diretório de um objeto de configurações de diretório específico, usando o GUID da ID de Configurações:
  ```powershell
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Esse cmdlet retorna os nomes e valores nesse objeto de configurações para esse grupo específico:
  ```powershell
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  CustomBlockedWordsList        
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            https://guideline.example.com
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Atualizar as configurações para um grupo específico

1. Procurar o modelo de configurações denominado "Groups.Unified.Guest"
  ```powershell
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Recupere o objeto de modelo para o modelo Groups.Unified.Guest:
  ```powershell
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Crie um novo objeto de configurações do modelo:
  ```powershell
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Defina a configuração com o valor necessário:
  ```powershell
  $Setting["AllowToAddGuests"]=$False
  ```
5. Crie a nova configuração para o grupo exigido no diretório:
  ```powershell
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Atualizar configurações no nível do diretório

Estas etapas atualizam as configurações no nível do diretório, que se aplicam a todos os Grupos do Office 365 no diretório. Estes exemplos pressupõem que já exista um objeto Configurações em seu diretório.

1. Encontre o objeto Configurações existente:
  ```powershell
  $setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ```
2. Atualize o valor:
  
  ```powershell
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Atualize a configuração:
  
  ```powershell
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Remover configurações no nível do diretório
Esta etapa remove configurações no nível do diretório, que se aplicam a todos os grupos do Office no diretório.
  ```powershell
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Referência de sintaxe de cmdlet
Você pode encontrar mais documentação do PowerShell do Azure Active Directory em [Cmdlets do Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Leitura adicional

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](../fundamentals/active-directory-manage-groups.md)
* [Integração de suas identidades locais com o Active Directory do Azure](../hybrid/whatis-hybrid-identity.md)
