---
title: Machine Learning-végpontok használata az Azure Stream Analytics szolgáltatásban
description: Ez a cikk azt ismerteti, gépi nyelvére felhasználó által definiált függvények használata az Azure Stream Analytics szolgáltatásban.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: dfb34f8c0fca792618860e0a8d5a1bf1736f3611
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416048"
---
# <a name="machine-learning-integration-in-stream-analytics-preview"></a>A Machine Learning-integráció a Stream Analytics (előzetes verzió)
Stream Analytics támogatja a felhasználó által definiált függvények, melyek az Azure Machine Learning-végpontokra irányuló. Ez a szolgáltatás REST API-támogatása részleteit a a [Stream Analytics REST API-könyvtár](https://msdn.microsoft.com/library/azure/dn835031.aspx). Ez a cikk nyújt kiegészítő információkat, amelyeket ez a funkció a Stream Analytics sikeres megvalósítását. Oktatóanyag is közzé lett téve, és elérhető [Itt](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Áttekintés: Azure Machine Learning-terminológia
A Microsoft Azure Machine Learning segítségével hozhat létre, tesztelheti és üzembe helyezése az adataihoz prediktív elemzési megoldások által biztosított együttműködési környezettel, fogd és vidd eszközt biztosít. Ez az eszköz neve a *Azure Machine Learning Studio*. A studio segítségével a Machine Learning-erőforrások kezelése és egyszerűen hozhat létre, tesztelheti és újrafuttathatja a tervező. Ezeket az erőforrásokat és a definíciójukat alatt van.

* **Munkaterület**: A *munkaterület* egy tároló, amely tartalmazza az összes többi Machine Learning-erőforrások együtt, a felügyelet és szabályozás tárolója.
* **Kísérlet**: *Kísérletek* hozzák létre az adatszakértők, az adatkészletek és a egy gépi tanulási modell betanításához.
* **Végpont**: *Végpontok* az Azure Machine Learning objektum szolgáltatások igénybe bemenetként, a alkalmazni egy megadott gépi tanulási modellt, és a pontozott a hibaüzenettel reagál.
* **Pontozási webszolgáltatás**: A *pontozási webszolgáltatás* végpontok gyűjteménye van a fent említett módon.

Minden végpont kötegelt végrehajtás és szinkron végrehajtás API-kkal rendelkezik. Stream Analytics szinkron végrehajtási használja. Az adott szolgáltatás nevű egy [kérés/válasz szolgáltatás](../machine-learning/studio/consume-web-services.md) az Azure Machine Learning studióban.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>A Machine Learning a Stream Analytics-feladatokhoz szükséges erőforrások
Stream Analytics az alkalmazásában feladat feldolgozása, egy kérés/válasz végpontot egy [apikey tulajdonsággal végzett tesztelése](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), és a egy swagger-definíció a sikeres végrehajtás minden szükséges. Stream Analytics egy további végpontot, amely a swagger-végpont URL-címet hoz létre, a felület és visszaadása egy alapértelmezett UDF-definíció a felhasználó rendelkezik.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>A Stream Analytics és a Machine Learning – REST API-n keresztül UDF konfigurálása
REST API-k használatával konfigurálhatja a feladatot az Azure gépi nyelvére-függvényeket. A lépések a következők:

1. Stream Analytics-feladat létrehozása
2. Egy bemeneti definiálása
3. Kimenet definiálása
4. Hozzon létre egy felhasználói függvény (UDF)
5. A Stream Analytics átalakítást, amely meghívja ezt az UDF írása
6. A feladat indítása

## <a name="creating-a-udf-with-basic-properties"></a>Egy UDF alapvető tulajdonságok létrehozása
Tegyük fel, az alábbi példakód létrehoz egy skaláris elnevezésű UDF *newudf* , amely összekapcsolja az Azure Machine Learning-végpont. Vegye figyelembe, hogy a *végpont* (szolgáltatás-URI) a kiválasztott szolgáltatás az API help oldalon található, és a *apikey tulajdonsággal végzett tesztelése* szolgáltatások fő lapján találhatók.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

A példában a kérés törzse:

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

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Alapértelmezett UDF RetrieveDefaultDefinition végpont meghívása
A vázban UDF létrehozása után az UDF teljes definíciója van szükség. A RetrieveDefaultDefinition végpont segít a skaláris függvény, amely egy Azure Machine Learning-végpont van kötve az alapértelmezett definíció lekérése. Az alábbi hasznos megköveteli, hogy az alapértelmezett skaláris függvény, amely egy Azure Machine Learning-végpont van kötve az UDF-definíció lekérése. Ez nem adja meg a tényleges végpontnak ahogy azt már megadott PUT kérelem során. Stream Analytics meghívja a végpont, ha explicit módon van megadva a kérelemben megadott. Ellenkező esetben az eredetileg hivatkozott egy használ. Itt a UDF beolvassa egy karakterlánc-paramétert (egy mondatnál), és adja vissza, egyetlen kimeneti típusú karakterlánc, ami azt jelenti, hogy a "vélemények" címkét a mondat helyett szerepel.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

A példában a kérés törzse:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

A minta kimeneti e keresse meg valami szeretné alatt.

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

## <a name="patch-udf-with-the-response"></a>A válasz javítás UDF-ben
Most már az UDF kell javítható az előző válaszban kapott, az alább látható módon.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

A kérelem törzsében (RetrieveDefaultDefinition kimenete):

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

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Stream Analytics átalakítási hívja az UDF megvalósítása
Most már lekérdezéséhez az UDF-ben (Itt nevű scoreTweet) minden bemeneti eseményhez, és választ az esemény-kimenetre.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
