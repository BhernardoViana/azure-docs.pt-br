---
title: Configurações de runbook na Automação do Azure
description: Descreve as definições de configuração de um runbook na Automação do Azure e como alterá-las usando o Portal do Azure e o Windows PowerShell.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 589df59e02a18629d5f405ff1ce8870333f2228e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397706"
---
# <a name="runbook-settings"></a>Configurações de runbook
Cada runbook na Automação do Azure tem várias configurações que ajudam a identificá-lo e a alterar seu comportamento de log. Cada uma dessas configurações é descrita abaixo seguida de procedimentos sobre como modificá-las.

## <a name="settings"></a>Configurações
### <a name="name-and-description"></a>Nome e descrição
Você não pode alterar o nome de um runbook após ele ter sido criado. A descrição é opcional e pode ter até 512 caracteres.

### <a name="tags"></a>Marcas
Os rótulos permitem que você atribua diferentes palavras e frases para ajudar a identificar um runbook. Por exemplo, quando você envia um runbook para a [Galeria do PowerShell](https://www.powershellgallery.com/), também define rótulos específicos para identificar em quais categorias o runbook deve estar listado. Você pode especificar vários rótulos para um runbook separando-os com vírgulas.

### <a name="logging"></a>Registro em log
Por padrão, os registros Detalhado e Progresso não são gravados no histórico do trabalho. Você pode alterar as configurações de um runbook específico para registrar logs. Para saber mais sobre esses registros, confira [Saída de Runbook e Mensagens](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Alterando as configurações de runbook

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Alterando as configurações de runbook com o portal do Azure
Você pode alterar as configurações de um runbook no portal do Azure na folha **Configurações** para o runbook.

1. No Portal do Azure, selecione **Automação** e, em seguida, clique no nome de uma conta de automação.
2. Selecione a guia **Runbooks** .
3. Clique no nome de um runbook e você será direcionado para a folha de configurações do runbook. Aqui, você pode especificar ou modificar marcas e a descrição do runbook, configurar definições de rastreamento e registro em log e acessar ferramentas de suporte para ajudar a resolver problemas.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Alterando as configurações de runbook com o Windows PowerShell
Você pode usar o cmdlet [Set-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/set-azurermautomationrunbook) para alterar as configurações de um runbook. Se você quiser especificar várias marcas, pode fornecer uma matriz ou uma única cadeia de caracteres com valores delimitados por vírgula para o parâmetro dos Rótulos. Você pode obter as marcas atuais com o [Get-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/get-azurermautomationrunbook).

Os comandos de exemplo a seguir mostram como definir as propriedades de um runbook. Este exemplo adiciona três rótulos aos rótulos existentes e especifica que registros detalhados devem ser registrados em log.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>Próximas etapas
* Para aprender a criar e recuperar mensagens de erro e de saída de runbooks, confira [Saída e mensagens de Runbook](automation-runbook-output-and-messages.md) 
* Para entender como adicionar um runbook que já foi desenvolvido pela comunidade ou de outra fonte, ou criar seu próprio runbook, confira [Criando ou importando um runbook](automation-creating-importing-runbook.md) 

