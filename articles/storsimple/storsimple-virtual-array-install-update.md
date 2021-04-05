---
title: Frissítések telepítése Microsoft Azure StorSimple virtuális tömbön | Microsoft Docs
description: Ismerteti, hogyan használható a StorSimple virtuális tömb webes felhasználói felülete a frissítések alkalmazásához a portál és a gyorsjavítási módszer használatával
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 9997a97b-9382-43ed-b56e-61369335c987
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3bd6f298ad2bb01503492b52c2d50dec82ec0ca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534043"
---
# <a name="install-updates-on-your-storsimple-virtual-array---azure-portal"></a>Telepítse a frissítéseket a StorSimple Virtual Array-Azure Portal

## <a name="overview"></a>Áttekintés

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a StorSimple virtuális tömb frissítéseinek telepítéséhez a helyi webes felületen és a Azure Portal keresztül. A StorSimple virtuális tömb naprakészen tartásához szükséges szoftverfrissítéseket vagy gyorsjavításokat kell alkalmaznia. 

Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Mivel a StorSimple virtuális tömb egyetlen csomópontos eszköz, a folyamatban lévő I/O-műveletek megszakadnak, és az eszköz leállást tapasztal. 

A frissítés alkalmazása előtt javasoljuk, hogy a köteteket vagy a megosztásokat a gazdagépen, majd az eszközön offline állapotba helyezze. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét.

> [!IMPORTANT]
> Ha a 0,1-es vagy a GA-os verziót futtatja, a gyorsjavítást a helyi webes felületen keresztül kell használnia az 0,3-es frissítés telepítéséhez. Ha a 0,2-es frissítést futtatja, javasoljuk, hogy telepítse a frissítéseket a klasszikus Azure portálon keresztül.
 

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

1. Indítsa el az Internet Explorert, és navigáljon a gombra [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A Microsoft Update-katalógus keresőmező mezőjébe írja be a letölteni kívánt gyorsjavítás tudásbázisának (KB) számát. Adja meg a **3182061** -es frissítést a 0,3-es frissítéshez, majd kattintson a **Keresés** gombra.
   
    Megjelenik a gyorsjavítások listája, például: **StorSimple Virtual Array Update 0,3**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update/download1.png)

4. Kattintson a **Hozzáadás** parancsra. Ezzel a frissítést hozzáadja a kosárhoz.

5. Kattintson a **Kosár megtekintése** gombra.

6. Kattintson a **Letöltés** gombra. Adja meg vagy **tallózással** válassza ki a helyet, ahová a fájlokat le szeretné tölteni. A frissítések a megadott helyre lesznek letöltve, azon belül az egyes frissítések nevével egyező nevű almappákba. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

7. Nyissa meg a másolt mappát, Microsoft Update önálló csomagfájl jelenik meg `WindowsTH-KB3011067-x64` . Ez a fájl a frissítés vagy a gyorsjavítás telepítéséhez használatos.

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítés vagy a gyorsjavítás le van töltve helyileg a gazdagépen, vagy elérhető egy hálózati megosztáson keresztül. 

Ezzel a módszerrel telepítheti a GA-t vagy a 0,1-es verziót futtató eszközök frissítéseit. Az eljárás végrehajtása kevesebb, mint 2 percet vesz igénybe. A frissítés vagy a gyorsjavítás telepítéséhez hajtsa végre a következő lépéseket.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

1. A helyi webes kezelőfelületen lépjen a **karbantartási**  >  **szoftverfrissítés** elemre.
   
    ![A képernyőfelvételen a karbantartási menüből kiválasztott szoftverfrissítés látható.](./media/storsimple-virtual-array-install-update/update1m.png)

2. A **frissítési fájl elérési útja** mezőben adja meg a frissítés vagy a gyorsjavítás fájlnevét. Ha hálózati megosztásra helyezi, tallózással is megkeresheti a frissítés vagy a gyorsjavítás telepítési fájlját. Kattintson az **Alkalmaz** gombra.
   
    ![Képernyőfelvétel: a frissítési fájl elérési útja szövegmező a szoftverfrissítés lapon.](./media/storsimple-virtual-array-install-update/update2m.png)

3. A rendszer figyelmeztetést jelenít meg. Ez az egyetlen csomópontos eszköz, amely a frissítés alkalmazása után újraindul, és leállási idő van. Kattintson a pipa ikonra.
   
   ![A képernyőfelvételen megjelenik egy párbeszédpanel, amely figyelmeztetést jelenít meg az állásidőről.](./media/storsimple-virtual-array-install-update/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület ebben az időtartamban nem érhető el.
   
    ![A képernyőképen a frissítés sikerességét jelző üzenet jelenik meg.](./media/storsimple-virtual-array-install-update/update5m.png)

5. Az újraindítás után a rendszer a **bejelentkezési** oldalra kerül. Annak ellenőrzéséhez, hogy az eszköz szoftvere frissítve lett-e, a helyi webes felületen lépjen a **karbantartási**  >  **szoftverfrissítés** lapra. A megjelenített szoftververzió **10.0.0.0.0.10288.0** kell lennie az 0,3-es frissítéshez.
   
   > [!NOTE]
   > A szoftver-verziókat a helyi webes KEZELŐFELÜLETen és a Azure Portal némileg eltérő módon jelentik be. A helyi webes felhasználói felület például a **10.0.0.0.0.10288** és a Azure Portal jelentések **10.0.10288.0** ugyanazt a verziót jelenti.
   
    ![Képernyőfelvétel: a szoftverfrissítés oldal a jelenlegi szoftververzió szerint.](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A 0,2-es frissítés futtatásakor javasoljuk, hogy telepítse a frissítéseket a Azure Portalon keresztül. A portál eljárása megköveteli a felhasználótól a frissítések vizsgálatát, letöltését és telepítését. Az eljárás végrehajtása körülbelül 7 percet vesz igénybe. A frissítés vagy a gyorsjavítás telepítéséhez hajtsa végre a következő lépéseket.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal.md)]

A telepítés befejezését követően (a feladatok állapota a 100%-ban jelezve) lépjen a StorSimple Eszközkezelő szolgáltatásra. Válassza az **eszközök** lehetőséget, majd válassza ki a frissíteni kívánt eszközt a szolgáltatáshoz csatlakozó eszközök listájából. A **Beállítások** panelen lépjen a **kezelés** szakaszra, és válassza az **eszközök frissítései** lehetőséget. A megjelenített szoftververzió **10.0.10288.0** kell lennie.


## <a name="next-steps"></a>Következő lépések

További információ [a StorSimple virtuális tömb felügyeletéről](storsimple-ova-web-ui-admin.md).

