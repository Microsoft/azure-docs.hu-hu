---
title: Adatlemez hozzáadása Linux rendszerű virtuális géphez az Azure CLI használatával
description: Megtudhatja, hogyan vehet fel állandó adatlemezt a linuxos virtuális gépre az Azure CLI-vel
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: adf6198cf12011c77fcf3f93d4b595ea433ddefd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580385"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)

Ez a cikk bemutatja, hogyan csatolhat állandó lemezt a virtuális géphez, így megőrizheti adatait – még akkor is, ha a virtuális gépet karbantartás vagy átméretezés miatt újra kiépítik.


## <a name="attach-a-new-disk-to-a-vm"></a>Új lemez csatolása egy virtuális géphez

Ha új, üres adatlemezt szeretne hozzáadni a virtuális géphez, használja az az [VM Disk Attach](/cli/azure/vm/disk) parancsot a `--new` paraméterrel. Ha a virtuális gép rendelkezésre állási zónában van, a rendszer automatikusan létrehozza a lemezt a virtuális géppel megegyező zónában. További információ: [Availability Zones áttekintése](../../availability-zones/az-overview.md). A következő példa egy *myDataDisk* nevű lemezt hoz létre, amely 50 GB méretű:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása

Meglévő lemez csatolásához keresse meg a lemez AZONOSÍTÓját, és adja át az azonosítót az [az VM Disk Attach](/cli/azure/vm/disk) parancsnak. A következő példa egy *myDataDisk* nevű lemezt kérdez le a *myResourceGroup*-ben, majd csatolja a *myVM* nevű virtuális géphez:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>A lemez formázása és csatlakoztatása

Az új lemez particionálásához, formázásához és csatlakoztatásához, így a linuxos virtuális gép használhatja az SSH-t a virtuális géphez. További információ: [SSH használata Linuxon az Azure-on](mac-create-ssh-keys.md). A következő példa egy virtuális géphez csatlakozik a *10.123.123.25* nyilvános IP-címével, a Felhasználónév *azureuser* használatával:

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>A lemez megkeresése

A virtuális géphez való csatlakozás után meg kell keresnie a lemezt. Ebben a példában a használatával `lsblk` listázjuk a lemezeket. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

A kimenet a következő példához hasonló:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0      50G
```

Itt `sdc` látható a kívánt lemez, mert 50G. Ha nem biztos abban, hogy melyik lemez mérete kizárólag a méreten alapul, akkor nyissa meg a virtuális gép lapot a portálon, válassza a **lemezek** lehetőséget, és ellenőrizze a lemez LUN számát az **adatlemezek** területen. 


### <a name="format-the-disk"></a>A lemez formázása

Formázza a lemezt `parted` , ha a lemez mérete 2 tebibájt (TiB) vagy nagyobb, akkor a GPT particionálást kell használnia, ha az 2TiB alatt van, akkor az MBR-vagy a GPT-particionálást is használhatja. 

> [!NOTE]
> Javasoljuk, hogy a `parted` disztribúcióhoz elérhető legújabb verziót használja.
> Ha a lemez mérete 2 tebibájt (TiB) vagy nagyobb, akkor GPT-particionálást kell használnia. Ha a lemez mérete 2 TiB alatti, akkor MBR-vagy GPT-particionálást is használhat.  


A következő példa a `parted` on `/dev/sdc` -t használja, amely az első adatlemez általában a legtöbb virtuális gépen. Cserélje le a `sdc` elemet a megfelelő beállítással a lemezre. A [XFS](https://xfs.wiki.kernel.org/) fájlrendszer használatával is formázza.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

A segédprogram használatával győződjön [`partprobe`](https://linux.die.net/man/8/partprobe) meg arról, hogy a rendszermag ismeri az új partíciót és a fájlrendszert. A használatának sikertelensége esetén `partprobe` a blkid vagy a lslbk parancsok azonnal nem adhatják vissza az UUID-t az új fájlrendszerhez.


### <a name="mount-the-disk"></a>A lemez csatlakoztatása

Most hozzon létre egy könyvtárat a fájlrendszer csatlakoztatásához a használatával `mkdir` . Az alábbi példa egy könyvtárat hoz létre a következő helyen `/datadrive` :

```bash
sudo mkdir /datadrive
```

Ezzel a paranccsal `mount` csatlakoztathatja a fájlrendszert. A következő példa csatlakoztatja a `/dev/sdc1` partíciót a `/datadrive` csatlakoztatási ponthoz:

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>A csatlakoztatás fenntartása

Annak biztosítása érdekében, hogy a meghajtó újracsatlakoztatása újraindítás után automatikusan megtörténjen, hozzá kell adni az */etc/fstab* fájlhoz. Emellett erősen ajánlott az UUID (univerzálisan egyedi azonosító) használata az */etc/fstab* -ben a meghajtóra, nem csak az eszköz neve (például */dev/sdc1*). Ha indítás közben az operációs rendszer lemezhibát észlel, az UUID használatával elkerülhető egy nem megfelelő lemez a megadott helyre történő csatolása. A többi adatlemez ettől még ugyanazokat az eszközazonosítókat kapja. Az új meghajtó UUID-jének megkereséséhez használja a `blkid` segédprogramot:

```bash
sudo blkid
```

A kimenet a következő példához hasonlóan néz ki:

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> Az **/etc/fstab** fájl nem megfelelő szerkesztése nem indítható rendszert eredményezhet. Ha nem biztos a dolgában, a fájl megfelelő szerkesztésével kapcsolatos információkért olvassa el a disztribúció dokumentációját. Azt is javasoljuk, hogy a Szerkesztés előtt hozza létre az/etc/fstab fájl biztonsági másolatát.

Ezután nyissa meg az */etc/fstab* fájlt egy szövegszerkesztőben a következőképpen:

```bash
sudo nano /etc/fstab
```

Ebben a példában használja az `/dev/sdc1` előző lépésekben létrehozott eszköz UUID értékét, valamint a csatlakoztatási pont `/datadrive` . Adja hozzá a következő sort a fájl végéhez `/etc/fstab` :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

Ebben a példában a nano Editort használjuk, így ha elkészült a fájl szerkesztésével, a `Ctrl+O` fájl írásához és `Ctrl+X` a szerkesztőből való kilépéshez használja a parancsot.

> [!NOTE]
> Ha később eltávolít egy adatlemezt az fstab szerkesztése nélkül, a virtuális gép nem tud elindulni. A legtöbb disztribúció a *sikertelen* és/vagy *nobootwait* fstab-beállításokat tartalmazza. Ezek a beállítások lehetővé teszik a rendszer rendszerindítását akkor is, ha a lemez nem csatlakoztatható a rendszerindítási időpontban. A paraméterekkel kapcsolatos további információkért olvassa el a terjesztés dokumentációját.
>
> A *nem sikertelen* beállítás biztosítja, hogy a virtuális gép akkor is elindul, ha a fájlrendszer sérült, vagy ha a lemez nem létezik a rendszerindítási időpontban. Ha ezt a lehetőséget választja, a következő témakörben leírtak szerint [nem lehet SSH-t Linux virtuális gépre az FSTAB-hibák miatt](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) .
>
> Az Azure virtuális gép soros konzolja a virtuális géphez való hozzáféréshez használható, ha az fstab módosítása rendszerindítási hibát eredményezett. További részletek a [soros konzol dokumentációjában](/troubleshoot/azure/virtual-machines/serial-console-linux)olvashatók.

### <a name="trimunmap-support-for-linux-in-azure"></a>A Linux az Azure-ban történő LEKÉPEZÉSÉNEK megszüntetése-támogatása
Egyes linuxos kernelek támogatják a TRIM/LEKÉPEZÉSÉNEK megszüntetése műveleteket a lemezen lévő nem használt blokkok elvetéséhez. Ez a funkció elsősorban a standard szintű tárolásban hasznos, hogy tájékoztassa az Azure-t arról, hogy a törölt lapok már nem érvényesek, és el lehet dobni, és pénzt takaríthat meg, ha nagyméretű fájlokat hoz létre, majd törli őket.

A Linux rendszerű virtuális gépen kétféleképpen engedélyezhető a TRIM-támogatás. A szokásos módon tekintse meg az ajánlott módszert az eloszlásban:

* Használja a `discard` csatlakoztatási lehetőséget az */etc/fstab*-ben, például:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
    ```
* Bizonyos esetekben a `discard` beállítás teljesítménybeli következményekkel járhat. Azt is megteheti, hogy manuálisan futtatja a `fstrim` parancsot a parancssorból, vagy hozzáadja azt a crontabhoz, hogy rendszeresen fusson:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Hibaelhárítás

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Következő lépések

* A linuxos virtuális gép megfelelő konfigurálásának biztosítása érdekében tekintse át a Linux rendszerű [gépek teljesítményével kapcsolatos javaslatok optimalizálását](/previous-versions/azure/virtual-machines/linux/optimization) ismertető oktatóanyagot.
* Bővítse a tárolókapacitást további lemezek hozzáadásával és a [RAID konfigurálásával](/previous-versions/azure/virtual-machines/linux/configure-raid) a további teljesítmény érdekében.