---
title: Python-szolgáltatások közötti hitelesítés – Data Lake Storage Gen1
description: Ismerje meg, hogyan valósítható meg a szolgáltatások közötti hitelesítés a Azure Data Lake Storage Gen1 használatával Azure Active Directory a Python használatával
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: fe987070438c689e899dee18edaf4f708555ab11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106756"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Szolgáltatások közötti hitelesítés Azure Data Lake Storage Gen1 a Python használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Ebből a cikkből megtudhatja, hogyan használhatja a Python SDK-t a szolgáltatások közötti hitelesítéshez Azure Data Lake Storage Gen1 használatával. A Data Lake Storage Gen1 a Python használatával történő végfelhasználói hitelesítéshez lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 a Python használatával](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "web" alkalmazást**. A Data Lake Storage Gen1 Azure Active Directory használatával kell végrehajtania a [szolgáltatások közötti hitelesítéshez](data-lake-store-service-to-service-authenticate-using-active-directory.md)szükséges lépéseket.

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 Python használatával való használatához három modult kell telepítenie.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modul, amely tartalmazza a Data Lake Storage Gen1 Fiókkezelés műveleteit. További információ erről a modulról: [Azure Data Lake Storage Gen1 felügyeleti modul referenciája](/python/api/azure-mgmt-datalake-store/).
* A `azure-datalake-store` modul, amely tartalmazza a Data Lake Storage Gen1 fájlrendszer műveleteit. További információ erről a modulról: [Azure-datalake-Store fájlrendszeri modul referenciája](/python/api/azure-datalake-store/azure.datalake.store.core/).

A modulok telepítéséhez használja a következő parancsokat.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. A választott IDE-ben hozzon létre egy új Python-alkalmazást, például **mysample.py** néven.

2. Adja hozzá a következő kódrészletet a szükséges modulok importálásához

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

3. Mentse a mysample.py módosításait.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Szolgáltatások közötti, titkos ügyfélkulccsal történő hitelesítés a fiókkezeléshez

Ezt a kódrészletet használhatja az Azure AD-vel való hitelesítéshez Data Lake Storage Gen1 például Data Lake Storage Gen1 fiók létrehozása, Data Lake Storage Gen1 fiók törlése stb. A következő kódrészlet használható az alkalmazás nem interaktív hitelesítéséhez, egy meglévő Azure AD "Web App" alkalmazás alkalmazás-és egyszerű szolgáltatásához tartozó ügyfél-titok használatával.

```python
authority_host_uri = 'https://login.microsoftonline.com'
tenant = '<TENANT>'
authority_uri = authority_host_uri + '/' + tenant
RESOURCE = 'https://management.core.windows.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

context = adal.AuthenticationContext(authority_uri, api_version=None)
mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)
```

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Szolgáltatások közötti, titkos ügyfélkulccsal történő hitelesítés a fájlrendszerműveletekhez

Az alábbi kódrészlettel hitelesítheti az Azure AD-t a fájlrendszerbeli műveletekhez Data Lake Storage Gen1 például a mappa létrehozása, a fájl feltöltése stb. használatával. A következő kódrészlet használható az alkalmazás nem interaktív hitelesítésére az alkalmazás/egyszerű szolgáltatáshoz tartozó ügyfél-titok használatával. Ezt meglévő „webes” Azure AD-alkalmazással használhatja.

```python
tenant = '<TENANT>'
RESOURCE = 'https://datalake.azure.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

adlCreds = lib.auth(tenant_id = tenant,
                client_secret = client_secret,
                client_id = client_id,
                resource = RESOURCE)
```

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

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használható a szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 a Python használatával történő hitelesítéshez. A következő cikkekből megtudhatja, hogyan használható a Python a Data Lake Storage Gen1 való együttműködéshez.

* [Fiókkezelés Data Lake Storage Gen1 a Python használatával](data-lake-store-get-started-python.md)
* [Az adatműveletek Data Lake Storage Gen1 a Python használatával](data-lake-store-data-operations-python.md)