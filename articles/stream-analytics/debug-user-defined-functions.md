---
title: Felhasználó által definiált függvények hibakeresése Azure Stream Analytics
description: Ez a cikk ismerteti, hogyan lehet hibakeresést végezni a felhasználó által definiált függvények Azure Stream Analyticsban.
author: jenssuessmeyer
ms.author: jenss
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7f5a24e4d351d7c133251900a80c70094b84cc4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019431"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Felhasználó által definiált függvények hibakeresése Azure Stream Analytics 

Ha a felhasználó által definiált függvények (UDF) nem a várt módon működnek, a probléma megoldásához hibakeresést kell végeznie. Ha a [Visual Studio Code](visual-studio-code-local-run-live-input.md) vagy a [Visual Studio](stream-analytics-vs-tools-local-run.md)használatával helyileg futtatja a feladatokat, a stream Analytics-feladatok UDF hibakeresését végezheti el.

Ha a feladatot egy élő bemeneti adatfolyamon helyileg futtatja, akkor csak a Cloud Azure Stream Analytics motor futtatását utánozza egy csomóponton. Az élő adatok helyi tesztelése nem helyettesítheti a felhőben végzett teljesítmény-és méretezhetőségi teszteket, de időt takaríthat meg a működés közbeni tesztelés során, mivel a teszteléshez nem kell elküldenie a feladatot a felhőbe. Emellett a helyi és a mintaadatok helyi futtatására vonatkozó időszabályzatok le vannak tiltva, de az élő adatellenőrzések esetében az időszabályzatok is támogatottak.

## <a name="pick-your-language"></a>Válasszon nyelvet

A .NET (C#) vagy a JavaScript használatával Azure Stream Analytics UDF is írhat. 

### <a name="functions-in-c"></a>Függvények a C-ben # 

Ha [.net-UDF ír a Visual Studióval](stream-analytics-edge-csharp-udf-methods.md), ugyanolyan szintű támogatást kap, mint bármely .net-osztály projekt esetében. Ez a támogatás a következőket tartalmazza:

* Fordítási támogatás, például szintaxis-ellenőrzés és fordító-támogatás.

* A Stream Analytics-megoldásban C#-projekt és-összetevők hozzáadására, elkészítésére és hivatkozására van lehetőség. 

* Egy megosztható projektbe ágyazott kód egyszerű újrafelhasználása. 

* Hibakeresés támogatása a Visual Studióban. Állítsa be a Stream Analytics projektet kezdési projektként, és állítsa be a töréspontokat C#-kódban. Ezután nyomja le az **F5** billentyűt a c# kód hibakereséséhez, mint bármely más C#-projekthez. 

### <a name="functions-in-javascript"></a>Függvények a JavaScriptben

A JavaScript egy másik lehetőség a függvények létrehozására a Stream Analytics. A JavaScript-kód közvetlenül a Stream Analytics projekt függvény területére kerül, amely nehezebbé teszi a projektek közötti megosztást.

A fordítás akkor történik meg, amikor a Stream Analytics projektet lefordítják vagy végrehajtják. A probléma csak futásidőben történő megkeresésének valószínűsége magasabb. A JavaScript-függvények nem támogatják a hibakeresést közvetlenül Stream Analytics.

## <a name="debug-options-for-javascript"></a>Hibakeresési beállítások a JavaScripthez

Mivel a JavaScript-függvények nem rendelkeznek hibakeresési támogatással közvetlenül a Stream Analyticsban, hibakeresést végezhet a függvény HTML-helyen való beágyazásával, és onnan is beolvashatja a kimenetet.

Az alábbi példa bemutatja, hogyan lehet a [Visual Studio Code](quick-create-visual-studio-code.md)-ban a JavaScript-UDF hibakeresését egy integrált futtatókörnyezeti környezet bizonyos korlátaival.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg róla, hogy az Azure Stream Analytics-projekt a következő elemekből áll:

* Egy bemenet 
* Egy kimenet 
* Stream Analytics lekérdezés (. asaql) 
* Stream Analytics feladatok konfigurálása (JobConfig.js)
* Egy JavaScript UDF

### <a name="prepare-files"></a>Fájlok előkészítése

A következő képen a *. asaql* lekérdezési fájl csak az UDF, a *fxcharCount* hívását tartalmazza. Ez a változás biztosítja, hogy a módosítások után is le tudja fordítani a projektet.

> [!div class="mx-imgBorder"]
> ![Stream Analytics teszt-lekérdezési fájl](./media/debug-user-defined-functions/asaql-file.png)

Hozzon létre egy további **mappát a** tesztekben a teszt fájl futtatásához, amelyet a rendszer a JavaScript függvénnyel hajt végre a teszt végrehajtásához. Ebben a példában a mappa neve *fxcharCount* , a teszt neve pedig *Test_UDF.js*. 

Az alábbi képen a teszt fájlban található kód jelenik meg, amely betölti a válaszfájlt, és végrehajtja a függvényt. Ez a példa egyszerű, de további teszt adatfájlokat is betölthet, és a kimenetet további tesztekkel is elvégezheti. A függvény hívásának jelölése kevéssé különbözik a gyakori hívásoktól, mert a fájl hivatkozik, és nem töltődik be a futtatókörnyezetbe, így hibakeresést lehet végezni. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics tesztelési fájl](./media/debug-user-defined-functions/test-file.png)

A függvényben adja hozzá a következő sornyi kódot a fájlhoz a metódusok megjelenítéséhez. Nem érintik a kód fordításának képességét a Visual Studio Code-ban.

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Stream Analytics JavaScript UDF](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>Hibakeresési támogatás telepítése

A hibakereséshez le kell [töltenie](https://nodejs.org/en/download/) és telepítenie kell **node.js**. Telepítse a megfelelő verziót a használt platformnak megfelelően. A node.js futtatókörnyezet telepítése után indítsa újra a Visual Studio Code-ot a módosítások végrehajtásához. 

A hibakeresés indításához válassza a **Futtatás és hibakeresés** lehetőséget, vagy nyomja le a **CTRL + SHIFT + D** billentyűkombinációt. Megjelenik egy kombinált lista, ahol kiválaszthatja a **node.jst** futtatókörnyezetként. Ha csak node.js van telepítve, alapértelmezés szerint a rendszer ezt használja. Ha az F11-ben szükség van rá, át kell lépnie a kóddal és a műhold-fájlba. 

> [!div class="mx-imgBorder"]
> ![Az UDF futtatása és hibakeresése Stream Analytics](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>Felhasználó által definiált összesítések hibakeresése 

A JavaScript-UDF tartozó hibakeresési módszert használhatja a felhasználó által definiált összesítések (UDA) hibakereséséhez. Ebben a példában egy UDA ad hozzá a *. asaql* és a test fájlhoz.

Ahogy az UDF esetében is, a UDA meghívásával biztosíthatja, hogy a projekt a módosítások után legyen lefordítva. 

> [!div class="mx-imgBorder"]
> ![Uda hozzáadása a asaql-hoz](./media/debug-user-defined-functions/asaql-uda.png)

A *Test_UDA.js* FÁJLBAN a uda fájlra hivatkozik, ahogy az UDF-vel tette. Emellett meghívja a, a és a programot is `main()` `init()` `accumulate()` . A `accumulate()` metódus hívása hurokban történik, hogy az értékeket az állami verembe helyezze. A `computeresult()` metódust a végső lekérdezés összeállítására kell meghívni. 

> [!div class="mx-imgBorder"]
> ![UDA-teszt fájl](./media/debug-user-defined-functions/uda-test.png)

Ahogy az UDF-példában is, bizonyos kódokat hozzá kell adni a UDA, hogy elérhetővé tegye a megfelelő módszereket.

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Kód hozzáadva a UDA-hez](./media/debug-user-defined-functions/uda-expose-methods.png)

A hibakeresés indításához válassza a **Futtatás és hibakeresés** lehetőséget, vagy nyomja le a **CTRL + SHIFT + D** billentyűkombinációt. Megjelenik egy kombinált lista, ahol kiválaszthatja a **node.jst** futtatókörnyezetként. Ha csak node.js van telepítve, alapértelmezés szerint a rendszer ezt használja. Ha az F11-ben szükség van rá, át kell lépnie a kóddal és a műhold-fájlba.

> [!div class="mx-imgBorder"]
> ![Uda Stream Analytics futtatása és hibakeresése](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics feladatok helyi fejlesztése és hibakeresése](develop-locally.md)
* [Azure Stream Analytics lekérdezések hibakeresése helyileg a Visual Studióban a feladatok diagramjának használatával](debug-locally-using-job-diagram.md)
* [Felhasználó által definiált függvények Azure Stream Analytics](functions-overview.md)
 
