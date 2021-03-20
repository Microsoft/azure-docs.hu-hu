---
title: Végfelhasználói hitelesítés – Python és Data Lake Storage Gen1 – Azure
description: Ismerje meg, hogyan érheti el a végfelhasználói hitelesítést a Azure Data Lake Storage Gen1 a Python használatával Azure Active Directory
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: c7ee58dec4e8bc04eb07695378e0d4dc973b6705
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109085"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Végfelhasználói hitelesítés Azure Data Lake Storage Gen1 a Python használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
>
>

Ebből a cikkből megtudhatja, hogyan használhatja a Python SDK-t a végfelhasználói hitelesítéshez Azure Data Lake Storage Gen1 használatával. A végfelhasználói hitelesítés további két kategóriára osztható:

* Végfelhasználói hitelesítés többtényezős hitelesítés nélkül
* Végfelhasználói hitelesítés többtényezős hitelesítéssel

Ezeket a lehetőségeket a cikk tárgyalja. A Data Lake Storage Gen1 a Python használatával történő szolgáltatások közötti hitelesítéshez lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 a Python használatával](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "natív" alkalmazást**. A Data Lake Storage Gen1 a Azure Active Directory használatával végre kell hajtania a [végfelhasználói hitelesítéshez](data-lake-store-end-user-authenticate-using-active-directory.md)szükséges lépéseket.

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 Python használatával való használatához három modult kell telepítenie.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modul, amely tartalmazza a Azure Data Lake Storage Gen1 Fiókkezelés műveleteit. További információ erről a modulról: [Azure Data Lake Storage Gen1 felügyeleti modul referenciája](/python/api/azure-mgmt-datalake-store/).
* A `azure-datalake-store` modul, amely tartalmazza a Azure Data Lake Storage Gen1 fájlrendszer műveleteit. További információ erről a modulról: [Azure-datalake-Store fájlrendszeri modul referenciája](/python/api/azure-datalake-store/azure.datalake.store.core/).

A modulok telepítéséhez használja a következő parancsokat.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. A kívánt IDE-ben hozzon létre egy új Python-alkalmazást, például **mysample.py**.

2. Adja hozzá a következő kódrészletet a szükséges modulok importálásához

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Mentse a mysample.py módosításait.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Végfelhasználói hitelesítés többtényezős hitelesítéssel

### <a name="for-account-management"></a>A fiókok felügyeletéhez

A következő kódrészlettel végezheti el a hitelesítést az Azure AD-vel a Data Lake Storage Gen1 fiókban található Fiókkezelés-műveletekhez. A következő kódrészlet használható az alkalmazás többtényezős hitelesítés használatával történő hitelesítéséhez. Adja meg az alábbi értékeket egy meglévő Azure AD **natív** alkalmazáshoz.

```python
authority_host_url = "https://login.microsoftonline.com"
tenant = "FILL-IN-HERE"
authority_url = authority_host_url + '/' + tenant
client_id = 'FILL-IN-HERE'
redirect = 'urn:ietf:wg:oauth:2.0:oob'
RESOURCE = 'https://management.core.windows.net/'

context = adal.AuthenticationContext(authority_url)
code = context.acquire_user_code(RESOURCE, client_id)
print(code['message'])
mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)
```

### <a name="for-filesystem-operations"></a>Fájlrendszerbeli műveletekhez

Ezzel a művelettel hitelesítheti az Azure AD-t a fájlrendszerbeli műveletek Data Lake Storage Gen1 fiókban való hitelesítéséhez. A következő kódrészlet használható az alkalmazás többtényezős hitelesítés használatával történő hitelesítéséhez. Adja meg az alábbi értékeket egy meglévő Azure AD **natív** alkalmazáshoz.

```console
adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')
```

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Végfelhasználói hitelesítés többtényezős hitelesítés nélkül

Ez elavult. További információ: Azure- [hitelesítés a PYTHON SDK használatával](/azure/python/python-sdk-azure-authenticate).

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használható a végfelhasználói hitelesítés a Azure Data Lake Storage Gen1 a Python használatával történő hitelesítéshez. A következő cikkekből megtudhatja, hogyan használható a Python a Azure Data Lake Storage Gen1 való együttműködéshez.

* [Fiókkezelés Data Lake Storage Gen1 a Python használatával](data-lake-store-get-started-python.md)
* [Az adatműveletek Data Lake Storage Gen1 a Python használatával](data-lake-store-data-operations-python.md)