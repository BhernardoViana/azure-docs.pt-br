---
title: Anexar um disco de dados gerenciado a uma VM Windows – Azure | Microsoft Docs
description: Como anexar um novo disco de dados gerenciado a uma VM Windows no portal do Azure usando o modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 14721b2f2bc7913c2b7eadfc5ee801a223201ea9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913409"
---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Como anexar um disco de dados gerenciado a uma VM Windows no portal do Azure

Este artigo mostra como anexar um novo disco de dados gerenciados a máquinas virtuais Windows no portal do Azure. Antes de fazer isso, revise estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](sizes.md).
* Para um novo disco, você não precisa criá-lo primeiro porque o Azure cria quando você anexa o mesmo.

Você também pode [anexar um disco de dados usando o Powershell](attach-disk-ps.md).



## <a name="add-a-data-disk"></a>Adicionar um disco de dados
1. No menu à esquerda, clique em **Máquinas Virtuais**.
2. Selecione a máquina virtual na lista.
3. Na página da máquina virtual, clique em **Discos**.
4. Na página **Discos**, clique em **+ Adicionar disco de dados**.
5. No menu suspenso do novo disco, selecione **Criar disco**.
6. Na página **Criar disco gerenciado**, digite um nome para o disco e ajuste as outras configurações, conforme necessário. Quando terminar, clique em **Criar**.
7. Na página **Discos**, clique em **Salvar** para salvar a nova configuração de disco da VM.
6. Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **Discos de dados**.


## <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Conecte-se à VM.
1. Clique no menu Iniciar dentro da VM, digite **diskmgmt.msc** e clique em **Enter**. O snap-in de gerenciamento de disco é aberto.
2. O Gerenciamento de Disco reconhece que você tem um disco novo não inicializado, e a janela **Inicializar Disco** será exibida.
3. Verifique se o novo disco está selecionado e clique em **OK** para inicializá-lo.
4. O novo disco é exibido como **não alocado**. Clique com o botão direito do mouse em qualquer lugar do disco e selecione **Novo volume simples**. O **Assistente Novo Volume Simples** é aberto.
5. Percorra as etapas do assistente mantendo todos os padrões. Quando terminar, selecione **Concluir**.
6. Feche Gerenciamento de Disco.
7. Você recebe um pop-up que é necessário para formatar o novo disco antes que você possa usá-lo. Clique em **Formatar disco**.
8. Na caixa de diálogo **Formatar novo disco**, verifique as configurações e, em seguida, clique em **Iniciar**.
9. Você recebe um aviso informando que a formatação dos discos apaga todos os dados; clique em **OK**.
10. Quando a formatação for concluída, clique em **OK**.

## <a name="use-trim-with-standard-storage"></a>Usar TRIM com o armazenamento padrão

Se você usar o armazenamento padrão (HDD), será necessário habilitar o TRIM. O TRIM descarta os blocos não usados do disco para que você seja cobrado apenas pelo armazenamento que está efetivamente sendo usado. Isso poderá representar uma economia dos custos se você criar arquivos grandes e, em seguida, excluí-los. 

Você pode executar esse comando para verificar a configuração de TRIM. Abra um prompt de comando na sua VM do Windows e digite:

```
fsutil behavior query DisableDeleteNotify
```

Se o comando retornar 0, o TRIM estará habilitado corretamente. Se ele retornar 1, execute o seguinte comando para habilitar o TRIM:
```
fsutil behavior set DisableDeleteNotify 0
```

Após a exclusão de dados do disco, você pode garantir a liberação de operações TRIM corretamente executando a desfragmentação com TRIM:

```
defrag.exe <volume:> -l
```

Você também pode garantir que o volume inteiro seja reduzido formatando-o.

## <a name="next-steps"></a>Próximas etapas
Caso seu aplicativo precise usar a unidade D: para armazenar dados, é possível [alterar a letra da unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
