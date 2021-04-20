---
title: Rövid útmutató az Azure-fájlmegosztások felügyeletéhez az Azure CLI-vel
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure CLI-t a Azure Files. Hozzon létre egy erőforráscsoportot és egy tárfiókot, majd hozzon létre és használjon egy Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9aec838aa762cd018a96b2f17e80cc04ea95795d
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738989"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Rövid útmutató: Azure-fájlmegosztások létrehozása és felügyelete az Azure CLI-vel
Ez az útmutató az [Azure-fájlmegosztások](storage-files-introduction.md) Azure CLI-vel történő használatának alapvető lépéseit mutatja be. Az Azure-fájlmegosztások nem különböznek más fájlmegosztásoktól, a tárolásuk azonban a felhőben történik, és az Azure platform nyújt számukra támogatást. Az Azure-fájlmegosztások támogatják az iparági szabvány Server Message Block (SMB) protokollt, a hálózati fájlrendszer (NFS) protokollt (előzetes verzió), és lehetővé teszik a fájlmegosztást több gép, alkalmazás és példány között. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.4-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

- Az Azure CLI-parancsok alapértelmezés szerint JavaScript Object Notation- (JSON-) választ adnak vissza. A JSON az üzenetek REST API-kba való küldésének és onnan történő fogadásának szabványos módja. A JSON-válaszok használatának megkönnyítése érdekében a jelen cikk néhány példája a *query* paramétert használja az Azure CLI-parancsokon. Ez a paraméter a [JMESPath lekérdezési nyelvet](http://jmespath.org/) használja a JSON elemzéséhez. Az Azure CLI-parancsok eredményeinek JMESPath lekérdezési nyelvvel való használatáról a [JMESPath oktatóanyagában](http://jmespath.org/tutorial.html) talál további információt.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Azure-erőforráscsoporttal, létrehozhat egyet az [az group create](/cli/azure/group) paranccsal. 

Az alábbi példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *USA 2. nyugati régiója helyen:*

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

Az alábbi példa létrehoz egy tárfiókot [az az storage account create paranccsal.](/cli/azure/storage/account) A tárfiókok névnek egyedinek kell lenniük, ezért a `$RANDOM` paranccsal fűzzön számot a névhez.

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
> Az 5 TiB-nél (megosztásonként legfeljebb 100 TiB) nagyobb megosztások csak helyileg redundáns (LRS) és zónaredundáns (ZRS) tárfiókban érhetők el. Georedundáns (GRS) vagy geozónaredundáns (GZRS) tárfiók létrehozásához távolítsa el a `--enable-large-file-share` paramétert.

### <a name="get-the-storage-account-key"></a>A tárfiókkulcs lekérése
A tárfiókkulcsok vezérlik a tárfiókokban lévő erőforrások elérését. A kulcsok automatikusan jönnek létre a tárfiókok létrehozásakor. Az [az storage account keys list](/cli/azure/storage/account/keys) paranccsal kérheti le a tárfiók tárfiókkulcsait: 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Most létrehozhatja az első Azure-fájlmegosztását. Hozzon létre fájlmegosztásokat [az az storage share-rm create paranccsal.](/cli/azure/storage/share-rm?view=azure-cli-latest&preserve-view=false#az_storage_share_rm_create) Ez a példa egy *myshare* nevű Azure-fájlmegosztást hoz létre: 

```azurecli-interactive
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat (de nem kezdődhet kötőjellel). A fájlmegosztások és fájlok elnevezésével kapcsolatos részletes információkért lásd: [Megosztások, könyvtárak,](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)fájlok és metaadatok elnevezése és hivatkozása.

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Az Azure Files két módszert biztosít a fájloknak és mappáknak az Azure-fájlmegosztásban való használatához: az iparági szabvány [Server Message Block (SMB) protokollt](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) és a [Fájl REST protokollt](/rest/api/storageservices/file-service-rest-api). 

A fájlmegosztás SMB-vel való csatlakoztatásához tekintse meg a használt operációs rendszernek megfelelő dokumentumot az alábbiak közül:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Azure-fájlmegosztások használata a Fájl REST protokollal 
A Fájl REST protokollal közvetlenül is dolgozhat (saját maga is készített REST HTTP-hívásokat), de a Fájl REST protokoll használatának leggyakoribb módja az Azure CLI, az [Azure PowerShell](storage-how-to-use-files-powershell.md)modul vagy egy Azure Storage SDK használata, amelyek megfelelő burkolóval szolgálnak a File REST protokoll körül a választott szkriptelési/programozási nyelven.  

Arra számítunk, hogy a legtöbb Azure Files-felhasználó az SMB protokollon keresztül kíván majd dolgozni az Azure-fájlmegosztásával, mivel ez lehetővé teszi számukra a mások által is vélhetően használt meglévő alkalmazások és eszközök használatát. A Fájl REST API használata azonban számos előnnyel jár az SMB-vel szemben, például a következő esetekben:

- A fájlmegosztást az Azure Bash Cloud Shellben böngészi (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 
- Olyan érték-hozzáadási szolgáltatást hoz létre, amely számos Azure-fájlmegosztást fog használni, például biztonsági mentést vagy víruskereső-vizsgálatot végez.

Az alábbi példák bemutatják, hogyan használhatja az Azure CLI-t az Azure-fájlmegosztás fájl REST protokollal való manipulálására. 

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
Ha bemutatja, hogyan tölthet fel egy fájlt az paranccsal, először hozzon létre egy feltölteni kívánt fájlt [`az storage file upload`](/cli/azure/storage/file) a Cloud Shell meghajtón. A következő példában létrehozzuk, majd feltöltjük a fájlt:

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

A fájl feltöltése után az paranccsal győződhet meg arról, hogy a fájl fel lett töltve [`az storage file list`](/cli/azure/storage/file) az Azure-fájlmegosztásba:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Fájl letöltése
Az paranccsal letöltheti az Cloud Shell [`az storage file download`](/cli/azure/storage/file) feltöltött fájl másolatát:

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
Gyakori feladat fájlok másolása egy fájlmegosztásból egy másikba. A funkció bemutatásához hozzon létre egy új megosztást. Másolja a feltöltött fájlt erre az új megosztásra az [az storage file copy](/cli/azure/storage/file/copy) paranccsal: 

```azurecli-interactive
otherShareName="myshare2"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $otherShareName \
    --quota 1024 \
    --enabled-protocols SMB \
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

Bár a parancs kényelmes megoldás az Azure-fájlmegosztások közötti fájláttelepítéshez, migrálások és nagyobb adatáttúdások esetén a macOS és Linux, illetve Windows rendszeren `az storage file copy start` `rsync` `robocopy` ajánlott. `rsync` és `robocopy` használja az SMB-t az adatmozgások végrehajtásához a FileREST API helyett.

## <a name="create-and-manage-share-snapshots"></a>Megosztási pillanatképek létrehozása és felügyelete
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható másolatát. A megosztási pillanatképek hasonlók néhány, esetleg már ismert operációsrendszer-technológiához:

- [A Logikaikötet-kezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképei Linux rendszerekhez.
- [Apple Fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS-hez.
- [Kötet árnyékmásolata szolgáltatás (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) windowsos fájlrendszerek, például NTFS és ReFS fájlrendszerek esetében.
 
Megosztási pillanatképet az paranccsal hozhat [`az storage share snapshot`](/cli/azure/storage/share) létre:

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
A megosztási pillanatképek az paranccsal [`az storage share delete`](/cli/azure/storage/share) törölhetők. Használja a `--snapshot` paraméterre mutató `$SNAPSHOT` hivatkozást tartalmazó változót:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha végzett, az paranccsal eltávolíthatja az erőforráscsoportot és az [`az group delete`](/cli/azure/group) összes kapcsolódó erőforrást: 

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