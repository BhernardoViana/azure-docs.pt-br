---
title: Colete dados de log com o agente do Azure Log Analytics | Microsoft Docs
description: Este tópico ajuda o reconhecimento de como coletar dados e monitorar computadores hospedados no Azure, no local ou em outro ambiente de nuvem com o Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: 33ba07ac8d89546856666cc7ab94fae650020001
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74306526"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Coletar dados de log com o agente de Log Analytics

O agente do Azure Log Analytics, anteriormente conhecido como agente do Microsoft Monitoring Agent (MMA) ou OMS Linux, foi desenvolvido para gerenciamento abrangente em máquinas locais, computadores monitorados pelo [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/), e máquinas virtuais em qualquer nuvem. Os agentes do Windows e Linux se conectam a um Azure Monitor e armazenam dados de log coletados de fontes diferentes em seu espaço de trabalho Log Analytics, bem como quaisquer logs ou métricas exclusivos, conforme definido em uma solução de monitoramento. 

Este artigo fornece uma visão geral detalhada do agente, dos requisitos do sistema e da rede e dos diferentes métodos de implantação.

## <a name="overview"></a>Visão geral

![Diagrama de comunicação do agente do Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

Antes de analisar e agir sobre os dados coletados, primeiro você precisa instalar e conectar agentes para todos os computadores que deseja enviar dados para o serviço de Azure Monitor. Você pode instalar agentes em suas VMs do Azure usando a extensão VM do Log do Azure para Windows e Linux e para computadores em um ambiente híbrido usando a instalação, a linha de comando ou o DSC (Configuração de Estado Desejado) na Automação do Azure. 

O agente para Linux e Windows comunica a saída para o serviço de Azure Monitor pela porta TCP 443 e, se o computador se conecta por meio de um firewall ou servidor proxy para se comunicar pela Internet, examine os requisitos abaixo para entender a configuração de rede Necessário. Se as suas políticas de segurança de ti não permitirem que computadores na rede se conectem à Internet, você poderá configurar um [Gateway de log Analytics](gateway.md) e, em seguida, configurar o agente para se conectar por meio do gateway para Azure monitor logs. O agente pode receber informações de configuração e enviar dados coletados, dependendo de quais regras de coleta de dados e soluções de monitoramento você habilitou em seu espaço de trabalho. 

Ao usar os agentes de Log Analytics para coletar dados, você precisa entender o seguinte para planejar a implantação do agente:

* Para coletar dados de agentes do Windows, você pode [configurar cada agente para reportar para um ou mais espaços de trabalho](agent-windows.md), mesmo enquanto estiver relatando a um grupo de gerenciamento de System Center Operations Manager. O agente do Windows pode relatar até quatro espaços de trabalho.
* O agente do Linux não dá suporte a hospedagem múltipla e só pode relatar para um único espaço de trabalho.
* O agente do Windows dá suporte ao [padrão FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation), enquanto o agente do Linux não oferece suporte a ele.  

Se você estiver usando o System Center Operations Manager 2012 R2 ou posterior:

* Cada grupo de gerenciamento de Operations Manager pode ser [conectado a apenas um espaço de trabalho](om-agents.md).
* Os computadores Linux que relatam para um grupo de gerenciamento devem ser configurados para relatar diretamente a um espaço de trabalho Log Analytics. Se os computadores Linux já estiverem se reportando diretamente a um espaço de trabalho e você quiser monitorá-los com Operations Manager, siga estas etapas para [relatar a um grupo de gerenciamento de Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group).
* Você pode instalar o Log Analytics agente do Windows no computador Windows e fazer com que ele se reporte a ambos Operations Manager integrados a um espaço de trabalho e a um espaço de trabalho diferente.

O agente para Linux e Windows não é apenas para se conectar ao Azure Monitor, ele também dá suporte à automação do Azure para hospedar a função de Hybrid runbook Worker e outros serviços, como [controle de alterações](../../automation/change-tracking.md), [Gerenciamento de atualizações](../../automation/automation-update-management.md)e [central de segurança do Azure](../../security-center/security-center-intro.md). Para obter mais informações sobre a função Hybrid Runbook Worker, consulte [Hybrid Runbook Worker de Automação do Azure](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Sistemas operacionais Windows compatíveis

Há suporte oficial para as seguintes versões do sistema operacional Windows para o agente para Windows:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, versão 1709 e 1803
* Windows 7 SP1, Windows 8 Enterprise e pro e Windows 10 Enterprise e pro

>[!NOTE]
>Embora o agente Log Analytics para Windows tenha sido projetado para dar suporte a cenários de monitoramento de servidor, percebemos que você pode executar o cliente Windows para dar suporte a cargas de trabalho configuradas e otimizadas para o sistema operacional do servidor. O agente oferece suporte ao Windows Client, mas nossas soluções de monitoramento não se concentram em cenários de monitoramento de clientes, a menos que explicitamente declarado.

## <a name="supported-linux-operating-systems"></a>Sistemas operacionais Linux com suporte

Esta seção fornece detalhes sobre as distribuições de Linux com suporte.

Começando com versões lançadas depois de agosto de 2018, estamos fazendo as seguintes alterações ao nosso modelo de suporte:  

* Somente o servidor versões têm suporte, um não cliente.  
* Novas versões de [distros do Azure Linux Endorsed](../../virtual-machines/linux/endorsed-distros.md) são sempre suportadas.  
* Todas as versões secundárias têm suporte para cada versão principal listada.
* As versões que passaram a data de fim de suporte do fabricante não são suportadas.  
* Não há suporte para novas versões do AMI.  
* Apenas versões que executam o SSL 1.x por padrão são suportadas.

>[!NOTE]
>Se você estiver usando uma distribuição ou versão que não é suportada no momento e não se alinha ao nosso modelo de suporte, recomendamos que você distribua esse repositório, reconhecendo que o suporte da Microsoft não fornecerá assistência com as versões do agente bifurcado.

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86 x64) e 7 (x64)  
* Oracle Linux 6 e 7 (x86 x64) 
* Red Hat Enterprise Linux Server 6 (x86 x64) e 7 (x64)
* Debian GNU/Linux 8 e 9 (x86 x64)
* Ubuntu 14.04 18.04 LTS (x64), 16.04 LTS (x86 x64) e LTS (x86 x64)
* SUSE Linux Enterprise Server 12 (x64) e 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 só tem suporte em plataformas de x86_x64 (64 bits) e OpenSSL mais cedo do que 1. x não tem suporte em qualquer plataforma.
>

### <a name="agent-prerequisites"></a>Pré-requisitos do agente

A tabela a seguir realça os pacotes necessários para distribuições do Linux com suporte em que o agente será instalado.

|Pacote necessário |DESCRIÇÃO |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca GNU C | 2.5-12 
|Openssl    | Bibliotecas OpenSSL | 1,0. x ou 1.1. x |
|Curl | cliente Web cURL | 7.15.5 |
|Python-ctypes | | 
|PAM | Módulos de autenticação conectáveis | | 

>[!NOTE]
>Rsyslog ou syslog-ng são necessários para coletar mensagens de syslog. O daemon syslog padrão na versão 5 do Red Hat Enterprise Linux, CentOS e na versão Oracle Linux (sysklog) não tem suporte para a coleta de eventos de syslog. Para coletar dados de syslog nessa versão das distribuições, o daemon rsyslog deverá ser instalado e configurado para substituir sysklog.

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para Azure Monitor logs, é altamente recomendável configurar o agente para usar pelo menos o protocolo TLS 1,2. Constatou-se que versões mais antigas do protocolo TLS/protocolo SSL eram vulneráveis e embora elas ainda funcionem no momento para permitir a compatibilidade com versões anteriores, elas **não são recomendadas**.  Para obter mais informações, examine [Enviando dados com segurança usando o TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-firewall-requirements"></a>Requisitos de firewall de rede

As informações abaixo listam as informações de configuração de proxy e firewall necessárias para que o agente do Linux e do Windows se comunique com os logs de Azure Monitor.  

|Recurso de agente|Portas |Direção |Ignorar a inspeção de HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Saída|sim |  
|*.oms.opinsights.azure.com |Porta 443 |Saída|sim |  
|*.blob.core.windows.net |Porta 443 |Saída|sim |  

Para obter informações de firewall necessárias para o Azure governamental, consulte [Gerenciamento do Azure governamental](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Se você planeja usar o Hybrid Runbook Worker de automação do Azure para se conectar e se registrar no serviço de automação para usar runbooks ou soluções de gerenciamento em seu ambiente, ele deve ter acesso ao número da porta e às URLs descritas em [configurar sua rede para o Hybrid runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

O agente do Windows e Linux dá suporte à comunicação por meio de um servidor proxy ou Log Analytics gateway para Azure Monitor usando o protocolo HTTPS.  Há suporte para a autenticação anônima e básica (nome de usuário/senha).  Para o agente do Windows conectado diretamente ao serviço, a configuração do proxy é especificada durante a instalação ou [após a implementação](agent-manage.md#update-proxy-settings) no Painel de Controle ou no PowerShell.  

Para o agente Linux, o servidor proxy pode ser especificado durante a instalação ou modificando o arquivo de configuração proxy.conf [após a instalação](agent-manage.md#update-proxy-settings).  O valor de configuração de proxy do agente do Linux tem a seguinte sintaxe:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Se seu servidor proxy não exigir autenticação, o agente para Linux exigirá mesmo assim fornecendo um pseudo usuário/senha. Isso pode ser qualquer nome de usuário ou senha.

|Propriedade| DESCRIÇÃO |
|--------|-------------|
|Protocolo | HTTPS |
|usuário | Nome de usuário opcional para autenticação de proxy |
|password | Senha opcional para autenticação de proxy |
|proxyhost | Endereço ou FQDN do servidor proxy/gateway do Log Analytics |
|porta | Número da porta opcional para o servidor proxy/gateway do Log Analytics |

Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Se você usar caracteres especiais, como “\@” em sua senha, você receberá um erro de conexão de proxy porque o valor é analisado incorretamente.  Para contornar esse problema, codifique a senha na URL usando uma ferramenta como [URLDecode](https://www.urldecoder.org/).  

## <a name="install-and-configure-agent"></a>Instalar e configurar o agente

Conectar computadores em sua assinatura do Azure ou ambiente híbrido diretamente com Azure Monitor logs pode ser feito usando métodos diferentes, dependendo de seus requisitos. A tabela a seguir realça cada método para determinar o que funciona melhor em sua organização.

|Fonte | Método | DESCRIÇÃO|
|-------|-------------|-------------|
|VM do Azure| - Extensão de VM do Log Analytics para [Windows](../../virtual-machines/extensions/oms-windows.md) ou [Linux](../../virtual-machines/extensions/oms-linux.md) usando a CLI do Azure ou com um modelo do Azure Resource Manager<br>- [manualmente no portal do Azure](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)<br>- o [provisionamento automático da central de segurança do Azure](../../security-center/security-center-enable-data-collection.md)| -A extensão instala o agente de Log Analytics em máquinas virtuais do Azure e os registra em um espaço de trabalho Azure Monitor existente.<br>-A central de segurança do Azure pode provisionar o agente de Log Analytics em todas as VMs do Azure com suporte e quaisquer novas que forem criadas se você habilitá-la para monitorar vulnerabilidades de segurança e ameaças. Se habilitada, qualquer VM nova ou existente sem um agente instalado será provisionada.|
| Computador Windows híbrido|- [Instalação manual](agent-windows.md)<br>- [DSC de Automação do Azure](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Modelo do Resource Manager com o Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Instalar o agente Microsoft Monitoring da linha de comando ou usando um método automatizado como DSC de automação do Azure, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications), ou com um modelo do Azure Resource Manager, se você implantou o Microsoft Azure Stack no seu datacenter.| 
| Computador Linux híbrido| [Instalação manual](../../azure-monitor/learn/quick-collect-linux-computer.md)|Instale o agente para Linux chamando um script de wrapper hospedado no GitHub. | 
| System Center Operations Manager|[Conectar o Operations Manager ao Log Analytics](../../azure-monitor/platform/om-agents.md) | Configure a integração entre Operations Manager e Azure Monitor logs para encaminhar os dados coletados de computadores com Windows relatando a um grupo de gerenciamento.|  

## <a name="next-steps"></a>Próximas etapas

* Consultar as [fontes de dados](../../azure-monitor/platform/agent-data-sources.md) para entender as fontes de dados disponíveis para coletar dados do sistema Windows ou Linux. 

* Saiba mais sobre [registrar consultas](../../azure-monitor/log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 

* Conheça as [soluções de monitoramento](../../azure-monitor/insights/solutions.md) que adicionam funcionalidade ao Azure Monitor e também coletam dados no espaço de trabalho do Log Analytics.
