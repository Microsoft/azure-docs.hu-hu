---
title: anomáliadetektor Python többváltozós ügyféloldali kódtár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: b0eba6d0be1d6a65b911f05dabf3cdbecc9c666d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879888"
---
A Pythonhoz készült anomáliadetektor ügyféloldali kódtár első lépések. Kövesse az alábbi lépéseket a csomag telepítéséhez, és kezdje el használni a szolgáltatás által biztosított algoritmusokat. Az új többváltozós anomáliadetektálási API-k lehetővé teszik a fejlesztők számára a fejlett AI integrálását a metrikák csoportjaiból származó anomáliák észleléséhez anélkül, hogy gépi tanulási ismeretekre vagy címkével jelölt adatokra lenne szükség. A különböző jelek közötti függőségeket és korrelációkat a rendszer automatikusan kulcsfontosságú tényezőknek számítja. Ez segít proaktív módon megvédeni az összetett rendszereket a hibáktól.

Használja a anomáliadetektor Pythonhoz készült ügyféloldali kódtárát a következőre:

* Rendszerszintű anomáliák észlelése idősorok egy csoportjából.
* Ha egyetlen idősor sem árul el sokat, és minden jelet meg kell néznie a probléma észlelése érdekében.
* Drága fizikai eszközök predikatív karbantartása több tíz vagy több száz különböző típusú érzékelővel, amelyek a rendszer állapotának különböző aspektusait mérik.

[Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Csomag (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/)  |  [Mintakód](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/samples/sample_multivariate_detect.py)  |  [Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb)

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.x](https://www.python.org/)
* A [Pandas adatelemzési kódtára](https://pandas.pydata.org/)
* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy anomáliadetektor-erőforrást, és hozzon létre anomáliadetektor erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Várja meg az üzembe helyezést, és kattintson az **Erőforráshoz ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrás kulcsra és végpontra az alkalmazás a anomáliadetektor API-hoz való csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.


## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

 Hozzon létre egy új Python-fájlt, és importálja a következő kódtárakat.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Hozzon létre változókat a kulcshoz környezeti változóként, egy idősorozat-adatfájl elérési útjának és az előfizetés Azure-beli helyének. Például: `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után a következővel telepítheti az ügyféloldali kódtárat:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következőket mutatják be a Pythonhoz készült anomáliadetektor kódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [A modell betanítása](#train-the-model)
* [Anomáliák észlelése](#detect-anomalies)
* [Modell exportálása](#export-model)
* [Modell törlése](#delete-model)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az új anomáliadetektor példányának példányosíthoz át kell adni a anomáliadetektor előfizetői előfizetési kulcsot és a társított végpontot. Létrehozunk egy adatforrást is.  

A többváltozós ANOMÁLIADETEKTOR használata előtt be kell betanítanunk a saját modellünket. A betanításhoz használt adatok idősorozatok, és minden idősornak CSV formátumúnak kell lennie két oszloppal, időbélyegzővel és értékkel. Az idősorokat egyetlen zip-fájlba kell tömörítenünk, és fel kell tölteni az [Azure Blob Storage-ba.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Alapértelmezés szerint a fájlnév az idősor változóját képviseli. Azt is meta.js, hogy a zip-fájlban egy további fájlnevet is fel kell venni, ha azt szeretné, hogy a változó neve eltér a .zip fájl nevétől. Miután létrehoztunk [egy BLOB SAS-url-címet (közös](../../../../storage/common/storage-sas-overview.md)hozzáférésű jogosultságok) URL-címet, a zip-fájl URL-címét használjuk a betanításhoz.

```python
def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    if not data_source:
        # Datafeed for test only
        self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
    else:
        self.data_source = data_source
```

## <a name="train-the-model"></a>A modell betanítása

Először betanítjuk a modellt, ellenőrizzük a modell állapotát a betanítás során, hogy megállapítsuk, mikor fejeződött be a betanítás, majd lek kérjük a legújabb modellazonosítót, amelyre az észlelési fázisra való továbblépéskor szükségünk lesz.

```python
def train(self, start_time, end_time, max_tryout=500):

    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    tryout_count = 0
    while (tryout_count < max_tryout and model_status != "READY"):
        model_status = self.ad_client.get_multivariate_model(trained_model_id).additional_properties["summary"][
            "status"]
        tryout_count += 1
        time.sleep(2)
    
    assert model_status == "READY"
    
    print("Done.", "\n--------------------")
    print("{:d} available models after training.".format(len(new_model_list)))
    
    # Return the latest model id
    return trained_model_id

```

## <a name="detect-anomalies"></a>Anomáliák észlelése

A és `detect_anomaly` a `get_dectection_result` használatával állapítsa meg, hogy vannak-e anomáliák az adatforrásban. Át kell adnia az előbb betanított modell modellazonosítóját.

```python
def detect(self, model_id, start_time, end_time, max_tryout=500):
    
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        tryout_count = 0
        while r.summary.status != "READY" and tryout_count < max_tryout:
            time.sleep(1)
            r = self.ad_client.get_detection_result(result_id)
            tryout_count += 1
    
        if r.summary.status != "READY":
            print("Request timeout after %d tryouts.".format(max_tryout))
            return None
    
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    
    return r
```

## <a name="export-model"></a>Modell exportálása

Ha exportálni szeretne egy modellt, és át szeretné adni az exportálni kívánt `export_model` modell modellazonosítóját:

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>Modell törlése

A modell használatának törléséhez és a törölni kívánt modell `delete_multivariate_model` azonosítójának áteshez:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatása előtt hozzá kell adnunk néhány kódot az újonnan létrehozott függvények hívásához.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

A futtatása előtt hasznos lehet ellenőrizni [](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) a projektet a teljes mintakóddal, amelyből ez a rövid útmutató származik.

Az első [lépésekhez Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) is van egy részletes Jupyter Notebook.

Futtassa az alkalmazást a `python` paranccsal és a fájlnévvel.


[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
