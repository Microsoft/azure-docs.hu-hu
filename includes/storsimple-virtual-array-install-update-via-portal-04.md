---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48ce594fa5f4b736e69b5b4ef7a10011fe8031fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027392"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Frissítések telepítése az Azure Portalon keresztül

1. Nyissa meg a StorSimple-eszközkezelőt, és válassza az **Eszközök** elemet. A szolgáltatáshoz csatlakozó eszközök listájából válassza ki a frissíteni kívánt eszközt, majd kattintson rá. 

    ![Az utóbbi esetben Eszközkezelő MySSDevManager ki van emelve és ki van választva, az eszközök kiemelve és kijelölve, az eszköz MYSSIS1103 pedig ki van emelve és ki van választva.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. A **Beállítások** panelen kattintson az **Eszközfrissítések** elemre. 

    ![A MYSSIS1103 kapcsolatos információk jelennek meg. A beállítások területen az eszközök frissítései ki vannak emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Megjelenik a szoftverfrissítések elérhetőségét jelző üzenet. A frissítések kereséséhez a **Vizsgálat** gombot is használhatja.

    ![Az eszköz frissítései ablaktáblán az "új frissítések érhetők el" és a "legutóbbi vizsgálat/18/01/2017 2:42 PM/szoftver verziója/10.0.10288.0" szerepel. A verzió ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate3m1.png)

    A vizsgálat indításáról és sikeres befejezéséről értesítést kap.

    ![Az értesítésben a "frissítések keresése az eszköz MySSIS1103. 2:12 PM/a művelet végrehajtása sikeresen befejeződött. "](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate5m.png)

4. Ha elkészült a frissítések vizsgálata, kattintson a **Frissítések letöltése** gombra. 

    ![Az eszköz frissítései ablaktáblán az "új frissítések érhetők el" üzenet jelenik meg. A frissítések letöltése gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate6m.png)

5. Az **Új frissítések** panel tájékoztatja arról, hogy a frissítések letöltése után meg kell erősítenie a telepítésüket. Kattintson az **OK** gombra.

    ![Az új frissítések ablaktábla "a frissítések letöltését követően meg kell erősítenie a telepítést". Az OK gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate7m.png)

6. A feltöltés indításáról és sikeres befejezéséről értesítést kap.

     ![Az értesítés a "frissítések letöltése az eszköz MySSIS1... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate8m.png)

5. Az **Eszközfrissítések** panelen kattintson a **Telepítés** elemre.

     ![Az eszközök frissítései a "frissítések telepítésének megerősítése" kifejezést jelentik. Van egy telepítési gomb.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate11m1.png)   

6. Az **Új frissítések** panelen a rendszer figyelmezteti, hogy a frissítési folyamat megszakítja az eszköz egyéb folyamatait. Mivel a virtuális tömb egy egyetlen csomóponttal rendelkező eszköz, ezért az eszköz a frissítést követően újraindul. Ez megszakítja a folyamatban lévő I/O-műveleteket. A frissítések telepítéséhez kattintson az **OK** gombra. 

    ![Az új frissítések ablaktáblán látható üzenet a következő: "az eszközön a frissítések telepítése után állásidő lesz." Az OK gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate12m.png) 

7. A telepítési feladat indításáról értesítést kap. 

    ![Az értesítés a "frissítések telepítése az eszköz MySSIS1103. 2:15 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate13m.png)

8.  A telepítési feladat sikeres befejezése után a telepítés eredményeinek megtekintéséhez kattintson a **Feladat megtekintése** hivatkozásra az **Eszközfrissítések** panelen. 

    ![Az eszköz frissítései ablaktáblán található "frissítések telepítése" címkével ellátott hivatkozás. A következő feladatot tekintheti meg: ".](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate15m1.png)

    Ezzel továbblép a **Frissítések telepítése** panelre. A feladatról részletes információt itt talál.

    ![A frissítések telepítése panel telepítési adatokat tartalmaz, beleértve az eszköz, az állapot, az időtartam, a kezdési idő és a Leállítás időpontját.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate16m1.png)

9. A frissítések sikeres telepítéséről értesítést láthat az **Eszközfrissítések** panelen. 
