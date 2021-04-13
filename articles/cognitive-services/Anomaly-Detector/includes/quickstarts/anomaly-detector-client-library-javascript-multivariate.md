---
title: Anomália-detektor többváltozós JavaScript ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 4e0f2d1bae07f0814b4f096d8be315bd92cd42fe
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316027"
---
Ismerkedjen meg a következővel: anomália-detektor többváltozós ügyféloldali kódtár a JavaScripthez. A következő lépésekkel telepítheti a csomagot, és megkezdheti a szolgáltatás által biztosított algoritmusok használatát. Az új többváltozós anomália-észlelési API-k lehetővé teszik a fejlesztők számára, hogy a speciális AI-t egyszerűen integrálják a mérőszámokból származó rendellenességek észlelése érdekében, anélkül, hogy gépi tanulási ismeretekre vagy címkézett adatokra lenne A különböző jelek közötti függőségek és korrelációk közötti összefüggések automatikusan kulcsfontosságú tényezőként számítanak. Ezzel a megoldással proaktív módon védhető meg az összetett rendszerek hibái.

A következőhöz tartozó anomália-detektor többváltozós ügyféloldali kódtárat használja a JavaScripthez:

* A rendszerszintű rendellenességek észlelése idősorozatok csoportjából.
* Ha az egyes idősorozatok nem sokat mondanak, és az összes jelet meg kell vizsgálnia a probléma észlelése érdekében.
* A Predicative több száz különböző típusú érzékelővel, a rendszerállapot különböző szempontjainak mérésével a költséges fizikai eszközök karbantartását is megfigyelheti.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Node.js](https://nodejs.org/) aktuális verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hozzon létre egy rendellenesség-Kiderítő erőforrást, "  target="_blank"> és hozzon létre egy anomália-detektor erőforrást </a> a Azure Portal a kulcs és a végpont Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazásnak a rendellenesség-érzékelő API-hoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` . 

```console
npm init
```

Hozzon létre egy nevű fájlt `index.js` , és importálja a következő kódtárakat:
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásakor. Hozzon létre egy másik változót a példaként szolgáló adatfájlhoz.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Az anomáliák többváltozós API-k használatához az észlelés használata előtt be kell tanítani a saját modelljét. A betanításhoz használt adatmennyiség az idősorozatok egy kötege, minden egyes idősorozatnak CSV formátumúnak kell lennie két oszloppal, egy időbélyeggel és egy értékkel. Az összes idősorozatot egy zip-fájlba kell csomagolni, és fel kell tölteni az [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md)-ba. Alapértelmezés szerint a rendszer a fájlnevet fogja használni az idősorozat változójának ábrázolásához. Azt is megteheti, hogy a fájl egy további meta.jsis tartalmazhatja a zip-fájlban, ha azt szeretné, hogy a változó neve eltér a. zip-fájl nevétől. Ha a [blob sas (közös hozzáférésű aláírás) URL-címet](../../../../storage/common/storage-sas-overview.md)generálta, a betanításhoz használhatja a zip-fájl URL-címét.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ms-rest-azure` és a `azure-ai-anomalydetector` NPM csomagokat. Ebben a rövid útmutatóban a CSV-elemzési függvénytárat is használják:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Node.js rendellenesség-Kiderítő ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Modell betanítása](#train-a-model)
* [Rendellenességek észlelése](#detect-anomalies)
* [Modell exportálása](#export-model)
* [Modell törlése](#delete-model)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Objektumot hoz létre `AnomalyDetectorClient` a végponttal és a hitelesítő adatokkal.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey)).client;
```

## <a name="train-a-model"></a>Modell betanítása

### <a name="construct-a-model-result"></a>Modell eredményének létrehozása

Először létre kell hoznia egy modell-kérelmet. Győződjön meg arról, hogy a kezdési és befejezési idő igazodik az adatforráshoz.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Új modell betanítása

A modellre vonatkozó kérelmet át kell adnia az anomália-detektor ügyfél- `trainMultivariateModel` metódusának.

```javascript
console.log("Training a new model...")
var train_response = await client.trainMultivariateModel(Modelrequest)
var model_id = train_response.location.split("/").pop()
console.log("New model ID: " + model_id)
```

Annak ellenőrzését, hogy a modell betanítása befejeződött-e, nyomon követheti a modell állapotát:

```javascript
var model_response = await client.getMultivariateModel(model_id)
var model_status = model_response.modelInfo.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    var model_response = await client.getMultivariateModel(model_id)
    var model_status = model_response.modelInfo.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Rendellenességek észlelése

A `detectAnomaly` és a `getDectectionResult` függvények segítségével meghatározhatja, hogy vannak-e rendellenességek az adatforráson belül.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location.split("/").pop()
var result = await client.getDetectionResult(result_id)
var result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    var result = await client.getDetectionResult(result_id)
    var result_status = result.summary.status
}
```

## <a name="export-model"></a>Modell exportálása

A betanított modell exportálásához használja a `exportModel` függvényt.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Modell törlése

Az aktuális erőforrás számára elérhető meglévő modell törléséhez használja a `deleteMultivariateModel` függvényt.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `node` fájlban található paranccsal.

```console
node index.js
```

## <a name="next-steps"></a>Következő lépések

* [Anomália-érzékelő többváltozós ajánlott eljárásai](../../concepts/best-practices-multivariate.md)
