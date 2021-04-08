---
title: Fő célkiszolgáló telepítése a Linux rendszerű virtuális gép feladat-visszavételéhez Azure Site Recovery
description: Megtudhatja, hogyan állíthat be egy linuxos fő célkiszolgáló számára a feladat-visszavételt egy helyszíni helyre, a VMware virtuális gépeknek az Azure-ba való vész-helyreállításával Azure Site Recovery használatával.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: mayg
ms.openlocfilehash: 9e1008f7acbfe0685b7a171176c7dc54592d1491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019242"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Linux fő célkiszolgáló telepítése feladat-visszavételhez
A virtuális gépek Azure-ba történő feladatátvétele után a virtuális gépeket a helyszíni helyre is visszaállíthatja. A feladat-visszavétel érdekében újra kell telepítenie a virtuális gépet az Azure-ból a helyszíni helyre. Ehhez a folyamathoz egy helyszíni fő célkiszolgáló szükséges a forgalom fogadásához. 

Ha a védett virtuális gép egy Windows rendszerű virtuális gép, akkor szükség van egy Windows fő tárolóra. Linux rendszerű virtuális gépek esetén Linux fő célpontra van szükség. A következő lépésekből megtudhatja, hogyan hozhat létre és telepíthet linuxos fő célt.

> [!IMPORTANT]
> A 9.10.0 fő célkiszolgáló kiadásával kezdődően a legújabb fő célkiszolgáló csak Ubuntu 16,04-kiszolgálóra telepíthető. Az új telepítések nem engedélyezettek CentOS 6.6-kiszolgálókon. A régi fő célkiszolgáló azonban továbbra is frissíthető a 9.10.0 verziójának használatával.
> Az LVM fő célkiszolgáló nem támogatott.

## <a name="overview"></a>Áttekintés
Ez a cikk útmutatást nyújt a Linux fő célhelyének telepítéséhez.

A jelen cikk végén vagy a [Microsoft Q&az Azure Recovery Services-ra vonatkozó kérdés oldalán](/answers/topics/azure-site-recovery.html)megjegyzéseket vagy kérdéseket tehet közzé.

## <a name="prerequisites"></a>Előfeltételek

* Annak a gazdagépnek a kiválasztásához, amelyre a fő célt telepíteni szeretné, állapítsa meg, hogy a feladat-visszavételi feladat egy meglévő helyszíni virtuális gép vagy egy új virtuális gép számára lesz-e. 
    * Egy meglévő virtuális gép esetében a fő célként megadott gazdagépnek hozzáféréssel kell rendelkeznie a virtuális gép adattárához.
    * Ha a helyszíni virtuális gép nem létezik (másodlagos hely helyreállítása esetén), a feladat-visszavételi virtuális gép ugyanazon a gazdagépen jön létre, mint a fő célként. Bármelyik ESXi-gazdagépet kiválaszthatja a fő cél telepítéséhez.
* A fő célként olyan hálózaton kell lennie, amely képes kommunikálni a Process Serverrel és a konfigurációs kiszolgálóval.
* A fő célként megadott verziónak egyenlőnek vagy annál korábbinak kell lennie a Process Server és a konfigurációs kiszolgáló verziójánál. Ha például a konfigurációs kiszolgáló verziója 9,4, akkor a fő cél verziója 9,4 vagy 9,3, de nem 9,5.
* A fő cél csak VMware virtuális gép lehet, és nem fizikai kiszolgáló.

> [!NOTE]
> Ügyeljen rá, hogy ne kapcsolja be a vMotion a felügyeleti összetevőkön, például a fő célhelyen. Ha a fő cél sikeres ismételt védelem után mozog, a virtuális gép lemezei (VMDK) nem választhatók le. Ebben az esetben a feladat-visszavétel sikertelen lesz.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>A fő célkiszolgáló létrehozásához szükséges Méretezési irányelvek

Hozza létre a fő célt a következő méretezési irányelveknek megfelelően:
- **RAM**: 6 GB vagy több
- **Operációsrendszer-lemez mérete**: 100 GB vagy több (operációs rendszer telepítéséhez)
- **További lemez mérete az adatmegőrzési meghajtó számára**: 1 TB
- **CPU-magok**: 4 mag vagy több
- **Kernel**: 4,16. *

## <a name="deploy-the-master-target-server"></a>A fő célkiszolgáló üzembe helyezése

### <a name="install-ubuntu-16042-minimal"></a>Az Ubuntu 16.04.2 minimális telepítése

Hajtsa végre az alábbi lépéseket az Ubuntu 16.04.2 64 bites operációs rendszer telepítéséhez.

1.   Nyissa meg a [letöltési hivatkozást](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), válassza ki a legközelebbi tükrözést, és töltse le az Ubuntu 16.04.2 minimális 64 bites ISO-t.
Tartsa meg az Ubuntu 16.04.2 minimális 64 bites ISO-t a DVD-meghajtón, és indítsa el a rendszerét.

1.  Válassza az **angol** nyelvet előnyben részesített nyelvként, majd válassza az **ENTER billentyűt**.
    
    ![Válasszon nyelvet](./media/vmware-azure-install-linux-master-target/image1.png)
1. Válassza az **Ubuntu Server telepítése** lehetőséget, majd válassza az **ENTER billentyűt**.

    ![Válassza az Ubuntu Server telepítése lehetőséget.](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Válassza az **angol** nyelvet előnyben részesített nyelvként, majd válassza az **ENTER billentyűt**.

    ![Válassza ki az angolt az előnyben részesített nyelvként](./media/vmware-azure-install-linux-master-target/image3.png)

1. Válassza ki a megfelelő lehetőséget az **időzóna** beállításai listából, majd válassza az **ENTER billentyűt**.

    ![Válassza ki a megfelelő időzónát](./media/vmware-azure-install-linux-master-target/image4.png)

1. Válassza a **nem** (az alapértelmezett beállítás) lehetőséget, majd válassza az **ENTER billentyűt**.

     ![A billentyűzet konfigurálása](./media/vmware-azure-install-linux-master-target/image5.png)
1. Válassza az **angol (amerikai)** lehetőséget a billentyűzet származási országa/régiója számára, majd válassza az **ENTER** gombot.

1. Válassza az **angol (US)** billentyűzetkiosztást, majd az **ENTER billentyűt**.

1. Adja meg a kiszolgáló állomásnevét a hostname ( **állomásnév** ) mezőben, majd kattintson a **Continue (folytatás**) gombra.

1. Felhasználói fiók létrehozásához adja meg a felhasználónevet, majd kattintson a **Continue (folytatás**) gombra.

      ![Felhasználói fiók létrehozása](./media/vmware-azure-install-linux-master-target/image9.png)

1. Adja meg az új felhasználói fiók jelszavát, majd kattintson a **Continue (folytatás**) gombra.

1.  Erősítse meg az új felhasználó jelszavát, majd kattintson a **Continue (folytatás**) gombra.

    ![A jelszavak megerősítése](./media/vmware-azure-install-linux-master-target/image11.png)

1.  A kezdőkönyvtár titkosításának következő kiválasztásakor válassza a **nem** (az alapértelmezett beállítás) lehetőséget, majd válassza az **ENTER billentyűt**.

1. Ha a megjelenő időzóna helyes, válassza az **Igen** (az alapértelmezett beállítás) lehetőséget, majd válassza az **ENTER billentyűt**. Az időzóna újrakonfigurálásához válassza a **nem** lehetőséget.

1. A particionálási módszer beállításainál válassza az **irányított – teljes lemez használata** lehetőséget, majd válassza az **ENTER billentyűt**.

     ![Válassza ki a particionálási módszer beállítást.](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Válassza ki a megfelelő lemezt a **lemez kiválasztása a particionáláshoz** lehetőségre, majd válassza az **ENTER billentyűt**.

    ![Válassza ki a lemezt](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Válassza az **Igen** lehetőséget a lemez módosításainak írásához, majd válassza az **ENTER billentyűt**.

    ![Válassza az alapértelmezett lehetőséget](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  A proxy kiválasztása beállításnál válassza az alapértelmezett beállítást, válassza a **Folytatás** lehetőséget, majd kattintson az **ENTER** gombra.
     
     ![Képernyőkép, amely megjeleníti a Folytatás lehetőséget, majd válassza az ENTER billentyűt.](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Válassza a **nincs automatikus frissítés** lehetőséget a rendszeren a frissítések kezeléséhez, majd válassza az **ENTER billentyűt**.

     ![Válassza ki a frissítések kezelésének módját](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Mivel a Azure Site Recovery fő célkiszolgáló az Ubuntu nagyon konkrét verzióját igényli, gondoskodnia kell arról, hogy a kernel frissítései le legyenek tiltva a virtuális gépen. Ha engedélyezve vannak, akkor minden rendszeres frissítés a fő célkiszolgáló meghibásodását okozza. Győződjön meg arról, hogy a **nincs automatikus frissítés** lehetőséget választotta.

1.  Válassza az alapértelmezett beállítások lehetőséget. Ha az openSSH-t SSH-kapcsolathoz szeretné használni, válassza az **OpenSSH-kiszolgáló** lehetőséget, majd kattintson a **Continue (folytatás**) gombra.

    ![Szoftver kiválasztása](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. A GRUB boot loader telepítéséhez válassza az **Igen**, majd az **ENTER** gombot.
     
    ![GRUB rendszerindítási telepítő](./media/vmware-azure-install-linux-master-target/image20.png)


1. Válassza ki a megfelelő eszközt a rendszerindító betöltő telepítéséhez (lehetőleg **/dev/sda**), majd válassza az **ENTER billentyűt**.
     
    ![Válassza ki a megfelelő eszközt](./media/vmware-azure-install-linux-master-target/image21.png)

1. Válassza a **Folytatás** lehetőséget, majd kattintson az **ENTER** gombra a telepítés befejezéséhez.

    ![A telepítés befejezése](./media/vmware-azure-install-linux-master-target/image22.png)

1. A telepítés befejezése után jelentkezzen be a virtuális gépre az új felhasználói hitelesítő adatokkal. (További információért tekintse meg a **10. lépést** .)

1. A ROOT felhasználói jelszó beállításához kövesse az alábbi képernyőképen leírt lépéseket. Ezután jelentkezzen be ROOT felhasználóként.

    ![A gyökér felhasználói jelszavának beállítása](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>A gép konfigurálása fő célkiszolgálóként

Az egyes SCSI-merevlemezek AZONOSÍTÓjának lekéréséhez egy Linux rendszerű virtuális gépen, a **lemezen. A EnableUUID = TRUE** paramétert engedélyezni kell. A paraméter engedélyezéséhez hajtsa végre a következő lépéseket:

1. Állítsa le a virtuális gépet.

2. Kattintson a jobb gombbal a virtuális gép bejegyzésére a bal oldali ablaktáblán, majd válassza a **beállítások szerkesztése** menüpontot.

3. Válassza a **Beállítások** lapot.

4. A bal oldali ablaktáblán válassza a **speciális**  >  **általános** lehetőséget, majd a képernyő jobb alsó részén kattintson a **konfigurációs paraméterek** gombra.

    ![Konfigurációs paraméter megnyitása](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    A **konfigurációs paraméterek** beállítás nem érhető el, ha a gép fut. A Lap aktívvá tételéhez állítsa le a virtuális gépet.

5. Ellenőrizze, hogy van-e egy sor **lemezrel. A EnableUUID** már létezik.

   - Ha az érték létezik, és false ( **hamis**) értékre van állítva, módosítsa az értéket **igaz** értékre. (Az értékek nem megkülönböztetik a kis-és nagybetűket.)

   - Ha az érték létezik, és a értéke **true (igaz**), válassza a **Mégse** gombot.

   - Ha az érték nem létezik, válassza a **sor hozzáadása** lehetőséget.

   - A név oszlopban adja hozzá a **lemezt. EnableUUID**, majd állítsa **igaz** értékre az értéket.

     ![Annak ellenőrzése, hogy a lemez. A EnableUUID már létezik](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Kernel-frissítések letiltása

Azure Site Recovery fő célkiszolgáló az Ubuntu adott verzióját igényli, gondoskodjon arról, hogy a kernel frissítései le legyenek tiltva a virtuális gépen. Ha a kernel frissítése engedélyezve van, akkor a fő célkiszolgáló meghibásodását okozhatja.

#### <a name="download-and-install-additional-packages"></a>További csomagok letöltése és telepítése

> [!NOTE]
> Ellenőrizze, hogy van-e internetkapcsolata további csomagok letöltéséhez és telepítéséhez. Ha nem rendelkezik internetkapcsolattal, ezeket a deb-csomagokat manuálisan kell megkeresnie, és telepítenie kell őket.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>A telepítő telepítőjének beszerzése

Ha a fő célként internetkapcsolat is van, a következő lépésekkel töltheti le a telepítőt. Ellenkező esetben a telepítőt a Process Serverről másolhatja, majd telepítheti.

#### <a name="download-the-master-target-installation-packages"></a>A fő cél telepítési csomagjainak letöltése

[Töltse le a legújabb Linux fő cél telepítési biteket](https://aka.ms/latestlinuxmobsvc).

A Linux használatával történő letöltéséhez írja be a következőt:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Győződjön meg arról, hogy letöltötte és kicsomagolja a telepítőt a saját könyvtárában. Ha kibontja a **/usr/local**-t, akkor a telepítés sikertelen lesz.


#### <a name="access-the-installer-from-the-process-server"></a>A telepítő elérése a Process Serverről

1. A Process Serveren lépjen a **C:\Program Files (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Másolja a szükséges telepítőfájlt a Process Server rendszerből, és mentse a **latestlinuxmobsvc. tar. gz** néven a saját könyvtárába.


### <a name="apply-custom-configuration-changes"></a>Egyéni konfigurációs módosítások alkalmazása

Az egyéni konfigurációs módosítások alkalmazásához használja a következő lépéseket ROOT felhasználóként:

1. Futtassa a következő parancsot a bináris fájl untar.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![A futtatandó parancs képernyőképe](./media/vmware-azure-install-linux-master-target/image16.png)

2. A következő parancs futtatásával adja meg az engedélyt.

    `chmod 755 ./ApplyCustomChanges.sh`


3. A szkript futtatásához hajtsa végre a következő parancsot.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Csak egyszer futtassa a parancsfájlt a kiszolgálón. Ezután állítsa le a kiszolgálót. A lemez hozzáadása után indítsa újra a kiszolgálót a következő szakaszban leírtak szerint.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Adatmegőrzési lemez hozzáadása a Linux fő célként szolgáló virtuális géphez

Adatmegőrzési lemez létrehozásához kövesse az alábbi lépéseket:

1. Csatoljon egy új 1 TB-os lemezt a Linux fő célként szolgáló virtuális géphez, majd indítsa el a gépet.

2. Az adatmegőrzési lemez többutas AZONOSÍTÓjának megismeréséhez használja a **többutas-ll** parancsot: **többutas-ll**

    ![Többutas azonosító](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formázza a meghajtót, majd hozzon létre egy fájlrendszert az új meghajtón: **mkfs. ext4 \<Retention disk's multipath id> /dev/Mapper/**.
    
    ![Fájlrendszer](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. A fájlrendszer létrehozása után csatlakoztassa az adatmegőrzési lemezt.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Hozza létre az **fstab** -bejegyzést úgy, hogy a számítógép minden indításakor csatlakoztassa az adatmegőrzési meghajtót.
    
    `vi /etc/fstab`
    
    A fájl szerkesztésének megkezdéséhez kattintson a **Beszúrás** gombra. Hozzon létre egy új sort, majd szúrja be a következő szöveget. Szerkessze a lemez többutas AZONOSÍTÓját az előző parancs kijelölt többutas AZONOSÍTÓjának alapján.

    **/dev/Mapper/ \<Retention disks multipath id> /mnt/Retention ext4 rw 0 0**

    Válassza az **ESC** lehetőséget, majd írja be a következőt **: wq** (írás és Kilépés) a szerkesztő ablak bezárásához.

### <a name="install-the-master-target"></a>A fő cél telepítése

> [!IMPORTANT]
> A fő célkiszolgáló verziójának egyenlőnek vagy annál korábbinak kell lennie a Process Server és a konfigurációs kiszolgáló verziójánál. Ha ez az állapot nem teljesül, az ismételt védelem sikeres lesz, de a replikáció meghiúsul.


> [!NOTE]
> A fő célkiszolgáló telepítése előtt győződjön meg arról, hogy a virtuális gépen található **/etc/hosts** -fájl olyan bejegyzéseket tartalmaz, amelyek leképezik a helyi gazdagépet az összes hálózati adapterhez társított IP-címekre.

1. Futtassa a következő parancsot a fő cél telepítéséhez.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    ```

2. Másolja a jelszót a **C:\ProgramData\Microsoft Azure site Recovery\private\connection.passphrase** a konfigurációs kiszolgálón. Ezután mentse **passphrase.txtként** ugyanabban a helyi könyvtárban a következő parancs futtatásával:

    `echo <passphrase> >passphrase.txt`

    Példa: 

    `echo itUx70I47uxDuUVY >passphrase.txt`
    

3. Jegyezze fel a konfigurációs kiszolgáló IP-címét. A következő parancs futtatásával regisztrálja a kiszolgálót a konfigurációs kiszolgálóval.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Példa: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Várjon, amíg a szkript be nem fejeződik. Ha a fő cél regisztrálása sikeresen megtörtént, a fő cél a portál **site Recovery infrastruktúra** lapján jelenik meg.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>A fő cél telepítése interaktív telepítés használatával

1. Futtassa a következő parancsot a fő cél telepítéséhez. Az ügynök szerepkörhöz válassza a **fő cél** lehetőséget.

    ```
    ./install
    ```

2. Válassza ki a telepítés alapértelmezett helyét, majd válassza az **ENTER billentyűt** a folytatáshoz.

    ![Alapértelmezett hely kiválasztása a fő cél telepítéséhez](./media/vmware-azure-install-linux-master-target/image17.png)

A telepítés befejezése után regisztrálja a konfigurációs kiszolgálót a parancssor használatával.

1. Jegyezze fel a konfigurációs kiszolgáló IP-címét. A következő lépésben szüksége lesz rá.

2. A következő parancs futtatásával regisztrálja a kiszolgálót a konfigurációs kiszolgálóval.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh
    ```

     Várjon, amíg a szkript be nem fejeződik. Ha a fő cél regisztrálása sikeresen megtörtént, a fő cél a portál **site Recovery infrastruktúra** lapján jelenik meg.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>A VMware Tools/Open-VM-Tools telepítése a fő célkiszolgálón

Telepítenie kell a VMware-eszközöket vagy a nyílt virtuálisgép-eszközöket a fő célhelyre, hogy az képes legyen felderíteni az adattárakat. Ha az eszközök nincsenek telepítve, az ismételt védelem képernyő nem jelenik meg az adattárakban. A VMware-eszközök telepítését követően újra kell indítania.

### <a name="upgrade-the-master-target-server"></a>A fő célkiszolgáló frissítése

Indítsa el a telepítőt. A automatikusan észleli, hogy az ügynök telepítve van a fő célhelyen. A frissítéshez válassza az **Y** lehetőséget.  A telepítés befejezése után a következő parancs használatával keresse meg a telepített fő cél verzióját:

`cat /usr/local/.vx_version`


Látni fogja, hogy a **Version (verzió** ) mező a fő cél verziószámát adja meg.

## <a name="common-issues"></a>Gyakori problémák

* Ügyeljen rá, hogy ne kapcsolja be a vMotion a felügyeleti összetevőkön, például a fő célhelyen. Ha a fő cél sikeres ismételt védelem után mozog, a virtuális gép lemezei (VMDK) nem választhatók le. Ebben az esetben a feladat-visszavétel sikertelen lesz.

* A fő cél nem rendelkezhet pillanatképekkel a virtuális gépen. Pillanatképek esetén a feladat-visszavétel sikertelen lesz.

* Egyes egyéni NIC-konfigurációk miatt a hálózati adapter az indítás során le van tiltva, és a fő célként megadott ügynök nem inicializálható. Győződjön meg arról, hogy a következő tulajdonságok helyesen vannak beállítva. Ezeket a tulajdonságokat az Ethernet kártya fájljának/etc/network/interfaces. kell megnéznie
    * automatikus ETH0
    * iface ETH0 inet DHCP <br>

    Indítsa újra a hálózati szolgáltatást a következő parancs használatával: <br>

`sudo systemctl restart networking`


## <a name="next-steps"></a>Következő lépések
A fő cél telepítésének és regisztrálásának befejeződése után a fő cél a **site Recovery infrastruktúra** **fő célhely** szakaszában jelenik meg, a konfigurációs kiszolgáló áttekintése alatt.

Most már folytathatja az ismételt [védelmet](vmware-azure-reprotect.md), majd a feladat-visszavétel után is.

