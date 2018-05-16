---
title: Instalar e configurar o PowerShell para início rápido do Azure pilha | Microsoft Docs
description: Saiba mais sobre como instalar e configurar o PowerShell para Azure pilha.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: 66598bda7ca1fcf5c6e05ab47232236b740177a6
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Colocar em funcionamento com o PowerShell na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Este guia de início rápido ajuda você a instalar e configurar um ambiente de pilha do Azure com o PowerShell. O script que fornecemos neste artigo tem como escopo o **operador Azure pilha** somente.

Este artigo é uma versão condensada das etapas descritas a [instalar o PowerShell]( azure-stack-powershell-install.md), [baixar ferramentas]( azure-stack-powershell-download.md), e [configurar o ambiente do PowerShell do operador Azure pilha]( azure-stack-powershell-configure-admin.md) artigos. Usando os scripts neste tópico, você pode definir o PowerShell para ambientes de pilha do Azure que são implantados com o Active Directory do Azure ou os serviços de Federação do Active Directory (AD FS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Configurar o PowerShell para implantações com base no Active Directory do Azure

Entrar no seu Kit de desenvolvimento de pilha do Azure, ou um cliente externo baseado em Windows se você estiver conectado por meio de VPN. Abra uma sessão de ISE do PowerShell com privilégios elevados e, em seguida, execute o seguinte script. Certifique-se de atualizar o **TenantName**, **ArmEndpoint**, e **GraphAudience** variáveis conforme necessário para a sua configuração de ambiente:

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configurar o PowerShell para implantações do AD FS com base em

Se você estiver operando Azure pilha quando conectado à internet, você pode usar o script a seguir. No entanto, se você estiver operando pilha do Azure sem conectividade com a internet, usar o [desconectada de maneira de instalar o PowerShell](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) e os cmdlets para configurar o PowerShell permanecerá mesmos conforme mostrado no script. Entrar no seu Kit de desenvolvimento de pilha do Azure, ou um cliente externo baseado em Windows se você estiver conectado por meio de VPN. Abra uma sessão de ISE do PowerShell com privilégios elevados e, em seguida, execute o seguinte script. Certifique-se de atualizar o **ArmEndpoint** e **GraphAudience** variáveis conforme necessário para a sua configuração de ambiente:

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Add-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que você configurou o PowerShell, você pode testar a configuração criando um grupo de recursos:

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

> [!note]  
> Para especificar um grupo de recursos, você precisará ter um grupo de recursos em sua assinatura. Para obter mais informações sobre assinaturas, consulte [visão geral de plano, oferta, cotas e assinatura](azure-stack-plan-offer-quota-overview.md)

Depois que o grupo de recursos é criado, o **estado de provisionamento** está definida como **êxito**.

## <a name="next-steps"></a>Próximas etapas

* [Instalar e configurar a CLI](azure-stack-connect-cli.md)

* [Desenvolver modelos](user/azure-stack-develop-templates.md)
