---
title: Red Hat frissítési infrastruktúra | Microsoft Docs
description: A Red Hat frissítési infrastruktúrájának megismerése az igény szerinti Red Hat Enterprise Linux-példányokhoz Microsoft Azure
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 968377ed09996b9a717e0739a3de8355d1c8d88d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677137"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Red Hat frissítési infrastruktúra az igény szerinti Red Hat Enterprise Linux virtuális gépek számára az Azure-ban
 A [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) lehetővé teszi, hogy a felhőalapú szolgáltatók, például az Azure, a Red Hat-ban üzemeltetett tárház tartalmait tükrözze, egyéni tárházat hozzon létre az Azure-specifikus tartalommal, és elérhetővé tegye a végfelhasználói virtuális gépek számára.

A Red Hat Enterprise Linux (RHEL) TB-lemezképek előre konfigurálva vannak az Azure RHUI eléréséhez. Nincs szükség további konfigurálásra. A legújabb frissítések beszerzéséhez futtassa a `sudo yum update` RHEL-példány elkészült verzióját. Ez a szolgáltatás része a RHEL TB.

További információ az Azure-beli RHEL-lemezképekről, beleértve a közzétételi és adatmegőrzési szabályzatokat is [itt](./redhat-images.md)érhető el.

A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.

> [!IMPORTANT]
> A RHUI kizárólag utólagos elszámolású (TB) rendszerképekhez készült. Az egyéni és az arany lemezképek, más néven a saját előfizetések (BYOS-EK) esetében a rendszernek a frissítések fogadásához csatolni kell a RHSM vagy a Satellite szolgáltatáshoz. További részletek: [Red Hat cikk](https://access.redhat.com/solutions/253273) .


## <a name="important-information-about-azure-rhui"></a>Fontos információk az Azure RHUI

* Az Azure RHUI a frissítési infrastruktúra, amely támogatja az Azure-ban létrehozott összes RHEL TB virtuális gépet. Ez nem zárja ki, hogy regisztrálja a TB RHEL-alapú virtuális gépeket az előfizetés-kezelővel, illetve a műholdon vagy más frissítési forrásokkal, de a TB virtuális géppel való használata esetén a rendszer közvetett kettős számlázást eredményez. A részletekért tekintse meg a következő pontot.
* Az Azure által üzemeltetett RHUI a RHEL TB rendszerképének ára tartalmazza. Ha töröl egy TB RHEL virtuális gépet az Azure által üzemeltetett RHUI, amely nem alakítja át a virtuális gépet saját Licences (BYOL) virtuálisgép-típusra. Ha ugyanezt a virtuális gépet egy másik frissítési forrással regisztrálja, a rendszer _közvetett_ kettős díjat is felmerülhet. Az Azure RHEL szoftver díját először számoljuk fel. A korábban megvásárolt Red Hat-előfizetések esetében a második alkalommal kell fizetnie. Ha az Azure által üzemeltetett RHUI eltérő frissítési infrastruktúrát kell használnia, érdemes regisztrálni a [RHEL BYOS-lemezképek](./byos.md)használatára.

* Az Azure-ban elérhető SAP-TB (RHEL for SAP, RHEL for SAP HANA és RHEL for SAP Business Applications) olyan dedikált RHUI-csatornákhoz csatlakoznak, amelyek az adott RHEL alverzióban maradnak, és az SAP-minősítéshez szükségesek.

* Az Azure által üzemeltetett RHUI való hozzáférés az [Azure-adatközpont IP-tartományán](https://www.microsoft.com/download/details.aspx?id=41653)belüli virtuális gépekre korlátozódik. Ha az összes virtuális gép forgalmát egy helyszíni hálózati infrastruktúrán keresztül végzi, lehetséges, hogy a RHEL TB virtuális gépekhez felhasználó által megadott útvonalakat kell beállítania az Azure-RHUI eléréséhez. Ha ez a helyzet, a felhasználó által megadott útvonalakat hozzá kell adni az _összes_ RHUI IP-címhez.


## <a name="image-update-behavior"></a>Rendszerkép-frissítési viselkedés

Április 2019 az Azure olyan RHEL-rendszerképeket kínál, amelyek alapértelmezés szerint a kiterjesztett frissítési támogatási (EUS) adattárakhoz csatlakoznak, és RHEL a normál (nem EUS) adattárakhoz csatlakozó lemezképeket. A RHEL EUS kapcsolatos további részletek a Red Hat [verziójának életciklus-dokumentációjában](https://access.redhat.com/support/policy/updates/errata) és a [EUs dokumentációjában](https://access.redhat.com/articles/rhel-eus)találhatók. Az alapértelmezett viselkedés `sudo yum update` attól függ, hogy melyik RHEL-lemezképet hozta létre a rendszer, mivel a különböző rendszerképek különböző adattárakhoz csatlakoznak.

A teljes rendszerkép listában futtassa `az vm image list --publisher redhat --all` Az Azure CLI-t.

### <a name="images-connected-to-non-eus-repositories"></a>Nem EUS-tárházhoz csatlakoztatott képek

Ha olyan RHEL-lemezképből hoz létre virtuális gépet, amely nem EUS-tárházhoz van csatlakoztatva, a futtatásakor a rendszer a legújabb RHEL alverzióra frissíti `sudo yum update` . Ha például egy virtuális gépet egy RHEL 7,4 TB-lemezképből hoz létre, és a futtatását futtatja `sudo yum update` , akkor a RHEL 7,8 virtuális gép (a RHEL7 család legújabb alverziója).

A nem EUS adattárakhoz csatlakoztatott lemezképek nem tartalmaznak alverziószámot az SKU-ban. Az SKU a harmadik eleme az URN-ben (a rendszerkép teljes neve). Az alábbi képek például a nem EUS adattárakhoz vannak csatolva:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Vegye figyelembe, hogy az SKU-nak 7-LVM vagy 7-RAW értékűnek kell lennie. Az alverzió a lemezképek (URN negyedik eleme) verziójában szerepel.

### <a name="images-connected-to-eus-repositories"></a>EUS-adattárakhoz csatlakoztatott képek

Ha EUS-adattárakhoz csatlakoztatott RHEL-lemezképből hoz létre virtuális gépet, a futtatásakor a rendszer nem frissíti a legújabb RHEL alverzióra `sudo yum update` . Ennek az az oka, hogy a EUS-adattárakhoz csatlakoztatott rendszerképek szintén a megadott alverzióhoz vannak rögzítve.

A EUS-adattárakhoz csatlakozó rendszerképek az SKU-ban is szerepelni fognak. Az alábbi képek például az EUS-adattárakhoz vannak csatolva:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS és verzió – RHEL virtuális gépek zárolása

Az Extended Update support (EUS) adattárházak olyan ügyfelek számára érhetők el, akik a virtuális gép üzembe helyezése után a RHEL-alapú virtuális gépeket egy bizonyos RHEL alverzióra szeretnék zárni. A RHEL virtuális gép egy adott alverzióra való rögzítéséhez frissítse a tárházat, hogy az a kiterjesztett frissítés támogatási tárházára mutasson. A EUS-zárolási műveletet is visszavonhatja.

>[!NOTE]
> A EUS nem támogatott a RHEL-extrák esetében. Ez azt jelenti, hogy ha olyan csomagot telepít, amely általában az RHEL extrák csatornából érhető el, akkor a EUS-on nem fogja tudni elvégezni. [Itt](https://access.redhat.com/support/policy/updates/extras/)részletesen ismertetjük a Red Hat extrák termék életciklusát.

Az írás időpontjában a EUS-támogatás befejeződött a RHEL <= 7,4 esetében. További részletekért tekintse meg a [Red Hat dokumentációjának](https://access.redhat.com/support/policy/updates/errata/#Long_Support) "Red Hat Enterprise Linux kiterjesztett karbantartás" című szakaszát.
* RHEL 7,4 EUS-támogatás vége augusztus 31., 2019
* RHEL 7,5 EUS támogatás vége április 30., 2020
* RHEL 7,6 EUS-támogatás vége május 31., 2021
* RHEL 7,7 EUS-támogatás vége augusztus 30-ig 2021

### <a name="switch-a-rhel-vm-7x-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL virtuális gép 7. x átváltása a EUS-be (verzió-zárolás egy adott alverzióra)
Az alábbi útmutatást követve zárolhat egy RHEL 7. x virtuális gépet egy adott másodlagos kiadásra (futtató gyökérként):

>[!NOTE]
> Ez csak azokra a RHEL 7. x verziókra vonatkozik, amelyekhez EUS érhető el. Az írás időpontjában ez a 7.2-7.7 RHEL is magában foglalja. További részletek a [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata) lapon érhetők el.

1. Nem EUS-alapú repók letiltása:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EUS-repók hozzáadása:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. A változó zárolása `releasever` (futtató gyökér):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A fenti utasítás a RHEL-alverzió zárolását az aktuális másodlagos kiadáshoz fogja zárolni. Adjon meg egy adott másodlagos kiadást, ha a frissítést és a zárolást egy későbbi, nem a legújabb verzióra szeretné használni. Például a `echo 7.5 > /etc/yum/vars/releasever` RHEL-verziót a RHEL 7,5-re fogja zárolni.

1. A RHEL virtuális gép frissítése
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-8x-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL virtuális gép 8. x átváltása a EUS-re (verzió – egy adott alverzióra való zárolás)
Az alábbi útmutatást követve zárolhat egy RHEL 8. x virtuális gépet egy adott másodlagos kiadásra (futtató gyökérként):

>[!NOTE]
> Ez csak azokra a 8. x verziójú RHEL vonatkozik, amelyekhez EUS érhető el. Az írás időpontjában ez a RHEL 8.1-8.2 részét képezi. További részletek a [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata) lapon érhetők el.

1. Nem EUS-alapú repók letiltása:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8'
    ```

1. A EUS Repos konfigurációs fájljának beolvasása:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8-eus.config
    ```

1. EUS-repók hozzáadása:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8-eus.config install rhui-azure-rhel8-eus
    ```

1. A változó zárolása `releasever` (futtató gyökér):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A fenti utasítás a RHEL-alverzió zárolását az aktuális másodlagos kiadáshoz fogja zárolni. Adjon meg egy adott másodlagos kiadást, ha a frissítést és a zárolást egy későbbi, nem a legújabb verzióra szeretné használni. Például a `echo 8.1 > /etc/yum/vars/releasever` RHEL-verziót a RHEL 8,1-re fogja zárolni.

    >[!NOTE]
    > Ha a releasever eléréséhez engedélyekkel kapcsolatos problémák lépnek fel, a fájlt szerkesztheti a "Nano/etc/yum/Vars/releaseve" paranccsal, és hozzáadhatja a rendszerkép verziószámát, és mentheti ("CTRL + o", majd nyomja le az ENTER billentyűt, majd a "CTRL + x" billentyűkombinációt).  

1. A RHEL virtuális gép frissítése
    ```bash
    sudo yum update
    ```


### <a name="switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock"></a>RHEL 7. x virtuális gép átváltása nem EUS (verzió zárolásának eltávolítása)
Futtassa a következőt root-ként:
1. Távolítsa el a `releasever` fájlt:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS-repók letiltása:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. RHEL virtuális gép konfigurálása
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. A RHEL virtuális gép frissítése
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-8x-vm-back-to-non-eus-remove-a-version-lock"></a>RHEL 8. x virtuális gép átváltása nem EUS (verziók zárolásának eltávolítása)
Futtassa a következőt root-ként:
1. Távolítsa el a `releasever` fájlt:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS-repók letiltása:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8-eus'
   ```

1. A rendszeres Repos konfigurációs fájljának beolvasása:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8.config
    ```

1. EUS-repók hozzáadása:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8.config install rhui-azure-rhel8
    ```
    
1. A RHEL virtuális gép frissítése
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>A RHUI-tartalomszolgáltató kiszolgálók IP-címei

A RHUI minden olyan régióban elérhető, ahol RHEL igény szerinti lemezképek érhetők el. A szolgáltatás jelenleg az [Azure status Dashboard](https://azure.microsoft.com/status/) oldalon, az Azure US governmentben és a Microsoft Azure Germany régiókban felsorolt összes nyilvános régiót tartalmazza.

Ha hálózati konfigurációt használ a RHEL TB virtuális gépekről való hozzáférés további korlátozására, akkor győződjön meg arról, hogy a következő IP-címek használhatók a `yum update` környezettől függően:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```
>[!NOTE]
>Az új Azure US government-rendszerképek a 2020-as számú Azure-beli nyilvános IP-címen lesznek használva.

>[!NOTE]
>Azt is vegye figyelembe, hogy az Azure Germany a németországi régiók mellett elavult. Az Azure Germany-ügyfelekre vonatkozó javaslat az [alábbi lépésekkel](#manual-update-procedure-to-use-the-azure-rhui-servers)indul el a nyilvános RHUI.

## <a name="azure-rhui-infrastructure"></a>Azure RHUI-infrastruktúra


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Lejárt RHUI-ügyféltanúsítvány frissítése egy virtuális gépen

Ha egy régebbi RHEL virtuálisgép-rendszerképet használ, például a RHEL 7,4 (rendszerkép URN: `RedHat:RHEL:7.4:7.4.2018010506` ), akkor kapcsolati problémák merülhetnek fel a RHUI, mert már lejárt a TLS/SSL-ügyféltanúsítvány. A megjelenített hiba a következőhöz hasonló lehet: _"az SSL-társ a tanúsítvány lejártként való elutasítása"_ vagy _"hiba: nem lehet lekérni a tárház metaadatait (repomd.xml) az adattár számára... Ellenőrizze az elérési útját, és próbálkozzon újra "_. A probléma megoldásához frissítse a RHUI-ügyfélszoftvert a virtuális gépen a következő parancs használatával:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Azt is megteheti, hogy a Futtatás `sudo yum update` a RHEL-verziótól függően frissíti az ügyféltanúsítvány-csomagot (a lejárt SSL-tanúsítvány után), és más adattárakban is megjelenik. Ha ez a frissítés sikeres, a más RHUI-adattárakhoz való normál kapcsolatot vissza kell állítani, hogy sikeresen fusson `sudo yum update` .

Ha a futtatása során 404-as hibát észlel `yum update` , próbálja meg a következőt a yum-gyorsítótár frissítéséhez:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Az Azure RHUI kapcsolódási problémáinak elhárítása
Ha problémákat tapasztal az Azure RHUI az Azure RHEL TB virtuális gépről való csatlakoztatásával kapcsolatban, kövesse az alábbi lépéseket:

1. Ellenőrizze az Azure RHUI-végpont virtuálisgép-konfigurációját:

    1. Ellenőrizze, `/etc/yum.repos.d/rh-cloud.repo` hogy a fájl tartalmazza-e a `rhui-[1-3].microsoft.com` `baseurl` `[rhui-microsoft-azure-rhel*]` fájl szakaszának hivatkozását. Ha igen, az új Azure-RHUI használja.

    1. Ha a következő mintának megfelelő helyre mutat, `mirrorlist.*cds[1-4].cloudapp.net` a konfiguráció frissítésére van szükség. A régi virtuális gép pillanatképét használja, és frissítenie kell, hogy az új Azure-RHUI mutasson.

1. Az Azure által üzemeltetett RHUI való hozzáférés az [Azure-adatközpont IP-tartományán](https://www.microsoft.com/download/details.aspx?id=41653)belüli virtuális gépekre korlátozódik.

1. Ha az új konfigurációt használja, ellenőrizte, hogy a virtuális gép csatlakozik az Azure IP-tartományhoz, és továbbra sem tud csatlakozni az Azure RHUI-hoz, a Microsoft vagy a Red Hat támogatási esetét.

### <a name="infrastructure-update"></a>Infrastruktúra frissítése

2016 szeptemberében üzembe helyezettünk egy frissített Azure-RHUI. 2017 áprilisában leállítottuk a régi Azure-RHUI. Ha a RHEL TB-lemezképeit (vagy pillanatképeit) a szeptember 2016-es vagy újabb verziójával használta, automatikusan csatlakozik az új Azure-RHUI. Ha azonban régebbi pillanatképeket használ a virtuális gépeken, manuálisan kell frissítenie a konfigurációját, hogy hozzáférjen az Azure-RHUI a következő szakaszban leírtak szerint.

Az új Azure RHUI-kiszolgálók üzembe helyezése az [azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). Traffic Manager egyetlen végpontot (rhui-1.microsoft.com) is használhat bármely virtuális gép, a régiótól függetlenül.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Az Azure RHUI-kiszolgálók használatának manuális frissítési eljárása
Ez az eljárás csak referenciául szolgál. A RHEL TB-lemezképek már rendelkeznek a megfelelő konfigurációval az Azure RHUI való kapcsolódáshoz. Ha manuálisan szeretné frissíteni a konfigurációt az Azure RHUI-kiszolgálók használatára, hajtsa végre a következő lépéseket:

- RHEL 6 esetén:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- A RHEL 7 esetében:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- A 8. RHEL esetében:
    1. Hozzon létre egy konfigurációs fájlt:
        ```bash
        vi rhel8.config
        ```
    1. Adja hozzá a következő tartalmat a konfigurációs fájlhoz:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Mentse a fájlt, és futtassa a következő parancsot:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. A virtuális gép frissítése
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Következő lépések
* Red Hat Enterprise Linux virtuális gép Azure Marketplace-TB rendszerképből való létrehozásához, valamint az Azure által üzemeltetett RHUI használatához látogasson el az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RHEL_6)-re.
* Ha többet szeretne megtudni az Azure-beli Red Hat-lemezképekről, lépjen a [dokumentáció lapra](./redhat-images.md).
* A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.
