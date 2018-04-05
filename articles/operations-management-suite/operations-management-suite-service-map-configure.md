---
title: Configurar Mapa do Serviço no Azure | Microsoft Docs
description: O Mapa do Serviço é uma solução no Azure que descobre automaticamente os componentes do aplicativo nos sistemas Windows e Linux, e mapeia a comunicação entre os serviços. Este artigo fornece detalhes sobre a implantação do Mapa do Serviço em seu ambiente e sobre como usá-lo em diversos cenários.
services: operations-management-suite
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: c01d18b17906a2b243a46241a6ec5c4b1d9ab8d9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-service-map-in-azure"></a>Configurar Mapa do Serviço no Azure
O Mapa do Serviço detecta automaticamente os componentes de aplicativos em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Você pode usá-lo para exibir os seus servidores da maneira como pensa neles – como sistemas interconectados que fornecem serviços essenciais. O Mapa do Serviço mostra conexões entre servidores, processos e portas em qualquer arquitetura conectada a TCP sem nenhuma configuração necessária além da instalação de um agente.

Este artigo descreve os detalhes sobre a configuração do Mapa do Serviço e dos agentes de integração. Para saber mais sobre como usar o Mapa do Serviço, confira [Como usar a solução Mapa do Serviço no Azure](operations-management-suite-service-map.md).

## <a name="dependency-agent-downloads"></a>Downloads do Agente de Dependência
| Arquivo | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.5.0 | 8B8FE0F6B0A9F589C4B7B52945C2C25DF008058EB4D4866DC45EE2485062C9D7 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.5.0 | 4125A88E60650FF168D6254AB4FCD14CDD3CC1C7B4CF168F3F5F3C1AF30895DD  |


## <a name="connected-sources"></a>Fontes conectadas
O Mapa de Serviço obtém seus dados do Microsoft Dependency Agent. O Agente de Dependência depende do Agente do OMS para suas conexões ao Log Analytics. Isso significa que um servidor deve ter o agente do OMS instalado e configurado primeiro e, em seguida, o Dependency Agent pode ser instalado. A tabela a seguir descreve as fontes conectadas às quais a solução Mapa do Serviço dá suporte.

| Fonte conectada | Com suporte | DESCRIÇÃO |
|:--|:--|:--|
| Agentes do Windows | sim | O Mapa do Serviço analisa e coleta dados de computadores de agente do Windows. <br><br>Além do [Agente do OMS](../log-analytics/log-analytics-windows-agent.md), os agentes do Windows exigem o Microsoft Dependency Agent. Veja os [sistemas operacionais com suporte](#supported-operating-systems) para obter uma lista completa das versões de sistema operacional. |
| Agentes do Linux | sim | O Mapa do Serviço analisa e coleta dados de computadores de agente do Linux. <br><br>Além do [Agente do OMS](../log-analytics/log-analytics-linux-agents.md), os agentes do Linux exigem o Microsoft Dependency Agent. Veja os [sistemas operacionais com suporte](#supported-operating-systems) para obter uma lista completa das versões de sistema operacional. |
| Grupo de gerenciamento do System Center Operations Manager | sim | O Mapa do Serviço analisa e coleta dados de agentes do Windows e do Linux em um [grupo de gerenciamento do System Center Operations Manager](../log-analytics/log-analytics-om-agents.md) conectado. <br><br>Uma conexão direta do computador do agente do System Center Operations Manager para Log Analytics é necessária. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho do Log Analytics.|
| Conta de Armazenamento do Azure | Não  | O Mapa do Serviço coleta dados de computadores do agente e, portanto, não há nenhum dado dele a ser coletado do Armazenamento do Azure. |

O Mapa do Serviço só dá suporte a plataformas de 64 bits.

No Windows, o MMA (Microsoft Monitoring Agent) é usado pelo System Center Operations Manager e pelo Log Analytics para coletar e enviar dados de monitoramento. (Esse agente é chamado de Agente do System Center Operations Manager, Agente OMS, Agente do Log Analytics, MMA ou Agente Direto, dependendo do contexto.) O System Center Operations Manager e o Log Analytics fornecem versões do MMA diferentes prontas para uso. Essas versões podem relatar para o System Center Operations Manager, para o Log Analytics ou para ambos.  

No Linux, o Agente do OMS para Linux coleta e envia os dados de monitoramento para o Log Analytics. Você pode usar o Mapa do Serviço em servidores com Agentes Diretos do OMS ou em servidores anexados ao Log Analytics por meio de grupos de gerenciamento do System Center Operations Manager.  

Neste artigo, nos referimos a todos os agentes, sejam Linux ou Windows, sejam conectados a um grupo de gerenciamento do System Center Operations Manager ou diretamente ao Log Analytics, como “Agente do OMS”. Usaremos o nome de implantação específico do agente somente se ele for necessário para o contexto.

O agente do Mapa do Serviço não transmite todos os dados em si e não requer alterações em portas ou em firewalls. Os dados no Mapa do Serviço sempre são transmitidos pelo Agente do OMS ao Log Analytics, seja diretamente ou pelo Gateway do OMS.

![Agentes do Mapa do Serviço](media/oms-service-map/agents.png)

Se você for um cliente do System Center Operations Manager com um grupo de gerenciamento conectado ao Log Analytics:

- Se os agentes do System Center Operations Manager podem acessar a Internet para conectarem-se ao Log Analytics, nenhuma configuração adicional é necessária.  
- Se os agentes do System Center Operations Manager não puderem acessar o Log Analytics pela Internet, você precisará configurar o Gateway do OMS para operar com o System Center Operations Manager.
  
Se você estiver usando o Direct Agent do OMS, precisará configurar o próprio Agente do OMS para conexão ao Log Analytics ou ao Gateway do OMS. O Gateway do OMS pode ser baixado do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

### <a name="management-packs"></a>Pacotes de gerenciamento
Quando o Mapa do Serviço é ativado em um espaço de trabalho do Log Analytics, um pacote de gerenciamento de 300 KB é enviado a todos os servidores do Windows nesse espaço de trabalho. Se você estiver usando agentes do System Center Operations Manager em um [grupo de gerenciamento conectado](../log-analytics/log-analytics-om-agents.md), o pacote de gerenciamento do Mapa do Serviço será implantado do System Center Operations Manager. Se os agentes forem conectados diretamente, o Log Analytics fornecerá o pacote de gerenciamento.

O pacote de gerenciamento chama-se Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Ele é gravado em %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\. A fonte de dados usada pelo pacote de gerenciamento é %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="installation"></a>Instalação
### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Instalar o Agente de Dependência no Microsoft Windows
São necessários privilégios de administrador para instalar ou desinstalar o agente.

O Agente de Dependência é instalado em computadores com o Windows por meio de InstallDependencyAgent-Windows.exe. Se você executar o arquivo executável sem opções, ele iniciará um assistente que você poderá seguir para executar a instalação interativamente.  

Use as etapas a seguir para instalar o Dependency Agent em cada computador com o Windows:

1.  Instalar o Agente OMS usando as instruções em [Conectar computadores Windows ao serviço do Log Analytics no Azure](../log-analytics/log-analytics-windows-agent.md).
2.  Baixe o Agente do Windows e execute-o usando o comando a seguir: <br>`InstallDependencyAgent-Windows.exe`
3.  Acompanhe o assistente para instalar o agente.
4.  Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em Agentes do Windows, o diretório de log será %Programfiles%\Microsoft Dependency Agent\logs. 

#### <a name="windows-command-line"></a>Linha de comando do Windows
Use as opções da tabela a seguir para instalar a partir de uma linha de comando. Para ver uma lista dos sinalizadores de instalação, execute o instalador usando o sinalizador /? da seguinte maneira.

    InstallDependencyAgent-Windows.exe /?

| Sinalizador | DESCRIÇÃO |
|:--|:--|
| /? | Obtenha uma lista das opções de linha de comando. |
| /S | Realize uma instalação silenciosa sem solicitações ao usuário. |

Os arquivos do Agente de Dependência do Windows são colocados em C:\Program Files\Microsoft Dependency Agent por padrão.

### <a name="install-the-dependency-agent-on-linux"></a>Instalar o Agente de Dependência no Linux
O acesso root é necessário para instalar ou configurar o agente.

O Agente de Dependência é instalado em computadores com o Linux por meio do InstallDependencyAgent-Linux64.bin, um script de shell com um binário de extração automática. Você pode executar o arquivo usando sh ou adicionar permissões de execução ao próprio arquivo.
 
Use as seguintes etapas para instalar o Dependency Agent em cada computador com o Linux:

1.  Instale o Agente OMS usando as instruções em [Coletar e gerenciar dados de computadores com o Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.  Instale o Agente de Dependência para Linux como raiz usando o comando a seguir:<br>`sh InstallDependencyAgent-Linux64.bin`
3.  Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em agentes do Linux, o diretório de log é /var/opt/microsoft/dependency-agent/log.

Para ver uma lista dos sinalizadores de instalação, execute o programa de instalação com o sinalizador -help conforme demonstrado a seguir.

    InstallDependencyAgent-Linux64.bin -help

| Sinalizador | DESCRIÇÃO |
|:--|:--|
| -help | Obtenha uma lista das opções de linha de comando. |
| -s | Realize uma instalação silenciosa sem solicitações ao usuário. |
| --check | Verifica as permissões e o sistema operacional, mas não instala o agente. |

Os arquivos do Dependency Agent são colocados nos diretórios a seguir:

| Arquivos | Local padrão |
|:--|:--|
| Arquivos de núcleo | /opt/microsoft/dependency-agent |
| Arquivos de log | /var/opt/microsoft/dependency-agent/log |
| Arquivos de configuração | /etc/opt/microsoft/dependency-agent/config |
| Arquivos executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Arquivos de armazenamento binário | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exemplos de script de instalação
Para implantar facilmente o agente de dependência em vários servidores simultaneamente, convém usar um script. Você pode usar os exemplos de script a seguir para baixar e instalar o agente de dependência no Windows ou Linux.

### <a name="powershell-script-for-windows"></a>Script do PowerShell para Windows
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script de Shell para Linux
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="azure-vm-extension"></a>Extensão da VM do Azure
Você pode facilmente implantar o Agente de Dependência para suas VMs do Azure usando uma [Extensão de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Com a Extensão de VM do Azure, você pode implantar o Agente de Dependência para suas VMs por meio de um script do PowerShell ou diretamente no modelo do Azure Resource Manager da VM.  Há uma extensão disponível para Windows (DependencyAgentWindows) e para Linux (DependencyAgentLinux).  Se você implantar por meio da Extensão de VM do Azure, os agentes poderão ser atualizados automaticamente para as versões mais recentes.

Para implantar a Extensão de VM do Azure por meio do PowerShell, você pode usar o exemplo a seguir:
```PowerShell
#
# Deploy the Dependency Agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzureRmVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzureRmVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Uma maneira ainda mais fácil de garantir que o Agente de Dependência esteja em cada uma das VMs é incluir o agente no modelo do Azure Resource Manager.  Observe que o Agente de Dependência ainda depende do Agente do OMS, portanto, a [Extensão de VM do Agente do OMS](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-vm-extension) deve ser implantada primeiro.  O trecho de código do JSON a seguir pode ser adicionado à seção *resources* do modelo.
```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
"[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```


## <a name="desired-state-configuration"></a>Configuração de estado desejado
Para implantar o agente de dependência por meio da Desired State Configuration, você pode usar o módulo xPSDesiredStateConfiguration e um pouco de código semelhante ao seguinte:
```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency Agent
    xRemoteFile DAPackage 
    {
        Uri = "https://aka.ms/dependencyagentwindows"
        DestinationPath = $DAPackageLocalPath
    }

    xPackage DA
    {
        Ensure="Present"
        Name = "Dependency Agent"
        Path = $DAPackageLocalPath
        Arguments = '/S'
        ProductId = ""
        InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
        InstalledCheckRegValueName = "DisplayName"
        InstalledCheckRegValueData = "Dependency Agent"
        DependsOn = "[xRemoteFile]DAPackage"
    }
  }
}
```

## <a name="uninstallation"></a>Desinstalação
### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstalar o Agente de Dependência no Windows
O Agente de Dependência para Windows pode ser desinstalado por um administrador por meio do Painel de Controle.

Um administrador também pode executar %Programfiles%\Microsoft Agent\Uninstall.exe para desinstalar o Agente de Dependência.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstalar o Agente de Dependência no Linux
Você pode desinstalar o Agente de Dependência do Linux com o seguinte comando.
<br>RHEL, CentOs ou Oracle:
```
sudo rpm -e dependency-agent
```
Ubuntu:
```
sudo apt -y purge dependency-agent
```
## <a name="troubleshooting"></a>solução de problemas
Se você enfrentar problemas ao instalar ou executar o Mapa do Serviço, esta seção poderá lhe ajudar. Se ainda não for possível resolver o problema, entre em contato com o Suporte da Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problemas de instalação do Agente de Dependência
#### <a name="installer-asks-for-a-reboot"></a>O instalador solicita uma reinicialização
O Agente de Dependência *geralmente* não exige uma reinicialização após a instalação ou desinstalação. No entanto, em alguns casos raros, o Windows Server exige uma reinicialização para continuar com uma instalação. Isso ocorre quando uma dependência, normalmente Pacotes Redistribuíveis do Microsoft Visual C++, exige uma reinicialização devido a um arquivo bloqueado.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>A mensagem "Não é possível instalar o Agente de Dependência: as bibliotecas de tempo de execução do Visual Studio não foram instaladas (código = [número_de_código])" aparece

O Microsoft Dependency Agent é compilado com base nas bibliotecas de tempo de execução do Microsoft Visual Studio. Você receberá uma mensagem se houver um problema durante a instalação das bibliotecas. 

Os instaladores da biblioteca de tempo de execução criam logs na pasta %LOCALAPPDATA%\temp. O arquivo é dd_vcredist_arch_yyyymmddhhmmss.log, em que *arch* é "x86" ou "amd64" e *aaaammddhhmmss* é a data e hora (relógio de 24 horas) em que o log foi criado. O log fornece detalhes sobre o problema que está bloqueando a instalação.

Pode ser útil instalar primeiro você mesmo as [bibliotecas de tempo de execução mais recentes](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

A tabela a seguir lista números de código e soluções sugeridas.

| Código | DESCRIÇÃO | Resolução |
|:--|:--|:--|
| 0x17 | O instalador da biblioteca exige uma atualização do Windows que não foi instalada. | Procure no log do instalador de biblioteca mais recente.<br><br>Se uma referência ao "Windows8.1-KB2999226-x64.msu" é seguida por uma linha "Erro 0x80240017: falha ao executar o pacote MSU.", você não tem os pré-requisitos para instalar o KB2999226. Siga as instruções na seção de pré-requisitos em [Tempo de Execução C Universal no Windows](https://support.microsoft.com/kb/2999226). Talvez seja necessário executar o Windows Update e reiniciar várias vezes para instalar os pré-requisitos.<br><br>Execute novamente o instalador do Agente de Dependência da Microsoft. |

### <a name="post-installation-issues"></a>Problemas pós-instalação
#### <a name="server-doesnt-appear-in-service-map"></a>O servidor não aparece no Mapa do Serviço
Se a instalação do Agente de Dependência for bem-sucedida, mas você não vir seu servidor na solução Mapa do Serviço:
* O Agente de Dependência foi instalado com êxito? Confirme isso verificando se o serviço está instalado e em execução.<br><br>
**Windows**: Procure o serviço denominado "Microsoft Dependency Agent".<br>
**Linux**: Procure o processo em execução "microsoft-dependency-agent".

* Você está no [tipo de preço Gratuito do Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)? O plano Gratuito permite até cinco servidores de Mapa do Serviço exclusivos. Quaisquer servidores subsequentes serão exibidos no Mapa do Serviço, mesmo que os cinco anteriores não estejam enviando dados.

* O servidor está enviando dados de log e desempenho para o Log Analytics? Acesse a Pesquisa de Logs e execute a consulta a seguir para seu computador: 

        * Computer="<your computer name here>" | measure count() by Type
        
  Você obteve uma variedade de eventos nos resultados? Os dados são recentes? Se sim, o Agente do OMS está operando corretamente e se comunicando com o Log Analytics. Caso contrário, verifique o Agente OMS no servidor: [Solução de problemas do Agente OMS para Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) ou [Solução de problemas do Agente OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>O servidor aparece no Mapa do Serviço, mas sem processos
Se você vir seu servidor no Mapa do Serviço, mas ele não tiver processos ou dados de conexão, isso indica que o Agente de Dependência está instalado e em execução, mas o driver do kernel não foi carregado. 

Verifique o arquivo C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) ou o arquivo /var/opt/microsoft/dependency-agent/log/service.log (Linux). As últimas linhas do arquivo devem indicar por que o kernel não foi carregado. Por exemplo, se o kernel tiver sido atualizado por você, talvez o Linux não dê suporte a ele.

## <a name="data-collection"></a>Coleta de dados
Você pode esperar que cada agente transmita aproximadamente 25 MB por dia, dependendo da complexidade das dependências do seu sistema. Os dados de dependência do Mapa do Serviço são enviados por cada agente a cada 15 segundos.  

O Agente de Dependência geralmente consome 0,1% da memória do sistema e 0,1% da CPU do sistema.

## <a name="supported-azure-regions"></a>Regiões do Azure com suporte
O Mapa do Serviço está disponível atualmente nas seguintes regiões do Azure:
- Leste dos EUA
- Europa Ocidental
- Centro-Oeste dos EUA
- Sudeste Asiático


## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
As seções a seguir listam os sistemas operacionais com suporte para o Dependency Agent. O Mapa do Serviço não dá suporte a arquiteturas de 32 bits de nenhum sistema operacional.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Área de trabalho do Windows
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Oracle Linux (com Kernel RHEL)
- Somente as versões de kernel padrão e Linux SMP têm suporte.
- Nenhuma distribuição do Linux dá suporte às versões de kernel não padrão, como PAE e Xen. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão "2.6.16.21-0.8-xen".
- Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.
- Não há suporte para o kernel CentOSPlus.
- O UEK (Unbreakable Enterprise Kernel) da Oracle é abordado em uma seção posterior deste artigo.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Versão do SO | Versão do kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Versão do SO | Versão do kernel |
|:--|:--|
| 6,0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Versão do SO | Versão do kernel |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419<br>2.6.18-420 |

### <a name="ubuntu-server"></a>Ubuntu Server
- Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.

| Versão do SO | Versão do kernel |
|:--|:--|
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux com Unbreakable Enterprise Kernel
#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versão do SO | Versão do kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versão do SO | Versão do kernel
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versão do SO | Versão do kernel
|:--|:--|
| 11 SP2 | 3.0.101-0.7 |
| 11 SP3 | 3.0.101-0.47 |
| 11 SP4 | 3.0.101-65 |


## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico
A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Mapa do Serviço. A Microsoft usa esses dados para fornecer e aprimorar a qualidade, a segurança e a integridade do serviço Mapa do Serviço. Os dados incluem informações sobre a configuração do software, como o sistema operacional e a versão. Ele também inclui o endereço IP, o nome DNS e o nome da estação de trabalho para fornecer recursos de solução de problemas precisos e eficientes. Não coletamos nomes, endereços ou outras informações de contato.

Para saber mais sobre o uso e a coleta de dados, veja a [Política de Privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Próximas etapas
- Saiba como [usar o Mapa do Serviço](operations-management-suite-service-map.md) após ele ter sido implantado e configurado.
