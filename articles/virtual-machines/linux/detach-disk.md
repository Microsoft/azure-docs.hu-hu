---
title: Adatlemez leválasztása Linux rendszerű virtuális gépről – Azure
description: Megtudhatja, hogyan választhat adatlemezt az Azure-beli virtuális gépről az Azure CLI vagy a Azure Portal használatával.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: 29a2cbbf2c390b81aa62b064a7cf93decbaa7457
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565988"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Adatlemez leválasztása Linux rendszerű virtuális gépről

Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ezzel eltávolítja a lemezt a virtuális gépről, de nem távolítja el a tárolóból. Ebben a cikkben egy Ubuntu LTS 16,04-disztribúciót dolgozunk. Ha más eloszlást használ, a lemez leválasztásának utasításai eltérőek lehetnek.

> [!WARNING]
> Ha leválaszt egy lemezt, nem törlődik automatikusan. Ha prémium szintű tárterületre iratkozott fel, akkor továbbra is a lemez tárolási díjait számítjuk fel. További információkért tekintse meg a [díjszabást és a számlázást Premium Storage használatakor](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Csatlakozás a virtuális géphez a lemez leválasztásához

Mielőtt leválasztja a lemezt a parancssori felület vagy a portál használatával, le kell választania a lemezt, és el kell távolítania a hivatkozásokat az fstab-fájlból.

Csatlakozzon a virtuális géphez. Ebben a példában a virtuális gép nyilvános IP-címét *10.0.1.4* a username *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Először keresse meg a leválasztani kívánt adatlemezt. Az alábbi példa a dmesg-et használja az SCSI-lemezek szűrésére:

```bash
dmesg | grep SCSI
```

A kimenet a következő példához hasonló:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Itt a *SDC* a leválasztani kívánt lemez. Meg kell ragadni a lemez UUID-azonosítóját is.

```bash
sudo -i blkid
```

A kimenet a következő példához hasonlóan néz ki:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Szerkessze az */etc/fstab* fájlt a lemezre mutató hivatkozások eltávolításához. 

> [!NOTE]
> Az **/etc/fstab** fájl nem megfelelő szerkesztése nem indítható rendszert eredményezhet. Ha nem biztos a dolgában, a fájl megfelelő szerkesztésével kapcsolatos információkért olvassa el a disztribúció dokumentációját. Azt is javasoljuk, hogy a Szerkesztés előtt hozza létre az/etc/fstab fájl biztonsági másolatát.

Nyissa meg az */etc/fstab* fájlt egy szövegszerkesztőben a következőképpen:

```bash
sudo vi /etc/fstab
```

Ebben a példában a következő sort törölni kell az */etc/fstab* fájlból:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

`umount`A lemez leválasztásához használja a következőt:. Az alábbi példa leválasztja a */dev/sdc1* partíciót a */datadrive* csatlakoztatási pontról:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Adatlemez leválasztása az Azure CLI-vel 

Ez a példa leválasztja a *myDataDisk* lemezt a *myVM* nevű virtuális gépről a *myResourceGroup*-ben.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

A lemez a tárolóban marad, de már nincs csatlakoztatva a virtuális géphez.


## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal

1. A bal oldali menüben válassza a **Virtual Machines** lehetőséget.
1. A virtuális gép panelen válassza a **lemezek** lehetőséget.
1. A **lemezek** panelen a leválasztani kívánt adatlemez jobb széléhez válassza az **X** gombot a lemez leválasztásához.
1. A lemez eltávolítása után válassza a **Mentés** lehetőséget a panel tetején.

A lemez a tárolóban marad, de már nincs csatlakoztatva a virtuális géphez. A lemez nincs törölve.

## <a name="next-steps"></a>Következő lépések
Ha újra fel szeretné használni az adatlemezt, egyszerűen [csatolhatja azt egy másik virtuális géphez](add-disk.md).

Ha törölni szeretné a lemezt, hogy a továbbiakban ne történjen tárolási költség, tekintse meg a nem [csatolt Azure felügyelt és nem felügyelt lemezek keresése és törlése – Azure Portal](../disks-find-unattached-portal.md).