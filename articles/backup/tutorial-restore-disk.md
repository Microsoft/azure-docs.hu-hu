---
title: Oktatóanyag – virtuális gép lemezének visszaállítása Azure Backup
description: Megtudhatja, hogyan állíthatja vissza a lemezt, valamint hogyan hozhat létre és állíthat helyre egy virtuális gépet az Azure-ban a Backup és a Recovery Services használatával.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 8a66cee7e844f0049f2d2ca2f6841943aa267f3e
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114185"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Lemez visszaállítása és helyreállított virtuális gép létrehozása az Azure-ban

Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak egyes fájlokat. Ez a cikk a teljes virtuális gép parancssori felülettel való visszaállításának módját ismerteti. Ennek az oktatóanyagnak a segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
> * Helyreállítási pontok listázása és kiválasztása
> * Lemez visszaállítása egy helyreállítási pontból
> * Virtuális gép létrehozása a visszaállított lemezről

További információért a PowerShell a lemez visszaállításhoz és egy helyreállított virtuális gép létrehozásához való használatáról, tekintse meg a [Azure-beli virtuális gépek biztonsági mentése és visszaállítása a PowerShell-lel](backup-azure-vms-automation.md#restore-an-azure-vm) című részt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.18-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz olyan Linux rendszerű virtuális gépre van szükség, amelyet az Azure Backup véd. A virtuális gép véletlen törlésének és a helyreállítási folyamatnak a szimulálásához hozzon létre egy virtuális gépet a helyreállítási pont egyik lemezéről. Ha szüksége van egy linuxos virtuális gépre, amelyet az Azure Backup véd, lásd: [Virtuális gép biztonsági mentése az Azure-ban a parancssori felület használatával](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>A biztonsági mentés áttekintése

Amikor az Azure biztonsági mentést kezdeményez, a virtuális gépen futó biztonsági mentési bővítmény időponthoz kötött pillanatképet készít. A biztonsági mentési bővítmény az első biztonsági mentés kérésekor települ a virtuális gépre. Az Azure Backup akkor is tud pillanatképet készíteni az alapul szolgáló tárolóról, ha a virtuális gép a biztonsági mentés közben nem fut.

Alapértelmezés szerint az Azure Backup a fájlrendszerrel konzisztens biztonsági másolatot készít. Amikor az Azure Backup elkészítette a pillanatképet, az adatok átkerülnek a helyreállítási tárba. A maximális hatékonyság érdekében az Azure Backup csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

## <a name="list-available-recovery-points"></a>Rendelkezésre álló helyreállítási pontok listázása

A lemez visszaállításához ki kell választania egy helyreállítási pontot a helyreállítási adatok forrásaként. Az alapértelmezett szabályzat minden nap létrehoz egy-egy helyreállítási pontot, és mindegyiket 30 napig tartja meg. Így fenntartható egy helyreállítási pontokból álló készlet, amelyből kiválaszthat egy adott időpontot a helyreállításhoz.

Az elérhető helyreállítási pontok listájának megjelenítéséhez használja az [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) parancsot. A lemezek a helyreállítási pont **nevével** állíthatók helyre. Ebben az oktatóanyagban a legutóbbi rendelkezésre álló helyreállítási pontot használjuk. A `--query [0].name` paraméter az alábbiak szerint választja ki a legutóbbi helyreállítási pont nevét:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Virtuálisgép-lemez visszaállítása

> [!IMPORTANT]
> Erősen ajánlott az az CLI Version 2.0.74 vagy újabb használata a gyors visszaállítás összes előnyének beszerzéséhez, beleértve a felügyelt lemezek visszaállítását. A legjobb, ha a felhasználó mindig a legújabb verziót használja.

### <a name="managed-disk-restore"></a>Felügyelt lemez visszaállítása

Ha a biztonsági másolatban szereplő virtuális gép felügyelt lemezekkel rendelkezik, és ha a felügyelt lemezeket a helyreállítási pontról szeretné visszaállítani, először egy Azure Storage-fiókot kell megadnia. Ez a Storage-fiók a virtuálisgép-konfiguráció és a központi telepítési sablon tárolására szolgál, amelyet később a virtuális gép a visszaállított lemezekről való üzembe helyezéséhez használhat. Ezután megadhat egy célként megadott erőforráscsoportot is a felügyelt lemezek visszaállításához.

1. A tárfiók létrehozásához használja az [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) parancsot. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Cserélje le *mystorageaccount* kifejezést egy saját egyedi névre:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Állítsa vissza a lemezt a helyreállítási pontról az [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) paranccsal. Cserélje le *mystorageaccount* kifejezést az előző paranccsal létrehozott tárfiók nevére. Cserélje le a *myrecoverypointname kifejezést* elemet az előző az [Backup recoverypoint List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) parancs kimenetében beszerzett helyreállítási pont nevére. ***Adja meg azt a célként megadott erőforráscsoportot is, amelyhez a felügyelt lemezeket vissza kívánja állítani***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --target-resource-group targetRG
    ```

> [!WARNING]
> Ha a cél-erőforráscsoport nincs megadva, a felügyelt lemezek nem felügyelt lemezként lesznek visszaállítva a megadott Storage-fiókba. Ennek jelentős következményei lesznek a visszaállítási időre, mivel a lemezek visszaállításához szükséges idő teljes mértékben a megadott Storage-fióktól függ.

### <a name="unmanaged-disks-restore"></a>Nem felügyelt lemezek visszaállítása

Ha a biztonsági másolatban szereplő virtuális gép nem felügyelt lemezekkel rendelkezik, és ha a helyreállítási pontról szeretné visszaállítani a lemezeket, először egy Azure Storage-fiókot kell megadnia. Ez a Storage-fiók a virtuálisgép-konfiguráció és a központi telepítési sablon tárolására szolgál, amelyet később a virtuális gép a visszaállított lemezekről való üzembe helyezéséhez használhat. Alapértelmezés szerint a nem felügyelt lemezeket a rendszer visszaállítja az eredeti Storage-fiókokba. Ha a felhasználó egyetlen helyre szeretné visszaállítani az összes nem felügyelt lemezt, akkor a megadott Storage-fiók is használható átmeneti helyként a lemezek számára.

A további lépésekben a visszaállított lemezzel létrehoz egy virtuális gépet.

1. A tárfiók létrehozásához használja az [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) parancsot. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Cserélje le *mystorageaccount* kifejezést egy saját egyedi névre:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Állítsa vissza a lemezt a helyreállítási pontról az [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) paranccsal. Cserélje le *mystorageaccount* kifejezést az előző paranccsal létrehozott tárfiók nevére. Cserélje le a *myRecoveryPointName* kifejezést az előzőleg használt [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) parancs kimenetéből származó helyreállításipont-névre:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

A fentiekben leírtaknak megfelelően a nem felügyelt lemezeket az eredeti Storage-fiókjába fogja visszaállítani. Ez biztosítja a legjobb visszaállítási teljesítményt. Ha azonban az összes nem felügyelt lemezt vissza kell állítani a megadott Storage-fiókra, akkor az alább látható módon használja a megfelelő jelzőt.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

A kimenet a következő példához hasonló, amelyben látható, hogy a visszaállítási feladat *InProgress* (Folyamatban) állapotban van:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Ha a visszaállítási feladatok jelentéseinek állapota *befejeződött*, a rendszer visszaállította a szükséges információkat (a virtuálisgép-konfigurációt és a központi telepítési sablont) a Storage-fiókra.

## <a name="create-a-vm-from-the-restored-disk"></a>Virtuális gép létrehozása a visszaállított lemezről

Utolsó lépésként hozzon létre egy virtuális gépet a visszaállított lemezekről. A virtuális gép létrehozásához a letöltött központi telepítési sablont használhatja a megadott Storage-fiókhoz.

### <a name="fetch-the-job-details"></a>A feladatok részleteinek beolvasása

Az eredő feladatok részletei megadják a sablon URI-JÁT, amely lekérdezhető és telepíthető. Az aktivált visszaállított feladattal kapcsolatos további részletekért használja a feladatok megjelenítése parancsot.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

A lekérdezés kimenete minden adatot megad, de csak a Storage-fiók tartalma érdekli. Az Azure CLI [lekérdezési funkciójának](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest) használatával beolvashatók a kapcsolódó részletek

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>A központi telepítési sablon beolvasása

A sablon nem érhető el közvetlenül, mert az ügyfél Storage-fiókja és a megadott tároló alatt található. Ehhez a sablonhoz a teljes URL-címet (valamint egy ideiglenes SAS-tokent) kell elérni.

Először bontsa ki a sablon blob URI-ját a feladatok részletei közül.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

A sablon blob URI-ja ebben a formátumban lesz, és Kinyeri a sablon nevét.

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Így a fenti példában szereplő sablon neve ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` lesz, és a tároló neve ```myVM-daa1931199fd4a22ae601f46d8812276```

Most szerezze be a tárolóhoz és a sablonhoz tartozó SAS-tokent az [itt](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment) részletezett módon.

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>A sablon üzembe helyezése a virtuális gép létrehozásához

Most telepítse a sablont a virtuális gép létrehozásához az [itt](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)leírtak szerint.

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Ha ellenőrizni szeretné, hogy létrejött-e a virtuális gép a helyreállított lemezről, listázza az erőforráscsoportban szereplő virtuális gépeket az [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) paranccsal az alábbiak szerint:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban visszaállított egy lemezt a helyreállítási pontról, és létrehozott egy virtuális gépet a lemezről. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Helyreállítási pontok listázása és kiválasztása
> * Lemez visszaállítása egy helyreállítási pontból
> * Virtuális gép létrehozása a visszaállított lemezről

Folytassa a következő oktatóanyaggal, amely egy helyreállítási pont egyes fájljainak visszaállítását ismerteti.

> [!div class="nextstepaction"]
> [Fájlok visszaállítása Azure-beli virtuális gépekre](tutorial-restore-files.md)
