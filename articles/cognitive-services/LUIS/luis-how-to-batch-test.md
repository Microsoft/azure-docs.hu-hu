---
title: Batch-teszt elvégzése – LUIS
titleSuffix: Azure Cognitive Services
description: Használjon Language Understanding (LUIS) batch-tesztelési készleteket a helytelen szándékokkal és entitásokkal rendelkező hosszúságú kimondott szöveg kereséséhez.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/29/2020
ms.openlocfilehash: b297330f3562babf9e83d36934827f7b92d5ea35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787012"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batch-tesztelés példaként megadott hosszúságú kimondott szöveg

A Batch-tesztelés ellenőrzi, hogy aktív-e a betanított verzió, és megméri az előrejelzés pontosságát. A Batch-tesztek segítségével megtekintheti az egyes szándékok és entitások pontosságát az aktív verzióban. Tekintse át a Batch-teszt eredményeit, hogy megfelelő lépéseket tegyen a pontosság javítása érdekében, például ha az alkalmazás gyakran nem tudja azonosítani a megfelelő szándékot vagy címkéző entitásokat a hosszúságú kimondott szöveg.

## <a name="group-data-for-batch-test"></a>A Batch-teszthez tartozó adatcsoportosítási szolgáltatás

Fontos, hogy a Batch-teszteléshez használt hosszúságú kimondott szöveg Újdonságok legyenek a LUIS-ben. Ha rendelkezik hosszúságú kimondott szöveg, ossza fel a hosszúságú kimondott szöveg három csoportba: példa a hosszúságú kimondott szöveg hozzáadására, a közzétett végponttól kapott hosszúságú kimondott szöveg, valamint a LUIS-teszt betanítása után használt hosszúságú kimondott szöveg.

A használt batch JSON-fájlnak tartalmaznia kell a hosszúságú kimondott szöveg és a legfelső szintű gépi tanulással rendelkező entitásokat, beleértve a kezdő és a záró pozíciót is. A hosszúságú kimondott szöveg nem lehet az alkalmazásban már szereplő példák része. Érdemes hosszúságú kimondott szöveg, ha pozitívan szeretné megjósolni a szándékot és az entitásokat.

Elkülönítheti a teszteket szándék és/vagy entitás alapján, vagy az összes tesztet (akár 1000 hosszúságú kimondott szöveg) ugyanabban a fájlban. 

### <a name="common-errors-importing-a-batch"></a>A kötegek importálása során előforduló gyakori hibák

Ha hibákba ütközik a Batch-fájlnak a LUIS-ba való feltöltésekor, ellenőrizze a következő gyakori problémákat:

* Több mint 1 000 hosszúságú kimondott szöveg egy batch-fájlban
* Olyan teljes JSON-objektum, amely nem rendelkezik entitások tulajdonsággal. A tulajdonság lehet üres tömb.
* Több entitásban címkézett szó (ek)
* Az entitások felirata egy szóközzel kezdődik vagy végződik.

## <a name="fixing-batch-errors"></a>Batch-hibák javítása

Ha hibák léptek fel a Batch-tesztelésben, hozzáadhat további hosszúságú kimondott szöveg egy szándékhoz, és/vagy megcímkézheti a további hosszúságú kimondott szöveg az entitással, hogy segítsen a LUIS számára a szándékok közötti diszkriminációban. Ha hozzáadta a hosszúságú kimondott szöveg-t, és címkézte őket, és továbbra is előrejelzési hibákat kap a Batch-tesztelésben, érdemes lehet egy, a tartományra jellemző szókincsgel kiegészíteni egy [kifejezés-lista](luis-concept-feature.md) szolgáltatást, amely segítséget nyújt a Luis számára.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Batch-tesztelés a LUIS portál használatával 

### <a name="import-and-train-an-example-app"></a>Egy példa alkalmazás importálása és betanítása

Importáljon egy olyan alkalmazást, amely pizzás sorrendet (például) vesz igénybe `1 pepperoni pizza on thin crust` .

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Válassza az **új alkalmazás** melletti nyilat, majd kattintson az **Importálás JSON-ként** lehetőségre a JSON új alkalmazásba való importálásához. Adja meg az alkalmazás nevét `Pizza app` .


1. A navigáció jobb felső sarkában válassza a **vonat** lehetőséget az alkalmazás betanításához.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Batch-teszt fájl

A JSON-példa tartalmaz egy címkével ellátott entitást, amely bemutatja, hogyan néz ki egy adott teszt fájl. A saját tesztek során sok hosszúságú kimondott szöveg kell rendelkeznie, és a megfelelő szándékú és gépi tanulási entitást kell megcímkézni.

1. Hozzon létre `pizza-with-machine-learned-entity-test.json` egy szövegszerkesztőben, vagy [töltse le](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) .

2. A JSON-formátumú batch-fájlban adjon hozzá egy, a tesztben előre jelezni kívánt **szándékot** .

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>A köteg futtatása

1. Válassza a **teszt** lehetőséget a felső navigációs sávon.

2. Válassza a **Batch-tesztelés panelt** a jobb oldali panelen.

    ![Batch-tesztelési hivatkozás](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Válassza az **Importálás** lehetőséget. A megjelenő párbeszédpanelen válassza a **fájl kiválasztása** elemet, és keresse meg a megfelelő JSON-formátumú JSON-fájlt, amely *nem tartalmaz több mint 1 000* hosszúságú kimondott szöveg a teszteléshez.

    Az importálási hibák jelentése a böngésző tetején található piros értesítési sávon történik. Ha egy importálás hibákat tartalmaz, a rendszer nem hoz létre adatkészletet. További információ: [gyakori hibák](#common-errors-importing-a-batch).

4. Válassza ki a fájl helyét `pizza-with-machine-learned-entity-test.json` .

5. Nevezze el az adatkészletet `pizza test` , majd válassza a **kész** lehetőséget.

6. Kattintson a **Futtatás** gombra. A Batch-teszt futtatása után válassza az **eredmények megtekintése** lehetőséget. 

    > [!TIP]
    > * A **Letöltés** lehetőség kiválasztásával a feltöltött fájlt fogja letölteni.
    > * Ha a Batch-teszt nem sikerült, legalább egy Kimondás nem egyezik meg az előrejelzéssel.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>A szándékok kötegelt eredményeinek áttekintése

A Batch-tesztek eredményeinek áttekintéséhez válassza az **eredmények megtekintése** lehetőséget. A teszt eredményei grafikusan mutatják be, hogy a teszt hosszúságú kimondott szöveg hogyan lettek előre jelezve az aktív verziónál.

A Batch-diagram az eredmények négy negyedét jeleníti meg. A diagram jobb oldalán egy szűrő látható. A szűrő leképezéseket és entitásokat tartalmaz. Amikor kijelöl egy [szakaszt](#review-batch-results-for-intents) a diagramon, vagy egy pontot a diagramon belül, a társított kimaradás (ek) a diagram alatt látható.

Amikor a diagram fölé viszi a kurzort, az egér kereke megnövelheti vagy csökkentheti a diagram megjelenítését. Ez akkor hasznos, ha a diagramon több pont is van, és szorosan össze van kapcsolva.

A diagram négy részből áll, és a piros színnel megjelenő két részből áll.

1. Válassza ki a **ModifyOrder** szándékot a szűrők listájában. A Kimondás értéke **igaz pozitív** , ami azt jelenti, hogy a Kimondás sikeresen megfelelt a Batch-fájlban felsorolt pozitív előrejelzésnek.

    > [!div class="mx-imgBorder"]
    > ![A Kimondás sikeresen megegyezett a pozitív előrejelzéssel](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    A szűrők listában a zöld pipa jelzi az egyes szándékok tesztelésének sikerességét is. Az összes többi szándék 1/1 pozitív pontszámmal van felsorolva, mivel a teljes ellenőrzés az egyes szándékok alapján lett tesztelve, a Batch-tesztben nem szereplő szándékok negatív vizsgálataként.

1. Válassza ki a **megerősítő** szándékot. Ez a szándék nem szerepel a Batch-tesztben, így ez a Batch-tesztben szereplő teljes érték negatív tesztje.

    > [!div class="mx-imgBorder"]
    > ![A kizáró eredmény sikeresen előre jelezve lett a nem listázott szándékhoz a Batch-fájlban](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    A negatív teszt sikeres volt, ahogy az a szűrőben lévő zöld szöveggel és a rácsmal is megjegyezve volt.

### <a name="review-batch-test-results-for-entities"></a>Entitások tesztelési eredményeinek áttekintése

A ModifyOrder entitás, amely alentitásokkal rendelkező gépi entitásként jeleníti meg a legfelső szintű entitást, valamint az alentitások előrejelzésének módját.

1. Válassza ki a **ModifyOrder** entitást a szűrő listában, majd válassza ki a rácsban található kört.

1. Az entitás előrejelzése a diagram alatt jelenik meg. A kijelző tartalmazza azokat az előrejelzéseket tartalmazó folytonos vonalakat, amelyek megfelelnek a vártnak nem megfelelő előrejelzések és pontozott soroknak.

    > [!div class="mx-imgBorder"]
    > ![Az entitás szülőjének sikeresen megjósolva a Batch-fájlban](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Diagram eredményeinek szűrése

Ha egy adott szándék vagy entitás alapján szeretné szűrni a diagramot, válassza ki a szándékot vagy az entitást a jobb oldali szűrés panelen. Az adatpontok és a terjesztési frissítésük a diagramon a választott módon történik.

![Vizualizációs batch-teszt eredménye](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Diagram eredményének példái

A LUIS-portál diagramja a következő műveleteket hajthatja végre:
 
#### <a name="view-single-point-utterance-data"></a>Egypontos Kimondás-adatelemek megtekintése

A diagramon vigye az egérmutatót egy adatpontra, és tekintse meg az előrejelzésének bizonyossági pontszámát. A lap alján található hosszúságú kimondott szöveg listában válasszon ki egy adatpontot a megfelelő kifejezés beolvasásához.

![Kijelölt Kimondás](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Szakaszra vonatkozó adatnézet

A négy szakaszból álló diagramon válassza ki a szakasz nevét, például a **hamis pozitív értéket** a diagram jobb felső sarkában. A diagram alatt az adott szakaszban található összes hosszúságú kimondott szöveg megjelenik a diagram alatt a listában.

![Kijelölt hosszúságú kimondott szöveg szakasz szerint](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Ebben az előző képen a Kimondás a `switch on` TurnAllOn szándékkal van megjelölve, de a nincs szándék előrejelzését kapta. Ez azt jelzi, hogy a TurnAllOn szándéknak több példát kell hosszúságú kimondott szöveg a várható előrejelzés elvégzéséhez.

A diagram két része piros színnel jelöli azt a hosszúságú kimondott szöveg, amely nem felel meg a várt előrejelzésnek. Ezek azt jelzik, hosszúságú kimondott szöveg, hogy a LUIS több betanításra van szüksége.

A diagram két része zöld színnel egyezik a várt előrejelzéssel.

## <a name="batch-testing-using-the-rest-api"></a>A Batch tesztelése a REST API használatával 

A LUIS lehetővé teszi a kötegelt tesztelést a LUIS portál és a REST API használatával. A REST API végpontjai alább láthatók. További információ a Batch-tesztelésről a LUIS-portál használatával [: oktatóanyag: batch test adatkészletek](). Használja az alábbi teljes URL-címeket, és cserélje le a helyőrző értékeket a saját LUIS előrejelzési kulcsára és a végpontra. 

Ne feledje, hogy adja hozzá a LUIS-kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és állítsa a következőre: `Content-Type` `application/json` .

### <a name="start-a-batch-test"></a>Batch-teszt elindítása

Indítsa el a Batch-tesztet az alkalmazás vagy egy közzétételi tárolóhely használatával. Küldje el a **post** kérelmet a következő végponti formátumok egyikére. A kérelem törzsében adja meg a batch-fájlt.

Közzétételi tárolóhely
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

Alkalmazás verziójának azonosítója
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Ezek a végpontok egy műveleti azonosítót adnak vissza, amelyet a rendszer az állapotának vizsgálatához fog használni, és az eredményeket kéri le. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Folyamatban lévő batch-teszt állapotának beolvasása

A Batch-tesztből származó művelet AZONOSÍTÓjának használatával a következő végpont-formátumokból szerezheti be az állapotát: 

Közzétételi tárolóhely
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

Alkalmazás verziójának azonosítója
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Eredmények lekérése egy batch-tesztből

A Batch-tesztből származó művelet AZONOSÍTÓjának használatával az eredményeket a következő végpont-formátumokból érheti el: 

Közzétételi tárolóhely
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

Alkalmazás verziójának azonosítója
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>A hosszúságú kimondott szöveg batch-fájlja

A Batch-teszteléshez elküldheti az *adatkészletként* ismert hosszúságú kimondott szöveg batch-fájlt. Az adatkészlet egy JSON-formátumú fájl, amely legfeljebb 1 000 címkézett hosszúságú kimondott szöveg tartalmaz. Egy alkalmazásban akár 10 adatkészletet is kipróbálhat. Ha további tesztelésre van szüksége, töröljön egy adatkészletet, majd adjon hozzá egy újat. A modellben lévő összes egyéni entitás megjelenik a Batch test entitások szűrőben akkor is, ha nincsenek megfelelő entitások a Batch-fájlban.

A batch-fájl hosszúságú kimondott szöveg áll. Minden Kimondás esetén a várt szándékú előrejelzésnek és a várhatóan észlelt [gépi tanulási entitásoknak](luis-concept-entity-types.md#types-of-entities) is szerepelniük kell.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Batch-szintaxis sablon az entitásokkal való leképezéshez

A batch-fájl elindításához használja a következő sablont:

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

A batch-fájl a **startPos** és a **endPos** tulajdonság használatával jegyezze fel az entitások elejét és végét. Az értékek nulla-alapúak, és nem kezdődhetnek és nem végződhet szóközre. Ez eltér a lekérdezési naplóktól, amelyek a startIndex és a endIndex tulajdonságokat használják.

Ha nem szeretne entitásokat tesztelni, foglalja bele a `entities` tulajdonságot, és állítsa üres tömbként az értéket `[]` .

### <a name="rest-api-batch-test-results"></a>REST API batch-tesztek eredményei

Az API több objektumot ad vissza:

* Információk a szándékokról és az entitások modelljeiről, például a pontosságról, a visszahívásról és az F-score-ról.
* Az entitások modelljeivel kapcsolatos információk, például a pontosság, az visszahívás és az F-score 
  * A `verbose` jelző használatával további információkat kaphat az entitásról, például a `entityTextFScore` és a `entityTypeFScore` .
* Megadott hosszúságú kimondott szöveg az előre jelzett és címkézett leképezési nevekkel
* A hamis pozitív entitások listája, valamint a hamis negatív entitások listája.

## <a name="next-steps"></a>Következő lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, a további hosszúságú kimondott szöveg címkézésével vagy funkciók hozzáadásával javíthatja a LUIS-alkalmazás teljesítményét.

* [Címke javasolt hosszúságú kimondott szöveg a LUIS-vel](luis-how-to-review-endpoint-utterances.md)
* [Szolgáltatások használata a LUIS-alkalmazás teljesítményének növeléséhez](luis-how-to-add-features.md)
