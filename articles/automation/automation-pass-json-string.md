---
title: Passar um objeto JSON para um runbook de Automação do Azure
description: Como passar parâmetros para um runbook como um objeto JSON
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell,  runbook, json, automação do azure
ms.openlocfilehash: 5e1ab8d6bd2de24251851cfc60d270a2fef4090d
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918811"
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Passar um objeto JSON para um runbook de Automação do Azure

Isso pode ser útil para armazenar os dados que você deseja passar para um runbook em um arquivo JSON.
Por exemplo, você pode criar um arquivo JSON que contém todos os parâmetros que você deseja passar para um runbook.
Para fazer isso, você precisa converter o JSON para uma cadeia de caracteres e, em seguida, converter a cadeia de caracteres em um objeto do PowerShell antes de passar o seu conteúdo para o runbook.

Neste exemplo, vamos criar um script do PowerShell que chama [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) para iniciar um runbook do PowerShell, passando o conteúdo de JSON para o runbook.
O runbook do PowerShell inicia uma VM do Azure, obtendo os parâmetros para a VM de JSON que foi passado.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se em uma conta gratuita](https://azure.microsoft.com/free/).
* [Conta de automação](automation-sec-configure-azure-runas-account.md) para manter o runbook e se autenticar nos recursos do Azure.  Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Paramos e iniciamos o computador, portanto, ele não deve ser uma VM de produção.
* Azure PowerShell instalado em um computador local. Consulte [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) (Instalar e configurar o Azure PowerShell) para obter informações sobre como obter o Azure PowerShell.

## <a name="create-the-json-file"></a>Criar o arquivo JSON

Digite o seguinte teste em um arquivo de texto e salve-o como `test.json` em algum lugar no seu computador local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Criar o runbook

Crie um novo runbook PowerShell chamado "Test-Json" na Automação do Azure.
Para saber como criar um novo runbook do PowerShell, consulte [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).

Para aceitar os dados JSON, o runbook deve utilizar um objeto como um parâmetro de entrada.

O runbook pode, então, usar as propriedades definidas no JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Salve e publique este runbook na sua conta de Automação.

## <a name="call-the-runbook-from-powershell"></a>Chamar o runbook do PowerShell

Agora você pode chamar o runbook de seu computador local usando o Azure PowerShell.
Execute os seguintes comandos do PowerShell:

1. Faça logon no Azure:
   ```powershell
   Connect-AzureRmAccount
   ```
    Você recebe uma solicitação para inserir suas credenciais do Azure.

   > [!IMPORTANT]
   > **Connect-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Ao pesquisar sua biblioteca de itens, se você não vir **Connect-AzureRMAccount**, você pode usar **Connect-AzureRmAccount**, ou você pode atualizar seus módulos em sua Conta de Automação.

1. Obtenha o conteúdo do arquivo JSON e converta-o em uma cadeia de caracteres:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` é o caminho em que você salvou o arquivo JSON.
1. Converta o conteúdo da cadeia de caracteres do `$json` em um objeto do PowerShell:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Crie uma tabela de hash para os parâmetros para `Start-AzureRmAutomationRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Observe que você está definindo o valor de `Parameters` para o objeto do PowerShell que contém os valores do arquivo JSON. 
1. Iniciar o runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

O runbook usa os valores do arquivo JSON para iniciar uma VM.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como editar runbooks do PowerShell e do Fluxo de Trabalho do PowerShell com um editor de texto, consulte [Editando runbooks textuais na Automação do Azure](automation-edit-textual-runbook.md) 
* Para saber mais sobre a criação e a importação de runbooks, consulte [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md)


