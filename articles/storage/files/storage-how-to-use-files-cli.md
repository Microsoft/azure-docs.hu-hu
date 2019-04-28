---
title: Rövid útmutató az Azure-fájlmegosztások felügyeletéhez az Azure CLI-vel
description: Ebből a rövid útmutatóból megtudhatja, hogyan felügyelheti az Azure Files szolgáltatást az Azure CLI használatával.
services: storage
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 43a5a72ac32d8ed3510cecb505f5e62cf91d7106
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766894"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Gyors útmutató: Létrehozása és kezelése az Azure-fájlmegosztások Azure CLI-vel
Ez az útmutató az [Azure-fájlmegosztások](storage-files-introduction.md) Azure CLI-vel történő használatának alapvető lépéseit mutatja be. Az Azure-fájlmegosztások nem különböznek más fájlmegosztásoktól, a tárolásuk azonban a felhőben történik, és az Azure platform nyújt számukra támogatást. Az Azure-fájlmegosztások támogatják az iparági szabvány SMB protokollt, és lehetővé teszik a több gép, alkalmazás és példány közötti fájlmegosztást. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor a jelen cikkben szereplő lépésekhez az Azure CLI 2.0.4-es vagy újabb verzióját kell futtatnia. Az Azure CLI-verzió megkereséséhez futtassa az **az --version** parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

Az Azure CLI-parancsok alapértelmezés szerint JavaScript Object Notation- (JSON-) választ adnak vissza. A JSON az üzenetek REST API-kba való küldésének és onnan történő fogadásának szabványos módja. A JSON-válaszok használatának megkönnyítése érdekében a jelen cikk néhány példája a *query* paramétert használja az Azure CLI-parancsokon. Ez a paraméter a [JMESPath lekérdezési nyelvet](http://jmespath.org/) használja a JSON elemzéséhez. Az Azure CLI-parancsok eredményeinek JMESPath lekérdezési nyelvvel való használatáról a [JMESPath oktatóanyagában](http://jmespath.org/tutorial.html) talál további információt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Ha az Azure CLI-t helyileg használja, nyisson meg egy parancssort, és ha még nem tette meg, jelentkezzen be az Azure-ba.

```bash 
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Azure-erőforráscsoporttal, létrehozhat egyet az [az group create](/cli/azure/group) paranccsal. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *USA keleti régiója* helyen:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. Egy tárfiók korlátlan számú fájlmegosztást tartalmazhat. Egy megosztás korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig.

A következő példa létrehoz egy *mystorageaccount\<random number\>* nevű tárfiókot az [az storage account create](/cli/azure/storage/account) paranccsal, majd a tárfiók nevét a `$STORAGEACCT` változóba helyezi. Tárfiókok nevének egyedinek kell lenniük, ezért ügyeljen arra, hogy egyedi nevére cserélje le a "mystorageacct".

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>A tárfiókkulcs lekérése
A tárfiókkulcsok vezérlik a tárfiókokban lévő erőforrások elérését. A kulcsok automatikusan jönnek létre a tárfiókok létrehozásakor. Az [az storage account keys list](/cli/azure/storage/account/keys) paranccsal kérheti le a tárfiók tárfiókkulcsait: 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Most létrehozhatja az első Azure-fájlmegosztását. Az [az storage share create](/cli/azure/storage/share) paranccsal hozhat létre fájlmegosztásokat. Ez a példa egy *myshare* nevű Azure-fájlmegosztást hoz létre: 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat (de nem kezdődhet kötőjellel). A fájlmegosztások és fájlok elnevezésére vonatkozó információkért tekintse meg a [megosztások, könyvtárak, fájlok és metaadatok elnevezésével és a rájuk való hivatkozással](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) foglalkozó cikket.

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Az Azure Files két módszert biztosít a fájloknak és mappáknak az Azure-fájlmegosztásban való használatához: az iparági szabvány [Server Message Block (SMB) protokollt](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) és a [Fájl REST protokollt](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

A fájlmegosztás SMB-vel való csatlakoztatásához tekintse meg a használt operációs rendszernek megfelelő dokumentumot az alábbiak közül:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Azure-fájlmegosztások használata a Fájl REST protokollal 
Lehetséges dolgozhat közvetlenül a fájl REST-protokollon közvetlenül (REST HTTP-hívások saját kezűleg handcrafting), de a fájl REST protokoll használatára leggyakoribb módja az, hogy az Azure CLI-vel, a [Azure PowerShell-modul](storage-how-to-use-files-powershell.md), vagy egy Azure Storage SDK-val , amelyek mindegyike adja meg a fájl REST-protokollon, a kiválasztott parancsfájlok és programozás nyelven nice burkolója.  

Arra számítunk, hogy a legtöbb Azure Files-felhasználó az SMB protokollon keresztül kíván majd dolgozni az Azure-fájlmegosztásával, mivel ez lehetővé teszi számukra a mások által is vélhetően használt meglévő alkalmazások és eszközök használatát. A Fájl REST API használata azonban számos előnnyel jár az SMB-vel szemben, például a következő esetekben:

- A fájlmegosztást az Azure Bash Cloud Shellben böngészi (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Akkor hajtsa végre egy szkriptet vagy az alkalmazás ügyfél, amely nem tud csatlakoztatni az SMB-megosztáson, például olyan helyszíni ügyfelek, amelyek nem rendelkeznek a 445-ös port.
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 

Az alábbi példák bemutatják, hogyan módosíthatja az Azure-fájlmegosztást a fájl REST protokoll az Azure CLI használatával. 

### <a name="create-a-directory"></a>Könyvtár létrehozása
Ha egy új, *myDirectory* nevű könyvtárat szeretne létrehozni az Azure-fájlmegosztás gyökérmappájában, használja az [`az storage directory create`](/cli/azure/storage/directory) parancsot:

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Fájl feltöltése
A fájlok [`az storage file upload`](/cli/azure/storage/file) paranccsal való feltöltésének bemutatásához először hozzon létre egy feltölteni kívánt fájlt a Cloud Shell ideiglenes meghajtóján. A következő példában létrehozzuk, majd feltöltjük a fájlt:

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Ha helyileg futtatja az Azure CLI-t, cserélje le a `~/clouddrive` elérési utat a gép egy meglévő elérési útjára.

A fájl feltöltése után az [`az storage file list`](/cli/azure/storage/file) paranccsal ellenőrizheti, hogy a fájl fel lett-e töltve az Azure-fájlmegosztásba:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Fájl letöltése
Az [`az storage file download`](/cli/azure/storage/file) paranccsal letöltheti a Cloud Shell ideiglenes meghajtójára a feltöltött fájl másolatát:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Fájlok másolása
A fájlok egy fájlmegosztásról egy másikra, vagy Azure Blob Storage-tárolóról/-tárolóra való másolása gyakori feladat. A funkció bemutatásához hozzon létre egy új megosztást. Másolja a feltöltött fájlt erre az új megosztásra az [az storage file copy](/cli/azure/storage/file/copy) paranccsal: 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Ha most listázza az új megosztásban lévő fájlokat, megjelenik az átmásolt fájl:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Bár az `az storage file copy start` parancs kényelmes megoldás a fájlok Azure-fájlmegosztások és Azure Blob Storage-tárolók közötti áthelyezéséhez, a nagyobb léptékű áthelyezésekhez az AzCopy használata ajánlott. (A nagyobb lépték alatt itt az áthelyezett fájlok nagyobb méretét és számát értjük.) További információ: [AzCopy segédprogram Linux rendszeren](../common/storage-use-azcopy-linux.md) és [AzCopy segédprogram Windows rendszeren](../common/storage-use-azcopy.md). Az AzCopyt helyileg kell telepíteni. Az AzCopy nem érhető el a Cloud Shellben. 

## <a name="create-and-manage-share-snapshots"></a>Megosztási pillanatképek létrehozása és felügyelete
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható másolatát. A megosztási pillanatképek hasonlók néhány, esetleg már ismert operációsrendszer-technológiához:

- [Logikaikötet-kezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképek Linux rendszerekhez
- [Apple fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS rendszerhez
- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows-fájlrendszerekhez (például NTFS és ReFS) A megosztási pillanatképek az [`az storage share snapshot`](/cli/azure/storage/share) paranccsal hozhatók létre:

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Böngészés a megosztási pillanatképek tartalmában
A megosztási pillanatképek tartalmában való böngészéshez illessze a rögzített megosztási pillanatkép időbélyegét az `az storage file list` parancs `$SNAPSHOT` változójába:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása
A következő paranccsal tekintheti meg a megosztásról készített pillanatképek listáját:

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlokat a korábban már használt `az storage file copy start` paranccsal állíthatja vissza. Először törölje a feltöltött SampleUpload.txt fájlt, hogy visszaállíthassa a pillanatképből:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"
 # Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')
 URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT
 # Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
A megosztási pillanatképek az [`az storage share delete`](/cli/azure/storage/share) paranccsal törölhetők. Használja a `--snapshot` paraméterre mutató `$SNAPSHOT` hivatkozást tartalmazó változót:

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Amikor végzett, az [`az group delete`](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást: 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Alternatív megoldásként egyenként is eltávolíthatja az erőforrásokat.
- A jelen cikkhez létrehozott Azure-fájlmegosztások eltávolítása:

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- Magának a tárfióknak az eltávolítása. (Ez implicit módon eltávolítja a létrehozott Azure-fájlmegosztásokat, valamint az esetlegesen létrehozott egyéb tárolási erőforrásokat, például az Azure Blob Storage-tárolókat.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi az Azure Files?](storage-files-introduction.md)
