---
title: Entender a integridade do Windows Hybrid Runbook Worker no Azure Gerenciamento de Atualizações
description: Saiba como solucionar problemas com o Hybrid Runbook Worker no Windows que oferece suporte a Gerenciamento de Atualizações.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 284376759e9e2da1f42bd04eea6e564c9690d4a9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850118"
---
# <a name="understand-the-windows-hybrid-runbook-worker-health-in-update-management"></a>Entenda a integridade do Windows Hybrid Runbook Worker no Gerenciamento de Atualizações

Pode haver vários motivos pelos quais o computador não mostra o status **Pronto** no Gerenciamento de Atualizações. No Gerenciamento de Atualizações, você pode verificar a integridade de um agente de Hybrid Runbook Worker para determinar o problema subjacente. Este artigo discute como executar a solução de problemas para computadores do Azure por meio do portal do Azure e de computadores não Azure no [cenário offline](#troubleshoot-offline).

A lista a seguir é composta pelos três estados de preparação em que um computador pode estar:

* **Pronto** -a Hybrid runbook Worker é implantada e foi vista pela última vez há menos de 1 hora.
* **Desconectado** -a Hybrid runbook Worker é implantada e foi vista pela última vez há uma hora.
* **Não configurado** -a Hybrid runbook Worker não foi encontrada ou não terminou a integração.

> [!NOTE]
> Pode haver um pequeno atraso entre o que o portal do Azure mostra e o estado atual do computador.

## <a name="start-the-troubleshooter"></a>Iniciar a solução de problemas

Para computadores do Azure, ao clicar no link **Solução de Problemas** na coluna **Preparação do Agente de Atualização** no portal, a página **Solucionar Problemas do Agente de Atualização** será iniciada. Para computadores não Azure, o link o leva a este artigo. Consulte as [instruções offline](#troubleshoot-offline) para solucionar problemas em um computador não Azure.

![Atualizar lista de gerenciamento de máquinas virtuais](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Para verificar a integridade do Hybrid Runbook Worker, a VM deve estar em execução. Se a VM não estiver em execução, será exibido o botão **Iniciar a VM**.

Sobre o **solucionar problemas de atualização de agente** página, selecione **executar verificações de** para iniciar a solução de problemas. A solução de problemas usa o [comando executar](../../virtual-machines/windows/run-command.md) para executar um script no computador para verificar as dependências. Quando a solução de problemas é concluída, ela retorna o resultado das verificações.

![Solucionar problemas de agente de atualização de página](../media/update-agent-issues/troubleshoot-page.png)

Os resultados são mostrados na página quando estão prontos. As seções de verificações mostram o que está incluído em cada verificação.

![Solucionar problemas de verificações do agente de atualização](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operacional

A verificação do sistema operacional verifica se o Hybrid Runbook Worker está executando um dos seguintes sistemas operacionais:

|Sistema operacional  |Notas  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Suporta apenas avaliações de atualização.         |
|Windows Server 2008 R2 SP1 e posterior |.NET Framework 4,6 ou posterior é necessário. ([Fazer o download do .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> O Windows PowerShell 5,1 é necessário.  ([Faça o download do Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

A verificação de .NET Framework verifica se o sistema tem um mínimo de [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) instalado.

### <a name="wmf-51"></a>WMF 5.1

A verificação do WMF verifica se o sistema tem a versão necessária do WMF (Windows Management Framework) – [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Essa verificação determina se você está usando o TLS 1.2 para criptografar suas comunicações. O TLS 1.0 não é mais suportado pela plataforma. Recomendamos que os clientes usem o TLS 1.2 para se comunicar com o Gerenciamento de Atualizações.

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="registration-endpoint"></a>Ponto de extremidade de registro

Essa verificação determina se o agente pode se comunicar corretamente com o serviço do agente.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o ponto de extremidade de registro. Para obter uma lista de endereços e portas a serem abertas, consulte [Planejamento de rede para trabalhadores híbridos](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Ponto de extremidade de operações

Essa verificação determina se o agente pode se comunicar corretamente com o Serviço de dados do runtime da tarefa.

As configurações de proxy e firewall devem permitir que o agente do Hybrid Runbook Worker se comunique com o Serviço de Dados de Runtime do Trabalho. Para obter uma lista de endereços e portas a serem abertas, consulte [Planejamento de rede para trabalhadores híbridos](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Verificações de integridade do serviço de VM

### <a name="monitoring-agent-service-status"></a>Monitoramento de status de serviço do agente

Essa verificação determina se `HealthService`, o Microsoft Monitoring Agent, está em execução no computador.

Para saber mais sobre a solução de problemas do serviço, confira [O Microsoft Monitoring Agent não está em execução](hybrid-runbook-worker.md#mma-not-running).

Para reinstalar o Microsoft Monitoring Agent, consulte [Instalar e configurar o Microsoft Monitoring Agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Monitoramento de eventos de serviço de agente

Essa verificação determina se qualquer evento `4502` aparece no log do Azure Operations Manager na máquina nas últimas 24 horas.

Para saber mais sobre esse evento, confira o [guia de solução de problemas](hybrid-runbook-worker.md#event-4502) para este evento.

## <a name="access-permissions-checks"></a>Verificações de permissões de acesso

### <a name="machinekeys-folder-access"></a>Acesso à pasta MachineKeys

A verificação de acesso à pasta Crypto determina se a conta do sistema local tem acesso a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a>Solucionar problemas offline

Você pode usar o solucionador de problemas em um Hybrid Runbook Worker offline, executando o script localmente. Você pode obter o script [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration), na Galeria do PowerShell. Você deve ter o WMF 4,0 ou superior instalado para executar o script. Para baixar a versão mais recente do PowerShell, consulte [Instalando várias versões do PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

A saída deste script se parece com o seguinte exemplo:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Próximos passos

Para solucionar mais problemas com os trabalhadores de runbook híbridos, consulte [Solucionar problemas de trabalhadores de runbook híbridos](hybrid-runbook-worker.md).
