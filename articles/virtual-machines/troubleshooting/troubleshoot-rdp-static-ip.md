---
title: Não é possível adicionar uma área de trabalho remota para máquinas virtuais do Azure devido a IP estático | Microsoft Docs
description: Saiba como solucionar problemas de RDP causados por IP estático no Microsoft Azure. Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: d1cb34f1666db0e213a41fa8d2e684ba148b7e61
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088456"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Não é possível adicionar o desktop remoto para máquinas virtuais do Azure devido a IP estático

Este artigo descreve um problema no qual você não pode adicionar uma área de trabalho remota para Máquinas Virtuais do Windows do Azure (VMs) depois que um IP estático é configurado na VM.

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo cobre o uso do modelo de implantação do Gerenciador de Recursos, quais recomendamos usar para novas implantações em vez do modelo de implantação clássico.

## <a name="symptoms"></a>Sintomas

Quando você faz uma conexão RDP para uma VM no Azure, você recebe a seguinte mensagem de erro:

**A Área de Trabalho Remota não pode se conectar ao computador remoto por um dos seguintes motivos:**

1. **O acesso remoto ao servidor não está habilitado**

2. **O computador remoto está desligado**

3. **O computador remoto não está disponível na rede**

**Verifique se o computador remoto está ligado e conectado à rede e que o acesso remoto está habilitado.**

Quando você verifica a captura de tela no [Diagnóstico de inicialização](../troubleshooting/boot-diagnostics.md) no portal do Azure, a VM é inicializada normalmente e aguarda as credenciais na tela de login.

## <a name="cause"></a>Causa

A VM tem um endereço IP estático definido na interface de rede no Windows. Esse endereço IP é diferente do endereço definido no portal do Azure.

## <a name="solution"></a>Solução

Antes de seguir essas etapas, tire um instantâneo do disco do SO da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver esse problema, use o controle Serial para ativar o DHCP ou [redefinir a interface de rede](reset-network-interface.md) para a VM.

### <a name="use-serial-control"></a>Usar o Controle serial

1. Conecte-se ao [Console Serial e abra a instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se o Console serial não estiver habilitado em sua VM, consulte [Redefinir a interface de rede](reset-network-interface.md).
2. Verifique se o DHCP está desativado na interface de rede:

        netsh interface ip show config
3. Se o DHCP estiver desativado, reverta a configuração da sua interface de rede para usar o DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Por exemplo, se a interface interwork nomear "Ethernet 2", execute o seguinte comando:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Consulte a configuração IP novamente para garantir que a interface de rede esteja configurada corretamente. O novo endereço IP deve corresponder ao que é fornecido pelo Azure.

        netsh interface ip show config

    Você não precisa reiniciar a VM neste momento. A VM estará de volta acessível.

Depois disso, se você quiser configurar o IP estático para a VM, consulte [Configurar endereços IP estático para uma VM](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).