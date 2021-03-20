---
title: Azure Kinect DK – részletes kamera
description: Ismerje meg az Azure Kinect DK részletes kamerájának működési elveit és főbb funkcióit.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, szenzor, SDK, részletes kamera, TOF, alapelvek, teljesítmény, érvénytelenítés
ms.openlocfilehash: 22f04b983ed7c6a2ab19a5c1c709621655ee31c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277299"
---
# <a name="azure-kinect-dk-depth-camera"></a>Azure Kinect DK – részletes kamera

Ez az oldal ismerteti, hogyan használható a részletes kamera az Azure Kinect DK-ben. A részletes kamera a két kamera második része. Az előző szakaszban leírtak szerint a másik kamera az RGB-kamera.  

## <a name="operating-principles"></a>Működési alapelvek

Az Azure Kinect DK részletes kamera az amplitúdó modulált folyamatos hullám (AMCW) időrepülési (ToF) elvét valósítja meg. A kamera a jelenetre a közel-IR (NIR) spektrumon található, modulált megvilágítás. Ezután rögzíti az idő közvetett mérését, amely a kamerából a jelenetbe és vissza történő utazáshoz szükséges időt veszi igénybe.

A rendszer feldolgozza ezeket a mértékeket a részletes térkép létrehozásához. A mélységi Térkép a kép minden képpontjához tartozó Z-koordináta értékek halmaza, milliméterben megadva.

A részletes Térkép mellett egy úgynevezett tiszta IR-olvasót is beszerezhetünk. A tiszta IR olvasásban a képpontok értéke arányos a jelenet által visszaadott mennyiséggel. A rendszerkép egy normál IR-képhez hasonlít. Az alábbi ábrán egy példa részletes Térkép (balra) és egy megfelelő tiszta IR-kép látható (jobbra).

![Mélység és IR egymás mellett](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>A legfontosabb jellemzők

A részletes kamera technikai jellemzői a következők:

- 1 megapixeles ToF képfeldolgozó lapka speciális pixel technológiával, amely lehetővé teszi a nagyobb modulációs frekvenciák és a mélység pontosságát.
- Két NIR lézeres dióda, amely lehetővé teszi a közelében és a széles körű látómező (FoV) mélységi módokat.
- A világ legkisebb ToF képpont, 3,5 μm-nél 3,5 μm.
- Automatikus/képpontos nyereség kiválasztásával lehetővé válik, hogy a nagy dinamikus tartomány legyen tisztán rögzítve.
- Globális redőny, amely lehetővé teszi a jobb teljesítményt a napfényben.
- Többfázisú mélységi számítási módszer, amely robusztus pontosságot tesz lehetővé a chip, a Laser és a tápegység variációjának jelenlétében is.
- Alacsony szisztematikus és véletlenszerű hibák.

![Részletes modul](./media/concepts/depth-camera-depth-module.jpg)

A részletes kamera a nyers modulált IR-képeket továbbítja a gazdagép SZÁMÍTÓGÉPének. A PC-n a GPU-gyorsított mélységmérő motor szoftvere átalakítja a nyers jeleket a részletes térképekbe.A részletes kamera több módot is támogat. A **keskeny látómező (FOV)** módok ideálisak az X és Y dimenzióban kisebb egységekkel rendelkező jeleneteknél, de nagyobb mértékben a Z-dimenzióban. Ha a jelenet nagy X-és Y-egységekkel rendelkezik, de kisebb Z-tartományokkal rendelkezik, a **széles FOV-üzemmódok** jobban illeszkednek.

A részletes kamera az **2x2 dobozolási módokat** támogatja a Z-tartomány kiterjesztéséhez a megfelelő **unbinned-módokhoz** képest. A dobozolási a képfelbontás csökkentése után kerül sor. Az összes mód akár 30 képkocka/másodperc (FPS) módban is futtatható, kivéve az 1 megapixel (MP) üzemmódot, amely legfeljebb 15 FPS képkockával fut. A részletes kamera a **passzív IR módot** is biztosítja. Ebben a módban a kamera megvilágítói nem aktívak, és csak a környezeti megvilágítás figyelhető meg.

## <a name="camera-performance"></a>Kamera teljesítménye

A kamera teljesítményének mérése szisztematikus és véletlenszerű hibákkal történik.

### <a name="systematic-error"></a>Szisztematikus hiba

Szisztematikus hiba van meghatározva a zaj eltávolítása utáni mért mélység és a helyes (alapvető igazság) mélység közötti különbséggel. Egy statikus jelenet számos keretének időbeli átlagát a lehető legnagyobb mértékben kiszűrjük. Pontosabban, a szisztematikus hiba a következőképpen van definiálva:

![Részletes részletességi hiba](./media/concepts/depth-camera-systematic-error.png)

Ahol *a d <sub>t</sub>* a mérték mélységét jelöli a *t* időpontban, *N* az átlagos eljárásban használt keretek számát, a *d <sub>gt</sub>* pedig a terepi igazság mélységét.

A részletes kamera szisztematikus hibájának specifikációja a többutas interferencia (MPI) kivételével. Az MPI az, amikor az egyik érzékelő képpont integrálja a fényt, amelyet több objektum is tükröz. Az MPI-t részben a nagy modulációs gyakorisággal, valamint a mélységi érvénytelenítéssel együttesen is enyhítjük, amelyet később fogunk bevezetni.

### <a name="random-error"></a>Véletlenszerű hiba

Tegyük fel, hogy ugyanazon objektum 100-es lemezképét vesszük át a kamera áthelyezése nélkül. Az objektum mélysége némileg eltér a 100-lemezképben. Ezt a különbséget a shot Noise okozta. A shot Noise az érzékelőt elkerülő fotonok száma az idő függvényében véletlenszerű tényezővel. Ezt a véletlenszerű hibát statikus jelenetként definiáljuk, mivel a mélységi szórás a következőképpen számítható ki:

![Véletlenszerű mélységi hiba](./media/concepts/depth-camera-random-error.png)

Ahol *N* a mélységi mérések számát jelöli, *a <sub></sub> t a* *t* és a *d* időpontra vonatkozó mélységi mérési értéket jelöli *.<sub></sub>*

## <a name="invalidation"></a>Vermek érvénytelenítési

Bizonyos helyzetekben előfordulhat, hogy a mélységi kamera nem biztosít helyes értékeket bizonyos képpontokhoz. Ezekben a helyzetekben a mélységi képpontok érvénytelenítve lettek. Érvénytelen képpontok vannak megadva a mélység értéke 0. A mélységi motor nem tud megfelelő értékeket létrehozni:

- Az aktív IR megvilágítás maszkján kívül
- Telített IR-jel
- Alacsony IR-jel
- Kiugró szűrő
- Többutas interferencia

### <a name="illumination-mask"></a>Megvilágítás maszkja

A képpontok érvénytelenítve lettek, amikor az aktív IR megvilágítás maszkján kívül esnek. A számítási mélységhez nem ajánlott az ilyen képpontok jelét használni. Az alábbi ábrán a megvilágítás maszkja általi érvénytelenítés példája látható. A nem ellenőrzött képpontok a széles FoV-módok (balra) és a hatszög a keskeny FoV-módokon (jobb oldalon) kívüli fekete színű képpontok.

![Nem érvényes a megvilágítás maszkján kívül](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>Jelerősség

A képpontok érvénytelenítve lettek, ha telített IR-jelet tartalmaznak. Ha a képpontok telítettek, a fázis adatai elvesznek. Az alábbi képen egy telített IR-jel általi érvénytelenítés példája látható. Tekintse meg a nyílra mutató nyilakat a mélységben és az IR-képekben.

![Érvénytelenítési telítettség](./media/concepts/depth-camera-invalidation-saturation.png)

Az érvénytelenítés akkor is megtörténhet, ha az IR-jel nem elég erős a mélység létrehozásához. Az alábbi ábrán egy alacsony IR-jel általi érvénytelenítés példája látható. Tekintse meg a nyílra mutató nyilakat a részletes és az IR-képekben.

![Érvénytelenítési alsó jel](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>Nem egyértelmű mélység

A képpontok akkor is érvénytelenítve lesznek, ha a jelenetben egynél több objektumtól kapott jeleket. Gyakori eset, ha ez a fajta érvénytelenítés látható a sarkokban.  A jelenet geometriája miatt a kamerából származó IR-fény egy falhoz és a másikra mutat. Ez a fényvisszaverő fény a képpont mért mélységében a kétértelműség okait eredményezi. A mélységi algoritmusban lévő szűrők felderítik ezeket a kétértelmű jeleket, és érvénytelenítik a képpontokat.

Az alábbi ábra példákat mutat be a többszörös elérésiút-észleléssel való érvénytelenítésre. Azt is láthatja, hogy ugyanaz a felület, amely egy kamera nézetből (felső sorból) lett érvénytelenítve, egy másik kamera-nézetből (alsó sorban) is megjelenhet. Ez a rendszerkép azt mutatja be, hogy az egyik perspektívából érvénytelenített felületek egy másikból is láthatóvá válnak.

![Nem érvényes többutas (sarok)](./media/concepts/depth-camera-invalidation-multipath.png)

Egy másik gyakori eset a többutas elem, amely tartalmazza az előtérből és a háttérből származó vegyes jelet (például az objektumok szegélyeit). A gyors mozgás során több érvénytelenített képpontot láthat a szélei körül. A további érvénytelenített képpontok a nyers mélységi rögzítés expozíciós intervalluma miatt vannak megadva.

![Nem érvényes többutas (élek)](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>Következő lépések

[Koordináta-rendszerek](coordinate-systems.md)
