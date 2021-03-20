---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95559960"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Frissítések telepítése az Azure Portalon keresztül

1. Nyissa meg a StorSimple-eszközkezelőt, és válassza az **Eszközök** elemet. A szolgáltatáshoz csatlakozó eszközök listájából válassza ki a frissíteni kívánt eszközt, majd kattintson rá. 

    ![Az utóbbi esetben Eszközkezelő MySSDevManager ki van emelve és ki van választva, az eszközök kiemelve és kijelölve, az eszköz MYSSIS1103 pedig ki van emelve és ki van választva.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. A **Beállítások** panelen kattintson az **Eszközfrissítések** elemre. 

    ![Megjelenik a MYSSIS1103 információja. A beállítások területen az eszközök frissítései ki vannak emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. Megjelenik a szoftverfrissítések elérhetőségét jelző üzenet. A frissítések kereséséhez a **Vizsgálat** gombot is használhatja.

    ![Az eszköz frissítései ablaktáblán a vizsgálat gomb ki van emelve. Van egy négy soros üzenet: utolsó vizsgálat/nem vizsgált/szoftver verziója/10.0.10280.0.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    A vizsgálat indításáról és sikeres befejezéséről értesítést kap.

    ![Az értesítésben a "frissítések keresése az eszköz MySSIS 1103.2:12 PM/a művelet sikeresen befejeződött".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. Ha elkészült a frissítések vizsgálata, kattintson a **Frissítések letöltése** gombra. 

    ![Az eszköz frissítései ablaktáblán az "új frissítések érhetők el" és a "legutóbbi vizsgálat/11/3/2016 2:12 PM/szoftver verziója/10.0.10280.0" szerepel. A verzió ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. Az **Új frissítések** panel tájékoztatja arról, hogy a frissítések letöltése után meg kell erősítenie a telepítésüket. Kattintson az **OK** gombra.

    ![Az új frissítések ablaktábla "a frissítések letöltését követően meg kell erősítenie a telepítést". Az OK gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. A feltöltés indításáról és sikeres befejezéséről értesítést kap.

     ![Az értesítés a "frissítések letöltése az eszköz MySSIS... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. Az **Eszközfrissítések** panelen kattintson a **Telepítés** elemre.

     ![Az eszköz frissítései ablaktáblán a "frissítések telepítésének megerősítése" üzenet jelenik meg. A telepítés gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. Az **Új frissítések** panelen a rendszer figyelmezteti, hogy a frissítési folyamat megszakítja az eszköz egyéb folyamatait. Mivel a virtuális tömb egy egyetlen csomóponttal rendelkező eszköz, ezért az eszköz a frissítést követően újraindul. Ez megszakítja a folyamatban lévő I/O-műveleteket. A frissítések telepítéséhez kattintson az **OK** gombra. 

    ![Az új frissítések ablaktáblán látható üzenet a következő: "az eszközön a frissítések telepítése után állásidő lesz." Az OK gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. A telepítési feladat indításáról értesítést kap. 

    ![Az értesítés a "frissítések telepítése az eszköz MYSSIS1103" kifejezést írja. 2:15 PM.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  A telepítési feladat sikeres befejezése után a telepítés eredményeinek megtekintéséhez kattintson a **Feladat megtekintése** hivatkozásra az **Eszközfrissítések** panelen. 

    ![Az eszköz frissítései ablaktáblán a "frissítések telepítése" feliratú hivatkozás látható. A feladat megtekintése kiemelve.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    Ezzel továbblép a **Frissítések telepítése** panelre. A feladatról részletes információt itt talál.

    ![A frissítések telepítése panel telepítési adatokat tartalmaz, beleértve az eszköz, az állapot, az időtartam, a kezdési idő és a Leállítás időpontját.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. A frissítések sikeres telepítéséről értesítést láthat az Eszközfrissítések panelen. A szoftver verziója a **10.0.10288.0** is módosítja. 

    ![Az eszköz frissítései ablaktáblán látható, hogy az eszköz naprakész-e, és biztosítja a szoftver verzióját (10.0.10288.0).](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
