---
title: 'Python: fájlrendszerbeli műveletek Azure Data Lake Storage Gen1on | Microsoft Docs'
description: Megtudhatja, hogyan használhatja a Python SDK-t a Data Lake Storage Gen1 fájlrendszerrel való együttműködéshez.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 8ca455a802b180163579f67104a61f455dd54f94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92109221"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Fájlrendszer-műveletek Azure Data Lake Storage Gen1 a Python használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Ebből a cikkből megtudhatja, hogyan hajthat végre fájlrendszerbeli műveleteket a Python SDK használatával Azure Data Lake Storage Gen1on. A Data Lake Storage Gen1 a Python használatával végzett felügyeleti műveletek végrehajtásával kapcsolatos útmutatásért lásd: [Fiókkezelés a Data Lake Storage Gen1 a Python használatával](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1 fiók**. Kövesse a [Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](data-lake-store-get-started-portal.md)című témakör utasításait.

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 Python használatával való használatához három modult kell telepítenie.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modul, amely tartalmazza a Azure Data Lake Storage Gen1 Fiókkezelés műveleteit. A modullal kapcsolatos további információkért tekintse meg az [Azure-mgmt-datalake-Store modul referenciáját](/python/api/azure-mgmt-datalake-store/).
* A `azure-datalake-store` modul, amely tartalmazza a Azure Data Lake Storage Gen1 fájlrendszer műveleteit. További információ erről a modulról: [Azure-datalake-Store File-System modul Reference](/python/api/azure-datalake-store/azure.datalake.store.core/).

A modulok telepítéséhez használja a következő parancsokat.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. A választott IDE-ben hozzon létre egy új Python-alkalmazást, például **mysample.py** néven.

2. Adja hozzá a következő sorokat a szükséges modulok importálásához.

   ```python
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Mentse a mysample.py módosításait.

## <a name="authentication"></a>Hitelesítés

Ebben a szakaszban az Azure AD-hitelesítés különböző módjait tárgyaljuk. Az elérhető lehetőségek:

* Az alkalmazás végfelhasználói hitelesítéséhez lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 a Python használatával](data-lake-store-end-user-authenticate-python.md).
* Az alkalmazás szolgáltatások közötti hitelesítéséhez lásd: [szolgáltatások közötti hitelesítés Data Lake Storage Gen1 a Python használatával](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Fájlrendszerügyfél létrehozása

A következő kódrészlet először létrehozza a Data Lake Storage Gen1 fiók ügyfelét. Az ügyfél objektum használatával hozza létre a Data Lake Storage Gen1 fiókot. Végül pedig létrehoz egy fájlrendszerügyfél-objektumot.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'

## Create a filesystem client object
adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

```python
## Create a directory
adlsFileSystemClient.mkdir('/mysampledirectory')
```

## <a name="upload-a-file"></a>Fájl feltöltése

```python
## Upload a file
multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```


## <a name="download-a-file"></a>Fájl letöltése

```python
## Download a file
multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)
```

## <a name="delete-a-directory"></a>Könyvtár törlése

```python
## Delete a directory
adlsFileSystemClient.rm('/mysampledirectory', recursive=True)
```

## <a name="next-steps"></a>Következő lépések
* [Fiókkezelés Data Lake Storage Gen1 a Python használatával](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Lásd még

* [Azure Data Lake Storage Gen1 Python (fájlrendszer) referenciája](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Azure Data Lake Storage Gen1-kompatibilis nyílt forráskódú Big adatalkalmazások](data-lake-store-compatible-oss-other-applications.md)