---
title: 'Recept: intelligens Art-kutatás a Big-adatCognitive Servicesokkal'
titleSuffix: Azure Cognitive Services
description: Ez a recept bemutatja, hogyan hozhat létre kereshető Art-adatbázist Azure Search és MMLSpark használatával.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94363289"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>Recept: intelligens Art-kutatás a Big-adatCognitive Servicesokkal

Ebben a példában a Big adatokhoz tartozó Cognitive Services fogjuk használni intelligens megjegyzéseket a Metropolitan Museum of Art (MET) nevű nyílt hozzáférésű gyűjteményhez. Ez lehetővé teszi, hogy intelligens keresőmotort hozzon létre a Azure Search manuális jegyzetek nélkül is. 

## <a name="prerequisites"></a>Előfeltételek

* Computer Vision és Cognitive Search előfizetési kulcsának kell lennie. A Computer Visionre való előfizetéshez és a kulcs beszerzéséhez kövesse az [Cognitive Services fiók létrehozása](../../cognitive-services-apis-create-account.md) című témakör utasításait.
  > [!NOTE]
  > A díjszabással kapcsolatos információkért lásd: [Azure Cognitive Search](https://azure.microsoft.com/services/search/#pricing).

## <a name="import-libraries"></a>Tárak importálása

A következő parancs futtatásával importálhatja a recepthez tartozó kódtárakat.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>Előfizetési kulcsok beállítása

A következő parancs futtatásával állíthatja be a szolgáltatási kulcsok változóit. Szúrja be Computer Vision és az Azure Cognitive Search előfizetési kulcsait.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>Az információk beolvasása

Futtassa a következő parancsot az adatok betöltéséhez a MET nyitott hozzáférési gyűjteményéből.

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>A képek elemzése

A következő parancs futtatásával Computer Visiont használhat a MET nyílt hozzáférésű műalkotások gyűjteményében. Ennek eredményeképpen vizuális funkciókat fog kapni a műalkotásokból.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>A keresési index létrehozása

Futtassa a következő parancsot az eredmények Azure Search való írásához, hogy a Computer Visionokból származó, dúsított metaadatokat tartalmazó keresőmotort hozzon létre.

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>A keresési index lekérdezése

Futtassa a következő parancsot a Azure Search index lekérdezéséhez.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan használhatja a [Cognitive Services for Big-adatmennyiséget a anomáliák észlelése](anomaly-detection.md)érdekében.