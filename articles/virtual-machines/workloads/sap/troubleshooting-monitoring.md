---
title: Monitoramento do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Monitore o SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0aea4dddef65600fe30f36499d4ad2a4f461245
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077931"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Como monitorar o SAP HANA (instâncias grandes) no Azure

SAP HANA no Azure (Instâncias Grandes) não é diferente de qualquer outra implantação IaaS: você precisa monitorar o que o sistema operacional e o aplicativo estão fazendo e como os aplicativos consomem os recursos a seguir:

- CPU
- Memória
- Largura de banda de rede
- Espaço em disco

Com as Máquinas Virtuais do Azure, você precisa descobrir se as classes de recursos indicadas acima são suficientes ou se esgotam. Confira mais detalhes sobre cada uma das diferentes classes:

**Consumo de recursos de CPU:** A proporção que o SAP definiu para determinadas cargas de trabalho em relação ao HANA é imposta para garantir que deve haver recursos suficientes de CPU disponíveis para trabalhar com os dados armazenados na memória. No entanto, pode haver casos em que o HANA consome muitas CPUs executando consultas devido à ausência de índices ou problemas semelhantes. Isso significa que você deve monitorar o consumo de recursos de CPU da unidade de instância grande do HANA, bem como recursos de CPU consumidos por serviços específicos do HANA.

**Consumo de memória:** É importante monitorar de dentro do HANA, bem como fora do HANA na unidade. Dentro do HANA, monitore como os dados estão consumindo memória alocada no HANA a fim de permanecer dentro das diretrizes de dimensionamento obrigatórias do SAP. Convém também monitorar o consumo de memória no nível da Instância Grande para certificar-se de que outros softwares não HANA instalados não consumam muita memória e, assim, compitam por memória com o HANA.

**Largura de banda da rede:** O gateway de VNet do Azure é limitado na largura de banda dos dados que se movem para a VNet do Azure, portanto, é útil monitorar os dados recebidos por todas as VMs do Azure em uma VNet para descobrir o quão perto você é para os limites do SKU do gateway do Azure selecionado. Na unidade de Instância Grande do HANA, também faz sentido monitorar o tráfego de rede de entrada e saída, além de controlar os volumes manipulados ao longo do tempo.

**Espaço em disco:** O consumo de espaço em disco geralmente aumenta com o passar do tempo. Os motivos mais comuns são: aumento no volume de dados, execução de backups do log de transações, armazenamento de arquivos de rastreamento e execução de instantâneos de armazenamento. Portanto, é importante monitorar o uso do espaço em disco e gerenciar o espaço em disco associado à Instância Grande do HANA.

Para os **SKUs do tipo II** de grandes instâncias HANA, o servidor é fornecido com as ferramentas de diagnóstico do sistema pré-carregadas. Você pode usar essas ferramentas de diagnóstico para executar a verificação de integridade do sistema. Execute o seguinte comando para gerar o arquivo de log de verificação de integridade em /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Quando você trabalha com a equipe de Suporte da Microsoft para solucionar problemas, também será solicitado que você forneça os arquivos de log usando essas ferramentas de diagnóstico. Zipe o arquivo usando o comando a seguir.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Próximas etapas**

- Veja [Como monitorar o SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot).
