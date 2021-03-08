---
title: Metrikák Advisor C# rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 31cdb1529ce06906aef2f546ab1c173361e7d779
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445165"
---
[Dokumentáció](/dotnet/api/overview/azure/ai.metricsadvisor-readme-pre)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.MetricsAdvisor)  |  [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [.net Core](https://dotnet.microsoft.com/download/dotnet-core)jelenlegi verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" hozzon létre egy mérőszámok Advisor "  target="_blank"> -erőforrást </a> a Azure Portal a metrikai tanácsadó példány üzembe helyezéséhez.  
* Saját SQL-adatbázis idősoros adataival.
   
> [!TIP]
> * A .NET metrika Advisor-mintákat a [githubon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)találja.
> * A metrikák Advisor-erőforrása 10 – 30 percet is igénybe vehet, ha a szolgáltatáshoz használni szeretné a szolgáltatási példányt. A sikeres üzembe helyezése után kattintson **az erőforrás keresése** gombra. Az üzembe helyezés után elkezdheti használni a metrikák Advisor-példányát a webes portál és a REST API használatával is. 
> * Az Azure Portal REST API URL-címét az erőforrás **Áttekintés** szakaszában találja. Ez így fog kinézni:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
   
## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat úgy, hogy a **megoldáskezelő** , majd a **NuGet-csomagok kezelése** lehetőségre kattint a jobb gombbal a projekt megoldásra. A megnyíló csomagkezelő válassza a **Tallózás** lehetőséget, jelölje be az **előzetes verzió** használata jelölőnégyzetet, és keressen rá `Azure.AI.MetricsAdvisor` . Válassza `1.0.0-beta.2` a verzió, majd a **telepítés** lehetőséget. 

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `metrics-advisor-quickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájlban: *program.cs*. 

```console
dotnet new console -n metrics-advisor-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Ha a Visual Studiótól eltérő IDE-t használ, akkor a következő paranccsal telepítheti a metrikai tanácsadó ügyféloldali kódtárat a .NET-hez:

```console
dotnet add package Azure.AI.MetricsAdvisor --version 1.0.0-beta.2
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.MetricsAdvisor_1.0.0-beta.1/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

A projekt könyvtárában nyissa meg a *program.cs* fájlt, és adja hozzá a következő `using` irányelveket:

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.AI.MetricsAdvisor.Administration;
using Azure.AI.MetricsAdvisor;
using Azure.AI.MetricsAdvisor.Models;
```

Az alkalmazás `Main()` metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később fogja létrehozni.

```csharp
static void Main(string[] args){
    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Objektummodell

A következő osztályok a metrikák Advisor C# SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [MetricsAdvisorClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) | **A következőhöz használatos**: <br> -Incidensek listázása <br> -Az incidensek kiváltó okának listázása <br> – A szolgáltatás által dúsított eredeti idősoros adatok és idősoros adatok beolvasása. <br> – Riasztások listázása <br> – Visszajelzés hozzáadása a modell finomhangolásához |
| [MetricsAdvisorAdministrationClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)| **A következőket teszi lehetővé:** <br> – Adatcsatornák kezelése <br> -Anomáliák észlelési konfigurációinak konfigurálása <br> -Anomáliák riasztási konfigurációinak konfigurálása <br> – Hookok kezelése  |
| [DataFeed](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeed.cs)| **A metrikák tanácsadója az adatforrásból származik. A a `DataFeed` következőket tartalmazza:** <br> – Időbélyeg <br> – Nulla vagy több dimenzió <br> -Egy vagy több mérték  |
| [DataFeedMetric](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeedMetric.cs)| A egy `DataFeedMetric` számszerűsíthető mérték, amely egy adott üzleti folyamat állapotának figyelésére és értékelésére szolgál. Több idősorozat-érték kombinációját is kioszthatja dimenzióra. A webes állapot mérőszáma például a felhasználók számának és az en-us piacnak a dimenzióit is tartalmazhatja. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a metrikai tanácsadó ügyféloldali kódtára a .NET-hez:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Adatcsatorna hozzáadása](#add-a-data-feed)
* [A betöltési állapot keresése](#check-the-ingestion-status)
* [Anomáliák észlelésének konfigurálása](#configure-anomaly-detection)
* [Hook létrehozása](#create-a-hook)
* [Riasztási konfiguráció létrehozása](#create-an-alert-configuration)
* [A riasztás lekérdezése](#query-the-alert)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az alkalmazás `Program` osztályában hozzon létre változókat az erőforrás kulcsainak és végpontjának megfelelően.

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott metrikai Advisor-erőforrást sikeresen telepítette, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. Az előfizetési kulcsokat és végpontokat az erőforrás- **kezelés** területen találja az erőforrás **kulcs és végpont** lapján. <br><br>Az API-kulcs lekéréséhez nyissa meg a következőt: [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Válassza ki a megfelelőt: **címtár**, **előfizetések** és **munkaterület** az erőforráshoz, majd válassza az első **lépések** lehetőséget. Ezután lekérheti az API-kulcsokat a alkalmazásból [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

Ha rendelkezik az előfizetéssel és az API-kulcsokkal, hozzon létre egy MetricsAdvisorKeyCredential. A végpont és a kulcs hitelesítő adataival a következőket hozhatja létre [`MetricsAdvisorClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) :

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var client = new MetricsAdvisorClient(new Uri(endpoint), credential);
```

A [`MetricsAdvisorAdministrationClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs) felügyeleti műveletek végrehajtásához a következőket is létrehozhatja:

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var adminClient = new MetricsAdvisorAdministrationClient(new Uri(endpoint), credential);
```

## <a name="add-a-data-feed"></a>Adatcsatorna hozzáadása

A metrikai tanácsadó több típusú adatforrást is támogat. Ebben a példában bemutatjuk, hogyan hozhat létre egy `DataFeed` adatkivonatot egy SQL Server-kiszolgálóról. Cserélje le a `connection_String` értéket a saját SQL Server-alapú kapcsolatok karakterláncára, és cserélje le `query` egy olyan lekérdezésre, amely egy időbélyegen keresztül adja vissza az adatait. A és az értékeket is módosítania kell az `DataFeedMetric` `DataFeedDimension` Egyéni adatok alapján.


```csharp
string sqlServerConnectionString = "<connection_String>";
string sqlServerQuery = "<query>";

var dataFeedName = "Sample data feed";
var dataFeedSource = new SqlServerDataFeedSource(sqlServerConnectionString, sqlServerQuery);
var dataFeedGranularity = new DataFeedGranularity(DataFeedGranularityType.Daily);

var dataFeedMetrics = new List<DataFeedMetric>()
{
    new DataFeedMetric("cost"),
    new DataFeedMetric("revenue")
};
var dataFeedDimensions = new List<DataFeedDimension>()
{
    new DataFeedDimension("category"),
    new DataFeedDimension("city")
};
var dataFeedSchema = new DataFeedSchema(dataFeedMetrics)
{
    DimensionColumns = dataFeedDimensions
};

var ingestionStartTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var dataFeedIngestionSettings = new DataFeedIngestionSettings(ingestionStartTime);

var dataFeed = new DataFeed(dataFeedName, dataFeedSource, dataFeedGranularity, dataFeedSchema, dataFeedIngestionSettings);

Response<string> response = await adminClient.CreateDataFeedAsync(dataFeed);

string dataFeedId = response.Value;

Console.WriteLine($"Data feed ID: {dataFeedId}");

// Note that only the ID of the data feed is known at this point. You can perform another
// service call to GetDataFeedAsync or GetDataFeed to get more information, such as status,
// created time, the list of administrators, or the metric IDs.

Response<DataFeed> response = await adminClient.GetDataFeedAsync(dataFeedId);

DataFeed dataFeed = response.Value;

Console.WriteLine($"Data feed status: {dataFeed.Status.Value}");
Console.WriteLine($"Data feed created time: {dataFeed.CreatedTime.Value}");

Console.WriteLine($"Data feed administrators:");
foreach (string admin in dataFeed.Administrators)
{
    Console.WriteLine($" - {admin}");
}

Console.WriteLine($"Metric IDs:");
foreach (DataFeedMetric metric in dataFeed.Schema.MetricColumns)
{
    Console.WriteLine($" - {metric.MetricName}: {metric.MetricId}");
}
```

## <a name="check-the-ingestion-status"></a>A betöltési állapot keresése

Egy korábban létrehozott betöltési állapotának keresése `DataFeed`

```csharp
string dataFeedId = "<dataFeedId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.Parse("2020-09-09T00:00:00Z");
var options = new GetDataFeedIngestionStatusesOptions(startTime, endTime)
{
    TopCount = 5
};

Console.WriteLine("Ingestion statuses:");
Console.WriteLine();

int statusCount = 0;

await foreach (DataFeedIngestionStatus ingestionStatus in adminClient.GetDataFeedIngestionStatusesAsync(dataFeedId, options))
{
    Console.WriteLine($"Timestamp: {ingestionStatus.Timestamp}");
    Console.WriteLine($"Status: {ingestionStatus.Status}");
    Console.WriteLine($"Service message: {ingestionStatus.Message}");
    Console.WriteLine();

    // Print at most 5 statuses.
    if (++statusCount >= 5)
    {
        break;
    }
}
```

## <a name="configure-anomaly-detection"></a>Anomáliák észlelésének konfigurálása 

Hozzon létre egy anomália-észlelési konfigurációt, amely azt jelzi, hogy az adatpontokat rendellenességeknek kell tekinteni.

```csharp
string metricId = "<metricId>";
string configurationName = "Sample anomaly detection configuration";

var hardThresholdSuppressCondition = new SuppressCondition(1, 100);
var hardThresholdCondition = new HardThresholdCondition(AnomalyDetectorDirection.Down, hardThresholdSuppressCondition)
{
    LowerBound = 5.0
};

var smartDetectionSuppressCondition = new SuppressCondition(4, 50);
var smartDetectionCondition = new SmartDetectionCondition(10.0, AnomalyDetectorDirection.Up, smartDetectionSuppressCondition);

var detectionCondition = new MetricWholeSeriesDetectionCondition()
{
    HardThresholdCondition = hardThresholdCondition,
    SmartDetectionCondition = smartDetectionCondition,
    CrossConditionsOperator = DetectionConditionsOperator.Or
};

var detectionConfiguration = new AnomalyDetectionConfiguration(metricId, configurationName, detectionCondition);

Response<string> response = await adminClient.CreateDetectionConfigurationAsync(detectionConfiguration);

string detectionConfigurationId = response.Value;

Console.WriteLine($"Anomaly detection configuration ID: {detectionConfigurationId}");
```

### <a name="create-a-hook"></a>Hook létrehozása

A metrikai tanácsadó a `EmailNotificationHook` `WebNotificationHook` riasztási értesítésekre való feliratkozás módjaként támogatja a és osztályokat. Ebben a példában bemutatjuk, hogyan hozhat létre egy `EmailNotificationHook` . Az értesítések beszerzésének megkezdéséhez át kell adnia a hookot egy anomália-riasztási konfigurációba. További információkért tekintse meg az [anomália-riasztás konfigurációjának létrehozása](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor#create-an-anomaly-alert-configuration) című témakört.

```csharp
string hookName = "Sample hook";
var emailsToAlert = new List<string>()
{
    "email1@sample.com",
    "email2@sample.com"
};

var emailHook = new EmailNotificationHook(hookName, emailsToAlert);

Response<string> response = await adminClient.CreateHookAsync(emailHook);

string hookId = response.Value;

Console.WriteLine($"Hook ID: {hookId}");
```

##  <a name="create-an-alert-configuration"></a>Riasztási konfiguráció létrehozása

Hozzon létre egy `AnomalyAlertConfiguration` -t, hogy tájékoztassa a szolgáltatást, mely rendellenességeket kell elindítania.

```csharp
string hookId = "<hookId>";
string anomalyDetectionConfigurationId = "<anomalyDetectionConfigurationId>";

string configurationName = "Sample anomaly alert configuration";
var idsOfHooksToAlert = new List<string>() { hookId };

var scope = MetricAnomalyAlertScope.GetScopeForWholeSeries();
var metricAlertConfigurations = new List<MetricAnomalyAlertConfiguration>()
{
    new MetricAnomalyAlertConfiguration(anomalyDetectionConfigurationId, scope)
};

AnomalyAlertConfiguration alertConfiguration = new AnomalyAlertConfiguration(configurationName, idsOfHooksToAlert, metricAlertConfigurations);

Response<string> response = await adminClient.CreateAlertConfigurationAsync(alertConfiguration);

string alertConfigurationId = response.Value;

Console.WriteLine($"Alert configuration ID: {alertConfigurationId}");
```

### <a name="query-the-alert"></a>A riasztás lekérdezése

Tekintse át az adott anomália-riasztási konfiguráció által létrehozott riasztásokat.

```csharp
string anomalyAlertConfigurationId = "<anomalyAlertConfigurationId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.UtcNow;
var options = new GetAlertsOptions(startTime, endTime, AlertQueryTimeMode.AnomalyTime)
{
    TopCount = 5
};

int alertCount = 0;

await foreach (AnomalyAlert alert in client.GetAlertsAsync(anomalyAlertConfigurationId, options))
{
    Console.WriteLine($"Alert created at: {alert.CreatedTime}");
    Console.WriteLine($"Alert at timestamp: {alert.Timestamp}");
    Console.WriteLine($"Id: {alert.Id}");
    Console.WriteLine();

    // Print at most 5 alerts.
    if (++alertCount >= 5)
    {
        break;
    }
}
```

Ha már ismeri a riasztás AZONOSÍTÓját, sorolja fel a riasztást kiváltó [rendellenességeket](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/README.md#data-point-anomaly) .

```csharp
string alertConfigurationId = "<alertConfigurationId>";
string alertId = "<alertId>";

var options = new GetAnomaliesForAlertOptions() { TopCount = 3 };

int anomalyCount = 0;

await foreach (DataPointAnomaly anomaly in client.GetAnomaliesAsync(alertConfigurationId, alertId, options))
{
    Console.WriteLine($"Anomaly detection configuration ID: {anomaly.AnomalyDetectionConfigurationId}");
    Console.WriteLine($"Metric ID: {anomaly.MetricId}");
    Console.WriteLine($"Anomaly at timestamp: {anomaly.Timestamp}");
    Console.WriteLine($"Anomaly detected at: {anomaly.CreatedTime}");
    Console.WriteLine($"Status: {anomaly.Status}");
    Console.WriteLine($"Severity: {anomaly.Severity}");
    Console.WriteLine("Series key:");

    foreach (KeyValuePair<string, string> keyValuePair in anomaly.SeriesKey.AsDictionary())
    {
        Console.WriteLine($"  Dimension '{keyValuePair.Key}': {keyValuePair.Value}");
    }

    Console.WriteLine();

    // Print at most 3 anomalies.
    if (++anomalyCount >= 3)
    {
        break;
    }
}
```


### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```dotnet
dotnet run
```