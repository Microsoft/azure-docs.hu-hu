---
title: 'Az adatkezeléshez használja a PowerShellt: Azure Data Lake Storage Gen2'
description: PowerShell-parancsmagok használatával kezelheti a olyan Storage-fiókokban lévő címtárakat és fájlokat, amelyeken engedélyezve van a hierarchikus névtér.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe7ad949d7301a035eb17d2b4d8d678dfb2e0546
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650200"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Könyvtárak és fájlok kezelése a PowerShell használatával Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan lehet a PowerShell használatával olyan könyvtárakat és fájlokat létrehozni és kezelni, amelyek hierarchikus névteret tartalmazó Storage-fiókokban vannak.

A címtárak és fájlok hozzáférés-vezérlési listái (ACL) beszerzésével, beállításával és frissítésével kapcsolatos további információkért lásd: a [PowerShell használata a ACL-ek kezeléséhez Azure Data Lake Storage Gen2](data-lake-storage-acl-powershell.md).

[Hivatkozás](/powershell/module/Az.Storage/)  |  [Gen1 a Gen2-megfeleltetéshez](#gen1-gen2-map)  |  [Visszajelzés küldése](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér. Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

- A .NET-keretrendszer 4.7.2 vagy újabb. Lásd: [.NET-keretrendszer letöltése](https://dotnet.microsoft.com/download/dotnet-framework).

- PowerShell `5.1` -verzió vagy újabb.

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

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>1. lehetőség: Engedélyezés beszerzése Azure Active Directory (Azure AD) használatával

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

## <a name="create-a-container"></a>Tároló létrehozása

A tároló fájlrendszerként működik a fájlok számára. A parancsmag használatával létrehozhat egyet `New-AzStorageContainer` . 

Ez a példa egy nevű tárolót hoz létre `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-hivatkozást a `New-AzDataLakeGen2Item` parancsmag használatával. 

Ez a példa egy nevű könyvtárat helyez `my-directory` el egy tárolóhoz.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Ez a példa hozzáadja ugyanazt a könyvtárat, de beállítja az engedélyeket, a umask, a tulajdonság értékeit és a metaadatok értékét is. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Könyvtár tulajdonságainak megjelenítése

Ez a példa egy könyvtárat kap a `Get-AzDataLakeGen2Item` parancsmag használatával, majd kinyomtatja a tulajdonságokat a konzolra.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

> [!NOTE]
> A tároló gyökérkönyvtárának beszerzéséhez hagyja ki a `-Path` paramétert.

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat a `Move-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa átnevez egy könyvtárat a név alapján `my-directory` `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Használja a `-Force` paramétert, ha kérés nélkül szeretné felülírni.

Ez a példa egy nevű könyvtárat helyez át `my-directory` egy nevű alkönyvtárba `my-directory-2` `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Könyvtár törlése

Törölje a könyvtárat a parancsmag használatával `Remove-AzDataLakeGen2Item` .

Ez a példa törli a nevű könyvtárat `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

A (z `-Force` ) paraméterrel a fájl parancssor nélkül is eltávolítható.

## <a name="download-from-a-directory"></a>Letöltés egy címtárból

Töltse le a fájlt egy könyvtárból a `Get-AzDataLakeGen2ItemContent` parancsmag használatával.

Ez a példa egy nevű könyvtárból tölt le egy nevű fájlt `upload.txt` `my-directory` .

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Egy könyvtár tartalmának listázása a parancsmag használatával `Get-AzDataLakeGen2ChildItem` . A nem kötelező paraméterrel `-OutputUserPrincipalName` beolvashatja a felhasználók nevét (az objektum azonosítója helyett).

Ez a példa egy nevű könyvtár tartalmát sorolja fel `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

A következő példa a `ACL` `Permissions` `Group` `Owner` címtár egyes elemeinek,, és tulajdonságait sorolja fel. A `-FetchProperty` tulajdonság értékének beolvasásához a paraméter szükséges `ACL` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> A tároló gyökérkönyvtárának tartalmának listázásához hagyja ki a `-Path` paramétert.

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Töltse fel a fájlt egy könyvtárba a parancsmag használatával `New-AzDataLakeGen2Item` .

Ez a példa egy nevű fájlt tölt fel egy nevű `upload.txt` könyvtárba `my-directory` . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Ez a példa feltölti ugyanazt a fájlt, de ezt követően beállítja a célfájl engedélyeit, umask, tulajdonságának értékeit és metaadat-értékeit. Ez a példa ezeket az értékeket is kiírja a konzolra.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Ha fel szeretne tölteni egy fájlt a tároló gyökérkönyvtárában, hagyja ki a `-Path` paramétert.

## <a name="show-file-properties"></a>Fájl tulajdonságainak megjelenítése

Ez a példa egy fájlt kap a `Get-AzDataLakeGen2Item` parancsmag használatával, majd kinyomtatja a tulajdonságokat a konzolra.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Fájl törlése

Fájl törlése a `Remove-AzDataLakeGen2Item` parancsmag használatával.

Ez a példa törli a nevű fájlt `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

A (z `-Force` ) paraméterrel a fájl parancssor nélkül is eltávolítható.

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Gen1 a Gen2-megfeleltetéshez

A következő táblázat azt mutatja be, hogy a parancsmagok hogyan használhatók a Data Lake Storage Gen1 leképezéshez a Data Lake Storage Gen2 parancsmagokhoz.

|Data Lake Storage Gen1 parancsmag| Data Lake Storage Gen2 parancsmag| Jegyzetek |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Alapértelmezés szerint a Get-AzDataLakeGen2ChildItem parancsmag csak az első szintű alárendelt elemeket sorolja fel. A-recurse paraméter rekurzív módon sorolja fel a alárendelt elemeket. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|A Get-AzDataLakeGen2Item parancsmag kimeneti elemei a következő tulajdonságokkal rendelkeznek: ACL, tulajdonos, csoport, engedély.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|A Get-AzDataLakeGen2FileContent parancsmag fájl tartalmát letölti a helyi fájlba.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Ez a parancsmag egy helyi fájlból tölti fel az új fájl tartalmát.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Az Update-AzDataLakeGen2Item parancsmag csak egyetlen elem frissítését frissíti, és nem rekurzív módon. Ha rekurzív módon szeretné frissíteni a frissítést, sorolja fel az elemeket az Get-AzDataLakeStoreChildItem parancsmag használatával, majd a folyamatot a Update-AzDataLakeGen2Item parancsmaghoz.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Az Get-AzDataLakeGen2Item parancsmag hibát jelez, ha az adott objektum nem létezik.|

## <a name="see-also"></a>Lásd még

- [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage)
