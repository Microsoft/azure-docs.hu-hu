---
title: anomáliadetektor JavaScript ügyféloldali kódtár létrehozása – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 261dbb7cab2ac17a39777241d24e2c73cf550873
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800277"
---
A JavaScripthez anomáliadetektor ügyféloldali kódtár első lépések. Kövesse az alábbi lépéseket a csomag telepítéséhez és a szolgáltatás által biztosított algoritmusok használatának elkezdéhez. Az új többváltozós anomáliadetektálási API-k a fejlett AI egyszerű integrálásával teszik lehetővé a fejlesztők számára a metrikák csoportjaiból származó anomáliadetektálást anélkül, hogy gépi tanulási ismeretekre vagy címkével jelölt adatokra lenne szükség. A különböző jelek közötti függőségeket és összefüggéseket a rendszer automatikusan kulcsfontosságú tényezőknek számítja. Ez segít proaktív módon megvédeni az összetett rendszereket a hibáktól.

A JavaScripthez anomáliadetektor ügyféloldali kódtár használatával a következőt használhatja:

* Rendszerszintű anomáliák észlelése idősorok egy csoportjából.
* Ha egy adott idősor nem árul el sokat, és minden jelet meg kell néznie a probléma észlelése érdekében.
* Drága fizikai eszközök predikatív karbantartása több tíz-száz különböző típusú érzékelővel, amelyek a rendszer állapotának különböző aspektusait mérik.

[Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector)  |  [Csomag (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* ANode.js[](https://nodejs.org/)
* Az Azure-előfizetés létrehozása után hozzon létre egy anomáliadetektor-erőforrást, anomáliadetektor erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Várja meg az üzembe helyezést, majd kattintson az **Erőforráshoz ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrás kulcsának és végpontjának létrehozására, hogy az alkalmazást a anomáliadetektor API-hoz. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Az parancs `npm init` futtatásával hozzon létre egy node-alkalmazást egy `package.json` fájllal. 

```console
npm init
```

Hozzon létre egy nevű fájlt, `index.js` és importálja a következő kódtárakat: '
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Hozza létre az erőforrás Azure-végpontját és kulcsát. Hozzon létre egy másik változót a példaadatfájlhoz.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 A többváltozós ANOMÁLIADETEKTOR való használathoz az észlelés előtt be kell betanítanunk a saját modellünket. A betanításhoz használt adatok idősorkötetek, és minden idősornak CSV formátumúnak kell lennie két oszloppal, időbélyegzővel és értékkel. Az idősorokat egyetlen zip-fájlba kell tömörítenünk, és fel kell tölteni az [Azure Blob Storage-ba.](../../../../storage/blobs/storage-blobs-introduction.md) Alapértelmezés szerint a fájlnév az idősor változóját képviseli. Azt is meta.js, hogy a zip-fájlban egy további fájlnevet is fel kell venni, ha azt szeretné, hogy a változó neve eltér a .zip fájl nevétől. Miután létrehoztunk egy SAS- (közös hozzáférésű [jogosultságú) URL-címet,](../../../../storage/common/storage-sas-overview.md)a zip-fájl URL-címét használjuk a betanításhoz.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse az `ms-rest-azure` és az `azure-ai-anomalydetector` NPM-csomagot. Ebben a rövid útmutatóban a csv-parse kódtárat is használjuk:

```console
npm install @azure/ai-anomaly-detector csv-parse
```

Az alkalmazás `package.json` fájlja frissül a függőségekkel.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következőt mutatják be a anomáliadetektor ügyféloldali kódtárával Node.js:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Modell betanítása](#train-a-model)
* [Anomáliák észlelése](#detect-anomalies)
* [Modell exportálása](#export-model)
* [Modell törlése](#delete-model)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Objektum példányosodása a végponttal és a `AnomalyDetectorClient` hitelesítő adatokkal.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="train-a-model"></a>Modell betanítása

### <a name="construct-a-model-result"></a>Modell eredményének megépítése

Először létre kell hoznunk egy modellkérést. Győződjön meg arról, hogy a kezdési és a záró időpont igazodik az adatforráshoz.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Új modell betanítás

A modellkérést át kell adnia a anomáliadetektor ügyfél `trainMultivariateModel` metódusának.

```javascript
console.log("Training a new model...")
const train_response = await client.trainMultivariateModel(Modelrequest)
const model_id = train_response.location?.split("/").pop() ?? ""
console.log("New model ID: " + model_id)
```

Annak ellenőrzéshez, hogy a modell betanítás befejeződött-e, nyomon követheti a modell állapotát:

```javascript
let model_response = await client.getMultivariateModel(model_id)
let model_status = model_response.modelInfo?.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    model_response = await client.getMultivariateModel(model_id)
    model_status = model_response.modelInfo?.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Anomáliák észlelése

A és `detectAnomaly` `getDectectionResult` a függvény használatával állapítsa meg, hogy vannak-e anomáliák az adatforrásban.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location?.split("/").pop() ?? ""
let result = await client.getDetectionResult(result_id)
let result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    result = await client.getDetectionResult(result_id)
    result_status = result.summary.status
}
```

## <a name="export-model"></a>Modell exportálása

A betanított modell exportáláshoz használja a `exportModel` függvényt.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody?.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Modell törlése

Az aktuális erőforrás számára elérhető meglévő modell törléséhez használja a `deleteMultivariateModel` függvényt.

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `node` gyorsindítási fájlban található paranccsal.

```console
node index.js
```

## <a name="next-steps"></a>Következő lépések

* [anomáliadetektor többválékos ajánlott eljárások](../../concepts/best-practices-multivariate.md)
