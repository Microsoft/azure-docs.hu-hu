---
title: 'Oktatóanyag: Azure FXT Edge Filer fizikai eszköz telepítése'
description: A Microsoft Azure FXT Edge Filer Hybrid Storage cache fizikai eszköz összetevőjének kicsomagolása, rack és kábeles csatlakoztatása
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 48ee6de28bcd76d4c484b77c981062bad1a3754d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219751"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Oktatóanyag: az Azure FXT Edge Filer telepítése

Ez az oktatóanyag azt ismerteti, hogyan telepíthet hardveres csomópontot az Azure FXT Edge Filer Hybrid Storage cache-hez. Az Azure FXT Edge Filer-fürt létrehozásához legalább három hardveres csomópontot kell telepítenie.

A telepítési eljárás magában foglalja az eszköz kicsomagolását és állványra történő csatlakoztatását, valamint a kábel-felügyeleti ARM (CMA) és az előlap csatlakoztatását. Egy külön oktatóanyag ismerteti A hálózati kábelek csatlakoztatását és a tápellátást.

Egy Azure FXT Edge Filer-csomópont telepítéséhez körülbelül egy óra szükséges.

Ez az oktatóanyag a következő telepítési lépéseket tartalmazza:

> [!div class="checklist"]
>
> * Az eszköz kicsomagolása
> * Az eszköz csatlakoztatása egy rackben
> * Az elõlap telepítése (nem kötelező)

## <a name="installation-prerequisites"></a>Telepítési előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy az Ön által használt adatközpont és állvány a következő funkciókkal rendelkezik:

* Egy elérhető 1U-tárolóhely az állványon, amelyhez csatlakoztatni kívánja az eszközt.
* Az Azure FXT Edge Filer igényeinek megfelelő AC-tápegységek és-hűtési rendszerek. (Olvasási [teljesítmény-és termál-specifikációk](fxt-specs.md#power-and-thermal-specifications) a telepítés megtervezéséhez és méretezéséhez.)  

  > [!NOTE]
  > Ahhoz, hogy teljes mértékben ki tudja használni a két redundáns tápegységet (PSUs), használja a Power Distribution units szolgáltatást két különböző fiókirodában az AC-teljesítmény csatlakoztatásakor. Részletekért olvassa el az [energiaellátási kábelek csatlakoztatása](fxt-network-power.md#connect-power-cables) című leírást.  

## <a name="unpack-the-hardware-node"></a>A hardver csomópontjának kicsomagolása

Minden Azure FXT Edge Filer-csomópont egyetlen mezőben van elküldve. Az eszköz kicsomagolásához hajtsa végre az alábbi lépéseket.

1. Helyezze a csomagot egy sima, vízszintes felületre.

2. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a doboz vagy a csomagolás súlyosan sérült, ne nyissa meg. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e.

3. Bontsa ki a dobozt. Győződjön meg arról, hogy az a következő elemeket tartalmazza:
   * Egyetlen ház FXT-eszköz
   * Két tápkábel
   * Egy előlap és egy kulcs
   * Egy vasúti készlet szerelvény
   * Egy kábel-felügyeleti ARM (CMA)
   * CMA telepítési útmutató füzet
   * A rack telepítési útmutatóját ismertető füzet
   * Biztonsági, környezeti és szabályozási tájékoztató füzet

Ha nem kapta meg az összes felsorolt elemet, forduljon az eszköz gyártójához támogatásért.

Győződjön meg arról, hogy az eszköznek elég ideje volt ahhoz, hogy ugyanazt a hőmérsékletet érje el, mielőtt telepítené vagy bekapcsolja a helyet. Ha a kondenzációt az eszköz bármely részén észleli, várjon legalább 24 órát a telepítés előtt.

A következő lépés az eszköz csatlakoztatása.

## <a name="rack-the-device"></a>Az eszköz állványra szerelése

Az Azure FXT Edge Filer eszközt standard 19 hüvelykes állványra kell telepíteni.

Az Azure FXT Edge Filer Hybrid Storage cache három vagy több Azure FXT Edge Filer-eszközből áll. Ismételje meg az állvány telepítési lépéseit a rendszer részét képező minden eszközön.

### <a name="rack-install-prerequisites"></a>A rack telepítési előfeltételei

* Mielőtt elkezdené, olvassa el az eszközével szállított biztonsági, környezeti és szabályozási tájékoztató füzet biztonsági utasításait.

  > [!NOTE]
  > Mindig használjon két személyt a csomópont feloldásakor, beleértve azt is, hogy mikor telepíti azt egy állványon, vagy távolítsa el a rackből.

* Azonosítsa a berendezések állványához használt vasúti telepítés típusát.
  * Szögletes vagy kerek furatos Snap-In állványok esetén kövesse az eszköz nélküli vasúti útmutatást.
  * Menetes furatok esetén kövesse az eszközre vonatkozó vasúti utasításokat.
  
    Az eszközre telepített vasúti csatlakoztatási konfigurációhoz nyolc csavart kell megadnia, a 10-32, 12-24, M5 vagy M6 típust. A csavarok fő átmérőjének 10 mm-nél (0,4 ") kisebbnek kell lennie.

### <a name="identify-the-rail-kit-contents"></a>A Rail Kit tartalmának azonosítása

Keresse meg a következő összetevőket a Rail Kit szerelvény telepítéséhez:

1. Két A7-es Dell ReadyRails II csúszó vasúti szerelvény (1)
1. Két Hook-és loop-pánt (2)

![A Rail Kit tartalmának számozott rajza](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Vasúti szerelvény – eszköz nélküli sínek (szögletes furatos vagy kerek furatos állványok)

A beépülő modult vagy a körkörös furatokat tartalmazó állványok esetében kövesse ezt az eljárást a Rails összeállításához és telepítéséhez.

1. Helyezze a bal és a jobb oldali sín **elülső** részét, amely a befelé néző. Helyezze az egyes végpontokat úgy, hogy az a függőlegesen futó karimák elülső oldalán lévő lyukakba kerüljön. 1

2. Illessze be az egyes végpontokat a rack alsó és felső lyukakba abban a térben, amelyhez csatlakoztatni kívánja. (2)

3. Folytassa a sín hátsó végét egészen addig, amíg teljes mértékben nem ül a függőleges állvány peremén, és a retesz a helyére kattan. Ismételje meg ezeket a lépéseket úgy, hogy az előtér-darabot a függőleges állvány peremén pozícionálja és helyezze el. 3

> [!TIP]
> Ha el szeretné távolítani a síneket, húzza a zárolás feloldása gombot a végének középpontban (4), és szüntesse meg az egyes korlátokat.

![Eszköz nélküli sínek telepítésének és eltávolításának ábrája, a következő lépésekkel:](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Vasúti szerelvény – eszközön futó sínek (többszálú furatok)

Az összefűzött lyukakkal rendelkező állványok esetében kövesse ezt az eljárást a Rails összeállításához és telepítéséhez.

1. Távolítsa el a PIN-kód első és hátsó szerelvényeit egy lapos csavarhúzóval. 1
1. A sínre reteszelő alszerelvények lekérése és elforgatásával távolítsa el őket a beépítési zárójelből. (2)
1. Csatolja a bal és a jobb oldali szerelvényt az elülső függőleges rack karimához két pár csavart használva. 3
1. Csúsztassa a bal és a jobb oldali zárójelet a hátsó függőleges rack karimák felé, és csatolja őket két pár csavar használatával. 4

![Az eszközhöz tartozó sínek telepítésének és eltávolításának ábrája, a következő lépésekkel megszámozva](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>A System telepítése a rackbe

A következő lépésekkel csatlakoztathatja az Azure FXT Edge Filer eszközt a rackben.

1. Húzza ki a belső diát a rackből, amíg a helyükre nem zár. 1
1. Keresse meg a hátsó vasúti patthelyzetet az eszköz mindkét oldalán, és csökkentse azokat a hátsó, a dia szerelvényeken található nyílásokkal. (2)
1. Forgassa el az eszközt lefelé, amíg az összes vasúti patthelyzet be nem illeszkedik a J-slotba. 3
1. Az eszköz befelé történő leküldése, amíg a zárolási kar be nem kattint a helyére.
1. Nyomja le mindkét Rails (4) lenyomott zárolási gombját, és csúsztassa az eszközt a rackbe.

![A rendszer telepítése rack-diagramon, a következő lépésekkel:](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>A rendszer eltávolítása a rackből

Az eszköz a rackből való eltávolításához kövesse az alábbi eljárást.

1. Keresse meg a rögzítési kart a belső sínek oldalain (1).
2. Oldja fel az egyes kart a kiadási pozícióra (2) való elforgatásával.
3. A rendszer szilárdan fogja megfogni a rendszer oldalait, és addig húzza azt addig, amíg a sínre nem állnak a J-slotok. Emelje fel a rendszer felszínét a rackből, és helyezze el egy szinten (3).

![A rendszer a rackből való eltávolításának ábrája, a következő lépések megszámozásával:](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>A Slam-zár bevonása

1. A rendszer mindkét oldalán megkeresi a Slam-zárat (1).
2. A zárolások automatikusan elvégzik a rendszer beküldését a rackbe.

A zárolások felszabadításához a számítógép eltávolításakor húzza őket (2).

A nem kötelezően rögzíthető rögzített csavarok biztosítják, hogy a rendszer az állványra vagy más instabil környezetbe kerüljön. Keresse meg az egyes zárolások alatt található csavart, és húzza őket egy #2 Phillips csavarhúzóval (3).

![A Slam-zár bevonásának és felszabadításának számozott illusztrációja](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>A kábel-felügyeleti ARM telepítése

A FXT Edge Filer-mel (CMA) nem kötelező megadni a kábel-felügyeleti ARM-t. A telepítéshez szükséges nyomtatott utasítások a csomagban találhatók.

1. Csomagolja ki és azonosítsa a Cable Management ARM-készlet összetevőit:
   * CMA-tálca (1)
   * CMA (2)
   * Nylon kábelek összekötése (3)
   * CMA csatolt zárójelek (4)
   * Állapotjelző kábel (5)

   > [!TIP]
   > A CMA a rackbe való szállítás biztonságossá tételéhez a döntetlent a kosárba és a tálcára, valamint az RCA-re kell rögzíteni. A CMA ily módon történő biztonságossá tétele instabil környezetekben is biztonságossá teszi a rendszerét.

   ![CMA-részek illusztrációja](media/fxt-install/cma-kit-400.png)

2. Telepítse a CMA tálcát.

   A CMA-tálca támogatást nyújt, és a CMA megőrzöttként működik.

   1. A tálca belső szélein a fogadó zárójelek között illessze be és folytassa az egyes lapokat.
   1. Továbbítsa a tálcát addig, amíg a helyére nem kattan. 1
   1. A tálca eltávolításához nyomja le a zárolási gombokat a középpont felé, és húzza a tálcát a fogadó zárójelek közül (2).

   ![A CMA-tálca telepítésének illusztrációja](media/fxt-install/cma-tray-install-400.png)

3. Telepítse a CMA csatolt zárójeleket.

   > [!NOTE]
   >
   > * A CMA a jobb vagy a bal oldali csatlakoztatási korláthoz csatolhatja attól függően, hogy hogyan kívánja továbbítani a kábeleket a rendszerből.
   > * A kényelem érdekében csatlakoztassa a CMA az áramforráshoz szemközt (a. oldal). Ha a B oldalon van csatlakoztatva, a CMA le kell kapcsolni, hogy el lehessen távolítani a külső tápegységet.
   > * A tápegység eltávolítása előtt mindig távolítsa el a tálcát.

   ![A CMA zárójeles telepítésének szemléltetése](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Válassza ki a megfelelő CMA-mellékletet arra az oldalra, amelyhez csatlakoztatni kívánja a CMA (B. oldal vagy oldal).
   1. Telepítse a CMA csatolt zárójelet az ahhoz tartozó oldalsó vagy oldalsó B jelöléssel a dia-sín hátulján.
   1. Igazítsa a zárójelben lévő lyukakat a görgetősávon lévő PIN-kódokhoz. A szögletes zárójelet leküldheti, amíg a helyére nem kattan.

4. Telepítse a CMA.

   1. A rendszer hátoldalán illessze be a zárolást a CMA elülső végére, amíg a zárolás befejeződik (1).
   1. Illessze be a többi zárolást a legkülső zárójel végére, amíg a zárolás befejeződik (2).
   1. A CMA eltávolításához szüntesse meg mindkét zárolást úgy, hogy megnyomja a belső és külső kilincsek házainak felső részén található CMA-kiadási gombokat (3).

   ![A CMA fő telepítésének szemléltetése](media/fxt-install/cma-install-400.png)

   A CMA elforgatható a rendszertől a hozzáférés és a szolgáltatás számára. A csuklós végponton szüntesse meg a CMA a tálcáról, hogy megtegye a helyet (1). Miután a tálcáról nem rendelkezik, a CMA a rendszertől (2).

   ![A szolgáltatás számára megnyitott CMA illusztrációja](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Az elõlap telepítése (nem kötelező)

Ez a szakasz ismerteti, hogyan telepítheti és távolíthatja el az Azure FXT Edge Filer-hardver előlapját (előlapját).

Az előlap telepítése:

1. Keresse meg és távolítsa el az elõlap-csomagban található elõlapi kulcsot.
1. Igazítsa az elõlapot a váz elejéhez, majd szúrja be az elõlap jobb oldalán lévő PIN-kódokat a csomópont jobb oldali rack-csatlakoztatási peremén lévő lyukakba.
1. Az elõlap bal oldali végét az alvázra illeszti. Nyomja le az elõlapot, amíg a bal oldali gomb a helyére nem kattint.
1. Zárolja az elõlapot a kulccsal.

Az előlap eltávolítása:

1. A keret feloldása a keret kulcsának használatával.
1. Nyomja le a bal oldalon a kiadás gombot, és húzza le az elõlap bal oldali végét az alvázról.
1. Bontsa ki a jobb oldali végpontot, és távolítsa el az elõlapot.

   ![Az elõlap bal oldalán lévő kiadás gombot, és azt, hogy miként távolítható el a bal oldali kifelé húzva](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Következő lépések

Az eszköz kicsomagolása és rögzítése után folytassa a telepítést a hálózati kábelek csatlakoztatásával és az Azure FXT Edge Filer csatlakoztatásával.

> [!div class="nextstepaction"]
> [Hálózati portok csatlakoztatása és áramellátás](fxt-network-power.md)
