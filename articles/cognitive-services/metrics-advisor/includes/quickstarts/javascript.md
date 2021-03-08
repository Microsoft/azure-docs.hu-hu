---
title: Metrikák Advisor JavaScript-útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: f34720697e545a4d8a5119605db2db70dff6c233
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444976"
---
[Dokumentáció](/java/api/overview/azure/ai-metricsadvisor-readme)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/ai-metrics-advisor)  |  [Példák](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Node.js](https://nodejs.org/) aktuális verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" hozzon létre egy mérőszámok Advisor "  target="_blank"> -erőforrást </a> a Azure Portal a metrikai tanácsadó példány üzembe helyezéséhez.  
* Saját SQL-adatbázis idősoros adataival.
  
> [!TIP]
> * A JavaScript metrika Advisor-mintákat a [githubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)találja.
> * A metrikák Advisor-erőforrása 10 – 30 percet is igénybe vehet, ha a szolgáltatáshoz használni szeretné a szolgáltatási példányt. A sikeres üzembe helyezése után kattintson **az erőforrás keresése** gombra. Az üzembe helyezés után elkezdheti használni a metrikák Advisor-példányát a webes portál és a REST API használatával is. 
> * Az Azure Portal REST API URL-címét az erőforrás **Áttekintés** szakaszában találja. Ez így fog kinézni:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
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

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `@azure/ai-metrics-advisor` NPM csomagot:

```console
npm install @azure/ai-metrics-advisor@1.0.0-beta.2
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

Hozzon létre egy nevű fájlt `index.js` , és importálja a következő könyvtárakat:

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/javascript), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. 

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott metrikai Advisor-erőforrást sikeresen telepítette, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. Az előfizetési kulcsokat és végpontokat az erőforrás- **kezelés** területen találja az erőforrás **kulcs és végpont** lapján. <br><br>Az API-kulcs lekéréséhez nyissa meg a következőt: [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Válassza ki a megfelelőt: **címtár**, **előfizetések** és **munkaterület** az erőforráshoz, majd válassza az első **lépések** lehetőséget. Ezután lekérheti az API-kulcsokat a alkalmazásból [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a metrikai tanácsadó JavaScript SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| MetricsAdvisorClient | **A következőhöz használatos**: <br> -Incidensek listázása <br> -Az incidensek kiváltó okának listázása <br> – A szolgáltatás által dúsított eredeti idősoros adatok és idősoros adatok beolvasása. <br> – Riasztások listázása <br> – Visszajelzés hozzáadása a modell finomhangolásához |
| MetricsAdvisorAdministrationClient | **A következőket teszi lehetővé:** <br> – Adatcsatornák kezelése <br> -Anomália-riasztási konfigurációk létrehozása, konfigurálása, beolvasása, listázása és törlése <br> – Hookok kezelése  |
| DataFeed | **A metrikák tanácsadója az adatforrásból származik. A a `DataFeed` következőket tartalmazza:** <br> – Időbélyeg <br> – Nulla vagy több dimenzió <br> -Egy vagy több mérték  |
| DataFeedMetric | A egy `DataFeedMetric` számszerűsíthető mérték, amely egy adott üzleti folyamat állapotának figyelésére és értékelésére szolgál. Több idősorozat-érték kombinációját is kioszthatja dimenzióra. A webes állapot mérőszáma például a felhasználók számának és az en-us piacnak a dimenzióit is tartalmazhatja. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a metrikai tanácsadó ügyféloldali kódtára a JavaScripthez:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Adatcsatorna hozzáadása](#add-a-data-feed)
* [Betöltési állapot keresése](#check-ingestion-status)
* [Anomáliák észlelésének konfigurálása](#configure-anomaly-detection)
* [Hook létrehozása](#create-a-hook)
* [Riasztási konfiguráció létrehozása](#create-an-alert-configuration)
* [A riasztás lekérdezése](#query-the-alert)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A két kulcs és a végpont címe után a MetricsAdvisorKeyCredential osztály használatával hitelesítheti az ügyfeleket az alábbiak szerint:

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>Adatcsatorna hozzáadása

A metrikai tanácsadó támogatja a különböző típusú adatforrások összekapcsolását. Itt látható egy példa az adatok SQL Serverból való betöltésére.

Cserélje le a `sql_server_connection_string` értéket a saját SQL Server-alapú kapcsolatok karakterláncára, és cserélje le `query` egy olyan lekérdezésre, amely egy időbélyegen keresztül adja vissza az adatait. A és az értékeket is módosítania kell az `metric` `dimension` Egyéni adatok alapján.

> [!IMPORTANT]
> A lekérdezésnek az egyes dimenzió-kombinációk esetében legfeljebb egy rekordot kell visszaadnia minden időbélyegnél. A lekérdezés által visszaadott összes rekordnak azonos időbélyegzővel kell rendelkeznie. A metrikai tanácsadó minden időbélyeg esetében futtatja ezt a lekérdezést az adatok betöltéséhez. További információért és példákért tekintse [meg a gyakori kérdések szakaszt](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  apiKey ="<paste-your-metrics-advisor-api-key-here>";
  endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  console.log("Creating Datafeed...");
  const dataFeed = {
    name: "test_datafeed_" + new Date().getTime().toString(),
    source: {
      dataSourceType: "SqlServer",
      dataSourceParameter: {
        connectionString: sqlServerConnectionString,
        query: sqlServerQuery
      }
    },
    granularity: {
      granularityType: "Daily"
    },
    schema: {
      metrics: [
        {
          name: "revenue",
          displayName: "revenue",
          description: "Metric1 description"
        },
        {
          name: "cost",
          displayName: "cost",
          description: "Metric2 description"
        }
      ],
      dimensions: [
        { name: "city", displayName: "city display" },
        { name: "category", displayName: "category display" }
      ],
      timestampColumn: null
    },
    ingestionSettings: {
      ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
      ingestionStartOffsetInSeconds: 0,
      dataSourceRequestConcurrency: -1,
      ingestionRetryDelayInSeconds: -1,
      stopRetryAfterInSeconds: -1
    },
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    adminEmails: ["xyz@example.com"]
  };
  const result = await adminClient.createDataFeed(dataFeed);

  return result;
}
```

## <a name="check-ingestion-status"></a>Betöltési állapot keresése

Az adatfeldolgozás megkezdése után ellenőrizhető a betöltési állapot.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  const iterator = adminClient.listDataFeedIngestionStatus(datafeedId, startTime, endTime);
  for await (const status of iterator) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>Anomáliák észlelésének konfigurálása

A anomáliák észlelési konfigurációjában meg kell határozni, hogy az idősorozat egy pontja anomália-e. Az alapértelmezett észlelési konfigurációt a rendszer automatikusan alkalmazza az egyes mérőszámokra, az adatokhoz használt észlelési módokat testreszabott anomália-észlelési konfiguráció létrehozásával hangolhatja.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  const detectionConfig = {
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  };
  return await adminClient.createDetectionConfig(detectionConfig);
}
```

### <a name="create-a-hook"></a>Hook létrehozása

A hookokat a valós idejű riasztásokra való előfizetéshez használjuk. Ebben a példában egy webhookot hozunk létre a metrikai tanácsadó szolgáltatáshoz, hogy ELKÜLDHESSE a riasztást.

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts", // you must enter a valid webhook url to post the alert payload
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>Riasztási konfiguráció létrehozása

Ebből a példából megtudhatja, hogyan konfigurálhat egy riasztást, és hogy a riasztások célhelyként milyen hookokat kell használni.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const anomalyAlertConfig = {
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [
      {
        detectionConfigurationId: detectionConfigId,
        alertScope: {
          scopeType: "All"
        },
        alertConditions: {
          severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
        },
        snoozeCondition: {
          autoSnooze: 0,
          snoozeScope: "Metric",
          onlyForSuccessive: true
        }
      }
    ],
    hookIds,
    description: "Alerting config description"
  };
  return await adminClient.createAlertConfig(anomalyAlertConfig);
}
```

## <a name="query-the-alert"></a>A riasztás lekérdezése

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alerts = [];
  const iterator = client.listAlerts(alertConfigId, startTime, endTime, "AnomalyTime");
  for await (const alert of iterator) {
    alerts.push(alert);
  }

  return alerts;
}

async function queryAnomaliesByAlert(client, alert) {
  console.log(
    `Listing anomalies for alert configuration '${alert.alertConfigId}' and alert '${alert.id}'`
  );
  const iterator = client.listAnomalies(alert);
  for await (const anomaly of iterator) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.seriesKey.dimension} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `node` fájlban található paranccsal.

```console
node index.js
```
