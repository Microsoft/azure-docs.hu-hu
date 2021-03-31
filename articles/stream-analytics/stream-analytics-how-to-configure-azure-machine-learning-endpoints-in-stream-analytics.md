---
title: Azure Machine Learning Studio (klasszikus) végpontok használata a Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan használhatók a gépi nyelvű felhasználó által definiált függvények a Azure Stream Analyticsban.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/11/2019
ms.openlocfilehash: 1dc85cb10a9e4300c57ad03900d8c8924988c6d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588120"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics"></a>Azure Machine Learning Studio (klasszikus) integráció Stream Analytics
Stream Analytics támogatja a felhasználó által definiált függvényeket, amelyek Azure Machine Learning Studio (klasszikus) végpontokat hívnak meg. A szolgáltatás REST API támogatását a [Stream Analytics REST API könyvtárban](/rest/api/streamanalytics/)részletesen ismertetjük. Ez a cikk a funkció sikeres megvalósításához szükséges kiegészítő információkat nyújt Stream Analyticsban. Egy oktatóanyag is közzé lett téve, és [itt](stream-analytics-machine-learning-integration-tutorial.md)érhető el.

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Áttekintés: Azure Machine Learning Studio (klasszikus) terminológia
A Microsoft Azure Machine Learning Studio (klasszikus) együttműködésen alapuló, fogd és vidd típusú eszközt biztosít az adatain alapuló prediktív elemzési megoldások létrehozásához, teszteléséhez és üzembe helyezéséhez. Az eszköz neve *Azure Machine learning Studio (klasszikus)*. A Studio (klasszikus) használatos a gépi tanulási erőforrásokkal való kommunikációhoz, valamint a tervezéshez való egyszerű létrehozáshoz, teszteléshez és iterációhoz. Ezek az erőforrások és a definícióik alább találhatók.

* **Munkaterület**: a *munkaterület* egy olyan tároló, amely az összes többi gépi tanulási erőforrást egy tárolóban tárolja a felügyelethez és a felügyelethez.
* **Kísérlet**: az adatszakértők által létrehozott *kísérletek* az adathalmazok felhasználására és a gépi tanulási modellek betanítására szolgálnak.
* **Végpont**: a *végpontok* a funkciók bemenetként való elvégzéséhez használt Studio (klasszikus) objektum, amely egy adott gépi tanulási modellt alkalmaz, és a mutató kimenetet adja vissza.
* **Pontozási webszolgáltatás**: a *pontozási webszolgáltatás* a fent említett végpontok gyűjteménye.

Mindegyik végpont API-kat tartalmaz a kötegelt végrehajtáshoz és a szinkron végrehajtáshoz. Stream Analytics szinkron végrehajtást használ. Az adott szolgáltatás neve a [kérelem/válasz szolgáltatás](../machine-learning/classic/consume-web-services.md) Azure Machine learning Studio (klasszikus).

## <a name="studio-classic-resources-needed-for-stream-analytics-jobs"></a>Stream Analytics feladatokhoz szükséges Studio (klasszikus) erőforrások
Stream Analytics a feladatok feldolgozását, a kérelem/válasz végpontot, egy [apikey](../machine-learning/classic/consume-web-services.md)és egy hencegő definíciót a sikeres végrehajtáshoz. Stream Analytics rendelkezik egy további végponttal, amely létrehozza a hencegő végpont URL-címét, megkeresi a felületet, és egy alapértelmezett UDF-definíciót ad vissza a felhasználónak.

## <a name="configure-a-stream-analytics-and-studio-classic-udf-via-rest-api"></a>Stream Analytics és Studio (klasszikus) UDF konfigurálása REST API használatával
A REST API-k használatával beállíthatja a feladatot a Studio (klasszikus) függvények meghívásához. Ennek lépései a következők:

1. Stream Analytics-feladat létrehozása
2. Bemenet megadása
3. Kimenet meghatározása
4. Felhasználó által definiált függvény (UDF) létrehozása
5. Az UDF-t meghívó Stream Analytics átalakítás írása
6. A feladat indítása

## <a name="creating-a-udf-with-basic-properties"></a>UDF létrehozása alapszintű tulajdonságokkal
Példaként az alábbi mintakód egy *newudf* nevű skaláris UDF-t hoz létre, amely egy Azure Machine learning Studio (klasszikus) végponthoz kötődik. Vegye figyelembe, hogy a *végpont* (szolgáltatás URI-ja) a kiválasztott szolgáltatás API-Súgó oldalán található, a *ApiKey* pedig a szolgáltatások főoldalán található.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Példa a kérelem törzsére:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Az alapértelmezett UDF RetrieveDefaultDefinition-végpontjának meghívása
Miután az UDF létrehozta a csontvázat, szükség van az UDF teljes definícióra. Az RetrieveDefaultDefinition végpont segít beolvasni egy skaláris függvény alapértelmezett definícióját, amely egy Azure Machine Learning Studio (klasszikus) végponthoz van kötve. Az alábbi adattartalomhoz meg kell kapnia egy, a Studio (klasszikus) végponthoz kötött skaláris függvény alapértelmezett UDF-definícióját. Nem adja meg a tényleges végpontot, mivel az PUT kérelem során már meg van határozva. Stream Analytics meghívja a kérésben megadott végpontot, ha az explicit módon van megadva. Ellenkező esetben az eredetileg hivatkozottt használja. Itt az UDF egyetlen karakterlánc-paramétert (egy mondatot) és egy string típusú kimenetet ad vissza, amely az adott mondat "hangulat" címkéjét jelzi.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Példa a kérelem törzsére:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

A minta kimenete az alábbihoz hasonlóan fog kinézni.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>A választal rendelkező UDF javítása
Most az UDF-t az előző választal kell megjavítani, az alább látható módon.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Kérelem törzse (kimenet a RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Stream Analytics átalakítás implementálása az UDF meghívásához
Most kérdezze le az UDF-t (itt scoreTweet) minden bemeneti eseménynél, és írjon egy választ az adott eseményre egy kimenetre.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Segítség kérése
További segítségért próbálja ki a [Microsoft Q&egy kérdés oldalt Azure stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)
