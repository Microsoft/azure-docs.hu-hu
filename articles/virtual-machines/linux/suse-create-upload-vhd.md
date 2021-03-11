---
title: SUSE Linux rendszerű virtuális merevlemez létrehozása és feltöltése az Azure-ban
description: Megtudhatja, hogyan hozhat létre és tölthet fel egy SUSE Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 276f5f4542ecea42c665764b8c4e5f66f2531126
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552711"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>SLES- vagy openSUSE-alapú virtuális gép előkészítése az Azure-beli használatra


Ez a cikk azt feltételezi, hogy már telepített egy SUSE vagy openSUSE Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik a. vhd fájlok létrehozásához, például egy virtualizációs megoldáshoz, például a Hyper-V-hez. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és a virtuális gép konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="sles--opensuse-installation-notes"></a>SLES/openSUSE telepítési megjegyzések
* A Linux for Azure előkészítésével kapcsolatos további tippeket a [Linux általános telepítési megjegyzései](create-upload-generic.md#general-linux-installation-notes) című témakörben talál.
* A VHDX formátuma nem támogatott az Azure-ban, csak a **rögzített VHD**.  A lemezt VHD formátumba konvertálhatja a Hyper-V kezelőjével vagy a convert-VHD parancsmag használatával.
* A Linux rendszer telepítésekor azt javasoljuk, hogy az LVM helyett standard partíciót használjon (ez általában számos telepítés esetében). Ezzel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemezt egy másik virtuális géphez kell csatolni a hibaelhárításhoz. Az [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) vagy a [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) adatlemezeken is használható, ha az előnyben részesített.
* Ne állítson be swap-partíciót az operációsrendszer-lemezen. A Linux-ügynök úgy konfigurálható, hogy lapozófájlt hozzon létre az ideiglenes erőforrás lemezén.  Erről további információt az alábbi lépésekben találhat.
* Az Azure-ban az összes virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor gondoskodnia kell arról, hogy a nyers lemez mérete a konverzió előtt egy 1MB többszöröse legyen. További információért lásd a [Linux telepítési megjegyzéseit](create-upload-generic.md#general-linux-installation-notes) .

## <a name="use-suse-studio"></a>A SUSE Studio használata
A [SUSE Studio](https://studioexpress.opensuse.org/) egyszerűen létrehozhatja és kezelheti az Azure-hoz és a Hyper-V-hez készült SLES és openSUSE-lemezképeket. Ez az ajánlott módszer a saját SLES és az openSUSE-lemezképek testre szabására.

A saját virtuális merevlemez kiépítésének alternatívájaként a SUSE a BYOS (saját előfizetés) lemezképeket is közzéteszi a SLES a [VM depotban](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>SUSE Linux Enterprise Server előkészítése az Azure-hoz
1. A Hyper-V kezelőjének középső ablaktábláján válassza ki a virtuális gépet.
2. Kattintson a **Kapcsolódás** gombra a virtuális gép ablakának megnyitásához.
3. Regisztrálja a SUSE Linux Enterprise rendszerét, hogy lehetővé tegye a frissítések letöltését és a csomagok telepítését.
4. A System frissítése a legújabb javításokkal:

    ```console
    # sudo zypper update
    ```
    
5. Az Azure Linux Agent és a Cloud-init telepítése

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. A waagent & Cloud-init engedélyezése a rendszerindítás elindításához

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. A waagent és a Cloud-init konfiguráció frissítése

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. Szerkessze a/etc/default/grub fájlt, hogy a konzol naplófájljai a soros portra legyenek küldve, majd frissítse a fő konfigurációs fájlt a GRUB2-mkconfig-o/boot/GRUB2/grub.cfg

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    Ezzel biztosítható, hogy az összes konzol üzenetei az első soros porton legyenek elküldve, amely a hibakeresési problémákkal segíti az Azure-támogatást.
    
9. Győződjön meg arról, hogy az/etc/fstab fájl hivatkozik a lemezre az UUID használatával (by-UUID)
         
10. Módosítsa a udev-szabályokat úgy, hogy ne generáljon statikus szabályokat az Ethernet-adapter (ek) számára. Ezek a szabályok problémákat okozhatnak a Microsoft Azure vagy Hyper-V-ben lévő virtuális gépek klónozásakor:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. Javasoljuk, hogy szerkessze a "/etc/sysconfig/network/DHCP" fájlt, és módosítsa a `DHCLIENT_SET_HOSTNAME` paramétert a következőre:

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. A "/etc/sudoers" elemnél írja ki vagy távolítsa el a következő sorokat, ha vannak ilyenek:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik. Ez általában az alapértelmezett.

14. Konfiguráció cseréje
 
    Ne hozzon létre lapozófájlt az operációs rendszer lemezén.

    Korábban az Azure Linux-ügynököt a virtuális géphez a virtuális gép üzembe helyezése után az Azure-ban való kiépítés után a rendszer automatikusan konfigurálja a swap-helyet. Ezt azonban a Cloud-init kezeli, **nem szabad** a Linux-ügynököt az erőforrás lemezének formázására használni, megfelelően módosítani a következő paramétereket `/etc/waagent.conf` :

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Ha a csatlakoztatást, a formázást és a cserét szeretné létrehozni, az alábbiakat teheti:
    * Minden egyes virtuális gép létrehozásakor adja át ezt a felhő-init konfigurációként.
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

15. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    ```console
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

---
## <a name="prepare-opensuse-131"></a>Az openSUSE 13.1 + előkészítése
1. A Hyper-V kezelőjének középső ablaktábláján válassza ki a virtuális gépet.
2. Kattintson a **Kapcsolódás** gombra a virtuális gép ablakának megnyitásához.
3. A rendszerhéjon futtassa a következő parancsot: " `zypper lr` ". Ha a parancs a következőhöz hasonló kimenetet ad vissza, akkor a Tárházak a várt módon lesznek konfigurálva – nincs szükség módosításra (vegye figyelembe, hogy a verziószámok száma változhat):

   | # | Alias                 | Name                  | Engedélyezve | Frissítés
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Felhő: Tools_13.1      | Felhő: Tools_13.1      | Igen     | Yes
   | 2 | openSUSE_13 openSUSE_13.1_OSS     | openSUSE_13 openSUSE_13.1_OSS     | Igen     | Yes
   | 3 | openSUSE_13 openSUSE_13.1_Updates | openSUSE_13 openSUSE_13.1_Updates | Igen     | Yes

    Ha a parancs visszaadja a "nincsenek adattárak definiálva..." értéket. Ezután az alábbi parancsokkal adhatja hozzá ezeket a repókat:

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
    # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
    ```

    Ezután a (z) parancs futtatásával ellenőrizheti, hogy a Tárházak hozzá lettek-e adva `zypper lr` . Ha az egyik releváns frissítési tárház nincs engedélyezve, engedélyezze a következő paranccsal:

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. A rendszermag frissítése a legújabb elérhető verzióra:

    ```console
    # sudo zypper up kernel-default
    ```

    Vagy frissítse a rendszeren a legújabb javításokat:

    ```console
    # sudo zypper update
    ```

5. Telepítse az Azure Linux-ügynököt.

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Módosítsa a rendszermag rendszerindítási sorát a grub-konfigurációban, hogy további kernel-paramétereket is tartalmazzon az Azure-hoz. Ehhez nyissa meg a "/boot/grub/menu.lst" kifejezést egy szövegszerkesztőben, és győződjön meg arról, hogy az alapértelmezett kernel a következő paramétereket tartalmazza:

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Ezzel biztosítható, hogy az összes konzol üzenetei az első soros porton legyenek elküldve, amely a hibakeresési problémákkal segíti az Azure-támogatást. Továbbá távolítsa el a következő paramétereket a kernel rendszerindítási sorából, ha vannak ilyenek:

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. Javasoljuk, hogy szerkessze a "/etc/sysconfig/network/DHCP" fájlt, és módosítsa a `DHCLIENT_SET_HOSTNAME` paramétert a következőre:

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **Fontos:** A "/etc/sudoers" elemnél írja ki vagy távolítsa el a következő sorokat, ha vannak ilyenek:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik.  Ez általában az alapértelmezett.
10. Ne hozzon létre lapozófájlt az operációsrendszer-lemezen.

    Az Azure Linux-ügynök automatikusan konfigurálhatja a lapozófájlt a virtuális géphez az Azure-ban való üzembe helyezést követően csatlakozó helyi erőforrás lemez használatával. Vegye figyelembe, hogy a helyi erőforrás lemeze egy *ideiglenes* lemez, és a virtuális gép kiépítésekor kiürítésre kerülhet. Az Azure Linux-ügynök telepítése után (lásd az előző lépést) a/etc/waagent.conf megfelelően módosítsa a következő paramétereket:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. Ellenőrizze, hogy az Azure Linux-ügynök fut-e indításkor:

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre. A linuxos virtuális merevlemez most már készen áll az Azure-ba való feltöltésre.

## <a name="next-steps"></a>Következő lépések
Most már készen áll a SUSE Linux rendszerű virtuális merevlemez használatára, hogy új virtuális gépeket hozzon létre az Azure-ban. Ha első alkalommal tölti fel a. vhd-fájlt az Azure-ba, tekintse meg a Linux rendszerű [virtuális gép létrehozása egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd)című témakört.