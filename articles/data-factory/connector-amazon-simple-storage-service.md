---
title: Copiar dados do Amazon Simple Storage Service usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como copiar dados do Amazon S3 (Simple Storage Service) para armazenamentos de dados de coletor com suporte por meio do Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 82d46d29b1e75995c5436b985717f45104dad955
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Copiar dados do Amazon Simple Storage Service usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Versão 2 – Versão prévia](connector-amazon-simple-storage-service.md)

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados de e para o Armazenamento de Blobs do Azure. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector do Amazon S3 na V1](v1/data-factory-amazon-simple-storage-service-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Data Lake Store ou copiar dados do Azure Data Lake Store para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Amazon S3 dá suporte à cópia de arquivos no estado em que se encontram ou à análise de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

## <a name="required-permissions"></a>Permissões necessárias

Para copiar dados do Amazon S3, verifique se você recebeu as permissões a seguir:

- `s3:GetObject` e `s3:GetObjectVersion` para operações de objeto do Amazon S3
- `s3:ListBucket` ou `s3:GetBucketLocation` para operações de bucket do Amazon S3. Se você estiver usando o assistente de cópia Data Factory, `s3:ListAllMyBuckets` também será exigido.

Parra detalhes sobre a lista completa e detalhada das permissões da Amazon S3,consulte [Especificar permissões em uma política](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As seções seguintes fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Amazon S3.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Amazon S3:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **AmazonS3**. | sim |
| accessKeyId | ID da chave de acesso secreta. |sim |
| secretAccessKey | A chave de acesso do secreta em si. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

>[!NOTE]
>Este conector requer chaves de acesso da conta IAM para copiar dados do Amazon S3. [Não há suporte para a credencial de segurança temporária](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html).
>

Veja um exemplo:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                    "type": "SecureString",
                    "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Amazon S3.

Para copiar dados do Amazon S3, defina a propriedade type do conjunto de dados como **AmazonS3Object**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como: **AmazonS3Object** |sim |
| bucketName | O nome do bucket S3. |sim |
| chave | A chave do objeto S3. Aplica-se somente quando o prefixo não é especificado. |Não  |
| prefixo | Prefixo da chave do objeto S3. Objetos cujas chaves começam com esse prefixo serão selecionados. Aplica-se somente quando a chave não é especificada. |Não  |
| version | A versão do objeto S3 se o controle de versão do S3 está habilitado. |Não  |
| formato | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, haverá suporte para os seguintes tipos de formatos de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis com suporte são **Ideal** e **O mais rápido**. |Não  |

> [!NOTE]
> **bucketName + chave** especifica a localização do objeto S3 em que bucket é o contêiner raiz para objetos S3 e a chave é o caminho completo para o objeto S3.

**Exemplo: usando o prefixo**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Exemplo: usando a chave e a versão (opcional)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor do Azure Data Lake.

### <a name="amazon-s3-as-source"></a>Amazon S3 como fonte

Para copiar dados do Amazon S3, defina o tipo de fonte na atividade de cópia como **FileSystemSource** (que inclui o Amazon S3). As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **FileSystemSource** |sim |
| recursiva | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando o recursivo estiver definido como verdadeiro e o coletor for um armazenamento baseado em arquivo, subpasta/pasta vazia não será copiada/criada no coletor.<br/>Os valores permitidos são: **true** (padrão), **false** | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).