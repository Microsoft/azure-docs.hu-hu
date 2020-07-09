---
title: Oracle Linux VHD létrehozása és feltöltése
description: Megtudhatja, hogyan hozhat létre és tölthet fel egy Oracle Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: gbowerman
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: 5e144f63358275292ec224a63ed3ca61f809297a
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135292"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Oracle Linux virtuális gép előkészítése az Azure-hoz

Ez a cikk azt feltételezi, hogy már telepített egy Oracle Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik a. vhd fájlok létrehozásához, például egy virtualizációs megoldáshoz, például a Hyper-V-hez. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és a virtuális gép konfigurálása](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="oracle-linux-installation-notes"></a>Oracle Linux telepítési megjegyzések
* A Linux for Azure előkészítésével kapcsolatos további tippeket a [Linux általános telepítési megjegyzései](create-upload-generic.md#general-linux-installation-notes) című témakörben talál.
* A Hyper-V és az Azure támogatási Oracle Linux a nem törhető vállalati rendszermaggal (UEK) vagy a Red hat-kompatibilis Kernelrel.
* Az Oracle UEK2 nem támogatott a Hyper-V-ben és az Azure-ban, mert nem tartalmazza a szükséges illesztőprogramokat.
* A VHDX formátuma nem támogatott az Azure-ban, csak a **rögzített VHD**.  A lemezt VHD formátumba konvertálhatja a Hyper-V kezelőjével vagy a convert-VHD parancsmag használatával.
* A Linux rendszer telepítésekor azt javasoljuk, hogy az LVM helyett standard partíciót használjon (ez általában számos telepítés esetében). Ezzel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemezt egy másik virtuális géphez kell csatolni a hibaelhárításhoz. Az [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy a [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) adatlemezeken is használható, ha az előnyben részesített.
* A 2.6.37-nál korábbi Linux kernel verziók nem támogatják a NUMA használatát a Hyper-V-n nagyobb méretű virtuálisgép-méretekkel. Ez a probléma elsősorban a régebbi, Red Hat 2.6.32 kernelt használó disztribúciókat érinti, és a Oracle Linux 6,6-es és újabb verzióiban lett javítva
* Ne állítson be swap-partíciót az operációsrendszer-lemezen. A Linux-ügynök úgy konfigurálható, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén.  Erről további információt az alábbi lépésekben találhat.
* Az Azure-ban az összes virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor gondoskodnia kell arról, hogy a nyers lemez mérete a konverzió előtt egy 1MB többszöröse legyen. További információért lásd a [Linux telepítési megjegyzéseit](create-upload-generic.md#general-linux-installation-notes) .
* Győződjön meg arról, hogy az `Addons` adattár engedélyezve van. Szerkessze a fájlt `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) vagy `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux 7), és módosítsa a sort a következőre `enabled=0` `enabled=1` : **[ol6_addons]** vagy **[ol7_addons]** .

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6,4 és újabb verziók
Az Azure-ban való futtatáshoz a virtuális gép operációs rendszerében meghatározott konfigurációs lépéseket kell végrehajtania.

1. A Hyper-V kezelőjének középső ablaktábláján válassza ki a virtuális gépet.
2. Kattintson a **Kapcsolódás** gombra a virtuális gép ablakának megnyitásához.
3. A következő parancs futtatásával távolítsa el a hálózatkezelő:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Megjegyzés:** Ha a csomag még nincs telepítve, akkor a parancs hibaüzenettel meghiúsul. Ez a várható eredmény.
4. Hozzon létre egy **hálózat** nevű fájlt a `/etc/sysconfig/` könyvtárban, amely a következő szöveget tartalmazza:

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Hozzon létre egy **ifcfg-eth0** nevű fájlt a `/etc/sysconfig/network-scripts/` könyvtárban, amely a következő szöveget tartalmazza:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Módosítsa a udev-szabályokat úgy, hogy ne generáljon statikus szabályokat az Ethernet-adapter (ek) számára. Ezek a szabályok problémákat okozhatnak a Microsoft Azure vagy Hyper-V-ben lévő virtuális gépek klónozásakor:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. A következő parancs futtatásával gondoskodjon arról, hogy a hálózati szolgáltatás a rendszerindítás során induljon el:

    ```console
    # chkconfig network on
    ```

8. Telepítse a Python-pyasn1 a következő parancs futtatásával:

    ```console
    # sudo yum install python-pyasn1
    ```

9. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez nyissa meg a "/boot/grub/menu.lst" szöveget egy szövegszerkesztőben, és győződjön meg arról, hogy a kernel a következő paramétereket tartalmazza:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Ezzel biztosítható, hogy az összes konzol üzenetei az első soros porton legyenek elküldve, amely a hibakeresési problémákkal segíti az Azure-támogatást.
   
   A fentiek mellett ajánlott *eltávolítani* a következő paramétereket:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra.
   
   `crashkernel`Ha szükséges, a beállítás meghagyható, de vegye figyelembe, hogy ez a paraméter a virtuális gépen rendelkezésre álló memória mennyiségét legfeljebb 128 MB-kal csökkenti, ami problémát okozhat a kisebb virtuálisgép-méretekben.
10. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik.  Ez általában az alapértelmezett.
11. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával. A legújabb verzió a 2.0.15.

    ```console
    # sudo yum install WALinuxAgent
    ```

    Vegye figyelembe, hogy a WALinuxAgent csomag telepítése eltávolítja a hálózatkezelő és a hálózatkezelő-GNOME csomagokat, ha azok még nem lettek eltávolítva a 2. lépésben leírtak szerint.
12. Ne hozzon létre lapozófájlt az operációsrendszer-lemezen.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatja a lapozófájlt a virtuális géphez az Azure-ban való üzembe helyezést követően csatlakozó helyi erőforrás lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy *ideiglenes* lemez, és a virtuális gép kiépítésekor kiürítésre kerülhet. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) a/etc/waagent.conf megfelelően módosítsa a következő paramétereket:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7,0 és újabb verziók
**Változások a Oracle Linux 7**

Egy Oracle Linux 7 virtuális gép Azure-hoz való előkészítése nagyon hasonlít a Oracle Linux 6-hoz, azonban számos fontos eltérést érdemes megjegyezni:

* Az Azure támogatja a Oracle Linux a nem törhető vállalati rendszermaggal (UEK) vagy a Red hat-kompatibilis Kernelrel. A UEK ajánlott Oracle Linux.
* A hálózatkezelő csomag már nem ütközik az Azure Linux-ügynökkel. Ez a csomag alapértelmezés szerint telepítve van, ezért azt javasoljuk, hogy ne távolítsa el.
* A GRUB2 mostantól alapértelmezett rendszerbetöltőként van használatban, így a kernel-paraméterek szerkesztésének eljárása módosult (lásd alább).
* A XFS mostantól az alapértelmezett fájlrendszer. Az ext4 fájlrendszer továbbra is használható, ha szükséges.

**Konfigurációs lépések**

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.
2. Kattintson a **Kapcsolódás** elemre a virtuális gép konzoljának megnyitásához.
3. Hozzon létre egy **hálózat** nevű fájlt a `/etc/sysconfig/` könyvtárban, amely a következő szöveget tartalmazza:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Hozzon létre egy **ifcfg-eth0** nevű fájlt a `/etc/sysconfig/network-scripts/` könyvtárban, amely a következő szöveget tartalmazza:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Módosítsa a udev-szabályokat úgy, hogy ne generáljon statikus szabályokat az Ethernet-adapter (ek) számára. Ezek a szabályok problémákat okozhatnak a Microsoft Azure vagy Hyper-V-ben lévő virtuális gépek klónozásakor:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. A következő parancs futtatásával gondoskodjon arról, hogy a hálózati szolgáltatás a rendszerindítás során induljon el:

    ```console
    # sudo chkconfig network on
    ```

7. Telepítse a Python-pyasn1 csomagot a következő parancs futtatásával:

    ```console
    # sudo yum install python-pyasn1
    ```

8. Futtassa a következő parancsot az aktuális yum-metaadatok törléséhez és a frissítések telepítéséhez:

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez nyissa meg a "/etc/default/grub" kifejezést egy szövegszerkesztőben, és szerkessze a `GRUB_CMDLINE_LINUX` paramétert, például:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ez azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely segítséget nyújt az Azure támogatásához hibakeresési problémák esetén. Emellett kikapcsolja a hálózati adapterek elnevezési konvencióit a Oracle Linux 7 környezetben a nem törhető vállalati Kernelrel. A fentiek mellett ajánlott *eltávolítani* a következő paramétereket:

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra.
   
   `crashkernel`Ha szükséges, a beállítás meghagyható, de vegye figyelembe, hogy ez a paraméter a virtuális gépen rendelkezésre álló memória mennyiségét legfeljebb 128 MB-kal csökkenti, ami problémát okozhat a kisebb virtuálisgép-méretekben.
10. Miután befejezte a "/etc/default/grub" szerkesztését, futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik.  Ez általában az alapértelmezett.
12. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

    ```console
    # sudo yum install WALinuxAgent
    # sudo systemctl enable waagent
    ```

13. Ne hozzon létre lapozófájlt az operációsrendszer-lemezen.
    
    Az Azure Linux-ügynök automatikusan konfigurálhatja a lapozófájlt a virtuális géphez az Azure-ban való üzembe helyezést követően csatlakozó helyi erőforrás lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy *ideiglenes* lemez, és a virtuális gép kiépítésekor kiürítésre kerülhet. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) megfelelően módosítsa a következő paramétereket a/etc/waagent.conf megfelelő módon:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

14. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:
    
    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

15. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="next-steps"></a>Következő lépések
Most már készen áll a Oracle Linux. vhd fájl használatára az Azure-beli új virtuális gépek létrehozásához. Ha első alkalommal tölti fel a. vhd-fájlt az Azure-ba, tekintse meg a Linux rendszerű [virtuális gép létrehozása egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd)című témakört.

