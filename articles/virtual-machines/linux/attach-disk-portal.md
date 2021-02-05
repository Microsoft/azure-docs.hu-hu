---
title: Adatlemez csatolása Linux rendszerű virtuális géphez
description: A portál használatával új vagy meglévő adatlemezt csatolhat egy linuxos virtuális géphez.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 647564a9b9ca909d2f45ae66673d8beac168a4aa
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575974"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Adatlemez csatlakoztatása Linux rendszerű virtuális géphez a portál használatával 
Ez a cikk bemutatja, hogyan csatolhat új és meglévő lemezeket egy linuxos virtuális géphez a Azure Portal keresztül. [Adatlemezt a Azure Portal egy Windows rendszerű virtuális géphez is csatolhat](../windows/attach-managed-disk-portal.md). 

Mielőtt lemezeket csatol a virtuális géphez, tekintse át a következő tippeket:

* A virtuális gép mérete határozza meg, hogy hány adatlemezt tud csatlakoztatni. Részletekért lásd: [virtuális gépek méretei](../sizes.md).
* A virtuális gépekhez csatolt lemezek valójában az Azure-ban tárolt VHD-fájlok. Részletekért tekintse [meg a Managed Disks bemutatása](../managed-disks-overview.md)című témakört.
* A lemez csatolása után [csatlakoznia kell a Linux rendszerű virtuális géphez az új lemez csatlakoztatásához](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>A virtuális gép megkeresése
1. A virtuális gép megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com/) . Keresse meg és válassza ki a **virtuális gépeket**.
2. Válassza ki a virtuális gépet a listából.
3. A **virtuális gépek** lap **Beállítások** területén válassza a **lemezek** elemet.


## <a name="attach-a-new-disk"></a>Új lemez csatolása

1. A **lemezek** ablaktáblán az **adatlemezek** területen válassza a **Létrehozás és az új lemez csatolása** lehetőséget.

1. Adja meg a felügyelt lemez nevét. Tekintse át az alapértelmezett beállításokat, és szükség szerint frissítse a **tárolási típust**, a **méretet (GIB)**, a **titkosítást** és a **gazdagépek gyorsítótárazását** .
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Ellenőrizze a lemez beállításait.":::


1. Ha elkészült, kattintson a **Mentés** gombra a lap tetején a felügyelt lemez létrehozásához és a virtuális gép konfigurációjának frissítéséhez.


## <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása
1. A **lemezek** ablaktábla **adatlemezek** területén válassza a  **meglévő lemezek csatolása** lehetőséget.
1. Kattintson a **lemez neve** legördülő menüre, és válasszon ki egy lemezt az elérhető felügyelt lemezek listájából. 

1. Kattintson a **Save (Mentés** ) gombra a meglévő felügyelt lemez csatolásához és a virtuális gép konfigurációjának frissítéséhez:
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Csatlakozás a Linux rendszerű virtuális géphez az új lemez csatlakoztatásához
Az új lemez particionálásához, formázásához és csatlakoztatásához, így a linuxos virtuális gép használhatja az SSH-t a virtuális géphez. További információ: [SSH használata Linuxon az Azure-on](mac-create-ssh-keys.md). A következő példa egy virtuális géphez csatlakozik a *10.123.123.25* nyilvános IP-címével, a Felhasználónév *azureuser* használatával: 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>A lemez megkeresése

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
sdc     3:0:0:0       4G
```

Ebben a példában a hozzáadott lemez a következő: `sdc` . Ez egy 0. logikai egység, amely 4 GB.

Összetettebb példaként itt látható, hogy milyen több adatlemez jelenik meg a portálon:

:::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Ellenőrizze a lemez beállításait.":::

A képen láthatja, hogy 3 adatlemez található: 4 GB a LUN 0, 16GB a LUN 1 és 32G a 2. LUN-on.

A következőhöz hasonló lehet `lsblk` :

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

A kimenetből láthatja, `lsblk` hogy a LUN 0 egységben található 4gb `sdc` -lemez, az 1. LUN LEMEZEN lévő 16gb-lemez `sdd` , a 32G-lemez pedig a 2 `sde` . LUN.

### <a name="partition-a-new-disk"></a>Új lemez particionálása

Ha olyan meglévő lemezt használ, amely tartalmaz egy adatkészletet, ugorjon a lemez csatlakoztatása elemre. Ha új lemezt csatol, particionálnia kell a lemezt.

A `parted` segédprogram egy adatlemez particionálására és formázására használható.

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

Hozzon létre egy könyvtárat a fájlrendszer csatlakoztatásához a használatával `mkdir` . Az alábbi példa egy könyvtárat hoz létre a következő helyen `/datadrive` :

```bash
sudo mkdir /datadrive
```

Ezzel a paranccsal `mount` csatlakoztathatja a fájlrendszert. Az alábbi példa a */dev/sdc1* partíciót csatlakoztatja a `/datadrive` csatlakoztatási ponthoz:

```bash
sudo mount /dev/sdc1 /datadrive
```

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

A nano Editort használtuk, így amikor elkészült a fájl szerkesztésével, a `Ctrl+O` fájl írásához és `Ctrl+X` a szerkesztőből való kilépéshez használja a parancsot.

> [!NOTE]
> Ha később eltávolít egy adatlemezt az fstab szerkesztése nélkül, a virtuális gép nem tud elindulni. A legtöbb disztribúció a *sikertelen* és/vagy *nobootwait* fstab-beállításokat tartalmazza. Ezek a beállítások lehetővé teszik a rendszer rendszerindítását akkor is, ha a lemez nem csatlakoztatható a rendszerindítási időpontban. A paraméterekkel kapcsolatos további információkért olvassa el a terjesztés dokumentációját.
> 
> A *nem sikertelen* beállítás biztosítja, hogy a virtuális gép akkor is elindul, ha a fájlrendszer sérült, vagy ha a lemez nem létezik a rendszerindítási időpontban. Ha ezt a lehetőséget választja, a következő témakörben leírtak szerint [nem lehet SSH-t Linux virtuális gépre az FSTAB-hibák miatt](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) .


## <a name="verify-the-disk"></a>A lemez ellenőrzése

Most már újra használhatja a `lsblk` lemez és a csatlakoztatási pont megtekintését.

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

A kimenet a következőhöz hasonlóan fog kinézni:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

Láthatja, hogy `sdc` most már csatlakoztatva van a következőhöz: `/datadrive` .

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

## <a name="next-steps"></a>Következő lépések

További információért és a lemezekkel kapcsolatos hibák elhárításához tekintse meg a [linuxos virtuális gép eszköz nevének módosításainak hibaelhárítása](../troubleshooting/troubleshoot-device-names-problems.md)című témakört.

[Adatlemezt](add-disk.md) az Azure CLI használatával is csatolhat.
