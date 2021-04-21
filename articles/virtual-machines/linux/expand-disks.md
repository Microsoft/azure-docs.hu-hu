---
title: Virtuális merevlemezek bővítése Linux rendszerű virtuális gépen
description: Megtudhatja, hogyan bővítheti ki a virtuális merevlemezeket Linux rendszerű virtuális gépeken az Azure CLI használatával.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c27b042b78931fd58e43e4bbb06699abe510f385
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762550"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Virtuális merevlemezek bővítése Linux rendszerű virtuális gépen az Azure CLI használatával

Ez a cikk bemutatja, hogyan bonthatja ki a linuxos virtuális gépek felügyelt lemezei az Azure CLI használatával. Hozzáadhat [adatlemezeket,](add-disk.md) amelyek további tárterületet biztosítanak, és kibővíthet egy meglévő adatlemezt is. Az operációs rendszer (OS) alapértelmezett virtuális merevlemez-mérete általában 30 GB Linux rendszerű virtuális gépen az Azure-ban. 

> [!WARNING]
> Mindig győződjön meg arról, hogy a fájlrendszer kifogástalan állapotban van, a lemezpartíciós tábla típusa támogatni fogja az új méretet, és a lemezátméretezési műveletek végrehajtása előtt győződjön meg arról, hogy az adatokról biztonsági mentése van. További információért tekintse meg a [Azure Backup útmutatót.](../../backup/quick-backup-vm-portal.md) 

## <a name="expand-an-azure-managed-disk"></a>Azure Managed Disk bővítése
Győződjön meg arról, hogy a legfrissebb [Azure CLI](/cli/azure/install-az-cli2) van telepítve, és hogy be van jelentkezve egy Azure-fiókba az az [login használatával.](/cli/azure/reference-index#az_login)

Ehhez a cikkhez egy meglévő Azure-beli virtuális gépre van szükség, amelyhez legalább egy adatlemez csatolva és elő van készítve. Ha még nem rendelkezik használható virtuális géptel, lásd: Virtuális gép létrehozása és előkészítése [adatlemezekkel.](tutorial-manage-disks.md#create-and-attach-disks)

A következő mintákban cserélje le a példaparaméter-neveket *(például myResourceGroup* és *myVM)* a saját értékeire.

1. Virtuális merevlemezen nem végezhetők műveletek, ha a virtuális gép fut. Szabadította fel a virtuális gépet az [az vm deallocate sal.](/cli/azure/vm#az_vm_deallocate) Az alábbi példa felszabadítja a *myVM* nevű virtuális gépet a *myResourceGroup nevű erőforráscsoportban:*

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > A virtuális merevlemez bővítéséhez fel kell szabadodni a virtuális gépet. A virtuális gép leállítása `az vm stop` a-val nem oldja fel a számítási erőforrásokat. A számítási erőforrások felszabadítása a következővel oldható `az vm deallocate` fel: .

1. Tekintse meg az erőforráscsoportban lévő felügyelt lemezek listáját az [az disk list listában.](/cli/azure/disk#az_disk_list) Az alábbi példa a *myResourceGroup* nevű erőforráscsoportban található felügyelt lemezek listáját jeleníti meg:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Bontsa ki a szükséges lemezt [az az disk update segítségével.](/cli/azure/disk#az_disk_update) Az alábbi példa *200* GB-ra bővíti a *myDataDisk* nevű felügyelt lemezt:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Felügyelt lemez kibontásakor a rendszer a frissített méretet a legközelebbi felügyelt lemezméretre kerekül. A felügyelt lemezek elérhető méretének és rétegének táblázatát az Azure Managed Disks – [Díjszabás és számlázás témakörben talál.](../managed-disks-overview.md)

1. Indítsa el a virtuális gépet [az az vm start sal.](/cli/azure/vm#az_vm_start) Az alábbi példa elindítja a *myResourceGroup* nevű erőforráscsoportban található *myVM* nevű virtuális gépet:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Lemezpartíció és fájlrendszer bővítése
Kibontott lemez használatához bontsa ki a mögöttes partíciót és a fájlrendszert.

1. Jelentkezzen be SSH-val a virtuális gépre a megfelelő hitelesítő adatokkal. A virtuális gép nyilvános IP-címét az [az vm show használhatja:](/cli/azure/vm#az_vm_show)

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Bontsa ki a mögöttes partíciót és a fájlrendszert.

    a. Ha a lemez már csatlakoztatva van, leválasztja:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. A `parted` használatával megtekintheti a lemezadatokat, és átméretezheti a partíciót:

    ```bash
    sudo parted /dev/sdc
    ```

    A meglévő partícióelrendezéssel kapcsolatos információk megtekintése a `print` következővel: . A kimenet az alábbi példához hasonló, amely azt mutatja, hogy a mögöttes lemez 215 GB:

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

    c. Bontsa ki a partíciót a `resizepart` következővel: . Adja meg az új partíció *partíciószámát (1)* és méretét:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. A kilépéshez írja be a `quit` gombra.

1. A partíció átméretezése után ellenőrizze a partíciókonzisztenciát a `e2fsck` következővel:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Méretezze át a fájlrendszert a `resize2fs` következővel:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Csatlakoztassa a partíciót a kívánt helyre, `/datadrive` például:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Az adatlemez átméretezésének ellenőrzéséhez használja a következőt: `df -h` . Az alábbi példakimeneten látható, hogy a */dev/sdc1* adat meghajtó most már 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Következő lépések
* Ha további tárhelyre van szüksége, adatlemezeket is hozzáadhat [egy Linux rendszerű virtuális géphez.](add-disk.md) 
* A lemeztitkosítással kapcsolatos további információkért lásd: Azure Disk Encryption [linuxos virtuális gépekhez.](disk-encryption-overview.md)
