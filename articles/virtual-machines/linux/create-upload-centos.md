---
title: CentOS-alapú linuxos virtuális merevlemez létrehozása és feltöltése
description: Megtudhatja, hogyan hozhat létre és tölthet fel egy CentOS-alapú Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 4745e631bd92675f8dd1ef0d390baa88f7666552
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554666"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>CentOS-alapú virtuális gép előkészítése az Azure-beli használatra

Megtudhatja, hogyan hozhat létre és tölthet fel egy CentOS-alapú Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).

* [CentOS 6. x virtuális gép előkészítése az Azure-hoz](#centos-6x)
* [CentOS 7.0 + virtuális gép előkészítése az Azure-hoz](#centos-70)


## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már telepítette a CentOS (vagy hasonló származtatott) Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik a. vhd fájlok létrehozásához, például egy virtualizációs megoldáshoz, például a Hyper-V-hez. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és a virtuális gép konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**A CentOS telepítési megjegyzései**

* A Linux for Azure előkészítésével kapcsolatos további tippeket a [Linux általános telepítési megjegyzései](create-upload-generic.md#general-linux-installation-notes) című témakörben talál.
* A VHDX formátuma nem támogatott az Azure-ban, csak a **rögzített VHD**.  A lemezt VHD formátumba konvertálhatja a Hyper-V kezelőjével vagy a convert-VHD parancsmag használatával. Ha a VirtualBox-t használja, akkor a **rögzített méretet** a lemez létrehozásakor dinamikusan lefoglalt alapértelmezett értékre kell kijelölni.
* A Linux rendszer telepítésekor azt *javasoljuk* , hogy az LVM helyett standard partíciót használjon (ez általában számos telepítés esetében). Ezzel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen abban az esetben, ha egy operációsrendszer-lemezt már csatlakoztatni kell egy másik, azonos virtuális géphez a hibaelhárításhoz. Az [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) vagy a [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) adatlemezeken is használható.
* **Az UDF-fájlrendszerek csatlakoztatásához kernel-támogatás szükséges.** Az Azure első indításakor a létesítési konfigurációt a rendszer az UDF-formázott adathordozón keresztül továbbítja a linuxos virtuális géphez, amely a vendéghez van csatolva. Az Azure Linux-ügynöknek képesnek kell lennie az UDF fájlrendszer csatlakoztatására a konfiguráció olvasásához és a virtuális gép kiépítéséhez.
* A Linux-kernelek 2.6.37 alatti verziói nem támogatják a NUMA használatát a Hyper-V-n nagyobb méretű virtuálisgép-méretekkel. Ez a probléma elsősorban a régebbi, Red Hat 2.6.32 kernelt használó disztribúciókat érinti, és a RHEL 6,6-ben (kernel-2.6.32-504) rögzítették. A 2.6.37-nál régebbi egyéni kerneleket futtató rendszerek vagy a 2.6.32-504 RHEL-alapú kernelek esetében a rendszerindítási paramétert a `numa=off` grub. conf fájlban található kernel parancssorban kell beállítani. További információ: Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Ne állítson be swap-partíciót az operációsrendszer-lemezen. Erről további információt az alábbi lépésekben találhat.
* Az Azure-ban az összes virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor gondoskodnia kell arról, hogy a nyers lemez mérete a konverzió előtt egy 1MB többszöröse legyen. További információért lásd a [Linux telepítési megjegyzéseit](create-upload-generic.md#general-linux-installation-notes) .

## <a name="centos-6x"></a>CentOS 6. x

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

2. Kattintson a **Kapcsolódás** elemre a virtuális gép konzoljának megnyitásához.

3. A CentOS 6-ban a hálózatkezelő zavarhatja az Azure Linux-ügynököt. A következő parancs futtatásával távolítsa el a csomagot:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Hozza létre vagy szerkessze a fájlt `/etc/sysconfig/network` , és adja hozzá a következő szöveget:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Hozza létre vagy szerkessze a fájlt `/etc/sysconfig/network-scripts/ifcfg-eth0` , és adja hozzá a következő szöveget:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Módosítsa a udev-szabályokat úgy, hogy ne generáljon statikus szabályokat az Ethernet-adapter (ek) számára. Ezek a szabályok problémákat okozhatnak a Microsoft Azure vagy Hyper-V-ben lévő virtuális gépek klónozásakor:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. A következő parancs futtatásával gondoskodjon arról, hogy a hálózati szolgáltatás a rendszerindítás során induljon el:

    ```bash
    sudo chkconfig network on
    ```

8. Ha az Azure-adatközpontokban üzemeltetett OpenLogic-tükröket szeretné használni, cserélje le a `/etc/yum.repos.d/CentOS-Base.repo` fájlt a következő adattárakra.  Ez a (z **) [openlogic]** tárházat is hozzáadja, amely további csomagokat tartalmaz, például az Azure Linux-ügynököt:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > Az útmutató további része feltételezi, hogy legalább a tárházat használja `[openlogic]` , amelyet az alábbi Azure Linux-ügynök telepítésére fog használni.

9. Adja hozzá a következő sort a/etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Futtassa a következő parancsot az aktuális yum-metaadatok törléséhez és a rendszer frissítéséhez a legújabb csomagokkal:

    ```bash
    yum clean all
    ```

    Ha nem hoz létre lemezképet a CentOS régebbi verziójához, ajánlott frissíteni az összes csomagot a legújabb verzióra:

    ```bash
    sudo yum -y update
    ```

    A parancs futtatása után újraindításra lehet szükség.

11. Választható Telepítse a Linux Integration Services (LIS) illesztőprogramjait.

    > [!IMPORTANT]
    > A lépés **szükséges** a CentOS 6,3-es és korábbi verzióihoz, és a későbbi kiadásokhoz nem kötelező.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Azt is megteheti, hogy a [lis letöltési oldalon](https://www.microsoft.com/download/details.aspx?id=55106) a manuális telepítési utasításokat követve telepíti az RPM-t a virtuális gépre.

12. Telepítse az Azure Linux-ügynököt és a függőségeket. A waagent szolgáltatás elindítása és engedélyezése:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    A WALinuxAgent csomag eltávolítja a hálózatkezelő és a hálózatkezelő-GNOME csomagokat, ha azok még nem lettek eltávolítva a 3. lépésben leírtak szerint.

13. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez nyisson meg `/boot/grub/menu.lst` egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Ez azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely segítséget nyújt az Azure támogatásához hibakeresési problémák esetén.

    A fentiek mellett ajánlott *eltávolítani* a következő paramétereket:

    ```console
    rhgb quiet crashkernel=auto
    ```

    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra.  `crashkernel`Ha szükséges, a beállítás meghagyható, de vegye figyelembe, hogy ez a paraméter a virtuális gépen rendelkezésre álló memória mennyiségét legfeljebb 128 MB-kal csökkenti, ami problémát okozhat a kisebb virtuálisgép-méretekben.

    > [!Important]
    > A CentOS 6,5-es és korábbi verzióiban a kernel paramétert is be kell állítani `numa=off` . Lásd: Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik.  Ez általában az alapértelmezett.

15. Ne hozzon létre lapozófájlt az operációsrendszer-lemezen.

    Az Azure Linux-ügynök automatikusan konfigurálhatja a lapozófájlt a virtuális géphez az Azure-ban való üzembe helyezést követően csatlakozó helyi erőforrás lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy *ideiglenes* lemez, és a virtuális gép kiépítésekor kiürítésre kerülhet. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) megfelelő módon módosítsa a következő paramétereket `/etc/waagent.conf` :

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    ```bash
    sudo waagent -force -deprovision+user
    export HISTSIZE=0
    logout
    ```

17. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.



## <a name="centos-70"></a>CentOS 7.0 +

**A CentOS 7 (és hasonló származtatott eszközök) változásai**

Az Azure-hoz készült CentOS 7 virtuális gép előkészítése nagyon hasonlít a CentOS 6-hoz, azonban számos fontos eltérést érdemes megjegyezni:

* A hálózatkezelő csomag már nem ütközik az Azure Linux-ügynökkel. Ez a csomag alapértelmezés szerint telepítve van, ezért azt javasoljuk, hogy ne távolítsa el.
* A GRUB2 mostantól alapértelmezett rendszerbetöltőként van használatban, így a kernel-paraméterek szerkesztésének eljárása módosult (lásd alább).
* A XFS mostantól az alapértelmezett fájlrendszer. Az ext4 fájlrendszer továbbra is használható, ha szükséges.

**Konfigurációs lépések**

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

2. Kattintson a **Kapcsolódás** elemre a virtuális gép konzoljának megnyitásához.

3. Hozza létre vagy szerkessze a fájlt `/etc/sysconfig/network` , és adja hozzá a következő szöveget:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Hozza létre vagy szerkessze a fájlt `/etc/sysconfig/network-scripts/ifcfg-eth0` , és adja hozzá a következő szöveget:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Módosítsa a udev-szabályokat úgy, hogy ne generáljon statikus szabályokat az Ethernet-adapter (ek) számára. Ezek a szabályok problémákat okozhatnak a Microsoft Azure vagy Hyper-V-ben lévő virtuális gépek klónozásakor:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Ha az Azure-adatközpontokban üzemeltetett OpenLogic-tükröket szeretné használni, cserélje le a `/etc/yum.repos.d/CentOS-Base.repo` fájlt a következő adattárakra.  Ez a **[openlogic]** adattárat is hozzáadja, amely tartalmazza az Azure Linux-ügynök csomagjait:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > Az útmutató további része feltételezi, hogy legalább a tárházat használja `[openlogic]` , amelyet az alábbi Azure Linux-ügynök telepítésére fog használni.

7. Futtassa a következő parancsot az aktuális yum-metaadatok törléséhez és a frissítések telepítéséhez:

    ```bash
    sudo yum clean all
    ```

    Ha nem hoz létre lemezképet a CentOS régebbi verziójához, ajánlott frissíteni az összes csomagot a legújabb verzióra:

    ```bash
    sudo yum -y update
    ```

    A parancs futtatása után újraindításra lehet szükség.

8. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez nyisson meg `/etc/default/grub` egy szövegszerkesztőben, és szerkessze a `GRUB_CMDLINE_LINUX` (z) paramétert, például:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ez azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely segítséget nyújt az Azure támogatásához hibakeresési problémák esetén. Emellett kikapcsolja az új CentOS 7 elnevezési konvenciókat a hálózati adapterekhez. A fentiek mellett ajánlott *eltávolítani* a következő paramétereket:

    ```console
    rhgb quiet crashkernel=auto
    ```

    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. `crashkernel`Ha szükséges, a beállítás meghagyható, de vegye figyelembe, hogy ez a paraméter a virtuális gépen rendelkezésre álló memória mennyiségét legfeljebb 128 MB-kal csökkenti, ami problémát okozhat a kisebb virtuálisgép-méretekben.

9. Miután végzett a szerkesztéssel `/etc/default/grub` , futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Ha **VMware-, VirtualBox-vagy KVM** -alapú rendszerképet hoz létre, győződjön meg arról, hogy a Hyper-V-illesztőprogramok szerepelnek a initramfs:

    Szerkesztés `/etc/dracut.conf` , tartalom hozzáadása:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Hozza létre újra a initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Telepítse az Azure Linux-ügynököt és az Azure-beli virtuálisgép-bővítményekkel kapcsolatos függőségeket:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. A Cloud-init telepítése a kiépítés kezelésére

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```


13. A swap konfiguráció nem hoz létre lapozófájlt az operációs rendszer lemezén.

    Korábban az Azure Linux-ügynököt a virtuális géphez a virtuális gép üzembe helyezése után az Azure-ban való kiépítés után a rendszer automatikusan konfigurálja a swap-helyet. Ezt azonban a Cloud-init kezeli, **nem szabad** a Linux-ügynököt az erőforrás lemezének formázására használni, megfelelően módosítani a következő paramétereket `/etc/waagent.conf` :

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Ha a csatlakoztatást, a formázást és a cserét szeretné létrehozni, az alábbiakat teheti:
    * Minden egyes virtuális gép létrehozásakor adja át ezt a felhő-init konfigurációként
    * Használjon egy Felhőbeli init direktívát, amely a virtuális gép létrehozásakor minden alkalommal megteszi ezt a képet:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

14. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history


    # export HISTSIZE=0

    # logout
    ```

15. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="next-steps"></a>Következő lépések

Most már készen áll arra, hogy a CentOS Linux rendszerű virtuális merevlemez használatával új virtuális gépeket hozzon létre az Azure-ban. Ha első alkalommal tölti fel a. vhd-fájlt az Azure-ba, tekintse meg a Linux rendszerű [virtuális gép létrehozása egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd)című témakört.