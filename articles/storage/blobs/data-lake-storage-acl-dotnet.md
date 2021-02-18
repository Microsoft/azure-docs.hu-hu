---
title: A .NET használata az ACL-ek beállításához Azure Data Lake Storage Gen2
description: A .NET használatával kezelheti a hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 626e89d8d758d5fe31ef6c913076a9154274bb61
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654234"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>A .NET használata a hozzáférés-vezérlési listák kezeléséhez Azure Data Lake Storage Gen2

Ebből a cikkből megtudhatja, hogyan használhatja a .NET-et a címtárak és fájlok hozzáférés-vezérlési listája beolvasására, beállítására és frissítésére.

Az ACL öröklése már elérhető az új alárendelt elemekhez, amelyeket a rendszer a szülő címtárban hozott létre. Az ACL-eket rekurzív módon is hozzáadhatja, frissítheti és eltávolíthatja a szülő könyvtár meglévő alárendelt elemein anélkül, hogy ezeket a módosításokat külön kellene elvégeznie az egyes alárendelt elemek esetében.

[Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [Rekurzív ACL-minta](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)  |  [API-referenciák](/dotnet/api/azure.storage.files.datalake)  |  [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

- Az Azure CLI verziója `2.6.0` vagy újabb.

- A következő biztonsági engedélyek egyike:

  - Egy kiépített Azure Active Directory (AD) [rendszerbiztonsági tag](../../role-based-access-control/overview.md#security-principal) , amely a [tároló blob-adattulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepkört rendelte hozzá a cél tároló, a szülő erőforráscsoport vagy az előfizetés hatókörében.  

  - Annak a tárolónak vagy könyvtárnak a tulajdonosa, amelyre az ACL-beállításokat alkalmazni kívánja. Az ACL-ek rekurzív beállításához a cél tárolóban vagy a címtárban található összes alárendelt elem szerepel.
  
  - Storage-fiók kulcsa.

## <a name="set-up-your-project"></a>A projekt beállítása

Első lépésként telepítse az [Azure. Storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet csomagot.

1. Nyisson meg egy parancssori ablakot (például: Windows PowerShell).

2. A projekt könyvtárából telepítse az Azure. Storage. files. DataLake előzetes verzióját a `dotnet add package` parancs használatával.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   Ezután adja hozzá ezeket az utasításokat a programkód elejéhez.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

A cikkben szereplő kódrészletek használatához létre kell hoznia egy [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Kapcsolat Azure Active Directory (AD) használatával

> [!NOTE]
> Ha Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy a rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és az azok módosításának hatásairól, tekintse meg a  [Azure Data Lake Storage Gen2 hozzáférés-vezérlési modelljét](./data-lake-storage-access-control-model.md).

A [.net-hez készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) használatával hitelesítheti alkalmazását az Azure ad-vel.

A csomag telepítése után adja hozzá a using utasítást a programkód elejéhez.

```csharp
using Azure.Identity;
```

Szerezze be az ügyfél-azonosítót, az ügyfél titkos kulcsát és a bérlő AZONOSÍTÓját. Ehhez tekintse meg [a jogkivonat beszerzése az Azure ad-ből az ügyfélalkalmazástól érkező kérések engedélyezéséhez](../common/storage-auth-aad-app.md)című témakört. A folyamat részeként hozzá kell rendelnie a következő [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../../role-based-access-control/overview.md) szerepkörök egyikét a rendszerbiztonsági tag számára. 

|Szerepkör|ACL-beállítási képesség|
|--|--|
|[Storage-blobadatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|A fiókban lévő összes könyvtár és fájl.|
|[Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Csak a rendszerbiztonsági tag tulajdonában lévő könyvtárak és fájlok.|

Ez a példa egy [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> További példákért tekintse meg a [.net-hez készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) dokumentációját.

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra.

Ez a példa egy [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt hoz létre a fiók kulcsa alapján.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>ACL-ek beállítása

Az ACL *beállításakor* a teljes ACL-t **lecseréli** , beleértve az összes bejegyzését. Ha módosítani szeretné egy rendszerbiztonsági tag hozzáférési szintjét, vagy egy új rendszerbiztonsági tag hozzáadása az ACL-hez anélkül, hogy ez hatással lenne a többi meglévő bejegyzésre, *frissítenie* kell az ACL-t. Ha az ACL-t nem a helyett szeretné frissíteni, tekintse meg a jelen cikk [frissítési ACL](#update-acls) -EK című szakaszát.  

Ha úgy dönt, hogy *Beállítja* az ACL-t, hozzá kell adnia egy bejegyzést a tulajdonos felhasználóhoz, egy bejegyzést a tulajdonos csoport számára, valamint egy bejegyzést az összes többi felhasználó számára. Ha többet szeretne megtudni a tulajdonos felhasználóról, a tulajdonos csoportról és az összes többi felhasználóról, tekintse meg a [felhasználók és az identitások](data-lake-storage-access-control.md#users-and-identities)című témakört.

Ez a szakasz a következőket mutatja be:

- Címtár ACL-listájának beállítása
- Fájl hozzáférés-vezérlési listájának beállítása
- ACL-ek rekurzív beállítása

### <a name="set-the-acl-of-a-directory"></a>Címtár ACL-listájának beállítása

A címtár hozzáférés-vezérlési listájának (ACL) beszerzéséhez hívja meg a [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) metódust, és állítsa be az ACL-t úgy, hogy meghívja a [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metódust.

Ez a példa lekérdezi és beállítja a nevű könyvtár ACL-listáját `my-directory` . A karakterlánc `user::rwx,group::r-x,other::rw-` Megadja az olvasási, írási és végrehajtási engedélyeket a tulajdonos felhasználó számára, csak olvasási és végrehajtási engedélyeket ad a tulajdonos csoportnak, és minden más olvasási és írási engedélyt ad.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

Lekérheti és beállíthatja egy tároló gyökérkönyvtárának ACL-listáját is. A gyökérkönyvtár beszerzéséhez adjon át egy üres karakterláncot ( `""` ) a [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) metódusba.

### <a name="set-the-acl-of-a-file"></a>Fájl hozzáférés-vezérlési listájának beállítása

Egy fájl hozzáférés-vezérlési listájának (ACL) beszerzéséhez hívja meg a [DataLakeFileClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) metódust, és állítsa be az ACL-t úgy, hogy meghívja a [DataLakeFileClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) metódust.

Ez a példa lekérdezi és beállítja a nevű fájl hozzáférés-vezérlési listáját `my-file.txt` . A karakterlánc `user::rwx,group::r-x,other::rw-` Megadja az olvasási, írási és végrehajtási engedélyeket a tulajdonos felhasználó számára, csak olvasási és végrehajtási engedélyeket ad a tulajdonos csoportnak, és minden más olvasási és írási engedélyt ad.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>ACL-ek rekurzív beállítása

Az ACL-eket rekurzív módon állíthatja be a **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** metódus meghívásával. Adja át ezt a metódust a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [listájához](/dotnet/api/system.collections.generic.list-1) . Mindegyik [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) egy ACL-bejegyzést definiál.

Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, akkor a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [PathAccessControlItem. defaultscope tulajdonságnak](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) tulajdonságát állítsa **igaz** értékre. 

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy az alapértelmezett ACL-t kell-e beállítani. Ezt a paramétert használja a rendszer a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)konstruktorában. Az ACL bejegyzései az olvasási, írási és végrehajtási engedélyeket biztosítanak a tulajdonosnak a tulajdonos csoport számára, és csak olvasási és végrehajtási engedélyeket biztosítanak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad a (z) "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "objektumhoz olvasási és végrehajtási engedélyekkel.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Ha egy köteg méretének megadásával szeretné megtekinteni egy olyan példát, amely az ACL-eket rekurzívan állítja be kötegekben, tekintse meg a .NET- [mintát](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="update-acls"></a>ACL-ek frissítése

*Ha módosít egy ACL* -t, akkor az ACL helyett módosítania kell az ACL-t. Hozzáadhat például egy új rendszerbiztonsági tag-t az ACL-hez anélkül, hogy ez hatással lenne az ACL-ben felsorolt többi rendszerbiztonsági tagra is.  Ha a frissítés helyett az ACL-t szeretné cserélni, tekintse meg a jelen cikk ACL-ek [beállítása](#set-acls) című szakaszát.

Ez a szakasz a következőket mutatja be:

- ACL frissítése
- Az ACL-ek rekurzív frissítése

### <a name="update-an-acl"></a>ACL frissítése

Először kérje le a címtár ACL-listáját a [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) metódus meghívásával. Másolja az ACL-bejegyzések listáját az [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) objektumok új [listájára](/dotnet/api/system.collections.generic.list-1) . Ezután keresse meg a frissíteni kívánt bejegyzést, és cserélje le a listára. Állítsa be az ACL-t úgy, hogy meghívja a [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metódust.

Ez a példa frissíti egy tároló legfelső szintű ACL-listáját úgy, hogy lecseréli az összes többi felhasználó ACL-bejegyzését. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>Az ACL-ek rekurzív frissítése

Egy ACL rekurzív frissítéséhez hozzon létre egy új ACL-objektumot a frissíteni kívánt ACL-bejegyzéssel, majd használja ezt az objektumot az ACL frissítése műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg a frissítendő ACL-bejegyzéseket.

Az ACL-t rekurzív módon frissítheti a **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** metódus meghívásával.  Adja át ezt a metódust a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [listájához](/dotnet/api/system.collections.generic.list-1) . Mindegyik [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) egy ACL-bejegyzést definiál.

Ha frissíteni szeretné az **alapértelmezett** ACL-bejegyzést, akkor a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [PathAccessControlItem. defaultscope tulajdonságnak](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) tulajdonságát állítsa **igaz** értékre.

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít. Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy frissíteni kell-e az alapértelmezett ACL-t. Ezt a paramétert használja a rendszer a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)konstruktorában.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Ha egy olyan példát szeretne látni, amely egy köteg méretének megadásával rekurzívan frissíti a ACL-eket, tekintse meg a .NET- [mintát](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="remove-acl-entries"></a>ACL-Bejegyzések eltávolítása

Egy vagy több ACL-bejegyzést is eltávolíthat. Ez a szakasz a következőket mutatja be:

- ACL-bejegyzés eltávolítása
- ACL-bejegyzések rekurzív eltávolítása

### <a name="remove-an-acl-entry"></a>ACL-bejegyzés eltávolítása

Először kérje le a címtár ACL-listáját a [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) metódus meghívásával. Másolja az ACL-bejegyzések listáját az [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) objektumok új [listájára](/dotnet/api/system.collections.generic.list-1) . Ezután keresse meg az eltávolítani kívánt bejegyzést, és hívja meg a gyűjtemény [eltávolítási](/dotnet/api/system.collections.ilist.remove) metódusát. Állítsa be a frissített ACL-t úgy, hogy meghívja a [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) metódust.

Ez a példa frissíti egy tároló legfelső szintű ACL-listáját úgy, hogy lecseréli az összes többi felhasználó ACL-bejegyzését. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>ACL-bejegyzések rekurzív eltávolítása

Az ACL-bejegyzések rekurzív eltávolításához hozzon létre egy új ACL-objektumot az ACL-bejegyzés eltávolításához, majd használja ezt az objektumot az ACL eltávolítása művelettel. Ne szerezze be a meglévő ACL-t, csak adja meg az eltávolítandó ACL-bejegyzéseket. 

Távolítsa el az ACL-bejegyzéseket a **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** metódus meghívásával. Adja át ezt a metódust a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [listájához](/dotnet/api/system.collections.generic.list-1) . Mindegyik [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) egy ACL-bejegyzést definiál. 

Ha el szeretné távolítani az **alapértelmezett** ACL-bejegyzést, akkor a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [PathAccessControlItem. defaultscope tulajdonságnak](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) tulajdonságát állítsa **igaz** értékre. 

Ez a példa egy ACL-bejegyzést távolít el a nevű könyvtár ACL-listájából `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy el kell-e távolítani a bejegyzést az alapértelmezett ACL-ből. Ezt a paramétert használja a rendszer a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)konstruktorában.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Ha egy köteg méretének megadásával szeretné megtekinteni egy példát, amely eltávolítja a hozzáférés-vezérlési listákat a kötegekben, tekintse meg a .NET- [mintát](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="recover-from-failures"></a>Helyreállítás hibákból

A hozzáférés-vezérlési listák rekurzív módosításakor előfordulhatnak futásidejű vagy engedélyezési hibák. Futásidejű hibák esetén indítsa újra a folyamatot az elejétől. Engedélyekkel kapcsolatos hibák akkor fordulhatnak elő, ha a rendszerbiztonsági tag nem rendelkezik megfelelő engedélyekkel egy olyan könyvtár vagy fájl hozzáférés-vezérlési listájának módosításához, amely a címtár-hierarchiában van módosítva. Oldja meg az engedélyekkel kapcsolatos problémát, majd a folytatási token használatával folytassa a folyamatot a meghibásodási pontról, vagy indítsa újra a folyamatot az elejétől. A folytatási tokent nem kell használnia, ha az elejéről szeretne újraindulni. Az ACL-bejegyzéseket a negatív hatás nélkül is újra alkalmazhatja.

Ez a példa egy folytatási tokent ad vissza egy hiba esetén. Az alkalmazás a hiba megoldását követően újra meghívhatja ezt a metódust, és a folytatási tokent adja át. Ha ezt a példát a rendszer első alkalommal hívja meg, akkor az alkalmazás a `null` folytatási jogkivonat paraméter értékét átadja. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Ha egy köteg méretének megadásával szeretné megtekinteni egy olyan példát, amely az ACL-eket rekurzívan állítja be kötegekben, tekintse meg a .NET- [mintát](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

Ha azt szeretné, hogy a folyamat az engedélyekkel kapcsolatos hibák mellett befejeződjön, megadhatja a következőt:.

Annak érdekében, hogy a folyamat befejeződjön, adjon meg egy **AccessControlChangedOptions** objektumot, és állítsa be az objektum **ContinueOnFailure** tulajdonságát a következőre: ``true`` .

Ez a példa az ACL-bejegyzéseket rekurzív módon állítja be. Ha ez a kód egy engedélyezési hibát észlel, akkor a hibát rögzíti, és folytatja a végrehajtást. Ez a példa a hibák számát jeleníti meg a konzolon.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Ha egy köteg méretének megadásával szeretné megtekinteni egy olyan példát, amely az ACL-eket rekurzívan állítja be kötegekben, tekintse meg a .NET- [mintát](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Lásd még

- [API-referenciadokumentáció](/dotnet/api/azure.storage.files.datalake)
- [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-net/issues)
- [Hozzáférés-vezérlési modell Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Hozzáférés-vezérlési listák (ACL-ek) Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)