---
title: Anomália-detektor Python többváltozós ügyféloldali kódtár rövid útmutatója
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 9b848f6c86f2ff2e95fa5cc191b088b7175f2311
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316018"
---
Ismerkedjen meg a Pythonhoz készült, többváltozós ügyféloldali kódtár használatába. Az alábbi lépéseket követve telepítheti a csomagot a szolgáltatás által biztosított algoritmusok használatával. Az új többváltozós anomália-észlelési API-k lehetővé teszik a fejlesztők számára, hogy a speciális AI-t egyszerűen integrálják a mérőszámokból származó rendellenességek észlelése érdekében, anélkül, hogy gépi tanulási ismeretekre vagy címkézett adatokra lenne A különböző jelek közötti függőségek és korrelációk közötti összefüggések automatikusan kulcsfontosságú tényezőként számítanak. Ezzel a megoldással proaktív módon védhető meg az összetett rendszerek hibái.

A következőhöz használhatja a Python többváltozós ügyféloldali függvénytárát:

* A rendszerszintű rendellenességek észlelése idősorozatok csoportjából.
* Ha az egyes idősorozatok nem sokat mondanak, és az összes jelet meg kell vizsgálnia a probléma észlelése érdekében.
* A Predicative több száz különböző típusú érzékelővel, a rendszerállapot különböző szempontjainak mérésével a költséges fizikai eszközök karbantartását is megfigyelheti.

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.x](https://www.python.org/)
* A [Panda adatelemzési könyvtára](https://pandas.pydata.org/)
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hozzon létre egy rendellenesség-Kiderítő erőforrást, "  target="_blank"> és hozzon létre egy anomália-detektor erőforrást </a> a Azure Portal a kulcs és a végpont Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazásnak a rendellenesség-érzékelő API-hoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.


## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

 Hozzon létre egy új Python-fájlt, és importálja a következő könyvtárakat.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Hozzon létre változókat a kulcshoz környezeti változóként, egy idősorozat-adatfájl elérési útját, valamint az előfizetés Azure-beli helyét. Például: `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült rendellenesség-Kiderítő ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [A modell betanítása](#train-the-model)
* [Rendellenességek észlelése](#detect-anomalies)
* [Modell exportálása](#export-model)
* [Modell törlése](#delete-model)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új anomália-detektor ügyfelének létrehozásához át kell adnia az anomália-detektor előfizetési kulcsát és a hozzá tartozó végpontot. Létrehozunk egy adatforrást is.  

Az anomáliák többváltozós API-k használatához az észlelés használata előtt be kell tanítani a saját modelljét. A betanításhoz használt adatmennyiség az idősorozatok egy kötege, minden egyes idősorozatnak CSV formátumúnak kell lennie két oszloppal, egy időbélyeggel és egy értékkel. Az összes idősorozatot egy zip-fájlba kell csomagolni, és fel kell tölteni az [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs)-ba. Alapértelmezés szerint a rendszer a fájlnevet fogja használni az idősorozat változójának ábrázolásához. Azt is megteheti, hogy a fájl egy további meta.jsis tartalmazhatja a zip-fájlban, ha azt szeretné, hogy a változó neve eltér a. zip-fájl nevétől. Ha a [blob sas (közös hozzáférésű aláírás) URL-címet](../../../../storage/common/storage-sas-overview.md)generálta, a betanításhoz használhatja a zip-fájl URL-címét.

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

Először betanítjuk a modellt, és betanítjuk a modell állapotát a képzés befejezését követően, majd lekérik a legújabb modell-azonosítót, amelyre szükség lesz az észlelési fázisra való áttéréskor.

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

## <a name="detect-anomalies"></a>Rendellenességek észlelése

A `detect_anomaly` és a `get_dectection_result` segítségével határozza meg, hogy van-e rendellenesség az adatforráson belül. A modell AZONOSÍTÓját át kell adni az imént betanított modellhez.

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

Ha modellt szeretne exportálni `export_model` , és át szeretné adni az exportálni kívánt modell azonosítóját:

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

A modell törléséhez használja a modellt, `delete_multivariate_model` és adja át a törölni kívánt modell azonosítóját:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatása előtt hozzá kell adnia egy kódot az újonnan létrehozott függvények meghívásához.

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

Futtassa az alkalmazást a `python` paranccsal és a fájl nevével.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
