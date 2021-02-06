---
title: ACL-ek rekurzív beállítása a Azure Data Lake Storage Gen2hoz | Microsoft Docs
description: A hozzáférés-vezérlési lista rekurzív módon adható hozzá, frissíthető és eltávolítható a szülő könyvtár meglévő alárendelt elemeihez.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 737608e4f83846187decb92b090f2385a8ff4ac6
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627340"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Hozzáférés-vezérlési listák (ACL-ek) rekurzív beállítása Azure Data Lake Storage Gen2

Az ACL öröklése már elérhető az új alárendelt elemekhez, amelyeket a rendszer a szülő címtárban hozott létre. Mostantól hozzáadhatja, frissítheti és eltávolíthatja a hozzáférés-vezérlési listákat a szülő könyvtár meglévő alárendelt elemeihez anélkül, hogy ezeket a módosításokat egyenként el kellene végeznie az egyes alárendelt elemek esetében.

[Könyvtárak](#libraries)  |  [Példák](#code-samples)  |  [Ajánlott eljárások](#best-practice-guidelines)

> [!NOTE]
> A Azure Storage Explorer mostantól támogatja az ACL-ek rekurzív alkalmazásának lehetőségét. További információért lásd: [ACL-ek rekurzív alkalmazása Azure Storage Explorerban](data-lake-storage-explorer.md#apply-acls-recursively). 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

- A rekurzív ACL-folyamat végrehajtásának megfelelő engedélyei. A megfelelő engedély a következők egyikét tartalmazza: 

  - Egy kiépített Azure Active Directory (AD) [rendszerbiztonsági tag](../../role-based-access-control/overview.md#security-principal) , amely a [tároló blob-adattulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepkört rendelte hozzá a cél tároló, a szülő erőforráscsoport vagy az előfizetés hatókörében.   

  - Annak a tárolónak vagy könyvtárnak a tulajdonosa, amelyre a rekurzív ACL-folyamat alkalmazását tervezi. Ez a cél tárolóban vagy a címtárban található összes alárendelt elemet magában foglalja. 

- Annak megértése, hogy a rendszer hogyan alkalmazza a ACL-eket a címtárakra és a fájlokra. Lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](./data-lake-storage-access-control.md). 

Ennek a cikknek a **projekt beállítása** című szakaszában találja a PowerShell, a .net SDK és a Python SDK telepítési útmutatójának megtekintését.

## <a name="set-up-your-project"></a>A projekt beállítása

Telepítse a szükséges kódtárakat.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. A következő parancs használatával ellenőrizze, hogy a telepített PowerShell `5.1` -verzió vagy magasabb-e.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   A PowerShell verziójának frissítéséhez lásd: a [meglévő Windows PowerShell frissítése](/powershell/scripting/install/installing-windows-powershell)
    
2. Telepítse **az az. Storage** modult.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   A PowerShell-modulok telepítésével kapcsolatos további információkért lásd: [a Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nyissa meg a [Azure Cloud Shell](../../cloud-shell/overview.md), vagy ha helyileg [telepítette](/cli/azure/install-azure-cli) az Azure CLI-t, nyisson meg egy parancssori alkalmazást, például a Windows PowerShellt.

2. A következő parancs használatával ellenőrizze, hogy a telepített Azure CLI `2.14.0` -verzió vagy magasabb-e.

   ```azurecli
    az --version
   ```
   Ha az Azure CLI verziója alacsonyabb, mint a `2.14.0` , telepítsen egy újabb verziót. Lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

### <a name="net"></a>[.NET](#tab/dotnet)

1. Nyisson meg egy parancssori ablakot (például: Windows PowerShell).

2. A projekt könyvtárából telepítse az Azure. Storage. files. DataLake előzetes verzióját a `dotnet add package` parancs használatával.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Vegye fel ezeket a using utasításokkal a programkód elejére.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="java"></a>[Java](#tab/java)

Első lépésként nyissa meg [ezt a lapot](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) , és keresse meg a Java-könyvtár legújabb verzióját. Ezután nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adjon hozzá egy függőségi elemet, amely hivatkozik erre a verzióra.

Ha az ügyfélalkalmazás Azure Active Directory (AD) használatával történő hitelesítését tervezi, akkor vegyen fel egy függőséget az Azure Secret ügyféloldali függvénytárba. Lásd:  [a titkos ügyféloldali kódtár csomag hozzáadása a projekthez](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Ezután adja hozzá ezeket az importálási utasításokat a programkódhoz.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. Töltse le a [Azure Data Lake Storage a Pythonhoz készült ügyféloldali kódtárat](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D).

2. Telepítse a letöltött könyvtárat a [pip](https://pypi.org/project/pip/)használatával.

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. Adja hozzá ezeket az importálási utasításokat a fájl elejéhez.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.core._match_conditions import MatchConditions
   from azure.storage.filedatalake._models import ContentSettings
   ```

---

## <a name="connect-to-your-account"></a>Csatlakozás a fiókhoz

A Azure Active Directory (AD) vagy egy fiók kulcsa segítségével csatlakozhat. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="connect-by-using-azure-active-directory-ad"></a>Kapcsolat Azure Active Directory (AD) használatával

Ezzel a módszerrel a rendszer biztosítja, hogy a felhasználói fiókja rendelkezik a megfelelő Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) hozzárendelésekkel és ACL-engedélyekkel. 

A következő táblázat az egyes támogatott szerepköröket és azok ACL-beállítási képességét mutatja be.

|Szerepkör|ACL-beállítási képesség|
|--|--|
|[Storage-blobadatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|A fiókban lévő összes könyvtár és fájl.|
|[Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Csak a rendszerbiztonsági tag tulajdonában lévő könyvtárak és fájlok.|

1. Nyisson meg egy Windows PowerShell-parancssorablakot, majd jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat.

   ```powershell
   Connect-AzAccount
   ```

2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetését azon Storage-fiók előfizetésére, amelyet a címtárban szeretne létrehozni és kezelni. Ebben a példában a helyőrző értékét cserélje le az `<subscription-id>` előfizetés azonosítójára.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```
3. A Storage-fiók környezetének beolvasása.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

#### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ezzel a módszerrel a rendszeren nem ellenőrizhető az Azure-RBAC vagy az ACL-engedélyek. A Storage-fiók környezetének beszerzése a fiók kulcsa alapján.

```powershell
$ctx = New-AzStorageContext -StorageAccountName "<storage-account-name>" -StorageAccountKey "<storage-account-key>"
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Ha helyileg használja az Azure CLI-t, futtassa a login parancsot.

   ```azurecli
   az login
   ```

   Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

   Ellenkező esetben nyisson meg egy böngészőt, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) és adja meg a terminálon megjelenő engedélyezési kódot. Ezután jelentkezzen be a fiókja hitelesítő adataival a böngészőben.

   A különböző hitelesítési módszerekkel kapcsolatos további tudnivalókért lásd: [hozzáférés engedélyezése a blobhoz vagy az üzenetsor-kezeléshez az Azure CLI-vel](./authorize-data-operations-cli.md).

2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetést a statikus webhelyét futtató Storage-fiók előfizetésére.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

> [!NOTE]
> A cikkben bemutatott példa Azure Active Directory (AD) engedélyezését mutatja. További információ az engedélyezési módszerekről: a [blob-vagy üzenetsor-hozzáférés engedélyezése az Azure CLI-vel](./authorize-data-operations-cli.md).

### <a name="net"></a>[.NET](#tab/dotnet)

A cikkben szereplő kódrészletek használatához létre kell hoznia egy [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt, amely a Storage-fiókot jelképezi.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Kapcsolat Azure Active Directory (AD) használatával

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

#### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a módszer a fiókhoz való kapcsolódás legegyszerűbb módja. 

Ez a példa egy [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt hoz létre a fiók kulcsa alapján.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

> [!NOTE]
> További példákért tekintse meg a [.net-hez készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) dokumentációját.

### <a name="java"></a>[Java](#tab/java)

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. 

#### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre a fiók kulcsa alapján.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Kapcsolat Azure Active Directory (Azure AD) használatával

A [Javához készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) használatával hitelesítheti alkalmazását az Azure ad-vel.

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  Az értékek [lekéréséhez lásd: token beszerzése az Azure ad-ből az ügyfélalkalmazások kéréseinek engedélyezéséhez](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> További példákért tekintse meg a Java-dokumentációhoz készült [Azure Identity ügyféloldali kódtárat](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="python"></a>[Python](#tab/python)

A cikkben szereplő kódrészletek használatához létre kell hoznia egy **DataLakeServiceClient** -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Kapcsolat Azure Active Directory (AD) használatával

A [Pythonhoz készült Azure Identity ügyféloldali kódtár](https://pypi.org/project/azure-identity/) használatával hitelesítheti az alkalmazást az Azure ad-vel.

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  Az értékek [lekéréséhez lásd: token beszerzése az Azure ad-ből az ügyfélalkalmazások kéréseinek engedélyezéséhez](../common/storage-auth-aad-app.md). A folyamat részeként hozzá kell rendelnie a következő [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../../role-based-access-control/overview.md) szerepkörök egyikét a rendszerbiztonsági tag számára. 

|Szerepkör|ACL-beállítási képesség|
|--|--|
|[Storage-blobadatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|A fiókban lévő összes könyvtár és fájl.|
|[Storage-blobadatok közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Csak a rendszerbiztonsági tag tulajdonában lévő könyvtárak és fájlok.|

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> További példákért tekintse meg az [Azure Identity ügyféloldali kódtárat a Python](https://pypi.org/project/azure-identity/) dokumentációjában.

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy **DataLakeServiceClient** -példányt hoz létre a fiók kulcsa alapján.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Cserélje le a `storage_account_name` helyőrző értékét a Storage-fiók nevére.

- Cserélje le a `storage_account_key` helyőrző értékét a Storage-fiók hozzáférési kulcsára.

---

## <a name="set-an-acl-recursively"></a>ACL beállítása rekurzív módon

Az ACL *beállításakor* a teljes ACL-t **lecseréli** , beleértve az összes bejegyzését. Ha módosítani szeretné egy rendszerbiztonsági tag hozzáférési szintjét, vagy egy új rendszerbiztonsági tag hozzáadása az ACL-hez anélkül, hogy ez hatással lenne a többi meglévő bejegyzésre, *frissítenie* kell az ACL-t. Ha az ACL-t nem a helyett szeretné frissíteni, tekintse meg a jelen cikk [ACL rekurzív frissítése](#update-an-acl-recursively) című szakaszát.  

Ha úgy dönt, hogy *Beállítja* az ACL-t, hozzá kell adnia egy bejegyzést a tulajdonos felhasználóhoz, egy bejegyzést a tulajdonos csoport számára, valamint egy bejegyzést az összes többi felhasználó számára. Ha többet szeretne megtudni a tulajdonos felhasználóról, a tulajdonos csoportról és az összes többi felhasználóról, tekintse meg a [felhasználók és az identitások](data-lake-storage-access-control.md#users-and-identities)című témakört. 

Ez a szakasz példákat tartalmaz az ACL beállítására.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Állítsa be az ACL-t rekurzív módon a **set-AzDataLakeGen2AclRecursive** parancsmag használatával.

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ezek a bejegyzések az olvasási, írási és végrehajtási engedélyeket adják meg a tulajdonos számára, és csak olvasási és végrehajtási engedélyeket biztosítanak a tulajdonos csoportnak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad meg az "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" AZONOSÍTÓJÚ objektumhoz olvasási és végrehajtási engedélyekkel.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, használja a **-defaultscope tulajdonságnak** paramétert a **set-AzDataLakeGen2ItemAclObject** parancs futtatásakor. Példa: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Ha egy köteg méretének megadásával szeretné megtekinteni egy olyan példát, amely az ACL-eket rekurzív módon állítja be a kötegekben, tekintse meg a [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) című cikket.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Állítsa be az ACL-t rekurzív módon az az [Storage FS Access set-rekurzív](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) parancs használatával.

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ezek a bejegyzések az olvasási, írási és végrehajtási engedélyeket adják meg a tulajdonos számára, és csak olvasási és végrehajtási engedélyeket biztosítanak a tulajdonos csoportnak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad meg az "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" AZONOSÍTÓJÚ objektumhoz olvasási és végrehajtási engedélyekkel.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, adja hozzá az előtagot az `default:` egyes bejegyzésekhez. Például `default:user::rwx` vagy `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`. 

### <a name="net"></a>[.NET](#tab/dotnet)

Az ACL-t rekurzív módon állíthatja be a **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** metódus meghívásával. Adja át ezt a metódust a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [listájához](/dotnet/api/system.collections.generic.list-1) . Mindegyik [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) egy ACL-bejegyzést definiál. 

Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, akkor a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [PathAccessControlItem. defaultscope tulajdonságnak](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) tulajdonságát állítsa **igaz** értékre. 

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy az alapértelmezett ACL-t kell-e beállítani. Ezt a paramétert használja a rendszer a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)konstruktorában. Az ACL bejegyzései az olvasási, írási és végrehajtási engedélyeket biztosítanak a tulajdonosnak a tulajdonos csoport számára, és csak olvasási és végrehajtási engedélyeket biztosítanak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad a (z) "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "objektumhoz olvasási és végrehajtási engedélyekkel.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Ha egy köteg méretének megadásával szeretné megtekinteni egy olyan példát, amely az ACL-eket rekurzívan állítja be kötegekben, tekintse meg a .NET- [mintát](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Az ACL-t rekurzív módon állíthatja be a **DataLakeDirectoryClient. setAccessControlRecursive** metódus meghívásával. Adja át ezt a metódust a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) objektumok [listájához](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) . Mindegyik [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) egy ACL-bejegyzést definiál. 

Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, akkor meghívhatja a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) **setDefaultScope** metódusát, és **igaz** értéket adhat át. 

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy az alapértelmezett ACL-t kell-e beállítani. Ez a paraméter a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) **setDefaultScope** metódusának minden hívásában használatos. Az ACL bejegyzései az olvasási, írási és végrehajtási engedélyeket biztosítanak a tulajdonosnak a tulajdonos csoport számára, és csak olvasási és végrehajtási engedélyeket biztosítanak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad meg az "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" AZONOSÍTÓJÚ objektumhoz olvasási és végrehajtási engedélyekkel.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Az ACL-t rekurzív módon állíthatja be a **DataLakeDirectoryClient.set_access_control_recursive** metódus meghívásával.

Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, adja hozzá a karakterláncot `default:` az egyes ACL-bejegyzések karakterláncának elejéhez. 

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . 

Ez a metódus egy nevű logikai paramétert fogad `is_default_scope` el, amely megadja, hogy az alapértelmezett ACL-t kell-e beállítani. Ha ez a paraméter `True` , a rendszer az ACL-bejegyzések listáját a sztring elé sorolja `default:` . 

Az ACL bejegyzései az olvasási, írási és végrehajtási engedélyeket biztosítanak a tulajdonosnak a tulajdonos csoport számára, és csak olvasási és végrehajtási engedélyeket biztosítanak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad a (z) "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "objektumhoz olvasási és végrehajtási engedélyekkel. Ezek a bejegyzések az olvasási, írási és végrehajtási engedélyeket adják meg a tulajdonos számára, és csak olvasási és végrehajtási engedélyeket biztosítanak a tulajdonos csoportnak, és minden más felhasználónak nincs hozzáférése. Ebben a példában az utolsó ACL-bejegyzés egy adott felhasználót ad a (z) "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "objektumhoz olvasási és végrehajtási engedélyekkel.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Ha egy köteg méretének megadásával szeretné megtekinteni, hogy a rendszer rekurzív módon dolgozza fel az ACL-eket a kötegekben, tekintse meg a Python- [mintát](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="update-an-acl-recursively"></a>ACL rekurzív frissítése

*Ha módosít egy ACL* -t, akkor az ACL helyett módosítania kell az ACL-t. Hozzáadhat például egy új rendszerbiztonsági tag-t az ACL-hez anélkül, hogy ez hatással lenne az ACL-ben felsorolt többi rendszerbiztonsági tagra is.  Ha a frissítés helyett az ACL-t szeretné cserélni, tekintse meg a jelen cikk [ACL rekurzív beállítása](#set-an-acl-recursively) című szakaszát. 

Az ACL frissítéséhez hozzon létre egy új ACL-objektumot a frissíteni kívánt ACL-bejegyzéssel, majd használja ezt az objektumot az ACL frissítése műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg a frissítendő ACL-bejegyzéseket.

Ez a szakasz példákat tartalmaz az ACL frissítésére.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Frissítsen egy ACL-t rekurzív módon az  **Update-AzDataLakeGen2AclRecursive** parancsmag használatával. 

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Ha frissíteni szeretné az **alapértelmezett** ACL-bejegyzést, használja a **-defaultscope tulajdonságnak** paramétert a **set-AzDataLakeGen2ItemAclObject** parancs futtatásakor. Példa: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

Ha egy olyan példát szeretne látni, amely egy köteg méretének megadásával rekurzívan frissíti a ACL-eket, tekintse meg az [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) Reference című cikket.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Frissítsen egy ACL-t rekurzív módon az az  [Storage FS Access Update-rekurzív](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) parancs használatával. 

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Ha frissíteni szeretné az **alapértelmezett** ACL-bejegyzést, adja hozzá az előtagot az `default:` egyes bejegyzésekhez. Például: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

### <a name="net"></a>[.NET](#tab/dotnet)

Az ACL-t rekurzív módon frissítheti a **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** metódus meghívásával.  Adja át ezt a metódust a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [listájához](/dotnet/api/system.collections.generic.list-1) . Mindegyik [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) egy ACL-bejegyzést definiál. 

Ha frissíteni szeretné az **alapértelmezett** ACL-bejegyzést, akkor a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [PathAccessControlItem. defaultscope tulajdonságnak](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) tulajdonságát állítsa **igaz** értékre. 

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít. Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy frissíteni kell-e az alapértelmezett ACL-t. Ezt a paramétert használja a rendszer a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)konstruktorában.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Ha egy olyan példát szeretne látni, amely egy köteg méretének megadásával rekurzívan frissíti a ACL-eket, tekintse meg a .NET- [mintát](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Az ACL-t rekurzív módon frissítheti a **DataLakeDirectoryClient. updateAccessControlRecursive** metódus meghívásával.  Adja át ezt a metódust a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) objektumok [listájához](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) . Mindegyik [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) egy ACL-bejegyzést definiál. 

Ha frissíteni szeretné az **alapértelmezett** ACL-bejegyzést, akkor a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) **setDefaultScope** metódusát kell átadnia, és az értéke TRUE ( **igaz**) lehet. 

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít. Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy frissíteni kell-e az alapértelmezett ACL-t. Ez a paraméter a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) **setDefaultScope** metódusának hívásakor használatos. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Az ACL-t rekurzív módon frissítheti a **DataLakeDirectoryClient.update_access_control_recursive** metódus meghívásával. Ha szeretné frissíteni az **alapértelmezett** ACL-bejegyzést, adja hozzá a karakterláncot `default:` az egyes ACL-bejegyzések karakterláncának elejéhez. 

Ez a példa egy írási engedéllyel rendelkező ACL-bejegyzést frissít.

Ez a példa egy nevű könyvtár ACL-listáját állítja be `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `is_default_scope` el, amely megadja, hogy frissíteni kell-e az alapértelmezett ACL-t. Ha ez a paraméter `True` , a frissített ACL-bejegyzés a karakterlánc előtt szerepel `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Ha egy köteg méretének megadásával szeretné megtekinteni, hogy a rendszer rekurzív módon dolgozza fel az ACL-eket a kötegekben, tekintse meg a Python- [mintát](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="remove-acl-entries-recursively"></a>ACL-bejegyzések rekurzív eltávolítása

Egy vagy több ACL-bejegyzést rekurzív módon eltávolíthat. Az ACL-bejegyzések eltávolításához hozzon létre egy új ACL-objektumot az ACL-bejegyzés eltávolításához, majd használja ezt az objektumot az ACL eltávolítása műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg az eltávolítandó ACL-bejegyzéseket. 

Ez a szakasz példákat tartalmaz az ACL eltávolítására. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Távolítsa el az ACL-bejegyzéseket a **Remove-AzDataLakeGen2AclRecursive** parancsmag használatával. 

Ez a példa egy ACL-bejegyzést távolít el a tároló gyökérkönyvtárában.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Ha el szeretné távolítani az **alapértelmezett** ACL-bejegyzést, használja a **-defaultscope tulajdonságnak** paramétert a **set-AzDataLakeGen2ItemAclObject** parancs futtatásakor. Példa: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Ha egy batch-méret megadásával szeretné megtekinteni a ACL-ek rekurzív eltávolítását a batchs szolgáltatásban, tekintse meg a [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) Reference című cikket.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Távolítsa el az ACL-bejegyzéseket az az [Storage FS Access Remove-rekurzív](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) parancs használatával. 

Ez a példa egy ACL-bejegyzést távolít el a tároló gyökérkönyvtárában.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Ha el szeretné távolítani az **alapértelmezett** ACL-bejegyzést, adja hozzá az előtagot az `default:` egyes bejegyzésekhez. Például: `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="net"></a>[.NET](#tab/dotnet)

Távolítsa el az ACL-bejegyzéseket a **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** metódus meghívásával. Adja át ezt a metódust a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [listájához](/dotnet/api/system.collections.generic.list-1) . Mindegyik [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) egy ACL-bejegyzést definiál. 

Ha el szeretné távolítani az **alapértelmezett** ACL-bejegyzést, akkor a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) [PathAccessControlItem. defaultscope tulajdonságnak](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) tulajdonságát állítsa **igaz** értékre. 

Ez a példa egy ACL-bejegyzést távolít el a nevű könyvtár ACL-listájából `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy el kell-e távolítani a bejegyzést az alapértelmezett ACL-ből. Ezt a paramétert használja a rendszer a [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)konstruktorában.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Ha egy köteg méretének megadásával szeretné megtekinteni egy példát, amely eltávolítja a hozzáférés-vezérlési listákat a kötegekben, tekintse meg a .NET- [mintát](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Távolítsa el az ACL-bejegyzéseket a **DataLakeDirectoryClient. removeAccessControlRecursive** metódus meghívásával. Adja át ezt a metódust a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) objektumok [listájához](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) . Mindegyik [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) egy ACL-bejegyzést definiál. 

Ha el kívánja távolítani egy **alapértelmezett** ACL-bejegyzést, akkor a PathAccessControlEntry **setDefaultScope** metódusát kell [](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) megadnia, és az értéke **true (igaz**) lehet.  

Ez a példa egy ACL-bejegyzést távolít el a nevű könyvtár ACL-listájából `my-parent-directory` . Ez a metódus egy nevű logikai paramétert fogad `isDefaultScope` el, amely megadja, hogy el kell-e távolítani a bejegyzést az alapértelmezett ACL-ből. Ez a paraméter a [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) **setDefaultScope** metódusának hívásakor használatos.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

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

---

## <a name="recover-from-failures"></a>Helyreállítás hibákból

A futásidejű vagy az engedélyekkel kapcsolatos hibák merülhetnek fel. Futásidejű hibák esetén indítsa újra a folyamatot az elejétől. Engedélyekkel kapcsolatos hibák akkor fordulhatnak elő, ha a rendszerbiztonsági tag nem rendelkezik megfelelő engedélyekkel egy olyan könyvtár vagy fájl hozzáférés-vezérlési listájának módosításához, amely a címtár-hierarchiában van módosítva. Oldja meg az engedélyekkel kapcsolatos problémát, majd a folytatási token használatával folytassa a folyamatot a meghibásodási pontról, vagy indítsa újra a folyamatot az elejétől. A folytatási tokent nem kell használnia, ha az elejéről szeretne újraindulni. Az ACL-bejegyzéseket a negatív hatás nélkül is újra alkalmazhatja.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a példa az eredményeket a változóhoz adja vissza, majd a hibás bejegyzéseket a formázott táblába.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

A táblázat kimenete alapján kijavíthatja az engedélyek hibáit, majd folytathatja a végrehajtást a folytatási token használatával.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Ha egy köteg méretének megadásával szeretné megtekinteni egy olyan példát, amely az ACL-eket rekurzív módon állítja be a kötegekben, tekintse meg a [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) című cikket.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Hiba esetén visszaállíthat egy folytatási tokent a paraméter értékre állításával `--continue-on-failure` `false` . A hibák elhárítása után folytathatja a folyamatot a meghibásodási pontról, ha újra futtatja a parancsot, majd a `--continuation` paramétert a folytatási tokenre állítja be. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

Ez a példa egy folytatási tokent ad vissza egy hiba esetén. Az alkalmazás a hiba megoldását követően újra meghívhatja ezt a metódust, és a folytatási tokent adja át. Ha ezt a példát a rendszer első alkalommal hívja meg, akkor az alkalmazás a `null` folytatási jogkivonat paraméter értékét átadja. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Ha egy köteg méretének megadásával szeretné megtekinteni egy olyan példát, amely az ACL-eket rekurzívan állítja be kötegekben, tekintse meg a .NET- [mintát](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Ez a példa egy folytatási tokent ad vissza egy hiba esetén. Az alkalmazás a hiba megoldását követően újra meghívhatja ezt a metódust, és a folytatási tokent adja át. Ha ezt a példát a rendszer első alkalommal hívja meg, akkor az alkalmazás a `null` folytatási jogkivonat paraméter értékét átadja. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Ez a példa egy folytatási tokent ad vissza egy hiba esetén. Az alkalmazás a hiba megoldását követően újra meghívhatja ezt a metódust, és a folytatási tokent adja át. Ha ezt a példát a rendszer első alkalommal hívja meg, akkor az alkalmazás a ``None`` folytatási jogkivonat paraméter értékét átadja. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Ha egy köteg méretének megadásával szeretné megtekinteni, hogy a rendszer rekurzív módon dolgozza fel az ACL-eket a kötegekben, tekintse meg a Python- [mintát](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

Ha azt szeretné, hogy a folyamat az engedélyekkel kapcsolatos hibák mellett befejeződjön, megadhatja a következőt:.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a példa a `ContinueOnFailure` paramétert használja, hogy a végrehajtás akkor is folytatódjon, ha a művelet engedélyekkel kapcsolatos hibát észlel. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Ha egy köteg méretének megadásával szeretné megtekinteni egy olyan példát, amely az ACL-eket rekurzív módon állítja be a kötegekben, tekintse meg a [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) című cikket.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A folyamat megszakítás nélküli befejezésének biztosításához állítsa a paramétert a következőre: `--continue-on-failure` `true` . 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

Annak érdekében, hogy a folyamat befejeződjön, adjon meg egy **AccessControlChangedOptions** objektumot, és állítsa be az objektum **ContinueOnFailure** tulajdonságát a következőre: ``true`` .

Ez a példa az ACL-bejegyzéseket rekurzív módon állítja be. Ha ez a kód egy engedélyezési hibát észlel, akkor a hibát rögzíti, és folytatja a végrehajtást. Ez a példa a hibák számát jeleníti meg a konzolon. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Ha egy köteg méretének megadásával szeretné megtekinteni egy olyan példát, amely az ACL-eket rekurzívan állítja be kötegekben, tekintse meg a .NET- [mintát](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Annak érdekében, hogy a folyamat zavartalanul befejeződjön, hívja meg a [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) objektum **setContinueOnFailure** metódusát, és adjon meg egy **igaz** értéket.

Ez a példa az ACL-bejegyzéseket rekurzív módon állítja be. Ha ez a kód egy engedélyezési hibát észlel, akkor a hibát rögzíti, és folytatja a végrehajtást. Ez a példa a hibák számát jeleníti meg a konzolon. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

### <a name="python"></a>[Python](#tab/python)

Annak érdekében, hogy a folyamat befejeződjön, ne adjon meg folytatási jogkivonatot a **DataLakeDirectoryClient.set_access_control_recursive** metódusnak.

Ez a példa az ACL-bejegyzéseket rekurzív módon állítja be. Ha ez a kód egy engedélyezési hibát észlel, akkor a hibát rögzíti, és folytatja a végrehajtást. Ez a példa a hibák számát jeleníti meg a konzolon. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Ha egy köteg méretének megadásával szeretné megtekinteni, hogy a rendszer rekurzív módon dolgozza fel az ACL-eket a kötegekben, tekintse meg a Python- [mintát](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="resources"></a>Források

Ez a szakasz a kódtárak és a kód mintáinak hivatkozásait tartalmazza.

#### <a name="libraries"></a>Kódtárak

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [Azure CLI](/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D)
- [REST](/rest/api/storageservices/datalakestoragegen2/path/update)

#### <a name="code-samples"></a>Kódminták

- PowerShell: [readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)-  |  [minta](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- Azure CLI: [minta](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- NET: [readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%2fSc2aDVW3De4A%2fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [minta](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [minta](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)

## <a name="best-practice-guidelines"></a>Ajánlott eljárási irányelvek

Ez a szakasz az ACL-ek rekurzív beállítására vonatkozó ajánlott eljárásokat ismerteti. 

#### <a name="handling-runtime-errors"></a>Futásidejű hibák elhárítása

Futásidejű hiba számos okból történhet (például leállás vagy ügyfél-csatlakozási probléma). Ha futásidejű hibát tapasztal, indítsa újra a rekurzív ACL-folyamatot. Az ACL-ek újra alkalmazhatók az elemekre anélkül, hogy negatív hatással lenne. 

#### <a name="handling-permission-errors-403"></a>Engedélyek kezelésére vonatkozó hibák (403)

Ha egy rekurzív ACL-folyamat futtatásakor hozzáférés-vezérlési kivételt tapasztal, előfordulhat, hogy az AD [rendszerbiztonsági tag](../../role-based-access-control/overview.md#security-principal) nem rendelkezik megfelelő engedéllyel ahhoz, hogy ACL-t alkalmazzon egy vagy több alárendelt elemhez a címtár-hierarchiában. Ha egy engedélyezési hiba történik, a folyamat leáll, és a folytatási jogkivonat van megadva. Javítsa ki az engedélyekkel kapcsolatos problémát, majd a folytatási token használatával dolgozza fel a fennmaradó adatkészletet. A már sikeresen feldolgozott címtárakat és fájlokat nem kell újra feldolgozni. Azt is megteheti, hogy újraindítja a rekurzív ACL-folyamatot. Az ACL-ek újra alkalmazhatók az elemekre anélkül, hogy negatív hatással lenne. 

#### <a name="credentials"></a>Hitelesítő adatok 

Azt javasoljuk, hogy hozzon létre egy Azure AD rendszerbiztonsági tag, amely hozzá lett rendelve a [Storage blob-adat tulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepköréhez a cél Storage-fiók vagy tároló hatókörében. 

#### <a name="performance"></a>Teljesítmény 

A késés csökkentése érdekében javasoljuk, hogy futtassa a rekurzív ACL-folyamatot egy olyan Azure-beli virtuális gépen (VM), amely ugyanabban a régióban található, mint a Storage-fiók. 

#### <a name="acl-limits"></a>ACL-korlátok

A címtárra vagy fájlra érvényes ACL-ek maximális száma 32 hozzáférési ACL-ek és 32 alapértelmezett ACL-ek. További információért lásd: [Hozzáférés-vezérlés az Azure Data Lake Storage Gen2-ben](./data-lake-storage-access-control.md).

## <a name="see-also"></a>Lásd még

- [Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben](./data-lake-storage-access-control.md)
- [Ismert problémák](data-lake-storage-known-issues.md)
