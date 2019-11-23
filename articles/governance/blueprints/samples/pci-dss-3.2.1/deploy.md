---
title: Exemplo do PCI-DSS v 3.2.1 Blueprint – implantar etapas
description: Implante as etapas para o cartão de pagamento do setor de informações de Data Security padrão v 3.2.1 Blueprint, incluindo detalhes do parâmetro de artefato Blueprint.
ms.date: 06/24/2019
ms.topic: conceptual
ms.openlocfilehash: b29a1a66b7407558769e506deed5e65f7ecb064c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037249"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Implantar a amostra do PCI-DSS v 3.2.1 Blueprint

Para implantar a amostra do plano gráfico do Azure Blueprints PCI-DSS v 3.2.1, as etapas a seguir devem ser executadas:

> [!div class="checklist"]
> - Criar um blueprint com base na amostra
> - Marcar sua cópia do exemplo como **Publicado**
> - Atribuir a sua cópia do blueprint a uma assinatura existente

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar um blueprint com base na amostra

Primeiro, implemente a amostra de blueprint criando um blueprint no ambiente usando a amostra como ponto de partida.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Encontre o exemplo de plano gráfico **PCI-DSS v 3.2.1** em _outros exemplos_ e selecione **usar este exemplo**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do plano gráfico**: forneça um nome para sua cópia do exemplo de plano gráfico PCI-DSS v 3.2.1.
   - **Local de definição**: Use as reticências e selecione o grupo de gerenciamento para salvar a cópia do exemplo.

1. Selecione a guia _artefatos_ na parte superior da página ou **próximo: artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo de blueprint. Muitos dos artefatos têm parâmetros que definiremos mais tarde. Selecione **Salvar Rascunho** quando terminar de examinar o exemplo de blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

Agora a cópia do exemplo de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada. A cópia do exemplo Blueprint pode ser personalizada para seu ambiente e precisa, mas essa modificação pode movê-lo para fora do padrão PCI-DSS v 3.2.1.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. Na nova página à direita, informe a **Versão** da sua cópia da amostra de blueprint. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **observações de alteração** , como "primeira versão publicada do exemplo de plano gráfico PCI-DSS v 3.2.1". Em seguida, selecione **Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Quando a cópia do exemplo de blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da cópia do exemplo de blueprint.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a cópia da amostra de blueprint e, em seguida, selecione-a.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - Noções básicas

     - **Assinaturas**: selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou sua cópia do exemplo Blueprint. Se você selecionar mais de uma assinatura, será criada uma atribuição para cada uma, usando os parâmetros inseridos.
     - **Nome da atribuição**: o nome é preenchido previamente para você com base no nome do plano gráfico.
       Altere-o conforme necessário ou mantenha-o como está.
     - **Local**: selecione uma região na qual a identidade gerenciada deve ser criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição do Blueprint**: escolha uma versão **publicada** da sua cópia do exemplo Blueprint.

   - Bloquear atribuição

     Selecione a configuração de bloqueio de blueprint para o seu ambiente. Para obter mais informações, consulte [bloqueio de recursos de projetos](../../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe a opção de identidade gerenciada _atribuída ao sistema_ padrão.

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para obter uma lista completa ou parâmetros de artefato e suas descrições, confira a [Tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página. A atribuição de blueprint é criada, e a implantação de artefato é iniciada. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição de blueprint.

> [!WARNING]
> As amostras internas de blueprint e o serviço Azure Blueprints são **gratuitos**. Os recursos do Azure são [precificados por produto](https://azure.microsoft.com/pricing/). Use a [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por essa amostra de blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A seguinte tabela fornece uma lista dos parâmetros de artefato de blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|DESCRIÇÃO|
|-|-|-|-|
|\[visualização\] auditoria de PCI v 3.2.1: controles de 2018 e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria|Atribuição de política|Lista de tipos de recursos | Configuração de diagnóstico de auditoria para tipos de recursos selecionados. O valor padrão é que todos os recursos estão selecionados| 
|Locais permitidos|Atribuição de política|Lista de locais permitidos|Lista de locais de data center permitidos para qualquer recurso a ser implantado. Essa lista é personalizável para os locais do Azure desejados globalmente. Selecione os locais que você deseja permitir.| 
|Locais permitidos para grupos de recursos|Atribuição de política |Local permitido |Essa política permite restringir os locais em que sua organização pode criar grupos de recursos. Use para impor seus requisitos de conformidade geográfica.| 
|Implantar Auditoria em servidores SQL|Atribuição de política|Dias de retenção|Retenção de dados em número de dias. O valor padrão é 180, mas o PCI requer 365.| 
|Implantar Auditoria em servidores SQL|Atribuição de política|Nome do grupo de recursos para a conta de armazenamento|A auditoria grava eventos de banco de dados em um log de auditoria na sua conta de Armazenamento do Azure (uma conta de armazenamento será criada em cada região onde um SQL Server é criado e será compartilhada por todos os servidores na região).| 

## <a name="next-steps"></a>Próximas etapas

Agora que você analisou as etapas para implantar o exemplo de plano gráfico PCI-DSS v 3.2.1, visite os seguintes artigos para saber mais sobre a visão geral e o mapeamento de controle:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 Blueprint-visão geral](./index.md)
> [PCI-DSS v 3.2.1 Blueprint – mapeamento de controle](./control-mapping.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).
