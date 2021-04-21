---
title: Oktatóanyag – Virtuális gép visszaállítása az Azure CLI használatával
description: Megtudhatja, hogyan állíthatja vissza a lemezt, valamint hogyan hozhat létre és állíthat helyre egy virtuális gépet az Azure-ban a Backup és a Recovery Services használatával.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7f4d70f43f76c3a72cd8e53037d06d32e61c3cdb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768499"
---
# <a name="restore-a-vm-with-azure-cli"></a>Virtuális gép visszaállítása az Azure CLI használatával

Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak egyes fájlokat. Ez a cikk a teljes virtuális gép parancssori felülettel való visszaállításának módját ismerteti. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
> * Helyreállítási pontok listázása és kiválasztása
> * Lemez visszaállítása egy helyreállítási pontból
> * Virtuális gép létrehozása a visszaállított lemezről

További információért a PowerShell a lemez visszaállításhoz és egy helyreállított virtuális gép létrehozásához való használatáról, tekintse meg a [Azure-beli virtuális gépek biztonsági mentése és visszaállítása a PowerShell-lel](backup-azure-vms-automation.md#restore-an-azure-vm) című részt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez az oktatóanyaghoz az Azure CLI 2.0.18-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

 - Ehhez az oktatóanyaghoz olyan Linux rendszerű virtuális gépre van szükség, amelyet az Azure Backup véd. A virtuális gép véletlen törlésének és a helyreállítási folyamatnak a szimulálásához hozzon létre egy virtuális gépet a helyreállítási pont egyik lemezéről. Ha szüksége van egy linuxos virtuális gépre, amelyet az Azure Backup véd, lásd: [Virtuális gép biztonsági mentése az Azure-ban a parancssori felület használatával](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>A biztonsági mentés áttekintése

Amikor az Azure biztonsági mentést kezdeményez, a virtuális gépen futó biztonsági mentési bővítmény időponthoz kötött pillanatképet készít. A biztonsági mentési bővítmény az első biztonsági mentés kérésekor települ a virtuális gépre. Azure Backup pillanatképet is készíthet a mögöttes tárolóról, ha a virtuális gép nem fut a biztonsági mentéskor.

Alapértelmezés szerint az Azure Backup a fájlrendszerrel konzisztens biztonsági másolatot készít. Amikor az Azure Backup elkészítette a pillanatképet, az adatok átkerülnek a helyreállítási tárba. A maximális hatékonyság érdekében az Azure Backup csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

## <a name="list-available-recovery-points"></a>Rendelkezésre álló helyreállítási pontok listázása

A lemez visszaállításához ki kell választania egy helyreállítási pontot a helyreállítási adatok forrásaként. Az alapértelmezett szabályzat minden nap létrehoz egy-egy helyreállítási pontot, és mindegyiket 30 napig tartja meg. Így fenntartható egy helyreállítási pontokból álló készlet, amelyből kiválaszthat egy adott időpontot a helyreállításhoz.

Az elérhető helyreállítási pontok listájának megjelenítéséhez használja az [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) parancsot. A lemezek a helyreállítási pont **nevével** állíthatók helyre. Ebben az oktatóanyagban a legutóbbi rendelkezésre álló helyreállítási pontot használjuk. A `--query [0].name` paraméter az alábbiak szerint választja ki a legutóbbi helyreállítási pont nevét:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Virtuálisgép-lemez visszaállítása

> [!IMPORTANT]
> Erősen ajánlott az Az CLI 2.0.74-es vagy újabb verziójával kihozni a gyors visszaállítás összes előnyét, beleértve a felügyelt lemezek visszaállítását is. A legjobb, ha mindig a legújabb verziót használja.

### <a name="managed-disk-restore"></a>Felügyelt lemez visszaállítása

Ha a virtuális gép biztonsági másolata felügyelt lemezekkel rendelkezik, és a cél a felügyelt lemezek visszaállítása a helyreállítási pontról, először meg kell adnia egy Azure-tárfiókot. Ez a tárfiók tárolja a virtuális gép konfigurációját és az üzembe helyezési sablont, amely később a virtuális gép a visszaállított lemezekről való üzembe helyezéséhez használható. Ezután meg kell adnia egy cél erőforráscsoportot is a felügyelt lemezek visszaállításához.

1. A tárfiók létrehozásához használja az [az storage account create](/cli/azure/storage/account#az_storage_account_create) parancsot. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Cserélje le *mystorageaccount* kifejezést egy saját egyedi névre:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Állítsa vissza a lemezt a helyreállítási pontról az [az backup restore restore-disks](/cli/azure/backup/restore#az_backup_restore_restore_disks) paranccsal. Cserélje le *mystorageaccount* kifejezést az előző paranccsal létrehozott tárfiók nevére. Cserélje *le a myRecoveryPointName* helyére az előző [az backup recoverypoint list parancs](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) kimenetében kapott helyreállítási pont nevét. ***Adja meg azt a cél erőforráscsoportot is,*** amelybe a felügyelt lemezek visszaállnak.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > Ha **a target-resource-group** nincs megadva, akkor a felügyelt lemezek nem felügyelt lemezként lesznek visszaállva az adott tárfiókba. Ez jelentős következményekkel jár a visszaállítási időre, mivel a lemezek visszaállításához szükséges idő teljes mértékben az adott tárfióktól függ. Az azonnali visszaállítás előnye csak akkor áll le, ha a target-resource-group paraméter meg van adva. Ha a cél a felügyelt lemezek nem felügyeltként való visszaállítása, akkor ne adja meg a **target-resource-group** paramétert, hanem a **restore-as-unmanaged-disk** paramétert az alább látható módon. Ez a paraméter az az 3.4.0-tól kezdve érhető el.

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

Ez nem felügyelt lemezként fogja visszaállítani a felügyelt lemezeket az adott tárfiókba, és nem fogja kihozni az azonnali visszaállítási funkciót. A CLI jövőbeli verzióiban kötelező megadni a **target-resource-group** vagy a **restore-as-unmanaged-disk paramétert.**

### <a name="unmanaged-disks-restore"></a>Nemmanaged disks restore

Ha a biztonsági másolatba biztonsági másolatba bement virtuális gép nem birtokolt lemezeket, és a szándéka a lemezek visszaállítása a helyreállítási pontról, először meg kell adnia egy Azure-tárfiókot. Ez a tárfiók tárolja a virtuális gép konfigurációját és az üzembe helyezési sablont, amely később a virtuális gép a visszaállított lemezekről való üzembe helyezéséhez használható. Alapértelmezés szerint a rendszer visszaállítja a nem törölt lemezeket az eredeti tárfiókjukba. Ha az összes nem nem használt lemezt egyetlen helyre szeretné visszaállítani, akkor az adott tárfiók is használható előkészítési helyként ezekhez a lemezekhez.

A további lépésekben a visszaállított lemezzel létrehoz egy virtuális gépet.

1. A tárfiók létrehozásához használja az [az storage account create](/cli/azure/storage/account#az_storage_account_create) parancsot. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Cserélje le *mystorageaccount* kifejezést egy saját egyedi névre:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Állítsa vissza a lemezt a helyreállítási pontról az [az backup restore restore-disks](/cli/azure/backup/restore#az_backup_restore_restore_disks) paranccsal. Cserélje le *mystorageaccount* kifejezést az előző paranccsal létrehozott tárfiók nevére. Cserélje le a *myRecoveryPointName* kifejezést az előzőleg használt [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) parancs kimenetéből származó helyreállításipont-névre:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Ahogy azt korábban említettük, a nem törölt lemezek visszaállnak az eredeti tárfiókjukra. Ez biztosítja a legjobb visszaállítási teljesítményt. Ha azonban az összes nem nem használt lemezt vissza kell állítani egy adott tárfiókba, használja a megfelelő jelzőt az alább látható módon.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az_backup_job_list):

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

Ha a *visszaállítási* feladat állapota Befejezve állapotot *ad* vissza, a szükséges információk (a virtuális gép konfigurációja és az üzembe helyezési sablon) visszaálltak a tárfiókra.

## <a name="create-a-vm-from-the-restored-disk"></a>Virtuális gép létrehozása a visszaállított lemezről

Az utolsó lépés egy virtuális gép létrehozása a visszaállított lemezekből. A virtuális gép létrehozásához használhatja az adott tárfiókba letöltött üzembe helyezési sablont.

### <a name="fetch-the-job-details"></a>A feladat részleteinek beolvasása

Az eredményül kapott feladat adatai a lekérdezhető és üzembe helyezhető sablon URI-ját adják meg. A job show paranccsal további részleteket olvashat be az aktivált visszaállított feladatról.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

A lekérdezés kimenete minden részletet megad, de minket csak a tárfiók tartalma érdekel. Az Azure CLI [lekérdezési](/cli/azure/query-azure-cli) funkcióját használva lekérheti a releváns részleteket

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

### <a name="fetch-the-deployment-template"></a>Az üzembe helyezési sablon lekérése

A sablon nem érhető el közvetlenül, mivel az ügyfél tárfiókja és az adott tároló alatt található. A sablon eléréséhez a teljes URL-címre (egy ideiglenes SAS-jogkivonattal együtt) van szükség.

Először bontsa ki a sablon blob URI-ját a feladat részleteiből

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

A sablonblob URI formátuma ilyen lesz, és kinyeri a sablon nevét

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Így a sablon neve a fenti példában a lesz, a ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` tároló neve pedig ```myVM-daa1931199fd4a22ae601f46d8812276```

Most szerezze be a tároló és a sablon SAS-jogkivonatát az itt [részletezettek szerint](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment)

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

Most telepítse a sablont a virtuális gép létrehozásához az itt leírtak [szerint.](../azure-resource-manager/templates/deploy-cli.md)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Ha ellenőrizni szeretné, hogy létrejött-e a virtuális gép a helyreállított lemezről, listázza az erőforráscsoportban szereplő virtuális gépeket az [az vm list](/cli/azure/vm#az_vm_list) paranccsal az alábbiak szerint:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban visszaállított egy lemezt a helyreállítási pontról, és létrehozott egy virtuális gépet a lemezről. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
>
> * Helyreállítási pontok listázása és kiválasztása
> * Lemez visszaállítása egy helyreállítási pontból
> * Virtuális gép létrehozása a visszaállított lemezről

Folytassa a következő oktatóanyaggal, amely egy helyreállítási pont egyes fájljainak visszaállítását ismerteti.

> [!div class="nextstepaction"]
> [Fájlok visszaállítása Azure-beli virtuális gépekre](tutorial-restore-files.md)
