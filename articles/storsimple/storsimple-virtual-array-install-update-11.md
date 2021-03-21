---
title: Az 1,1-es frissítés telepítése a StorSimple virtuális tömbön | Microsoft Docs
description: Ismerteti, hogyan alkalmazhatók a frissítések a StorSimple virtuális tömb Azure Portal és helyi webes felhasználói felületének használatával
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2018
ms.author: alkohli
ms.openlocfilehash: 709794dcccf9b100cbb11381ac34cfb86238b22b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94533991"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>Az 1,1-es frissítés telepítése a StorSimple virtuális tömbön

## <a name="overview"></a>Áttekintés

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a 1,1-es frissítés telepítéséhez a StorSimple virtuális tömbön a helyi webes felületen és a Azure Portal keresztül.

A szoftverfrissítések vagy gyorsjavítások alkalmazása a StorSimple virtuális tömb naprakészen tartásához. A frissítés alkalmazása előtt javasoljuk, hogy a köteteket vagy a megosztásokat a gazdagépen, majd az eszközön offline állapotba helyezze. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét. A kötetek vagy a megosztások offline állapotba helyezése után manuálisan kell biztonsági másolatot készíteni az eszközről.

> [!IMPORTANT]
> - Az 1,1-es frissítés megfelel az eszközön **10.0.10307.0** szoftver verziójának. A frissítéssel kapcsolatos újdonságokról a 1,1-es [frissítés kibocsátási megjegyzései](storsimple-virtual-array-update-11-release-notes.md)című témakörben olvashat bővebben.
>
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Mivel a StorSimple virtuális tömb egyetlen csomópontos eszköz, a folyamatban lévő I/O-műveletek megszakadnak, és az eszköz leállást tapasztal.
>
> - Az 1,1-es frissítés csak akkor érhető el a Azure Portalban, ha a virtuális tömb az 1. frissítést futtatja. Az 0,6-es verzió frissítését futtató virtuális tömbök esetében először telepíteni kell a 1,0-es frissítést, majd alkalmaznia kell az Update 1,1 alkalmazást.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Ha a 0,2-es vagy újabb frissítést futtatja, javasoljuk, hogy telepítse a frissítéseket a Azure Portal. A portál eljárása megköveteli a felhasználótól a frissítések vizsgálatát, letöltését és telepítését. Attól függően, hogy a virtuális tömb melyik szoftververzió fut, a frissítés alkalmazása a Azure Portal eltérő.

 - Ha a virtuális tömb az 1. frissítést futtatja, a Azure Portal közvetlenül telepíti a 1,1-es frissítést (10.0.10307.0) az eszközön. Az eljárás végrehajtása körülbelül 10 percet vesz igénybe.
 - Ha a virtuális tömb a 0,6-es frissítést futtatja, a frissítés két fázisban történik. A Azure Portal először a 1,0 (10.0.10296.0) frissítést telepíti az eszközön. A virtuális tömb újraindul, és a portál telepíti az 1,1-es frissítést (10.0.10307.0) az eszközre. Az eljárás végrehajtása körülbelül 15 percet vesz igénybe.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

A telepítés befejezése után lépjen a StorSimple Eszközkezelő szolgáltatásra. Válassza az **eszközök** lehetőséget, majd válassza ki, majd kattintson az imént frissített eszközre. Válassza a **beállítások > > eszköz frissítéseinek kezelése lehetőséget**. A megjelenített szoftververzió **10.0.10307.0** kell lennie.

![Szoftverfrissítés utáni szoftververzió](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

> [!IMPORTANT] 
> **Csak akkor folytassa ezt a frissítést, ha az 1. frissítést (10.0.10296.0) futtatja. Ha a 0,6-es frissítést futtatja, először [telepítse az 1. frissítést](storsimple-virtual-array-install-update-1.md) az eszközre, majd alkalmazza a 1,1-es frissítést.**

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

Az alábbi lépések végrehajtásával töltse le a 1,1-es frissítést a Microsoft Update-katalógusból.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

1. Indítsa el az Internet Explorert, és navigáljon a gombra [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Ha első alkalommal használja a Microsoft Update-katalógust ezen a számítógépen, kattintson a **telepítés** gombra, amikor a rendszer felszólítja az Microsoft Update Catalog bővítmény telepítésére.

3. A Microsoft Update-katalógus keresőmező mezőjébe írja be a letölteni kívánt gyorsjavítás tudásbázisának (KB) számát. Adja meg a **4337628** -es frissítést a 1,1-es frissítéshez, majd kattintson a **Keresés** gombra.
   
    Megjelenik a gyorsjavítások listája, például: **StorSimple Virtual Array Update 1,1**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-11/download1.png)

4. Kattintson a **Letöltés** gombra.

5. Töltse le a két fájlt egy mappába. A mappát átmásolhatja egy olyan hálózati megosztásra is, amely elérhető az eszközről.

6. Nyissa meg azt a mappát, ahol a fájlok találhatók.

    ![A csomagban található fájlok](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    Két fájl jelenik meg:
    -  Egy önálló csomagfájl Microsoft Update `WindowsTH-KB3011067-x64` . Ez a fájl az eszköz szoftverének frissítésére szolgál.
    - A júniusi összesítő frissítéseket tartalmazó fájl `Windows8.1-KB4284815-x64` . További információ a jelen összesítésben található adatokról: [június havi biztonsági összesítés](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815).

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg az alábbiakról:

 - A frissítés vagy a gyorsjavítás helyileg, a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.
 - A virtuális tömb az 1. frissítést (10.0.10296.0) futtatja. Ha a 0,6-es frissítést futtatja, először [telepítse az 1. frissítést](storsimple-virtual-array-install-update-1.md) , majd telepítse a 1,1-es frissítést.

Az eljárás végrehajtása körülbelül 4 percet vesz igénybe. A frissítés vagy a gyorsjavítás telepítéséhez hajtsa végre a következő lépéseket.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

1. A helyi webes kezelőfelületen lépjen a **karbantartási**  >  **szoftverfrissítés** elemre. Jegyezze fel a futtatott szoftver verzióját. **Csak akkor folytassa ezt a frissítést, ha az 1. frissítést (10.0.10296.0) futtatja. Ha a 0,6-es frissítést futtatja, először [telepítse az 1. frissítést](storsimple-virtual-array-install-update-1.md) az eszközre, majd alkalmazza a 1,1-es frissítést.**
   
    ![A képernyőfelvételen a karbantartási menüből kiválasztott szoftverfrissítés látható.](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. A **frissítési fájl elérési útja** mezőben adja meg a frissítés vagy a gyorsjavítás fájlnevét. Ha hálózati megosztásra helyezi, tallózással is megkeresheti a frissítés vagy a gyorsjavítás telepítési fájlját. Kattintson az **Alkalmaz** gombra.
   
    ![Képernyőfelvétel: a frissítési fájl elérési útja szövegmező a szoftverfrissítés lapon.](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. A rendszer figyelmeztetést jelenít meg. Mivel a virtuális tömb egyetlen csomópontos eszköz, a frissítés alkalmazása után az eszköz újraindul, és leállási idő van. Kattintson a pipa ikonra.
   
   ![A képernyőfelvételen megjelenik egy párbeszédpanel, amely figyelmeztetést jelenít meg az állásidőről.](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület ebben az időtartamban nem érhető el.
   
    ![A képernyőképen a frissítés sikerességét jelző üzenet jelenik meg.](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. Az újraindítás után a rendszer a **bejelentkezési** oldalra kerül. Annak ellenőrzéséhez, hogy az eszköz szoftvere frissítve lett-e, a helyi webes felületen lépjen a **karbantartási**  >  **szoftverfrissítés** lapra. A megjelenített szoftververzió **10.0.0.0.0.10307** kell lennie az 1,1-es frissítéshez.
   
   > [!NOTE]
   > A szoftver-verziókat a helyi webes KEZELŐFELÜLETen és a Azure Portal némileg eltérő módon jelentik be. A helyi webes felhasználói felület például a **10.0.0.0.0.10307** és a Azure Portal jelentések **10.0.10307.0** ugyanazt a verziót jelenti.
   
    ![Képernyőfelvétel: a szoftverfrissítés oldal a jelenlegi szoftververzió szerint.](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. Ismételje meg a 2-4 lépést a Windows biztonsági javítás fájl használatával történő telepítéséhez `Windows8.1-KB4284815-x64` . A virtuális tömb a telepítés után újraindul, és be kell jelentkeznie a helyi webes felhasználói felületre.


## <a name="next-steps"></a>Következő lépések

További információ [a StorSimple virtuális tömb felügyeletéről](storsimple-ova-web-ui-admin.md).
