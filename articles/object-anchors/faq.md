---
title: Gyakori kérdések
description: Az Azure Object Anchors szolgáltatással kapcsolatos gyakori kérdések.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 6de027f317072512bd9893303d3a79d0f34f3e3e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748813"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Az Azure Object-Horgonyokkal kapcsolatos gyakori kérdések

Az Azure Object-horgonyok lehetővé teszik az alkalmazás számára, hogy 3D modell használatával azonosítsa a fizikai világban lévő objektumokat, és megbecsülje a 6-DoF póz értékét.

További információ: az [Azure Object-horgonyok áttekintése](overview.md).

## <a name="product-faq"></a>Termékkel kapcsolatos gyakori kérdések
**K: milyen javaslatok szükségesek a használni kívánt objektumokhoz?**

**A:** A következő tulajdonságokat ajánljuk az objektumokhoz:

* 1-10 méter az egyes dimenziók esetében
* Nem szimmetrikus, megfelelő változatokkal a geometriában
* Gyengén visszaverő (Matt felületek) világos színnel
* Álló objektumok
* Nincs vagy kis mennyiségű artikuláció
* Hátterek törlése a nem vagy a minimális zűrzavar nélkül
* A beolvasott objektumnak 1:1 egyeznie kell a betanított modellel

**K: melyek a modell betöltésére feldolgozható objektumok maximális mérete?**

**A:** A CAD-modellek mindegyik dimenziójának 10 méternél rövidebbnek kell lennie.

**K: mekkora a CAD-modell maximális mérete, amely feldolgozásra használható?**

**A:** A modell fájlméretének 150 MB-nál kisebbnek kell lennie.

**K: Mik a támogatott CAD-formátumok?**

**A:** Jelenleg a,,, `fbx` `ply` `obj` `glb` és `gltf` fájltípusokat támogatjuk.

**K: milyen gravitációs irány és egység szükséges a modell betöltésére szolgáló szolgáltatáshoz? Hogyan lehet kitalálni őket?**

**A:** A gravitáció iránya a földre mutató lefelé irányuló vektor. A CAD-modellek esetében a gravitáció iránya általában egy felfelé irányuló irány ellentéte. Például a (z) és a (Z) és a (Z) függvény a következőt jelenti: `Vector3(0.0, 0.0, -1.0)` A gravitáció meghatározásakor ne csak a modellt vegye figyelembe, hanem azt a tájolást is, amelyben a modell látható lesz a futtatókörnyezetben. Ha egy olyan széket próbál felderíteni a valós világban, amely lapos felületen van, a gravitáció lehet `Vector3(0.0, 0.0, -1.0)` . Ha azonban a szék 45 fokos lejtőn van, a gravitáció lehet `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)` .

A gravitáció iránya egy 3D megjelenítési eszközzel, például a [MeshLab](http://www.meshlab.net/)is indokolható.

Az egység a modell mértékegységét jelöli. A támogatott egységek a **Microsoft. Azure. ObjectAnchors. betöltés. Unit** enumerálás használatával találhatók.

**K: mennyi ideig tart a CAD-modell betöltése?**

**A:** Egy `ply` modell esetében, amely általában 3-15 percet vesz igénybe. Ha más formátumokban küldi el a modelleket, a fájlmérettől függően várjon 15-60 percet.

**K: milyen eszközök támogatják az objektum-horgonyokat?**

**A:** 2. HoloLens 

**K: melyik operációsrendszer-buildnek kell futnia a HoloLens?**

**A:** Az operációs rendszer 18363,720-es vagy újabb verziója a 2020. március 12. után jelent meg.

  További részletek a [Windows 10 március 12-én 2020 frissítés](https://support.microsoft.com/help/4551762).

**K: mennyi ideig tart a HoloLens lévő objektumok észlelése?**

**A:** Ez az objektum méretétől és a beolvasási folyamattól függ. A gyorsabb észleléshez próbálkozzon az ajánlott eljárások követésével egy alapos vizsgálathoz. Az egyes dimenziókban 2 méteren belüli kisebb objektumok esetében az észlelés néhány másodpercen belül megtörténhet. Nagyobb objektumokhoz, például autóhoz a felhasználónak egy teljes hurkot kell sétálnia az objektum körül, hogy megbízható észlelést kapjon, ami azt jelenti, hogy az észlelés több tízezer másodpercig is eltarthat.

**K: Mi az ajánlott eljárás az objektum-horgonyok HoloLens-alkalmazásokban való használata során?**

**Egy**

 1. A szem kalibrálásával pontos renderelést érhet el.
 2. Győződjön meg arról, hogy a helyiség gazdag vizuális textúrával és jó világítással rendelkezik.
 3. Ha lehetséges, tartsa a zűrzavart az objektumon.
 4. Szükség esetén törölje a [térbeli leképezési](https://docs.microsoft.com/windows/mixed-reality/spatial-mapping) gyorsítótárat a HoloLens-eszközön.
 5. Vizsgálja meg az objektumot a körülötte lévő lépésekkel. Győződjön meg arról, hogy a legtöbb objektum megfigyelhető.
 6. Az objektum lefedéséhez elég nagy méretű keresőmezőt kell beállítania.
 7. Az objektumnak az észlelés során a helynek kell maradnia.
 8. Indítsa el az objektumok észlelését, és jelenítse meg a megjelenítést a becsült érték alapján.
 9. Zárolt objektum zárolása vagy a követés leállítása, ha a póz stabil és pontos az akkumulátorok élettartamának megőrzése érdekében.

**K: milyen pontosságot jelent a becslés?**

**A:** Ez az objektum méretétől, az anyagtól, a környezettől stb. függ. Kisméretű objektumok esetében a becsült érték 2 cm-es hibán belül lehet. A nagyméretű objektumok, például az autók esetében a hiba akár 2-8 cm is lehet.

**K: az Object-horgonyok képesek a mozgó objektumok kezelésére?**

**A:** **Folyamatosan mozgó** vagy **dinamikus** objektumokat nem támogatunk.

**K: az objektum-horgonyok kezelik a deformáció vagy a tagolást?**

**A:** Részben, attól függően, hogy mennyi objektum vagy geometria változik a deformáció vagy a kialakítás miatt. Ha az objektum geometriája sok változást mutat, a felhasználó létrehozhat egy másik modellt az adott konfigurációhoz, és felhasználhatja azt észleléshez.

**K: hány különböző objektum képes a horgonyok egyidejű észlelésére?**

**A:** Jelenleg egyszerre csak egyetlen objektummodell észlelését támogatja. 

**K: lehet, hogy az Object-horgonyok több példányban is észlelhetők az adott objektummodell esetében?**

**A:** Igen, az azonos típusú modellben legfeljebb 3 objektum észlelhető. Az alkalmazás többször is meghívhatja a `ObjectObserver.DetectAsync` különböző lekérdezéseket az ugyanazon modell több példányának észlelésére.

**K: mit tegyek, ha az objektum-horgonyok futásidejű funkciója nem ismeri fel a saját objektumot?**

**Egy**

* Néhány plakát hozzáadásával győződjön meg arról, hogy a helyiség elegendő textúrákkal rendelkezik.
* Az objektum teljes vizsgálata.
* Módosítsa a modell paramétereit a fent leírtak szerint.
* Adjon meg egy szűk határvonalat olyan keresési területként, amely tartalmazza az objektum összes vagy többségét.
* Törölje a térbeli leképezési gyorsítótárat, és ellenőrizze újra az objektumot.
* Rögzítse a diagnosztikát, és küldje el nekünk az adatait.

**K: válassza ki az objektum lekérdezési paramétereit?**

**Egy**

* Biztosítson szűk keresési területeket, hogy ideálisan fedezzék a teljes objektumot az észlelés sebességének és pontosságának javítása érdekében.
* Az `ObjectQuery.MinSurfaceCoverage` objektummodell alapértelmezett értéke általában jó, ellenkező esetben kisebb értéket kell használnia a gyorsabb észleléshez.
* Kis értékű érték használata, `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` Ha az objektumnak megfelelőnek kell lennie.
* Az alkalmazásoknak mindig egy objektummodell használatát kell használniuk `1:1` az észleléshez. A becsült skálának 1%-os hibán belül ideálisnak kell lennie. Egy alkalmazás beállítható `ObjectQuery.MaxScaleChange` úgy, hogy `0` `0.1` letiltsa vagy engedélyezze a méretezési becslést, és minőségileg értékelje ki a példányt.

**K: Hogyan lekérési objektum a HoloLens diagnosztikai adatait rögzíti?**

**A:** Az alkalmazás megadhatja a diagnosztikai archívumok helyét. Az objektum a **TempState** mappába írja a minta alkalmazás által írt diagnosztikát.

**K: Miért nem igazodik a forrás modell a fizikai objektumhoz az objektum által visszaadott, az Unity SDK-val való használatakor?**

**A:** Az egység az objektummodell importálásakor megváltoztathatja a koordináta-rendszerét. Például az objektum-horgonyok Unity SDK invertálja a Z tengelyt, amikor a jobbról a bal oldali koordináta-rendszerre konvertál, de az egység további rotációt alkalmazhat az X vagy az Y tengelyen. A fejlesztők a koordináta-rendszerek megjelenítésével és összehasonlításával határozhatják meg ezt a további rotációt.

**K: támogatja a 2D-t?**

**A:** Mivel a geometrián alapulnak, csak a 3D-t támogatjuk.

**K: különböző színekben is megkülönböztetni ugyanazt a modellt?**

**A:** Mivel az algoritmusok geometrián alapulnak, az azonos modell különböző színei nem fognak másképpen viselkedni az észlelés során.

**K: használhatok az objektum-horgonyokat internetkapcsolat nélkül?**

**Egy** 
* A modell betöltéséhez és képzéséhez szükség van a kapcsolódásra, mivel ez a felhőben történik.
* A futtatókörnyezeti munkamenetek teljes mértékben az eszközön vannak, és nincs szükség kapcsolatra, mivel az összes számítás a 2. HoloLens történik.

## <a name="privacy-faq"></a>Adatvédelem – gyakori kérdések
**K: hogyan tárolja az Azure Object az adattárolást?**

**A:** A rendszer csak a Microsoft által felügyelt adattitkosítási kulccsal tárolt rendszermetaadatokat tárolja.
