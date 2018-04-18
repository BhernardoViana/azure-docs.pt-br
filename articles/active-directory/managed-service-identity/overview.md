---
title: Identidade de Serviço Gerenciado (MSI) para o Azure Active Directory
description: Uma visão geral da Identidade de Serviço Gerenciado para recursos do Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: e4f9d9e4e0f84610ad072d889abf68b62c0dd41f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Identidade de Serviço Gerenciado (MSI) para recursos do Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Um desafio comum ao criar aplicativos de nuvem é como gerenciar as credenciais que precisam estar em seu código para autenticar para serviços de nuvem. Manter essas credenciais seguras é uma tarefa importante. Idealmente, eles nunca aparecem em estações de trabalho do desenvolvedor ou são verificadas no controle do código-fonte. O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar para o Key Vault para recuperá-los. A Identidade de Serviço Gerenciado (MSI) torna a solução desse problema mais simples, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

## <a name="how-does-it-work"></a>Como ele funciona?

Quando você habilita a Identidade de Serviço Gerenciado em um serviço do Azure, o Azure cria automaticamente uma identidade para a instância de serviço no locatário do Azure AD usado por sua assinatura do Azure.  Nos bastidores, o Azure provisiona as credenciais de identidade para a instância do serviço.  Seu código, em seguida, pode fazer uma solicitação local para obter tokens de acesso para os serviços que oferecem suporte à autenticação do Azure AD.  O Azure é responsável por reverter as credenciais usadas pela instância de serviço.  Se a instância de serviço é excluída, o Azure limpa automaticamente as credenciais e a identidade no Azure AD.

Aqui está um exemplo de como a Identidade de Serviço Gerenciado funciona com as Máquinas Virtuais do Azure.

![Exemplo de MSI de Máquina Virtual](../media/msi-vm-example.png)

1. O Azure Resource Manager recebe uma mensagem para habilitar o MSI em uma máquina virtual.
2. O Azure Resource Manager cria uma Entidade de Serviço no Azure AD para representar a identidade da máquina virtual. A Entidade de Serviço é criada no locatário do Azure AD que é confiável para essa assinatura.
3. O Azure Resource Manager configura os detalhes da Entidade de Serviço na extensão de VM do MSI da VM.  Essa etapa inclui definir a ID do cliente e o certificado usado pela extensão para obter tokens de acesso do Azure AD.
4. Agora que a identidade da Entidade de Serviço da VM é conhecida, ela pode ser concedida acesso aos recursos do Azure.  Por exemplo, se seu código precisa chamar o Azure Resource Manager, em seguida, você atribuiria à Entidade de Serviço da VM a função apropriada usando o controle de acesso baseado em função (RBAC) no Azure AD.  Se seu código precisar chamar o Key Vault, então você concederia ao seu código o acesso ao segredo ou chave específica no Key Vault.
5. O código em execução na máquina virtual solicita um token de um ponto de extremidade local que é hospedado pela extensão de VM MSI: http://localhost:50342/oauth2/token.  O parâmetro de recurso especifica o serviço ao qual o token é enviado. Por exemplo, se você quiser que seu código autentique para o Azure Resource Manager, você usaria resource=https://management.azure.com/.
6. A extensão de VM do MSI usa sua ID de cliente configurado e o certificado para solicitar um token de acesso do Azure AD.  O Azure AD retorna um token de acesso do JSON Web Token (JWT).
7. Seu código envia o token de acesso em uma chamada para um serviço que dá suporte à autenticação do Azure AD.

Cada serviço do Azure que dá suporte a Identidades de Serviço Gerenciado tem seu próprio método para seu código obter um token de acesso. Confira os tutoriais para cada serviço para descobrir o método específico para obter um token.

## <a name="try-managed-service-identity"></a>Experimente a Identidade de Serviço Gerenciado

Tente um tutorial de identidade de serviço gerenciado para saber os cenários de ponta a ponta para acessar recursos do Azure diferentes:
<br><br>
| De recurso de permissão MSI | Saiba como |
| ------- | -------- |
| VM do Azure (Windows) | [Acessar o Azure Data Lake Store com a identidade do serviço gerenciado da VM Windows](tutorial-windows-vm-access-datalake.md) |
|                    | [Acessar o Azure Resource Manager com uma Identidade de Serviço Gerenciado de VM do Windows](tutorial-windows-vm-access-arm.md) |
|                    | [Acessar o SQL do Azure com uma Identidade de Serviço Gerenciado de VM do Windows](tutorial-windows-vm-access-sql.md) |
|                    | [Acessar o Armazenamento do Azure por meio de uma chave de acesso com uma Identidade do Serviço Gerenciado de VM do Windows](tutorial-windows-vm-access-storage.md) |
|                    | [Acessar o Armazenamento do Azure por meio do SAS com uma Identidade do Serviço Gerenciado de VM do Windows](tutorial-windows-vm-access-storage-sas.md) |
|                    | [Acessar um recurso que não é do Azure AD com uma Identidade de Serviço Gerenciado de VM do Windows e um Azure Key Vault](tutorial-windows-vm-access-nonaad.md) |
| VM do Azure (Linux)   | [Acessar o Azure Data Lake Store com a identidade do serviço gerenciado da VM Linux](tutorial-linux-vm-access-datalake.md) |
|                    | [Acessar o Azure Resource Manager com uma Identidade de Serviço Gerenciado de VM do Linux](tutorial-linux-vm-access-arm.md) |
|                    | [Acessar o Armazenamento do Azure por meio de uma chave de acesso com uma Identidade do Serviço Gerenciado de VM do Linux](tutorial-linux-vm-access-storage.md) |
|                    | [Acessar o Armazenamento do Azure por meio do SAS com uma Identidade do Serviço Gerenciado de VM do Linux](tutorial-linux-vm-access-storage-sas.md) |
|                    | [Acessar um recurso que não é do Azure AD com uma Identidade de Serviço Gerenciado de VM do Linux e um Azure Key Vault](tutorial-linux-vm-access-nonaad.md) |
| Serviço de aplicativo do Azure  | [Use a identidade de Serviço Gerenciado do Usuário com o Serviço de Aplicativo do Azure ou o Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Funções do Azure    | [Use a identidade de Serviço Gerenciado do Usuário com o Serviço de Aplicativo do Azure ou o Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Barramento de Serviço do Azure  | [Usar Identidade de Serviço Gerenciado com o Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-managed-service-identity.md) |
| Hubs de eventos do Azure   | [Usar Identidade de Serviço Gerenciada com Hubs de Eventos do Azure](../../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Os serviços do Azure oferecem suporte à Identidade de Serviço Gerenciado?

Serviços do Azure que oferecem suporte à Identidade de Serviço Gerenciado podem usar o MSI para autenticar para serviços com suporte à autenticação do Azure AD.  Estamos no processo de integração da autenticação do MSI e do Azure AD no Azure.  Verifique regularmente as atualizações.

### <a name="azure-services-that-support-managed-service-identity"></a>Serviços do Azure que oferecem suporte à Identidade de Serviço Gerenciado

Os seguintes serviços do Azure oferecem suporte à Identidade de Serviço Gerenciado.

| Serviço | Status | Data | Configurar | Obter um token |
| ------- | ------ | ---- | --------- | ----------- |
| Máquinas Virtuais do Azure | Visualização | Setembro de 2017 | [Portal do Azure](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[CLI do Azure](qs-configure-cli-windows-vm.md)<br>[Modelos do Gerenciador de Recursos do Azure](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Serviço de aplicativo do Azure | Visualização | Setembro de 2017 | [Portal do Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modelo do Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions<sup>1</sup> | Visualização | Setembro de 2017 | [Portal do Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modelo do Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Visualização | Novembro de 2017 | [Portal do Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

<sup>1</sup> O Azure Functions permite que o código de usuário use uma identidade, mas gatilhos e associações ainda podem exigir cadeias de conexão.

### <a name="azure-services-that-support-azure-ad-authentication"></a>Serviços do Azure que suportam a autenticação do Azure AD

Os seguintes serviços dão suporte à autenticação do Azure AD e foram testados com serviços de cliente que usam a Identidade de Serviço Gerenciado.

| Serviço | ID de Recurso | Status | Data | Atribuir acesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Gerenciador de Recursos do Azure | https://management.azure.com | Disponível | Setembro de 2017 | [Portal do Azure](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[CLI do Azure](howto-assign-access-CLI.md) |
| Cofre da Chave do Azure | https://vault.azure.net | Disponível | Setembro de 2017 | |
| Azure Data Lake | https://datalake.azure.net | Disponível | Setembro de 2017 | |
| SQL do Azure | https://database.windows.net | Disponível | Outubro de 2017 | |
| Hubs de eventos do Azure | https://eventhubs.azure.net | Disponível | Dezembro de 2017 | |
| Barramento de Serviço do Azure | https://servicebus.azure.net | Disponível | Dezembro de 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Quanto custa a Identidade de Serviço Gerenciado?

A Identidade de Serviço Gerenciado vem com o Azure Active Directory Gratuito, que é o padrão para as assinaturas do Azure.  Não há nenhum custo adicional para a Identidade de Serviço Gerenciado.

## <a name="support-and-feedback"></a>Suporte e comentários

Adoraríamos ouvir o que você tem para nos dizer!

* Faça perguntas sobre instruções no Stack Overflow com a marca [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Faça solicitações de recursos ou deixe comentários no [Fórum de comentários do Azure AD para desenvolvedores](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






