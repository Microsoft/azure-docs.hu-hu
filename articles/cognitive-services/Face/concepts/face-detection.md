---
title: Arcfelismerés és attribútumok – fogalmak
titleSuffix: Azure Cognitive Services
description: Az Arcfelismerés az a művelet, amellyel az emberi arcok megkereshetők egy képben, és opcionálisan különböző típusú adatokkal térnek vissza.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 68d1e9744d937cf80327c3f41cc69f4af97d3400
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600180"
---
# <a name="face-detection-and-attributes"></a>Arcfelismerés és attribútumok

Ez a cikk az arcfelismerés és a Face attribútum-adatelemzési fogalmakat ismerteti. Az Arcfelismerés az a művelet, amellyel az emberi arcok megkereshetők egy képben, és opcionálisan különböző típusú adatokkal térnek vissza.

Az [Arcfelismerés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) művelettel azonosíthatja a képekben lévő arcokat. Minden észlelt arc legalább egy faceRectangle-mezőnek felel meg a válaszban. Ez a képpontok koordinátái a bal, a felső, a szélesség és a magasság megjelölése mellett láthatók. A koordináták használatával lekérheti az arc helyét és méretét. Az API-válaszban az arcok a legnagyobb és a legkisebb közötti méret sorrend szerint vannak felsorolva.

## <a name="face-id"></a>Arcazonosító

A Face azonosító egy egyedi azonosító sztring minden észlelt képpel kapcsolatban. Az arc [-észlelési API-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) hívásban kérheti a Face ID-t.

## <a name="face-landmarks"></a>Arcrészek

Az arc tereptárgyak könnyen megtalált pontok, például a tanulók és az orr hegye. Alapértelmezés szerint 27 előre meghatározott arcpont van. Az alábbi ábrán az összes 27 pont látható:

![Egy, az összes 27 tereptárgy címkével ellátott arc diagram](../Images/landmarks.1.jpg)

A pontok koordinátáit képpontban adja vissza.

## <a name="attributes"></a>Attribútumok

Az attribútumok olyan funkciók összessége, amelyek a [Face-Detect API-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) val opcionálisan észlelhetők. A következő attribútumok észlelhetők:

* **Életkor**. Egy adott arc becsült kora (év).
* **Életlenítés**. A képen látható arc homályos. Ez az attribútum a nulla és az egy érték közötti értéket adja vissza, valamint az alacsony, közepes vagy magas informális minősítést.
* **Érzelem**. Az adott arc észlelési megbízhatóságával kapcsolatos érzelmek listája. A megbízhatósági pontszámok normalizálva vannak, és az összes érzelem pontszáma akár eggyel is kiegészíthető. A visszaadott érzelmek a boldogság, a szomorúság, a semleges, a düh, a megvetés, az undor, a meglepetés és a félelem.
* **Expozíció**. A képen látható arc expozíciója. Ez az attribútum a nulla és az egy érték közötti értéket adja vissza, valamint az expozíció, a goodExposure vagy a túlzott expozíció informális minősítését.
* **Arc szőr**. A becsült arc-haj és az adott arc hossza.
* Nem. Az adott arc becsült neme. A lehetséges értékek a következők: férfi, női és nemi.
* **Szemüveg**. Azt jelzi, hogy a megadott arc szemüveggel rendelkezik-e. A lehetséges értékek: nem üvegek, ReadingGlasses, napszemüvegek és úszási szemüvegek.
* **Haj**. Az arc szőrének típusa Ez az attribútum azt mutatja, hogy látható-e a haj, hogy a rendszer a kopaszodás észlelését és a haj színét észleli-e.
* **Head**. Az arc tájolása 3D térben. Ezt az attribútumot a Pitch, a roll és a kitérés szögek írják le fokban. Az érték tartománya-90 fok, 90 fok,-90 fok és 90 fok, illetve – 90 fok – 90 fok. Tekintse meg a következő diagramot a szög-hozzárendelésekhez:

    ![A Pitch, a roll és a kitérés tengelysel jelölt fej](../Images/headpose.1.jpg)
* **Smink**. Azt jelzi, hogy az arc rendelkezik-e sminktel. Ez az attribútum a eyeMakeup és a lipMakeup logikai értékét adja vissza.
* **Zaj**. Az arcképben észlelt vizuális zaj Ez az attribútum a nulla és az egy érték közötti értéket adja vissza, valamint az alacsony, közepes vagy magas informális minősítést.
* **Elzáródás**. Azt határozza meg, hogy vannak-e olyan objektumok, amelyek blokkolja az arc részeit. Ez az attribútum a eyeOccluded, a foreheadOccluded és a mouthOccluded logikai értékét adja vissza.
* **Mosoly**. Az adott arc mosoly kifejezése Ez az érték nulla, és nem mosolyog, egyet pedig tiszta mosolyra.

> [!IMPORTANT]
> A Face attribútumok a statisztikai algoritmusok használatával lesznek előre jelezve. Előfordulhat, hogy nem mindig pontosak. Körültekintően járjon el, ha az attribútum adatain alapuló döntéseket tesz.

## <a name="input-data"></a>Bemeneti adatok

A következő tippek segítségével győződjön meg arról, hogy a bemeneti képek a legpontosabb észlelési eredményeket adják meg:

* A támogatott bemeneti képformátumok a következők: JPEG, PNG, GIF az első képkockához és a BMP-hez.
* A képfájl mérete nem lehet hosszabb 6 MB-nál.
* Az észlelhető minimális méret 36 x 36 képpont egy olyan képen, amely nem nagyobb, mint 1920 x 1080 képpont. A 1920 x 1080 képpontnál nagyobb méretű képeknél a minimális méret arányosan nagyobb. Az arc méretének csökkentése miatt előfordulhat, hogy egyes arcok nem észlelhetők, még akkor is, ha azok nagyobbak, mint a észlelt minimális méret.
* A észlelhető maximális méret 4096 x 4096 képpont.
* A 36 x 36 és 4096 x 4096 képpont tartományon kívüli arcok nem észlelhetők.
* Előfordulhat, hogy a technikai kihívások miatt egyes arcok nem észlelhetők. A szélsőséges Face Angles (Head póz) vagy az arc elzáródás (objektumok, például napszemüvegek vagy az arc részét letiltó kezek) befolyásolhatják az észlelést. Az elülső és a közel-elülső arcok a legjobb eredményeket adják.

Ha egy videó-hírcsatornából észlel arcokat, akkor a videó kamerájának bizonyos beállításainak módosításával növelheti a teljesítményt:

* **Simítás**: számos videokamera alkalmaz simítási hatást. Ezt ki kell kapcsolni, ha azért van, mert a keretek között elmosódottan jön létre, és csökkenti az érthetőséget.
* **Zársebesség**: a gyorsabb zársebesség csökkenti a keretek közötti mozgás mértékét, és az egyes képkockák tisztább méretét teszi lehetővé. Javasoljuk, hogy a zársebesség 1/60 másodperc vagy annál gyorsabb legyen.
* **Redőny szöge**: egyes kamerák a zársebesség helyett a zársebességet határozzák meg. Ha lehetséges, használjon alacsonyabb zársebesség-látószöget. Ez tisztább képkockákat eredményez.

    >[!NOTE]
    > Az alsó zársebességtel rendelkező kamera kevesebb fényt fog kapni az egyes keretekben, így a kép sötétebb lesz. Meg kell határoznia a használandó megfelelő szintet.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri az Arcfelismerés fogalmait, megtudhatja, hogyan írhat olyan parancsfájlt, amely észleli az arcokat egy adott képen.

* [Arcok felismerése a képeken](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
