---
title: Rövid útmutató az Azure-fájlmegosztásoknak az Azure PowerShell-lel történő felügyeletéhez
description: Ebből a rövid útmutatóból megtudhatja, hogyan felügyelheti az Azure-fájlmegosztásokat az Azure PowerShell-lel.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8b4bd9ece5f010f1294356ad4673543834e5076a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94626911"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Rövid útmutató: Azure-fájlmegosztások létrehozása és felügyelete az Azure PowerShell-lel 
Ez az útmutató az [Azure-fájlmegosztások](storage-files-introduction.md) PowerShell-lel való használatának alapvető lépéseit mutatja be. Az Azure-fájlmegosztások nem különböznek más fájlmegosztásoktól, a tárolásuk azonban a felhőben történik, és az Azure platform nyújt számukra támogatást. Az Azure-fájlmegosztás támogatja az iparági szabványnak megfelelő SMB-protokollt, a hálózati fájlrendszer (NFS) protokollját (előzetes verzió), és lehetővé teszi a fájlmegosztás használatát több gépen, alkalmazásban és példányban. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha helyileg szeretné telepíteni és használni a PowerShellt, az útmutatóhoz az Azure PowerShell-modul az 0,7-es vagy újabb verziójára lesz szükség. A jelenleg futtatott Azure PowerShell-modul verziójának kiderítéséhez futtassa a következő parancsot: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a `Login-AzAccount` parancsot is futtatnia kell az Azure-fiókba való bejelentkezéshez.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Azure-erőforráscsoport használatával, létrehozhat egy újat a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmaggal. 

A következő példában létrehozunk egy *myResourceGroup* nevű ERŐFORRÁSCSOPORTOT az USA 2. nyugati régiójában:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
A Storage-fiók az Azure-fájlmegosztás üzembe helyezéséhez használható tároló megosztott készlete. Egy tárfiók korlátlan számú megosztást tartalmazhat, egy megosztás pedig korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig. Ez a példa egy általános célú 2-es verziójú (GPv2 Storage-fiókot) hoz létre, amely a szabványos Azure-fájlmegosztás vagy más tárolási erőforrások, például Blobok vagy várólisták tárolásához használható a merevlemez-meghajtó (HDD) rotációs adathordozóján. A Azure Files támogatja a prémium SSD-meghajtókat is. a prémium szintű Azure-fájlmegosztás FileStorage-fiókokban is létrehozhatók.

Ez a példa egy Storage-fiókot hoz létre a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmag használatával. A tárfiók neve *mystorageaccount\<random number>*, a tárfiókra mutató hivatkozást pedig a **$storageAcct** változó tárolja. A tárfiókok névnek egyedinek kell lenniük, ezért a `Get-Random` paranccsal fűzzön számot a névhez. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> Az 5 TiB-nál nagyobb (legfeljebb 100 TiB-ig terjedő) megosztások csak a helyileg redundáns (LRS) és a zóna redundáns (ZRS) Storage-fiókokban érhetők el. A Geo-redundáns (GRS) vagy a Geo-Zone-redundáns (GZRS) Storage-fiók létrehozásához távolítsa el a `-EnableLargeFileShare` paramétert.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Most létrehozhatja az első Azure-fájlmegosztását. A [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) parancsmag használatával létrehozhat fájlmegosztást. Ez a példa egy `myshare` nevű fájlmegosztást hoz létre.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Az Azure Files két módszert biztosít a fájloknak és mappáknak az Azure-fájlmegosztásban való használatához: az iparági szabvány [Server Message Block (SMB) protokollt](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) és a [Fájl REST protokollt](/rest/api/storageservices/file-service-rest-api). 

A fájlmegosztás SMB-vel való csatlakoztatásához tekintse meg a használt operációs rendszernek megfelelő dokumentumot az alábbiak közül:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Azure-fájlmegosztások használata a Fájl REST protokollal 
A file REST protokoll használatával közvetlenül is dolgozhat (például a REST HTTP-hívások saját kezűleg), de a fájl REST protokoll használatának leggyakoribb módja a Azure PowerShell modul, az [Azure CLI](storage-how-to-use-files-cli.md)vagy egy Azure Storage SDK használata, amely az Ön által választott parancsfájlkezelési/programozási nyelvben egy szép burkolót biztosít a fájl Rest protokollja körül.  

Az Azure-fájlmegosztást a legtöbb esetben az SMB protokollon keresztül fogja használni, mivel ez lehetővé teszi a mások által is vélhetően használt meglévő alkalmazások és eszközök használatát. A Fájl REST API használata azonban számos előnnyel jár az SMB-vel szemben, például a következő esetekben:

- A fájlmegosztást a PowerShell Cloud Shellben böngészi (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 
- Olyan értéknövelt szolgáltatást hoz létre, amely számos Azure-fájlmegosztás esetében fog működni, például biztonsági mentési vagy víruskereső-vizsgálatok végrehajtásakor.

Az alábbi példák bemutatják, hogyan kezelheti az Azure-fájlmegosztást a fájl REST protokollal a Azure PowerShell modul használatával. A `-Context` paraméter a Storage-fiók kulcsának beolvasására szolgál a megadott műveletek a fájlmegosztás alapján történő végrehajtásához. A Storage-fiók kulcsainak lekéréséhez a Storage-fiókban rendelkeznie kell Azure-szerepkörrel `Owner` .

#### <a name="create-directory"></a>Könyvtár létrehozása
Ha a *myDirectory* nevű új könyvtárat szeretné létrehozni az Azure-fájlmegosztás gyökerében, használja a [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) parancsmagot.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Fájl feltöltése
A fájlok a [set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) parancsmag használatával történő feltöltésének bemutatásához először létre kell hoznia egy fájlt a PowerShell-Cloud Shell a feltöltéshez. 

Ez a példa az aktuális dátumot és időt helyezi az ideiglenes meghajtón lévő új fájlba, majd feltölti a fájlt a fájlmegosztásra.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Ha helyileg futtatja a PowerShellt, helyettesítse be a `~/CloudDrive/` elérési utat a gép egy meglévő elérési útjával.

A fájl feltöltése után a [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) parancsmaggal ellenőrizze, hogy a fájl fel lett-e töltve az Azure-fájlmegosztást. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Fájl letöltése
A [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) parancsmag használatával letöltheti az imént feltöltött fájl másolatát a Cloud shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

A fájl letöltése után a `Get-ChildItem` parancsmaggal tekintheti meg, hogy a fájl le lett-e töltve a PowerShell Cloud Shell ideiglenes meghajtójára.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Fájlok másolása
Az egyik gyakori feladat, hogy egy fájlmegosztás fájljait egy másik fájlmegosztást másolja. A funkció bemutatásához létrehozhat egy új megosztást, és az imént feltöltött fájlt átmásolhatja erre az új megosztásra a [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) parancsmag használatával. 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Ha most listázza az új megosztásban lévő fájlokat, meg kell jelennie a fájl másolatának.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

Habár a `Start-AzStorageFileCopy` parancsmag kényelmes az Azure-fájlmegosztás, az áttelepítések és a nagyobb adatátvitelek között, a `robocopy` Windows és `rsync` a MacOS és Linux rendszereken egyaránt ajánlott. `robocopy` és az `rsync` SMB használatával hajtsa végre az adatáthelyezést a legtöbbet a legtöbbet kihasználó API helyett.

## <a name="create-and-manage-share-snapshots"></a>Megosztási pillanatképek létrehozása és felügyelete
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható állapotát. A megosztási pillanatképek hasonlóak az esetleg már ismert operációsrendszer-technológiákhoz, például a következőkhöz:

- [Kötet árnyékmásolata szolgáltatás (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows fájlrendszerekhez (például NTFS és ReFS).
- [Logikai Volume Manager-(LVM-)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) Pillanatképek Linux rendszerekhez.
- [Apple fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) Pillanatképek MacOS rendszerhez. 

Megoszthat megosztási pillanatképet a megosztáshoz a (z `Snapshot` ) metódus használatával a fájlmegosztás PowerShell-objektumán, amely a [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) parancsmaggal kérhető le. 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>Böngészés a megosztási pillanatképekben
A pillanatkép referenciáját (`$snapshot`) a `Get-AzStorageFile` parancsmag `-Share` paraméterébe illesztve böngészhet a megosztási pillanatkép tartalmában.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása
A következő paranccsal tekintheti meg a megosztáshoz készített pillanatképek listáját.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlokat a korábban már használt `Start-AzStorageFileCopy` paranccsal állíthatja vissza. A rövid útmutató során először töröljük a korábban feltöltött `SampleUpload.txt` fájlt, hogy visszaállíthassuk a pillanatképből.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
A [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) parancsmag használatával törölheti a megosztási pillanatképet a `$snapshot` paraméterre mutató hivatkozást tartalmazó változóval `-Share` .

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha elkészült, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmagot az erőforráscsoport és az összes kapcsolódó erőforrás eltávolításához. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Egyesével is eltávolíthatja az erőforrásokat:

- A gyors útmutatóhoz létrehozott Azure-fájlmegosztások eltávolítása.

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Az Azure-fájlmegosztás törlése előtt törölnie kell az összes, az Azure-fájlmegosztás számára létrehozott megosztási pillanatképet.

- Magának a tárfióknak az eltávolítása (ez implicit módon eltávolítja a létrehozott Azure-fájlmegosztásokat, valamint az esetlegesen létrehozott egyéb tárolási erőforrásokat, például az Azure Blob Storage-tárolókat).

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Mi az az Azure Files?](storage-files-introduction.md)