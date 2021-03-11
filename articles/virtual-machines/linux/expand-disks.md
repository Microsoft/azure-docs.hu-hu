---
title: Virtuális merevlemezek kibontása linuxos virtuális gépen
description: Megtudhatja, hogyan bővítheti a virtuális merevlemezeket egy Linux rendszerű virtuális GÉPEN az Azure CLI-vel.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 72778c431c561f5345dde3d6803e814d6fdebfba
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549124"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Virtuális merevlemezek kibontása Linux rendszerű virtuális GÉPEN az Azure CLI-vel

Ez a cikk azt ismerteti, hogyan lehet kibontani a Linux rendszerű virtuális gépek (VM) felügyelt lemezeit az Azure CLI-vel. [Adatlemezeket adhat hozzá](add-disk.md) a további tárolóhelyek biztosításához, és kibővítheti a meglévő adatlemezeket is. Az operációs rendszer (OS) alapértelmezett virtuális merevlemez-mérete jellemzően 30 GB az Azure-beli Linux rendszerű virtuális gépeken. 

> [!WARNING]
> Mindig győződjön meg arról, hogy a fájlrendszer kifogástalan állapotban van, a lemezpartíció-tábla típusa támogatja az új méretet, és gondoskodjon arról, hogy az adatok biztonsági mentése a lemez átméretezési műveleteinek végrehajtása előtt megtörténjen. További információ: [Azure Backup](../../backup/quick-backup-vm-portal.md)rövid útmutató. 

## <a name="expand-an-azure-managed-disk"></a>Azure-beli felügyelt lemez kibontása
Győződjön meg arról, hogy a legújabb [Azure CLI](/cli/azure/install-az-cli2) telepítve van, és be van jelentkezve egy Azure-fiókba az [az login](/cli/azure/reference-index#az-login)használatával.

Ez a cikk egy meglévő virtuális gépet igényel az Azure-ban legalább egy csatlakoztatott és előkészített adatlemezzel. Ha még nem rendelkezik a használni kívánt virtuális géppel, tekintse meg [a virtuális gép létrehozása és előkészítése adatlemezekkel](tutorial-manage-disks.md#create-and-attach-disks)című témakört.

A következő példákban cserélje le a példában szereplő paraméterek nevét, például a *myResourceGroup* és a *myVM* értéket a saját értékeire.

1. A virtuális merevlemezeken végrehajtott műveletek nem hajthatók végre a rendszert futtató virtuális géppel. Szabadítsa fel a virtuális gépet az [az VM felszabadításával](/cli/azure/vm#az-vm-deallocate). Az alábbi példa felszabadítja a *myVM* nevű virtuális gépet a *myResourceGroup* nevű erőforráscsoport-csoportba:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > A virtuális merevlemez kibontásához fel kell osztani a virtuális GÉPET. A virtuális gép leállítása a szolgáltatással `az vm stop` nem szabadítja fel a számítási erőforrásokat. Számítási erőforrások kiadásához használja a következőt: `az vm deallocate` .

1. Egy erőforráscsoport felügyelt lemezeinek listáját az [az Disk List](/cli/azure/disk#az-disk-list)paranccsal tekintheti meg. Az alábbi példa megjeleníti a felügyelt lemezek listáját a *myResourceGroup* nevű erőforráscsoport:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Bontsa ki a szükséges lemezt az [az Disk Update paranccsal](/cli/azure/disk#az-disk-update). A következő példa a *myDataDisk* nevű felügyelt lemezt *200* GB-ra bővíti:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Felügyelt lemez kibontásakor a frissített méret a legközelebbi felügyelt lemez méretére lesz kerekítve. Az elérhető felügyelt lemezek méreteiről és szintjeiről az [Azure Managed Disks áttekintése – díjszabás és számlázás](../managed-disks-overview.md)című cikkben olvashat.

1. Indítsa el a virtuális gépet az [az VM Start](/cli/azure/vm#az-vm-start)paranccsal. A következő példa elindítja a *myVM* nevű virtuális gépet a *myResourceGroup* nevű erőforráscsoporthoz:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Lemezpartíció és fájlrendszer kibontása
Kibontott lemez használatához bontsa ki a mögöttes partíciót és a fájlrendszert.

1. SSH-t a virtuális géphez a megfelelő hitelesítő adatokkal. A virtuális gép nyilvános IP-címét az [az VM show](/cli/azure/vm#az-vm-show)paranccsal tekintheti meg:

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Bontsa ki a mögöttes partíciót és a fájlrendszert.

    a. Ha a lemez már csatlakoztatva van, válassza le:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. `parted`A használatával megtekintheti a lemez adatait, és átméretezheti a partíciót:

    ```bash
    sudo parted /dev/sdc
    ```

    Megtekintheti a meglévő partíciók elrendezésével kapcsolatos információkat `print` . A kimenet a következő példához hasonlóan jelenik meg, amely a mögöttes lemez 215 GB-ot mutatja:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Bontsa ki a partíciót a rel `resizepart` . Adja meg a partíció számát, az *1* értéket és az új partíció méretét:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. A kilépéshez írja be a következőt: `quit` .

1. A partíció átméretezése után ellenőrizze a partíció konzisztenciáját az alábbiakkal `e2fsck` :

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. A fájlrendszer átméretezése az alábbiakkal `resize2fs` :

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Csatlakoztassa a partíciót a kívánt helyre, például `/datadrive` :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Az adatlemez átméretezésének ellenőrzéséhez használja a következőt: `df -h` . A következő példa kimenetében az adatmeghajtó */dev/sdc1* jelenleg 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Következő lépések
* Ha további tárhelyre van szüksége, [adatlemezeket is hozzáadhat egy Linux rendszerű virtuális géphez](add-disk.md). 
* További információ a lemezek titkosításáról: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-overview.md).
