---
title: API de preenchimento SaaS V2 | O Azure Marketplace
description: Explica como criar uma oferta de SaaS no AppSource e Azure Marketplace usando o preenchimento associado APIs V2.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pabutler
ms.openlocfilehash: 4efd9556e255709204654cf0acbf1b08fa2c1fc0
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872149"
---
# <a name="saas-fulfillment-apis-version-2"></a>Versão de APIs de preenchimento SaaS 2 

Este artigo detalha a API que permite que fornecedores de software independentes (ISVs) para vender seus aplicativos SaaS no Azure Marketplace e AppSource. Essa API é que um requisito para SaaS transacionáveis oferece no Azure Marketplace e AppSource.

## <a name="managing-the-saas-subscription-lifecycle"></a>Gerenciar o ciclo de vida de assinatura de SaaS

Microsoft SaaS Service gerencia o ciclo de vida de uma compra de assinatura de SaaS e usa a API de preenchimento de um mecanismo para direcionar o cumprimento real, as alterações aos planos e a exclusão da assinatura com o ISV. O cliente será cobrado de acordo com o estado da assinatura de SaaS que a Microsoft mantém. O diagrama a seguir descreve os estados e as operações que orientam as alterações entre estados.

![Estados de ciclo de vida de assinatura de SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Estados de uma assinatura de SaaS

A tabela a seguir lista os estados de provisionamento para uma assinatura de SaaS, incluindo um diagrama de sequência e a descrição para cada (se aplicável). 

#### <a name="provisioning"></a>Provisionamento

Quando um cliente inicia uma compra, o ISV recebe essas informações em um código de autenticação em uma página de web interativos de cliente usando um parâmetro de URL. Por exemplo: `https://contoso.com/signup?token=..`, em que o provedor de URL de página de aterrissagem no partner center é `https://contoso.com/signup`. O código de autenticação pode ser validado e trocado para obter os detalhes do que precisa ser provisionado chamando a API de resolver.  Quando o serviço de SaaS termina de provisionamento, ele envia uma chamada de ativar para sinalizar que o cumprimento é concluído e o cliente pode ser cobrado.  O diagrama a seguir mostra a sequência de chamadas à API para um cenário de provisionamento.  

![Chamadas à API para provisionar um serviço SaaS.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Provisionado

Esse estado é o estado estável de um serviço provisionado.

#### <a name="provisioning-for-update"></a>O provisionamento de atualização
(a partir do marketplace) 

Esse estado representa uma atualização de um existente serviço está pendente. Essa atualização pode ser iniciada pelo cliente no marketplace ou no serviço de SaaS (somente para direto para transações do cliente). O diagrama a seguir mostra as ações quando uma atualização é iniciada do marketplace.

![Chamadas à API quando a atualização é iniciada do marketplace.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>O provisionamento de atualização  
(no serviço de SaaS) 

O diagrama a seguir mostra as ações quando uma atualização é iniciada pelo serviço de SaaS. (A chamada do webhook é substituída por uma atualização para a assinatura iniciada pelo Service SaaS. 

![Chamadas à API quando a atualização é iniciada pelo serviço de SaaS.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspenso

Este estado indica que o pagamento do cliente ainda não foram recebido. Por diretiva, forneceremos o cliente um período de carência antes unfulfilling a assinatura. Quando uma assinatura estiver nesse estado: 

- Como um ISV, você pode optar por diminuir ou bloquear o acesso do usuário para o serviço. 
- A assinatura deve ser mantida em um estado recuperável que pode restaurar a funcionalidade completa sem qualquer perda de dados ou configurações. 
- Você pode esperar obter uma solicitação de restabelecimento para esta assinatura por meio do preenchimento de API ou uma solicitação de desprovisionamento no final do período de cortesia. 

#### <a name="unsubscribed"></a>Assinatura cancelada 

Assinaturas atinjam esse estado em resposta a uma solicitação de cliente explícita ou como uma resposta ao não pagamento de dívidas. A expectativa do ISV é que os dados do cliente é retidos para recuperação de solicitação por um mínimo de X dias e, em seguida, excluídos. 


## <a name="api-reference"></a>Referência da API

Esta seção documenta o SaaS *API de assinatura* e *operações API*.  O valor de `api-version` APIs de parâmetro para a versão 2 é `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definições de parâmetro e de entidade

A tabela a seguir lista as definições de entidades usadas pelas APIs de preenchimento e parâmetros comuns.

|     Entidade/parâmetro     |     Definição                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identificador GUID de um recurso de SaaS  |
| `name`                   | Nome amigável fornecido para este recurso pelo cliente |
| `publisherId`            | Identificador de cadeia de caracteres exclusiva para cada editor, por exemplo, "contoso" |
| `offerId`                | Identificador de cadeia de caracteres exclusiva para cada oferta, por exemplo "offer1"  |
| `planId`                 | Identificador de cadeia de caracteres exclusiva para cada plano/sku, por exemplo "silver" |
| `operationId`            | Identificador GUID de uma determinada operação  |
|  `action`                | A ação que está sendo executada em um recurso, ou `subscribe`, `unsubscribe`, `suspend`, `reinstate`, ou `changePlan`, `changeQuantity`, `transfer`  |
|   |   |

Identificadores globais exclusivos ([GUIDs](https://en.wikipedia.org/wiki/Universally_unique_identifier)) são números (hexadecimal de 32) de 128 bits que normalmente são gerados automaticamente. 

#### <a name="resolve-a-subscription"></a>Resolver uma assinatura 

O ponto de extremidade de resolução permite que o Editor resolver um token do marketplace para uma ID de recurso persistentes. A ID de recurso é o identificador exclusivo para a assinatura de SAAS.  Quando um usuário é redirecionado para um site do ISV, a URL contém um token nos parâmetros de consulta. O ISV deve usar esse token e fazer uma solicitação para resolvê-lo. A resposta contém a ID de assinatura exclusiva de SAAS, nome, ID de oferta e o plano para o recurso. Esse token é válido por apenas uma hora. 

**Postar:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versão da operação a ser usado para esta solicitação  |

*Cabeçalhos de solicitação:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de Conteúdo      | `application/json` |
|  x-ms-requestid    |  Valor de cadeia de caracteres exclusiva para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  autorização     |  [Obter o token de portador do JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  Parâmetro de consulta de token na URL quando o usuário é redirecionado ao site do ISV SaaS do Azure (para ex: `https://contoso.com/signup?token=..`). *Observação:* A URL decodifica o valor do token do navegador antes de usá-lo.  |

*Códigos de resposta:*

Código: 200<br>
Resolve o token opaco para uma assinatura de SaaS.<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Código: 404<br>
Não encontrado

Código: 400<br>
Solicitação inválida. x-ms-marketplace-token estiver ausente, malformadas ou expiradas.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual.

Código: 500<br>
Erro interno do servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API de assinatura

A API de assinatura suporta as seguintes operações de HTTPS: **Obtenha**, **Post**, **Patch**, e **excluir**.


#### <a name="list-subscriptions"></a>Listar assinaturas

Lista todas as assinaturas para um publicador do SaaS.

**Get:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  A versão da operação a ser usada para esta solicitação.  |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
| Tipo de Conteúdo       |  `application/json`  |
| x-ms-requestid     |  Valor de cadeia de caracteres exclusiva para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| x-ms-correlationid |  Valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| autorização      |  [Obter o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Códigos de resposta:*

Código: 200 <br/>
Com base no token de autenticação, obtenha o publicador e o correspondentes assinaturas para ofertas de todas as do Editor.<br> Carga de resposta:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

O token de continuação estarão presente somente se houver adicionais "páginas" de planos para recuperar. 

Código: 403 <br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual. 

Código: 500 Erro Interno do Servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Obter assinatura

Obtém a assinatura especificada de SaaS. Use essa chamada para obter informações de licença e informações do plano.

**Get:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Identificador exclusivo da assinatura de SaaS que é obtido depois de resolver o token por meio da API de resolver   |
|  ApiVersion        |   Versão da operação a ser usado para esta solicitação   |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de Conteúdo      |  `application/json`  |
|  x-ms-requestid    |  Valor de cadeia de caracteres exclusiva para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  autorização     |  [Obter o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Códigos de resposta:*

Código: 200<br>
Obtém a assinatura de SaaS do identificador<br> Carga de resposta:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Código: 404<br>
Não encontrado<br> 

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual.

Código: 500<br>
Erro interno do servidor<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Lista de planos disponíveis

Use essa chamada para descobrir se há qualquer oferta privada/pública para o publicador atual.

**Get:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Versão da operação a ser usado para esta solicitação  |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de Conteúdo     |  `application/json` |
|   x-ms-requestid   |   Valor de cadeia de caracteres exclusiva para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid  | Valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  autorização     |  [Obter o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Códigos de resposta:*

Código: 200<br>
Obter uma lista de planos disponíveis para um cliente.<br>

Corpo da resposta:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Código: 404<br>
Não encontrado<br> 

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual. <br> 

Código: 500<br>
Erro interno do servidor<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Ativar uma assinatura

**Postar:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versão da operação a ser usado para esta solicitação  |
| subscriptionId     | Identificador exclusivo da assinatura do SaaS é obtido depois de resolver o token usando a API de resolver  |

*Cabeçalhos de solicitação:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de Conteúdo      | `application/json`  |
|  x-ms-requestid    | Valor de cadeia de caracteres exclusiva para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  | Valor de cadeia de caracteres exclusiva para a operação no cliente. Essa cadeia de caracteres se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  autorização     |  [Obter o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Solicitação:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Códigos de resposta:*

Código: 200<br>
Ativa a assinatura.<br>

Código: 404<br>
Não encontrado

Código: 400<br>
Falha na validação de solicitação incorreta

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual.

Código: 500<br>
Erro interno do servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Alterar o plano de assinatura

Atualize o plano na assinatura.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Identificador exclusivo da assinatura do SaaS é obtido depois de resolver o token usando a API de resolver.  |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de Conteúdo      | `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.    |
| autorização      |  [Obter o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Carga de solicitação:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Vincular a um recurso para obter o status da operação.   |

*Códigos de resposta:*

Código: 202<br>
A solicitação de alteração de plano foi aceita. O ISV deve sondar a operação de localização para determinar um êxito/falha. <br>

Código: 404<br>
Não encontrado

Código: 400<br>
Falhas de validação de solicitação incorreta.

>[!Note]
>Apenas um plano ou quantidade pode ser corrigida ao mesmo tempo, não ambos. Edita em uma assinatura com **atualização** não está em `allowedCustomerOperations`.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual.

Código: 500<br>
Erro interno do servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>Alterar a quantidade da assinatura

Atualize a quantidade na assinatura.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Identificador exclusivo da assinatura do SaaS é obtido depois de resolver o token usando a API de resolver.  |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de Conteúdo      | `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.    |
| autorização      |  [Obter o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Carga de solicitação:*

```json
Request Body:
{
    "quantity": 5
}
```

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Vincular a um recurso para obter o status da operação.   |

*Códigos de resposta:*

Código: 202<br>
Aceita. A solicitação para alterar a quantidade foi aceita. O ISV deve sondar a operação de localização para determinar um êxito/falha. <br>

Código: 404<br>
Não encontrado

Código: 400<br>
Falhas de validação de solicitação incorreta.

>[!Note]
>Apenas um plano ou quantidade pode ser corrigida ao mesmo tempo, não ambos. Edita em uma assinatura com **atualização** não está em `allowedCustomerOperations`.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual.

Código: 500<br>
Erro interno do servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>Excluir uma assinatura

Cancelar a assinatura e exclua a assinatura especificada.

**Delete:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Identificador exclusivo da assinatura do SaaS é obtido depois de resolver o token usando a API de resolver.  |

*Cabeçalhos de solicitação:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de Conteúdo     |  `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
|  autorização     |  [Obter o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Códigos de resposta:*

Código: 202<br>
Chamada de ISV iniciada para indicar que cancelar a assinatura em uma assinatura de SaaS.<br>

Código: 404<br>
Não encontrado

Código: 400<br>
Excluir em uma assinatura com **exclua** não está no `allowedCustomerOperations`.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual.

Código: 500<br>
Erro interno do servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Operações de API

A API de operações suporta as seguintes operações de Patch e Get.

#### <a name="list-outstanding-operations"></a>Lista de operações pendentes 

Lista as operações pendentes para o publicador atual. 

**Get:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   A versão da operação a ser usada para esta solicitação.                |
| subscriptionId     | Identificador exclusivo da assinatura do SaaS é obtido depois de resolver o token usando a API de resolver.  |

*Cabeçalhos de solicitação:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de Conteúdo     |  `application/json` |
|  x-ms-requestid    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  autorização     |  [Obter o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Códigos de resposta:*

Código: 200<br> Obtém a lista de operações em uma assinatura pendentes.<br>
Carga de resposta:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```

Código: 404<br>
Não encontrado

Código: 400<br>
Falha na validação de solicitação incorreta

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual.

Código: 500<br>
Erro interno do servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Obter status da operação

Permite que o Editor acompanhar o status da operação especificada assíncrona disparadas (assinar / cancelar a assinatura / alterar plano / alterar quantidade).

**Get:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de Conteúdo      |  `application/json`   |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  autorização     |[Obter o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Códigos de resposta:* Código: 200<br> Obtém especificado SaaS operação pendente<br>
Carga de resposta:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Código: 404<br>
Não encontrado

Código: 400<br>
Falha na validação de solicitação incorreta

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual.
 
Código: 500<br> Erro interno do servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>O status de uma operação de atualização

Atualize o status de uma operação para indicar êxito/falha com os valores fornecidos.

**Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Identificador exclusivo da assinatura do SaaS é obtido depois de resolver o token usando a API de resolver.  |
|  operationId       | A operação que está sendo concluída. |

*Cabeçalhos de solicitação:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de Conteúdo     | `application/json`   |
|   x-ms-requestid   |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse parâmetro se correlaciona a todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  autorização     |  [Obter o token de portador do JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Carga de solicitação:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Códigos de resposta:*

Código: 200<br> Chamada para informar de conclusão de uma operação no lado do ISV. Por exemplo, essa resposta pode sinalizar a alteração de estações/planos.

Código: 404<br>
Não encontrado

Código: 400<br>
Falha na validação de solicitação incorreta

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido, é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence do publicador atual.

Código: 409<br>
Conflito. Por exemplo, uma transação mais recente já é atendida

Código: 500<br> Erro interno do servidor

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>Webhook no serviço de SaaS

O publicador deve implementar um webhook neste serviço de SaaS para notificar os usuários das alterações no seu serviço. A API deve ser autenticado e será chamada pelo serviço de SaaS da Microsoft. O serviço de SaaS deve chamar as operações API para validar e autorizar antes de realizar uma ação na notificação de webhook.

```json
{
    "operationId": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Subscribe",
    "timeStamp": "2018-12-01T00:00:00"
}
```

Onde a ação pode ser um destes procedimentos: 
- `Subscribe`  (Quando o recurso tiver sido ativado)
- `Unsubscribe` (Quando o recurso foi excluído)
- `ChangePlan` (Quando estiver concluída, a operação de alteração de plano)
- `ChangeQuantity` (Quando estiver concluída, a operação de quantidade de alteração)
- `Suspend` (Quando o recurso foi suspenso)
- `Reinstate` (Quando recursos foi restabelecido após suspensão)


## <a name="mock-api"></a>API de simulação

Você pode usar nossas APIs fictícios para ajudá-lo a começar com o desenvolvimento, especialmente criação de protótipos, e projetos de teste. 

Ponto de extremidade do host: `https://marketplaceapi.microsoft.com/api` <br/>
Versão da API: `2018-09-15` <br/>
Nenhuma autenticação necessária <br/>
Uri de exemplo: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Os caminhos de ponto de extremidade de API são os mesmos em simulação e APIs Real, mas as versões de API são diferentes. A versão é 2018-09-15 para simulação e 2018-08-31 para a versão de produção. 

Qualquer uma das chamadas à API neste artigo podem ser feitas para o ponto de extremidade do host fictício. Você pode esperar obter os dados fictícios, como uma resposta. Em geral, você pode esperar obter os dados fictícios, como uma resposta. Chamadas para os métodos de assinatura de atualização sobre a API de simulação sempre retornam 500. 

## <a name="next-steps"></a>Próximas etapas

Os desenvolvedores também programaticamente podem recuperar e manipulação de cargas de trabalho, ofertas e publisher perfis usando o [as APIs REST do Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
