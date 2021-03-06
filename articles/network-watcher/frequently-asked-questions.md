---
title: Perguntas frequentes (FAQ) sobre o observador de rede do Azure | Microsoft Docs
description: Este artigo responde às perguntas frequentes sobre o serviço observador de rede do Azure.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: 3305590f2d8abf0d894bc1df42b84edcc96a2b2d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598232"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Perguntas frequentes (FAQ) sobre o observador de rede do Azure
O serviço [observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornece um conjunto de ferramentas para monitorar, diagnosticar, exibir métricas e habilitar ou desabilitar logs de recursos em uma rede virtual do Azure. Este artigo responde a perguntas comuns sobre o serviço.

## <a name="general"></a>Geral

### <a name="what-is-network-watcher"></a>O que é o observador de rede?
O observador de rede foi projetado para monitorar e reparar a integridade da rede de componentes IaaS (infraestrutura como serviço), que inclui máquinas virtuais, redes virtuais, gateways de aplicativo, balanceadores de carga e outros recursos em uma rede virtual do Azure. Não é uma solução para monitorar a infraestrutura PaaS (plataforma como serviço) ou obter a análise Web/móvel.

### <a name="what-tools-does-network-watcher-provide"></a>Quais ferramentas o observador de rede fornece?
O observador de rede fornece três conjuntos principais de recursos
* Monitoramento
  * A [exibição de topologia](https://docs.microsoft.com/azure/network-watcher/view-network-topology) mostra os recursos em sua rede virtual e as relações entre eles.
  * O [Monitor de conexão](https://docs.microsoft.com/azure/network-watcher/connection-monitor) permite monitorar a conectividade e a latência entre uma VM e outro recurso de rede.
  * O [Monitor de desempenho de rede](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) permite que você monitore a conectividade e as latências entre arquiteturas de rede híbridas, circuitos de Expressroute e pontos de extremidade de serviço/aplicativo.  
* Diagnóstico
  * A [verificação de fluxo de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) permite detectar problemas de filtragem de tráfego em um nível de VM.
  * O [próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) ajuda a verificar as rotas de tráfego e detectar problemas de roteamento.
  * A [solução de problemas de conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) permite uma verificação única de conectividade e latência entre uma VM e outro recurso de rede.
  * A [captura de pacotes](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) permite capturar todo o tráfego em uma VM em sua rede virtual.
  * A [solução de problemas de VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) executa várias verificações de diagnóstico em seus gateways de VPN e conexões para ajudar a depurar problemas.
* Registro em log
  * Os [logs de fluxo do NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) permitem registrar todo o tráfego em seus [NSGs (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/security-overview)
  * O [análise de tráfego](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) processa seus dados de log de fluxo do NSG, permitindo que você visualize, consulte, analise e entenda o tráfego de rede.


Para obter informações mais detalhadas, consulte a [página Visão geral do observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Como funciona o preço do observador de rede?
Visite a [página de preços](https://azure.microsoft.com/pricing/details/network-watcher/) dos componentes do observador de rede e seus preços.

### <a name="which-regions-is-network-watcher-available-in"></a>Em quais regiões o observador de rede está disponível?
Você pode exibir a disponibilidade regional mais recente na [página de disponibilidade do serviço do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="what-are-resource-limits-on-network-watcher"></a>O que são limites de recursos no observador de rede?
Consulte a página [limites de serviço](https://docs.microsoft.com/azure/azure-subscription-service-limits#network-watcher-limits) para todos os limites.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Por que apenas uma instância do observador de rede é permitida por região?
O observador de rede precisa ser habilitado apenas uma vez para uma assinatura para que seus recursos funcionem, esse não é um limite de serviço.

## <a name="nsg-flow-logs"></a>Logs de fluxo do NSG

### <a name="what-does-nsg-flow-logs-do"></a>O que os logs de fluxo do NSG fazem?
Os recursos de rede do Azure podem ser combinados e gerenciados por meio [de NSGs (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/security-overview). Os logs de fluxo do NSG permitem que você registre informações de fluxo de 5 tuplas sobre todo o tráfego por meio de seu NSGs. Os logs de fluxo brutos são gravados em uma conta de armazenamento do Azure de onde eles podem ser processados, analisados, consultados ou exportados conforme necessário.

### <a name="are-there-any-caveats-to-using-nsg-flow-logs"></a>Há alguma advertência para usar os logs de fluxo do NSG?
Não há pré-requisitos para o uso de logs de fluxo de NSG. No entanto, há duas limitações
- Os **pontos de extremidade de serviço não devem estar presentes em sua VNET**: os logs de fluxo NSG são emitidos de agentes em suas VMs para contas de armazenamento. No entanto, hoje você só pode emitir logs diretamente para contas de armazenamento e não pode usar um ponto de extremidade de serviço adicionado à sua VNET.

- A **conta de armazenamento não deve ser armazenada em Firewall**: devido a limitações internas, as contas de armazenamento devem estar acessíveis por meio da Internet pública para que os logs de fluxo do NSG funcionem com elas. O tráfego ainda será roteado por meio do Azure internamente e você não enfrentará encargos de egresso extras.

Consulte as duas próximas perguntas para obter instruções sobre como contornar esses problemas. Espera-se que ambas as limitações sejam abordadas por Jan 2020.

### <a name="how-do-i-use-nsg-flow-logs-with-service-endpoints"></a>Como fazer usar logs de fluxo NSG com pontos de extremidade de serviço?

*Opção 1: reconfigurar os logs de fluxo de NSG para emitir para a conta de armazenamento do Azure sem pontos de extremidade de VNET*

* Localizar sub-redes com pontos de extremidade:

    - Na portal do Azure, procure grupos de **recursos** na pesquisa global na parte superior
    - Navegue até o grupo de recursos que contém o NSG com o qual você está trabalhando
    - Use a segunda lista suspensa para filtrar por tipo e selecionar **redes virtuais**
    - Clique na rede virtual que contém os pontos de extremidade de serviço
    - Selecione **pontos de extremidade de serviço** em **configurações** no painel esquerdo
    - Anote as sub-redes em que **Microsoft. Storage** está habilitado

* Desabilitar pontos de extremidade de serviço:

    - Continuando acima, selecione **sub-redes** em **configurações** no painel esquerdo
    * Clique na sub-rede que contém os pontos de extremidade de serviço
    - Na seção **pontos de extremidade de serviço** , em **Serviços**, desmarque **Microsoft. Storage**

Você pode verificar os logs de armazenamento após alguns minutos, você deve ver um carimbo de data/hora atualizado ou um novo arquivo JSON criado.

*Opção 2: desabilitar os logs de fluxo de NSG*

Se os pontos de extremidade do serviço Microsoft. Storage forem um, será necessário desabilitar os logs de fluxo do NSG.

### <a name="how-do-i-disable-the--firewall-on-my-storage-account"></a>Como fazer desabilitar o firewall na minha conta de armazenamento?

Esse problema é resolvido ao habilitar "todas as redes" para acessar a conta de armazenamento:

* Localize o nome da conta de armazenamento localizando o NSG na [página Visão geral dos logs de fluxo do NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)
* Navegue até a conta de armazenamento digitando o nome da conta de armazenamento na pesquisa global no portal
* Na seção **configurações** , selecione **firewalls e redes virtuais**
* Selecione **todas as redes** e salve-a. Se já estiver selecionada, nenhuma alteração será necessária.  

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Qual é a diferença entre as versões 1 & 2 dos logs de fluxo?
Os logs de fluxo versão 2 apresentam o conceito de *estado de fluxo* & armazena informações sobre bytes e pacotes transmitidos. [Leia mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Próximas etapas
 - Acesse nossa [página de visão geral da documentação](https://docs.microsoft.com/azure/network-watcher/) para ver alguns tutoriais para ajudá-lo a começar a usar o observador de rede.
