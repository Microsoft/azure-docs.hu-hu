---
title: A Python használata a Azure Data Lake Storage Gen2ban tárolt adatkezeléshez
description: A Python használatával olyan Storage-fiókokban kezelheti a címtárakat és a fájlokat, amelyeken engedélyezve van a hierarchikus névtér.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652292"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>A Python használata könyvtárak és fájlok kezelésére Azure Data Lake Storage Gen2

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet Python-címtárakat és-fájlokat olyan Storage-fiókokban, amelyek hierarchikus névteret használnak.

A címtárak és fájlok hozzáférés-vezérlési listáinak (ACL) beszerzéséről, beállításáról és frissítéséről a [Python használata az ACL-ek kezelése Azure Data Lake Storage Gen2ban](data-lake-storage-acl-python.md)című cikkből tájékozódhat.

[Csomag (Python-csomag indexe)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Példák](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [API-referenciák](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér. Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

## <a name="set-up-your-project"></a>A projekt beállítása

Telepítse a Pythonhoz készült Azure Data Lake Storage ügyféloldali kódtárat a [pip](https://pypi.org/project/pip/)használatával.

```
pip install azure-storage-file-datalake
```

Adja hozzá ezeket az importálási utasításokat a fájl elejéhez.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre a fiók kulcsa alapján.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- Cserélje le a `storage_account_name` helyőrző értékét a Storage-fiók nevére.

- Cserélje le a `storage_account_key` helyőrző értékét a Storage-fiók hozzáférési kulcsára.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Kapcsolat Azure Active Directory (Azure AD) használatával

A [Pythonhoz készült Azure Identity ügyféloldali kódtár](https://pypi.org/project/azure-identity/) használatával hitelesítheti az alkalmazást az Azure ad-vel.

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  Az értékek [lekéréséhez lásd: token beszerzése az Azure ad-ből az ügyfélalkalmazások kéréseinek engedélyezéséhez](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> További példákért tekintse meg az [Azure Identity ügyféloldali kódtárat a Python](https://pypi.org/project/azure-identity/) dokumentációjában.

## <a name="create-a-container"></a>Tároló létrehozása

A tároló fájlrendszerként működik a fájlok számára. A **FileSystemDataLakeServiceClient.create_file_system** metódus meghívásával létrehozhat egyet.

Ez a példa egy nevű tárolót hoz létre `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-hivatkozást a **FileSystemClient.create_directory** metódus meghívásával.

Ez a példa egy nevű könyvtárat helyez `my-directory` el egy tárolóhoz. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a **DataLakeDirectoryClient.rename_directory** metódus meghívásával. Adja meg a kívánt könyvtár elérési útját (a paramétert). 

Ez a példa átnevez egy alkönyvtárat a névre `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Könyvtár törlése

A **DataLakeDirectoryClient.delete_directory** metódus meghívásával törölhet egy könyvtárat.

Ez a példa törli a nevű könyvtárat `my-directory` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Először hozzon létre egy fájlt a célhelyen a **DataLakeFileClient** osztály egy példányának létrehozásával. Töltsön fel egy fájlt a **DataLakeFileClient.append_data** metódus meghívásával. Ügyeljen arra, hogy a feltöltést a **DataLakeFileClient.flush_data** metódus meghívásával végezze el.

Ez a példa egy szövegfájlt tölt fel egy nevű könyvtárba `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Ha a fájl mérete nagy, akkor a kódnak több hívást kell tennie a **DataLakeFileClient.append_data** metódushoz. Ehelyett érdemes inkább a **DataLakeFileClient.upload_data** metódust használni. Így feltöltheti a teljes fájlt egyetlen hívással. 

## <a name="upload-a-large-file-to-a-directory"></a>Nagyméretű fájl feltöltése egy könyvtárba

A **DataLakeFileClient.upload_data** metódus használatával nagyméretű fájlokat tölthet fel anélkül, hogy több hívást kellene tennie a **DataLakeFileClient.append_data** metódusnak.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Letöltés egy címtárból 

Nyisson meg egy helyi fájlt írásra. Ezután hozzon létre egy **DataLakeFileClient** -példányt, amely a letölteni kívánt fájlt jelöli. Hívja meg a **DataLakeFileClient.read_filet** a fájl bájtjainak olvasásához, majd írja be a bájtokat a helyi fájlba. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

A könyvtár tartalmának listázásához hívja meg a **FileSystemClient.get_paths** metódust, majd az eredmények alapján sorolja fel azokat.

Ez a példa kinyomtatja az egyes alkönyvtárak és fájlok elérési útját, amely egy nevű könyvtárban található `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Lásd még

- [API-referenciadokumentáció](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-storage-file-datalake/)
- [Példák](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-python/issues)