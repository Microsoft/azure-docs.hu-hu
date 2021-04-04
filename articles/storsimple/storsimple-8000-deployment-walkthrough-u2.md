---
title: A StorSimple 8000 Series eszköz üzembe helyezése Azure Portal
description: Ez a cikk a 3-as vagy újabb frissítést futtató StorSimple 8000 sorozatú eszköz és a StorSimple-eszközkezelő szolgáltatás üzembe helyezésének lépéseit és ajánlott eljárásait ismerteti.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: alkohli
ms.openlocfilehash: 2c9feb1131f6d2d0eb75ac71e27dc46c226c52c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961056"
---
# <a name="deploy-your-on-premises-storsimple-device-update-3-and-later"></a>A helyszíni StorSimple-eszköz (3. frissítés vagy újabb) üzembe helyezése

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés
Üdvözöljük a Microsoft Azure StorSimple eszköztelepítő útmutatójában. Ezek az üzembehelyezési oktatóanyagok a StorSimple 8000 Series 3-as vagy újabb frissítési verzióra vonatkoznak. Ez az oktatóanyag-sorozat tartalmazza a StorSimple eszköz konfigurálásához szükséges ellenőrzőlistát, előfeltételeket és részletes lépéseket.

Az oktatóanyagban szereplő információk arra a feltételezésre alapulnak, hogy áttekintette a biztonsági óvintézkedéseket, valamint hogy kicsomagolta, az állványba helyezte és bekábelezte a StorSimple eszközt. Ha még nem hajtotta végre ezeket a feladatokat, kezdje a [biztonsági óvintézkedések](storsimple-8000-safety.md) áttekintésével. Kövesse az eszköz kicsomagolására, állványra szerelésére és bekábelezésére vonatkozó eszközspecifikus utasításokat.

* [A StorSimple 8100 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8100-hardware-installation.md)
* [A StorSimple 8600 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8600-hardware-installation.md)

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. Azt javasoljuk, hogy mielőtt hozzákezdene, tekintse át a konfigurációs ellenőrzőlistát. Az üzembehelyezési és konfigurációs folyamatok végrehajtása eltarthat egy ideig.

> [!NOTE]
> A Microsoft Azure webhelyen közzétett StorSimple üzembehelyezési információk csak a StorSimple 8000 sorozat eszközeire érvényesek. Az 7000 sorozatú eszközökről a következő témakörben olvashat részletesen: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com) . A 7000-es sorozatra vonatkozó üzembehelyezési információkat a [StorSimple rendszer gyors üzembehelyezési útmutatójában](http://onlinehelp.storsimple.com/111_Appliance/) találja. 


## <a name="deployment-steps"></a>A központi telepítés lépései
Ezen szükséges lépések végrehajtásával konfigurálhatja a StorSimple-eszközt és csatlakoztathatja a StorSimple-eszközkezelő szolgáltatáshoz. A szükséges lépéseken kívül opcionális lépések és eljárások végrehajtására is szükség lehet az üzembe helyezés során. A részletes üzembehelyezési utasítások jelzik, amikor ezeket az opcionális lépéseket végre kell hajtania.

| Lépés | Leírás |
| --- | --- |
| **Előfeltételek** |Ezeknek teljesülniük kell az üzembe helyezésre való felkészülés során. |
| [Üzembe helyezési konfigurációs ellenőrzőlista](#deployment-configuration-checklist) |Ezzel az ellenőrzőlistával információkat gyűjthet és rögzíthet az üzembe helyezés előtt és közben. |
| [Üzembe helyezési előfeltételek](#deployment-prerequisites) |Ezek alapján lehet ellenőrizni, hogy a környezet készen áll-e az üzembe helyezésre. |
|  | |
| **RÉSZLETES ÜZEMBE HELYEZÉS** |Ezek a lépések szükségesek a StorSimple eszköz üzemi környezetben való telepítéséhez. |
| [1. lépés: Új szolgáltatás létrehozása](#step-1-create-a-new-service) |A felhőfelügyelet és a felhőalapú tárolás beállítása a StorSimple-eszközhöz. *Hagyja ki ezt a lépést, ha már rendelkezik meglévő szolgáltatással más StorSimple-eszközökhöz*. |
| [2. lépés: a szolgáltatás regisztrációs kulcsának beszerzése](#step-2-get-the-service-registration-key) |Ezzel a kulccsal regisztrálhatja és csatlakoztathatja az eszközét a felügyeleti szolgáltatáshoz. |
| [3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |A felügyeleti szolgáltatással végzett telepítés befejezéséhez csatlakoztassa az eszközt a hálózathoz, és regisztrálja az Azure-ban. |
| [4. lépés: Minimális eszközbeállítások végrehajtása.](#step-4-complete-minimum-device-setup)</br>[Ajánlott eljárás: A StorSimple eszköz frissítése](#scan-for-and-apply-updates) |A felügyeleti szolgáltatással végezze el az eszköz beállítását, és engedélyezze rajta a tárolást. |
| [5. lépés: Kötettároló létrehozása](#step-5-create-a-volume-container) |Hozzon létre egy tárolót a kötetek kiépítéséhez. A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára. |
| [6. lépés: kötet létrehozása](#step-6-create-a-volume) |Tárköteteket építhet ki a StorSimple-eszközön a kiszolgálói számára. |
| [7. lépés: Kötet csatlakoztatása, inicializálása és formázása](#step-7-mount-initialize-and-format-a-volume)</br>[Választható lehetőség: az MPIO konfigurálása](storsimple-8000-configure-mpio-windows-server.md) |Csatlakoztassa a kiszolgálókat az eszköz által biztosított iSCSI-tárolóhoz. Választható lehetőségként konfigurálhatja az MPIO-t, hogy a kiszolgálók képesek legyenek tűrni a kapcsolati, a hálózati és az adapterhibákat. |
| [8. lépés: biztonsági mentés készítése](#step-8-take-a-backup) |A biztonsági mentés házirendjének beállítása az adatok védelme érdekében |
|  | |
| **EGYÉB ELJÁRÁSOK** |Előfordulhat, hogy a megoldás üzembe helyezése során ezekre az eljárásokra kell majd hivatkoznia. |
| [Új tárfiók konfigurálása a szolgáltatáshoz](#configure-a-new-storage-account-for-the-service) | |
| [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](#use-putty-to-connect-to-the-device-serial-console) | |
| [Egy Windows Server-gazdagép IQN beolvasása](#get-the-iqn-of-a-windows-server-host) | |
| [Manuális biztonsági mentés létrehozása](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Üzembehelyezési konfigurációs ellenőrzőlista
Mielőtt üzembe helyezné az eszközt, információkat kell gyűjtenie a StorSimple-eszköz szoftverének konfigurálásához. Az információk időben történő előkészítésével leegyszerűsíthető a StorSimple-eszköz üzembe helyezésének folyamata a környezetben. Töltse le és használja ezt az ellenőrzőlistát a konfigurációs részletek lejegyzésére az eszköz üzembe helyezése során.

* [StorSimple üzembehelyezési konfigurációs ellenőrzőlista letöltése](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Üzembehelyezési előfeltételek
Az alábbi szakaszok ismertetik a StorSimple-eszközkezelő szolgáltatás és a StorSimple-eszköz konfigurációs előfeltételeit.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik Microsoft-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* A Microsoft Azure-előfizetés engedélyezve van a StorSimple-eszközkezelő szolgáltatáshoz. Az előfizetést a [Nagyvállalati Szerződésen](https://azure.microsoft.com/pricing/enterprise-agreement/) keresztül kell megvásárolni.
* Rendelkezik hozzáféréssel olyan terminálemulációs szoftverekhez, mint a PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Az adatközpontban található eszköz esetén
Az eszköz konfigurálása előtt győződjön meg róla, hogy az teljesen ki van csomagolva, állványra van rögzítve és minden kábel be van kötve a tápellátáshoz, a hálózathoz és a soros hozzáféréshez, a következő helyen leírtak szerint:

* [A StorSimple 8100 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8100-hardware-installation.md)
* [A StorSimple 8600 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az adatközpont tűzfalának portjai nyitva vannak az iSCSI és a felhőalapú forgalom számára, [A StorSimple eszköz hálózatkezelési követelményei](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device) című szakaszban leírtaknak megfelelően.

## <a name="step-by-step-deployment"></a>Részletes üzembe helyezés
Az alábbi részletes útmutatás segítségével helyezze üzembe a StorSimple eszközt az adatközpontban.

## <a name="step-1-create-a-new-service"></a>1. lépés: Új szolgáltatás létrehozása
A StorSimple-eszközkezelő szolgáltatás több StorSimple-eszközt is tud kezelni. Az alábbi lépések végrehajtásával hozza létre a StorSimple-eszközkezelő szolgáltatás egy példányát.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]

> [!IMPORTANT]
> Ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással, akkor legalább egy tárfiókot létre kell hoznia, miután sikeresen létrehozott egy szolgáltatást. Ezt a tárfiókot akkor használja a rendszer, amikor egy kötettárolót hoz létre.
>
> * Ha nem hozott létre automatikusan egy tárfiókot, a részletes utasításokat az [Új tárfiók konfigurálása a szolgáltatáshoz](#configure-a-new-storage-account-for-the-service) című szakaszban tekintheti meg.
> * Ha engedélyezte a tárfiók automatikus létrehozását, folytassa a [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2-get-the-service-registration-key) című szakasszal.


## <a name="step-2-get-the-service-registration-key"></a>2. lépés: Szolgáltatásregisztrációs kulcs lekérése
Ha a StorSimple-eszközkezelő szolgáltatás működik és elérhető, le kell kérnie a szolgáltatásregisztrációs kulcsot. Ezzel a kulccsal regisztrálhatja és csatlakoztathatja StorSimple eszközét a szolgáltatáshoz.

Hajtsa végre a következő lépéseket az Azure Portalon.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel
A StorSimple-höz készült Windows PowerShell-lel végezze el a StorSimple eszköz kezdeti beállítását az alábbiakban ismertetett eljárás alapján. A lépés végrehajtásához terminálemulációs szoftverre van szükség. További információ: [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-u2](../../includes/storsimple-8000-configure-and-register-device-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>4. lépés: Minimális eszközbeállítások végrehajtása.
A StorSimple minimális eszközkonfigurációjához a következőket kell végrehajtania: 

* Adjon egy valódi (értelmezhető) nevet az eszköznek.
* Adja meg az eszköz időzónáját.
* Rendeljen rögzített IP-címeket mindkét vezérlőhöz.

Hajtsa végre az alábbi lépéseket az Azure Portalon a minimális eszközbeállítások teljesítéséhez.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

A minimális eszközbeállítás elvégzése után az ajánlott eljárás a [legújabb frissítések keresése és alkalmazása](#scan-for-and-apply-updates).

## <a name="step-5-create-a-volume-container"></a>5. lépés: Kötettároló létrehozása
A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára. Ahhoz, hogy elkezdhessen köteteket kiépíteni a StorSimple eszközön, létre kell hoznia egy kötettárolót.

Kötettároló létrehozásához hajtsa végre a következő lépéseket az Azure Portalon.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>6. lépés: Kötet létrehozása
A kötettároló létrehozása után tárkötetet építhet ki a StorSimple eszközön a kiszolgálók számára. Kötet létrehozásához hajtsa végre a következő lépéseket az Azure Portalon.

> [!IMPORTANT]
> A StorSimple-eszközkezelő dinamikusan és teljesen kiosztott köteteket is képes létrehozni. Azonban nem hozhat létre részben kiosztott köteteket.


[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>7. lépés: Kötet csatlakoztatása, inicializálása és formázása
A következő lépéseket a Windows Server-állomásán kell végrehajtania.

> [!IMPORTANT]
> * Annak érdekében, hogy a StorSimple-megoldás nagy mértékben rendelkezésre álljon, javasolt az MPIO konfigurálását (opcionális) az iSCSI konfigurálása előtt elvégezni a gazdakiszolgálókon. Az MPIO gazdakiszolgálón történő konfigurálásával biztosítható, hogy a kiszolgálók képesek legyenek tűrni a kapcsolati, a hálózati és az adapterhibákat.
> * Az MPIO és az iSCSI Windows Server-állomásra való telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple eszközhöz](storsimple-8000-configure-mpio-windows-server.md) című szakaszban találja. Ezekben a StorSimple-kötetek csatolásához, inicializálásához és formázásához szükséges lépéseket is megtalálja.
> * Az MPIO és az iSCSI Linux-állomásra való telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple Linux-állomáshoz](storsimple-configure-mpio-on-linux.md) című szakaszban találja.

Ha mégsem szeretné konfigurálni az MPIO-t, az alábbi lépések végrehajtásával csatlakoztassa, inicializálja és formázza a StorSimple-köteteket egy Windows Server-állomáson.

[!INCLUDE [storsimple-8000-mount-initialize-format-volume](../../includes/storsimple-8000-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>8. lépés: Biztonsági mentés készítése
Az adott időpontban mentett biztonsági másolatok védelmet biztosítanak a kötetek számára, továbbá javítják a rendelkezésre álló helyreállítási lehetőségeket, miközben a helyreállítási időt csökkentik. A StorSimple eszközén kétféle biztonsági mentést készíthet: helyi pillanatképeket és felhőbeli pillanatképeket. Mind a kétféle biztonsági mentés lehet **Ütemezett** vagy **Manuális**.

Ütemezett biztonsági mentés létrehozásához hajtsa végre a következő lépéseket az Azure Portalon.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Manuális biztonsági mentést bármikor létrehozhat. Az eljárásokat a [Manuális biztonsági mentés létrehozása](#create-a-manual-backup) című szakaszban találja.

Befejezte az eszköz konfigurálását.

## <a name="configure-a-new-storage-account-for-the-service"></a>Új tárfiók konfigurálása a szolgáltatáshoz
Ez egy opcionális lépés, amelyet csak akkor kell végrehajtania, ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással. A StorSimple-kötettároló létrehozásához Microsoft Azure Storage-fiók szükséges.

Ha az Azure-tárfiókot egy másik régióban szeretné létrehozni, tekintse meg a részletes utasításokat a [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-account-create.md) című szakaszban.

Hajtsa végre a következő lépéseket az Azure Portal **StorSimple-eszközkezelő szolgáltatás** lapján.

[!INCLUDE [storsimple-8000-configure-new-storage-account-u2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz
A StorSimple-höz készült Windows PowerShellhez való csatlakozáshoz szükség lesz egy terminálemulációs szoftverre, például a PuTTY-ra. A PuTTY az eszköz soros konzolon keresztül történő közvetlen elérésekor vagy egy telnet-munkamenet távoli számítógépről történő megnyitása során vehető igénybe.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Frissítések keresése és telepítése
Az eszköz frissítése több órát is igénybe vehet. A legújabb frissítés telepítésének lépéseiért lásd: [A 5-es frissítés telepítése](storsimple-8000-install-update-5.md).


## <a name="get-the-iqn-of-a-windows-server-host"></a>Egy Windows Server-állomás IQN-nevének lekérése
Hajtsa végre a következő lépéseket egy Windows Server® 2012 rendszert futtató Windows-állomás iSCSI teljes nevének (IQN) lekéréséhez.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása
Ha szeretne egy azonnali manuális mentést létrehozni StorSimple-eszköze egyik kötetéről, akkor hajtsa végre a következő lépéseket a Azure Portalon.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="view-the-pinout-diagram-for-serial-cable-for-storsimple"></a>A StorSimple-höz tartozó soros kábel bekötési rajzának megtekintése
Az alábbi bekötési rajz a StorSimple soros konzol kábeléhez használható.

A képen a DB9 csatlakozóhüvely a P1, a 3,5 mm-es csatlakozó pedig a P2.

![1. bekötési rajz a StorSimple soros konzol kábeléhez](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram1.png)

A sztereó csatlakozó hegye PIN 3 RX-nek, a közepe PIN 2 TX-nek, az alapja pedig PIN 1 GND-nek minősül, ahogyan az a következő ábrán látható.

![2. bekötési rajz a StorSimple soros konzol kábeléhez](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram2.png)



## <a name="next-steps"></a>Következő lépések
* [A StorSimple Cloud Appliance konfigurálása](storsimple-8000-cloud-appliance-u2.md).
* [A StorSimple-eszközt a StorSimple-eszközkezelő szolgáltatás segítségével kezelheti.](storsimple-8000-manager-service-administration.md)