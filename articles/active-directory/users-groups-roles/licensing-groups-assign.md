---
title: Atribuir licenças a um grupo - Azure Active Directory | Microsoft Docs
description: Como atribuir licenças a usuários com o licenciamento de grupo do Azure Active Directory
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.openlocfilehash: ea243fd0dea2fe0c6afe325be2756330af47dba9
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510299"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Atribuir licenças a usuários por meio da associação a grupos no Azure Active Directory

Este artigo descreve como atribuir licenças de produtos para um grupo de usuários no Azure AD (Azure Active Directory) e, em seguida, verificar se eles estão licenciados corretamente.

Neste exemplo, o locatário contém um grupo de segurança chamado **Departamento de RH**. Esse grupo inclui todos os membros do departamento de recursos humanos (cerca de 1.000 usuários). Você deseja atribuir licenças do Office 365 Enterprise E3 para todo o departamento. O serviço Yammer Enterprise, que está incluído no produto, precisa ser desabilitado temporariamente até que o departamento esteja pronto para começar a usá-lo. Você também deseja implantar licenças do Enterprise Mobility + Security para o mesmo grupo de usuários.

> [!NOTE]
> Alguns serviços da Microsoft não estão disponíveis em todos os locais. Para que uma licença possa ser atribuída a um usuário, o administrador precisa especificar a propriedade “Local de uso” para o usuário.

> Para a atribuição de licenças de grupo, qualquer usuário sem um local de uso especificado herdará o local do diretório. Se você tiver usuários em vários locais, é recomendável sempre definir o local de uso como parte de seu fluxo de criação do usuário no Microsoft Azure AD (por exemplo, por meio da configuração do AAD Connect) – isso garante que o resultado da atribuição de licenças sempre seja correto e que os usuários não recebam serviços em locais que não são permitidos.

## <a name="step-1-assign-the-required-licenses"></a>Etapa 1: Atribuir as licenças necessárias

1. Entre no [**portal do Azure**](https://portal.azure.com) com uma conta Administrador. Para gerenciar licenças, a conta precisa ter função de administrador global ou de administrador da conta de usuário.

2. Selecione **Todos os serviços** no painel de navegação esquerdo e selecione **Azure Active Directory**. Você pode adicionar esse painel aos Favoritos ou fixá-lo no painel do portal.

3. No painel **Azure Active Directory**, selecione **Licenças** para abrir um painel onde é possível ver e gerenciar todos os produtos licenciados no locatário.

4. Em **todos os produtos**, selecione o Office 365 Enterprise E3 e o Enterprise Mobility + Security selecionando os nomes de produto. Para iniciar a atribuição, selecione **Atribuir** na parte superior do painel.

   ![Todos os produtos, atribuir licença](./media/licensing-groups-assign/all-products-assign.png)

5. No painel **Atribuir licença**, clique em **Usuários e grupos** para abrir o painel de **Usuários e grupos**. Pesquise o nome do grupo *Departamento de RH*, selecione o grupo e, em seguida, certifique-se de confirmar clicando em **Selecionar** na parte inferior do painel.

   ![Selecione um grupo](./media/licensing-groups-assign/select-a-group.png)

6. No painel **Atribuir licença**, clique em **Opções de atribuição (opcional)**, que exibe todos os planos de serviço incluídos nos dois produtos que foram selecionados anteriormente. Localize **Yammer Enterprise** e **desative** para desabilitar esse serviço de licença do produto. Confirme clicando em **OK** na parte inferior das **opções de Atribuição**.

   ![Opções de atribuição](./media/licensing-groups-assign/assignment-options.png)

7. Para concluir a atribuição, no painel **Atribuir licença**, clique em **Atribuir** na parte inferior do painel.

8. Uma notificação é exibida no canto superior direito mostrando o status e o resultado do processo. Se a atribuição para o grupo não pode ser concluída (por exemplo, devido a licenças já existentes no grupo), clique na notificação para exibir detalhes da falha.

Agora podemos ter especificado um modelo de licença no grupo de departamento de RH. Um processo em segundo plano no Azure AD foi iniciado para processar todos os membros existentes do grupo. Essa operação inicial pode levar algum tempo, dependendo do tamanho atual do grupo. A próxima etapa descreve como verificar se o processo foi concluído e como determinar se atenção adicional é necessária para resolver problemas.

> [!NOTE]
> Inicie a mesma atribuição de uma localização alternativa: **Usuários e grupos** no Azure AD. Vá para **Azure Active Directory** > **Usuários e grupos** > **Todos os grupos**. Localize o grupo, selecione-o e acesse a guia **Licenças**. No botão **Atribuir** na parte superior do painel abre o painel de atribuição da licença.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Etapa 2: Verificar se a atribuição inicial foi concluída

1. Vá para **Azure Active Directory** > **Usuários e grupos** > **Todos os grupos**. Localize o grupo do **Departamento de RH** cujas licenças foram atribuídas.

2. No painel de grupo **Departamento de RH**, selecione **Licenças**. Isso lhe permite confirmar rapidamente se as licenças foram totalmente atribuídas a usuários e se houve erros que exigem atenção. As informações a seguir estão disponíveis:

   - Lista de licenças de produtos que estão atualmente atribuídas ao grupo. Selecione uma entrada para mostrar a serviços específicos que foram habilitada e fazer alterações.

   - Status das alterações mais recentes de licença que foram feitas para o grupo (se as alterações estão sendo processadas ou o processamento foi concluído para todos os membros de usuário).

   - Informações sobre os usuários que estão em um estado de erro porque não foi possível atribuir licenças a eles.

   ![Opções de atribuição](./media/licensing-groups-assign/assignment-errors.png)

3. Confira informações mais detalhadas sobre a licença de processamento em **Azure Active Directory** > **Usuários e grupos** > *Nome do grupo* > **Logs de auditoria**. Observe as seguintes atividades:

   - Atividade: **Iniciar a aplicação da licença baseada em grupo aos usuários**. Isso é registrado quando o sistema identifica a alteração de atribuição de licença no grupo e começa a aplicá-la a todos os membros de usuário. Ele contém informações sobre a alteração foi feita.

   - Atividade: **Concluir a aplicação da licença baseada em grupo aos usuários**. Isso é registrado quando o sistema conclui o processamento de todos os usuários no grupo. Ele contém um resumo de quantos usuários foram processados com êxito e quantos usuários não puderam ter licenças de grupo atribuídas.

   [Leia esta seção](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) para saber mais sobre como logs de auditoria podem ser usados para analisar alterações feitas pelo licenciamento baseado em grupo.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Etapa 3: Verificar problemas de licença e resolvê-los

1. Vá para **Azure Active Directory** > **Usuários e grupos** > **Todos os grupos** e localize o grupo do **Departamento de RH** ao qual as licenças foram atribuídas.
2. No painel de grupo **Departamento de RH**, selecione **Licenças**. A notificação na parte superior do painel mostra que há 10 usuários cujas licenças não podem ser atribuídas. Clicar nela abre uma lista de todos os usuários com um estado de erro para esse grupo.
3. A coluna **Atribuições com falha** informa que ambas as licenças de produtos não puderam ser atribuídas aos usuários. A coluna **Principal motivo para falha** contém a causa da falha. Nesse caso, **Planos de serviço conflitante**.

   ![Atribuições com falha](./media/licensing-groups-assign/failed-assignments.png)

4. Selecione um usuário para abrir o painel **Licenças**. Esse painel mostra todas as licenças que atualmente estão atribuídas ao usuário. Neste exemplo, o usuário tem a licença Office 365 Enterprise E1 herdada do grupo **Usuários do quiosque**. Isso está em conflito com a licença E3 que o sistema tentou aplicar a partir do grupo **Departamento de RH**. Como resultado, nenhuma das licenças do grupo foram atribuídas ao usuário.

   ![Exibir as licenças para um usuário](./media/licensing-groups-assign/user-license-view.png)

5. Para resolver este conflito, podemos remover o usuário do grupo **Usuários do quiosque**. Após a alteração do Azure AD processa o **departamento de RH** licenças são atribuídas corretamente.

   ![Licença atribuída corretamente](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o conjunto de recursos de gerenciamento de licenças por meio de grupos, consulte os artigos a seguir:

* [O que é o licenciamento baseado em grupo no Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](licensing-groups-resolve-problems.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](licensing-groups-migrate-users.md)
* [Como migrar usuários entre licenças de produto usando o licenciamento baseado em grupo no Microsoft Azure Active Directory](licensing-groups-change-licenses.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](../active-directory-licensing-group-advanced.md)
* [Exemplos do PowerShell para licenciamento baseado em grupo no Azure Active Directory](licensing-ps-examples.md)
