---
title: Anomália detektor JavaScript ügyféloldali kódtár rövid útmutatója
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.custom: devx-track-js
ms.openlocfilehash: ca842a3d235cd58cb12bf9d8d80419e42ddf8441
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445507"
---
Ismerkedjen meg az anomáliák Kiderítő ügyféloldali kódtáraval a JavaScripthez. Az alábbi lépéseket követve telepítheti a csomagot a szolgáltatás által biztosított algoritmusok használatával. Az anomália-detektor szolgáltatás lehetővé teszi, hogy az idősoros adataiban az adatsorozatok adatait automatikusan a legjobb illeszkedő modellekkel találja, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől.

A következőhöz használhatja az anomália-Kiderítő ügyféloldali kódtárat a JavaScripthez:

* Az idősorozat-adatkészletben lévő rendellenességek észlelése batch-kérelemként
* Az idősorozat legújabb adatpontjának anomália állapotának észlelése
* Az adatkészletben lévő trendek változási pontjainak észlelése.

[Könyvtár-referenciák dokumentációja](https://go.microsoft.com/fwlink/?linkid=2090788)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)  |  [Csomag (NPM)](https://www.npmjs.com/package/%40azure/ai-anomaly-detector)  |  [A kód megkeresése a githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Node.js](https://nodejs.org/) aktuális verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hozzon létre egy rendellenesség-Kiderítő erőforrást, "  target="_blank"> és hozzon létre egy anomália-detektor erőforrást </a> a Azure Portal a kulcs és a végpont Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazásnak a rendellenesség-érzékelő API-hoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` . 

```console
npm init
```

Hozzon létre egy nevű fájlt `index.js` , és importálja a következő könyvtárakat:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásakor. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt. Hozzon létre egy másik változót egy későbbi lépésben letölteni kívánt adatfájlhoz, és az adatpontok üres listáját. Ezután hozzon létre egy `ApiKeyCredentials` objektumot, amely tartalmazza a kulcsot.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ms-rest-azure` és a `azure-cognitiveservices-anomalydetector` NPM csomagokat. Ebben a rövid útmutatóban a CSV-elemzési függvénytárat is használják:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

## <a name="object-model"></a>Objektummodell

A rendellenesség-Kiderítő ügyfél egy [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél a [entireDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#entiredetect-request--servicecallback-entiredetectresponse--)vagy a [LastDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#lastdetect-request--msrest-requestoptionsbase-)használatával egy teljes adatkészlet esetében elvégezheti a anomáliák észlelését. A [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090788) metódus észleli azokat a pontokat, amelyek egy trend változásait jelzik. 

Az idősorozat-információk küldése a [kérelem](/javascript/api/@azure/cognitiveservices-anomalydetector/request) objektumában lévő [pontok](/javascript/api/@azure/cognitiveservices-anomalydetector/point) sorozata. Az `Request` objektum olyan tulajdonságokat tartalmaz, amelyek leírják az adatok (például a[részletesség](/javascript/api/@azure/cognitiveservices-anomalydetector/request#granularity) ) és az anomáliák észlelésének paramétereit. 

Az anomália-detektor válasza egy [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse), [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse)vagy [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788) objektum a használt metódustól függően. 

## <a name="code-examples"></a>Kódpéldák 

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Node.js rendellenesség-Kiderítő ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Idősorozat-adatkészlet betöltése fájlból](#load-time-series-data-from-a-file)
* [A teljes adathalmazban észlelt rendellenességek észlelése](#detect-anomalies-in-the-entire-data-set) 
* [A legutóbbi adatpont anomália állapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)
* [Az adatkészletben lévő változási pontok észlelése](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozza létre a [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient) objektumot a végponttal és a hitelesítő adatokkal.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Adatsorozat-adatok betöltése fájlból

A rövid útmutatóhoz tartozó példa adatainak letöltése a [githubról](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. A böngészőben kattintson a jobb gombbal a **RAW** elemre.
2. Kattintson **a hivatkozás mentése másként** elemre.
3. Mentse a fájlt egy. csv-fájlként az alkalmazás könyvtárába.

Ez az idősoros adat. csv-fájlként van formázva, és a rendszer elküldi a rendellenesség-Kiderítő API-nak.

Olvassa el az adatfájlt a CSV-parse Library `readFileSync()` metódusával, és elemezze a fájlt a következővel: `parse()` . Az egyes sorok esetében leküldheti az időbélyeget tartalmazó [pont](/javascript/api/@azure/cognitiveservices-anomalydetector/point) objektumot és a numerikus értéket.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>A teljes adathalmazban észlelt rendellenességek észlelése 

Hívja meg az API-t, hogy a teljes idősorozaton keresztül észlelje a rendellenességeket az ügyfél [entireDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#entiredetect-request--msrest-requestoptionsbase-) metódusával. Tárolja a visszaadott [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse) objektumot. Ismételje meg a válasz `isAnomaly` listáját, és nyomtassa ki bármelyik érték indexét `true` . Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

A rendellenesség-Kiderítő API meghívásával megállapíthatja, hogy a legutóbbi adatpontja anomália-e az ügyfél [lastDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#lastdetect-request--msrest-requestoptionsbase-) metódusának használatával, és tárolja a visszaadott [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse) objektumot. A válasz `isAnomaly` értéke egy olyan logikai érték, amely megadja az adott pont anomáliának állapotát.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Az adatkészletben lévő változási pontok észlelése

Hívja meg az API-t a változási pontok észleléséhez az idősorozatban az ügyfél [detectChangePoint ()](https://go.microsoft.com/fwlink/?linkid=2090788) metódusával. Tárolja a visszaadott [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788) objektumot. Ismételje meg a válasz `isChangePoint` listáját, és nyomtassa ki bármelyik érték indexét `true` . Ezek az értékek a trend változási pontjainak indexeit tükrözik, ha vannak ilyenek.

[!code-javascript[detect change points](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=changePointDetection)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `node` fájlban található paranccsal.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
