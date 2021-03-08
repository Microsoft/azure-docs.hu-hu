---
title: Metrikai Advisor – Java rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 316a27c6b63d71fb87649524adb4b61bd2bd133b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445394"
---
[Dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src)  |  Összetevő [(Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)  |  [Példák](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" hozzon létre egy mérőszámok Advisor "  target="_blank"> -erőforrást </a> a Azure Portal a metrikai tanácsadó példány üzembe helyezéséhez.  
* Saját SQL-adatbázis idősoros adataival.
  
  
> [!TIP]
> * Java metrikai Advisor-mintákat a [githubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)talál.
> * A metrikák Advisor-erőforrása 10 – 30 percet is igénybe vehet, ha a szolgáltatáshoz használni szeretné a szolgáltatási példányt. A sikeres üzembe helyezése után kattintson **az erőforrás keresése** gombra. Az üzembe helyezés után elkezdheti használni a metrikák Advisor-példányát a webes portál és a REST API használatával is. 
> * Az Azure Portal REST API URL-címét az erőforrás **Áttekintés** szakaszában találja. Ez így fog kinézni:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
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
    compile("com.azure:azure-ai-metricsadvisor:1.0.0-beta.2")
}
```

### <a name="create-a-java-file"></a>Java-fájl létrehozása

Hozzon létre egy mappát a minta alkalmazáshoz. A munkakönyvtárból futtassa a következő parancsot:

```console
mkdir -p src/main/java
```

Navigáljon az új mappára, és hozzon létre egy *MetricsAdvisorQuickstarts. Java* nevű fájlt. Nyissa meg a kívánt szerkesztőben vagy IDE, és adja hozzá a következő `import` utasításokat:

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

Az alkalmazás `MetricsAdvisorQuickstarts` osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára.


> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott metrikai Advisor-erőforrást sikeresen telepítette, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. Az előfizetési kulcsokat és végpontokat az erőforrás- **kezelés** területen találja az erőforrás **kulcs és végpont** lapján. <br><br>Az API-kulcs lekéréséhez nyissa meg a következőt: [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Válassza ki a megfelelőt: **címtár**, **előfizetések** és **munkaterület** az erőforráshoz, majd válassza az első **lépések** lehetőséget. Ezután lekérheti az API-kulcsokat a alkalmazásból [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

Az alkalmazás `Main()` metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később hozza létre.

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Objektummodell

A következő osztályok a metrikai tanácsadó Java SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | **A következőhöz használatos**: <br> -Anomália-incidensek listázása <br> -Az incidensek kiváltó okának listázása <br> – A szolgáltatás által dúsított eredeti idősoros adatok és idősoros adatok beolvasása. <br> – Riasztások listázása <br> – Visszajelzés hozzáadása a modell finomhangolásához |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| **A következőket teszi lehetővé:** <br> – Adatcsatornák kezelése <br> -Anomáliák észlelési konfigurációinak konfigurálása <br> -Anomáliák riasztási konfigurációinak konfigurálása <br> – Hookok kezelése  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeed.html) | **A metrikák tanácsadója az adatforrásból származik. A a `DataFeed` következőket tartalmazza:** <br> – Időbélyeg <br> – Nulla vagy több dimenzió <br> -Egy vagy több mérték  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeedMetric.html) | A egy `DataFeedMetric` számszerűsíthető mérték, amely egy adott üzleti folyamat állapotának figyelésére és értékelésére szolgál. Több idősorozat-érték kombinációját is kioszthatja dimenzióra. A webes állapot mérőszáma például a felhasználók számának és az en-us piacnak a dimenzióit is tartalmazhatja. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a metrikai tanácsadó ügyféloldali kódtára Javához:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Adatcsatorna hozzáadása](#add-a-data-feed)
* [A betöltési állapot keresése](#check-the-ingestion-status)
* [Anomáliák észlelésének konfigurálása](#configure-anomaly-detection)
* [Hook létrehozása](#create-a-hook)
* [Riasztási konfiguráció létrehozása](#create-an-alert-configuration)
* [A riasztás lekérdezése](#query-the-alert)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>Metrikai tanácsadói ügyfél létrehozása a MetricsAdvisorKeyCredential használatával

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>Metrika-felügyeleti ügyfél létrehozása a MetricsAdvisorKeyCredential használatával

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>Adatcsatorna hozzáadása

Cserélje le a `sql_server_connection_string` értéket a saját SQL Server-alapú kapcsolatok karakterláncára, és cserélje le `query` egy olyan lekérdezésre, amely egy időbélyegen keresztül adja vissza az adatait. A és az értékeket is módosítania kell az `DataFeedMetric` `DataFeedDimension` Egyéni adatok alapján.

> [!IMPORTANT]
> A lekérdezésnek az egyes dimenzió-kombinációk esetében legfeljebb egy rekordot kell visszaadnia minden időbélyegnél. A lekérdezés által visszaadott összes rekordnak azonos időbélyegzővel kell rendelkeznie. A metrikai tanácsadó minden időbélyeg esetében futtatja ezt a lekérdezést az adatok betöltéséhez. További információért és példákért tekintse [meg a gyakori kérdések szakaszt](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 

```java
DataFeed dataFeed = new DataFeed()
    .setName("dataFeedName")
    .setSource(new MySqlDataFeedSource("sql_server_connection_string", "query"))
    .setGranularity(new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY))
    .setSchema(new DataFeedSchema(
        Arrays.asList(
            new DataFeedMetric().setName("cost"),
            new DataFeedMetric().setName("revenue")
        )).setDimensions(
        Arrays.asList(
            new DataFeedDimension().setName("city"),
            new DataFeedDimension().setName("category")
        ))
    )
    .setIngestionSettings(new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")))
    .setOptions(new DataFeedOptions()
        .setDescription("data feed description")
        .setRollupSettings(new DataFeedRollupSettings()
            .setRollupType(DataFeedRollupType.AUTO_ROLLUP)));
final DataFeed createdSqlDataFeed = metricsAdvisorAdminClient.createDataFeed(dataFeed);

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(System.out::println);
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());

if (SQL_SERVER_DB == createdSqlDataFeed.getSourceType()) {
    System.out.printf("Data feed sql server query: %s%n",
        ((SQLServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
}
```

## <a name="check-the-ingestion-status"></a>A betöltési állapot keresése

Ez a példa egy előzőleg megadott adatcsatorna-forrás betöltési állapotát ellenőrzi.

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdminClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>Anomáliák észlelésének konfigurálása 

Ez a példa azt szemlélteti, hogy egy felhasználó hogyan konfigurálhatja az adatrendellenesség-észlelési konfigurációt az adatkezeléshez.

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.BOTH)
    .setChangePercentage(20)
    .setShiftPoint(10)
    .setWithinRange(true)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.DOWN)
    .setLowerBound(5.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(1));

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.UP)
    .setSensitivity(10.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdminClient.createMetricAnomalyDetectionConfig(
        metricId,
        new AnomalyDetectionConfiguration("My dataPoint anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setCrossConditionOperator(DetectionConditionsOperator.OR))
    );
```

## <a name="create-a-hook"></a>Hook létrehozása

Ez a példa egy e-mail-hookot hoz létre, amely anomália incidensi riasztásokat fogad.

```java
NotificationHook emailNotificationHook = new EmailNotificationHook("email Hook")
    .setDescription("my email Hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://example.com/handleAlerts"); // you must enter a valid webhook url to post the alert payload

final NotificationHook notificationHook = metricsAdvisorAdminClient.createHook(emailNotificationHook);
EmailNotificationHook createdEmailHook = (EmailNotificationHook) notificationHook;
System.out.printf("Email Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Email Hook name: %s%n", createdEmailHook.getName());
System.out.printf("Email Hook description: %s%n", createdEmailHook.getDescription());
System.out.printf("Email Hook external Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Email Hook emails to alert: %s%n",
    String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>Riasztási konfiguráció létrehozása

Ez a példa azt mutatja be, hogy a felhasználó hogyan konfigurálhat riasztási konfigurációt az észlelt rendellenességekkel kapcsolatban.

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdminClient.createAnomalyAlertConfig(
        new AnomalyAlertConfiguration("My anomaly alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityRangeCondition(new SeverityCondition()
                                .setMaxAlertSeverity(AnomalySeverity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>A riasztás lekérdezése

Ez a példa azt szemlélteti, hogy a felhasználók hogyan tudják lekérdezni a riasztások észlelési konfigurációjában elindított riasztásokat, és rendellenességeket kapjanak a riasztáshoz.

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
final OffsetDateTime startTime = OffsetDateTime.parse("2020-01-01T00:00:00Z");
final OffsetDateTime endTime = OffsetDateTime.parse("2020-09-09T00:00:00Z");
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
        startTime, endTime)
    .forEach(alert -> {
        System.out.printf("AnomalyAlert Id: %s%n", alert.getId());
        System.out.printf("AnomalyAlert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("DataPoint Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("DataPoint Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("DataPoint Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("DataPoint Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

Az alkalmazást az alábbiakkal hozhatja létre:

```console
gradle build
```
### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `run` célnak megfelelően:

```console
gradle run
```