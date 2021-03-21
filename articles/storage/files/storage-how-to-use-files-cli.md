---
title: Rövid útmutató az Azure-fájlmegosztások felügyeletéhez az Azure CLI-vel
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti Azure Fileseit az Azure CLI használatával. Hozzon létre egy erőforráscsoport-és Storage-fiókot, majd hozzon létre és használjon egy Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5611088b76d8acf785fc0951100dcd4a2f439250
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593186"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Rövid útmutató: Azure-fájlmegosztások létrehozása és felügyelete az Azure CLI-vel
Ez az útmutató az [Azure-fájlmegosztások](storage-files-introduction.md) Azure CLI-vel történő használatának alapvető lépéseit mutatja be. Az Azure-fájlmegosztások nem különböznek más fájlmegosztásoktól, a tárolásuk azonban a felhőben történik, és az Azure platform nyújt számukra támogatást. Az Azure-fájlmegosztás támogatja az iparági szabványnak megfelelő SMB-protokollt, a hálózati fájlrendszer (NFS) protokollját (előzetes verzió), és lehetővé teszi a fájlmegosztás használatát több gépen, alkalmazásban és példányban. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

- Az Azure CLI-parancsok alapértelmezés szerint JavaScript Object Notation- (JSON-) választ adnak vissza. A JSON az üzenetek REST API-kba való küldésének és onnan történő fogadásának szabványos módja. A JSON-válaszok használatának megkönnyítése érdekében a jelen cikk néhány példája a *query* paramétert használja az Azure CLI-parancsokon. Ez a paraméter a [JMESPath lekérdezési nyelvet](http://jmespath.org/) használja a JSON elemzéséhez. Az Azure CLI-parancsok eredményeinek JMESPath lekérdezési nyelvvel való használatáról a [JMESPath oktatóanyagában](http://jmespath.org/tutorial.html) talál további információt.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Azure-erőforráscsoporttal, létrehozhat egyet az [az group create](/cli/azure/group) paranccsal. 

A következő példában létrehozunk egy *myResourceGroup* nevű ERŐFORRÁSCSOPORTOT az *USA 2. nyugati* régiójában:

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. Egy tárfiók korlátlan számú fájlmegosztást tartalmazhat. a megosztásokban pedig tetszőleges számú fájlt tárolhat, egészen a tárfiókja kapacitásának korlátjáig.

Az alábbi példa egy Storage-fiókot hoz létre az az [Storage Account Create](/cli/azure/storage/account) paranccsal. A tárfiókok névnek egyedinek kell lenniük, ezért a `$RANDOM` paranccsal fűzzön számot a névhez.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> Az 5 TiB-nál nagyobb (legfeljebb 100 TiB-ig terjedő) megosztások csak a helyileg redundáns (LRS) és a zóna redundáns (ZRS) Storage-fiókokban érhetők el. A Geo-redundáns (GRS) vagy a Geo-Zone-redundáns (GZRS) Storage-fiók létrehozásához távolítsa el a `--enable-large-file-share` paramétert.

### <a name="get-the-storage-account-key"></a>A tárfiókkulcs lekérése
A tárfiókkulcsok vezérlik a tárfiókokban lévő erőforrások elérését. A kulcsok automatikusan jönnek létre a tárfiókok létrehozásakor. Az [az storage account keys list](/cli/azure/storage/account/keys) paranccsal kérheti le a tárfiók tárfiókkulcsait: 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Most létrehozhatja az első Azure-fájlmegosztását. Az [az storage share create](/cli/azure/storage/share) paranccsal hozhat létre fájlmegosztásokat. Ez a példa egy *myshare* nevű Azure-fájlmegosztást hoz létre: 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat (de nem kezdődhet kötőjellel). A fájlmegosztás és a fájlok elnevezésével kapcsolatos részletes információkért lásd: [megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Az Azure Files két módszert biztosít a fájloknak és mappáknak az Azure-fájlmegosztásban való használatához: az iparági szabvány [Server Message Block (SMB) protokollt](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) és a [Fájl REST protokollt](/rest/api/storageservices/file-service-rest-api). 

A fájlmegosztás SMB-vel való csatlakoztatásához tekintse meg a használt operációs rendszernek megfelelő dokumentumot az alábbiak közül:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Azure-fájlmegosztások használata a Fájl REST protokollal 
Közvetlenül is dolgozhat a fájl REST protokollal közvetlenül (TÖBBek között a REST HTTP-hívások esetében), de a fájl REST protokoll használatának leggyakoribb módja az Azure CLI, a [Azure PowerShell modul](storage-how-to-use-files-powershell.md)vagy egy Azure Storage SDK használata, amely az Ön által választott parancsfájlkezelési/programozási nyelvben egy szép burkolót biztosít a fájl Rest protokollja körül.  

Arra számítunk, hogy a legtöbb Azure Files-felhasználó az SMB protokollon keresztül kíván majd dolgozni az Azure-fájlmegosztásával, mivel ez lehetővé teszi számukra a mások által is vélhetően használt meglévő alkalmazások és eszközök használatát. A Fájl REST API használata azonban számos előnnyel jár az SMB-vel szemben, például a következő esetekben:

- A fájlmegosztást az Azure Bash Cloud Shellben böngészi (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 
- Olyan értéknövelt szolgáltatást hoz létre, amely számos Azure-fájlmegosztás esetében fog működni, például biztonsági mentési vagy víruskereső-vizsgálatok végrehajtásakor.

Az alábbi példák bemutatják, hogyan kezelheti az Azure-fájlmegosztás használatát az Azure CLI-vel a file REST protokollal. 

### <a name="create-a-directory"></a>Könyvtár létrehozása
Ha egy új, *myDirectory* nevű könyvtárat szeretne létrehozni az Azure-fájlmegosztás gyökérmappájában, használja az [`az storage directory create`](/cli/azure/storage/directory) parancsot:

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Fájl feltöltése
Ha szeretné bemutatni, hogyan tölthet fel egy fájlt a [`az storage file upload`](/cli/azure/storage/file) paranccsal, először hozzon létre egy fájlt, amelyet fel szeretne tölteni a Cloud Shell-meghajtón. A következő példában létrehozzuk, majd feltöltjük a fájlt:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Ha helyileg futtatja az Azure CLI-t, cserélje le a `~/clouddrive` elérési utat a gép egy meglévő elérési útjára.

A fájl feltöltése után a parancs használatával győződjön meg [`az storage file list`](/cli/azure/storage/file) arról, hogy a fájl fel lett töltve az Azure-fájlmegosztásba:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Fájl letöltése
A [`az storage file download`](/cli/azure/storage/file) parancs használatával letöltheti a Cloud Shell-meghajtóra feltöltött fájl másolatát:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Fájlok másolása
Gyakori feladat, hogy egy fájlmegosztás fájljait egy másik fájlmegosztást másolja. A funkció bemutatásához hozzon létre egy új megosztást. Másolja a feltöltött fájlt erre az új megosztásra az [az storage file copy](/cli/azure/storage/file/copy) paranccsal: 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Ha most listázza az új megosztásban lévő fájlokat, megjelenik az átmásolt fájl:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Habár a `az storage file copy start` parancs kényelmes az Azure-fájlmegosztás, a Migrálás és a nagyobb adatátvitelek között, a `rsync` MacOS és a Linux és `robocopy` a Windows rendszereken is ajánlott. `rsync` és az `robocopy` SMB használatával hajtsa végre az adatáthelyezést a legtöbbet a legtöbbet kihasználó API helyett.

## <a name="create-and-manage-share-snapshots"></a>Megosztási pillanatképek létrehozása és felügyelete
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható másolatát. A megosztási pillanatképek hasonlók néhány, esetleg már ismert operációsrendszer-technológiához:

- [Logikai Volume Manager-(LVM-)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) Pillanatképek Linux rendszerekhez.
- [Apple fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) Pillanatképek MacOS rendszerhez.
- [Kötet árnyékmásolata szolgáltatás (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows fájlrendszerekhez (például NTFS és ReFS).
 
A megosztási pillanatképet a parancs használatával hozhatja létre [`az storage share snapshot`](/cli/azure/storage/share) :

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Böngészés a megosztási pillanatképek tartalmában
A megosztási pillanatképek tartalmában való böngészéshez illessze a rögzített megosztási pillanatkép időbélyegét az `az storage file list` parancs `$snapshot` változójába:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása
A következő paranccsal tekintheti meg a megosztásról készített pillanatképek listáját:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlokat a korábban már használt `az storage file copy start` paranccsal állíthatja vissza. Először törölje a feltöltött SampleUpload.txt fájlt, hogy visszaállíthassa a pillanatképből:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
A megosztási pillanatképet a parancs használatával törölheti [`az storage share delete`](/cli/azure/storage/share) . Használja a `--snapshot` paraméterre mutató `$SNAPSHOT` hivatkozást tartalmazó változót:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha elkészült, a [`az group delete`](/cli/azure/group) parancs használatával távolíthatja el az erőforráscsoportot és az összes kapcsolódó erőforrást: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

Alternatív megoldásként egyenként is eltávolíthatja az erőforrásokat.
- A jelen cikkhez létrehozott Azure-fájlmegosztások eltávolítása:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Magának a tárfióknak az eltávolítása. (Ez implicit módon eltávolítja a létrehozott Azure-fájlmegosztásokat, valamint az esetlegesen létrehozott egyéb tárolási erőforrásokat, például az Azure Blob Storage-tárolókat.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Mi az az Azure Files?](storage-files-introduction.md)
