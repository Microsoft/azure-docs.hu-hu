---
title: 'Recept: prediktív karbantartás a nagy adatmennyiséggel rendelkező Cognitive Services'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan hajthatja végre az elosztott anomáliák észlelését a Big adatok Cognitive Services
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a583d74ae0bf421a7a863a65442d250489a2f8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775391"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>Recept: prediktív karbantartás a nagy adatmennyiséggel rendelkező Cognitive Services

Ez a recept bemutatja, hogyan használható az Azure szinapszis Analytics és a Cognitive Services on Apache Spark a IoT-eszközök prediktív karbantartásához. Követjük a [CosmosDB és a szinapszis hivatkozás](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) mintáját. Az egyszerű dolgok megtartása érdekében ebben a receptben közvetlenül egy CSV-fájlból fogjuk beolvasni az adatátvitelt a CosmosDB és a szinapszis-kapcsolaton keresztül. Határozottan javasoljuk, hogy tekintse át a szinapszis hivatkozás mintáját.

## <a name="hypothetical-scenario"></a>Feltételezett forgatókönyv

A feltételezett forgatókönyv egy erőmű, ahol a IoT-eszközök figyelik a [gőzfejlesztő](https://en.wikipedia.org/wiki/Steam_turbine)eszközöket. A IoTSignals gyűjtemény percenkénti (RPM) és megawatt (MW) adatmennyiséggel rendelkezik. A gőzfejlesztő-jelek elemzése folyamatban van, és a rendszer rendellenes jeleket észlel.

Az adatokat véletlenszerű gyakorisággal lehet kiugróan értelmezni. Ezekben az esetekben az RPM-értékek fel lesznek mentve, és az MW kimenete leáll, az áramkör védelme érdekében. Az a gondolat, hogy az azonos időben eltérő, de különböző jelekkel rendelkező adatértékek láthatók.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* [Kiszolgáló nélküli Apache Spark készlettel](../../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) konfigurált [Azure szinapszis-munkaterület](../../../synapse-analytics/quickstart-create-workspace.md)

## <a name="setup"></a>Beállítás

### <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást a fordítónak a [Azure Portal](../../cognitive-services-apis-create-account.md) vagy az [Azure CLI](../../cognitive-services-apis-create-account-cli.md)használatával. További lehetőségek:

- Megtekintheti a  [Azure Portal](https://portal.azure.com/)meglévő erőforrásait.

Jegyezze fel a végpontot és az erőforrás kulcsát, ezért ebben az útmutatóban szüksége lesz rá.

## <a name="enter-your-service-keys"></a>Adja meg a szolgáltatás kulcsait

Kezdjük a kulcs és a hely hozzáadásával.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>Az adatolvasás egy DataFrame

Ezután olvassa el a IoTSignals-fájlt egy DataFrame. Nyisson meg egy új jegyzetfüzetet a szinapszis munkaterületen, és hozzon létre egy DataFrame a fájlból.

```python
df_signals = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Anomáliák észlelésének futtatása a Spark Cognitive Services használatával

A cél olyan példányok megkeresése, amelyekben a IoT-eszközöktől érkező jelek kimutatják a rendellenes értékeket, így láthatjuk, ha valami hiba történik, és a prediktív karbantartás is megtörténik. Ehhez használja a "anomália" detektort a Sparkban:

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

Vessünk egy pillantást az adatpontokra:

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

Ennek a cellának a következőhöz hasonló eredményt kell eredményeznie:

| időbélyeg           |   érték | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | dev-7      | Hamis       |
| 2020-05-01 18:33:52 |    2976 | dev-7      | Hamis       |
| 2020-05-01 18:33:53 |    2714 | dev-7      | Hamis       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>Az egyik eszközön észlelt rendellenességek megjelenítése

IoTSignals.csv több IoT-eszközről származó jeleket tartalmaz. Egy adott eszközre koncentrálunk, és az eszköz rendellenes kimeneteit jeleníti meg.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

Most, hogy létrehoztunk egy dataframe, amely egy adott eszközön észlelt rendellenességeket jelöli, megjeleníthetjük ezeket a rendellenességeket:

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

Ha a művelet sikeres, a kimenet a következőképpen fog kinézni:

![Anomália-detektor ábrázolása](../media/anomaly-output.png)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan végezheti el a prediktív karbantartást az Azure Cognitive Services, az Azure szinapszis Analytics és az Azure CosmosDB használatával. További információ: teljes minta a [githubon](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples).
