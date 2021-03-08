---
title: Mérőszámok Advisor Python rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 2ea2e24049a2ff57e284894bc13c44d617f3c661
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445560"
---
[Dokumentáció](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md)  |  [Csomag (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/)  |  [Példák](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" hozzon létre egy mérőszámok Advisor "  target="_blank"> -erőforrást </a> a Azure Portal a metrikai tanácsadó példány üzembe helyezéséhez.  
* Saját SQL-adatbázis idősoros adataival.
  
> [!TIP]
> * A Python metrika Advisor-mintákat a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples)találja.
> * A metrikák Advisor-erőforrása 10 – 30 percet is igénybe vehet, ha a szolgáltatáshoz használni szeretné a szolgáltatási példányt. A sikeres üzembe helyezése után kattintson **az erőforrás keresése** gombra. Az üzembe helyezés után elkezdheti használni a metrikák Advisor-példányát a webes portál és a REST API használatával is.
> * Az Azure Portal REST API URL-címét az erőforrás **Áttekintés** szakaszában találja. Ez így fog kinézni:
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-fájlt, és importálja a következő könyvtárakat.

```python
import os
import datetime
```

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához.

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott metrikai Advisor-erőforrást sikeresen telepítette, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. Az előfizetési kulcsokat és végpontokat az erőforrás- **kezelés** területen találja az erőforrás **kulcs és végpont** lapján. <br><br>Az API-kulcs lekéréséhez nyissa meg a következőt: [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Válassza ki a megfelelőt: **címtár**, **előfizetések** és **munkaterület** az erőforráshoz, majd válassza az első **lépések** lehetőséget. Ezután lekérheti az API-kulcsokat a alkalmazásból [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Objektummodell

A következő osztályok a metrikai tanácsadó Python SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **A következőhöz használatos**: <br> -Incidensek listázása <br> -Az incidensek kiváltó okának listázása <br> – A szolgáltatás által dúsított eredeti idősoros adatok és idősoros adatok beolvasása. <br> – Riasztások listázása <br> – Visszajelzés hozzáadása a modell finomhangolásához |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **A következőket teszi lehetővé:** <br> – Adatcsatornák kezelése <br> -Anomália-észlelési konfigurációk létrehozása, konfigurálása, beolvasása, listázása és törlése <br> -Anomália-riasztási konfigurációk létrehozása, konfigurálása, beolvasása, listázása és törlése <br> – Hookok kezelése  | |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **A metrikák tanácsadója az adatforrásból származik. A a `DataFeed` következőket tartalmazza:** <br> – Időbélyeg <br> – Nulla vagy több dimenzió <br> -Egy vagy több mérték  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | A egy `DataFeedMetric` számszerűsíthető mérték, amely egy adott üzleti folyamat állapotának figyelésére és értékelésére szolgál. Több idősorozat-érték kombinációját is kioszthatja dimenzióra. A webes állapot mérőszáma például a felhasználók számának és az en-us piacnak a dimenzióit is tartalmazhatja. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült metrika Advisor ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Adatcsatorna hozzáadása](#add-a-data-feed)
* [Betöltési állapot keresése](#check-the-ingestion-status)
* [Telepítési észlelési konfiguráció és riasztás konfigurálása](#configure-anomaly-detection)
* [Riasztási konfiguráció létrehozása](#create-an-alert-configuration)
* [Lekérdezési rendellenesség észlelésének eredményei](#query-the-alert)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A példában szereplő ügyfél egy olyan `MetricsAdvisorAdministrationClient` objektum, amely a végpontot és egy olyan objektumot használ, `MetricsAdvisorKeyCredential` amely a kulcsokat tartalmazza. A kód mintájának másolása nem szükséges. A később létrehozott módszerek a-ügyfelet fogják létrehozni. A másodlagos ügyfél neve `MetricsAdvisorClient` további információkkal szolgál az ügyfélről a [dokumentációban](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient).

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Adatcsatorna hozzáadása

Egy új metódusban hozzon létre importálási utasításokat az alábbi példához hasonlóan. Cserélje le a `sql_server_connection_string` értéket a saját SQL Server-alapú kapcsolatok karakterláncára, és cserélje le `query` egy olyan lekérdezésre, amely egy időbélyegen keresztül adja vissza az adatait. A és az értékeket is módosítania kell az `DataFeedmetric` `DataFeedDimension` Egyéni adatok alapján.

> [!IMPORTANT]
> A lekérdezésnek az egyes dimenzió-kombinációk esetében legfeljebb egy rekordot kell visszaadnia minden időbélyegnél. A lekérdezés által visszaadott összes rekordnak azonos időbélyegzővel kell rendelkeznie. A metrikai tanácsadó minden időbélyeg esetében futtatja ezt a lekérdezést az adatok betöltéséhez. További információért és példákért tekintse [meg a gyakori kérdések szakaszt](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) . 

Hozzon létre egy ügyfelet a kulcsokkal és a végponttal, és `client.create_data_feed()` a használatával konfigurálja a nevet, a forrást, a részletességet és a sémát. Beállíthatja a betöltési időt, a kumulatív beállításokat és egyebeket is.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>A betöltési állapot keresése

Egy új metódusban hozzon létre egy importálási utasítást az alábbi példához hasonlóan. Cserélje le a helyére a `data_feed_id` létrehozott adatcsatorna azonosítóját. Hozzon létre egy ügyfelet a kulcsokkal és a végponttal, és használja a betöltési `client.list_data_feed_ingestion_status()` folyamat lekéréséhez. Nyomtassa ki a részleteket, például a legutóbbi aktív és sikeres időbélyeget.


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>Anomáliák észlelésének konfigurálása

Egy új metódusban hozzon létre importálási utasításokat az alábbi példához hasonlóan. Cserélje le a értékét a `metric_id` konfigurálni kívánt METRIKA azonosítójának helyére. Hozzon létre egy ügyfelet a kulcsokkal és a végponttal, és `client.create_detection_configuration` hozzon létre egy új észlelési konfigurációt. A küszöbértékek a anomáliák észlelésére vonatkozó paramétereket határozzák meg.

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>Hook létrehozása

Egy új metódusban hozzon létre importálási utasításokat az alábbi példához hasonlóan. Hozzon létre egy ügyfelet a kulcsokkal és a végponttal, és `client.create_hook()` hozzon létre egy hookot. Adja meg a riasztás küldésére szolgáló e-mailek listáját, valamint egy külső hivatkozást a riasztás fogadásához.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>Riasztási konfiguráció létrehozása

Egy új metódusban hozzon létre importálási utasításokat az alábbi példához hasonlóan. Cserélje le `detection_configuration_id` az azonosítót az anomália-észlelési konfiguráció azonosítójával, és cserélje le `hook_id` a helyére a korábban létrehozott hookot. Hozzon létre egy ügyfelet a kulcsokkal és a végponttal, és `client.create_alert_configuration()` a használatával hozzon létre egy riasztási konfigurációt. 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>A riasztás lekérdezése

Egy új metódusban hozzon létre egy importálási utasítást az alábbi példához hasonlóan. Cserélje le a `alert_id` t a riasztás azonosítójának helyére, és cserélje le `alert_config_id` a beállítást a riasztások konfigurációs azonosítójával. Hozzon létre egy ügyfelet a kulcsokkal és a végponttal, és `client.list_anomalies` a használatával sorolja fel a riasztások rendellenességeit. 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `python` fájlban található paranccsal.

```console
python quickstart-file.py
```