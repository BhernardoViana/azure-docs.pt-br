---
title: Criar cofres dos serviços de recuperação
description: Neste artigo, saiba como criar cofres dos serviços de recuperação que armazenam os backups e os pontos de recuperação.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 144d8cdb870e12474dfc47784749b5f0e466f8bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273389"
---
# <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos Serviços de Recuperação é uma entidade que armazena os backups e os pontos de recuperação criados ao longo do tempo. O cofre de Serviços de Recuperação também contém as políticas de backup associadas às máquinas virtuais protegidas.

Para criar um cofre de Serviços de Recuperação:

1. Entre na sua assinatura no [portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Todos os serviços**.

    ![Selecionar Todos os serviços](./media/backup-create-rs-vault/click-all-services.png)

3. Na caixa de diálogo **Todos os serviços**, insira **Serviços de Recuperação**. A lista de recurso filtra de acordo com sua entrada. Na lista de recursos, selecione **Cofres dos Serviços de Recuperação**.

    ![Insira e escolha os cofres dos Serviços de Recuperação](./media/backup-create-rs-vault/all-services.png)

    A lista de cofres de Serviços de Recuperação na assinatura aparecerá.

4. No painel **Cofres dos Serviços de Recuperação**, selecione **Adicionar**.

    ![Adicionar um cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/add-button-create-vault.png)

    A caixa de diálogo **Cofre dos Serviços de Recuperação** é aberta. Forneça valores para **Nome**, **Assinatura**, **Grupo de recursos** e **Local**.

    ![Configurar o cofre de Serviços de Recuperação](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: digite um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hifens.
   - **Assinatura**: escolha a assinatura a ser usada. Se você for um membro de apenas uma assinatura, verá esse nome. Se você não tem certeza de qual assinatura usar, utilize a assinatura padrão (sugerida). Só haverá múltiplas opções se a sua conta corporativa ou de estudante estiver associada a várias assinaturas do Azure.
   - **Grupo de recursos**: use um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis em sua assinatura, selecione **Usar existente** e, em seguida, selecione um recurso na caixa de listagem suspensa. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome. Para obter informações completas sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - **Local**: selecione a região geográfica para o cofre. Se você estiver criando um cofre para proteger as máquinas virtuais, o cofre **deverá** estar na mesma região que as máquinas virtuais.

      > [!IMPORTANT]
      > Se você não tem certeza do local da sua VM, feche a caixa de diálogo. Vá para a lista de máquinas virtuais no portal. Se você tem máquinas virtuais em várias regiões, crie um cofre dos Serviços de Recuperação em cada região. Crie o cofre no primeiro local antes de criar o cofre para outro local. Não é necessário especificar contas de armazenamento para armazenar os dados de backup. O cofre de Serviços de Recuperação e o serviço de Backup do Azure lidam com isso automaticamente.
      >
      >

5. Quando você estiver pronto para criar o cofre dos Serviços de Recuperação, clique em **Criar**.

    ![Criar o cofre dos Serviços de Recuperação](./media/backup-create-rs-vault/click-create-button.png)

    Pode levar um tempo para criar o cofre dos Serviços de Recuperação. Monitore as notificações de status na área **Notificações** no canto superior direito do portal. Depois que o cofre tiver sido criado, ele aparecerá na lista de cofres dos Serviços de Recuperação. Se você não encontrar seu cofre, selecione **Atualizar**.

     ![Atualizar a lista de cofres de backup](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Definir redundância de armazenamento

O backup do Azure manipula automaticamente o armazenamento para o cofre. Você precisa especificar como esse armazenamento é replicado.

1. Na folha **Cofres dos Serviços de Recuperação**, clique no novo cofre. Na seção **configurações** , clique em **Propriedades**.
2. Em **Propriedades**, em **configuração de backup**, clique em **Atualizar**.

3. Selecione o tipo de replicação de armazenamento e clique em **salvar**.

     ![Definir a configuração de armazenamento para o novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Recomendamos que, se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup primário, continue a usar a configuração padrão **com redundância geográfica** .
   - Se você não usar o Azure como um ponto de extremidade de armazenamento de backup principal, escolha **Localmente redundante**, que reduz os custos de armazenamento do Azure.
   - Saiba mais sobre a redundância [geográfica](../storage/common/storage-redundancy-grs.md) e [local](../storage/common/storage-redundancy-lrs.md) .

> [!NOTE]
> A alteração do **tipo de replicação de armazenamento** (com redundância local/geograficamente) para um cofre dos serviços de recuperação deve ser feita antes de configurar backups no cofre. Depois de configurar o backup, a opção para modificar é desabilitada e você não pode alterar o **tipo de replicação de armazenamento**.

## <a name="modifying-default-settings"></a>Modificando configurações padrão

É altamente recomendável revisar as configurações padrão de **tipo de replicação de armazenamento** e configurações de **segurança** antes de configurar backups no cofre. 
* O **tipo de replicação de armazenamento** por padrão é definido como com **redundância geográfica**. Depois de configurar o backup, a opção para modificar é desabilitada. Siga estas [etapas](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) para examinar e modificar as configurações. 
* A **exclusão reversível** por padrão é **habilitada** em cofres recém-criados para proteger dados de backup de exclusões acidentais ou mal-intencionadas. Siga estas [etapas](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) para examinar e modificar as configurações.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](backup-azure-recovery-services-vault-overview.md) Cofres dos serviços de recuperação.
[Saiba mais](backup-azure-delete-vault.md) Excluir cofres dos serviços de recuperação.
