---
title: Azure Stream Analytics integrálása Azure Machine Learning
description: Ez a cikk bemutatja, hogyan integrálhat egy Azure Stream Analytics feladatot Azure Machine Learning modellekkel.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: devx-track-js
ms.openlocfilehash: c35d5d2f63f4a7abe80a0ff19e5994013355c386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020519"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Azure Stream Analytics integrálása Azure Machine Learning (előzetes verzió)

A gépi tanulási modelleket felhasználó által definiált függvényként (UDF) implementálhatja a Azure Stream Analytics-feladatokban, így valós idejű pontozást és előrejelzéseket végezhet a folyamatos átviteli bemeneti adatokon. A [Azure Machine learning](../machine-learning/overview-what-is-azure-ml.md) lehetővé teszi, hogy bármilyen népszerű, nyílt forráskódú eszközt, például Tensorflow, Scikit vagy PyTorch használjon a modellek előkészítéséhez, betanításához és üzembe helyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

A Machine learning-modellnek a Stream Analytics feladathoz való hozzáadása előtt végezze el a következő lépéseket:

1. A Azure Machine Learning használatával [üzembe helyezheti a modellt webszolgáltatásként](../machine-learning/how-to-deploy-and-where.md).

2. A pontozási szkriptnek tartalmaznia kell egy [minta bemeneteket és kimeneteket](../machine-learning/how-to-deploy-and-where.md) , amelyeket a Azure Machine learning használ a séma specifikációjának létrehozásához. Stream Analytics a séma segítségével értelmezi a webszolgáltatás funkciójának aláírását. Ezt a minta-Felskálázási [definíciót](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json) hivatkozásként használva ellenőrizheti, hogy helyesen állította-e be.

3. Győződjön meg arról, hogy a webszolgáltatás elfogadja és visszaadja a JSON szerializált adatait.

4. A modell üzembe helyezése az [Azure Kubernetes szolgáltatásban](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) nagy léptékű üzemi környezetekben. Ha a webszolgáltatás nem tudja kezelni a feladatból érkező kérések számát, akkor a Stream Analytics feladatának teljesítménye csökken, ami hatással van a késésre. A Azure Container Instanceson üzembe helyezett modellek csak a Azure Portal használata esetén támogatottak. A [Azure Machine learning Designer](../machine-learning/concept-designer.md) használatával létrehozott modellek még nem támogatottak a stream Analyticsban.

## <a name="add-a-machine-learning-model-to-your-job"></a>Gépi tanulási modell hozzáadása a feladatokhoz

A Stream Analytics feladathoz közvetlenül a Azure Portal vagy a Visual Studio Code-ból adhat hozzá Azure Machine Learning függvényeket.

### <a name="azure-portal"></a>Azure Portal

1. Navigáljon a Stream Analytics-feladathoz a Azure Portal, és válassza a **feladatok** lehetőséget a feladatok **topológiája** alatt. Ezután válassza ki **Azure Machine learning szolgáltatást** a **+** legördülő menüből.

   ![Azure Machine Learning UDF hozzáadása](./media/machine-learning-udf/add-azure-machine-learning-udf.png)

2. Töltse ki a **Azure Machine learning Service Function** űrlapot a következő tulajdonságértékek megírásával:

   ![Azure Machine Learning UDF konfigurálása](./media/machine-learning-udf/configure-azure-machine-learning-udf.png)

### <a name="visual-studio-code"></a>Visual Studio Code

1. Nyissa meg a Stream Analytics projektet a Visual Studio Code-ban, és kattintson a jobb gombbal a **functions** mappára. Ezután válassza a **művelet hozzáadása** lehetőséget. Válassza ki **Machine learning UDF** elemet a legördülő listából.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function.png" alt-text="UDF hozzáadása a VS Code-ban":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function-2.png" alt-text="Azure Machine Learning UDF hozzáadása a VS Code-ban":::

2. Adja meg a függvény nevét, és töltse ki a konfigurációs fájlban lévő beállításokat a Codelensben- **előfizetések közül a kiválasztás** lehetőség használatával.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-function-name.png" alt-text="Azure Machine Learning UDF kiválasztása a VS Code-ban":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-configure-settings.png" alt-text="Azure Machine Learning UDF konfigurálása a VS Code-ban":::

Az alábbi táblázat a Stream Analytics Azure Machine Learning Service functions egyes tulajdonságait ismerteti.

|Tulajdonság|Leírás|
|--------|-----------|
|Függvény aliasa|Adjon meg egy nevet a függvény meghívásához a lekérdezésben.|
|Előfizetés|Azure-előfizetése...|
|Azure Machine Learning-munkaterület|A modell webszolgáltatásként való üzembe helyezéséhez használt Azure Machine Learning munkaterület.|
|Központi telepítés|A modellt működtető webszolgáltatás.|
|Függvény aláírása|A webszolgáltatás aláírása az API sémájának specifikációja alapján lett kikövetkeztetve. Ha az aláírás nem töltődik be, ellenőrizze, hogy a pontozási parancsfájlban megadott minta bemenetet és kimenetet adott-e meg a séma automatikus létrehozásához.|
|Párhuzamos kérelmek száma partíción|Ez egy speciális konfiguráció a nagy léptékű átviteli sebesség optimalizálása érdekében. Ez a szám a feladat egyes partíciói által a webszolgáltatásba küldött egyidejű kérelmeket jelöli. A hat folyamatos átviteli egységgel (SU) és eggyel alacsonyabb szintű feladatokhoz egy partíció tartozik. A 12 SUs-vel rendelkező feladatok két partícióval rendelkeznek, a 18 SUs pedig három partíciót és így tovább.<br><br> Ha például a feladatainak két partíciója van, és ezt a paramétert négyre állítja, a feladatokból nyolc egyidejű kérelem lesz a webszolgáltatásnak. A nyilvános előzetes verzióban ez az érték alapértelmezés szerint 20, és nem frissíthető.|
|Kötegek maximális száma|Ez egy speciális konfiguráció a nagy léptékű átviteli sebesség optimalizálásához. Ez a szám azt jelöli, hogy az események maximális száma a webszolgáltatásnak elküldhető egyetlen kérelemben legyen egyesítve.|

## <a name="supported-input-parameters"></a>Támogatott bemeneti paraméterek

Ha a Stream Analytics-lekérdezés egy Azure Machine Learning UDF-t hív meg, a feladatokhoz egy JSON-szerializált kérelem jön létre a webszolgáltatáshoz. A kérelem modell-specifikus sémán alapul. A pontozási parancsfájlban meg kell adnia egy minta bemenetet és kimenetet a [séma automatikus létrehozásához](../machine-learning/how-to-deploy-and-where.md). A séma lehetővé teszi, hogy Stream Analytics a JSON szerializált kérelmét a támogatott adattípusok, például a NumPy, a pandák és a PySpark számára. Egyszerre több bemeneti esemény is feldolgozható egyetlen kérelemben.

A következő Stream Analytics lekérdezés egy Azure Machine Learning UDF meghívását szemlélteti:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

A Stream Analytics csak Azure Machine Learning függvények esetében támogatja a Passing paramétert. Előfordulhat, hogy elő kell készítenie az adatokat, mielőtt bemenetként továbbítja azt a Machine learning UDF-nek. Győződjön meg arról, hogy a bemeneti ML UDF nem null értékű, mert a null bemenetek sikertelenek lesznek.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Több bemeneti paraméter átadása az UDF-nek

A gépi tanulási modellekbe való bemenetek leggyakoribb példái a NumPy tömbök és DataFrames. Létrehozhat egy tömböt JavaScript UDF használatával, és létrehozhat egy JSON-szerializált DataFrame a `WITH` záradék használatával.

### <a name="create-an-input-array"></a>Bemeneti tömb létrehozása

Létrehozhat egy JavaScript UDF-t, amely *nagy* számú bemenetet fogad el, és létrehoz egy tömböt, amely a Azure Machine learning UDF bemenetként használható.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Miután hozzáadta a JavaScript UDF-t a feladatokhoz, a következő lekérdezéssel hívhatja a Azure Machine Learning UDF-t:

```SQL
WITH 
ModelInput AS (
#use JavaScript UDF to construct array that will be used as input to ML UDF
SELECT udf.createArray(vendorid, weekday, pickuphour, passenger, distance) as inputArray
FROM input
)

SELECT udf.score(inputArray)
INTO output
FROM ModelInput
#validate inputArray is not null before passing it to ML UDF to prevent job from failing
WHERE inputArray is not null
```

A következő JSON egy példa erre a kérelemre:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Panda-vagy PySpark-DataFrame létrehozása

A `WITH` záradék használatával létrehozhat egy JSON szerializált DataFrame, amely a Azure Machine learning UDF bemenetként adható át az alábbi ábrán látható módon.

A következő lekérdezés létrehoz egy DataFrame a szükséges mezők kiválasztásával, és a DataFrame használja a Azure Machine Learning UDF bemenetként.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM Dataframe
```

A következő JSON egy példa az előző lekérdezésből származó kérelemre:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Azure Machine Learning UDF teljesítményének optimalizálása

Amikor üzembe helyezi a modellt az Azure Kubernetes Service-ben, [az erőforrás-használat meghatározásához profilokat állíthat be a modellben](../machine-learning/how-to-deploy-profile-model.md). A [központi telepítések esetében is engedélyezheti az alkalmazás-bepillantást](../machine-learning/how-to-enable-app-insights.md) , hogy megértse a kérelmek sebességét, a válaszidőt és a meghibásodási arányt.

Ha magas szintű adatátviteli sebességgel rendelkezik, előfordulhat, hogy módosítania kell az alábbi paramétereket a Stream Analyticsban, hogy optimális teljesítményt érjen el az alacsony végpontok közötti késésekkel:

1. Kötegek maximális száma.
2. A párhuzamos kérelmek száma egy partíción.

### <a name="determine-the-right-batch-size"></a>A köteg megfelelő méretének meghatározása

A webszolgáltatás üzembe helyezése után elküldheti a minta kérést, amely az 50-től kezdődően változó méretű kötegeket és több százat vesz igénybe. Például: 200, 500, 1000, 2000 és így tovább. Megfigyelheti, hogy egy adott köteg mérete után a válasz késése növekszik. Az a pont, amely után a válaszok száma növekszik, a feladatokhoz tartozó kötegek maximális száma.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>A párhuzamos kérelmek számának meghatározása partíción

Optimális skálázás esetén a Stream Analyticsi feladatnak több párhuzamos kérelmet kell küldenie a webszolgáltatásba, és néhány ezredmásodpercen belül választ kaphat. A webszolgáltatás válaszának késése közvetlenül befolyásolhatja Stream Analytics feladatának késését és teljesítményét. Ha a feladatból a webszolgáltatásba irányuló hívás hosszú időt vesz igénybe, akkor valószínű, hogy a vízjel késleltetése megnő, és a várakozó bemeneti eseményeinek számát is megnöveli.

Az ilyen késések elkerülése érdekében győződjön meg arról, hogy az Azure Kubernetes szolgáltatás (ak) fürtjének [megfelelő számú csomóponttal és replikával](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli)lett kiépítve. Fontos, hogy a webszolgáltatás nagy rendelkezésre állású legyen, és sikeres válaszokat ad vissza. Ha a feladat egy szolgáltatás nem érhető el választ (503) kap a webszolgáltatástól, a rendszer folyamatosan újrapróbálkozik az exponenciális visszalépéssel. Ha a sikertől (200) és a szolgáltatástól (503) nem érhető el válasz, a művelet sikertelen állapotba kerül.

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: Az Azure Stream Analytics felhasználói JavaScript-függvényei](stream-analytics-javascript-user-defined-functions.md)
* [Stream Analytics-feladat skálázása Azure Machine Learning Studio (klasszikus) függvénnyel](stream-analytics-scale-with-machine-learning-functions.md)
