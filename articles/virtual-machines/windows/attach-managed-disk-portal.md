---
title: Anexar um disco de dados gerenciado a uma VM do Windows – Azure
description: Como anexar um disco de dados gerenciado a uma VM Windows usando o portal do Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d98fdab6dcd0abb70967d1e17d138a860a31da5f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039312"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Anexar um disco de dados gerenciado a uma VM Windows usando o portal do Azure

Este artigo mostra como anexar um novo disco de dados gerenciados a uma VM (máquina virtual) do Windows usando o portal do Azure. O tamanho da VM determina quantos discos de dados você pode anexar. Para obter mais informações, confira [Tamanhos das máquinas virtuais](sizes.md).


## <a name="add-a-data-disk"></a>Adicionar um disco de dados

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **Máquinas virtuais**.
2. Selecione uma máquina virtual na lista.
3. Na página **Máquina virtual**, selecione **Discos**.
4. Na página **Discos**, selecione **Adicionar disco de dados**.
5. No menu suspenso do novo disco, selecione **Criar disco**.
6. Na página **Criar disco gerenciado**, digite um nome para o disco e ajuste as outras configurações, conforme necessário. Quando terminar, selecione **Criar**.
7. Na página **Discos**, selecione **Salvar** para salvar a nova configuração de disco da VM.
8. Depois que o Azure cria o disco e o anexa à máquina virtual, o novo disco é listado nas configurações de disco da máquina virtual em **Discos de dados**.


## <a name="initialize-a-new-data-disk"></a>Inicializar um novo disco de dados

1. Conecte-se à VM.
1. Selecione o menu **Iniciar** do Windows na VM em execução e insira **diskmgmt.msc** na caixa de pesquisa. O console **Gerenciamento de Disco** é aberto.
2. O Gerenciamento de Disco reconhece que você tem um disco novo não inicializado e a janela **Inicializar Disco** é exibida.
3. Verifique se o novo disco está selecionado e, em seguida, selecione **OK** para inicializá-lo.
4. O novo disco é exibido como **não alocado**. Clique com o botão direito do mouse em qualquer lugar do disco e selecione **Novo volume simples**. A janela **Assistente de Novo Volume Simples** é aberta.
5. Percorra o assistente, mantendo todos os padrões e, quando terminar, selecione **Concluir**.
6. Feche **Gerenciamento de Disco**.
7. Uma janela pop-up será exibida informando que é necessário formatar o novo disco antes de usá-lo. Selecione **Formatar disco**.
8. Na janela **Formatar novo disco**, verifique as configurações e, em seguida, selecione **Iniciar**.
9. Será exibido um aviso informando que formatar os discos apaga todos os dados. Selecione **OK**.
10. Quando a formatação estiver concluída, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

- Você também pode [anexar um disco de dados usando o PowerShell](attach-disk-ps.md).
- Caso seu aplicativo precise usar a unidade *D:* para armazenar dados, é possível [alterar a letra da unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
