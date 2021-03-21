---
title: fájl belefoglalása
description: fájl belefoglalása
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 8ef8b9d3350d0599ab00dfdbb018cf8dd900e316
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95564337"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Frissítések telepítése az Azure Portalon keresztül

1. Nyissa meg a StorSimple-eszközkezelőt, és válassza az **Eszközök** elemet. A szolgáltatáshoz csatlakozó eszközök listájából válassza ki a frissíteni kívánt eszközt, majd kattintson rá.

2. A **Beállítások** területen kattintson az **eszközök frissítései** elemre.  

3. Megjelenik a szoftverfrissítések elérhetőségét jelző üzenet. A frissítések kereséséhez a **Vizsgálat** gombot is használhatja. Jegyezze fel a futtatott szoftver verzióját. 

    ![Az eszköz frissítései ablaktáblán az "új frissítések elérhetők" (kiemelve) és a "legutóbbi vizsgálat/6/22/2018 2:46 PM/szoftver verziója/10.0.10296.0" láthatók. A verzió ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    A rendszer értesítést küld, amikor a vizsgálat elindul, és sikeresen befejeződik.
 
4. Ha elkészült a frissítések vizsgálata, kattintson a **Frissítések letöltése** gombra. Az **új frissítések** területen tekintse át a kibocsátási megjegyzéseket. Azt is vegye figyelembe, hogy a frissítések letöltése után meg kell erősítenie a telepítést. Kattintson az **OK** gombra.

    ![Az eszköz frissítései területen a frissítések letöltése gomb ki van emelve. Az új frissítések lapon található egy hivatkozás a kibocsátási megjegyzésekre, valamint egy üzenet, amely a frissítések letöltését követően megerősíti a telepítést. Van egy OK gomb.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    A feltöltés indításáról és sikeres befejezéséről értesítést kap.

5. Az **eszköz frissítései** területen kattintson a **telepítés** elemre.

     ![Az eszközök frissítései a "frissítések telepítésének megerősítése" kifejezést jelentik. A telepítés gomb és a szoftver verziója ki van emelve.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Az **új frissítések** területen a rendszer figyelmezteti, hogy a frissítés megszakadt. Mivel a virtuális tömb egy egyetlen csomóponttal rendelkező eszköz, ezért az eszköz a frissítést követően újraindul. Ez megszakítja a folyamatban lévő I/O-műveleteket. A frissítések telepítéséhez kattintson az **OK** gombra.

    ![Az új frissítések azt jelentik, hogy "az eszközön a frissítések telepítése után állásidő lesz." Létezik egy hivatkozás a kibocsátási megjegyzésekre, és egy OK gombra.](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    A telepítési feladat indításáról értesítést kap.

7.  Miután a telepítési feladatot sikeresen befejeződik, kattintson a **feladatok megtekintése** hivatkozásra. Ez a művelet a **frissítések telepítése** panelre lép. A feladatról részletes információt itt talál. 

    ![Az eszközök frissítései a "frissítések telepítése" címkével rendelkeznek. A következő feladatot tekintheti meg: ".](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Ha az 1. verziójú szoftverfrissítési (10.0.10296.0) verziót futtató virtuális tömböt futtat, akkor a 1,1-es frissítés már fut, és készen áll. A hátralévő lépéseket kihagyhatja. 

    Ha a 0,6-es (10.0.10293.0) szoftververzió-verziót futtató virtuális tömbtel indult, a rendszer frissíti a 1,0-es frissítését. Megjelenik egy másik üzenet, amely jelzi, hogy a frissítések elérhetők. Ismételje meg a 4-7-es lépést a 1,1-es frissítés telepítéséhez.

    

