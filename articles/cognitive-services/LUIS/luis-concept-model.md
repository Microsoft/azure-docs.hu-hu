---
title: Tervezés modellekből – LUIS
description: A Language Understanding számos típusú modellt biztosít. Egyes modellek több módon is használhatók.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316511"
---
# <a name="design-with-intent-and-entity-models"></a>Tervezés a szándék és az entitás modelljeivel

A Language Understanding két típusú modellt biztosít az alkalmazás sémájának definiálásához. Az alkalmazás sémája határozza meg, hogy az új felhasználói kifejezés előrejelzése milyen adatokat kapjon.

Az alkalmazás sémája a [Machine Teaching](#authoring-uses-machine-teaching)használatával létrehozott modellekből épül fel:
* A felhasználók [hosszúságú kimondott szöveg osztályozása](#intents-classify-utterances)
* Az [entitásokból](#entities-extract-data) kinyert adatok kinyerése

## <a name="authoring-uses-machine-teaching"></a>A szerző gépi tanítást használ

A LUIS gépi tanítási módszere lehetővé teszi, hogy könnyedén tanítson fogalmakat a gépre. A _Machine learning_ megismerése nem szükséges a Luis használatához. Ehelyett a tanárként egy koncepciót küld a LUIS-nek a koncepció példáinak megadásával, és ismerteti, hogyan kell modellezni egy koncepciót más kapcsolódó fogalmak használatával. A tanárként az előrejelzési hibák azonosításával és javításával interaktív módon is javíthatja a LUIS modelljét.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>A szándékok beszédelemeket sorolnak be

A szándék egy példaként osztályozza a hosszúságú kimondott szöveg, hogy megtanítsa LUISt a szándékról. Egy szándékon belüli hosszúságú kimondott szöveg például pozitív példaként szolgál a kiértékeléshez. Ugyanezek a hosszúságú kimondott szöveg az összes többi szándékban negatív példákként használatosak.

Vegyünk egy olyan alkalmazást, amelynek meg kell határoznia a felhasználó azon szándékát, hogy egy könyvet és egy olyan alkalmazást rendeljen, amelynek szüksége van az ügyfél szállítási címeként. Az alkalmazásnak két célja van: `OrderBook` és `ShippingLocation` .

A következő Kimondás **pozitív példa** a `OrderBook` szándékra és **negatív példára** a `ShippingLocation` és a `None` szándékhoz:

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>Az entitások adatokat nyernek ki

Az entitás a kinyert adatok adategységét jelöli. A gépi tanulási entitások olyan legfelső szintű entitások, amelyek alentitásokat tartalmaznak, amelyek szintén gépi tanulási entitások.

Egy gépi tanulási entitás például egy repülőjegy rendelése. Ez egy tranzakció, amely sok kisebb adategységgel rendelkezik, mint például a dátum, az idő, az ülőhelyek száma, az ülés típusa, például az első osztály vagy az edző, a forrás helye, a célhely és az étkezési lehetőség.

## <a name="intents-versus-entities"></a>Leképezések versus entitások

A teljes kiértékelés kívánt eredménye az, hogy az entitások a _teljességből_ kinyert adatok részleteit jelentik. A leképezések általában olyan műveletekhez vannak kötve, amelyekhez az ügyfélalkalmazás szükséges. Az entitások a művelet végrehajtásához szükséges információk. Programozási szempontból a szándék egy metódus hívását indítja el, és az entitások paraméterként lesznek használva a metódus hívásakor.

Ennek a Kimondás szándékának _meg kell felelnie_ , és _rendelkezhet_ entitásokkal:

`Buy an airline ticket from Seattle to Cairo`

Ez a Kimondás egyetlen szándékkal rendelkezik:

* Repülőjegy vásárlása

A Kimondás több entitással _is_ rendelkezhet:

* Seattle (Origin) és Kairó (rendeltetés) helyei
* Egyetlen jegy mennyisége

## <a name="entity-model-decomposition"></a>Entitás-modell elbomlása

A LUIS támogatja a _modellek dekompozícióját_ a szerzői API-kkal, és a koncepciót kisebb részekre bontja. Ez lehetővé teszi, hogy a modelleket a különböző részek kiépítésének és előrejelzésének módjával bízza.

A modell elbomlása a következő részekből áll:

* [leképezések](#intents-classify-utterances)
    * [szolgáltatások](#features)
* [gépi tanulással rendelkező entitások](reference-entity-machine-learned-entity.md)
    * alentitások (a gépi tanulási entitások is)
        * [szolgáltatások](#features)
            * [kifejezések listája](luis-concept-feature.md)
            * [nem gépi tanulási entitások](luis-concept-feature.md) , például [reguláris kifejezések](reference-entity-regular-expression.md), [felsorolások](reference-entity-list.md)és [előre összeépített entitások](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>Funkciók

A [szolgáltatás](luis-concept-feature.md) a rendszer által megfigyelt adattípusok vagy adatattribútumok megkülönböztető tulajdonságai. A Machine learning-funkciók lehetővé teszik a LUIS számára, hogy hol találják meg a koncepciót megkülönböztető dolgokat. Arra utalnak, hogy LUIS használható, de nem rögzített szabályok. Ezeket a mutatókat a címkékkel együtt használva keresheti meg az adatgyűjtést.

## <a name="patterns"></a>Minták

A [minták](luis-concept-patterns.md) úgy vannak kialakítva, hogy javítsák a pontosságot, ha több hosszúságú kimondott szöveg nagyon hasonló. A minta lehetővé teszi, hogy nagyobb pontosságot szerezzen egy szándékhoz anélkül, hogy sok további hosszúságú kimondott szöveg lenne.

## <a name="extending-the-app-at-runtime"></a>Az alkalmazás kiterjesztése futásidőben

Az alkalmazás sémája (modelljei és funkciói) ki van tanítva, és közzé van téve az előrejelzési végponton. Az előrejelzési végponthoz [új információkat](schema-change-prediction-runtime.md)adhat meg, valamint a felhasználó teljes kifejezését is.

## <a name="next-steps"></a>Következő lépések

* A [szándékok](luis-concept-intent.md) és az [entitások](luis-concept-entity-types.md)ismertetése.
* További információ a [funkciókról](luis-concept-feature.md)