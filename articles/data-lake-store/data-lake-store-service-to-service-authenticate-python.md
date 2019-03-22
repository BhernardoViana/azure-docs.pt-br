---
title: 'Autenticação serviço a serviço: Python com Gen1 de armazenamento do Azure Data Lake usando o Active Directory do Azure | Microsoft Docs'
description: Saiba como obter a autenticação de serviço a serviço com o Azure Data Lake Storage Gen1 usando o Azure Active Directory usando Python
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 4cad09877a3fc4f586db2d92ce97a64b6efe295e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537143"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autenticação de serviço a serviço com Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [Usando o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Usar o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Usando o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Usar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Neste artigo, você aprenderá como usar o SDK do Python para fazer a autenticação de serviço a serviço com Azure Data Lake Storage Gen1. Para autenticação de usuário final com Data Lake Storage Gen1 usando Python, consulte [Autenticação de usuário final com Data Lake Storage Gen1 usando Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Você pode baixar o Python [aqui](https://www.python.org/downloads/). Este artigo usa o Python 3.6.2.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Web" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação de serviço a serviço com Azure Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com o Data Lake Storage Gen1 usando o Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory etc.
* O módulo `azure-mgmt-datalake-store`, que inclui as operações de gerenciamento de conta do Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [Referência do módulo de gerenciamento do Azure Data Lake Storage Gen1](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* O módulo `azure-datalake-store`, que inclui as operações do sistema de arquivos do Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [referência do módulo Filesystem azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest/).

Use os comandos a seguir para instalar os módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

1. Use o IDE de sua escolha para criar um novo aplicativo Python, por exemplo, **mysample.py**.

2. Adicione o seguinte snippet para importar os módulos necessários

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Salve as alterações a mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticação de serviço a serviço com o segredo do cliente para gerenciamento de conta

Use esse trecho de código para autenticar com Azure AD para operações de gerenciamento de contas no Data Lake Storage Gen1, como criar uma conta do Data Lake Storage Gen1, excluir uma conta do Data Lake Storage Gen1, etc. O snippet a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando segredo do cliente para entidade de serviço/aplicativo de um aplicativo "Web App" existente do Azure AD.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Autenticação de serviço a serviço com o segredo do cliente para operações do sistema de arquivos

Use o trecho de código a seguir para autenticar com Azure AD para operações do sistema de arquivos no Data Lake Storage Gen1 como criar pasta, carregar arquivo, etc. O snippet a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando segredo do cliente para entidade de segurança/aplicativo. Use-o com o aplicativo "Aplicativo Web" Azure AD existente.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como usar a autenticação de serviço a serviço para autenticar com o Data Lake Storage Gen1 usando Python. Agora você pode consultar os seguintes artigos que descrevem como usar o Python para trabalhar com o Data Lake Storage Gen1.

* [Operações de gerenciamento de conta no Data Lake Storage Gen1 usando Python](data-lake-store-get-started-python.md)
* [Operações de dados no Data Lake Storage Gen1 usando Python ](data-lake-store-data-operations-python.md)


