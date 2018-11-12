---
title: Ativos de credenciais na Automação do Azure
description: Os ativos de credenciais na Automação do Azure contêm credenciais de segurança que podem ser usadas para a autenticação em recursos acessados pelo runbook ou pela configuração DSC. Este artigo descreve como criar ativos de credenciais e usá-los em um runbook ou uma configuração DSC.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 07452a97ba16d7745d48e210dd90ce077be64683
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233432"
---
# <a name="credential-assets-in-azure-automation"></a>Ativos de credenciais na Automação do Azure

Um ativo de credencial de automação contém um objeto que contém as credenciais de segurança, como um nome de usuário e uma senha. Runbooks e configurações DSC podem usar cmdlets que aceitam um objeto PSCredential para autenticação ou eles podem extrair o nome de usuário e a senha do objeto PSCredential para fornecê-los a algum aplicativo ou serviço que exija a autenticação. As propriedades de uma credencial são armazenadas com segurança na Automação do Azure e podem ser acessadas no runbook ou na configuração DSC com a atividade [Get-AutomationPSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) .

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada no Key Vault. Antes de armazenar um ativo seguro, a chave é carregada do Key Vault e usada para criptografar o ativo.

## <a name="azure-classic-powershell-cmdlets"></a>Cmdlets do PowerShell Clássico do Azure

Os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell.  Eles são fornecidos como parte do [módulo do Azure PowerShell](/powershell/azure/overview) que está disponível para uso em runbooks e na configuração DSC da Automação.

| Cmdlets | DESCRIÇÃO |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Recupera informações sobre um ativo de credencial. Você só pode recuperar a credencial propriamente dita da atividade **Get-AutomationPSCredential** . |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Cria uma nova credencial de Automação. |
| [Remove- AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Remove uma credencial de Automação. |
| [Set- AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Define as propriedades de uma credencial de Automação existente. |

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do AzureRM PowerShell

Para o AzureRM, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell.  Eles são fornecidos como parte do [módulo de AzureRM.Automation](/powershell/azure/overview) que está disponível para uso em runbooks de automação e na configuração de DSC.

| Cmdlets | DESCRIÇÃO |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Recupera informações sobre um ativo de credencial.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Cria uma nova credencial de Automação. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Remove uma credencial de Automação. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Define as propriedades de uma credencial de Automação existente. |

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar credenciais em um runbook ou em uma configuração DSC.

| Atividades | DESCRIÇÃO |
|:--- |:--- |
| Get-AutomationPSCredential |Obtém uma credencial a ser usada em um runbook ou configuração DSC. Retorna um objeto [System.Management.Automation.PSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential) . |

> [!NOTE]
> Evite usar variáveis no parâmetro –Name de Get-AutomationPSCredential, pois isso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de credenciais no momento do design.

## <a name="python2-functions"></a>Funções Python2

A função na tabela a seguir é usada para acessar credenciais em um runbook em Python2.

| Função | DESCRIÇÃO |
|:---|:---|
| automationassets.get_automation_credential | Recupera informações sobre um ativo de credencial. |

> [!NOTE]
> É necessário importar o módulo "automationassets", na parte superior do runbook Python para acessar as funções do ativo.

## <a name="creating-a-new-credential-asset"></a>Criando um novo ativo de credencial

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Para criar um novo ativo de credencial com o portal do Azure

1. Em sua conta de automação, clique na parte **Ativos** para abrir a folha **Ativos**.
2. Clique na parte **Credenciais** para abrir a folha **Credenciais**.
3. Clique em **Adicionar uma credencial** na parte superior da folha.
4. Preencha o formulário e clique em **Criar** para salvar a nova credencial.

> [!NOTE]
> As contas de usuário que usam autenticação multifator não têm suporte para uso na Automação do Azure.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Para criar um novo ativo de credencial com o Windows PowerShell

Os comandos de exemplo a seguir mostram como criar uma nova credencial de automação. Um objeto PSCredential é criado pela primeira vez com o nome e a senha e, em seguida, é usado para criar o ativo de credencial. Como alternativa, você pode usar o cmdlet **Get-Credential** para ser solicitado a digitar um nome e uma senha.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Usando uma credencial do PowerShell

Recupere um ativo de credencial em um runbook ou configuração DSC com a atividade **Get-AutomationPSCredential** . Isso retorna um [objeto PSCredential](https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) que você pode usar com uma atividade ou cmdlet que exija um parâmetro PSCredential. Você também pode recuperar as propriedades do objeto de credencial para usar individualmente. O objeto tem uma propriedade para o nome de usuário e a senha segura, ou você pode usar o método **GetNetworkCredential** para retornar um objeto [NetworkCredential](https://msdn.microsoft.com/library/system.net.networkcredential.aspx) que fornecerá uma versão sem segurança da senha.

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual

Os comandos de exemplo a seguir mostram como usar uma credencial do PowerShell em um runbook. Neste exemplo, a credencial é recuperada e seu nome de usuário e senha são atribuídos a variáveis.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

### <a name="graphical-runbook-sample"></a>Exemplo de runbook gráfico

Adicione uma atividade **Get-AutomationPSCredential** a um runbook gráfico clicando com o botão direito na credencial no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicionar a credencial à tela](media/automation-credentials/credential-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma credencial em um runbook gráfico.  Nesse caso, ela está sendo usada para fornecer autenticação para um runbook para os recursos do Azure, conforme descrito em [Autenticar Runbooks com uma conta de Usuário do Azure AD](automation-create-aduser-account.md).  A primeira atividade recupera a credencial que tem acesso à assinatura do Azure.  A atividade **Add-AzureAccount** usa essa credencial para fornecer autenticação para qualquer atividade que vier depois dela.  Um [link de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) está disponível, já que **Get-AutomationPSCredential** está esperando um único objeto.  

![Adicionar a credencial à tela](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Usando uma credencial do PowerShell na DSC

Embora as Configurações DSC na Automação do Azure possam fazer referência aos ativos de credencial usando **Get-AutomationPSCredential**, os ativos de credencial também podem ser transmitidos por meio de parâmetros, se desejado. Para obter mais informações, veja [Compilando configurações na DSC de Automação do Azure](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Usando credenciais no Python2

A seguir, veja um exemplo de como acessar as credenciais em runbooks Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre links na criação gráfica, veja [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow)
* Para entender os diferentes métodos de autenticação com Automação, consulte [Segurança da Automação do Azure](automation-security-overview.md)
* Para começar a usar os runbooks Gráficos, consulte [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md) 
* Para começar a usar runbooks Python2, consulte [Meu primeiro runbook Python2](automation-first-runbook-textual-python2.md) 

