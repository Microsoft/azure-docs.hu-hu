---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3129bbe171329ecf37f8712394cedf9b70188220
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005816"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Frissítések telepítése az Azure Portalon keresztül

1. Nyissa meg a StorSimple-eszközkezelőt, és válassza az **Eszközök** elemet. A szolgáltatáshoz csatlakozó eszközök listájából válassza ki a frissíteni kívánt eszközt, majd kattintson rá.

    ![Az utóbbi esetben Eszközkezelő MySSDevManager ki van emelve és ki van választva, az eszközök kiemelve és kijelölve, az eszköz MYSSIS1103 pedig ki van emelve és ki van választva.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. A **Beállítások** panelen kattintson az **Eszközfrissítések** elemre.

    ![A MYSSIS1103 kapcsolatos információk jelennek meg. A beállítások területen az eszközök frissítései ki vannak emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Megjelenik a szoftverfrissítések elérhetőségét jelző üzenet. A frissítések kereséséhez a **Vizsgálat** gombot is használhatja. Jegyezze fel a futtatott szoftver verzióját. 

    ![Az eszköz frissítései ablaktáblán az "új frissítések érhetők el" és a "legutóbbi vizsgálat/11/01/2017 10:56 AM/szoftver verziója/10.0.10289.0" szerepel. A verzió ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    A rendszer értesítést küld, amikor a vizsgálat elindul, és sikeresen befejeződik.

    ![Az értesítésben a "frissítések keresése az eszköz MySVAFS110.... 10:57 AM/a művelet folyamatban van. "](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Ha elkészült a frissítések vizsgálata, kattintson a **Frissítések letöltése** gombra.

    ![Az eszköz frissítései ablaktáblán az "új frissítések érhetők el" üzenet jelenik meg. A frissítések letöltése gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Az **új frissítések** panelen tekintse át a kibocsátási megjegyzéseket. Azt is vegye figyelembe, hogy a frissítések letöltése után meg kell erősítenie a telepítést. Kattintson az **OK** gombra.

    ![Az új frissítések ablaktábla "a frissítések letöltését követően meg kell erősítenie a telepítést". Az OK gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. A feltöltés indításáról és sikeres befejezéséről értesítést kap.

     ![Az értesítés a "frissítések letöltése az eszköz MySVAFS... 11:07 AM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. Az **Eszközfrissítések** panelen kattintson a **Telepítés** elemre.

     ![Az eszközök frissítései a "frissítések telepítésének megerősítése" kifejezést jelentik. A telepítés gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. Az **Új frissítések** panelen a rendszer figyelmezteti, hogy a frissítési folyamat megszakítja az eszköz egyéb folyamatait. Mivel a virtuális tömb egy egyetlen csomóponttal rendelkező eszköz, ezért az eszköz a frissítést követően újraindul. Ez megszakítja a folyamatban lévő I/O-műveleteket. A frissítések telepítéséhez kattintson az **OK** gombra.

    ![Az új frissítések ablaktáblán látható üzenet a következő: "az eszközön a frissítések telepítése után állásidő lesz." Az OK gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. A telepítési feladat indításáról értesítést kap.

    ![Az értesítés a "frissítések telepítése az eszköz MySVAFS110..." üzenetet írja. 11:09 AM ".](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  A telepítési feladat sikeres befejezése után a telepítés eredményeinek megtekintéséhez kattintson a **Feladat megtekintése** hivatkozásra az **Eszközfrissítések** panelen. 

    ![Az eszköz frissítései ablaktáblán található "frissítések telepítése" címkével ellátott hivatkozás. A következő feladatot tekintheti meg: ". A telepítés gomb ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Ez a művelet a **frissítések telepítése** panelre lép. A feladatról részletes információt itt talál.

    ![A frissítések telepítése panel telepítési adatokat tartalmaz, beleértve az eszköz, az állapot, az időtartam, a kezdési idő és a Leállítás időpontját.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Ha a 0,6-es (10.0.10293.0) szoftverfrissítés-verziót futtató virtuális tömböt futtat, akkor most már az 1. frissítést futtatja, és készen áll. A hátralévő lépéseket kihagyhatja. Ha a 0,6-es (10.0.10293.0) frissítés előtti verziót futtató virtuális tömbtel indult, a rendszer frissíti a 0,6-es frissítését. Megjelenik egy másik üzenet, amely jelzi, hogy a frissítések elérhetők. Ismételje meg az 4-8-es lépést az 1. frissítés telepítéséhez.

    ![Az eszköz frissítései ablaktáblán az "új frissítések érhetők el" és a "legutóbbi vizsgálat/11/1/2017 10:56 AM/szoftver verziója/10.0.10293.0" szerepel.](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

