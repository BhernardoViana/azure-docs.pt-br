---
title: Copiar dados de/para o Azure Data Lake Storage Gen1 usando o Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Azure Data Lake Store ou do Data Lake Store para armazenamentos de coletor com suporte, usando o Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: jingwang
ms.openlocfilehash: d500bc9c910858341d7fdacb4d85bffc8be215e1
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338755"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Copiar dados de/para o Azure Data Lake Storage Gen1 usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-datalake-connector.md)
> * [Versão atual](connector-azure-data-lake-store.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de/para o Azure Data Lake Storage Gen1 (conhecido anteriormente como Azure Data Lake Store). Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Data Lake Store ou copiar dados do Azure Data Lake Store para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Azure Data Lake Store dá suporte à:

- Copiar arquivos usando **entidade de serviço** ou autenticação de **identidade de serviço gerenciada** (MSI).
- Cópia de arquivos no estado em que se encontram ou análise/geração de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Se você copiar dados usando o Integration Runtime Auto-hospedado, configure o firewall corporativo para permitir o tráfego de saída para `<ADLS account name>.azuredatalakestore.net` e `login.microsoftonline.com/<tenant>/oauth2/token` na porta 443. O último é o Serviço de Segurança de Token do Azure (STS) com o qual o IR precisa se comunicar para obter o token de acesso.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter um passo a passo de como usar o conector do Azure Data Lake Store, consulte [Carregar dados no Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

>[!NOTE]
>Ao utilizar a Ferramenta Copiar Dados para criar um pipeline de cópia ou usar a interface do usuário do ADF para executar testes de pastas de navegação/conexão durante a criação, a permissão da entidade de serviço ou MSI no nível raiz será exigida. A execução da atividade de cópia poderá funcionar, desde que a permissão seja concedida aos dados a serem copiados. Você pode ignorar as operações de criação se precisar limitar a permissão.

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Data Lake Store:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como **AzureDataLakeStore**. | SIM |
| dataLakeStoreUri | Informações sobre a conta do Azure Data Lake Store. Essas informações usam um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | SIM |
| subscriptionId | ID de assinatura do Azure à qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| resourceGroupName | Nome do grupo de recursos do Azure ao qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

Consulte as seções a seguir para mais propriedades e amostras JSON para diferentes tipos de autenticação, respectivamente:

- [Usar a autenticação de entidade de serviço](#using-service-principal-authentication)
- [Usar autenticação de identidade do serviço gerenciado](#using-managed-service-identity-authentication)

### <a name="using-service-principal-authentication"></a>Usar a autenticação de entidade de serviço

Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure AD (Azure Active Directory) e conceda a ela o acesso ao Data Lake Store. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:

- ID do aplicativo
- Chave do aplicativo
- ID do locatário

>[!IMPORTANT]
> Verifique se você concedeu a permissão apropriada à entidade de serviço no Azure Data Lake Store:
>- **Como fonte**, em Data Explorer -> Acesso, conceda pelo menos a permissão **Leitura + Execução** para listar e copiar os arquivos em pastas/subpastas, ou permissão de **Leitura** para copiar um único arquivo, e escolha adicionar a **Esta pasta e todos os filhos** para recursivo e adicione como **uma permissão de acesso e uma entrada de permissão padrão**. Nenhum requisito de controle de acesso no nível da conta (IAM).
>- **Como coletor**, no Data Explorer -> Acesso, conceda pelo menos acesso **Gravação + Execução** para criar itens filhos na pasta e escolha adicionar a **Esta pasta e todos os filhos** para recursivo e adicione como **uma permissão de acesso e uma entrada de permissão padrão**. Se você usar o Azure IR para copiar (tanto a origem quanto o coletor que estão na nuvem) no controle de acesso (IAM), conceda pelo menos a função **Leitor** para permitir que o Data Factory detecte a região da Data Lake Store. Se você quiser evitar essa função IAM, explicitamente [crie um Azure IR](create-azure-integration-runtime.md#create-azure-ir) com o local do Data Lake Store e associe-o no serviço vinculado do Data Lake Store como no exemplo a seguir.

Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | SIM |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | SIM |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | SIM |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-managed-service-identity-authentication"></a>Usar autenticação de identidade do serviço gerenciado

Uma data factory pode ser associada a uma [identidade de serviço gerenciada](data-factory-service-identity.md), que representa a data factory específica. Você pode usar essa identidade de serviço diretamente para a autenticação do Data Lake Store da mesma maneira que no uso de sua própria entidade de serviço. Ele permite que este alocador designado acesse e copie dados de/para o seu Data Lake Store.

Para usar a autenticação de identidade do serviço gerenciado (MSI):

1. [Recuperar a identidade do serviço de data factory](data-factory-service-identity.md#retrieve-service-identity) copiando o valor de "SERVIÇO DE IDENTIDADE ID DO APLICATIVO" gerado junto com seu alocador.
2. Conceda o acesso de identidade do serviço do Data Lake Store da mesma maneira que faria para a entidade de serviço abaixo das notas.

>[!IMPORTANT]
> Verifique se você concedeu a permissão apropriada à identidade de serviço do data factory no Azure Data Lake Store:
>- **Como fonte**, em Data Explorer -> Acesso, conceda pelo menos a permissão **Leitura + Execução** para listar e copiar os arquivos em pastas/subpastas, ou permissão de **Leitura** para copiar um único arquivo, e escolha adicionar a **Esta pasta e todos os filhos** para recursivo e adicione como **uma permissão de acesso e uma entrada de permissão padrão**. Nenhum requisito de controle de acesso no nível da conta (IAM).
>- **Como coletor**, no Data Explorer -> Acesso, conceda pelo menos acesso **Gravação + Execução** para criar itens filhos na pasta e escolha adicionar a **Esta pasta e todos os filhos** para recursivo e adicione como **uma permissão de acesso e uma entrada de permissão padrão**. Se você usar o Azure IR para copiar (tanto a origem quanto o coletor que estão na nuvem) no controle de acesso (IAM), conceda pelo menos a função **Leitor** para permitir que o Data Factory detecte a região da Data Lake Store. Se você quiser evitar essa função IAM, explicitamente [crie um Azure IR](create-azure-integration-runtime.md#create-azure-ir) com o local do Data Lake Store e associe-o no serviço vinculado do Data Lake Store como no exemplo a seguir.

No Azure Data Factory, você não precisa especificar nenhuma propriedade além das informações do Data Lake Store gerais no serviço vinculado.

**Exemplo:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Azure Data Lake Store.

Para copiar dados para/do Azure Data Lake Store, defina a propriedade type do conjunto de dados como **AzureDataLakeStoreFile**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como: **AzureDataLakeStoreFile** |SIM |
| folderPath | Caminho para a pasta no Data Lake Store. O filtro curinga não é suportado. Exemplo: rootfolder/subfolder/ |SIM |
| fileName | **Filtro de nome ou curinga** para os arquivos em "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para se seu nome de arquivo real curinga ou esse caractere de escape dentro de escape.<br/><br/>Quando fileName não é especificado para um dataset de saída e **preserveHierarchy** ão é especificado no coletor de atividade, a atividade de cópia gera automaticamente o nome do arquivo com o seguinte formato: "*Data. [Activity run id GUID]. [GUID se FlattenHierarchy]. [Formato se configurado]. [Compressão se configurado]*". Um exemplo é "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". |Não  |
| formato | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, haverá suporte para os seguintes tipos de formatos de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis com suporte são **Ideal** e **O mais rápido**. |Não  |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um determinado nome, especifique **folderPath** com parte da pasta e **fileName** com nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro curinga. 

**Exemplo:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "myfile.csv.gz",
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

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store como fonte

Para copiar dados do Azure Data Lake Store, defina o tipo de fonte na atividade de cópia como **AzureDataLakeStoreSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **AzureDataLakeStoreSource** |SIM |
| recursiva | Indica se os dados são lidos recursivamente a partir das subpastas ou somente da pasta especificada. Observe que quando o recursivo estiver definido como verdadeiro e o coletor for um armazenamento baseado em arquivo, subpasta/pasta vazia não será copiada/criada no coletor.<br/>Os valores permitidos são: **true** (padrão), **false** | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store como coletor

Para copiar dados para o Azure Data Lake Store, defina o tipo de coletor na atividade de cópia como **AzureDataLakeStoreSink**. As propriedades a seguir têm suporte na seção **coletor**:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do coletor da atividade de cópia deve ser definida como: **AzureDataLakeStoreSink** |SIM |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm o nome gerado automaticamente. <br/><b>- MergeFiles</b>: mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, será o nome de arquivo gerado automaticamente. | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="recursive-and-copybehavior-examples"></a>exemplos de recursive e copyBehavior

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

| recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| verdadeiro |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| verdadeiro |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| verdadeiro |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos dos Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um único arquivo com um nome de arquivo gerado automaticamente |
| falso |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| falso |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| falso |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos dos Arquivo1 + Arquivo2 são mesclados em um único arquivo com um nome de arquivo gerado automaticamente. Nome gerado automaticamente para o Arquivo1<br/><br/>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados. |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
