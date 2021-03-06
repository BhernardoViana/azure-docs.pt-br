---
title: Funções e permissões do Azure Data Factory
description: Descreve as funções e permissões necessárias para criar Data Factories e para trabalhar com recursos filho.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 00d84a7f3b4a15c9278caac8c2715380dd330482
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930492"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Funções e permissões do Azure Data Factory

Este artigo descreve as funções necessárias para criar e gerenciar recursos do Azure Data Factory e as permissões concedidas por essas funções.

## <a name="roles-and-requirements"></a>Requisitos e funções

Para criar instâncias de Data Factory, a conta de usuário usada para entrar no Azure deve ser um membro das funções *colaborador* ou *proprietário*, ou um *administrador* da assinatura do Azure. Para exibir as permissões que você tem na assinatura, portal do Azure, selecione seu nome de usuário no canto superior direito e selecione **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. 

Para criar e gerenciar recursos filho para o Data Factory – incluindo conjuntos de dados, serviços vinculados, pipelines, gatilhos e runtimes de integração –, os requisitos a seguir são aplicáveis:
- Para criar e gerenciar recursos filho no portal do Azure, você precisa pertencer à função **Colaborador do Data Factory** no nível do grupo de recursos ou superior.
- Para criar e gerenciar recursos filho com o PowerShell ou o SDK, a função de **colaborador** no nível do recurso ou superior é suficiente.

Para obter instruções de exemplo sobre como adicionar um usuário a uma função, confira o artigo [Adicionar funções](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="set-up-permissions"></a>Configurar permissões

Depois de criar um Data Factory, convém permitir que outros usuários trabalhem com ele. Para permitir acesso para outros usuários, você precisa adicioná-los à função interna **Colaborador do Data Factory** no grupo de recursos que contém o data factory.

### <a name="scope-of-the-data-factory-contributor-role"></a>Escopo da função de Colaborador do Data Factory

A associação à função **Colaborador do Data Factory** permite que os usuários façam o seguinte:
- Criem, editem e excluam os data factories e os recursos filho, incluindo conjuntos de dados, serviços vinculados, pipelines, gatilhos e runtimes de integração.
- Implantem modelos do Resource Manager. A implantação do Resource Manager é o método de implantação usado pelo Data Factory no portal do Azure.
- Gerenciem alertas do App Insights de um data factory.
- Criem tíquetes de suporte.

Para obter mais informações sobre essa função, confira [Função de Colaborador do Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Implantação de modelo do Resource Manager

A função **Colaborador do Data Factory**, no nível do grupo de recursos ou acima, permite que os usuários implantem modelos do Resource Manager. Como resultado, os membros da função podem usar modelos do Resource Manager para implantar o data factory e seus recursos filho, incluindo conjuntos de dados, serviços vinculados, pipelines, gatilhos e runtimes de integração. No entanto, a associação a essa função não permite que o usuário crie outros recursos.

As permissões no Azure Repos e no GitHub são independentes das permissões do Data Factory. Como resultado, um usuário com permissões de repo que é apenas um membro da função Leitor pode editar os recursos filhos do Data Factory e confirmar alterações no repo, mas não pode publicar essas alterações.

> [!IMPORTANT]
> A implantação de modelo do Resource Manager com a função **Colaborador do Data Factory** não eleva as permissões. Por exemplo, se você implantar um modelo que cria uma máquina virtual do Azure e você não tiver permissão para criar máquinas virtuais, a implantação falhará com um erro de autorização.

### <a name="custom-scenarios-and-custom-roles"></a>Cenários personalizados e funções personalizadas

Às vezes, você precisa conceder diferentes níveis de acesso para diferentes usuários do data factory. Por exemplo:
- Você pode precisar de um grupo em que os usuários só têm permissões em um data factory específico.
- Ou você pode precisar de um grupo em que os usuários só podem monitorar um data factory (ou factories), mas não podem modificá-lo.

Você pode obter esses cenários personalizados criando funções personalizadas e atribuindo usuários a essas funções. Para obter mais informações sobre funções personalizadas, confira [Funções personalizadas no Azure](..//role-based-access-control/custom-roles.md).

Aqui estão alguns exemplos que demonstram o que você pode obter com as funções personalizadas:

- Permitir que um usuário crie, edite ou exclua qualquer data factory em um grupo de recursos por meio do portal do Azure.

  Atribuir a função interna **Colaborador do Data Factory** no nível do grupo de recursos ao usuário. Se você quiser permitir o acesso a qualquer data factory em uma assinatura, atribua a função no nível da assinatura.

- Permitir que um usuário exiba (leia) e monitore um data factory, mas não o edite ou altere.

  Atribuir a função interna **leitor** no recurso do data factory ao usuário.

- Permitir que um usuário edite um único data factory no portal do Azure.

  Esse cenário requer duas atribuições de função.

  1. Atribuir a função interna **Colaborador** no nível do data factory.
  2. Crie uma função personalizada com a permissão **Microsoft.Resources/deployments/** . Atribua essa função personalizada ao usuário no nível do grupo de recursos.

- Permitir que um usuário só possa testar a conexão em um serviço vinculado

    Crie uma função de função personalizada com permissões para as seguintes ações: **Microsoft. datafactory/fábricas/Getfeaturevalue/Read** e **Microsoft. datafactory/factories/getDataPlaneAccess/Read**. Atribua essa função personalizada no recurso de data factory para o usuário.

- Permitir que um usuário atualize um data factory usando o PowerShell ou o SDK, mas não o portal do Azure.

  Atribuir a função interna **Colaborador** no recurso do data factory ao usuário. Essa função permite que o usuário veja os recursos no portal do Azure, mas não permite que ele acesse os botões **Publicar** e **Publicar Tudo**.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre as funções no Azure – [Entender as definições de função](../role-based-access-control/role-definitions.md)

- Saiba mais sobre a função **Colaborador do Data Factory** – [Função de Colaborador do Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).
