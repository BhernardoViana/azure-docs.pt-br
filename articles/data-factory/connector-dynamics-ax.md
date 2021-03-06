---
title: Copiar dados do Dynamics AX
description: Saiba como copiar dados do Dynamics AX para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 2a2debf9b1cbc669d2402b1797097b97e94139fc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929470"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Copiar dados do Dynamics AX usando o Azure Data Factory

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados de uma fonte Dynamics AX. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector do Dynamics AX tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Dynamics AX para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Dynamics AX é compatível com à cópia de dados do Dynamics AX usando o **protocolo OData** com **Autenticação de Entidade de serviço**.

>[!TIP]
>Você também pode usar esse conector para copiar dados do **Dynamics 365 Finance and Operations**. Consulte o [suporte OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) e o [método de autenticação](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication) do Dynamic 365.

## <a name="get-started"></a>Comece agora

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory específicas do conector Dynamics AX.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a autenticação de entidade de serviço, siga estas etapas:

1. Registre uma entidade de aplicativo no Azure Active Directory (Azure AD) seguindo [Registre o aplicativo com um locatário do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Vá para o Dynamics AX e conceda essa permissão adequada de entidade de serviço para acessar o Dynamics AX.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Dynamics AX:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade **tipo** deve ser definida como: **DynamicsAX**. |SIM |
| URL | Ponto de extremidade OData da instância do Dynamics AX (ou Dynamics 365 Finance and Operations). |SIM |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | SIM |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque esse campo como  **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | SIM |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Para recuperá-lo, passe o mouse no canto superior direito do portal do Azure. | SIM |
| aadResourceId | Especifique o recurso do AAD ao qual você está solicitando autorização. Por exemplo, se a URL do Dynamics é `https://sampledynamics.sandbox.operations.dynamics.com/data/`, o recurso do AAD correspondente é geralmente `https://sampledynamics.sandbox.operations.dynamics.com`. | SIM |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode escolher o Azure Integration Runtime ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não especificado, o Azure Integration Runtime padrão será usado. |Não |

**Exemplo**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Esta seção fornece uma lista de propriedades compatíveis com o conjunto de dados Dynamics AX.

Para obter uma lista completa de seções e propriedades disponíveis para definição de conjuntos de dados, consulte [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md). 

Para copiar dados do Dynamics AX, defina a propriedade **tipo** do conjunto de dados como **DynamicsAXResource**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade **tipo** do conjunto de dados deve ser definida como **DynamicsAXResource**. | SIM |
| caminho | Caminho para a entidade OData do Dynamics AX. | SIM |

**Exemplo**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista de propriedades compatíveis com a fonte Dynamics AX.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX como fonte

Para copiar dados do Dynamics AX, defina o **tipo** de fonte em Copiar atividade para **DynamicsAXSource**. As seguintes propriedades são suportadas na seção **source** da atividade de cópia:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade **tipo** da fonte Copiar atividade deve ser definida como **DynamicsAXSource**. | SIM |
| query | Opções de consulta OData para filtrar dados. Exemplo: `"?$select=Name,Description&$top=5"`.<br/><br/>**Observação**: o conector copia os dados da URL combinada: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Para saber mais, confira as [Componentes da URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

**Exemplo**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximos passos

Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores no Azure Data Factory, consulte [Armazenamentos e formatos de dados compatíveis](copy-activity-overview.md##supported-data-stores-and-formats).
