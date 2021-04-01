---
title: Előrejelzési pontszámok – LUIS
description: Az előrejelzési pontszám azt jelzi, hogy a LUIS API szolgáltatásnak milyen megbízhatósági foka van az előrejelzési eredményekhez a felhasználók teljes száma alapján.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d836273e61752ff208133466016ce7c6ff9c28fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316460"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Az előrejelzési pontszámok előrejelzési pontosságot jeleznek a szándék és az entitások számára

Az előrejelzési pontszám azt jelzi, hogy a LUIS megbízhatósági foka a felhasználók teljes számának előrejelzési eredményeire utal.

Az előrejelzési pontszám nulla (0) és egy (1) között van. Egy példa arra, hogy egy nagyon magabiztos LUIS-pontszám 0,99. Az alacsony megbízhatósági pontszám például 0,01.

|Pontszám értéke|Megbízhatóság|
|--|--|
|1|határozott egyezés|
|0,99|magas megbízhatóság|
|0,01|alacsony megbízhatóság|
|0|nem megfelelőnek határozott hiba|

## <a name="top-scoring-intent"></a>Top-pontozási szándék

Minden Kimondás becslése egy top-pontozási szándékot ad vissza. Ez az előrejelzés az előrejelzési pontszámok számszerű összehasonlítását.

## <a name="proximity-of-scores-to-each-other"></a>A pontszámok közelsége egymáshoz

A 2 legfontosabb pontszámok között nagyon kis különbség lehet. LUIS nem jelzi ezt a közelséget, mint a felső pontszámot.

## <a name="return-prediction-score-for-all-intents"></a>Az összes cél visszaadott előrejelzési pontszáma

Egy tesztelési vagy végponti eredmény az összes leképezést tartalmazhatja. Ez a konfiguráció a végponton a megfelelő querystring név/érték párok használatával van beállítva.

|Prediction API|QueryString neve|
|--|--|
|V3|`show-all-intents=true`|
|2. verzió|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Hasonló pontszámokkal rendelkező leképezések áttekintése

Az összes cél pontszámának áttekintése jó módszer annak ellenőrzésére, hogy nem csak a megfelelő szándék van-e azonosítva, de a következő azonosított szándékok pontszáma jelentősen és következetesen alacsonyabb a hosszúságú kimondott szöveg.

Ha több cél is van, a Kimondás kontextusa alapján az előrejelzési pontszámok is megváltozhatnak a leképezések között. A probléma megoldásához folytassa a hosszúságú kimondott szöveg hozzáadásával az egyes szándékokhoz a kontextusbeli különbségek széles skáláját, vagy megadhatja, hogy az ügyfélalkalmazás, például a csevegési robot, hogyan kezelje a 2 legfontosabb szándékot.

A túlságosan hasonló pontszámmal rendelkező 2 szándék felcserélődhet a **nem determinisztikus betanítás** következtében. A legfelső pontszámot lehet a második csúcs, a második pedig az első legfelső pontszámot. Az ilyen helyzetek megelőzése érdekében vegyen fel például hosszúságú kimondott szöveg az első két cél elé, hogy a szó kiválasztásával és kontextusával a 2 szándékot megkülönböztetse. A két szándéknak azonos számú példa hosszúságú kimondott szöveg kell lennie. A leválasztásra szolgáló szabály, amely megakadályozza a betanítást, 15%-os különbség a pontszámokban.

Kikapcsolhatja a **nem determinisztikus képzést** az [összes adattal betanítva](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>A különböző képzési munkamenetek közötti előrejelzések közötti különbségek

Ha egy másik alkalmazásban ugyanazt a modellt tanítja, és a pontszámok nem azonosak, ez a különbség azért van, mert nem determinisztikus (véletlenszerű) **képzésre** van szüksége. Másodszor, hogy a Kimondás bármely átfedése egynél több szándékot jelent, az azonos Kimondás alapjául szolgáló cél a képzés alapján változhat.

Ha a csevegési robot egy konkrét LUIS-pontszámra van szüksége, hogy jelezze a megbízhatóságot egy szándékban, használja a pontszám különbségét az első két cél között. Ez a helyzet rugalmasságot biztosít a betanítás változásaihoz.

Kikapcsolhatja a **nem determinisztikus képzést** az [összes adattal betanítva](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>E (kitevő) jelölés

Az előrejelzési pontszámok használhatnak kitevői jelölést, amely az 0-1 tartomány fölött _jelenik_ meg, például: `9.910309E-07` . Ez a pontszám nagyon **kis** számot jelez.

|E jelölési pontszám |Tényleges pontszám|
|--|--|
|9.910309 e-07|.0000009910309|

<a name="punctuation"></a>

## <a name="application-settings"></a>Alkalmazásbeállítások

Az [Alkalmazásbeállítások](luis-reference-application-settings.md) segítségével szabályozhatja, hogyan befolyásolják a Mellékjelek és a központozás az előrejelzések pontszámait.

## <a name="next-steps"></a>Következő lépések

Az entitások LUIS-alkalmazáshoz való hozzáadásával kapcsolatos további tudnivalókért tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md) című témakört.
