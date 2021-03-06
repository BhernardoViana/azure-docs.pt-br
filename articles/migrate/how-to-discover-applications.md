---
title: Descobrir aplicativos, funções e recursos instalados em servidores locais usando a avaliação de servidor de migrações para Azure
description: Descreve como descobrir aplicativos, funções e recursos em servidores locais usando a avaliação de servidor de migrações para Azure.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 11/20/2019
ms.author: snehaa
ms.openlocfilehash: 279e326ace308b354d7bcb8366d3286980e7b8c6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278467"
---
# <a name="discover-machine-apps-roles-and-features"></a>Descobrir aplicativos, funções e recursos de computador

Este artigo descreve como descobrir aplicativos, funções e recursos em servidores locais, usando migrações para Azure: avaliação de servidor.

Descobrir o inventário de aplicativos e funções/recursos em execução em suas máquinas locais ajuda você a identificar e planejar um caminho de migração para o Azure adaptado para suas cargas de trabalho.

> [!NOTE]
> Atualmente, o app Discovery tem suporte apenas para VMs VMware e é limitado apenas à descoberta. Ainda não oferecemos a avaliação baseada em aplicativo.  Atualmente, a avaliação do servidor avalia as VMs VMware locais, as VMs do Hyper-V e os servidores físicos no nível do computador, para migrações de comparação e deslocamento.

Descoberta de aplicativos usando migrações para Azure: a avaliação do servidor é sem agente. Nada precisa ser instalado em máquinas e VMs. A avaliação do servidor usa o dispositivo de migrações para Azure para executar a descoberta junto com as credenciais de convidado do computador. O dispositivo acessa remotamente as máquinas VMware usando APIs do VMware.


## <a name="before-you-start"></a>Antes de começar

1. Examine as [limitações de suporte](migrate-support-matrix-vmware.md#application-discovery) para a descoberta em nível de aplicativo.
2. Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto de migrações para Azure.
3. Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta migrações do Azure: Server Assessment.
4. Verifique os [requisitos de VMware](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) para descobrir e avaliar VMs VMware com o dispositivo de migrações para Azure.
4. Verifique os [requisitos](migrate-support-matrix-vmware.md#assessment-appliance-requirements) para implantar o dispositivo de migrações para Azure.

## <a name="prepare-for-app-discovery"></a>Preparar para a descoberta de aplicativos

1. [Prepare-se para a implantação do dispositivo](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware). A preparação inclui a verificação das configurações do dispositivo e a configuração de uma conta que será usada pelo dispositivo para acessar vCenter Server.
2. Verifique se você tem uma conta de usuário (uma para servidores Windows e Linux) com permissões de administrador para computadores nos quais você deseja descobrir aplicativos, funções e recursos.
3. [Implante o dispositivo VMware](how-to-set-up-appliance-vmware.md) para iniciar a descoberta. Para implantar o dispositivo, você baixa e importa um modelo OVA no VMware para criar o dispositivo como uma VM VMware. Você configura o dispositivo e o registra com as migrações para Azure.
2. Ao implantar o dispositivo, para iniciar a descoberta contínua, especifique o seguinte:
    - O nome do vCenter Server ao qual você deseja se conectar.
    - Credenciais que você criou para o dispositivo se conectar ao vCenter Server.
    - As credenciais de conta que você criou para o dispositivo se conectar a VMs do Windows/Linux.

Depois que o dispositivo é implantado e você forneceu credenciais, o dispositivo inicia a descoberta contínua de metadados de VM e dados de desempenho, juntamente com a descoberta de aplicativos, recursos e funções.  A duração da descoberta de aplicativos depende de quantas VMs você tem. Normalmente leva uma hora para a descoberta de aplicativos de 500 VMs.

## <a name="review-and-export-the-inventory"></a>Revisar e exportar o inventário

Após a conclusão da descoberta, se você forneceu credenciais para a descoberta de aplicativos, poderá revisar e exportar o inventário de aplicativos no portal do Azure.

1. Em **migrações para Azure-servidores** > **migrações para Azure: avaliação do servidor**, clique na contagem exibida para abrir a página **servidores descobertos** .

    > [!NOTE]
    > Nesse estágio, opcionalmente, você também pode configurar o mapeamento de dependência para computadores descobertos, para que você possa Visualizar dependências entre computadores que deseja avaliar. [Saiba mais](how-to-create-group-machine-dependencies.md).

2. Em **aplicativos descobertos**, clique na contagem exibida.
3. No **inventário de aplicativos**, você pode examinar os aplicativos, funções e recursos descobertos.
4. Para exportar o inventário, em **servidores descobertos**, clique em **Exportar inventário de aplicativos**.

O inventário de aplicativos é exportado e baixado no formato do Excel. A folha de **inventário de aplicativos** exibe todos os aplicativos descobertos em todos os computadores.

## <a name="next-steps"></a>Próximas etapas

- [Crie uma avaliação](how-to-create-assessment.md) para a migração de comparação e de deslocamento dos servidores descobertos.
- Avaliar um SQL Server bancos de dados usando [migrações para Azure: avaliação de banco de dados](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
