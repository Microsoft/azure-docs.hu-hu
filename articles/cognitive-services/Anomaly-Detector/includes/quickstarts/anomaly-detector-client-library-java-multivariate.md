---
title: Anomália-detektor többváltozós Java ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: eae4d00cd7b1a0ff90648086320135505a0d900a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316022"
---
Ismerkedjen meg a Javához készült anomália-detektor többváltozós ügyféloldali függvénytárával. Az alábbi lépéseket követve telepítheti a csomagot a szolgáltatás által biztosított algoritmusok használatával. Az új többváltozós anomália-észlelési API-k lehetővé teszik a fejlesztők számára, hogy a speciális AI-t egyszerűen integrálják a mérőszámokból származó rendellenességek észlelése érdekében, anélkül, hogy gépi tanulási ismeretekre vagy címkézett adatokra lenne A különböző jelek közötti függőségek és korrelációk közötti összefüggések automatikusan kulcsfontosságú tényezőként számítanak. Ezzel a megoldással proaktív módon védhető meg az összetett rendszerek hibái.

A következőhöz tartozó anomália-detektor többváltozós ügyféloldali kódtárat használja a Javához:

* A rendszerszintű rendellenességek észlelése idősorozatok csoportjából.
* Ha az egyes idősorozatok nem sokat mondanak, és az összes jelet meg kell vizsgálnia a probléma észlelése érdekében.
* A Predicative több száz különböző típusú érzékelővel, a rendszerállapot különböző szempontjainak mérésével a költséges fizikai eszközök karbantartását is megfigyelheti.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hozzon létre egy rendellenesség-Kiderítő erőforrást, "  target="_blank"> és hozzon létre egy anomália-detektor erőforrást </a> a Azure Portal a kulcs és a végpont Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazásnak a rendellenesség-érzékelő API-hoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Ez a rövid útmutató a Gradle függőség-kezelőt használja. További ügyféloldali függvénytár-információkat a [Maven központi adattárában](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)talál.

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárból. Ez a parancs alapvető Build-fájlokat hoz létre a Gradle számára, beleértve a *Build. Gradle. KTS* fájlt, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL** kiválasztására, válassza a **Kotlin** lehetőséget.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Keresse meg a *Build. gradle. KTS* , és nyissa meg a kívánt ide-vagy szövegszerkesztővel. Ezután másolja ezt a Build-konfigurációt. Ügyeljen arra, hogy a projekt függőségei is szerepeljenek.

```kotlin
dependencies {
    compile("com.azure:azure-ai-anomalydetector")
}
```

### <a name="create-a-java-file"></a>Java-fájl létrehozása

Hozzon létre egy mappát a minta alkalmazáshoz. A munkakönyvtárból futtassa a következő parancsot:

```console
mkdir -p src/main/java
```

Navigáljon az új mappára, és hozzon létre egy *MetricsAdvisorQuickstarts. Java* nevű fájlt. Nyissa meg a kívánt szerkesztőben vagy IDE, és adja hozzá a következő `import` utasításokat:

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

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásakor. Hozzon létre egy másik változót a példaként szolgáló adatfájlhoz.

```java
String key = "YOUR_API_KEY";
String endpoint = "YOUR_ENDPOINT";
```

 Az anomáliák többváltozós API-k használatához az észlelés használata előtt be kell tanítani a saját modelljét. A betanításhoz használt adatmennyiség az idősorozatok egy kötege, minden egyes idősorozatnak CSV formátumúnak kell lennie két oszloppal, egy időbélyeggel és egy értékkel. Az összes idősorozatot egy zip-fájlba kell csomagolni, és fel kell tölteni az [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md)-ba. Alapértelmezés szerint a rendszer a fájlnevet fogja használni az idősorozat változójának ábrázolásához. Azt is megteheti, hogy a fájl egy további meta.jsis tartalmazhatja a zip-fájlban, ha azt szeretné, hogy a változó neve eltér a. zip-fájl nevétől. Ha a [blob sas (közös hozzáférésű aláírás) URL-címet](../../../../storage/common/storage-sas-overview.md)generálta, a betanításhoz használhatja a zip-fájl URL-címét.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Node.js rendellenesség-Kiderítő ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Modell betanítása](#train-a-model)
* [Rendellenességek észlelése](#detect-anomalies)
* [Modell exportálása](#export-model)
* [Modell törlése](#delete-model)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Objektumot hoz létre `anomalyDetectorClient` a végponttal és a hitelesítő adatokkal.

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

### <a name="construct-a-model-result-and-train-model"></a>Modell eredményének és betanítási modellének létrehozása

Először létre kell hoznia egy modell-kérelmet. Győződjön meg arról, hogy a kezdési és befejezési idő igazodik az adatforráshoz.

 Az anomáliák többváltozós API-k használatához az észlelés használata előtt be kell tanítani a saját modelljét. A betanításhoz használt adatmennyiség az idősorozatok egy kötege, minden egyes idősorozatnak CSV formátumúnak kell lennie két oszloppal, egy időbélyeggel és egy értékkel. Az összes idősorozatot egy zip-fájlba kell csomagolni, és fel kell tölteni az [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs)-ba. Alapértelmezés szerint a rendszer a fájlnevet fogja használni az idősorozat változójának ábrázolásához. Azt is megteheti, hogy a fájl egy további meta.jsis tartalmazhatja a zip-fájlban, ha azt szeretné, hogy a változó neve eltér a. zip-fájl nevétől. Ha a [blob sas (közös hozzáférésű aláírás) URL-címet](../../../../storage/common/storage-sas-overview.md)generálta, a betanításhoz használhatja a zip-fájl URL-címét.

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

## <a name="detect-anomalies"></a>Rendellenességek észlelése

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

A betanított modell exportálásához használja a következőt: `exportModelWithResponse` .

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

Az alkalmazást az alábbiakkal hozhatja létre:

```console
gradle build
```
### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `run` célnak megfelelően:

```console
gradle run
```

## <a name="next-steps"></a>Következő lépések

* [Anomália-érzékelő többváltozós ajánlott eljárásai](../../concepts/best-practices-multivariate.md)
