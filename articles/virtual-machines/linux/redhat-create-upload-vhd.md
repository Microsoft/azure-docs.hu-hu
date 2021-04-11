---
title: Red Hat Enterprise Linux VHD létrehozása és feltöltése az Azure-ban való használatra
description: Ismerje meg, hogyan hozhat létre és tölthet fel egy Red Hat Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: danielsollondon
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: ff22010f12a7d76e60b629c4c1c4e24946d4af5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552966"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Red Hat-alapú virtuális gép előkészítése az Azure-beli használatra

Ebből a cikkből megtudhatja, hogyan készítheti elő Red Hat Enterprise Linux (RHEL) virtuális gépet az Azure-ban való használatra. A cikkben tárgyalt RHEL-verziók 6,7 + és 7.1 +. A cikkben tárgyalt, a Hyper-V, a kernel-alapú virtuális gép (KVM) és a VMware. A Red Hat felhőalapú hozzáférési programjában való részvételre vonatkozó jogosultsági követelményekkel kapcsolatos további információkért tekintse [meg a Red Hat Cloud Access webhelyét](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) , és [futtassa a RHEL az Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure)-ban című témakört. A RHEL-lemezképek készítésének automatizálásához lásd: az [Azure Image Builder](../image-builder-overview.md).

## <a name="hyper-v-manager"></a>Hyper-V kezelője

Ebből a szakaszból megtudhatja, hogyan készítse elő a [RHEL 6](#rhel-6-using-hyper-v-manager), [RHEL 7](#rhel-7-using-hyper-v-manager)vagy [RHEL 8](#rhel-8-using-hyper-v-manager) virtuális gépet a Hyper-V kezelőjével.

### <a name="prerequisites"></a>Előfeltételek
Ez a szakasz azt feltételezi, hogy már beszerzett egy ISO-fájlt a Red Hat webhelyről, és telepítette a RHEL-lemezképet egy virtuális merevlemezre (VHD). A Hyper-V kezelőjének operációsrendszer-rendszerkép telepítéséhez való használatával kapcsolatos további információkért lásd: [a Hyper-v szerepkör telepítése és a virtuális gép konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**RHEL-telepítési megjegyzések**

* Az Azure nem támogatja a VHDX formátumot. Az Azure csak a rögzített VHD-t támogatja. A Hyper-V kezelőjével átalakíthatja a lemezt VHD formátumba, vagy használhatja a convert-VHD parancsmagot is. Ha VirtualBox-t használ, a lemez létrehozásakor válassza a **rögzített méret** lehetőséget a dinamikusan lefoglalt alapértelmezett beállítással szemben.
* Az Azure támogatja a Gen1 (BIOS rendszerindítási) & Gen2 (UEFI rendszerindítási) virtuális gépeket.
* A VHD számára engedélyezett maximális méret 1 023 GB.
* A logikai kötet-kezelő (LVM) támogatott, és az operációsrendszer-lemezen vagy az Azure-beli virtuális gépeken található adatlemezeken is használható. Általánosságban azonban ajánlott standard partíciókat használni az operációsrendszer-lemezen az LVM helyett. Ezzel a gyakorlattal elkerülhető, hogy az LVM Name ütközik a klónozott virtuális gépekkel, különösen akkor, ha az operációs rendszer lemezét egy másik azonos virtuális géphez kell csatlakoztatni a hibaelhárításhoz. Lásd még: az  [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) és a [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) dokumentációja.
* Az **univerzális lemezes formázású (UDF-) fájlrendszerek kernel-támogatása szükséges**. Az Azure első indításakor a vendéghez csatlakoztatott UDF formátumú adathordozó továbbítja a kiépítési konfigurációt a linuxos virtuális gépre. Az Azure Linux-ügynöknek képesnek kell lennie csatlakoztatni az UDF fájlrendszert a konfigurációjának olvasásához és a virtuális gép üzembe helyezéséhez, így a kiépítés sikertelen lesz.
* Ne állítson be swap partíciót az operációs rendszer lemezén. Erről további információt a következő lépésekben találhat.

* Az Azure-ban az összes virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor gondoskodnia kell arról, hogy a nyers lemez mérete a konverzió előtt egy 1MB többszöröse legyen. További részleteket az alábbi lépésekben találhat. További információért lásd a [Linux telepítési megjegyzéseit](create-upload-generic.md#general-linux-installation-notes) is.

### <a name="rhel-6-using-hyper-v-manager"></a>RHEL 6 a Hyper-V kezelőjével

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

1. Kattintson a **Kapcsolódás** elemre a virtuális gép konzoljának megnyitásához.

1. A RHEL 6 hálózatkezelő az Azure Linux-ügynököt is zavarhatja. A következő parancs futtatásával távolítsa el a csomagot:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Helyezze át (vagy távolítsa el) a udev-szabályokat, hogy elkerülje az Ethernet-interfész statikus szabályainak létrehozását. Ezek a szabályok problémákat okozhatnak a Microsoft Azure vagy Hyper-V-ben lévő virtuális gépek klónozásakor:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```console
    # sudo chkconfig network on
    ```

1. Regisztrálja a Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. A WALinuxAgent-csomagot `WALinuxAgent-<version>` leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. A módosítás elvégzéséhez nyisson meg `/boot/grub/menu.lst` egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Ez azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely segítséget nyújt az Azure támogatásához hibakeresési problémák esetén.
    
    Emellett azt javasoljuk, hogy távolítsa el a következő paramétereket:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
    
    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra.  Ha szükséges, hagyja a `crashkernel` beállítást. Vegye figyelembe, hogy ez a paraméter a virtuális gépen elérhető memória mennyiségét 128 MB vagy újabb értékkel csökkenti. Előfordulhat, hogy ez a konfiguráció a kisebb virtuálisgép-méretekkel kapcsolatos problémát okoz.


1. Győződjön meg arról, hogy a Secure Shell-(SSH-) kiszolgáló telepítve van és úgy van konfigurálva, hogy indításkor induljon el, ami általában az alapértelmezett. Módosítsa a/etc/ssh/sshd_config a következő sor belefoglalásával:

    ```config
    ClientAliveInterval 180
    ```

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

    A WALinuxAgent csomag telepítése eltávolítja a hálózatkezelő és a hálózatkezelő-GNOME csomagokat, ha azok még nem lettek eltávolítva a 3. lépésben.

1. Ne hozzon létre lapozófájlt az operációs rendszer lemezén.

    Az Azure Linux-ügynök a virtuális gép üzembe helyezése után automatikusan konfigurálhatja a swap-helyet a virtuális géphez csatlakoztatott helyi erőforrás-lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy ideiglenes lemez, és a virtuális gép kiépítésekor a rendszer kiüríti azt. Miután telepítette az Azure Linux-ügynököt az előző lépésben, megfelelően módosítsa a következő paramétereket a/etc/waagent.conf-ben:

    ```config-cong
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. A következő parancs futtatásával szüntesse meg az előfizetés regisztrációját (ha szükséges):

    ```console
    # sudo subscription-manager unregister
    ```

1. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```

1. Kattintson a **művelet**  >  **leállítása** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.


### <a name="rhel-7-using-hyper-v-manager"></a>RHEL 7 a Hyper-V kezelőjével

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

1. Kattintson a **Kapcsolódás** elemre a virtuális gép konzoljának megnyitásához.

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```console
    # sudo systemctl enable network
    ```

1. Regisztrálja a Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. A módosítás elvégzéséhez nyisson meg `/etc/default/grub` egy szövegszerkesztőben, és szerkessze a (z `GRUB_CMDLINE_LINUX` ) paramétert. Például:

    
    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1
    ```
   
    Ez azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, és engedélyezze az interakciót a soros konzollal, amely segítséget nyújt az Azure támogatásához hibakeresési problémákkal. Ez a konfiguráció a hálózati adapterek új RHEL 7 elnevezési konvencióit is kikapcsolja.

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. Ha szükséges, hagyja a `crashkernel` beállítást. Vegye figyelembe, hogy ez a paraméter a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel csökkenti, ami problémát okozhat a kisebb virtuálisgép-méreteknél.

1. Miután végzett a szerkesztéssel `/etc/default/grub` , futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    > [!NOTE]
    > UEFI-kompatibilis virtuális gép feltöltése esetén a grub frissítésére szolgáló parancs `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg` .

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik, ami általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sor befoglalásával:

    ```config
    ClientAliveInterval 180
    ```

1. A WALinuxAgent-csomagot `WALinuxAgent-<version>` leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. A következő parancs futtatásával telepítse az Azure Linux-ügynököt, a Cloud-init és az egyéb szükséges segédprogramokat:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. A Cloud-init konfigurálása a kiépítés kezelésére:

    1. A waagent konfigurálása a Cloud-init számára:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Ha egy adott virtuális gépet telepít át, és nem kíván általánosított rendszerképet létrehozni, állítsa be a `Provisioning.Agent=disabled` `/etc/waagent.conf` konfigurációt.
    
    1. Csatlakoztatások konfigurálása:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Az Azure-adatforrás konfigurálása:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF
    ```

    1. Ha be van állítva, távolítsa el a meglévő swapfile:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Cloud-init naplózás konfigurálása:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. A swap konfiguráció nem hoz létre lapozófájlt az operációs rendszer lemezén.

    Korábban az Azure Linux-ügynököt a virtuális géphez a virtuális gép üzembe helyezése után az Azure-ban való kiépítés után a rendszer automatikusan konfigurálja a swap-helyet. Ezt azonban a Cloud-init kezeli, **nem szabad** a Linux-ügynököt az erőforrás lemezének formázására használni, megfelelően módosítani a következő paramétereket `/etc/waagent.conf` :

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
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
1. Ha törölni szeretné az előfizetés regisztrációját, futtassa a következő parancsot:

    ```console
    # sudo subscription-manager unregister
    ```

1. Megszüntetése

    Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    > [!CAUTION]
    > Ha egy adott virtuális gépet telepít át, és nem kíván általánosított rendszerképet létrehozni, ugorja át a megszüntetési lépést. A parancs futtatásakor `waagent -force -deprovision` a forrásszámítógép használhatatlanná válik, ez a lépés csak általánosított rendszerkép létrehozására szolgál.
    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    

1. Kattintson a **művelet**  >  **leállítása** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

### <a name="rhel-8-using-hyper-v-manager"></a>RHEL 8 a Hyper-V kezelőjével

1. A Hyper-V kezelőjében válassza ki a virtuális gépet.

1. Kattintson a **Kapcsolódás** elemre a virtuális gép konzoljának megnyitásához.

1. A következő parancs futtatásával győződjön meg arról, hogy a hálózati kezelő szolgáltatás indításkor elindul:

    ```console
    # sudo systemctl enable NetworkManager.service
    ```

1. Konfigurálja úgy a hálózati adaptert, hogy automatikusan induljon a rendszerindításkor, és DHCP-t használjon:

    ```console
    # nmcli con mod eth0 connection.autoconnect yes ipv4.method auto
    ```


1. Regisztrálja a Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket tartalmazzon az Azure-hoz, és engedélyezze a soros konzolt. 

    1. Az aktuális GRUB-paraméterek eltávolítása:
    ```console
    # grub2-editenv - unset kernelopts
    ```

    1. Szerkesszen `/etc/default/grub` egy szövegszerkesztőben, és adja hozzá a következő paraméterek adatfolyamának:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
    ```
   
   Ez azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, és engedélyezze az interakciót a soros konzollal, amely segítséget nyújt az Azure támogatásához hibakeresési problémákkal. Ez a konfiguráció a hálózati adapterek új elnevezési konvencióit is kikapcsolja.
   
   1. Javasoljuk továbbá, hogy távolítsa el a következő paramétereket:

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. Ha szükséges, hagyja a `crashkernel` beállítást. Vegye figyelembe, hogy ez a paraméter a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel csökkenti, ami problémát okozhat a kisebb virtuálisgép-méreteknél.

1. Miután végzett a szerkesztéssel `/etc/default/grub` , futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    És UEFI-kompatibilis virtuális gépek esetén futtassa a következő parancsot:

    ```console
    # sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik, ami általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sor befoglalásával:

    ```config
    ClientAliveInterval 180
    ```

1. A következő parancs futtatásával telepítse az Azure Linux-ügynököt, a Cloud-init és az egyéb szükséges segédprogramokat:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. A Cloud-init konfigurálása a kiépítés kezelésére:

    1. A waagent konfigurálása a Cloud-init számára:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Ha egy adott virtuális gépet telepít át, és nem kíván általánosított rendszerképet létrehozni, állítsa be a `Provisioning.Agent=disabled` `/etc/waagent.conf` konfigurációt.
    
    1. Csatlakoztatások konfigurálása:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Az Azure-adatforrás konfigurálása:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF
    ```

    1. Ha be van állítva, távolítsa el a meglévő swapfile:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Cloud-init naplózás konfigurálása:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. A swap konfiguráció nem hoz létre lapozófájlt az operációs rendszer lemezén.

    Korábban az Azure Linux-ügynököt a virtuális géphez a virtuális gép üzembe helyezése után az Azure-ban való kiépítés után a rendszer automatikusan konfigurálja a swap-helyet. Ezt azonban a Cloud-init kezeli, **nem szabad** a Linux-ügynököt az erőforrás lemezének formázására használni, megfelelően módosítani a következő paramétereket `/etc/waagent.conf` :

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
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
1. Ha törölni szeretné az előfizetés regisztrációját, futtassa a következő parancsot:

    ```console
    # sudo subscription-manager unregister
    ```

1. Megszüntetése

    Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    > [!CAUTION]
    > Ha egy adott virtuális gépet telepít át, és nem kíván általánosított rendszerképet létrehozni, ugorja át a megszüntetési lépést. A parancs futtatásakor `waagent -force -deprovision` a forrásszámítógép használhatatlanná válik, ez a lépés csak általánosított rendszerkép létrehozására szolgál.


1. Kattintson a **művelet**  >  **leállítása** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.


## <a name="kvm"></a>KVM

Ebből a szakaszból megtudhatja, hogyan készíthet [RHEL 6](#rhel-6-using-kvm) vagy [RHEL 7](#rhel-7-using-kvm) disztribúciót az Azure-ba való feltöltéshez a KVM használatával. 

### <a name="rhel-6-using-kvm"></a>RHEL 6 a KVM használatával

1. Töltse le a RHEL 6 KVM-rendszerképét a Red Hat webhelyről.

1. Adja meg a gyökér jelszavát.

    Titkosított jelszó létrehozása, és a parancs kimenetének másolása:

    ```console
    # openssl passwd -1 changeme
    ```

    Adja meg a root jelszót a guestfish:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   A root felhasználó második mezőjének módosítása a következőről: "!!" a titkosított jelszóhoz.

1. Hozzon létre egy virtuális gépet a KVM-ben a qcow2 rendszerképből. Állítsa be a lemez típusát a **qcow2** értékre, és állítsa a virtuális hálózati adapter modelljét a **virtio** értékre. Ezután indítsa el a virtuális gépet, és jelentkezzen be root-ként.

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Helyezze át (vagy távolítsa el) a udev-szabályokat, hogy elkerülje az Ethernet-interfész statikus szabályainak létrehozását. Ezek a szabályok problémákat okozhatnak az Azure-ban vagy a Hyper-V-ben lévő virtuális gépek klónozásakor:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```console
    # chkconfig network on
    ```

1. Regisztrálja a Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez a konfigurációhoz nyisson meg `/boot/grub/menu.lst` egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Ez azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely segítséget nyújt az Azure támogatásához hibakeresési problémák esetén.
    
    Emellett azt javasoljuk, hogy távolítsa el a következő paramétereket:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. Ha szükséges, hagyja a `crashkernel` beállítást. Vegye figyelembe, hogy ez a paraméter a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel csökkenti, ami problémát okozhat a kisebb virtuálisgép-méreteknél.


1. Hyper-V modulok hozzáadása a initramfs-hez:  

    Szerkessze `/etc/dracut.conf` és adja hozzá a következő tartalmat:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs újraépítése:

    ```config-conf
    # dracut -f -v
    ```

1. Cloud-init eltávolítása:

    ```console
    # yum remove cloud-init
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van és konfigurálva van a rendszerindítás indítására:

    ```console
    # chkconfig sshd on
    ```

    Módosítsa a/etc/ssh/sshd_config a következő sorok belefoglalásával:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. A WALinuxAgent-csomagot `WALinuxAgent-<version>` leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

    ```console
    # yum install WALinuxAgent

    # chkconfig waagent on
    ```

1. Az Azure Linux-ügynök a virtuális gép üzembe helyezése után automatikusan konfigurálhatja a swap-helyet a virtuális géphez csatlakoztatott helyi erőforrás-lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy ideiglenes lemez, és a virtuális gép kiépítése után kiüríthető. Miután telepítette az Azure Linux-ügynököt az előző lépésben, megfelelően módosítsa a következő paramétereket a **/etc/waagent.conf** -ben:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. A következő parancs futtatásával szüntesse meg az előfizetés regisztrációját (ha szükséges):

    ```console-conf
    # subscription-manager unregister
    ```

1. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

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

1. Állítsa le a virtuális gépet a KVM-ben.

1. Alakítsa át a qcow2-rendszerképet a VHD formátumba.

    > [!NOTE]
    > Létezik egy ismert hiba a qemu-IMG verzióban >= 2.2.1, amely egy nem megfelelően formázott VHD-t eredményez. A probléma a QEMU 2,6-es verziójában lett kijavítva. Javasoljuk, hogy a qemu-IMG 2.2.0 vagy az alacsonyabb, vagy a 2,6-es vagy újabb verzióra frissítsen. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611 .
    >

    Először alakítsa át a képet nyers formátumba:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw
    ```

    Győződjön meg arról, hogy a nyers rendszerkép mérete 1 MB-ra van igazítva. Ellenkező esetben az 1 MB-ra való igazításhoz kerekítse a méretet:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    A nyers lemez átalakítása rögzített méretű virtuális merevlemezre:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    A (z) és a (z) és a (z) a qemu **2.6 +** verziójában `force_size`

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

        
### <a name="rhel-7-using-kvm"></a>7. RHEL a KVM használatával

1. Töltse le a RHEL 7 KVM-rendszerképét a Red Hat webhelyről. Ez az eljárás a RHEL 7 módszert használja példaként.

1. Adja meg a gyökér jelszavát.

    Titkosított jelszó létrehozása, és a parancs kimenetének másolása:

    ```console
    # openssl passwd -1 changeme
    ```

    Adja meg a root jelszót a guestfish:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   A root felhasználó második mezőjének módosítása a következőről: "!!" a titkosított jelszóhoz.

1. Hozzon létre egy virtuális gépet a KVM-ben a qcow2 rendszerképből. Állítsa be a lemez típusát a **qcow2** értékre, és állítsa a virtuális hálózati adapter modelljét a **virtio** értékre. Ezután indítsa el a virtuális gépet, és jelentkezzen be root-ként.

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```console
    # sudo systemctl enable network
    ```

1. Regisztrálja a Red Hat-előfizetését, hogy engedélyezze a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez a konfigurációhoz nyisson meg `/etc/default/grub` egy szövegszerkesztőben, és szerkessze a (z `GRUB_CMDLINE_LINUX` ) paramétert. Például:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ez a parancs azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely a hibakeresési problémákkal segíti az Azure-támogatást. A parancs a hálózati adapterek új RHEL 7 elnevezési konvencióit is kikapcsolja. Emellett azt javasoljuk, hogy távolítsa el a következő paramétereket:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. Ha szükséges, hagyja a `crashkernel` beállítást. Vegye figyelembe, hogy ez a paraméter a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel csökkenti, ami problémát okozhat a kisebb virtuálisgép-méreteknél.

1. Miután végzett a szerkesztéssel `/etc/default/grub` , futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```console
    # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Hyper-V modulok hozzáadása a initramfs-hez.

    `/etc/dracut.conf`Tartalom szerkesztése és hozzáadása:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs újraépítése:

    ```config-conf
    # dracut -f -v
    ```

1. Cloud-init eltávolítása:

    ```console
    # yum remove cloud-init
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítve van és konfigurálva van a rendszerindítás indítására:

    ```console
    # systemctl enable sshd
    ```

    Módosítsa a/etc/ssh/sshd_config a következő sorok belefoglalásával:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. A WALinuxAgent-csomagot `WALinuxAgent-<version>` leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

    ```config
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

    ```console
    # yum install WALinuxAgent
    ```

    A waagent szolgáltatás engedélyezése:

    ```console
    # systemctl enable waagent.service
    ```

1. A Cloud-init telepítése kövesse a "RHEL 7 virtuális gép előkészítése a Hyper-V kezelőjéből", 12. lépés, "a Cloud-init telepítése a kiépítés kezelésére" című szakasz lépéseit.

1. Konfiguráció cseréje 

    Ne hozzon létre lapozófájlt az operációs rendszer lemezén.
    Kövesse a "RHEL 7 virtuális gép előkészítése a Hyper-V kezelőjéből", a 13. lépés: "a konfiguráció cseréje" című szakasz lépéseit.


1. A következő parancs futtatásával szüntesse meg az előfizetés regisztrációját (ha szükséges):

    ```console
    # subscription-manager unregister
    ```


1. Megszüntetése

    Kövesse a "RHEL 7 virtuális gép előkészítése a Hyper-V kezelőjéből" című szakasz lépéseit, 15. lépés: "megszüntetés"

1. Állítsa le a virtuális gépet a KVM-ben.

1. Alakítsa át a qcow2-rendszerképet a VHD formátumba.

    > [!NOTE]
    > Létezik egy ismert hiba a qemu-IMG verzióban >= 2.2.1, amely egy nem megfelelően formázott VHD-t eredményez. A probléma a QEMU 2,6-es verziójában lett kijavítva. Javasoljuk, hogy a qemu-IMG 2.2.0 vagy az alacsonyabb, vagy a 2,6-es vagy újabb verzióra frissítsen. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611 .
    >

    Először alakítsa át a képet nyers formátumba:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Győződjön meg arról, hogy a nyers rendszerkép mérete 1 MB-ra van igazítva. Ellenkező esetben az 1 MB-ra való igazításhoz kerekítse a méretet:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    A nyers lemez átalakítása rögzített méretű virtuális merevlemezre:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    A (z) és a (z) és a (z) a qemu **2.6 +** verziójában `force_size`

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="vmware"></a>VMware

Ebből a szakaszból megtudhatja, hogyan készítse elő a [RHEL 6](#rhel-6-using-vmware) vagy [RHEL 7](#rhel-6-using-vmware)  disztribúciót a VMware-ről.

### <a name="prerequisites"></a>Előfeltételek
Ez a szakasz azt feltételezi, hogy már telepített egy RHEL virtuális gépet a VMware-ben. A VMware operációs rendszer telepítésével kapcsolatos további információkért lásd: a [VMware vendég operációs rendszer telepítési útmutatója](https://partnerweb.vmware.com/GOSIG/home.html).

* A Linux operációs rendszer telepítésekor azt javasoljuk, hogy az LVM helyett a standard partíciót használja, ami gyakran az alapértelmezett a sok telepítés esetén. Ezzel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális géppel, különösen akkor, ha egy operációsrendszer-lemezt egy másik virtuális géphez csatlakoztatni kell a hibaelhárításhoz. Az LVM vagy RAID adatlemezeken is használható, ha az előnyben részesített.
* Ne állítson be swap partíciót az operációs rendszer lemezén. A Linux-ügynököt beállíthatja úgy, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén. Erről további információt az alábbi lépésekben találhat.
* A virtuális merevlemez létrehozásakor válassza a **virtuális lemez tárolása egyetlen fájlként** lehetőséget.

### <a name="rhel-6-using-vmware"></a>RHEL 6 a VMware használatával
1. A RHEL 6 hálózatkezelő az Azure Linux-ügynököt is zavarhatja. A következő parancs futtatásával távolítsa el a csomagot:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Hozzon létre egy **Network** nevű fájlt a/etc/sysconfig/könyvtárban, amely a következő szöveget tartalmazza:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Helyezze át (vagy távolítsa el) a udev-szabályokat, hogy elkerülje az Ethernet-interfész statikus szabályainak létrehozását. Ezek a szabályok problémákat okozhatnak az Azure-ban vagy a Hyper-V-ben lévő virtuális gépek klónozásakor:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```console
    # sudo chkconfig network on
    ```

1. Regisztrálja a Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. A WALinuxAgent-csomagot `WALinuxAgent-<version>` leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez nyisson meg `/etc/default/grub` egy szövegszerkesztőben, és szerkessze a (z `GRUB_CMDLINE_LINUX` ) paramétert. Például:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
    ```

   Ez azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely segítséget nyújt az Azure támogatásához hibakeresési problémák esetén. Emellett azt javasoljuk, hogy távolítsa el a következő paramétereket:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
   
    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. Ha szükséges, hagyja a `crashkernel` beállítást. Vegye figyelembe, hogy ez a paraméter a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel csökkenti, ami problémát okozhat a kisebb virtuálisgép-méreteknél.

1. Hyper-V modulok hozzáadása a initramfs-hez:

    Szerkessze `/etc/dracut.conf` és adja hozzá a következő tartalmat:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs újraépítése:

    ```config-cong
    # dracut -f -v
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik, ami általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sor befoglalásával:

    ```config
    ClientAliveInterval 180
    ```

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

1. Ne hozzon létre lapozófájlt az operációs rendszer lemezén.

    Az Azure Linux-ügynök a virtuális gép üzembe helyezése után automatikusan konfigurálhatja a swap-helyet a virtuális géphez csatlakoztatott helyi erőforrás-lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy ideiglenes lemez, és a virtuális gép kiépítése után kiüríthető. Miután telepítette az Azure Linux-ügynököt az előző lépésben, megfelelően módosítsa a következő paramétereket `/etc/waagent.conf` :

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. A következő parancs futtatásával szüntesse meg az előfizetés regisztrációját (ha szükséges):

    ```console
    # sudo subscription-manager unregister
    ```

1. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

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

1. Állítsa le a virtuális gépet, és alakítsa át a VMDK-fájlt egy. vhd fájlba.

    > [!NOTE]
    > Létezik egy ismert hiba a qemu-IMG verzióban >= 2.2.1, amely egy nem megfelelően formázott VHD-t eredményez. A probléma a QEMU 2,6-es verziójában lett kijavítva. Javasoljuk, hogy a qemu-IMG 2.2.0 vagy az alacsonyabb, vagy a 2,6-es vagy újabb verzióra frissítsen. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611 .
    >

    Először alakítsa át a képet nyers formátumba:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw
    ```

    Győződjön meg arról, hogy a nyers rendszerkép mérete 1 MB-ra van igazítva. Ellenkező esetben az 1 MB-ra való igazításhoz kerekítse a méretet:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    A nyers lemez átalakítása rögzített méretű virtuális merevlemezre:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    A (z) és a (z) és a (z) a qemu **2.6 +** verziójában `force_size`

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

### <a name="rhel-7-using-vmware"></a>RHEL 7 a VMware használatával
1. Hozza létre vagy szerkessze a `/etc/sysconfig/network` fájlt, és adja hozzá a következő szöveget:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Hozza létre vagy szerkessze a `/etc/sysconfig/network-scripts/ifcfg-eth0` fájlt, és adja hozzá a következő szöveget:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. A következő parancs futtatásával győződjön meg arról, hogy a hálózati szolgáltatás indításkor elindul:

    ```console
    # sudo systemctl enable network
    ```

1. Regisztrálja a Red Hat-előfizetését, hogy lehetővé tegye a csomagok telepítését a RHEL adattárból a következő parancs futtatásával:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. A módosítás elvégzéséhez nyisson meg `/etc/default/grub` egy szövegszerkesztőben, és szerkessze a (z `GRUB_CMDLINE_LINUX` ) paramétert. Például:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ez a konfiguráció azt is biztosítja, hogy az összes konzolos üzenet el legyen küldve az első soros portra, amely a hibakeresési problémákkal segíti az Azure-támogatást. Emellett kikapcsolja a hálózati adapterek új RHEL 7 elnevezési konvencióit is. Emellett azt javasoljuk, hogy távolítsa el a következő paramétereket:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    A grafikus és a csendes rendszerindítás nem hasznos olyan felhőalapú környezetben, ahol az összes naplót el szeretné juttatni a soros portra. Ha szükséges, hagyja a `crashkernel` beállítást. Vegye figyelembe, hogy ez a paraméter a virtuális gép rendelkezésre álló memóriájának mennyiségét 128 MB vagy több értékkel csökkenti, ami problémát okozhat a kisebb virtuálisgép-méreteknél.

1. Miután végzett a szerkesztéssel `/etc/default/grub` , futtassa a következő parancsot a grub-konfiguráció újraépítéséhez:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Hyper-V modulok hozzáadása a initramfs-hez.

    Szerkesztés `/etc/dracut.conf` , tartalom hozzáadása:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs újraépítése:

    ```console
    # dracut -f -v
    ```

1. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik. Ez a beállítás általában az alapértelmezett. Módosítsa `/etc/ssh/sshd_config` a következő sor befoglalásával:

    ```config
    ClientAliveInterval 180
    ```

1. A WALinuxAgent-csomagot `WALinuxAgent-<version>` leküldte a Red Hat extrák tárházba. Engedélyezze az extrák tárházát a következő parancs futtatásával:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Telepítse az Azure Linux-ügynököt a következő parancs futtatásával:

    ```console
    # sudo yum install WALinuxAgent

    # sudo systemctl enable waagent.service
    ```

1. A Cloud-init telepítése

    Kövesse a "RHEL 7 virtuális gép előkészítése a Hyper-V kezelőjéből", 12. lépés, "a Cloud-init telepítése a kiépítés kezelésére" című szakasz lépéseit.

1. Konfiguráció cseréje

    Ne hozzon létre lapozófájlt az operációs rendszer lemezén.
    Kövesse a "RHEL 7 virtuális gép előkészítése a Hyper-V kezelőjéből", a 13. lépés: "a konfiguráció cseréje" című szakasz lépéseit.

1. Ha törölni szeretné az előfizetés regisztrációját, futtassa a következő parancsot:

    ```console
    # sudo subscription-manager unregister
    ```

1. Megszüntetése

    Kövesse a "RHEL 7 virtuális gép előkészítése a Hyper-V kezelőjéből" című szakasz lépéseit, 15. lépés: "megszüntetés"


1. Állítsa le a virtuális gépet, és alakítsa át a VMDK-fájlt a VHD formátumba.

    > [!NOTE]
    > Létezik egy ismert hiba a qemu-IMG verzióban >= 2.2.1, amely egy nem megfelelően formázott VHD-t eredményez. A probléma a QEMU 2,6-es verziójában lett kijavítva. Javasoljuk, hogy a qemu-IMG 2.2.0 vagy az alacsonyabb, vagy a 2,6-es vagy újabb verzióra frissítsen. Hivatkozás: https://bugs.launchpad.net/qemu/+bug/1490611 .
    >

    Először alakítsa át a képet nyers formátumba:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw
    ```

    Győződjön meg arról, hogy a nyers rendszerkép mérete 1 MB-ra van igazítva. Ellenkező esetben az 1 MB-ra való igazításhoz kerekítse a méretet:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    A nyers lemez átalakítása rögzített méretű virtuális merevlemezre:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    A (z) és a (z) és a (z) a qemu **2.6 +** verziójában `force_size`

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="kickstart-file"></a>Kickstart-fájl

Ebből a szakaszból megtudhatja, hogyan készítse elő a RHEL 7 disztribúciót egy ISO-fájlból egy Kickstart-fájllal.

### <a name="rhel-7-from-a-kickstart-file"></a>RHEL 7 egy Kickstart-fájlból

1.  Hozzon létre egy olyan Kickstart-fájlt, amely tartalmazza a következő tartalmat, és mentse a fájlt. A Kickstart telepítésével kapcsolatos részletekért tekintse meg a [Kickstart telepítési útmutatóját](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```text
    # Kickstart for provisioning a RHEL 7 Azure VM

    # System authorization information
      auth --enableshadow --passalgo=sha512

    # Use graphical install
    text

    # Do not run the Setup Agent on first boot
    firstboot --disable

    # Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    # System language
    lang en_US.UTF-8

    # Network information
    network  --bootproto=dhcp

    # Root password
    rootpw --plaintext "to_be_disabled"

    # System services
    services --enabled="sshd,waagent,NetworkManager"

    # System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    # Partition clearing information
    clearpart --all --initlabel

    # Clear the MBR
    zerombr

    # Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    # System bootloader configuration
    bootloader --location=mbr

    # Firewall configuration
    firewall --disabled

    # Enable SELinux
    selinux --enforcing

    # Don't configure X
    skipx

    # Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    # Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    # Install latest repo update
    yum update -y

    # Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    # Install WALinuxAgent
    yum install -y WALinuxAgent

    # Unregister Red Hat subscription
    subscription-manager unregister

    # Enable waaagent at boot-up
    systemctl enable waagent

    # Install cloud-init
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    # Disable the root account
    usermod root -p '!!'

    # Disabke swap in WALinuxAgent
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n

    # Configure swap using cloud-init
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

    # Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    # Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    # Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    # Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    EOF

    # Deprovision and prepare for Azure if you are creating a generalized image
    sudo cloud-init clean --logs --seed
    sudo rm -rf /var/lib/cloud/
    sudo rm -rf /var/lib/waagent/
    sudo rm -f /var/log/waagent.log

    sudo waagent -force -deprovision+user
    rm -f ~/.bash_history
    export HISTSIZE=0

    %end
    ```

1. Helyezze el azt a Kickstart-fájlt, ahová a telepítési rendszer hozzá tud férni.

1. A Hyper-V kezelőjében hozzon létre egy új virtuális gépet. A **virtuális merevlemez csatlakoztatása** lapon válassza a **virtuális merevlemez csatolása később** lehetőséget, és fejezze be az új virtuális gép varázslót.

1. Nyissa meg a virtuális gép beállításait:

    a.  Csatoljon egy új virtuális merevlemezt a virtuális géphez. Győződjön meg arról, hogy a **VHD-formátum** és a **rögzített méret** van kiválasztva.

    b.  Csatlakoztassa a telepítési ISO-t a DVD-meghajtóhoz.

    c.  Állítsa be a BIOS-t a CD-ről történő rendszerindításhoz.

1. Indítsa el a virtuális gépet. Amikor megjelenik a telepítési útmutató, nyomja le a **Tab** billentyűt a rendszerindítási beállítások konfigurálásához.

1. Adja meg a `inst.ks=<the location of the kickstart file>` rendszerindítási beállítások végét, majd nyomja le az **ENTER** billentyűt.

1. Várjon, amíg a telepítés befejeződik. Ha elkészült, a rendszer automatikusan leállítja a virtuális gépet. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="known-issues"></a>Ismert problémák
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>A Hyper-V illesztőprogram nem szerepelhet a kezdeti RAM-lemezen, ha nem Hyper-V Hypervisort használ

Bizonyos esetekben előfordulhat, hogy a Linux-telepítők nem tartalmazzák a Hyper-V illesztőprogramjait a kezdeti RAM-lemezen (initrd vagy initramfs), kivéve, ha a Linux azt észleli, hogy egy Hyper-V környezetben fut.

Ha más virtualizációs rendszert (azaz VirtualBox, Xen stb.) használ a Linux-lemezkép előkészítéséhez, előfordulhat, hogy újra kell építenie a initrd, hogy legalább a hv_vmbus és hv_storvsc kernel-modulok elérhetők legyenek a kezdeti RAM-lemezen. Ez egy ismert probléma, amely legalább a felsőbb rétegbeli Red Hat-disztribúción alapuló rendszereken jelent meg.

A probléma megoldásához vegyen fel Hyper-V modulokat a initramfs és az újraépítéshez:

Szerkessze `/etc/dracut.conf` és adja hozzá a következő tartalmat:

```config-conf
add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
```

Initramfs újraépítése:

```console
# dracut -f -v
```

További részletekért tekintse meg a [initramfs újjáépítésével](https://access.redhat.com/solutions/1958)kapcsolatos információkat.

## <a name="next-steps"></a>Következő lépések
* Most már készen áll a Red Hat Enterprise Linux virtuális merevlemez használatára az Azure-beli új virtuális gépek létrehozásához. Ha első alkalommal tölti fel a. vhd-fájlt az Azure-ba, tekintse meg a Linux rendszerű [virtuális gép létrehozása egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd)című témakört.
* A Red Hat Enterprise Linux futtatására jogosult hypervisorokkal kapcsolatos további részletekért tekintse meg [a Red Hat webhelyét](https://access.redhat.com/certified-hypervisors).
* Ha többet szeretne megtudni a RHEL BYOS-lemezképek használatáról, látogasson el a [BYOS](../workloads/redhat/byos.md)dokumentációs oldalára.
