---
title: Anomália detektor .NET ügyféloldali kódtár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: ee4dc926269d3ac66243a3953d7e41eb3a30198c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947709"
---
Ismerkedés az anomália-detektor .NET-hez készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot a szolgáltatás által biztosított algoritmusok használatával. Az anomália-detektor szolgáltatás lehetővé teszi, hogy az idősoros adataiban az adatsorozatok adatait automatikusan a legjobb illeszkedő modellekkel találja, függetlenül az iparágtól, a forgatókönyvtől vagy az adatmennyiségtől.

Használja a következőhöz tartozó rendellenesség-Kiderítő ügyféloldali kódtárat a .NET-hez:

* Az idősorozat-adatkészletben lévő rendellenességek észlelése batch-kérelemként
* Az idősorozat legújabb adatpontjának anomália állapotának észlelése
* Az adatkészletben lévő trendek változási pontjainak észlelése.

[Könyvtár-referenciák dokumentációja](https://aka.ms/anomaly-detector-dotnet-ref)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.2)  |  [A kód megkeresése a githubon](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core) jelenlegi verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hozzon létre egy rendellenesség-Kiderítő erőforrást, "  target="_blank"> és hozzon létre egy anomália-detektor erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazásnak a rendellenesség-érzékelő API-hoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>Új .NET Core-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `anomaly-detector-quickstart` . Ez a parancs egy egyszerű "Hello World" projektet hoz létre egyetlen C# forrásfájl használatával: *program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```dotnetcli
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse a következő paranccsal a .NET-hez készült rendellenesség-Kiderítő ügyféloldali kódtárat:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector
```

A projekt könyvtárában nyissa meg a *program.cs* fájlt, és adja hozzá a következőt a használatával `directives` :

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

Az alkalmazás `main()` metódusában hozzon létre változókat az erőforrás Azure-beli helyéhez, valamint a kulcsot környezeti változóként. Ha az alkalmazás elindítása után hozta létre a környezeti változót, az azt futtató szerkesztőt, IDE vagy rendszerhéjat le kell zárnia, majd újra kell töltenie a változó eléréséhez.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Objektummodell

A rendellenesség-Kiderítő ügyfél egy [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) objektum, amely a kulcsot tartalmazó [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials)használatával hitelesíti magát az Azure-ban. Az ügyfél a [EntireDetectAsync ()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync)vagy a [LastDetectAsync ()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync)használatával egy teljes adatkészlet esetében elvégezheti a anomáliák észlelését. A [ChangePointDetectAsync](https://aka.ms/anomaly-detector-dotnet-ref) metódus észleli azokat a pontokat, amelyek egy trend változásait jelzik.

Az idősorozat-információk küldése egy [kérelem](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) -objektumban lévő [pontok](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) sorozata. Az `Request` objektum olyan tulajdonságokat tartalmaz, amelyek leírják az adatok (például a[részletesség](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity) ) és az anomáliák észlelésének paramétereit.

Az anomália-detektor válasza egy [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse), [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse)vagy [changePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) objektum, a használt módszertől függően.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a következőhöz: az anomália-detektor .NET-hez készült ügyféloldali kódtára:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Idősorozat-adatkészlet betöltése fájlból](#load-time-series-data-from-a-file)
* [A teljes adathalmazban észlelt rendellenességek észlelése](#detect-anomalies-in-the-entire-data-set)
* [A legutóbbi adatpont anomália állapotának észlelése](#detect-the-anomaly-status-of-the-latest-data-point)
* [Az adatkészletben lévő változási pontok észlelése](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új metódusban hozza létre az ügyfelet a végponttal és a kulccsal. Hozzon létre egy [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials) objektumot a kulccsal, és használja a végpontján egy [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) objektum létrehozásához.

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>Adatsorozat-adatok betöltése fájlból

A rövid útmutatóhoz tartozó példa adatainak letöltése a [githubról](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. A böngészőben kattintson a jobb gombbal a **RAW** elemre.
2. Kattintson **a hivatkozás mentése másként** elemre.
3. Mentse a fájlt egy. csv-fájlként az alkalmazás könyvtárába.

Ez az idősoros adat. csv-fájlként van formázva, és a rendszer elküldi a rendellenesség-Kiderítő API-nak.

Hozzon létre egy új metódust az idősorozat-adatként való olvasáshoz, és adja hozzá azt egy [kérelem](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) objektumhoz. Hívja meg `File.ReadAllLines()` a fájl elérési útját, és hozzon létre egy listát a [pontok](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point) objektumairól, és minden új sor karakterét. Bontsa ki az értékeket, és válassza el a datestamp a numerikus értékétől, és vegye fel őket egy új `Point` objektumba.

Tegyen egy `Request` objektumot a pontok sorozatával, valamint az `Granularity.Daily` adatpontok [részletességét](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity) (vagy gyakoriságát).

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>A teljes adathalmazban észlelt rendellenességek észlelése

Hozzon létre egy metódust az ügyfél [EntireDetectAsync ()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metódusának meghívásához az `Request` objektummal, és várja meg a választ [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) objektumként. Ha az idősorozat bármely rendellenességet tartalmaz, ismételje meg a válasz [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly) értékeit, és nyomtassa ki a kívánt értékeket `true` . Ezek az értékek a rendellenes adatpontok indexének felelnek meg, ha vannak ilyenek.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>A legutóbbi adatpont anomália állapotának észlelése

Hozzon létre egy metódust az ügyfél [LastDetectAsync ()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) metódusának meghívásához az `Request` objektummal, és várja meg a választ [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) objektumként. Ellenőrizze a válasz [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly) attribútumát annak megállapításához, hogy a legutóbbi adatpont elküldte-e az anomáliát.

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="detect-change-points-in-the-data-set"></a>Az adatkészletben lévő változási pontok észlelése

Hozzon létre egy metódust az ügyfél [DetectChangePointAsync](https://aka.ms/anomaly-detector-dotnet-ref) metódusának meghívásához az `Request` objektummal, és várja meg a választ [ChangePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref) objektumként. Keresse meg a válasz IsChangePoint-értékeit, és nyomtassa ki a kívánt értékeket `true` . Ezek az értékek a trend változási pontjainak felelnek meg, ha vannak ilyenek.

[!code-csharp[DetectChangePoint() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=changePointExample)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `dotnet run` paranccsal az alkalmazás könyvtárából.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
