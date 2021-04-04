---
title: StorSimple 8000 Series-eszköz üzembe helyezése a Government Portalon | Microsoft Docs
description: Ismerteti azokat a lépéseket és ajánlott eljárásokat, amelyekkel üzembe helyezheti az StorSimple 8000 Series eszközt a 3. frissítést és az újabb verziót, valamint a szolgáltatást a Azure Government portálon.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: alkohli
ms.openlocfilehash: d736c09fc1c9490f79dfc526895970e01b8b45cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94963181"
---
# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>Helyszíni StorSimple-eszköz üzembe helyezése a kormányzati portálon

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés
Üdvözöljük a Microsoft Azure StorSimple eszköztelepítő útmutatójában. Ezek az üzembehelyezési oktatóanyagok a 3. frissítés szoftvert futtató StorSimple 8000 sorozatra vonatkoznak, vagy később a Azure Government-portálon. Az oktatóanyagok ezen sorozata tartalmaz egy konfigurációs ellenőrzőlistát, a konfigurációs előfeltételek listáját, valamint a StorSimple-eszköz részletes konfigurációs lépéseit.

Az oktatóanyagban szereplő információk arra a feltételezésre alapulnak, hogy áttekintette a biztonsági óvintézkedéseket, valamint hogy kicsomagolta, az állványba helyezte és bekábelezte a StorSimple eszközt. Ha még nem hajtotta végre ezeket a feladatokat, kezdje a [biztonsági óvintézkedések](storsimple-8000-safety.md) áttekintésével. Kövesse az eszköz kicsomagolására, állványra szerelésére és bekábelezésére vonatkozó eszközspecifikus utasításokat.

* [A StorSimple 8100 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8100-hardware-installation.md)
* [A StorSimple 8600 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8600-hardware-installation.md)

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. Azt javasoljuk, hogy mielőtt hozzákezdene, tekintse át a konfigurációs ellenőrzőlistát. Az üzembehelyezési és konfigurációs folyamatok végrehajtása eltarthat egy ideig.

> [!NOTE]
> A Microsoft Azure webhelyen közzétett StorSimple üzembehelyezési információk csak a StorSimple 8000 sorozat eszközeire érvényesek. Az 7000 sorozatú eszközökről a következő témakörben olvashat részletesen: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com) . A 7000-es sorozatra vonatkozó üzembehelyezési információkat a [StorSimple rendszer gyors üzembehelyezési útmutatójában](http://onlinehelp.storsimple.com/111_Appliance/) találja.


## <a name="deployment-steps"></a>A központi telepítés lépései
Ezen szükséges lépések végrehajtásával konfigurálhatja a StorSimple-eszközt és csatlakoztathatja a StorSimple-eszközkezelő szolgáltatáshoz. A szükséges lépések mellett választható lépések és eljárások is megadhatók, amelyeket a telepítés során esetlegesen kell végrehajtania. A részletes üzembehelyezési utasítások jelzik, amikor ezeket az opcionális lépéseket végre kell hajtania.

| Lépés | Leírás |
| --- | --- |
| **Előfeltételek** |Ezeknek kell teljesülniük az üzembe helyezésre való felkészülés során. |
| [Üzembe helyezési konfigurációs ellenőrzőlista](#deployment-configuration-checklist) |Ezzel az ellenőrzőlistával információkat gyűjthet és rögzíthet az üzembe helyezés előtt és közben. |
| [Üzembe helyezési előfeltételek](#deployment-prerequisites) |Ezek ellenőrzik, hogy a környezet készen áll-e a telepítésre. |
|  | |
| **RÉSZLETES ÜZEMBE HELYEZÉS** |Ezek a lépések szükségesek a StorSimple eszköz üzemi környezetben való telepítéséhez. |
| [1. lépés: Új szolgáltatás létrehozása](#step-1-create-a-new-service) |A felhőfelügyelet és a felhőalapú tárolás beállítása a StorSimple-eszközhöz. *Hagyja ki ezt a lépést, ha már rendelkezik meglévő szolgáltatással más StorSimple-eszközökhöz*. |
| [2. lépés: a szolgáltatás regisztrációs kulcsának beszerzése](#step-2-get-the-service-registration-key) |Ezzel a kulccsal regisztrálhat és csatlakozhat a StorSimple-eszközhöz a felügyeleti szolgáltatással. |
| [3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Csatlakoztassa az eszközt a hálózathoz, és a beállítás befejezéséhez regisztrálja az Azure-ban a felügyeleti szolgáltatás segítségével. |
| [4. lépés: az eszköz minimális telepítésének befejezése](#step-4-complete-minimum-device-setup) </br>Választható lehetőség: A StorSimple eszköz frissítése. |A felügyeleti szolgáltatással végezze el az eszköz beállítását, és engedélyezze rajta a tárolást. |
| [5. lépés: Kötettároló létrehozása](#step-5-create-a-volume-container) |Hozzon létre egy tárolót a kötetek kiépítéséhez. A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára. |
| [6. lépés: kötet létrehozása](#step-6-create-a-volume) |Tárkötet(ek)et építhet ki a StorSimple eszközön a kiszolgálói számára. |
| [7. lépés: Kötet csatlakoztatása, inicializálása és formázása](#step-7-mount-initialize-and-format-a-volume) </br>Választható lehetőség: Az MPIO konfigurálása. |Csatlakoztassa a kiszolgálókat az eszköz által biztosított iSCSI-tárolóhoz. Ha szükséges, konfigurálja az MPIO-t annak biztosítására, hogy a kiszolgálók el tudják viselni a kapcsolat, a hálózat és az interfész meghibásodását. |
| [8. lépés: biztonsági mentés készítése](#step-8-take-a-backup) |A biztonsági mentés házirendjének beállítása az adatok védelme érdekében |
|  | |
| **EGYÉB ELJÁRÁSOK** |Előfordulhat, hogy a megoldás üzembe helyezése során ezekre az eljárásokra kell majd hivatkoznia. |
| [Új tárfiók konfigurálása a szolgáltatáshoz](#configure-a-new-storage-account-for-the-service) | |
| [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](#use-putty-to-connect-to-the-device-serial-console) | |
| [Frissítések keresése és telepítése](#scan-for-and-apply-updates) | |
| [Egy Windows Server-gazdagép IQN beolvasása](#get-the-iqn-of-a-windows-server-host) | |
| [Manuális biztonsági mentés létrehozása](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Üzembehelyezési konfigurációs ellenőrzőlista
A StorSimple-eszköz üzembe helyezése előtt össze kell gyűjtenie az eszközön a szoftver konfigurálásához szükséges adatokat. Az információk időben történő előkészítésével leegyszerűsíthető a StorSimple eszköz üzembe helyezésének folyamata a környezetben. Töltse le és használja ezt a feladatlistát, és jegyezze fel a konfiguráció részleteit az eszköz üzembe helyezése során.

[StorSimple üzembehelyezési konfigurációs ellenőrzőlista letöltése](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Üzembehelyezési előfeltételek
Az alábbi szakaszok ismertetik a StorSimple-eszközkezelő szolgáltatás és a StorSimple-eszköz konfigurációs előfeltételeit.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik Microsoft-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* A Microsoft Azure-előfizetés engedélyezve van a StorSimple-eszközkezelő szolgáltatáshoz. Az előfizetést a [Nagyvállalati Szerződésen](https://azure.microsoft.com/pricing/enterprise-agreement/) keresztül kell megvásárolni.
* Rendelkezik hozzáféréssel olyan terminálemulációs szoftverekhez, mint a PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Az adatközpontban található eszköz esetén
Az eszköz konfigurálása előtt győződjön meg az alábbiakról:

* Az eszköz teljesen ki van csomagolva, állványra van rögzítve és minden kábel be van kötve a tápellátáshoz, a hálózathoz és a soros hozzáféréshez, a következő helyen leírtak szerint:
  
  * [A StorSimple 8100 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8100-hardware-installation.md)
  * [A StorSimple 8600 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az adatközpont tűzfalának portjai nyitva vannak az iSCSI és a felhőalapú forgalom számára, [A StorSimple eszköz hálózatkezelési követelményei](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device) című szakaszban leírtaknak megfelelően.

## <a name="step-by-step-deployment"></a>Részletes üzembe helyezés
Az alábbi részletes útmutatás segítségével helyezze üzembe a StorSimple eszközt az adatközpontban.

## <a name="step-1-create-a-new-service"></a>1. lépés: Új szolgáltatás létrehozása
A StorSimple-eszközkezelő szolgáltatás több StorSimple-eszközt is tud kezelni. A StorSimple Eszközkezelő szolgáltatás új példányának létrehozásához hajtsa végre a következő lépéseket.

[!INCLUDE [storsimple-8000-create-new-service-gov](../../includes/storsimple-8000-create-new-service-gov.md)]

> [!IMPORTANT]
> Ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással, akkor legalább egy tárfiókot létre kell hoznia, miután sikeresen létrehozott egy szolgáltatást. Ezt a tárfiókot akkor fogja használni a rendszer, amikor egy kötettárolót hoz létre.
> 
> * Ha nem hozott létre automatikusan egy tárfiókot, a részletes utasításokat az [Új tárfiók konfigurálása a szolgáltatáshoz](#configure-a-new-storage-account-for-the-service) című szakaszban tekintheti meg.
> * Ha engedélyezte a tárfiók automatikus létrehozását, folytassa a [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2-get-the-service-registration-key) című szakasszal.


## <a name="step-2-get-the-service-registration-key"></a>2. lépés: Szolgáltatásregisztrációs kulcs lekérése
Ha a StorSimple-eszközkezelő szolgáltatás működik és elérhető, le kell kérnie a szolgáltatásregisztrációs kulcsot. Ezzel a kulccsal regisztrálhatók és csatlakoztathatók a StorSimple-eszközök a szolgáltatáshoz.

Hajtsa végre a következő lépéseket a kormányzati portálon.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel
A StorSimple-höz készült Windows PowerShell-lel végezze el a StorSimple eszköz kezdeti beállítását az alábbiakban ismertetett eljárás alapján. A lépés végrehajtásához terminálemulációs szoftverre lesz szüksége. További információ: [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-gov](../../includes/storsimple-8000-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>4. lépés: Minimális eszközbeállítások végrehajtása.
A StorSimple minimális eszközkonfigurációjához a következőket kell végrehajtania:

* Adjon egy valódi (értelmezhető) nevet az eszköznek.
* Adja meg az eszköz időzónáját.
* Rendeljen rögzített IP-címeket mindkét vezérlőhöz.

A minimális eszköz telepítésének befejezéséhez hajtsa végre a következő lépéseket a Azure Government-portálon.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

## <a name="step-5-create-a-volume-container"></a>5. lépés: Kötettároló létrehozása
A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára. Ahhoz, hogy elkezdhessen köteteket kiépíteni a StorSimple eszközön, létre kell hoznia egy kötettárolót.

A mennyiségi tároló létrehozásához hajtsa végre a következő lépéseket a kormányzati portálon.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>6. lépés: Kötet létrehozása
A kötettároló létrehozása után tárkötetet építhet ki a StorSimple eszközön a kiszolgálók számára. Kötet létrehozásához hajtsa végre a következő lépéseket a kormányzati portálon.

> [!IMPORTANT]
> A StorSimple Eszközkezelő csak dinamikusan kiosztott köteteket hozhat létre.  Azonban nem hozhat létre részben kiosztott köteteket.

[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>7. lépés: Kötet csatlakoztatása, inicializálása és formázása
Hajtsa végre ezeket a lépéseket a Windows Server-gazdagépen.

> [!IMPORTANT]
> * Annak érdekében, hogy a StorSimple-megoldás nagy mértékben rendelkezésre álljon, javasolt az MPIO konfigurálását (opcionális) az iSCSI konfigurálása előtt elvégezni a gazdakiszolgálókon. Az MPIO gazdakiszolgálón történő konfigurálásával biztosítható, hogy a kiszolgálók képesek legyenek tűrni a kapcsolati, a hálózati és az adapterhibákat.
> * Az MPIO és az iSCSI Windows Server-állomásra való telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple eszközhöz](./storsimple-8000-configure-mpio-windows-server.md) című szakaszban találja. Ezekben a StorSimple-kötetek csatolásához, inicializálásához és formázásához szükséges lépéseket is megtalálja.
> * Az MPIO és az iSCSI Linux-állomásra való telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple Linux-állomáshoz](storsimple-configure-mpio-on-linux.md) című szakaszban találja.

Ha mégsem szeretné konfigurálni az MPIO-t, az alábbi lépések végrehajtásával csatlakoztassa, inicializálja és formázza a StorSimple-köteteket egy Windows Server-állomáson.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>8. lépés: Biztonsági mentés készítése
Az adott időpontban mentett biztonsági másolatok védelmet biztosítanak a kötetek számára, továbbá javítják a rendelkezésre álló helyreállítási lehetőségeket, miközben a helyreállítási időt csökkentik. A StorSimple eszközén kétféle biztonsági mentést készíthet: helyi pillanatképeket és felhőbeli pillanatképeket. Mind a kétféle biztonsági mentés lehet **Ütemezett** vagy **Manuális**.

Ütemezett biztonsági mentés létrehozásához hajtsa végre a következő lépéseket a kormányzati portálon.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Manuális biztonsági mentést bármikor létrehozhat. Az eljárásokat a [Manuális biztonsági mentés létrehozása](#create-a-manual-backup) című szakaszban találja.

## <a name="configure-a-new-storage-account-for-the-service"></a>Új tárfiók konfigurálása a szolgáltatáshoz
Ez egy opcionális lépés, amelyet csak akkor kell végrehajtania, ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással. A StorSimple-kötettároló létrehozásához Microsoft Azure Storage-fiók szükséges.

Ha az Azure-tárfiókot egy másik régióban szeretné létrehozni, tekintse meg a részletes utasításokat a [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-account-create.md) című szakaszban.

Hajtsa végre a következő lépéseket a kormányzati portál **StorSimple Eszközkezelő szolgáltatás** lapján.

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz
A StorSimple-höz készült Windows PowerShellhez való csatlakozáshoz szükség lesz egy terminálemulációs szoftverre, például a PuTTY-ra. A PuTTY az eszköz soros konzolon keresztül történő közvetlen elérésekor vagy egy telnet-munkamenet távoli számítógépről történő megnyitása során vehető igénybe.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Frissítések keresése és telepítése
Az eszköz frissítése több órát is igénybe vehet. A legújabb frissítés telepítésének lépéseiért lásd: [A 4-es frissítés telepítése](storsimple-8000-install-update-4.md).

## <a name="get-the-iqn-of-a-windows-server-host"></a>Egy Windows Server-állomás IQN-nevének lekérése
Hajtsa végre a következő lépéseket egy Windows Server® 2012 rendszert futtató Windows-állomás iSCSI teljes nevének (IQN) lekéréséhez.

[!INCLUDE [Get IQN of your Windows Server host](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása
Hajtsa végre a következő lépéseket a kormányzati portálon, és hozzon létre egy igény szerinti manuális biztonsági mentést a StorSimple-eszköz egyetlen kötetén.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Következő lépések
* [Virtuális eszköz](storsimple-8000-cloud-appliance-u2.md) konfigurálása.
* A StorSimple-eszköz kezeléséhez használja a [StorSimple Eszközkezelő szolgáltatást](storsimple-8000-manager-service-administration.md) .