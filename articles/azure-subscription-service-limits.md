---
title: Limites e cotas de assinatura do Azure
description: Fornece uma lista de assinaturas comuns do Azure e limites de serviço, cotas e restrições. Isso inclui informações sobre como aumentar os limites junto com os valores máximos.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 12/10/2018
ms.author: byvinyal
ms.openlocfilehash: 00930fd99fd4c0078623aa11f3e2cd5b1c549034
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812660"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Assinatura do Azure e limite de serviços, cotas e restrições
Este documento lista alguns dos limites mais comuns do Microsoft Azure, que também são chamados de cotas. Esse documento não cobre atualmente todos os serviços do Azure. Com o passar do tempo, a lista será expandida e atualizada para uma maior cobertura da plataforma.

Visite [Visão geral de preços do Azure](https://azure.microsoft.com/pricing/) para saber mais sobre preços do Azure. Lá, você pode estimar os custos usando a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) ou visitando a página de detalhes de preços para um serviço (por exemplo, [VMs do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Para obter dicas sobre como ajudar a gerenciar custos, consulte [Evitar custos inesperados com o gerenciamento de custo e cobrança do Azure](billing/billing-getting-started.md).

> [!NOTE]
> Se você deseja aumentar o limite ou a cota acima do **Limite Padrão**, [abra uma solicitação no suporte ao cliente online sem custo](azure-resource-manager/resource-manager-quota-errors.md). Os limites não podem ser aumentados acima do valor **Limite Máximo** mostrado nas tabelas a seguir. Se não houver nenhuma coluna **Limite Máximo**, o recurso não terá limites ajustáveis.
>
> As [assinaturas de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) não estão qualificadas para os aumentos de cota ou limite. Se você tiver uma [assinatura de Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), você poderá atualizar para uma assinatura de [pagamento conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Para obter mais informações, consulte [Atualizar a Versão de Avaliação Gratuita do Azure para Pagamento Conforme o Uso](billing/billing-upgrade-azure-subscription.md) e [Perguntas Frequentes sobre a assinatura de Avaliação Gratuita](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-the-azure-resource-manager"></a>Limites e o Gerenciador de Recursos do Azure
Agora é possível combinar vários recursos do Azure em um único Grupo de Recursos do Azure. Ao usar os Grupos de Recursos, limites que antes eram globais passam a ser gerenciados em nível regional com o Gerenciador de Recursos do Azure. Para saber mais sobre os Grupos de Recursos do Azure, confira [Visão geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Nos limites abaixo, uma nova tabela foi adicionada para refletir quaisquer diferenças nos limites ao usar o Gerenciador de Recursos do Azure. Por exemplo, há uma tabela de **Limites de Assinatura** e uma tabela de **Limites de Assinatura – Azure Resource Manager**. Quando um limite se aplica a ambos os cenários, ele é mostrado apenas na primeira tabela. A menos que indicado de outro modo, os limites são globais em todas as regiões.

> [!NOTE]
> É importante enfatizar que as cotas para recursos nos Grupos de Recursos do Azure são acessíveis de acordo com a região pela assinatura e não de acordo com a assinatura, assim como acontece com as cotas de gerenciamento de serviço. Vamos usar cotas vCPU como um exemplo. Se você precisar solicitar um aumento de cota com suporte para vCPU, você precisa decidir quantos vCPUs deseja usar em quais regiões e, em seguida, fazer uma solicitação específica de cotas de vCPU do Grupo de Recursos do Azure para as quantidades e regiões desejadas. Portanto, se precisar usar 30 vCPUs na Europa Ocidental para executar seu aplicativo lá, você deve solicitar especificamente 30 vCPUs na Europa Ocidental. Mas você não terá um aumento na cota de vCPU em nenhuma outra região – somente a Europa Ocidental terá a cota de 30 vCPUs.
> <!-- -->
> Como resultado, pode ser útil pensar em decidir quais devem ser as cotas do Grupo de Recursos do Azure para a carga de trabalho em determinada região e solicitar essa quantidade em cada região na qual esteja considerando a possibilidade de implantação. Consulte [Solucionando problemas de implantação](resource-manager-common-deployment-errors.md) para obter mais ajuda ao descobrir suas cotas atuais para regiões específicas.
>
>

## <a name="service-specific-limits"></a>Limites específicos de serviço
* [Active Directory](#active-directory-limits)
* [Gerenciamento da API](#api-management-limits)
* [Serviço de Aplicativo](#app-service-limits)
* [Gateway de Aplicativo](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automação](#automation-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Banco de Dados do Azure para MySQL](#azure-database-for-mysql)
* [Banco de Dados do Azure para PostgreSQL](#azure-database-for-postgresql)
* [Grade de Eventos do Azure](#azure-event-grid-limits)
* [Mapas do Azure](#azure-maps-limits)
* [Azure Monitor](#monitor-limits)
* [Azure Policy](#azure-policy-limits)
* Cache Redis do Azure
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [Serviços do BizTalk](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Serviços de Nuvem](#cloud-services-limits)
* [Instâncias de Contêiner](#container-instances-limits)
* [Registro de Contêiner](#container-registry-limits)
* [Serviço do Kubernetes](#kubernetes-service-limits)
* [Fábrica de dados](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Serviço de Migração do Banco de Dados](#database-migration-service-limits)
* [DNS](#dns-limits)
* [Hubs de Evento](#event-hubs-limits)
* [Firewall do Azure](#azure-firewall-limits)
* [Front Door](#azure-front-door-service-limits)
* [Hub IoT](#iot-hub-limits)
* [Serviço de Provisionamento de Dispositivos no Hub IoT](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Identidade gerenciada](#managed-identity-limits)
* [Serviços de Mídia](#media-services-limits)
* [Serviços Móveis](#mobile-services-limits)
* [Autenticação Multifator](#multi-factor-authentication)
* [Rede](#networking-limits)
* [Observador de Rede](#network-watcher-limits)
* [Serviço de hub de notificação](#notification-hub-service-limits)
* [Grupo de recursos](#resource-group-limits)
* [Controle de acesso baseado em função](#role-based-access-control-limits)
* [Agendador](#scheduler-limits)
* [Search](#search-limits)
* [Barramento de Serviço](#service-bus-limits)
* [Serviço SignalR](#signalr-service-limits)
* [Recuperação de Site](#site-recovery-limits)
* [Banco de Dados SQL](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Armazenamento](#storage-limits)
* [Sistema StorSimple](#storsimple-system-limits)
* [Análise de fluxo](#stream-analytics-limits)
* [Assinatura](#subscription-limits)
* [Gerenciador de Tráfego](#traffic-manager-limits)
* [Máquinas virtuais](#virtual-machines-limits)
* [Conjuntos de Escala de Máquina Virtual](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limites de assinatura
#### <a name="subscription-limits---azure-service-management-classic-resources"></a>Limites de Assinatura - Gerenciamento de Serviço do Azure (Recursos Clássicos)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limites de Assinatura – Azure Resource Manager
Os limites a seguir se aplicam ao usar o Gerenciador de Recursos do Azure e os Grupos de Recursos do Azure. Limites que não foram alterados com o Gerenciador de Recursos do Azure não estão listados abaixo. Consulte a tabela anterior para obter esses limites.

Para obter informações sobre limites de leitura e gravação da API do Resource Manager, consulte [solicitações do Gerenciador de Recursos de limitação](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limites do Grupo de Recursos
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limites de Máquinas virtuais
#### <a name="virtual-machine-limits"></a>Limites de Máquina virtual
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de Máquinas Virtuais – Gerenciador de Recursos do Azure
Os limites a seguir se aplicam ao usar o Gerenciador de Recursos do Azure e os Grupos de Recursos do Azure. Limites que não foram alterados com o Gerenciador de Recursos do Azure não estão listados abaixo. Consulte a tabela anterior para obter esses limites.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Limites de conjuntos de escala de máquina virtual
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limites das Instâncias de Contêiner
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limites do Registro de Contêiner
A tabela a seguir fornece detalhes sobre os recursos e os limites das [camadas de serviço](./container-registry/container-registry-skus.md) Básico, Standard e Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="kubernetes-service-limits"></a>Limites do Serviço do Kubernetes
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="networking-limits"></a>Limites de rede
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limites de rede
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Limites do Gateway de Aplicativo

A tabela a seguir se aplica aos SKUs v1, v2, Standard e WAF, salvo indicação em contrário.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limites do Observador de Rede
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Limites do Gerenciador de Tráfego
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limites de DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limites de Firewall do Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limites do Azure Front Door Service
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limites de armazenamento
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Para obter mais detalhes sobre os limites da conta de armazenamento, veja [Metas de desempenho e escalabilidade do Armazenamento do Azure](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Limites do provedor de recursos de armazenamento 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limites de Armazenamento de Blobs do Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limites de Arquivos do Azure
Para obter mais detalhes sobre os limites de Arquivos do Azure, veja [Metas de desempenho e escalabilidade dos Arquivos do Azure](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Limites de Sincronização de Arquivo do Azure
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limites de Armazenamento de Filas do Azure
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limites de Armazenamento de Tabelas do Azure
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limites de disco de máquina virtual
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Consulte [Tamanhos de máquina virtual](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para saber mais detalhes.

#### <a name="managed-virtual-machine-disks"></a>Discos de máquina virtual gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Discos de máquina virtual não gerenciados

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="cloud-services-limits"></a>Limites de Serviços de Nuvem
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Limites do Serviço de Aplicativo
Os seguintes limites do Serviço de Aplicativo incluem limites para Aplicativos Web, Aplicativos Móveis e Aplicativos de API.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limites do Agendador
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limites de lote
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limites dos Serviços BizTalk
A tabela a seguir mostra os limites para os serviços Biztalk do Azure.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Limites do Azure Cosmos DB
O Azure Cosmos DB é um banco de dados de escala global no qual o armazenamento e a produtividade podem ser dimensionados para atender às necessidades de seu aplicativo. Em caso de dúvidas sobre a escala fornecida pelo Azure Cosmos DB, envie um email para askcosmosdb@microsoft.com.

### <a name="azure-database-for-mysql"></a>Banco de Dados do Azure para MySQL
Para limites do Banco de Dados do Azure para MySQL, consulte [Limitações no Banco de Dados do Azure para MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Banco de Dados do Azure para PostgreSQL
Para limites do Banco de Dados do Azure para PostgreSQL, consulte [Limitações no Banco de Dados do Azure para PostgreSQL](postgresql/concepts-limits.md).

### <a name="search-limits"></a>Limites do Search
Os tipos de preço determinam a capacidade e os limites de seu serviço Search. Eles incluem:

* *Gratuito* serviço multilocatário, compartilhado com outros assinantes do Azure, destinado à avaliação e a pequenos projetos de desenvolvimento.
* *Básico* fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor, com até três réplicas para cargas de trabalho de consulta altamente disponíveis.
* *Standard (S1, S2, S3, S3 de alta densidade)* para cargas de trabalho de produção maiores. Há vários níveis dentro da camada Standard para que você possa escolher uma configuração de recursos que corresponda ao seu perfil de carga de trabalho.

**Limites por assinatura**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limites por serviço Search**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Para saber mais sobre limites em um nível mais granular, como o tamanho do documento, consultas por segundo, chaves, solicitações e respostas, confira [Limites de serviço no Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limites de Serviços de Mídia
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limites de CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limites de Serviços Móveis
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>Monitorar limites
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Limites de serviço de hub de notificação
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limites de Hubs de Eventos
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limites de Barramento de Serviço
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limites do Hub IoT
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Limites do Serviço de Provisionamento de Dispositivos no Hub IoT
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Limites de fábrica de dados
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limites do Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limites do Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Limites do Serviço de Migração do Banco de Dados
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Limites do Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limites do Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-event-grid-limits"></a>Limites de Grade de Eventos do Azure
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Limites do Azure Mapas
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Limites do Azure Policy
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Limites do sistema StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Limites do Log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limites do Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="signalr-service-limits"></a>Limites do serviço SignalR
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limites da Recuperação de Site
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limites do Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limites de Gerenciamento de API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Limites do Cache Redis do Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limites do Cofre da Chave
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Autenticação Multifator
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limites de automação
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="managed-identity-limits"></a>Limites de identidade gerenciada
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limites de controle de acesso baseado em função
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limites de banco de dados SQL
Para conferir os limites do Banco de Dados SQL, veja [Limites de recursos de Banco de Dados SQL para bancos de dados individuais](sql-database/sql-database-vcore-resource-limits-single-databases.md) e [Limites de recursos de Banco de Dados SQL para pools elásticos e bancos de dados em pool](sql-database/sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="sql-data-warehouse-limits"></a>Limites do SQL Data Warehouse
Para limites do SQL Data Warehouse, consulte [Limites de recursos do SQL Data Warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Consulte também
[Entendendo os limites e aumentos do Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Tamanhos de máquinas virtuais e serviços de nuvem do Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Tamanhos dos serviços de nuvem](cloud-services/cloud-services-sizes-specs.md)
