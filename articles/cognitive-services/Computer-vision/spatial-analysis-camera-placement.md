---
title: Térbeli elemzési kamera elhelyezése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan állíthat be egy kamerát térbeli elemzéssel való használatra
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 4c2d1cd1c73b377d85501fd31d0f5a1893df5183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102487423"
---
# <a name="camera-placement-guide"></a>Kamera elhelyezése – útmutató

Ez a cikk a térbeli elemzéshez (nyilvános előzetes verzió) készült kamera elhelyezésére vonatkozó javaslatokat tartalmaz. Általános irányelveket is tartalmaz, valamint konkrét javaslatokat nyújt az összes befoglalt művelethez a magasságra, a szögre, valamint a kamera és a középpont közötti távolságra. 

> [!NOTE]
> Ez az útmutató a tengely M3045-V kamerához készült. Ez a kamera a felbontás 1920 × 1080, a 106 fokos vízszintes látómező, a 59 fokos függőleges látómező és a rögzített 2.8 mm gyújtótávolság használatát fogja használni. Az alábbi alapelvek minden kamerára érvényesek, de a kamera magasságára és a kamera és a fókusz közötti távolságra vonatkozó konkrét irányelveket más kamerákkal való használathoz kell beállítani. 

## <a name="general-guidelines"></a>Általános irányelvek

Vegye figyelembe a következő általános irányelveket a térbeli elemzéshez használt kamerák esetében:

* **Megvilágítás magassága** A kamerákat a lámpatestek alatt helyezheti el, így a lámpatestek nem blokkolják a kamerákat.
* **Akadály.** A kamera-nézetek akadályozásának elkerülése érdekében jegyezze fel az akadályokat, például a lengyelek, a jelzések, a polcok, a falak és a meglévő LP-kamerák használatát.
* **Környezeti háttérvilágítás.** A kültéri háttérvilágítás hatással van a kamera képminőségére. A súlyos háttérvilágítási feltételek elkerülése érdekében ne irányítsa a kamerákat a külső Windows-és üvegajtó-ajtókra.
* **Helyi adatvédelmi szabályok és rendeletek.** A helyi szabályozások korlátozhatják, hogy milyen kamerákat rögzíthetnek. A kamerák elhelyezése előtt győződjön meg arról, hogy tisztában van a helyi szabályokkal és szabályozásokkal.
* **Építési struktúra.** A HVAC, a szórófejek és a meglévő vezetékek a kamerák nehéz csatlakoztatását korlátozhatják.
* **Kábelek kezelése.** Győződjön meg arról, hogy a tervezett kamera-csatlakoztatási helyekről a Power over Internet (PoE) kapcsolóra irányítja az Ethernet-kábelt.

## <a name="height-focal-point-distance-and-angle"></a>Magasság, fókuszponti távolság és szög

A következő három dolgot kell figyelembe vennie, amikor eldönti, hogyan telepítsen egy kamerát a térbeli elemzéshez:
- Kamera magassága
- Kameráról a fókuszra mutató távolság
- A kamera szöge a padló síkjával viszonyítva

Azt is fontos tudni, hogy az emberek többsége (a fő gyaloglás iránya) a kamera mezőhöz képest, ha lehetséges. Ez az irány a rendszer teljesítményének szempontjából fontos.

![Egy irányba besétáló személy képe](./media/spatial-analysis/person-walking-direction.png)

A következő ábrán a személy gyaloglás irányának emelési nézete látható.

![Jogosultságszint-emelési és-tervezési nézet](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>Kamera magassága

Általában a kamerákat a területről 12-14 méterre kell csatlakoztatni. A maszk észleléséhez azt javasoljuk, hogy a kamerákat a helyről 8-12 méterre kell csatlakoztatni. Ha megtervezi a kamera csatlakoztatását ebben a tartományban, érdemes megfontolnia az akadályozó tényezőket (például a polcokat, a függő lámpákat, a függő aláírásokat és a megjelenített értékeket), amelyek hatással lehetnek a kamera nézetre, majd szükség szerint módosíthatja a magasságot.

## <a name="camera-to-focal-point-distance"></a>Kameráról a fókuszra mutató távolság

A _kamera és a fókusztávolság közötti távolság a középponton_ mért kamera (vagy a kamera-rendszerkép középpontja) közötti lineáris távolság.

![Kamera és gyújtópont közötti távolság](./media/spatial-analysis/camera-focal-point.png)

Ez a távolság a padló síkja alapján mérhető.

![A kamera és a fókusz közötti távolság mérése a padlóról](./media/spatial-analysis/camera-focal-point-floor-plane.png)

A fentiek alapján a következőképpen néz ki:

![A kamera és a fókusz közötti távolság mérése](./media/spatial-analysis/camera-focal-point-above.png)

Az alábbi táblázat segítségével meghatározhatja a kamera távolságát a fókuszponttól az adott csatlakoztatási magasságok alapján. Ezek a távolságok optimális elhelyezést biztosítanak. Vegye figyelembe, hogy a táblázat a 12 "-14" ajánlás alatt nyújt útmutatást, mivel egyes felső határértékek korlátozhatják a magasságot. A maszk észlelése esetén az ajánlott kamerától a fókusztávolságig terjedő távolság (min/max) 4 "-10" a kamera magassága 8 és 12 között.

| Kamera magassága | Kamerától a fókusztávolságig terjedő távolság (min/max) |  
| ------------- | ---------------------------------------- |  
| 8            | 4.6 '-8 '                                  |  
| 10           | 5.8 – 10                                 |  
| 12           | 7 – 12                                   |  
| 14           | 8 '-14 ' '                                  |  
| 16           | 9.2 – 16                                 |  
| 20           | 11,5-20                                |  

A következő ábra a kamera nézeteit szimulálja a legközelebb és legtávolabbi kameráról a fókuszra mutató távolságokra.

| Legközelebbi                                      | Szélső                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![Legközelebb a kamera és a fókusz közötti távolság](./media/spatial-analysis/focal-point-closest.png) | ![Legtávolabbi kamerától a gyújtópontig terjedő távolság](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>Kamera szögének csatlakoztatási tartományai

Ez a szakasz az elfogadható kamera szögének csatlakoztatási tartományait ismerteti. Ezek a csatlakoztatási tartományok az optimális elhelyezéshez elfogadható tartományt mutatják.

### <a name="line-configuration"></a>Vonal konfigurációja

A **cognitiveservices. vízió. spatialanalysis-personcrossingline** művelet esetében a +/-5 ° az optimális kamera-csatlakoztatási szög a pontosság maximalizálása érdekében.

Az álarcok észleléséhez a +/-30 fok a kamera magasságának optimális kamera-csatlakoztatási szöge 8 és 12 között.

Az alábbi ábrán a bal szélső (-) és a jobb oldali (+) csatlakozási szöggel kapcsolatos javaslatok láthatók a **cognitiveservices. vízió. spatialanalysis-personcrossingline** használatával a bejárati számláláshoz.

| Bal szélső nézet                | Jobb szélső nézet                |  
| ---------------------------- | ----------------------------- |  
| ![Bal szélső kamera szöge](./media/spatial-analysis/camera-angle-left.png) | ![Jobb oldali kamera szöge](./media/spatial-analysis/camera-angle-right.png) |  

Az alábbi ábrán a kamera elhelyezését és a csatlakoztatási szögek láthatók a madárvédelmi nézetből.

![Madártávlati nézet](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>Zóna konfigurációja

Javasoljuk, hogy a kamerákat a terepen legalább 10 méterre helyezze, hogy a kezelt terület elég nagy legyen. 

Ha a zóna egy olyan akadály mellett van, mint a fal vagy a polc, a megadott távolságban lévő kamerákat az elfogadható 120 fokos szög tartományán belül, az alábbi ábrán látható módon csatlakoztathatja.

![Elfogadható kamera szöge](./media/spatial-analysis/camera-angle-acceptable.png)

A következő ábra a polcok melletti terület bal és jobb oldali kamerás nézeteinek szimulációit tartalmazza.

| Bal oldali nézet        | Jobb oldali nézet        |  
| ---------------- | ----------------- |  
| ![Bal oldali nézet](./media/spatial-analysis/end-cap-left.png) | ![Jobb oldali nézet](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>Üzenetsorok

A **cognitiveservices. vízió. spatialanalysis-personcount**, **cognitiveservices. vízió. spatialanalysis-persondistance** és **cognitiveservices. vízió. spatialanalysis-personcrossingpolygon** készségek használhatók a várólisták figyelésére. Az optimális üzenetsor-adatminőség érdekében a visszaállítható biztonsági öv korlátai előnyben részesítik a sorban lévő személyek elzáródásának minimalizálását, és a várólisták helyének időbeli egységességét.

![Visszavonható öv üzenetsor](./media/spatial-analysis/retractable-belt-queue.png)

Az ilyen típusú akadályok előnyben részesítettek a várólista-kialakulás során, így maximalizálható a rendszerből származó adatok pontossága.

A várólistáknak két típusa van: lineáris és Zig-Zag.

A következő ábrán a lineáris várólistákra vonatkozó javaslatok láthatók:

![Lineáris üzenetsor-javaslat](./media/spatial-analysis/camera-angle-linear-queue.png)

A következő ábrán a lineáris várólisták bal és jobb oldali kamera-nézeteinek szimulációi láthatók. Vegye figyelembe, hogy csatlakoztathatja a kamerát a várólista túlsó oldalára.

| Bal oldali nézet                          | Jobb oldali nézet                          |  
| ---------------------------------- | ----------------------------------- |  
| ![Lineáris várólista bal oldali szöge](./media/spatial-analysis/camera-angle-linear-left.png) | ![Derékszög a lineáris sorhoz](./media/spatial-analysis/camera-angle-linear-right.png) |  

A Zig-Zag várólisták esetében a legjobb megoldás, ha a kamerát a következő ábrán látható módon közvetlenül a sorban lévő vonal irányába helyezi el. Vegye figyelembe, hogy az ábrán látható négy példában szereplő kamera ideális nézetet biztosít, amely minden irányban +/-15 fokos elfogadható eltéréssel rendelkezik.

A következő ábrák szimulálják a nézetet egy, a Zig-Zag várólista ideális helyein elhelyezett kamerából.

| 1. nézet        | 2. nézet        |  
| ------------- | ------------- |  
| ![1. nézet](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![2. nézet](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| 3. nézet |  4. nézet |  
| ---- | ----  |
| ![3. nézet](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![4. nézet](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>Szerves várólisták

A szerves üzenetsor-sorok szerves módon jelennek meg. Ez a várólista akkor fogadható el, ha a várólisták nem a 2-3 személynél és a zóna definíciójában lévő vonal űrlapokon kívül esnek. Ha a várólista hossza általában több, mint 2-3 ember, javasoljuk, hogy egy visszavonható biztonsági övet használjon a várólista irányának irányításához, és gondoskodjon arról, hogy a zóna-definíción belül legyenek a vonal űrlapok.

## <a name="next-steps"></a>Következő lépések

* [Felhasználók üzembe helyezése webes alkalmazásokban](spatial-analysis-web-app.md)
* [Térbeli elemzési műveletek konfigurálása](./spatial-analysis-operations.md)
* [Naplózás és hibaelhárítás](spatial-analysis-logging.md)
* [A zóna és a vonal elhelyezési útmutatója](spatial-analysis-zone-line-placement.md)
