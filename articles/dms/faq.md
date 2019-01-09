---
title: Perguntas Frequentes sobre como usar o Serviço de Migração de Banco de Dados do Azure | Microsoft Docs
description: Saiba mais sobre as perguntas frequentes sobre o uso do Serviço de Migração de Banco de Dados do Azure para executar migrações de banco de dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/19/2018
ms.openlocfilehash: b8001729c85c8447ab22bafffe24a32524a0662a
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714688"
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Perguntas Frequentes sobre como usar o Serviço de Migração de Banco de Dados do Azure
Este artigo lista as perguntas frequentes sobre como usar o Serviço de Migração de Banco de Dados do Azure junto com as respostas relacionadas.

### <a name="q-what-is-azure-database-migration-service"></a>P. O que é o Serviço de Migração de Banco de Dados do Azure?
O Serviço de Migração de Banco de Dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo. Atualmente, o serviço está Geralmente Disponível, com esforços de desenvolvimento contínuo focados em:
- Confiabilidade e desempenho.
- Adição iterativa de pares de fonte-destino.
- Investimento contínuo em migrações sem conflitos.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>P. Para quais pares de origem e destino o Serviço de Migração de Banco de Dados do Azure oferece suporte?
O serviço atualmente dá suporte para uma variedade de cenários de migração. Para obter uma listagem completa do status de cada cenário de migração disponível, consulte o artigo [Status dos cenários de migração com suporte pelo Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/resource-scenario-status). Outros cenários de migração estão em versão prévia limitada e exigem o envio de uma nominação por meio do site da Versão Prévia do DMS. Para obter uma listagem completa dos cenários de visualização limitada e para inscrever-se para participar de uma dessas ofertas, consulte o [site da Versão Prévia do DMS](https://aka.ms/dms-preview/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>P. Como o Serviço de Migração de Banco de Dados do Azure se compara com outras ferramentas de migração de banco de dados da Microsoft, como o Assistente de Migração de Banco de Dados (DMA) ou o Assistente de Migração do SQL Server (SSMA)?
O Serviço de Migração de Banco de Dados do Azure é o método preferencial para a migração de banco de dados para o Microsoft Azure em grande escala. Para obter mais detalhes sobre como o Serviço de Migração de Banco de Dados do Azure se compara a outras ferramentas de migração de banco de dados da Microsoft e para obter recomendações sobre como usar o serviço para vários cenários, consulte a postagem de blog [Diferenciando serviços e ferramentas de migração de banco de dados da Microsoft](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>P. Como o Serviço de Migração de Banco de Dados do Azure se compara à oferta de Migrações para Azure?
O serviço de Migrações para Azure ajuda na migração de máquinas virtuais locais para o Azure IaaS. O serviço avalia a adequação da migração e o dimensionamento com base no desempenho, e fornece estimativas de custo para a execução das máquinas virtuais locais no Azure. As Migrações para Azure são úteis para migrações de lift-and-shift de cargas de trabalho baseadas em VM local para VMs de IaaS do Azure. No entanto, ao contrário do Serviço de Migração de Banco de Dados do Azure, as Migrações para Azure não são uma oferta de serviço de migração de banco de dados especializada para plataformas de banco de dados relacional de PaaS do Azure, como o Banco de Dados SQL do Azure ou SQL do Azure ou Instância Gerenciada do Banco de Dados SQL.

### <a name="q-what-versions-of-sql-server-does-the-azure-database-migration-service-support-as-a-source"></a>P. Quais versões do SQL Server são compatíveis com o Serviço de Migração de Banco de Dados do Azure como fonte?
Ao migrar do SQL Server, o Serviço de Migração de Banco de Dados do Azure dá suporte ao SQL Server 2005 por meio do SQL Server 2017.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>P. O que é um resumo das etapas necessárias para usar o Serviço de Migração de Banco de Dados do Azure para executar uma migração de banco de dados?
Durante uma migração de banco de dados típica e simples, você:
1.  Cria um banco de dados de destino.
2.  Migra o esquema do banco de dados usando o [Assistente de Migração de Dados](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
4.  Cria um projeto de migração especificando os bancos de dados de origem, bancos de dados de destino e tabelas para migração.
5.  Inicia a carga completa.
6.  Escolhe a validação subsequente.
7.  Execute uma mudança manual do seu ambiente de produção para o novo banco de dados baseado em nuvem. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>P. Quais são os pré-requisitos para usar o Serviço de Migração de Banco de Dados do Azure?
Existem vários pré-requisitos necessários para garantir que o Serviço de Migração de Banco de Dados do Azure funcione sem problemas ao executar migrações de bancos de dados. Alguns dos pré-requisitos se aplicam em todos os cenários (pares de origem e destino) com suporte do serviço, enquanto outros pré-requisitos são exclusivos para um cenário específico.
Os pré-requisitos do Serviço de Migração de Banco de Dados do Azure que são comuns a todos os cenários de migração compatíveis incluem a necessidade de:
- Criar um VNET para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Verificar se as regras do Grupo de Segurança de Rede da VNET (Rede Virtual) do Azure não bloqueiam as portas de comunicação 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego do NSG da Rede Virtual do Azure, consulte o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.
 
Para obter uma lista de todos os pré-requisitos necessários para competir com cenários de migração específicos usando o Serviço de Migração de Banco de Dados do Azure, consulte os tutoriais relacionados na [documentação](https://docs.microsoft.com/azure/dms/dms-overview) do Serviço de Migração de Banco de Dados do Azure em docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>P. Como localizar o endereço IP para o Serviço de Migração de Banco de Dados do Azure para que seja possível criar uma lista de permissões para as regras de firewall usadas para acessar meu banco de dados de origem para migração?
Talvez seja necessário adicionar regras de firewall permitindo que o Serviço de Migração de Banco de Dados do Azure acesse o banco de dados de origem para migração. O endereço IP para o serviço é dinâmico, mas se você estiver usando o ExpressRoute esse endereço em particular será atribuído pela sua rede corporativa. A maneira mais fácil de identificar o endereço IP apropriado é pesquisar no mesmo grupo de recursos fornecido para o recurso de Serviço de Migração de Banco de Dados do Azure para localizar a Interface de Rede associada. Normalmente, o nome do recurso de Adaptador de Rede começa com o prefixo de NIC e é seguido por um caractere único e sequência numérica, por exemplo NIC-jj6tnztnmarpsskr82rbndyp. Ao selecionar esse recurso de interface de rede, você pode ver o endereço IP que deve ser incluído na lista de permissões na página do portal do Azure para visão de geral de recursos.

Talvez você precise incluir a origem da porta que o SQL Server está escutando na lista de permissões. Por padrão, é a porta 1433, mas o SQL Server de origem também pode estar configurado para escutar outras portas. Nesse caso, você também precisa incluir as portas na lista de permissões. Você pode determinar a porta que o SQL Server está escutando usando uma consulta de modo de exibição de Gerenciamento Dinâmico:

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Você também pode determinar a porta que o SQL Server está escutando consultando o log de erros do SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>P. Há recomendações para otimizar o desempenho do Serviço de Migração de Banco de Dados do Azure?
Você pode fazer algumas coisas para acelerar a sua migração de banco de dados usando o serviço:
- Use os vários tipos de preços de uso geral da CPU ao criar sua instância de serviço para permitir que o serviço aproveite as várias vCPUs para paralelização e transferência de dados mais rápida.
- Temporariamente, escale verticalmente a sua instância de destino de banco de dados SQL do Azure para o SKU da camada Premium durante a operação de migração de dados para minimizar a limitação do banco de dados SQL do Azure que pode afetar as atividades de transferência de dados ao usar SKUs de nível inferior.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>P. Como configurar uma Rede Virtual do Azure?
Embora existam vários tutoriais da Microsoft que podem orientar você durante o processo de configuração de uma VNET do Azure, a documentação oficial aparece no artigo [Rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>P. Por que meu Serviço de Migração de Banco de Dados do Azure está indisponível ou parado?
Se o usuário explicitamente parar o Serviço de Migração de Banco de Dados do Azure (DMS) ou se o serviço ficar inativo por um período de 24 horas, o serviço será interrompido ou estará em estada de pausa automático. Em cada caso, o serviço estará indisponível e em status parado.  Para retomar as migrações ativas, reinicie o serviço.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>P. Onde é possível fazer comentários sobre o Serviço de Migração de Banco de Dados do Azure?
Queremos ouvir você. Envie quaisquer comentários e ideias que você tenha sobre o Serviço de Migração de Banco de Dados do Azure por meio de voz do usuário, [aqui](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral do Serviço de Migração de Banco de Dados do Azure e da disponibilidade regional, consulte o artigo [ O que é o Serviço de Migração de Banco de Dados do Azure ](dms-overview.md). 
