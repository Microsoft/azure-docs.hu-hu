---
title: anomáliadetektor Java-ügyfélkönyvtár rövid útmutatója
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: f2e227b2a589955191a2e602495cf0ffbb3f6d8b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732587"
---
A Javához anomáliadetektor ügyféloldali kódtár első lépések. Kövesse az alábbi lépéseket a csomag telepítéséhez, és kezdje el használni a szolgáltatás által biztosított algoritmusokat. Az új többváltozós anomáliadetektálási API-k lehetővé teszik a fejlesztők számára a fejlett AI integrálását a metrikák csoportjaiból származó anomáliák észleléséhez anélkül, hogy gépi tanulási ismeretekre vagy címkével jelölt adatokra lenne szükség. A különböző jelek közötti függőségeket és korrelációkat a rendszer automatikusan kulcsfontosságú tényezőknek számítja. Ez segít proaktív módon megvédeni az összetett rendszereket a hibáktól.

Használja a anomáliadetektor java-ügyféloldali kódtárat a következőre:

* Rendszerszintű anomáliák észlelése idősorok egy csoportjából.
* Ha egyetlen idősor sem árul el sokat, és minden jelet meg kell néznie a probléma észlelése érdekében.
* Drága fizikai eszközök predikatív karbantartása több tíz vagy több száz különböző típusú érzékelővel, amelyek a rendszer állapotának különböző aspektusait mérik.

[Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Csomag (Maven)](https://repo1.maven.org/maven2/com/azure/azure-ai-anomalydetector/3.0.0-beta.2/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* A Java fejlesztői készlet [(JDK) aktuális verziója](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [Gradle buildeszköz ,](https://gradle.org/install/)vagy egy másik függőségkezelő.
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy anomáliadetektor-erőforrást, és hozzon létre anomáliadetektor erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Várja meg az üzembe helyezést, és kattintson az **Erőforráshoz ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrás kulcsra és végpontra az alkalmazás a anomáliadetektor API-hoz való csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Ez a rövid útmutató a Gradle függőségkezelőt használja. További ügyféloldali kódtár-információkat a [Maven központi adattárában talál.](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa `gradle init` a parancsot a munkakönyvtárból. Ez a parancs alapvető buildfájlokat hoz létre a Gradle-hez, beleértve a *build.gradle.kts* fájlt, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer kéri, hogy válasszon **egy DSL-t,** válassza a **Kotlin lehetőséget.**

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Keresse *meg a build.gradle.kts* et, és nyissa meg a kívánt IDE-val vagy szövegszerkesztővel. Ezután másolja be ezt a buildkonfigurációt. Mindenképpen foglalja bele a projektfüggőségeket.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Java-fájl létrehozása

Hozzon létre egy mappát a mintaalkalmazáshoz. Futtassa a következő parancsot a munkakönyvtárból:

```console
mkdir -p src/main/java
```

Lépjen az új mappába, és hozzon létre egy *MetricsQuickstarts.java nevű fájlt.* Nyissa meg a kívánt szerkesztőben vagy IDE-ban, és adja hozzá a következő `import` utasításokat:

```java
package com.azure.ai.anomalydetector;

import com.azure.ai.anomalydetector.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.core.http.*;
import com.azure.core.http.policy.*;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.PagedResponse;
import com.azure.core.http.rest.Response;
import com.azure.core.http.rest.StreamResponse;
import com.azure.core.util.Context;
import reactor.core.publisher.Flux;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.ByteBuffer;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.*;
import java.time.format.DateTimeFormatter;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;
```

Hozza létre az erőforrás Azure-végpontját és kulcsát. Hozzon létre egy másik változót a példaadatfájlhoz.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 A többváltozós ANOMÁLIADETEKTOR való használathoz az észlelés előtt be kell betanítanunk a saját modellünket. A betanításhoz használt adatok idősorozatok, és minden idősornak CSV formátumúnak kell lennie két oszloppal, időbélyegzővel és értékkel. Az idősorokat egyetlen zip-fájlba kell tömörítenünk, és fel kell tölteni az [Azure Blob Storage-ba.](../../../../storage/blobs/storage-blobs-introduction.md) Alapértelmezés szerint a fájlnév az idősor változóját képviseli. Azt is meta.js, hogy a zip-fájlban egy további fájlnevet is fel kell venni, ha azt szeretné, hogy a változó neve eltér a .zip fájl nevétől. Miután létrehoztunk egy SAS- (közös hozzáférésű [jogosultságú) URL-címet,](../../../../storage/common/storage-sas-overview.md)a zip-fájl URL-címét használjuk a betanításhoz.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következőt mutatják be a anomáliadetektor ügyféloldali kódtárával Node.js:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Modell betanítása](#train-a-model)
* [Anomáliák észlelése](#detect-anomalies)
* [Modell exportálása](#export-model)
* [Modell törlése](#delete-model)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Objektum `anomalyDetectorClient` példányosodása a végponttal és a hitelesítő adatokkal.

```java
HttpHeaders headers = new HttpHeaders()
    .put("Accept", ContentType.APPLICATION_JSON);

HttpPipelinePolicy authPolicy = new AzureKeyCredentialPolicy(key,
    new AzureKeyCredential(key));
AddHeadersPolicy addHeadersPolicy = new AddHeadersPolicy(headers);

HttpPipeline httpPipeline = new HttpPipelineBuilder().httpClient(HttpClient.createDefault())
    .policies(authPolicy, addHeadersPolicy).build();
// Instantiate a client that will be used to call the service.
HttpLogOptions httpLogOptions = new HttpLogOptions();
httpLogOptions.setLogLevel(HttpLogDetailLevel.BODY_AND_HEADERS);

AnomalyDetectorClient anomalyDetectorClient = new AnomalyDetectorClientBuilder()
    .pipeline(httpPipeline)
    .endpoint(endpoint)
    .httpLogOptions(httpLogOptions)
    .buildClient();
```

## <a name="train-a-model"></a>Modell betanítása

### <a name="construct-a-model-result-and-train-model"></a>Modell eredményének és betanításának eredménye

Először létre kell hoznunk egy modellkérést. Ügyeljen arra, hogy a kezdési és a záró időpont igazodjon az adatforráshoz.

 A többváltozós ANOMÁLIADETEKTOR való használathoz az észlelés előtt be kell betanítanunk a saját modellünket. A betanításhoz használt adatok idősorkötetek, és minden idősornak CSV formátumúnak kell lennie két oszloppal, időbélyegzővel és értékkel. Az idősorokat egyetlen zip-fájlba kell tömörítenünk, és fel kell tölteni az [Azure Blob Storage-ba.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Alapértelmezés szerint a fájlnév az idősor változóját képviseli. Azt is meta.js, hogy a zip-fájlban egy további fájlnevet is fel kell venni, ha azt szeretné, hogy a változó neve eltér a .zip fájl nevétől. Miután létrehoztunk egy SAS- (közös hozzáférésű [jogosultságú) URL-címet,](../../../../storage/common/storage-sas-overview.md)a zip-fájl URL-címét használjuk a betanításhoz.

```java
Path path = Paths.get("test-data.csv");
List<String> requestData = Files.readAllLines(path);
List<TimeSeriesPoint> series = requestData.stream()
    .map(line -> line.trim())
    .filter(line -> line.length() > 0)
    .map(line -> line.split(",", 2))
    .filter(splits -> splits.length == 2)
    .map(splits -> {
        TimeSeriesPoint timeSeriesPoint = new TimeSeriesPoint();
        timeSeriesPoint.setTimestamp(OffsetDateTime.parse(splits[0]));
        timeSeriesPoint.setValue(Float.parseFloat(splits[1]));
        return timeSeriesPoint;
    })
    .collect(Collectors.toList());

Integer window = 28;
AlignMode alignMode = AlignMode.OUTER;
FillNAMethod fillNAMethod = FillNAMethod.LINEAR;
Integer paddingValue = 0;
AlignPolicy alignPolicy = new AlignPolicy().setAlignMode(alignMode).setFillNAMethod(fillNAMethod).setPaddingValue(paddingValue);
String source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
OffsetDateTime startTime = OffsetDateTime.of(2021, 1, 2, 0, 0, 0, 0, ZoneOffset.UTC);
;
OffsetDateTime endTime = OffsetDateTime.of(2021, 1, 3, 0, 0, 0, 0, ZoneOffset.UTC);
;
String displayName = "Devops-MultiAD";

ModelInfo request = new ModelInfo().setSlidingWindow(window).setAlignPolicy(alignPolicy).setSource(source).setStartTime(startTime).setEndTime(endTime).setDisplayName(displayName);
TrainMultivariateModelResponse trainMultivariateModelResponse = anomalyDetectorClient.trainMultivariateModelWithResponse(request, Context.NONE);
String header = trainMultivariateModelResponse.getDeserializedHeaders().getLocation();
String[] model_ids = header.split("/");
UUID model_id = UUID.fromString(model_ids[model_ids.length - 1]);
System.out.println(model_id);

Integer skip = 0;
Integer top = 5;
PagedIterable<ModelSnapshot> response = anomalyDetectorClient.listMultivariateModel(skip, top);
Iterator<PagedResponse<ModelSnapshot>> ite = response.iterableByPage().iterator();

while (true) {
    Response<Model> response_model = anomalyDetectorClient.getMultivariateModelWithResponse(model_id, Context.NONE);
    UUID model = response_model.getValue().getModelId();
    System.out.println(response_model.getStatusCode());
    System.out.println(response_model.getValue().getModelInfo().getStatus());
    System.out.println(model);
    if (response_model.getValue().getModelInfo().getStatus() == ModelStatus.READY) {
        break;
    }
}
```

## <a name="detect-anomalies"></a>Anomáliák észlelése

```java
DetectionRequest detectionRequest = new DetectionRequest().setSource(source).setStartTime(startTime).setEndTime(endTime);
DetectAnomalyResponse detectAnomalyResponse = anomalyDetectorClient.detectAnomalyWithResponse(model_id, detectionRequest, Context.NONE);
String result = detectAnomalyResponse.getDeserializedHeaders().getLocation();

String[] result_list = result.split("/");
UUID result_id = UUID.fromString(result_list[result_list.length - 1]);

while (true) {
    DetectionResult response_result = anomalyDetectorClient.getDetectionResult(result_id);
    if (response_result.getSummary().getStatus() == DetectionStatus.READY) {
        break;
    }
    else if(response_result.getSummary().getStatus() == DetectionStatus.FAILED){

    }
}
```

## <a name="export-model"></a>Modell exportálása

A betanított modell exportáláshoz használja a `exportModelWithResponse` következőt: .

```java
StreamResponse response_export = anomalyDetectorClient.exportModelWithResponse(model_id, Context.NONE);
Flux<ByteBuffer> value = response_export.getValue();
FileOutputStream bw = new FileOutputStream("result.zip");
value.subscribe(s -> write(bw, s), (e) -> close(bw), () -> close(bw));
```

## <a name="delete-model"></a>Modell törlése

Az aktuális erőforrás számára elérhető meglévő modell törléséhez használja a `deleteMultivariateModelWithResponse` függvényt.

```java
Response<Void> deleteMultivariateModelWithResponse = anomalyDetectorClient.deleteMultivariateModelWithResponse(model_id, Context.NONE);
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást a következővel építheti fel:

```console
gradle build
```
### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a következő `run` céllal:

```console
gradle run
```

## <a name="next-steps"></a>Következő lépések

* [anomáliadetektor többválékos ajánlott eljárások](../../concepts/best-practices-multivariate.md)
