---
title: Kötegelt teszt végrehajtása – LUIS
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) kötegelt tesztelési készletekkel megkeresi a helytelen szándékú és entitásokkal írt kimondott szövegeket.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 9fe4f21a5c9e9e26a2f94b8a60cba47916842fe3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501790"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Kötegelt tesztelés kimondott példa szövegekkel

A kötegelt tesztelés ellenőrzi az aktív betanított verziót az előrejelzés pontosságának méréséhez. A kötegelt teszt segítségével megtekintheti az aktív verzióban lévő egyes szándékok és entitások pontosságát. Tekintse át a kötegelt teszt eredményeit a pontosság javítása érdekében, például további kimondott szövegeket adhat hozzá egy szándékhoz, ha az alkalmazás gyakran nem tudja azonosítani a megfelelő szándékot vagy címkézni az entitásokat a kimondott szövegben.

## <a name="group-data-for-batch-test"></a>Adatok csoportosítása kötegelt teszteléshez

Fontos, hogy a kötegelt teszteléshez használt kimondott szöveg új a LUIS-hoz. Ha vannak kimondott szöveghalmazai, ossza fel a kimondott szövegeket három készletre: szándékhoz hozzáadott példa kimondott szövegre, közzétett végpontról kapott kimondott szövegre, valamint a LUIS betanítása után kötegelt tesztelésre használt kimondott szövegre.

A használt kötegelt JSON-fájlnak tartalmaznia kell olyan beszédeket, amelyek legfelső szintű gépi tanulási entitásokkal vannak címkézve, beleértve a kezdő és a záró pozíciót is. A kimondott szövegnek nem szabad az alkalmazásban már példáknak lennie. Olyan kimondott szövegnek kell lennie, amely szándékot és entitásokat szeretne pozitívan előrejelezni.

A teszteket elkülönítheti szándék és/vagy entitás alapján, vagy az összes tesztet (legfeljebb 1000 kimondott szöveg) ugyanabban a fájlban használhatja. 

### <a name="common-errors-importing-a-batch"></a>Köteg importálása során előforduló gyakori hibák

Ha hibába fog belefutni, amikor feltölti a batch-fájlt a LUIS-ba, ellenőrizze a következő gyakori problémákat:

* Több mint 1000 kimondott szöveg egy kötegfájlban
* Egy kimondott szöveg JSON-objektuma, amely nem entitástulajdonságokkal működik. A tulajdonság lehet egy üres tömb.
* Több entitásban címkével rendelkező szó(k)
* Az entitáscímkék egy szóköztől indulnak vagy végződnek.

## <a name="fixing-batch-errors"></a>Köteghibák javítása

Ha hibák vannak a kötegelt tesztelésben, hozzáadhat további kimondott szövegeket egy szándékhoz, és/vagy további kimondott szövegeket címkézhet meg az entitással, hogy a LUIS meg tudja adni a szándékok közötti kapcsolatot. Ha kimondott szövegeket adott hozzá, címkézte őket, és továbbra is előrejelzési hibákat kap [a](luis-concept-feature.md) kötegelt tesztelés során, érdemes lehet tartományspecifikus szókészletet hozzáadni a kifejezéslista funkcióhoz, amely segít a LUIS-nak a gyorsabb tanulásban.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Kötegelt tesztelés a LUIS-portál használatával 

### <a name="import-and-train-an-example-app"></a>Példaalkalmazás importálása és betanítás

Importálhat egy olyan alkalmazást, amely egy pizzarendelést vesz fel, `1 pepperoni pizza on thin crust` például: .

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Jelentkezzen be a [LUIS portálra,](https://www.luis.ai)  és válassza ki az Előfizetés és szerzői erőforrást az adott szerzői erőforráshoz rendelt alkalmazásokhoz. 
1. Válassza az Új alkalmazás melletti **nyilat, majd** kattintson az **Importálás JSON-fájlként** elemre a JSON új alkalmazásba való importáláshoz. Az alkalmazásnak nevezze el a `Pizza app` következőt: .


1. Az **alkalmazás betanításhoz** válassza a navigáció jobb felső sarkában található Betanítás lehetőséget.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Kötegelt tesztfájl

A JSON-példa egy címkével jelölt entitást tartalmazó kimondott szövegből áll, amely bemutatja, hogyan néz ki egy tesztfájl. A saját tesztje során számos kimondott szövegnek kell lennie, a megfelelő szándékkal és gépi tanulási entitással címkézve.

1. Hozzon `pizza-with-machine-learned-entity-test.json` létre egy szövegszerkesztőt, vagy [töltse](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) le.

2. A JSON-formátumú kötegelt fájlban adjon hozzá egy  kimondott szövegeket a tesztben előrejelezni kívánt szándékkal.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>A köteg futtatása

1. A **felső navigációs** sávon válassza a Tesztelés lehetőséget.

2. A **jobb oldali panelen válassza** a Batch-tesztelés panelt.

    ![Kötegelt tesztelési hivatkozás](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Válassza az **Importálás** lehetőséget. A megjelenő párbeszédpanelen válassza  a Fájl kiválasztása lehetőséget, és keresse meg a megfelelő JSON-formátumot, amely nem tartalmaz több mint *1000* tesztelni kívánt kimondott szót.

    Az importálási hibákat a böngésző tetején egy piros értesítési sávban jelenti a rendszer. Ha egy importálás hibákat tartalmaz, a rendszer nem hoz létre adatkészletet. További információ: Gyakori [hibák.](#common-errors-importing-a-batch)

4. Válassza ki a fájl `pizza-with-machine-learned-entity-test.json` helyét.

5. Nevezze el az adatkészletet, `pizza test` és válassza a Kész **lehetőséget.**

6. Kattintson a **Futtatás** gombra. A kötegelt teszt futtatása után válassza az **Eredmények elérése lehetőséget.** 

    > [!TIP]
    > * A **Letöltés lehetőség** kiválasztásakor a rendszer letölti a feltöltött fájlt.
    > * Ha azt látja, hogy a kötegelt teszt sikertelen volt, legalább egy kimondott szöveg szándéka nem felelt meg az előrejelzésnek.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>A szándékok kötegelt eredményeinek áttekintése

A kötegelt teszt eredményeinek áttekintéshez válassza az **Eredmények lásd lehetőséget.** A teszteredmények grafikusan mutatják be, hogyan voltak előrejelzve a teszt kimondott szövegei az aktív verzióhoz.

A kötegelt diagram négy negyed eredményt jelenít meg. A diagramtól jobbra található egy szűrő. A szűrő szándékokat és entitásokat tartalmaz. Amikor [kiválasztja](#review-batch-results-for-intents) a diagram egy szakaszát vagy egy pontot a diagramon belül, a társított kimondott szöveg(ék) a diagram alatt jelennek meg.

Ha a diagram fölé mozgatja a kurzort, az egérkerék nagyíthatja vagy csökkentheti a diagramon látható megjelenítést. Ez akkor hasznos, ha a diagramon számos pont szorosan össze van csoportosítva.

A diagram négy negyedben található, és két szakasz piros színben jelenik meg.

1. Válassza ki **a ModifyOrder szándékot** a szűrőlistában. A kimondott szöveg valódi pozitívként van előrejelezni, ami azt jelenti, hogy **a** kimondott szöveg sikeresen megfelelt a batch-fájlban szereplő pozitív előrejelzésének.

    > [!div class="mx-imgBorder"]
    > ![A kimondott szöveg sikeresen megfelelt a pozitív előrejelzésnek](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    A szűrők listájában található zöld pipák az egyes szándékok teszteredményeit is jelzik. Az összes többi szándék 1/1 pozitív pontszámmal szerepel, mert a kimondott szöveg tesztelése minden szándékon negatív tesztként történt a kötegelt tesztben nem szereplő szándékok esetén.

1. Válassza a **Megerősítés szándékot.** Ez a szándék nem szerepel a kötegelt tesztben, ezért ez a kötegelt tesztben szereplő kimondott szöveg negatív tesztje.

    > [!div class="mx-imgBorder"]
    > ![A kimondott szöveg sikeresen negatív előrejelzést adott a nem listált szándékhoz a batch-fájlban](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    A negatív teszt sikeres volt, ahogy a szűrőben a zöld szöveg és a rács is fel van jelölve.

### <a name="review-batch-test-results-for-entities"></a>Entitások kötegelt teszteredményének áttekintése

A ModifyOrder entitás, amely alentitásokkal rendelkezik gépentitásként, megjeleníti, hogy a legfelső szintű entitás megfelelt-e az egyezésnek, és hogy az alentitások hogyan vannak előrejelezve.

1. Válassza ki **a ModifyOrder entitást** a szűrőlistában, majd válassza ki a rácsban található kört.

1. Az entitás előrejelzése a diagram alatt jelenik meg. A kijelző folytonos vonalakat tartalmaz az előrejelzésekhez, amelyek megfelelnek a vártnak, és pontozott vonalakat az előrejelzésnek nem megfelelő előrejelzésekhez.

    > [!div class="mx-imgBorder"]
    > ![Entitás szülője sikeresen előrejelezni a batch-fájlban](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Diagrameredmények szűrése

A diagram adott szándék vagy entitás alapján való szűréséhez válassza ki a szándékot vagy entitást a jobb oldali szűrőpanelen. Az adatpontok és azok eloszlása a kiválasztott beállításoknak megfelelően frissül a gráfon.

![Vizualizált Batch-teszt eredménye](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Diagramok eredménypépéi

A LUIS portálon a diagramon a következő műveleteket végezheti el:
 
#### <a name="view-single-point-utterance-data"></a>Egypontos kimondott szöveg adatainak megtekintése

A diagramon vigye a kurzort egy adatpont fölé az előrejelzése bizonyosságpontszámának megjelenítéséhez. Válasszon ki egy adatpontot a hozzá tartozó kimondott szöveg lekéréséhez az oldal alján található kimondott szöveglistában.

![Kiválasztott kimondott szöveg](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Szakaszadatok megtekintése

A négyszakaszos diagramon válassza ki a  szakasz nevét( például Hamis pozitív) a diagram jobb felső részében. A diagram alatt az ebben a szakaszban található összes kimondott szöveg megjelenik a diagram alatt egy listában.

![Kiválasztott kimondott szöveg szakasz szerint](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Az előző képen a kimondott szöveg a TurnAllOn szándékkal van megcímkézve, de a None szándék `switch on` előrejelzését kapta. Ez azt jelzi, hogy a TurnAllOn szándéknak több kimondott példa kimondott szövegre van szüksége a várt előrejelzéshez.

A diagram két piros szakasza olyan beszédeket jelez, amelyek nem egyeznek a várt előrejelzéssel. Ezek olyan kimondott szövegeket jeleznek, amelyekhez a LUIS-nak több betanítást kell betanítani.

A diagram két zöld szakasza megfelelt a várt előrejelzésnek.

## <a name="batch-testing-using-the-rest-api"></a>Kötegelt tesztelés a REST API 

A LUIS lehetővé teszi a kötegelt tesztelést a LUIS portál és a REST API. A szolgáltatásvégpontokat REST API alább soroljuk fel. További információ a LUIS-portálon végzett kötegelt [tesztelésről: Oktatóanyag: kötegelt tesztelési adatkészletek.]() Használja az alábbi teljes URL-címeket, és cserélje le a helyőrző értékeket a saját LUIS előrejelzési kulcsával és végpontjára. 

Ne felejtse el hozzáadni a LUIS-kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és állítsa a `Content-Type` következőre: `application/json` .

### <a name="start-a-batch-test"></a>Kötegelt teszt kezdete

Kötegelt teszteket indít el egy alkalmazásverzió-azonosító vagy egy közzétételi pont használatával. KÜLDJÖN **EGY POST kérést** az alábbi végpontformátumok egyikére. A kérelem törzsébe foglalja bele a kötegfájlt.

Közzétételi pont
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

Alkalmazás verzióazonosítója
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Ezek a végpontok egy műveletazonosítót fognak visszaadni, amely az állapot ellenőrzésére és az eredmények lekért végrehajtásához használható. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Folyamatban lévő kötegelt teszt állapotának lekért állapota

Használja az elindított kötegelt teszt műveletazonosítóját, hogy le tudja kapni az állapotát a következő végpontformátumból: 

Közzétételi pont
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

Alkalmazás verzióazonosítója
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Eredmények lekérte egy kötegelt tesztből

Használja a kötegelt tesztből származó műveletazonosítót, hogy az eredményeket a következő végpontformátumból szerezze be: 

Közzétételi pont
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

Alkalmazás verzióazonosítója
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Kimondott szövegeket kötegelő fájl

Küldje el a kimondott szövegeket , más néven *adatkészletet,* kötegelt teszteléshez. Az adatkészlet egy JSON-formátumú fájl, amely legfeljebb 1000 címkézett kimondott szöveget tartalmaz. Egy alkalmazásban legfeljebb 10 adatkészletet tesztelhet. Ha többet szeretne tesztelni, töröljön egy adatkészletet, majd adjon hozzá egy újat. A modellben minden egyéni entitás megjelenik a kötegelt tesztentitások szűrőben, még akkor is, ha nincsenek megfelelő entitások a kötegelt fájl adataiban.

A kötegfájl kimondott szövegeket tartalmaz. Minden kimondott szövegnek a várt szándék-előrejelzéssel, valamint a várhatóan észlelt gépi tanulási entitásokkal kell lennie. [](luis-concept-entity-types.md#machine-learned-ml-entity)

### <a name="batch-syntax-template-for-intents-with-entities"></a>Batch-szintaxissablon entitásokkal használható szándékokhoz

A következő sablonnal indítsa el a kötegfájlt:

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

A batch-fájl a **startPos** és **az endPos** tulajdonsággal jegyezze fel az entitás kezdetét és végét. Az értékek nulla alapúak, és nem kezdődnek vagy végződnek szóköz alapján. Ez eltér a lekérdezési naplóktól, amelyek a startIndex és az endIndex tulajdonságokat használják.

Ha nem szeretné tesztelni az entitásokat, adja meg a tulajdonságot, és állítsa be üres tömbként `entities` az értéket. `[]`

### <a name="rest-api-batch-test-results"></a>REST API teszteredmények létrehozása

Az API számos objektumot ad vissza:

* A szándék- és entitásmodellekkel kapcsolatos információk, például a pontosság, a felidézés és az F-pontszám.
* Az entitásmodellekkel kapcsolatos információk, például pontosság, előhívás és F-pontszám) az egyes entitásokhoz 
  * A jelzővel további információkat kaphat az entitásról, például a és `verbose` `entityTextFScore` a `entityTypeFScore` entitásról.
* Kimondott szövegeket adott meg az előrejel jelzett és címkével jelölt szándéknevekkel
* Hamis pozitív entitások listája és hamis negatív entitások listája.

## <a name="next-steps"></a>Következő lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, több kimondott szöveg címkézésével vagy funkciók hozzáadásával javíthatja a LUIS-alkalmazás teljesítményét.

* [Javasolt kimondott szöveg címkézése a LUIS-val](luis-how-to-review-endpoint-utterances.md)
* [Funkciók használata a LUIS-alkalmazás teljesítményének javításához](luis-how-to-add-features.md)
