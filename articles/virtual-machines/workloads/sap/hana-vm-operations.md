---
title: Configurações e operações de infraestrutura do SAP HANA no Azure | Microsoft Docs
description: Guia de operações para sistemas SAP HANA que são implantados em máquinas virtuais (VMs) do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/20/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 39f250a6c989e5208eda071ed543a1045d65580b
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952660"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurações e operações de infraestrutura do SAP HANA no Azure
Este documento fornece orientação para configurar a infraestrutura do Azure e operar sistemas SAP HANA que são implantados em máquinas virtuais nativas (VMs) do Azure. O documento também inclui informações de configuração de expansão para a SKU de VM M128s do SAP HANA. Este documento não pretende substituir a documentação padrão do SAP, que inclui o seguinte conteúdo:

- [Guia de administração do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de instalação do SAP](https://service.sap.com/instguides)
- [Observações sobre o SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Pré-requisitos
Para usar este guia, você precisa ter um conhecimento básico dos seguintes componentes do Azure:

- [Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Rede do Azure e redes virtuais](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Armazenamento do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Para saber mais sobre o SAP NetWeaver e outros componentes SAP no Azure, consulte a seção [SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) na [documentação do Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerações básicas sobre a instalação
As seções a seguir descrevem considerações de configuração básica para implantar sistemas SAP HANA em máquinas virtuais do Azure.

### <a name="connect-into-azure-virtual-machines"></a>Conecte-se em máquinas virtuais do Azure
Conforme documentado no [guia de planejamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), há dois métodos básicos para se conectar em máquinas virtuais do Azure:

- Conexão pela Internet e por pontos de extremidade públicos em uma VM de atalho ou na VM que executa o SAP HANA.
- Conexão por meio de uma [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou do Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Uma conectividade site a site por meio de VPN ou rota expressa é necessária para cenários de produção. Esse tipo de conexão também é necessário para cenários de não produção que alimentam em cenários de produção em que o software SAP está sendo usado. A imagem a seguir mostra um exemplo de conectividade entre sites:

![Conectividade entre sites](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Escolha os tipos de VM do Azure
Os tipos de VM do Azure que podem ser usados para cenários de produção são listados na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, você pode escolher uma variedade maior de VMs nativas do Azure.

>[!NOTE]
> Para cenários de não produção, use os tipos de VM que são listados na [nota da SAP nº 1928533](https://launchpad.support.sap.com/#/notes/1928533). Para o uso de VMs do Azure para cenários de produção, verifique as VMs certificadas para SAP HANA na [lista de plataformas de IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Implante as VMs no Azure usando:

- O portal do Azure.
- Cmdlets do Azure PowerShell.
- A CLI do Azure.

Você também pode implantar uma plataforma completa de SAP HANA instalada nos serviços de VM do Azure por meio da [plataforma SAP Cloud](https://cal.sap.com/). O processo de instalação está descrito em [Implantar SAP S/4HANA ou BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) ou com a automação liberada [aqui](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Escolha o tipo de armazenamento do Azure
O Azure fornece dois tipos de armazenamento principais adequados a máquinas virtuais do Azure que executam o SAP HANA: Unidades de disco rígido padrão (HDD) e unidades de estado sólido premium (SSD). Para saber mais sobre esses tipos de disco, consulte nosso artigo [Selecionar um tipo de disco](../../windows/disks-types.md)

- Unidades de disco rígido padrão (HDD)
- Unidades de estado sólido premium (SSD)

O Azure oferece dois métodos de implantação para VHDs nos Armazenamentos do Azure Standard e Premium. Se o cenário geral permitir, aproveite as implantações do [Azure Managed disk](https://azure.microsoft.com/services/managed-disks/).

Para obter uma lista de tipos de armazenamento e os respectivos SLAs em IOPS e taxa de transferência de armazenamento, revise a [documentação do Azure para discos gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Configurar o armazenamento para máquinas virtuais do Azure

Até o momento, nunca foi necessário preocupar-se com os subsistemas de E/S e seus recursos. O motivo era que o fornecedor do dispositivo precisava certificar-se de que os requisitos mínimos de armazenamento fossem atendidos para o SAP HANA. Ao criar a infraestrutura do Azure por conta própria, você também deve estar ciente de alguns desses requisitos. E também entender os requisitos de configuração sugeridos nas seções a seguir. Ou para casos em que você esteja configurando as Máquinas Virtuais nas quais deseja executar o SAP HANA. Algumas das características solicitadas resultam na necessidade de:

- Habilite o volume de leitura / gravação em **/hana/log** de um mínimo de 250 MB / s com tamanhos de E / S de 1 MB
- Habilite a atividade de leitura de pelo menos 400MB/seg para **/hana/data** para tamanhos de E / S de 16MB e 64MB
- Habilite a atividade gravação de pelo menos 250 MB/seg para **/hana/data** para tamanhos de E / S de 16MB e 64MB

Uma vez que a baixa latência de armazenamento é crítica aos sistemas DBMS, mesmo que o DBMS, como SAP HANA, mantenha os dados na memória. O caminho crítico no armazenamento é geralmente em torno das gravações de log de transações dos sistemas DBMS. Mas também operações como gravar pontos de salvamento ou carregar dados na memória após a recuperação de falhas poderão ser críticas. Portanto, é obrigatório aproveitar os discos de Premium do Azure para **hana/data** e **hana/log** volumes. Para alcançar a taxa de transferência mínima **hana/log** e **hana/data** conforme desejado pelo SAP, você precisa para criar um RAID 0 utilizando MDADM ou LVM em vários discos de armazenamento Premium do Azure. E usar os volumes RAID como **hana/data** e **hana/log** volumes. Com tamanhos de faixa para RAID 0, o recomendável é usar:

- 64 KB ou 128 KB para **hana/data**
- 32KB para **hana/log**

> [!NOTE]
> Não é necessário configurar nenhum nível de redundância usando volumes RAID, pois o armazenamento Premium e Standard do Azure mantém três imagens de um VHD. O uso de um volume RAID é puramente para configurar volumes que fornecem rendimento de E/S suficiente.

A acumulação de vários VHDs do Azure sob um RAID é cumulativa de um lado de taxa de transferência de IOPS e armazenamento. Portanto, se você colocar um RAID 0 em discos de Armazenamento Premium do Azure de 3 x P30, ele deverá fornecer três vezes a IOPS e três vezes a taxa de transferência de armazenamento de um único disco P30 do Armazenamento Premium do Azure.

As recomendações de cache abaixo pressupõem que as características de E/S para o SAP HANA sejam listadas como:

- Praticamente, não há nenhuma carga de trabalho de leitura nos arquivos de dados do HANA. As exceções são E / Ss de tamanho grande após o reinício da instância do HANA ou quando os dados são carregados no HANA. Outro caso de E/Ss de leitura maiores em arquivos de dados podem ser backups de banco de dados do HANA. Como resultado, em grande parte, o cache de leitura não faz sentido pois, na maioria dos casos, todos os volumes de arquivos de dados precisam ser lidos na íntegra.
- A gravação nos arquivos de dados está ocorrendo em intermitências com base nos pontos de salvamento e na recuperação de pane do HANA. Os pontos de salvamento de gravação são assíncronos e não impedem nenhuma transação de usuário. A gravação de dados durante a recuperação de pane é crítica para o desempenho para que o sistema responda com rapidez novamente. No entanto, a recuperação de pane devem ser situações excepcionais
- Praticamente, não há nenhuma leitura dos arquivos de refazer do HANA. As exceções são E/Ss grandes ao executar backups de log de transações, recuperação de pane ou na fase de reinicialização de uma instância do HANA.  
- A carga principal no arquivo de log de refazer do SAP HANA são gravações. Dependendo da natureza da carga de trabalho, você pode ter E/Ss pequenas de 4 KB ou, em outros casos, tamanhos de E/S iguais ou superiores a 1 MB. A latência de gravação no log de refazer do SAP HANA é crítica para o desempenho.
- Todas as gravações precisam ser persistidas em disco de forma confiável

Como resultado desses padrões de E/S observados pelo SAP HANA, o cache dos diferentes volumes usando o Armazenamento Premium do Azure deve ser definido como:

- **hana/data** -sem cache
- **hana/log** - sem cache - exceção para a série M (veja mais adiante neste documento)
- **hana/shared** - ler o cache


Considere a taxa de transferência de E/S geral ao dimensionar ou escolher uma VM. A taxa de transferência de armazenamento de VM geral está documentada no artigo [Tamanhos da máquina virtual otimizada para memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Modo Agendador de E/S do Linux
O Linux possui vários modos de agendamento de E/S diferentes. A recomendação comum através dos fornecedores do Linux e do SAP é definir o modo do agendador de E/S para volumes de disco longe do modo **cfq** para o modo **noop**. Detalhes são referenciados na [Nota SAP Nº 1984798](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solução de armazenamento com Acelerador de Gravação do Azure para máquinas virtuais da Série M do Azure
O Acelerador de Gravação do Azure é uma funcionalidade que está sendo implementada exclusivamente para VMs da Série M do Azure. Como o nome indica, o objetivo da funcionalidade é melhorar a latência de E/S das gravações no Armazenamento Premium do Azure. Para o SAP HANA, acelerador de gravação deve ser usado em relação a **hana/log** somente no volume. Portanto, as configurações mostradas até o momento precisam ser alteradas. A principal mudança é a divisão entre o **hana/data** e **hana/log** para usar o Acelerador de gravação do Azure em relação a **hana/log** somente no volume. 

> [!IMPORTANT]
> Certificação do SAP HANA para máquinas virtuais da série M do Azure é exclusivamente com o Acelerador de gravação do Azure para o **hana/log** volume. Como resultado, as implantações de SAP HANA de cenário de produção em máquinas virtuais da série M do Azure devem ser configurados com o Acelerador de gravação do Azure para o **hana/log** volume.  

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

As configurações recomendadas são semelhantes a:

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atende a carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **hana/data** e **hana/log**, você precisa aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs que listados aumenta a IOPS e taxa de transferência de e/s dentro dos limites do tipo de máquina virtual do Azure.

O Acelerador de Gravação do Azure funciona somente em conjunto com o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Portanto, pelo menos os discos de armazenamento Premium do Azure que formam o **hana/log** volume precisam ser implantados como discos gerenciados.

Há limites de VHDs de Armazenamento Premium do Azure por VM que podem ter suporte pelo Acelerador de Gravação do Azure. Os limites atuais são:

- 16 VHDs para uma VM M128xx e M416xx
- 8 VHDs para uma VM M64xx e M208xx
- 4 VHDs para uma VM M32xx

Instruções mais detalhadas sobre como habilitar o Acelerador de Gravação do Azure podem ser encontradas no artigo [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Detalhes e restrições para o Acelerador de Gravação do Azure podem ser localizados na mesma documentação.


#### <a name="cost-conscious-azure-storage-configuration"></a>Configuração com consciência de custos do Armazenamento do Microsoft Azure
A tabela a seguir mostra uma configuração de tipos de VM que os clientes também usam para hospedar o SAP HANA em VMs do Azure. Pode haver alguns tipos de VM que poderá não atender todos os critérios de armazenamento mínimo para o SAP HANA ou não tenham suporte oficial com o SAP HANA pelo SAP. Mas até o momento essas VMs parecem funcionar adequadamente para cenários de não produção. 

> [!NOTE]
> Para cenários com suporte do SAP, verifique se um determinado tipo de VM tem suporte para o SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> É uma mudança da antigas recomendações para uma solução de custo consciente, mover de [discos de HDD Standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) para executar melhor e mais confiável [discos SSD Standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | hana/data e hana/log<br /> distribuídos com LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Os discos recomendados para os tipos menores de VM com 3 x P20 ultrapassam os volumes de recomendação de espaço conforme o [Whitepaper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No entanto a opção conforme exibida na tabela foi feita para fornecer a taxa suficiente de transferência de disco para o SAP HANA. Se você precisar de alterações no volume **/hana/backu**p, que foi dimensionado para manter backups que representam o dobro do volume da memória, sinta-se à vontade para fazer ajustes.   
Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atende a carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **hana/data** e **hana/log**, você precisa aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs que listados aumenta a IOPS e taxa de transferência de e/s dentro dos limites do tipo de máquina virtual do Azure. 

> [!NOTE]
> As configurações acima NÃO seriam benefício de [SLA de única VM de máquina virtual do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), pois ele utiliza uma combinação do Armazenamento Premium do Azure e Armazenamento Standard do Azure. No entanto, a seleção foi escolhida para otimizar os custos. É necessário escolher o Armazenamento Premium para todos os discos acima listados como o Armazenamento Standard do Azure (Sxx) para tornar a configuração da VM compatível com o SLA da VM única do Azure.


> [!NOTE]
> As recomendações de configuração de disco declaradas estão visando requisitos mínimos que o SAP expressa para seus provedores de infraestrutura. Em cenários de carga de trabalho e implantações de clientes reais, houve situações em que essas recomendações ainda não forneciam recursos suficientes. Essas poderiam ser situações em que um cliente precisava de um recarregamento mais rápido dos dados após uma reinicialização do HANA ou em que as configurações de backup demandavam mais alta largura de banda para o armazenamento. Outros casos incluíram **/hana/log**, onde 5000 IOPS não foram suficientes para a carga de trabalho específica. Portanto, considere essas recomendações como um ponto de partida e adapte-se com base nos requisitos da carga de trabalho.
>  

#### <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Configuração de armazenamento de SSD Ultra do Azure para SAP HANA
Microsoft está no processo de introduzir um novo tipo de armazenamento do Azure chamado [SSD de Ultra Azure](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/). A grande diferença entre o armazenamento do Azure oferecido até o momento e Ultra SSD é que os recursos de disco não estão vinculados ao tamanho do disco mais. Como um cliente, você pode definir esses recursos para SSD Ultra:

- Tamanho de um disco desde 4 GiB até 65.536 GiB
- Intervalo IOPS de 100 IOPS a 160 mil IOPS (máximo depende também tipos de VM)
- Taxa de transferência do armazenamento de 300 MB/s para 2.000 MB/s

Para obter detalhes leia o artigo [anunciando Ultra SSD – a próxima geração de tecnologia de discos do Azure (visualização)](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

UltraSSD lhe dá a possibilidade de definir um único disco que atende seu intervalo de taxa de transferência do disco, IOPS e tamanho. Em vez de usar gerenciadores de volume lógico como LVM ou MDADM sobre o armazenamento Premium do Azure para construir os volumes que atendam aos requisitos de taxa de transferência IOPS e armazenamento. Você pode executar uma combinação de configuração entre UltraSSD e o armazenamento Premium. Como resultado, você pode restringir o uso do UltraSSD para /hana/data críticos de desempenho e /Hana/log. e abrangem os outros volumes de armazenamento Premium do Azure

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | /hana/data volume | taxa de transferência de e/s de dados Hana | /hana/data IOPS | /hana/log volume | taxa de transferência de e/s de log Hana | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GB | 1.000 MB/s |  1.200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1.000 MB/s | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2.000 MB/s |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2.000 MB/s | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1.000 MB/s | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1.000 MB/s | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2.000 MB/s | 7200 GB | 1500 M Bps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2.000 MB/s | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

Os valores forem considerados apenas para ser um ponto de partida e precisam ser avaliadas em relação às demandas reais. A vantagem com SSD Ultra do Azure é que os valores de IOPS e taxa de transferência podem ser adaptados sem a necessidade de desligar a VM ou interrompendo a carga de trabalho aplicados ao sistema.   

> [!NOTE]
> Até agora, os instantâneos de armazenamento com armazenamento UltraSSD não está disponível. Isso bloqueia o uso de instantâneos da VM com os serviços de Backup do Azure


### <a name="set-up-azure-virtual-networks"></a>Configurar redes virtuais do Azure
Quando você tiver conectividade site a site no Azure por meio de VPN ou ExpressRoute, você deve ter pelo menos uma rede virtual do Azure que está conectada por meio de um Gateway Virtual ao circuito de ExpressRoute ou VPN. Em implantações simples, o Gateway Virtual pode ser implantado em uma sub-rede de rede virtual do Azure (VNet) que hospeda as instâncias do SAP HANA também. Para instalar o SAP HANA, você cria duas sub-redes adicionais dentro da rede virtual do Azure. Uma sub-rede hospeda as VMs para executar as instâncias do SAP HANA. A outra sub-rede executa o Jumpbox ou as VMs de gerenciamento para hospedar o SAP HANA Studio, outros softwares de gerenciamento ou o software do aplicativo.

> [!IMPORTANT]
> Fora de funcionalidade, mas mais importante por motivos de desempenho, não há suporte para configurar [Dispositivos virtuais de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo SAP e a camada DBMS de um SAP NetWeaver, Hybris ou S / 4HANA baseado no sistema SAP. É necessário a comunicação entre a camada de aplicativo SAP e a camada DBMS seja direta. A restrição não inclui [Regras de NSG e ASG do Azure](https://docs.microsoft.com/azure/virtual-network/security-overview), desde que as regras de NSG e ASG permitam uma comunicação direta. Outros cenários em que não há suporte para NVAs estão em caminhos de comunicação entre VMs do Azure que representam nós de cluster do Linux Pacemaker e dispositivos SBD, conforme descrito em [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Ou em caminhos de comunicação entre VMs do Azure e SOFS do Windows Server configurados conforme descrito em [Cluster de uma instância do SAP ASCS / SCS em um cluster de failover do Windows usando um compartilhamento de arquivos no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Os NVAs em caminhos de comunicação podem facilmente duplicar a latência de rede entre dois parceiros de comunicação, podendo restringir o rendimento em caminhos críticos entre a camada de aplicação SAP e a camada DBMS. Em alguns cenários observados com os clientes, os NVAs podem causar falhas nos clusters do Pacemaker Linux nos casos em que as comunicações entre os nós do cluster do Linux Pacemaker precisam se comunicar com o dispositivo SBD por meio de um NVA.  
> 

> [!IMPORTANT]
> Outro design que **NÃO** tem suporte é a segregação da camada de aplicativo SAP e da camada DBMS em diferentes redes virtuais do Azure que não estejam [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre si. É recomendável separar a camada de aplicativo SAP e a camada DBMS usando sub-redes em uma rede virtual do Azure, em vez de usar redes virtuais diferentes do Azure. Se você decidir não seguir a recomendação e, em vez disso, segregar as duas camadas em uma rede virtual diferente, as duas redes virtuais deverão ser [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Esteja ciente de que o tráfego entre duas redes virtuais [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) do Azure está sujeito a custos de transferência. Com o enorme volume de dados em muitos Terabytes trocados entre a camada de aplicativo SAP e a camada DBMS, custos substanciais poderão ser acumulados se a camada de aplicativo SAP e a camada DBMS forem segregadas entre duas redes virtuais do Azure. 

Quando você instala as VMs para executar o SAP HANA, as VMs necessitam de:

- Duas NICs virtuais instaladas: uma NIC para conectar-se à sub-rede de gerenciamento, e uma NIC para conectar-se da rede local ou de outras redes para a instância do SAP HANA na VM do Azure.
- Endereços IP privados estáticos que são implantados para as duas NICS virtuais.

> [!NOTE]
> Você deve atribuir endereços IP estáticos por meio do Azure para vNICs individuais. Você não deve atribuir endereços IP estáticos dentro do SO convidado para um vNIC. Alguns serviços do Azure, como o serviço de Backup do Azure, dependem do fato de que pelo menos o vNIC primário esteja definido como DHCP, e não para endereços IP estáticos. Veja também o documento [Solucionar problemas de backup da máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Se você precisar atribuir vários endereços IP estáticos a uma VM, precisará atribuir vários vNICs a uma VM.
>
>

No entanto, para implantações que são permanentes, você precisará criar uma arquitetura de rede do datacenter virtual no Azure. Essa arquitetura recomenda a separação de Gateway de rede virtual do Azure que se conecta a locais em uma VNet do Azure separado. Essa rede de virtual separado deve hospedar todo o tráfego que sai para o local ou na internet. Essa abordagem permite que você implantar o software para auditoria e registro em log o tráfego que entra o datacenter virtual no Azure neste hub separado de rede virtual. Portanto, você tem uma VNet que hospeda todo o software e as configurações relacionadas ao tráfego de entrada e saída para sua implantação do Azure.

Os artigos [Datacenter Virtual do Azure: Uma perspectiva de rede](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) e [Datacenter Virtual do Azure e o Plano de Controle Empresarial](https://docs.microsoft.com/azure/architecture/vdc/) fornecem mais informações sobre a abordagem do datacenter virtual e o design relacionado da VNet do Azure.


>[!NOTE]
>O tráfego que flui entre uma VNet do hub e spoke VNet usando [emparelhamento de rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) é o assunto adicionais [custos](https://azure.microsoft.com/pricing/details/virtual-network/). Com base nesses custos, você talvez precise considerar o risco de comprometimento entre executar um projeto de rede de hub e spoke estrito e executar várias [Gateways de ExpressRoute do Azure](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) que você se conectar a 'spokes' para ignorar o emparelhamento de rede virtual. No entanto, os Gateways de ExpressRoute do Azure introduz adicionais [custos](https://azure.microsoft.com/pricing/details/vpn-gateway/) também. Você também pode encontrar custos adicionais para softwares de terceiros usados para registro, auditoria e monitoramento de tráfego de rede. Dependendo dos custos para troca de dados por meio do emparelhamento de rede virtual no um lado e os custos criados por licenças de software adicionais e Gateways de ExpressRoute do Azure adicionais, você pode decidir para microssegmentação dentro de uma rede virtual usando sub-redes como unidade de isolamento em vez de redes virtuais.


Para obter uma visão geral dos diferentes métodos para atribuir endereços IP, consulte [Tipos de endereços IP e métodos de alocação no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Para VMs em execução no SAP HANA, é necessário trabalhar com endereços IP estáticos atribuídos. A razão é que alguns atributos de configuração para o HANA referenciam endereços IP.

Os [ Grupos de segurança de rede (NSGs) do Azure ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são usados para direcionar tráfego roteado para a instância do SAP HANA ou para a caixa de depósito. Os NSGs e, por fim, os [grupos de segurança de aplicativos](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) estão associados à sub-rede SAP HANA e à sub-rede de gerenciamento.

A imagem a seguir mostra uma visão geral de um esquema aproximado de implantação para o SAP HANA de uma arquitetura de rede virtual hub e spoke a seguir:

![Esquema de implantação aproximado para o SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Para implantar o SAP HANA no Azure sem uma conexão site a site, você ainda deseja proteger a instância do SAP HANA da internet pública e ocultá-lo por trás de um proxy de encaminhamento. Neste cenário básico, a implantação se baseia em serviços DNS internos do Azure para resolver nomes de host. Em uma implantação mais complexa, em que endereços IP públicos são usados, os serviços DNS internos do Azure são especialmente importantes. Use os NSGs do Azure e [NVAs Azure](https://azure.microsoft.com/solutions/network-appliances/) para controlar, monitorar o roteamento da internet em sua arquitetura de rede virtual do Azure no Azure. A imagem a seguir mostra um esquema aproximado para implantar o SAP HANA sem uma conexão site a site em uma arquitetura VNet hub e spoke:
  
![Esquema de implantação aproximado para o SAP HANA sem conexão site a site](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Outra descrição sobre como usar os NVAs do Azure para controlar e monitorar o acesso da Internet sem a arquitetura VNet hub e spoke pode ser encontrada no artigo [Implantar dispositivos virtuais de rede altamente disponíveis](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Configurando a infraestrutura do Azure para escalonamento do SAP HANA
A Microsoft possui uma SKU de VMs da série M certificada para uma configuração de scale-out do SAP HANA. O tipo M128s da VM foi certificado para uma escala de até 16 nós. Para alterações nas certificações de escalonamento SAP HANA em VMs do Azure, verifique a [Lista de plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

As versões de SO mínimas para a implantação de configurações de escala horizontal em VMs do Azure são:

- SUSE Linux 12 SP3
- Red hat Linux 7.4

Da certificação de scale-out de 16 nós

- Um nó é o nó mestre
- Um máximo de 15 nós são nós de trabalho

>[!NOTE]
>Em implantações de expansão de VM do Azure não há nenhuma possibilidade de usar um nó em espera
>

O motivo para não ser capaz de configurar um nó em espera são duplos:

- O Azure neste momento não possui nenhum serviço NFS nativo. Como resultado, os compartilhamentos do NFS precisam ser configurados com a ajuda de funcionalidades de terceiros.
- Nenhuma das configurações de NFS de terceiros é capaz de atender aos critérios de latência de armazenamento do SAP HANA com suas soluções implantadas no Azure.

Como resultado, **hana/data** e **hana/log** volumes não podem ser compartilhados. Não compartilhar esses volumes dos nós únicos, impede o uso de um nó de espera do SAP HANA em uma configuração de scale-out.

Como resultado, o design básico de um único nó em uma configuração de scale-out será semelhante a:

![Princípios básicos de expansão de um único nó](media/hana-vm-operations/scale-out-basics.PNG)

A configuração básica de um nó de VM para scale-out do SAP HANA é semelhante a:

- Para **/hana/shared**, você cria um cluster NFS altamente disponível com base no SUSE Linux 12 SP3. A hosts de cluster a **hana/shared** NFS compartilhamento (s) de sua configuração de expansão e SAP NetWeaver ou serviços centrais do BW/4HANA. Documentação para criar essa configuração está disponível no artigo [alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Todos os outros volumes de disco **NÃO** são compartilhados entre os diferentes nós e **NÃO** são com base no NFS. As configurações de instalação e as etapas para instalações HANA escaláveis com **/hana/data** e **/hana/log** não compartilhados são fornecidas mais abaixo neste documento.

>[!NOTE]
>O cluster NFS altamente disponível, conforme exibido nos gráficos até o momento, é suportado apenas pelo SUSE Linux. Uma solução NFS altamente disponível baseada no Red Hat vai ser aconselhável mais tarde.

O dimensionamento dos volumes para os nós é o mesmo que para scale-up, exceto **/hana/shared**. A SKU de VM M128s, os tipos e tamanhos sugeridos de aparência:

| SKU da VM | RAM | Máx. VM E/S<br /> Produtividade | /hana/data | /hana/log | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atende a carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para **hana/data** e **hana/log**, você precisa aumentar o número de VHDs de armazenamento Premium do Azure. Dimensionar um volume com mais VHDs que listados aumenta a IOPS e taxa de transferência de e/s dentro dos limites do tipo de máquina virtual do Azure. Também se aplicam o Acelerador de gravação do Azure para os discos que formam o **hana/log** volume.
 
No documento [Requisitos de armazenamento SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), uma fórmula é nomeada para definir o tamanho do volume **/hana/shared** para expansão, como o tamanho da memória de um único nó de trabalho por quatro nós de trabalhado.

Supondo que você tirar o SAP HANA expansão certificada M128s VM do Azure com mais ou menos memória de 2 TB, as recomendações do SAP podem ser resumidas como:

- Um nó mestre e até o nó de trabalho de quatro, o **hana/shared** volume precisariam ser 2 TB de tamanho. 
- Um nó mestre e cinco e oito nós de trabalho, o tamanho de **hana/shared** deve ser de 4 TB. 
- Um nó mestre e nós de trabalho de 9 a 12, um tamanho de 6 TB para **hana/shared** seria necessária. 
- Um nó principal e usando entre 12 e 15 nós de trabalho, você precisa fornecer um volume **/hana/shared** com 8 TB de tamanho.

O outro design importante que é exibido nos gráficos da configuração de nó único para uma VM SAP HANA de scale-out é a VNet, ou melhor, a configuração de sub-rede. A SAP recomenda uma separação do tráfego de cliente / aplicativo das comunicações entre os nós do HANA. Como mostrado nos gráficos, essa meta é obtida com dois vNICs diferentes conectados à VM. Ambos os vNICs estão em sub-redes diferentes, possuem dois endereços IP diferentes. Você controla o fluxo de tráfego com regras de roteamento usando NSGs ou rotas definidas pelo usuário.

Particularmente no Azure, não há meios e métodos para impor a qualidade do serviço e cotas em vNICs específicos. Como resultado, a separação entre o cliente/aplicativo e a comunicação entre nós não abre nenhuma oportunidade de priorizar um fluxo de tráfego sobre o outro. Em vez disso, a separação continua a ser uma medida de segurança na proteção das comunicações intra-nó das configurações de scale-out.  

>[!IMPORTANT]
>A SAP recomenda a separação do tráfego de rede para o lado do cliente / aplicativo e o tráfego entre nós, conforme descrito neste documento. Portanto, é altamente recomendável colocar uma arquitetura no lugar, como mostrado nos últimos gráficos.
>

Do ponto de vista da rede, a arquitetura de rede mínima necessária seria semelhante a:

![Princípios básicos de expansão de um único nó](media/hana-vm-operations/scale-out-networking-overview.PNG)

Os limites suportados até agora são 15 adicionais para o nó mestre.

Do ponto de vista do armazenamento, a arquitetura de armazenamento seria semelhante a:


![Princípios básicos de expansão de um único nó](media/hana-vm-operations/scale-out-storage-overview.PNG)

O volume **/hana/shared** está localizado na configuração de compartilhamento NFS altamente disponível. 'Local', enquanto todas as outras unidades é montados para as VMs individuais. 

### <a name="highly-available-nfs-share"></a>Compartilhamento de NFS altamente disponível
O cluster NFS altamente disponível até o momento está trabalhando com o SUSE Linux somente. O documento [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) descreve como configurá-lo. Se você não compartilhe cluster NFS com qualquer outra configuração fora da VNet do azure que executa o SAP HANA em instâncias do HANA, instale-o na mesma rede virtual. Instale-o em sua própria sub-rede e certifique-se de que não todo o tráfego arbitrário possa acessar a sub-rede. Em vez disso, você deseja limitar o tráfego para essa sub-rede para os endereços IP da VM que executam o tráfego **hana/shared** volume.

Relacionado à vNIC de uma VM de expansão do HANA deve rotear a **hana/shared** tráfego, as recomendações são:

- Desde o tráfego para **hana/shared** é moderado, encaminhá-lo por meio de vNIC atribuído à rede do cliente em uma configuração mínima
- Por fim, para o tráfego **hana/shared**, implante uma terceira sub-rede na VNet que você implanta a configuração de expansão do SAP HANA e a vNIC de atribuir um terceiro que é hospedado na sub-rede. Use o terceiro vNIC e o endereço IP associado para o tráfego para o compartilhamento NFS. Em seguida, você pode aplicar regras de roteamento e de acesso separados.

>[!IMPORTANT]
>O tráfego de rede entre as VMs que possuem o SAP HANA de maneira escalonada implantado e o NFS altamente disponível pode, sob nenhuma circunstância, ser roteado por meio de um [NVA](https://azure.microsoft.com/solutions/network-appliances/) ou dispositivos virtuais semelhantes. Enquanto os NSGs do Azure são sem esses dispositivos. Verifique suas regras de roteamento para garantir que os NVAs ou dispositivos virtuais semelhantes sejam desviados quando acessar o compartilhamento NFS altamente disponível das VMs que executam o SAP HANA.
> 

Se você quiser compartilhar o cluster NFS altamente disponível entre as configurações do SAP HANA, mova todas as configurações do HANA para a mesma rede virtual. 
 

### <a name="installing-sap-hana-scale-out-n-azure"></a>Instalando a expansão do SAP HANA n Azure
Instalando uma configuração SAP scale-out, você precisa executar etapas aproximadas de:

- Implantando nova ou adaptando a nova infraestrutura do Azure VNet
- Implantar as novas VMs usando volumes do Armazenamento Premium Gerenciado do Azure
- Implantando um novo ou adaptando um cluster NFS altamente disponível existente
- Adapte o roteamento de rede para garantir que, por exemplo, a comunicação entre nós entre as VMs não seja roteada por meio de um [NVA](https://azure.microsoft.com/solutions/network-appliances/). Mesmo é verdadeiro para o tráfego entre as VMs e o cluster NFS altamente disponível.
- Instale o nó mestre SAP HANA.
- Adaptar parâmetros de configuração do nó mestre do SAP HANA
- Continue com a instalação dos nós de trabalho do SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Instalação do SAP HANA na configuração de scale-out
À medida que sua infraestrutura de VM do Azure é implantada e todas as outras preparações são feitas, é necessário instalar as configurações de escalabilidade de saída do SAP HANA nas seguintes etapas:

- Instale o nó mestre do SAP HANA de acordo com a documentação da SAP
- **Após a instalação, você precisa alterar o arquivo global.ini e adicionar o parâmetro 'basepath_shared = no' ao global.ini**. Esse parâmetro permite que o SAP HANA executar na expansão sem 'compartilhado' **hana/data** e **hana/log** volumes entre os nós. Detalhes são documentados em [Nota SAP # 2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Depois de alterar o parâmetro ini, reinicie a instância do SAP HANA
- Adicione nós de trabalho adicionais. Consulte também <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Especifique a rede interna para a comunicação entre nós durante a instalação do SAP HANA ou posteriormente, usando, por exemplo, o hdblcm local. Para obter mais documentação, consulte também [SAP Observação #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Seguindo essa rotina de instalação, a configuração de scale-out que você instalou usará discos não compartilhados para executar **/hana/data** e **/hana/log**. Enquanto o **hana/shared** volume vai ser colocado em altamente disponíveis de compartilhamento NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA Dynamic Tiering 2.0 para as máquinas virtuais do Azure

Além das certificações de SAP HANA em VMs da série M do Azure, o SAP HANA Dynamic Tiering 2.0 também tem suporte no Microsoft Azure (consulte a documentação do SAP HANA Dynamic Tiering abaixo). Embora não haja nenhuma diferença na instalação do produto ou operação, por exemplo, por meio da ferramenta Cockpit do SAP HANA dentro de uma máquina de Virtual do Azure, há alguns itens importantes, que são obrigatórios para suporte oficial no Azure. Estes pontos-chave são descritos abaixo. Em todo o artigo, a abreviação de "DT 2.0" vai ser usado em vez do nome completo dinâmico 2.0 disposição em camadas.

SAP HANA Dynamic Tiering 2.0 não tem suporte pelo SAP BW ou S4HANA. Casos de uso principal agora são aplicativos nativos do HANA.


### <a name="overview"></a>Visão geral

A figura abaixo fornece uma visão geral sobre o suporte de DT 2.0 no Microsoft Azure. Há um conjunto de requisitos obrigatórios que devem ser seguidos para cumprir a certificação oficial:

- DT 2.0 deve ser instalado em uma VM do Azure dedicada. Pode não ser executada na mesma VM em que o SAP HANA é executado
- O SAP HANA e DT 2.0 VMs devem ser implantados na mesma rede virtual do Azure
- O SAP HANA e DT 2.0 VMs devem ser implantados com a rede acelerada do Azure habilitada
- Tipo de armazenamento para VMs DT 2.0 deve ser o Armazenamento Premium
- Vários discos do Azure devem ser anexados à VM DT 2.0
- É necessário criar um volume distribuído/raid de software (via lvm ou mdadm) usando a distribuição nos discos do Azure

Mais detalhes serão explicados nas seções a seguir.

![Visão geral da arquitetura SAP HANA DT 2.0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>VM do Azure dedicada para SAP HANA DT 2.0

No Azure IaaS, DT 2.0 tem suporte apenas em uma VM dedicada. Não é permitido executar DT 2.0 na mesma VM do Azure onde a instância do HANA está em execução. Inicialmente, dois tipos de VMs podem ser usados para executar o SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Consulte a descrição de tipo VM [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Dada a ideia básica do 2.0 DT, que aborda o descarregamento de dados "passivos" para economizar custos, faz sentido usar tamanhos de VM correspondentes. Embora não haja nenhuma regra rígida sobre as combinações possíveis. Isso depende da carga de trabalho de cliente específico.

As configurações recomendadas seriam:

| Tipo de VM do SAP HANA | Tipo de VM do DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Todas as combinações de VMs da série M certificadas para SAP HANA com VMs de 2.0 com suporte DT (M64-32ms e E32sv3) são possíveis.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Rede do Azure e SAP HANA DT 2.0

Instalar DT 2.0 em uma VM dedicada requer a taxa de transferência de rede entre a VM de 2.0 DT e VM do SAP HANA de 10 Gb no mínimo. Portanto, é obrigatório colocar todas as VMs na mesma rede virtual do Azure e habilitar a rede acelerada do Azure.

Consulte informações adicionais sobre a rede acelerada do Azure [aqui](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Armazenamento VM para SAP HANA DT 2.0

Acordo com diretrizes de práticas recomendadas de DT 2.0, a taxa de transferência de e/s de disco deve ser mínimo 50 MB/s por núcleo físico. Observando a especificação para os dois tipos de VM do Azure, que têm suporte para 2.0 DT, como o limite de taxa de transferência de e/s para obter a aparência VM de disco máximo:

- E32sv3    :   768 MB/s (sem cache), o que significa uma taxa de 48 MB/s por núcleo físico
- M64-32ms  :  1000 MB/s (sem cache), o que significa uma razão de 62,5 MB/s por núcleo físico

É necessário anexar vários discos do Azure para a VM de 2.0 DT e criar um raid de software (distribuição) no nível do sistema operacional para atingir o limite máximo de taxa de transferência de disco por VM. Um único disco do Azure não pode fornecer a taxa de transferência para alcançar o limite máximo de VM em relação a isso. Armazenamento Premium do Azure é obrigatório para executar o DT 2.0. 

- Detalhes sobre os tipos de disco do Azure disponíveis podem ser encontrados [aqui](../../windows/disks-types.md)
- Encontre detalhes sobre como criar um raid de software via mdadm [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Detalhes sobre como configurar o LVM para criar um volume distribuído para taxa de transferência máxima que pode ser encontrada [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

Dependendo dos requisitos de tamanho, há opções diferentes para alcançar a taxa de transferência máxima de uma VM. Aqui estão as configurações de disco de volume de dados possíveis para cada tipo de VM de 2.0 DT atingir o limite de taxa de transferência da VM. A VM E32sv3 deve ser considerada como um nível de entrada para cargas de trabalho menores. Caso isso resulte em não ser rápido o suficiente, pode ser necessário redimensionar a VM para M64-32ms.
Assim como a VM M64 32ms tem a quantidade de memória, a carga de e/s pode não alcançar o limite, especialmente para cargas de trabalho com uso intensivo de leitura. Portanto, menos discos no conjunto de distribuição podem ser suficientes, dependendo da carga de trabalho específica do cliente. Mas, por segurança, as configurações de disco abaixo foram escolhidas para garantir a máxima taxa de transferência:


| SKU da VM | Configuração de disco 1 | Configuração de disco 2 | Configuração de disco 3 | Configuração de disco 4 | Configuração de disco 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


Especialmente no caso da carga de trabalho intensas de leitura ele poderia melhorar o desempenho de e/s para ativar o cache de host do Azure "somente leitura" conforme recomendado para os volumes de dados do software de banco de dados. Enquanto a transação de log de cache de disco do host do Azure deve ser "nenhum". 

Sobre o tamanho do volume de log de um ponto de partida recomendado está uma heurística de 15% do tamanho dos dados. A criação do volume de log pode ser feita usando os tipos de disco do Azure diferente dependendo dos requisitos de custo e a taxa de transferência. Para o volume do log, é necessária uma alta taxa de transferência de E/S.  Se usar o tipo de VM M64-32ms, é altamente recomendável habilitar o [Acelerador de Gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). O Acelerador de Gravação do Azure fornece latência de gravação de disco ideal para o log de transações (disponível apenas para a série M). Há alguns itens a considerar, embora, como o número máximo de discos por tipo de VM. Detalhes sobre o Acelerador de Gravação podem ser encontrados [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


A seguir, são apresentados alguns exemplos sobre dimensionamento de volume do log:

| tipo de disco e tamanho do volume de dados | configuração 1 de tipo de disco e volume de log | configuração 2 de tipo de disco e volume de log |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Como para escalabilidade horizontal de SAP HANA, o diretório /hana/shared deve ser compartilhado entre a VM do SAP HANA e VM DT 2.0. A mesma arquitetura quanto ao uso de expansão do SAP HANA dedicado VMs que atuam como um servidor NFS altamente disponível é recomendado. Para fornecer um volume compartilhado de backup, o design idêntico pode ser usado. Mas, cabe ao cliente se HA será necessária ou se é suficiente usar apenas uma VM dedicada com capacidade de armazenamento suficiente para atuar como um servidor de backup.



### <a name="links-to-dt-20-documentation"></a>Links para documentação DT 2.0 

- [Instalação do SAP HANA Dynamic Tiering e atualizar guia](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Recursos e tutoriais de camadas do SAP HANA Dynamic Tiering](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [PoC de SAP HANA Dynamic Tiering](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Aprimoramentos do SAP HANA 2.0 SPS 02](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operações de implantação do SAP HANA em VMs do Azure
As seções a seguir descrevem algumas das operações relacionadas à implantação de sistemas SAP HANA  em VMs do Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Operações de backup e restauração em VMs do Azure
Os documentos a seguir descrevem como fazer backup e restaurar a sua implantação do SAP HANA:

- [Visão geral de backup do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup em nível de arquivo do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Parâmetro de comparação de instantâneos de armazenamento do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Inicie e reinicie as VMs que contêm o SAP HANA
Uma característica importante da nuvem pública do Azure é que você será cobrado somente pelos seus minutos de computação. Por exemplo, quando você desligar uma VM que executa o SAP HANA, você será cobrado somente pelos custos de armazenamento durante aquele período. Outro recurso fica disponível quando você especificar endereços IP estáticos para as VMs na sua implantação inicial. Quando você reiniciar uma VM que contém o SAP HANA, a VM é reiniciada com seus endereços IP anteriores. 


### <a name="use-saprouter-for-sap-remote-support"></a>Use o SAProuter para suporte remoto SAP
Se você tem uma conexão site a site entre suas localizações locais e o Azure, e estiver executando componentes SAP, você provavelmente já está executando o SAProuter. Nesse caso, complete os seguintes itens para suporte remoto:

- Manter o endereço IP estático e privado da VM que hospeda o SAP HANA na configuração do SAProuter.
- Configure o NSG da sub-rede que hospeda a VM do HANA para permitir o tráfego pela porta TCP/IP 3299.

Se estiver conectando ao Azure através da Internet e não tiver um roteador SAP para a VM com SAP HANA, será necessário instalar o componente. Instale o SAProuter em uma VM separada na sub-rede de gerenciamento. A imagem a seguir mostra um esquema aproximado para a implantação do SAP HANA sem uma conexão site a site:

![Esquema de implantação aproximado para o SAP HANA sem conexão site a site e SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Certifique-se de instalar o SAProuter em uma VM separada e não em sua VM Jumpbox. A VM separada precisa de um endereço IP estático. Para conectar o SAProuter ao SAProuter que é hospedado pela SAP, entre em contato com o SAP para um endereço IP. (O SAProuter que é hospedado pela SAP é a contraparte da instância do SAProuter que você instala em sua VM.) Use o endereço IP da SAP para configurar sua instância do SAProuter. Nas definições de configuração, a única porta necessária é a porta TCP 3299.

Para saber mais sobre como configurar e manter conexões de suporte remoto por meio do SAPRouter, confira a [ documentação do SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidade com o SAP HANA em VMs nativas do Azure
Se estiver executando o SUSE Linux Enterprise Server para Aplicativos SAP 12 SP1 ou posteriores, você poderá estabelecer um cluster Pacemaker com dispositivos STONITH. Você pode usar os dispositivos para definir a configuração do SAP HANA que usa replicação síncrona com a Replicação de sistema do HANA e failover automático. Para obter mais informações sobre o procedimento de configuração, consulte [Guia de Alta Disponibilidade do SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
