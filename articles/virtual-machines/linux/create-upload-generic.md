---
title: Linuxos virtuális merevlemez létrehozása és feltöltése
description: Megtudhatja, hogyan hozhat létre és tölthet fel egy Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: 5a62bae8881f20eb7778f179cbecce77f102f6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554632"
---
# <a name="information-for-non-endorsed-distributions"></a>Nem támogatott disztribúciók adatai

Az Azure platform SLA-ja csak akkor érvényes a Linux operációs rendszert futtató virtuális gépekre, ha az egyik [támogatott disztribúciót](endorsed-distros.md) használja. Ezen támogatott disztribúciók esetében az előre konfigurált Linux-lemezképek az Azure piactéren érhetők el.

* [Linux az Azure-ban – támogatott disztribúciók](endorsed-distros.md)
* [Linux-rendszerképek támogatása a Microsoft Azureban](https://support.microsoft.com/kb/2941892)

Az Azure-on futó összes disztribúciónak számos előfeltétele van. Ez a cikk nem lehet átfogó, mivel minden eloszlás eltérő. Előfordulhat, hogy az alábbi feltételek teljesülése esetén is jelentős mértékben kell megcsípni a Linux rendszerét, hogy megfelelően fusson.

Javasoljuk, hogy kezdje az [Azure által támogatott disztribúciók](endorsed-distros.md)egyikével. A következő cikkek bemutatják, hogyan készítheti elő az Azure-ban támogatott különböző támogatott Linux-disztribúciókat:

- [CentOS-alapú disztribúciók](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES és openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)

Ez a cikk általános útmutatást nyújt a Linux-disztribúciók Azure-beli futtatásához.

## <a name="general-linux-installation-notes"></a>Általános linuxos telepítési megjegyzések
* A Hyper-V virtuális merevlemez (VHDX) formátuma nem támogatott az Azure-ban, csak a *rögzített VHD*.  A lemezt VHD formátumba konvertálhatja a Hyper-V kezelőjével vagy a [Convert-VHD](/powershell/module/hyper-v/convert-vhd) parancsmag használatával. Ha a VirtualBox-t használja, a lemez létrehozásakor válassza a **rögzített méret** lehetőséget az alapértelmezett (dinamikusan lefoglalt) helyett.
* Az Azure támogatja a Gen1 (BIOS rendszerindítási) & Gen2 (UEFI rendszerindítási) virtuális gépeket.
* A VHD számára engedélyezett maximális méret 1 023 GB.
* A Linux rendszer telepítésekor azt javasoljuk, hogy a logikai kötet-kezelő (LVM) helyett szabványos partíciókat használjon, amely számos telepítés esetében az alapértelmezett. A standard partíciók használatával elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemez már csatlakoztatva van egy másik, azonos virtuális géphez a hibaelhárításhoz. Az [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) vagy a [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) adatlemezeken is használható.
* Szükség van az UDF-fájlrendszerek csatlakoztatásához szükséges kernel-támogatásra. Az Azure-ban az első indításkor a kiépítési konfigurációt a rendszer a vendéghez csatolt UDF formátumú adathordozó használatával továbbítja a linuxos virtuális géphez. Az Azure Linux-ügynöknek csatlakoztatnia kell az UDF fájlrendszert a konfigurációjának olvasásához és a virtuális gép kiépítéséhez.
* A 2.6.37-nál korábbi Linux kernel verziók nem támogatják a NUMA használatát a Hyper-V-n nagyobb méretű virtuálisgép-méretekkel. Ez a probléma elsősorban a régebbi, Red Hat 2.6.32 kernelt használó disztribúciókat érinti, és Red Hat Enterprise Linux (RHEL) 6,6 (kernel-2.6.32-504) rögzített. A 2.6.37-nál régebbi egyéni kerneleket futtató rendszerek vagy a 2.6.32-504 RHEL-alapú kernelek esetében a rendszerindítási paramétert a `numa=off` grub. conf fájlban lévő kernel parancssorban kell beállítani. További információ: [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Ne állítson be swap-partíciót az operációsrendszer-lemezen. A Linux-ügynök konfigurálható úgy, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén az alábbi lépésekben leírtak szerint.
* Minden Azure-beli virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor győződjön meg arról, hogy a nyers lemez mérete 1 MB-nál több, a konverzió előtt, az alábbi lépések szerint.

### <a name="installing-kernel-modules-without-hyper-v"></a>Kernel-modulok telepítése Hyper-V nélkül
Az Azure a Hyper-V hypervisoron fut, így a Linux bizonyos kernel modulokat igényel az Azure-ban való futtatáshoz. Ha olyan virtuális gépet használ, amely a Hyper-V-n kívül lett létrehozva, akkor előfordulhat, hogy a Linux-telepítők nem tartalmazzák a Hyper-V illesztőprogramjait a kezdeti Ramdisk-ben (initrd vagy initramfs), kivéve, ha a virtuális gép nem észleli, hogy egy Hyper-V környezetben fut. Ha más virtualizációs rendszert (például VirtualBox, KVM stb.) használ a Linux-rendszerkép előkészítéséhez, előfordulhat, hogy újra kell építenie a initrd, hogy legalább a hv_vmbus és hv_storvsc kernel-modulok elérhetők legyenek a kezdeti Ramdisk-ben.  Ez az ismert probléma a felsőbb rétegbeli Red Hat-disztribúción alapuló rendszerek, és esetleg mások számára is lehetséges.

A initrd vagy initramfs-rendszerkép újraépítési mechanizmusa a terjesztéstől függően változhat. A megfelelő eljáráshoz olvassa el a disztribúció dokumentációját vagy támogatását.  Íme egy példa arra, hogyan lehet a initrd újraépíteni a `mkinitrd` segédprogram használatával:

1. A meglévő initrd-rendszerkép biztonsági mentése:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Hozza létre újra a initrd a hv_vmbus és hv_storvsc kernel-modulokkal:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Virtuális merevlemezek átméretezése
Az Azure-beli VHD-lemezképeknek egy 1 MB-ra igazított virtuális mérettel kell rendelkezniük.  A Hyper-V használatával létrehozott virtuális merevlemezek általában megfelelően vannak igazítva.  Ha a virtuális merevlemez nem megfelelően van igazítva, az alábbihoz hasonló hibaüzenet jelenhet meg, amikor megpróbál létrehozni egy rendszerképet a VHD-ből.

* A VHD http: \/ / \<mystorageaccount> . blob.Core.Windows.net/VHDs/MyLinuxVM.vhd virtuális mérete 21475270656 bájtnál nem támogatott. A méretnek egész számnak kell lennie (MB-ban).

Ebben az esetben méretezze át a virtuális gépet a Hyper-V Manager konzol vagy a [Resize-VHD PowerShell-](/powershell/module/hyper-v/resize-vhd) parancsmag használatával.  Ha nem Windows-környezetben fut, javasoljuk, `qemu-img` hogy a használatával alakítsa át (ha szükséges), és méretezze át a VHD-t.

> [!NOTE]
> Létezik egy [ismert hiba a qemu-IMG](https://bugs.launchpad.net/qemu/+bug/1490611) verzióban >= 2.2.1, amely egy nem megfelelően formázott VHD-t eredményez. A probléma a QEMU 2,6-es verziójában lett kijavítva. Javasoljuk, hogy a `qemu-img` 2.2.0 vagy az alacsonyabb, vagy a 2,6 vagy újabb rendszer használatát javasolja.
> 

1. A virtuális merevlemez átméretezése közvetlenül olyan eszközökkel történhet, mint a `qemu-img` vagy `vbox-manage` egy nem indítható virtuális merevlemez.  Javasoljuk, hogy először konvertálja a VHD-t egy nyers lemezképre.  Ha a virtuálisgép-lemezkép nyers lemezképként lett létrehozva (egyes hypervisorok alapértelmezett értéke, például KVM), akkor kihagyhatja ezt a lépést.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Kiszámítja a lemezkép szükséges méretét úgy, hogy a virtuális méret 1 MB-ra legyen igazítva.  A következő bash rendszerhéj-parancsfájl a `qemu-img info` lemezkép virtuális méretének meghatározásához használja, majd a következő 1 MB-ra kiszámítja a méretet.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Méretezze át a nyers lemezt a `$rounded_size` fent megadott módon.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Most alakítsa vissza a nyers lemezt egy rögzített méretű VHD-re.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Vagy a qemu 2.6 + verziójában adja meg a `force_size` kapcsolót.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Linux kernel-követelmények

A Hyper-V és az Azure-hoz készült Linux Integration Services (LIS) illesztőprogramok közvetlenül a felsőbb rétegbeli linuxos kernelhez járulnak hozzá. Számos, a Linux kernel legújabb verzióját (például 3. x) tartalmazó disztribúció már rendelkezik ezekkel az illesztőprogramokkal, vagy egyéb módon biztosítja ezeknek az illesztőprogramoknak a backported-verzióit a rendszermagban.  Ezek az illesztőprogramok folyamatosan frissülnek a felsőbb rétegbeli kernelben új javításokkal és szolgáltatásokkal, így ha lehetséges, javasoljuk, hogy futtasson egy [támogatott disztribúciót](endorsed-distros.md) , amely tartalmazza ezeket a javításokat és frissítéseket.

Ha a 6,0-es és a 6,3-es Red Hat Enterprise Linux-es verziójú változatot futtatja, akkor telepítenie kell a [Hyper-V legújabb lis-illesztőprogramjait](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). A RHEL 6.4 + (és a származtatott) verziótól kezdődően az LIS-illesztőprogramok már szerepelnek a rendszermagban, így nincs szükség további telepítési csomagokra.

Ha egyéni kernelre van szükség, javasoljuk, hogy a legújabb kernel-verziót (például a 3.8 +-ot). A saját kernelt fenntartó disztribúciók vagy szállítók esetében rendszeresen vezetnie kell az LIS-illesztőprogramokat a felsőbb rétegbeli kernelből az egyéni rendszermagba.  Még ha már fut egy viszonylag új kernel-verzió is, javasoljuk, hogy kövesse nyomon az összes felsőbb rétegbeli javítást az LIS-illesztőprogramokban, és szükség szerint vezetnie azokat. A LIS-illesztőprogram forrásfájljait a [karbantartók](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) fájlban kell megadni a Linux kernel forrás fájában:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
A következő javításokat kell tartalmazniuk a kernelben. Ez a lista nem fejezhető be az összes disztribúcióhoz.

* [ata_piix: a lemezek elhalasztása a Hyper-V-illesztőprogramok számára alapértelmezés szerint](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: fiók továbbítása az átviteli útvonalon az átvitt csomagoknál](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: a WRITE_SAME használatának elkerülése](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: a RAID és a virtuális gazdagép adapter-illesztőprogramjaihoz tartozó írás letiltása](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL mutató hivatkozásának kijavítása](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: a gyűrűs puffer meghibásodása I/O-lefagyást eredményezhet](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: a __scsi_remove_device kettős végrehajtásának elleni védelem](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Az Azure Linux-ügynök
Az [Azure Linux-ügynök](../extensions/agent-linux.md) `waagent` egy Linux rendszerű virtuális gépet foglal le az Azure-ban. A legújabb verziót, a fájlokkal kapcsolatos problémákat vagy a lekéréses kérelmeket a [Linux-ügynök GitHub](https://github.com/Azure/WALinuxAgent)-tárházában szerezheti be.

* A Linux-ügynököt az Apache 2,0 licenc alatt bocsátjuk ki. Számos disztribúció már biztosít RPM vagy. deb csomagokat az ügynök számára, és ezeket a csomagokat egyszerűen telepítheti és frissítheti.
* Az Azure Linux-ügynökhöz a Python v 2.6 + verzió szükséges.
* Az ügynöknek a Python-pyasn1 modulra is szüksége van. A legtöbb disztribúció külön csomagként biztosítja ezt a modult a telepítéshez.
* Bizonyos esetekben előfordulhat, hogy az Azure Linux-ügynök nem kompatibilis a hálózatkezelő. A disztribúciók által biztosított RPM/deb csomagok többsége a hálózatkezelő-t a waagent-csomagba ütközőként konfigurálja. Ezekben az esetekben a Linux-ügynök csomag telepítésekor a rendszer eltávolítja a hálózatkezelő.
* Az Azure Linux-ügynöknek a [minimális támogatott verziónál](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)nagyobbnak vagy magasabbnak kell lennie.

## <a name="general-linux-system-requirements"></a>Általános Linux rendszerre vonatkozó követelmények

1. Módosítsa a kernel rendszerindítási sorát a GRUB-ban vagy a GRUB2, hogy tartalmazza a következő paramétereket, hogy az összes konzolos üzenet el legyen küldve az első soros portra. Ezek az üzenetek segítséget nyújthatnak az Azure-támogatásban az esetleges problémák hibakeresése érdekében.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Javasoljuk továbbá, hogy ha vannak ilyenek, *távolítsa* el a következő paramétereket.
    ```  
    rhgb quiet crashkernel=auto
    ```
    A grafikus és a csendes rendszerindítás nem hasznos felhőalapú környezetben, ahol a soros portra érkező összes naplót szeretnénk használni. `crashkernel`Ha szükséges, a beállítás meghagyható, de vegye figyelembe, hogy ez a paraméter kevesebb, mint 128 MB-tal csökkenti a virtuális gép rendelkezésre álló memóriájának mennyiségét, ami problémát okozhat a kisebb virtuálisgép-méreteknél.

1. Telepítse az Azure Linux-ügynököt.
  
    A Linux-rendszerkép Azure-beli üzembe helyezéséhez az Azure Linux-ügynök szükséges.  Számos disztribúció biztosítja az ügynököt RPM vagy. deb csomagként (a csomagot általában WALinuxAgent vagy WALinuxAgent nevezik).  Az ügynököt manuálisan is telepítheti a [Linux-ügynök útmutatójának](../extensions/agent-linux.md)lépéseit követve.

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van, és úgy van konfigurálva, hogy indításkor induljon el.  Ez a konfiguráció általában az alapértelmezett.

1. Ne hozzon létre lapozófájlt az operációsrendszer-lemezen.
  
    Az Azure Linux-ügynök automatikusan konfigurálhatja a lapozófájlt a virtuális géphez az Azure-ban való üzembe helyezést követően csatlakozó helyi erőforrás lemez használatával. A helyi erőforrás lemez egy *ideiglenes* lemez, és előfordulhat, hogy a virtuális gép kiépítésekor ki van ürítve. Az Azure Linux-ügynök telepítése után (2. lépés) szükség szerint módosítsa a következő paramétereket a/etc/waagent.conf.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > A VirtualBox-on a következő hibaüzenet jelenhet meg a Futtatás után: `waagent -force -deprovision` `[Errno 5] Input/output error` . Ez a hibaüzenet nem kritikus, és figyelmen kívül hagyható.

* Állítsa le a virtuális gépet, és töltse fel a VHD-t az Azure-ba.