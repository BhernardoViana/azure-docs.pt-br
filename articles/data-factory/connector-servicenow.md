---
title: Copiar dados do ServiceNow usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do ServiceNow para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 234b78a97c2663121d0d585154695887a58b9522
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203407"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Copiar dados do ServiceNow usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do ServiceNow. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do ServiceNow para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, não é necessário instalar manualmente qualquer driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do ServiceNow.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do ServiceNow:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **ServiceNow** | Sim |
| endpoint | O endpoint do servidor do ServiceNow (`http://<instance>.service-now.com`).  | Sim |
| authenticationType | O tipo de autenticação a ser usado. <br/>Valores permitidos são: **Básica**, **OAuth2** | Sim |
| Nome de Usuário | O nome de usuário usado para se conectar ao servidor ServiceNow para autenticação básica e OAuth2.  | Sim |
| Senha | A senha correspondente ao nome de usuário para autenticação básica e OAuth2. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| clientId | A ID do cliente para autenticação OAuth2.  | Não  |
| clientSecret | O segredo do cliente para autenticação OAuth2. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não  |
| useEncryptedEndpoints | Especifica se os endpoints de fonte de dados são criptografados usando HTTPS. O valor padrão é true.  | Não  |
| useHostVerification | Especifica se é necessário o nome do host no certificado do servidor para corresponder ao nome de host do servidor ao se conectar via SSL. O valor padrão é true.  | Não  |
| usePeerVerification | Especifica se deve verificar a identidade do servidor quando se conecta por meio de SSL. O valor padrão é true.  | Não  |

**Exemplo:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do ServiceNow.

Para copiar dados do ServiceNow, defina a propriedade type do conjunto de dados como **ServiceNowObject**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **ServiceNowObject** | Sim |
| tableName | Nome da tabela. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem do ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow como origem

Para copiar dados do ServiceNow, defina o tipo de origem na atividade de cópia como **ServiceNowSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **ServiceNowSource** | Sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Actual.alm_asset"`. | Não (se "tableName" no conjunto de dados for especificado) |

Observe o seguinte ao especificar o esquema e a coluna para ServiceNow em consulta, e **consultem [dicas de desempenho](#performance-tips) em implicação de desempenho de cópia**.

- **Esquema:** especifique o esquema como `Actual` ou `Display` na consulta do ServiceNow, que pode ser visto como o parâmetro `sysparm_display_value` como verdadeiro ou falso ao chamar [APIs restful do ServiceNow](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Coluna:** o nome da coluna para o valor real no esquema `Actual` é `[column name]_value`, e para o valor de exibição no esquema `Display` é `[column name]_display_value`. Observe que o nome da coluna precisa ser mapeado para o esquema que está sendo usado na consulta.

**Exemplo de consulta:**
`SELECT col_value FROM Actual.alm_asset` OU 
`SELECT col_display_value FROM Display.alm_asset`

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Dicas de desempenho

### <a name="schema-to-use"></a>Esquema a ser usado

O ServiceNow possui 2 esquemas diferentes, um é **"Real"** que retorna dados reais, o outro é **"Display"**, que retorna os valores de exibição dos dados. 

Se você tiver um filtro em sua consulta, use o esquema "Real" que tenha melhor desempenho de cópia. Ao consultar o esquema "Real", o ServiceNow suporta de forma nativa o filtro ao buscar os dados para retornar apenas o conjunto de resultados filtrado, enquanto ao consultar o esquema "Exibir", o ADF recupera todos os dados e aplica o filtro internamente.

### <a name="index"></a>Índice

Índice da tabela de ServiceNow pode ajudar a melhorar o desempenho de consulta, consulte [criar um índice de tabela](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="next-steps"></a>Próximos passos
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
