---
title: 'Oktatóanyag: gépi tanulási alkalmazások készítése a Microsoft Machine Learning for Apache Spark (előzetes verzió)'
description: Ismerje meg, hogyan hozhat létre gépi tanulási alkalmazásokat az Azure szinapszis Analyticsben a Microsoft Machine Learning for Apache Spark használatával.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruixinxu
ms.author: ruxu
ms.openlocfilehash: 5258d8f16e288e7df7e1286eb1902cc6ba6d10f7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227721"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>Oktatóanyag: gépi tanulási alkalmazások készítése a Microsoft Machine Learning for Apache Spark (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan hozhat létre gépi tanulási alkalmazásokat a Microsoft Machine Learning for Apache Spark ([MMLSpark](https://github.com/Azure/mmlspark)) használatával. A MMLSpark számos mély tanulási és adatelemzési eszközt, például az [Azure Cognitive Services](../../cognitive-services/big-data/cognitive-services-for-big-data.md), a [OpenCV](https://opencv.org/), a [LightGBM](https://github.com/Microsoft/LightGBM) és sok más, a Apache Spark elosztott gépi tanulási megoldását bővíti ki.  A MMLSpark lehetővé teszi, hogy a különböző Spark-adatforrásokból származó hatékony és rugalmasan méretezhető prediktív és analitikai modelleket építsen ki.
A szinapszis Spark beépített MMLSpark-kódtárakat biztosít, többek között a következőket:

- [Vowpal Wabbit](https://github.com/Azure/mmlspark/blob/master/docs/vw.md) – a gépi tanuláshoz készült Library-szolgáltatások lehetővé teszik a szöveges elemzések, például a tweetek elemzését.
- [Cognitive Services a Sparkban](https://github.com/Azure/mmlspark/blob/master/docs/cogsvc.md) – az Azure Cognitive Services funkcióinak SparkML-folyamatokban való összevonásához a megoldás kialakításához a kognitív adatmodellezési szolgáltatások, például a anomáliák észlelése érdekében.
- [LightGBM](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) – a LightGBM egy olyan gradiens-növelő keretrendszer, amely faalapú tanulási algoritmusokat használ. Ez a megoldás elosztott és nagyobb hatékonyságot biztosít.
- Feltételes KNN – skálázható KNN-modellek feltételes lekérdezésekkel.
- [Http on Spark](https://github.com/Azure/mmlspark/blob/master/docs/http.md) – a Spark és a HTTP protokollon alapuló kisegítő lehetőségek integrálásával lehetővé teszi az elosztott szolgáltatások előkészítését.

Ez az oktatóanyag a MMLSpark Azure Cognitive Servicest használó mintákat ismerteti 

- Text Analytics – megszerezheti a mondatok egy halmazának hangulatát (vagy hangulatát).
- Computer Vision – beolvassa a képek egy halmazához társított címkéket (egyszavas leírásokat).
- Bing Image Search – megkeresheti a weben a természetes nyelvű lekérdezéshez kapcsolódó rendszerképeket.
- Anomália-detektor – a rendellenességek észlelése egy idősorozat-adathalmazon belül.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek 

- Az [Azure szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy Azure Data Lake Storage Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A Data Lake Storage Gen2 fájlrendszer *tárolási blob-Adatközreműködőinek* kell lennie.
- Spark-készlet az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: [Spark-készlet létrehozása az Azure szinapszisban](../quickstart-create-sql-pool-studio.md).
- Az oktatóanyag [konfigurálása Cognitive Services az Azure szinapszisban](./tutorial-configure-cognitive-services-synapse.md)című témakörben leírt előzetes konfigurációs lépések.


## <a name="get-started"></a>Bevezetés
Első lépésként importálja a mmlspark és a configurate szolgáltatás kulcsait. 

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# Your linked key vault for Synapse workspace
key_vault = "YOUR_KEY_VAULT_NAME"


cognitive_service_key = mssparkutils.credentials.getSecret(key_vault, service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret(key_vault, bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret(key_vault, anomaly_key)

```


## <a name="text-analytics-sample"></a>Text Analytics-minta

A [text Analytics](../../cognitive-services/text-analytics/index.yml) szolgáltatás számos algoritmust biztosít az intelligens adatok szövegből való kinyeréséhez. Megtalálhatja például az adott bemeneti szöveg hangulatát. A szolgáltatás 0,0 és 1,0 közötti pontszámot ad vissza, ahol az alacsony pontszámok negatív hangulatot és magas pontszámot jeleznek a pozitív hangulat alapján. Ez a példa három egyszerű mondatot használ, és visszaadja az egyes minták hangulatát.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Várt eredmények

|szöveg | hangulatelemzés|
|--|--|
| Ez a Rush Hour-forgalom csalódott vagyok. | negatív |
| Ez egy kutya | semleges |
| Boldog vagyok, ma, a napsütötte! | pozitív |

## <a name="computer-vision-sample"></a>Számítógépes jövőkép minta
[Computer Vision](../../cognitive-services/computer-vision/index.yml) a lemezképek elemzésével azonosíthatja a struktúrát, például az arcokat, az objektumokat és a természetes nyelvi leírásokat. Ebben a példában a következő képet címkézjük. A címkék egyszavas leírások a képen látható dolgokról, például a felismerhető objektumokról, a személyekről, a díszletekről és a műveletekről.


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Várt eredmények

|image | tags|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [korcsolyázás, személy, férfi, Outdoor, lovaglás, sport, gördeszka, fiatal, tábla, ing, levegő, Park, fiú, oldal, ugrás, rámpa, trükk, művelet, repülés] |

## <a name="bing-image-search-sample"></a>Bing képkeresési minta
[Bing Image Search](../../cognitive-services/bing-image-search/overview.md) megkeresi a weben a felhasználó természetes nyelvi lekérdezéséhez kapcsolódó rendszerképek lekéréséhez. Ebben a példában egy szöveges lekérdezést használunk, amely idézőjelekkel rendelkező képeket keres. A lekérdezéshez kapcsolódó fényképeket tartalmazó kép URL-címeinek listáját adja vissza.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Várt eredmények

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Rendellenesség-érzékelő minta

Az [anomália detektor](../../cognitive-services/anomaly-detector/index.yml) kiválóan használható a szabálytalanságok észlelésére az idősorozat-adataiban. Ebben a példában a szolgáltatást használjuk a teljes idősorozatban található anomáliák keresésére.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Várt eredmények

|időbélyeg | érték | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826,0|hamis|
|1972-02-01T00:00:00Z|799,0|hamis|
|1972-03-01T00:00:00Z|890,0|hamis|
|1972-04-01T00:00:00Z|900,0|hamis|
|1972-05-01T00:00:00Z|766,0|hamis|
|1972-06-01T00:00:00Z|805,0|hamis|
|1972-07-01T00:00:00Z|821,0|hamis|
|1972-08-01T00:00:00Z|20000,0|true|
|1972-09-01T00:00:00Z|883,0|hamis|
|1972-10-01T00:00:00Z|898,0|hamis|
|1972-11-01T00:00:00Z|957,0|hamis|
|1972-12-01T00:00:00Z|924,0|hamis|
|1973-01-01T00:00:00Z|881,0|hamis|
|1973-02-01T00:00:00Z|837,0|hamis|
|1973-03-01T00:00:00Z|9000,0|true|

## <a name="next-steps"></a>Következő lépések

* [Tekintse meg a szinapszis-minta jegyzetfüzeteket](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [MMLSpark GitHub-adattár](https://github.com/Azure/mmlspark)
