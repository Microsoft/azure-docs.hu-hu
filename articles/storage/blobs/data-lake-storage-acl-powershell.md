---
title: A Azure Data Lake Storage Gen2 ACL-ek kezelése a PowerShell használatával
description: A PowerShell-parancsmagok használatával kezelheti a hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b606e69baec8d159a6a3fa7373500176260ef0d7
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654354"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>A Azure Data Lake Storage Gen2 ACL-ek kezelése a PowerShell használatával

Ez a cikk bemutatja, hogyan lehet a PowerShell használatával beolvasni, beállítani és frissíteni a címtárak és fájlok hozzáférés-vezérlési listáját. 

Az ACL öröklése már elérhető az új alárendelt elemekhez, amelyeket a rendszer a szülő címtárban hozott létre. Az ACL-eket rekurzív módon is hozzáadhatja, frissítheti és eltávolíthatja a szülő könyvtár meglévő alárendelt elemein anélkül, hogy ezeket a módosításokat külön kellene elvégeznie az egyes alárendelt elemek esetében. 

[Hivatkozás](/powershell/module/Az.Storage/)  |  [Rekurzív ACL-minták](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)  |  [Visszajelzés küldése](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

- Az Azure CLI verziója `2.6.0` vagy újabb.

- A következő biztonsági engedélyek egyike:

  - Egy kiépített Azure Active Directory (AD) [rendszerbiztonsági tag](../../role-based-access-control/overview.md#security-principal) , amely a [tároló blob-adattulajdonosi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) szerepkört rendelte hozzá a cél tároló, a szülő erőforráscsoport vagy az előfizetés hatókörében.  

  - Annak a tárolónak vagy könyvtárnak a tulajdonosa, amelyre az ACL-beállításokat alkalmazni kívánja. Az ACL-ek rekurzív beállításához a cél tárolóban vagy a címtárban található összes alárendelt elem szerepel.
  
  - Storage-fiók kulcsa.

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

1. A következő parancs használatával ellenőrizze, hogy a telepített PowerShell `5.1` -verzió vagy magasabb-e.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   A PowerShell verziójának frissítéséhez lásd: a [meglévő Windows PowerShell frissítése](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)

2. Telepítse **az az. Storage** modult.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   A PowerShell-modulok telepítésével kapcsolatos további információkért lásd: [a Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

Válassza ki, hogy a parancsok Hogyan kapják meg az engedélyeket a Storage-fióknak. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>1. lehetőség: Engedélyezés beszerzése Azure Active Directory (AD) használatával

> [!NOTE]
> Ha Azure Active Directory (Azure AD) használatával engedélyezi a hozzáférést, akkor győződjön meg arról, hogy a rendszerbiztonsági tag hozzá lett rendelve a [Storage blob-adat tulajdonosi szerepköréhez](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Ha többet szeretne megtudni az ACL-engedélyek alkalmazásáról és az azok módosításának hatásairól, tekintse meg a  [Azure Data Lake Storage Gen2 hozzáférés-vezérlési modelljét](./data-lake-storage-access-control-model.md).

Ezzel a módszerrel a rendszer biztosítja, hogy a felhasználói fiókja rendelkezik a megfelelő Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) hozzárendelésekkel és ACL-engedélyekkel.

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

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>2. lehetőség: engedély beszerzése a Storage-fiók kulcsa alapján

Ezzel a módszerrel a rendszeren nem ellenőrizhető az Azure-RBAC vagy az ACL-engedélyek. A Storage-fiók környezetének beszerzése a fiók kulcsa alapján.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="get-acls"></a>ACL-ek lekérése

Egy könyvtár vagy fájl hozzáférés-vezérlési listájának lekérése a `Get-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa egy **tároló** gyökérkönyvtárának ACL-listáját jeleníti meg, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Ez a példa egy **könyvtár** ACL-listáját kéri le, majd kiírja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Ez a példa egy **fájl** ACL-listáját kéri le, majd kiírja az ACL-t a konzolra.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Az alábbi képen egy könyvtár ACL-listájának beolvasása után a kimenet látható.

![ACL-kimenet beolvasása a címtárhoz](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Ebben a példában a tulajdonos felhasználó olvasási, írási és végrehajtási engedélyekkel rendelkezik. A tulajdonos csoport csak olvasási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listával kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

## <a name="set-acls"></a>ACL-ek beállítása

Az ACL *beállításakor* a teljes ACL-t **lecseréli** , beleértve az összes bejegyzését. Ha módosítani szeretné egy rendszerbiztonsági tag hozzáférési szintjét, vagy egy új rendszerbiztonsági tag hozzáadása az ACL-hez anélkül, hogy ez hatással lenne a többi meglévő bejegyzésre, *frissítenie* kell az ACL-t. Ha az ACL-t nem a helyett szeretné frissíteni, tekintse meg a jelen cikk [frissítési ACL](#update-acls) -EK című szakaszát.  

Ha úgy dönt, hogy *Beállítja* az ACL-t, hozzá kell adnia egy bejegyzést a tulajdonos felhasználóhoz, egy bejegyzést a tulajdonos csoport számára, valamint egy bejegyzést az összes többi felhasználó számára. Ha többet szeretne megtudni a tulajdonos felhasználóról, a tulajdonos csoportról és az összes többi felhasználóról, tekintse meg a [felhasználók és az identitások](data-lake-storage-access-control.md#users-and-identities)című témakört.

Ez a szakasz a következőket mutatja be:

- ACL beállítása
- ACL-ek rekurzív beállítása

### <a name="set-an-acl"></a>ACL beállítása

A `set-AzDataLakeGen2ItemAclObject` parancsmag használatával hozzon létre egy ACL-t a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók számára. Ezután a parancsmag használatával `Update-AzDataLakeGen2Item` véglegesítse az ACL-t.

Ez a példa egy **tároló** gyökérkönyvtárában lévő ACL-t állítja be a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók számára, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Ez a példa a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók **könyvtárának** ACL-listáját állítja be, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, használja a **-defaultscope tulajdonságnak** paramétert a **set-AzDataLakeGen2ItemAclObject** parancs futtatásakor. Példa: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Ez a példa egy fájl ACL- **fájlját** állítja be a tulajdonos felhasználó, tulajdonos csoport vagy más felhasználók számára, majd kinyomtatja az ACL-t a konzolra.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> Ha **alapértelmezett** ACL-bejegyzést kíván beállítani, használja a **-defaultscope tulajdonságnak** paramétert a **set-AzDataLakeGen2ItemAclObject** parancs futtatásakor. Példa: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Az alábbi képen egy fájl ACL-listájának beállítása után a kimenet látható.

![A fájl ACL-kimenetének beolvasása](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Ebben a példában a tulajdonos felhasználó és a tulajdonos csoport csak olvasási és írási engedéllyel rendelkezik. Minden más felhasználó írási és végrehajtási engedélyekkel rendelkezik. A hozzáférés-vezérlési listával kapcsolatos további információkért lásd: [hozzáférés-vezérlés Azure Data Lake Storage Gen2ban](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>ACL-ek rekurzív beállítása

Az ACL-ek rekurzív beállítása a **set-AzDataLakeGen2AclRecursive** parancsmag használatával.

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

## <a name="update-acls"></a>ACL-ek frissítése

*Ha módosít egy ACL* -t, akkor az ACL helyett módosítania kell az ACL-t. Hozzáadhat például egy új rendszerbiztonsági tag-t az ACL-hez anélkül, hogy ez hatással lenne az ACL-ben felsorolt többi rendszerbiztonsági tagra is.  Ha a frissítés helyett az ACL-t szeretné cserélni, tekintse meg a jelen cikk ACL-ek [beállítása](#set-acls) című szakaszát.

Az ACL frissítéséhez hozzon létre egy új ACL-objektumot a frissíteni kívánt ACL-bejegyzéssel, majd használja ezt az objektumot az ACL frissítése műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg a frissítendő ACL-bejegyzéseket.

Ez a szakasz a következőket mutatja be:

- ACL frissítése
- Az ACL-ek rekurzív frissítése

### <a name="update-an-acl"></a>ACL frissítése

Először kérje le az ACL-t. Ezután használja a `set-AzDataLakeGen2ItemAclObject` parancsmagot egy ACL-bejegyzés hozzáadásához vagy frissítéséhez. Az `Update-AzDataLakeGen2Item` ACL-t a parancsmag használatával véglegesítheti.

Ez a példa egy felhasználó **címtárában** lévő ACL-t hozza létre vagy frissíti.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Ha frissíteni szeretné az **alapértelmezett** ACL-bejegyzést, használja a **-defaultscope tulajdonságnak** paramétert a **set-AzDataLakeGen2ItemAclObject** parancs futtatásakor. Példa: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Az ACL-ek rekurzív frissítése

Az ACL-ek rekurzív frissítése az  **Update-AzDataLakeGen2AclRecursive** parancsmag használatával.

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

## <a name="remove-acl-entries"></a>ACL-Bejegyzések eltávolítása

Ez a szakasz a következőket mutatja be:

- ACL-bejegyzés eltávolítása
- ACL-bejegyzések rekurzív eltávolítása

### <a name="remove-an-acl-entry"></a>ACL-bejegyzés eltávolítása

Ez a példa egy meglévő ACL-ből származó bejegyzést távolít el.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>ACL-bejegyzések rekurzív eltávolítása

Egy vagy több ACL-bejegyzést rekurzív módon eltávolíthat. Az ACL-bejegyzések eltávolításához hozzon létre egy új ACL-objektumot az ACL-bejegyzés eltávolításához, majd használja ezt az objektumot az ACL eltávolítása műveletben. Ne szerezze be a meglévő ACL-t, csak adja meg az eltávolítandó ACL-bejegyzéseket.

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

## <a name="recover-from-failures"></a>Helyreállítás hibákból

A hozzáférés-vezérlési listák rekurzív módosításakor előfordulhatnak futásidejű vagy engedélyezési hibák. 

Futásidejű hibák esetén indítsa újra a folyamatot az elejétől. Engedélyekkel kapcsolatos hibák akkor fordulhatnak elő, ha a rendszerbiztonsági tag nem rendelkezik megfelelő engedélyekkel egy olyan könyvtár vagy fájl hozzáférés-vezérlési listájának módosításához, amely a címtár-hierarchiában van módosítva. Oldja meg az engedélyekkel kapcsolatos problémát, majd a folytatási token használatával folytassa a folyamatot a meghibásodási pontról, vagy indítsa újra a folyamatot az elejétől. A folytatási tokent nem kell használnia, ha az elejéről szeretne újraindulni. Az ACL-bejegyzéseket a negatív hatás nélkül is újra alkalmazhatja.

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

Ha azt szeretné, hogy a folyamat az engedélyekkel kapcsolatos hibák mellett befejeződjön, megadhatja a következőt:.

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

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Lásd még

- [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage)
- [Hozzáférés-vezérlési modell Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Hozzáférés-vezérlési listák (ACL-ek) Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
