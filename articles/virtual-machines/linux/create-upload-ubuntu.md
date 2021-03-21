---
title: Ubuntu Linux VHD létrehozása és feltöltése az Azure-ban
description: Megtudhatja, hogyan hozhat létre és tölthet fel egy Ubuntu Linux operációs rendszert tartalmazó Azure-beli virtuális merevlemezt (VHD-t).
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: 92ceecd16a428593764fe5ab6478cc4ea7ab91d7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554615"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Ubuntu rendszerű virtuális gép előkészítése az Azure-beli használatra


Az Ubuntu mostantól közzéteszi a hivatalos Azure VHD-ket a letöltéshez [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) . Ha saját, speciális Ubuntu-lemezképet kell létrehoznia az Azure-hoz, és nem az alábbi manuális eljárást szeretné használni, érdemes elindítani ezeket az ismert munkavhd-ket, és szükség szerint testre szabnia azokat. A legújabb rendszerkép-verziók mindig a következő helyeken találhatók:

* Ubuntu 16.04/Xenia: [Ubuntu-16,04-Server-cloudimg-amd64-Disk1. VMDK](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-amd64. VMDK](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már telepített egy Ubuntu Linux operációs rendszert egy virtuális merevlemezre. Több eszköz létezik a. vhd fájlok létrehozásához, például egy virtualizációs megoldáshoz, például a Hyper-V-hez. Útmutatásért lásd: [a Hyper-V szerepkör telepítése és a virtuális gép konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Ubuntu telepítési megjegyzések**

* A Linux for Azure előkészítésével kapcsolatos további tippeket a [Linux általános telepítési megjegyzései](create-upload-generic.md#general-linux-installation-notes) című témakörben talál.
* A VHDX formátuma nem támogatott az Azure-ban, csak a **rögzített VHD**.  A lemezt a Hyper-V kezelőjével vagy a parancsmaggal konvertálhatja VHD formátumba `Convert-VHD` .
* A Linux rendszer telepítésekor azt javasoljuk, hogy az LVM helyett standard partíciót használjon (ez általában számos telepítés esetében). Ezzel elkerülhető, hogy az LVM neve ütközik a klónozott virtuális gépekkel, különösen akkor, ha egy operációsrendszer-lemezt egy másik virtuális géphez kell csatolni a hibaelhárításhoz. Az [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) vagy a [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) adatlemezeken is használható, ha az előnyben részesített.
* Ne állítson be swap partíciót vagy swapfile az operációsrendszer-lemezen. A Cloud-init kiépítési ügynök úgy konfigurálható, hogy egy lapozófájlt vagy egy swap partíciót hozzon létre az ideiglenes erőforrás lemezén. Erről további információt az alábbi lépésekben találhat.
* Az Azure-ban az összes virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor gondoskodnia kell arról, hogy a nyers lemez mérete a konverzió előtt egy 1MB többszöröse legyen. További információért lásd a [Linux telepítési megjegyzéseit](create-upload-generic.md#general-linux-installation-notes) .

## <a name="manual-steps"></a>Manuális lépések
> [!NOTE]
> Mielőtt saját Ubuntu-lemezképet hozna létre az Azure-hoz, érdemes inkább az előre elkészített és tesztelt lemezképeket használni [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) .
> 
> 

1. A Hyper-V kezelőjének középső ablaktábláján válassza ki a virtuális gépet.

2. Kattintson a **Kapcsolódás** gombra a virtuális gép ablakának megnyitásához.

3. Cserélje le a rendszerkép aktuális tárházait az Ubuntu Azure-tárházának használatára.

    A Szerkesztés előtt `/etc/apt/sources.list` ajánlott biztonsági másolatot készíteni:

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16,04 és Ubuntu 18,04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. Az Ubuntu Azure-lemezképek mostantól az [Azure-ra szabott kernelt](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)használják. Frissítse az operációs rendszert a legújabb Azure-ra szabott kernelre, és telepítse az Azure Linux-eszközöket (beleértve a Hyper-V-függőségeket) az alábbi parancsok futtatásával:

    Ubuntu 16,04 és Ubuntu 18,04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Módosítsa a grub kernel-rendszerindítási sorát, hogy további kernel-paramétereket tartalmazzon az Azure-hoz. Ehhez nyissa meg `/etc/default/grub` egy szövegszerkesztőben, keresse meg a nevű változót `GRUB_CMDLINE_LINUX_DEFAULT` (vagy adja hozzá, ha szükséges), és szerkessze úgy, hogy tartalmazza a következő paramétereket:

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    Mentse és zárda be ezt a fájlt, majd futtassa a parancsot `sudo update-grub` . Ezzel biztosítható, hogy az összes konzol üzenetei az első soros porton legyenek elküldve, amely segítséget nyújt az Azure technikai támogatásához hibakeresési problémák esetén.

6. Győződjön meg arról, hogy az SSH-kiszolgáló telepítése és konfigurálása a rendszerindítás indításakor történik.  Ez általában az alapértelmezett.

7. Telepítse a Cloud-init (a kiépítési ügynököt) és az Azure Linux-ügynököt (a vendég bővítmények kezelőjét). A Cloud-init a netplan használatával konfigurálja a rendszerhálózati konfigurációt a kiépítés és az egyes későbbi rendszerindítások során.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  `walinuxagent`Előfordulhat, hogy a csomag eltávolítja a `NetworkManager` és a `NetworkManager-gnome` csomagokat, ha azok telepítve vannak.

8. Távolítsa el a Cloud-init alapértelmezett konfigurációit és a maradék netplan-összetevőket, amelyek ütközhetnek a Cloud-init kiépítés az Azure-ban:

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. A Cloud-init konfigurálásával a rendszer kiépíthető az Azure-adatforrással:

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Konfigurálja az Azure Linux-ügynököt úgy, hogy a kiépítés elvégzéséhez a Cloud-init-t használja. A beállításokkal kapcsolatos további információkért tekintse meg a [WALinuxAgent projektet](https://github.com/Azure/WALinuxAgent) .

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. A Cloud-init és az Azure Linux Agent futásidejű összetevőinek és naplóinak tisztítása:

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. Futtassa a következő parancsokat a virtuális gép megszüntetéséhez, és készítse elő az Azure-beli üzembe helyezéshez:

    > [!NOTE]
    > A `sudo waagent -force -deprovision+user` parancs megkísérli a rendszer tisztítását, és lehetővé teszi az újbóli kiépítés megfelelővé tételét. A `+user` kapcsoló törli az utolsó kiosztott felhasználói fiókot és a hozzájuk tartozó összes adatmennyiséget.

    > [!WARNING]
    > A fenti parancs használatával történő megszüntetés nem garantálja, hogy a rendszerkép törlődik az összes bizalmas adatról, és alkalmas az újraterjesztésre.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Kattintson a **művelet – > leállítás** a Hyper-V kezelőjében elemre.

14. Az Azure csak rögzített méretű VHD-ket fogad el. Ha a virtuális gép operációsrendszer-lemeze nem rögzített méretű VHD, használja a `Convert-VHD` PowerShell-parancsmagot, és válassza a `-VHDType Fixed` lehetőséget. Tekintse meg az `Convert-VHD` alábbi dokumentumokat: [Convert-VHD](/powershell/module/hyper-v/convert-vhd).


## <a name="next-steps"></a>Következő lépések
Most már készen áll a Ubuntu Linux virtuális merevlemez használatára az Azure-beli új virtuális gépek létrehozásához. Ha első alkalommal tölti fel a. vhd-fájlt az Azure-ba, tekintse meg a Linux rendszerű [virtuális gép létrehozása egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd)című témakört.