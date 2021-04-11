---
title: A Python használata a Azure Data Lake Storage Gen2 ACL-ek kezeléséhez
description: A Python használatával kezelheti a hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654224"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>A Python használata a Azure Data Lake Storage Gen2 ACL-ek kezeléséhez

Ebből a cikkből megtudhatja, hogyan használhatja a Pythont a címtárak és fájlok hozzáférés-vezérlési listája beolvasásához, beállításához és frissítéséhez. 

Az ACL öröklése már elérhető az új alárendelt elemekhez, amelyeket a rendszer a szülő címtárban hozott létre. Az ACL-eket rekurzív módon is hozzáadhatja, frissítheti és eltávolíthatja a szülő könyvtár meglévő alárendelt elemein anélkül, hogy ezeket a módosításokat külön kellene elvégeznie az egyes alárendelt elemek esetében. 

[Csomag (Python-csomag indexe)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Példák](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Rekurzív ACL-minták](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)  |  [API-referenciák](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

- Az Azure CLI verziója `2.6.0` vagy újabb.

- A következő biztonsági engedélyek egyike:

  - Egy kiépített Azure Active Directory (AD) [rendszerbiztonsági tag](../../role-based-access-control/overview.md#security-principal) , amely a [tároló blob-adattulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepkört rendelte hozzá a cél tároló, a szülő erőforráscsoport vagy az előfizetés hatókörében.  

  - Annak a tárolónak vagy könyvtárnak a tulajdonosa, amelyre az ACL-beállításokat alkalmazni kívánja. Az ACL-ek rekurzív beállításához a cél tárolóban vagy a címtárban található összes alárendelt elem szerepel.
  
  - Storage-fiók kulcsa.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Kapcsolat Azure Active Directory (AD) használatával

> [!NOTE]
> Ha Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy a rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és az azok módosításának hatásairól, tekintse meg a  [Azure Data Lake Storage Gen2 hozzáférés-vezérlési modelljét](./data-lake-storage-access-control-model.md).

A [Pythonhoz készült Azure Identity ügyféloldali kódtár](https://pypi.org/project/azure-identity/) használatával hitelesítheti az alkalmazást az Azure ad-vel.

Szerezze be az ügyfél-azonosítót, az ügyfél titkos kulcsát és a bérlő AZONOSÍTÓját. Ehhez tekintse meg [a jogkivonat beszerzése az Azure ad-ből az ügyfélalkalmazástól érkező kérések engedélyezéséhez](../common/storage-auth-aad-app.md)című témakört. A folyamat részeként hozzá kell rendelnie a következő [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../../role-based-access-control/overview.md) szerepkörök egyikét a rendszerbiztonsági tag számára. 

|Szerepkör|ACL-beállítási képesség|
|--|--|
|[Storage-blobadatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|A fiókban lévő összes könyvtár és fájl.|
|[Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Csak a rendszerbiztonsági tag tulajdonában lévő könyvtárak és fájlok.|

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> További példákért tekintse meg az [Azure Identity ügyféloldali kódtárat a Python](https://pypi.org/project/azure-identity/) dokumentációjában.

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra.

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre a fiók kulcsa alapján.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Cserélje le a `storage_account_name` helyőrző értékét a Storage-fiók nevére.

- Cserélje le a `storage_account_key` helyőrző értékét a Storage-fiók hozzáférési kulcsára.

## <a name="set-acls"></a>ACL-ek beállítása

Az ACL *beállításakor* a teljes ACL-t **lecseréli** , beleértve az összes bejegyzését. Ha módosítani szeretné egy rendszerbiztonsági tag hozzáférési szintjét, vagy egy új rendszerbiztonsági tag hozzáadása az ACL-hez anélkül, hogy ez hatással lenne a többi meglévő bejegyzésre, *frissítenie* kell az ACL-t. Ha az ACL-t nem a helyett szeretné frissíteni, tekintse meg a jelen cikk [frissítési ACL](#update-acls-recursively) -EK című szakaszát.  

Ez a szakasz a következőket mutatja be:

- Címtár ACL-listájának beállítása
- Fájl hozzáférés-vezérlési listájának beállítása

### <a name="set-the-acl-of-a-directory"></a>Címtár ACL-listájának beállítása

A címtár hozzáférés-vezérlési listájának (ACL) beszerzéséhez hívja meg a **DataLakeDirectoryClient.get_access_control** metódust, és állítsa be az ACL-t a **DataLakeDirectoryClient.set_access_control** metódus meghívásával.

Ez a példa lekérdezi és beállítja a nevű könyvtár ACL-listáját `my-directory` . A karakterlánc `rwxr-xrw-` Megadja az olvasási, írási és végrehajtási engedélyeket a tulajdonos felhasználó számára, csak olvasási és végrehajtási engedélyeket ad a tulajdonos csoportnak, és minden más olvasási és írási engedélyt ad.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Lekérheti és beállíthatja egy tároló gyökérkönyvtárának ACL-listáját is. A gyökérkönyvtár beszerzéséhez hívja meg a **FileSystemClient._get_root_directory_client** metódust.

### <a name="set-the-acl-of-a-file"></a>Fájl hozzáférés-vezérlési listájának beállítása

Egy fájl hozzáférés-vezérlési listájának (ACL) beszerzéséhez hívja meg a **DataLakeFileClient.get_access_control** metódust, és állítsa be az ACL-t a **DataLakeFileClient.set_access_control** metódus meghívásával.

Ez a példa lekérdezi és beállítja a nevű fájl hozzáférés-vezérlési listáját `my-file.txt` . A karakterlánc `rwxr-xrw-` Megadja az olvasási, írási és végrehajtási engedélyeket a tulajdonos felhasználó számára, csak olvasási és végrehajtási engedélyeket ad a tulajdonos csoportnak, és minden más olvasási és írási engedélyt ad.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>ACL-ek rekurzív beállítása

Az ACL *beállításakor* a teljes ACL-t **lecseréli** , beleértve az összes bejegyzését. Ha módosítani szeretné egy rendszerbiztonsági tag hozzáférési szintjét, vagy egy új rendszerbiztonsági tag hozzáadása az ACL-hez anélkül, hogy ez hatással lenne a többi meglévő bejegyzésre, *frissítenie* kell az ACL-t. Ha az ACL-t nem a helyett szeretné frissíteni, tekintse meg a jelen cikk [frissítési ACL-ek rekurzív](#update-acls-recursively) szakaszát.

Az ACL-eket rekurzív módon állíthatja be a **DataLakeDirectoryClient.set_access_control_recursive** metódus meghívásával.

Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, adja hozzá a karakterláncot `default:` az egyes ACL-bejegyzések karakterláncának elejéhez.

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` .

Ez a metódus egy nevű logikai paramétert fogad `is_default_scope` el, amely megadja, hogy az alapértelmezett ACL-t kell-e beállítani. Ha ez a paraméter `True` , a rendszer az ACL-bejegyzések listáját a sztring elé sorolja `default:` .

Az ACL bejegyzései az olvasási, írási és végrehajtási engedélyeket biztosítanak a tulajdonosnak a tulajdonos csoport számára, és csak olvasási és végrehajtási engedélyeket biztosítanak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad a (z) "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "objektumhoz olvasási és végrehajtási engedélyekkel. Ezek a bejegyzések az olvasási, írási és végrehajtási engedélyeket adják meg a tulajdonos számára, és csak olvasási és végrehajtási engedélyeket biztosítanak a tulajdonos csoportnak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad a (z) "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "objektumhoz olvasási és végrehajtási engedélyekkel.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Ha egy köteg méretének megadásával szeretné megtekinteni, hogy a rendszer rekurzív módon dolgozza fel az ACL-eket a kötegekben, tekintse meg a Python- [mintát](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

## <a name="update-acls-recursively"></a>Az ACL-ek rekurzív frissítése

*Ha módosít egy ACL* -t, akkor az ACL helyett módosítania kell az ACL-t. Hozzáadhat például egy új rendszerbiztonsági tag-t az ACL-hez anélkül, hogy ez hatással lenne az ACL-ben felsorolt többi rendszerbiztonsági tagra is.  Ha a frissítés helyett az ACL-t szeretné cserélni, tekintse meg a jelen cikk ACL-ek [beállítása](#set-acls) című szakaszát.

Egy ACL rekurzív frissítéséhez hozzon létre egy új ACL-objektumot a frissíteni kívánt ACL-bejegyzéssel, majd használja ezt az objektumot az ACL frissítése műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg a frissítendő ACL-bejegyzéseket. Az ACL-t rekurzív módon frissítheti a **DataLakeDirectoryClient.update_access_control_recursive** metódus meghívásával. Ha szeretné frissíteni az **alapértelmezett** ACL-bejegyzést, adja hozzá a karakterláncot `default:` az egyes ACL-bejegyzések karakterláncának elejéhez.

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít.

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `is_default_scope` el, amely megadja, hogy frissíteni kell-e az alapértelmezett ACL-t. Ha ez a paraméter `True` , a frissített ACL-bejegyzés a karakterlánc előtt szerepel `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Ha egy köteg méretének megadásával szeretné megtekinteni, hogy a rendszer rekurzív módon dolgozza fel az ACL-eket a kötegekben, tekintse meg a Python- [mintát](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

## <a name="remove-acl-entries-recursively"></a>ACL-bejegyzések rekurzív eltávolítása

Egy vagy több ACL-bejegyzést is eltávolíthat. Az ACL-bejegyzések rekurzív eltávolításához hozzon létre egy új ACL-objektumot az ACL-bejegyzés eltávolításához, majd használja ezt az objektumot az ACL eltávolítása művelettel. Ne szerezze be a meglévő ACL-t, csak adja meg az eltávolítandó ACL-bejegyzéseket. 

Távolítsa el az ACL-bejegyzéseket a **DataLakeDirectoryClient.remove_access_control_recursive** metódus meghívásával. Ha el szeretné távolítani az **alapértelmezett** ACL-bejegyzést, adja hozzá a karakterláncot az `default:` ACL-bejegyzési karakterlánc elejéhez. 

Ez a példa egy ACL-bejegyzést távolít el a nevű könyvtár ACL-listájából `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `is_default_scope` el, amely megadja, hogy el kell-e távolítani a bejegyzést az alapértelmezett ACL-ből. Ha ez a paraméter `True` , a frissített ACL-bejegyzés a karakterlánc előtt szerepel `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Ha egy köteg méretének megadásával szeretné megtekinteni, hogy a rendszer rekurzív módon dolgozza fel az ACL-eket a kötegekben, tekintse meg a Python- [mintát](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

## <a name="recover-from-failures"></a>Helyreállítás hibákból

A futásidejű vagy az engedélyekkel kapcsolatos hibák merülhetnek fel. Futásidejű hibák esetén indítsa újra a folyamatot az elejétől. Engedélyekkel kapcsolatos hibák akkor fordulhatnak elő, ha a rendszerbiztonsági tag nem rendelkezik megfelelő engedélyekkel egy olyan könyvtár vagy fájl hozzáférés-vezérlési listájának módosításához, amely a címtár-hierarchiában van módosítva. Oldja meg az engedélyekkel kapcsolatos problémát, majd a folytatási token használatával folytassa a folyamatot a meghibásodási pontról, vagy indítsa újra a folyamatot az elejétől. A folytatási tokent nem kell használnia, ha az elejéről szeretne újraindulni. Az ACL-bejegyzéseket a negatív hatás nélkül is újra alkalmazhatja.

Ez a példa egy folytatási tokent ad vissza egy hiba esetén. Az alkalmazás a hiba megoldását követően újra meghívhatja ezt a metódust, és a folytatási tokent adja át. Ha ezt a példát a rendszer első alkalommal hívja meg, akkor az alkalmazás a ``None`` folytatási jogkivonat paraméter értékét átadja.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Ha egy köteg méretének megadásával szeretné megtekinteni, hogy a rendszer rekurzív módon dolgozza fel az ACL-eket a kötegekben, tekintse meg a Python- [mintát](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

Ha azt szeretné, hogy a folyamat az engedélyekkel kapcsolatos hibák mellett befejeződjön, megadhatja a következőt:.

Annak érdekében, hogy a folyamat befejeződjön, ne adjon meg folytatási jogkivonatot a **DataLakeDirectoryClient.set_access_control_recursive** metódusnak.

Ez a példa az ACL-bejegyzéseket rekurzív módon állítja be. Ha ez a kód egy engedélyezési hibát észlel, akkor a hibát rögzíti, és folytatja a végrehajtást. Ez a példa a hibák számát jeleníti meg a konzolon.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Ha egy köteg méretének megadásával szeretné megtekinteni, hogy a rendszer rekurzív módon dolgozza fel az ACL-eket a kötegekben, tekintse meg a Python- [mintát](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Lásd még

- [API-referenciadokumentáció](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-storage-file-datalake/)
- [Példák](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-python/issues)
- [Hozzáférés-vezérlési modell Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Hozzáférés-vezérlési listák (ACL-ek) Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)