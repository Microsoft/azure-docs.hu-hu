---
title: Az 0,5-es frissítés telepítése a StorSimple virtuális tömbön | Microsoft Docs
description: Ismerteti, hogyan használható a StorSimple Virtual Array webes FELÜLETe az 0,5-es frissítés alkalmazásához a Azure Portal és a gyors javítási módszer használatával.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: 5723e8d9fc7b0a72393dda1b225ca073a6474a0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94534313"
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Az 0,5-es frissítés telepítése a StorSimple virtuális tömbön

## <a name="overview"></a>Áttekintés

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a 0,5-es frissítés telepítéséhez a StorSimple virtuális tömbön a helyi webes felületen és a Azure Portal keresztül. A StorSimple virtuális tömb naprakészen tartásához szükséges szoftverfrissítéseket vagy gyorsjavításokat kell alkalmaznia.

A frissítés alkalmazása előtt javasoljuk, hogy a köteteket vagy a megosztásokat a gazdagépen, majd az eszközön offline állapotba helyezze. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. A kötetek vagy a megosztások offline állapotba helyezése után manuálisan kell biztonsági másolatot készíteni az eszközről.

> [!IMPORTANT]
> - Az 0,5-es frissítés megfelel az eszközön **10.0.10290.0** szoftver verziójának. A frissítéssel kapcsolatos újdonságokról a 0,5-es [frissítés kibocsátási megjegyzései](storsimple-virtual-array-update-05-release-notes.md)című témakörben olvashat bővebben.
>
> - Ha a 0,2-es vagy újabb frissítést futtatja, javasoljuk, hogy telepítse a frissítéseket a Azure Portal használatával. Ha a 0,1-es vagy a GA-os verziót futtatja, a gyorsjavítást a helyi webes felületen keresztül kell használnia az 0,5-es frissítés telepítéséhez.
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Mivel a StorSimple virtuális tömb egyetlen csomópontos eszköz, a folyamatban lévő I/O-műveletek megszakadnak, és az eszköz leállást tapasztal.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Ha a 0,2-es vagy újabb frissítést futtatja, javasoljuk, hogy telepítse a frissítéseket a Azure Portal. A portál eljárása megköveteli a felhasználótól a frissítések vizsgálatát, letöltését és telepítését. Az eljárás végrehajtása körülbelül 7 percet vesz igénybe. A frissítés vagy a gyorsjavítás telepítéséhez hajtsa végre a következő lépéseket.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

A telepítés befejezése után lépjen a StorSimple Eszközkezelő szolgáltatásra. Válassza az **eszközök** lehetőséget, majd válassza ki, majd kattintson az imént frissített eszközre. Válassza a **beállítások > > eszköz frissítéseinek kezelése lehetőséget**. A megjelenített szoftververzió **10.0.10290.0** kell lennie.

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

1. Indítsa el az Internet Explorert, és navigáljon a gombra [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A Microsoft Update-katalógus keresőmező mezőjébe írja be a letölteni kívánt gyorsjavítás tudásbázisának (KB) számát. Adja meg a **4021576** -es frissítést a 0,5-es frissítéshez, majd kattintson a **Keresés** gombra.
   
    Megjelenik a gyorsjavítások listája, például: **StorSimple Virtual Array Update 0,5**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Kattintson a **Letöltés** gombra. 

5. A letöltéshez két fájlt kell látnia, egy *. msu* -t és egy *. cab* -fájlt. Töltse le ezeket a fájlokat egy mappába. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

6. Nyissa meg azt a mappát, ahol a fájlok találhatók.
    ![A csomagban található fájlok](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    A következőt fogja látni:
    -  Egy önálló csomagfájl Microsoft Update `WindowsTH-KB3011067-x64` . Ez a fájl az eszköz szoftverének frissítésére szolgál.
    - Egy Genfi figyelési ügynök csomagfájl `GenevaMonitoringAgentPackageInstaller` . Ez a fájl a monitoring and Diagnostics szolgáltatás (MDS) ügynökének frissítésére szolgál. Kattintson duplán a cab-fájlra. Megjelenik egy. msi fájl. Válassza ki a fájlt, kattintson rá a jobb gombbal, majd **bontsa** ki a fájlt. Az ügynök frissítéséhez az _. msi_ fájlt fogja használni.

        ![MDS-ügynök frissítési fájljának kibontása](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítés vagy a gyorsjavítás le van töltve helyileg a gazdagépen, vagy elérhető egy hálózati megosztáson keresztül.

Ezzel a módszerrel telepítheti a GA-t vagy a 0,1-es verziót futtató eszközök frissítéseit. Az eljárás végrehajtása kevesebb, mint 2 percet vesz igénybe. A frissítés vagy a gyorsjavítás telepítéséhez hajtsa végre a következő lépéseket.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

1. A helyi webes kezelőfelületen lépjen a **karbantartási**  >  **szoftverfrissítés** elemre.
   
    ![A képernyőfelvételen a karbantartási menüből kiválasztott szoftverfrissítés látható.](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. A **frissítési fájl elérési útja** mezőben adja meg a frissítés vagy a gyorsjavítás fájlnevét. Ha hálózati megosztásra helyezi, tallózással is megkeresheti a frissítés vagy a gyorsjavítás telepítési fájlját. Kattintson az **Alkalmaz** gombra.
   
    ![Képernyőfelvétel: a frissítési fájl elérési útja szövegmező a szoftverfrissítés lapon.](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. A rendszer figyelmeztetést jelenít meg. Ez az egyetlen csomópontos eszköz, amely a frissítés alkalmazása után újraindul, és leállási idő van. Kattintson a pipa ikonra.
   
   ![A képernyőfelvételen megjelenik egy párbeszédpanel, amely figyelmeztetést jelenít meg az állásidőről.](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület ebben az időtartamban nem érhető el.
   
    ![A képernyőképen a frissítés sikerességét jelző üzenet jelenik meg.](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. Az újraindítás után a rendszer a **bejelentkezési** oldalra kerül. Annak ellenőrzéséhez, hogy az eszköz szoftvere frissítve lett-e, a helyi webes felületen lépjen a **karbantartási**  >  **szoftverfrissítés** lapra. A megjelenített szoftververzió **10.0.0.0.0.10290.0** kell lennie az 0,5-es frissítéshez.
   
   > [!NOTE]
   > A szoftver-verziókat a helyi webes KEZELŐFELÜLETen és a Azure Portal némileg eltérő módon jelentik be. A helyi webes felhasználói felület például a **10.0.0.0.0.10290** és a Azure Portal jelentések **10.0.10290.0** ugyanazt a verziót jelenti.
   
    ![Képernyőfelvétel: a szoftverfrissítés oldal a jelenlegi szoftververzió szerint.](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. A következő lépés az MDS-ügynök frissítése. A **szoftverfrissítés** lapon lépjen a **fájl frissítése elérési útra** , és keresse meg a `GenevaMonitoringAgentPackageInstaller.msi` fájlt. Ismételje meg a 2-4. lépést. A virtuális tömb újraindítása után jelentkezzen be a helyi webes felhasználói felületre.

A frissítés befejeződött.

## <a name="next-steps"></a>Következő lépések

További információ [a StorSimple virtuális tömb felügyeletéről](storsimple-ova-web-ui-admin.md).

