---
title: Suporte para o Azure Active Directory B2C | Microsoft Docs
description: Como registrar solicitações de suporte para o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ae5d04b7fac89cefb40d5542e2c6e83533afcfce
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835769"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Preencher solicitações de suporte
Você pode arquivar solicitações de suporte para o Azure AD (Azure Active Directory) B2C no portal do Azure usando as seguintes etapas:

1. Alterne do locatário B2C para outro que tenha uma assinatura do Azure associada a ele. Normalmente, o segundo é o locatário de funcionário ou o locatário padrão criado quando você se inscreveu para uma assinatura do Azure. Para saber mais, consulte [como uma assinatura do Azure está relacionada ao Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![Portal do Azure com a seleção de locatário realçado](./media/active-directory-b2c-support/support-switch-dir.png)

3. Depois de alternar os locatários, clique em **Ajuda + suporte**.

    ![Ajuda + bloco suporte realçado no portal do Azure](./media/active-directory-b2c-support/support-support.png)

4. Clique em **Nova solicitação de suporte**.

    ![Novo bloco de solicitação de suporte realçado no portal do Azure](./media/active-directory-b2c-support/support-new.png)
5. Na folha **Fundamentos**, use esses detalhes e clique em **Próximo**.

   * O **Tipo de emissão** é **Técnico**.
   * Escolha a **Assinatura**apropriada.
   * O **Serviço** é **Active Directory**.
   * Escolha o **Plano de suporte**apropriado. Se não tiver um, inscreva-se para ter um [aqui](https://azure.microsoft.com/support/plans/).

     ![Página de Noções básicas com o botão Avançar realçado no portal do Azure](./media/active-directory-b2c-support/support-basics.png)
6. Na folha **Problema**, use esses detalhes e clique em **Próximo**.

   * Escolha o nível de **Gravidade** apropriado.
   * O **Tipo de problema** é **B2C**.
   * Escolha a **Categoria**apropriada.
   * Descreva seu problema no campo **Detalhes** . Forneça detalhes como o nome do locatário B2C, a descrição do problema, a mensagens de erro, as IDs de correlação (se estiverem disponíveis) e assim por diante.
   * No campo **Período** , forneça a data e a hora (incluindo o fuso horário) de quando o problema ocorreu.
   * Em **Upload do arquivo**, carregue todas as capturas de tela e arquivos que você acha que ajudariam na resolução do problema.

     ![Página do problema com o botão Avançar realçado no portal do Azure](./media/active-directory-b2c-support/support-problem.png)
7. Na folha **Informações de contato** , adicione suas informações de contato. Clique em **Criar**.

    ![Página de informações de contato com o botão Criar realçado no portal do Azure](./media/active-directory-b2c-support/support-contact.png)
8. Depois de enviar a solicitação de suporte, você pode monitorá-lo clicando em **Ajuda + suporte** no quadro inicial, então, em **Gerenciar solicitações de suporte**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Problema conhecido: O preenchimento de uma solicitação de suporte no contexto de um locatário B2C
Se tiver ignorado a etapa 2 descrita acima e tentar criar uma solicitação de suporte no contexto do seu locatário B2C, você verá o erro a seguir.

> [!IMPORTANT]
> Não tente se inscrever em uma nova assinatura do Azure em seu locatário B2C.
>
>

![Suporte — sem assinatura](./media/active-directory-b2c-support/support-no-sub.png)

