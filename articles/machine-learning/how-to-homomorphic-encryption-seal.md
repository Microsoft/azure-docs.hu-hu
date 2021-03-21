---
title: Titkosított következtetési szolgáltatás üzembe helyezése (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan helyezhet üzembe egy titkosított előrejelző szolgáltatást a Microsoft SEAL használatával a lemezképek besorolásához
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, responsible-ml
ms.openlocfilehash: a2c8b765f109408b8cb08f78a4b3ad2283f35eb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223584"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>Titkosított viszonyítási webszolgáltatás üzembe helyezése (előzetes verzió)

Megtudhatja, hogyan helyezhet üzembe egy lemezkép-besorolási modellt titkosított, [Azure Container instances](../container-instances/index.yml) (ACI) webszolgáltatásként. A webszolgáltatás egy Docker-tároló képe, amely tartalmazza a modell és a pontozási logikát.

Ebben az útmutatóban Azure Machine Learning szolgáltatást használ a következőhöz:

> [!div class="checklist"]
> * A környezetek konfigurálása
> * Titkosított viszonyítási webszolgáltatás üzembe helyezése
> * Tesztelési célú adatfeldolgozás
> * Titkosított előrejelzések készítése
> * Az erőforrások eltávolítása

Az ACI nagyszerű megoldás a modell üzembe helyezési munkafolyamatának tesztelésére és megismerésére. Skálázható termelési környezetek esetén fontolja meg az Azure Kubernetes Service használatát. További információ: [a telepítés és a hol](./how-to-deploy-and-where.md).

A mintában használt titkosítási módszer a [homomorphic titkosítás](https://github.com/Microsoft/SEAL#homomorphic-encryption). A homomorphic titkosítás lehetővé teszi a számítások elvégzését a titkosított adatokon anélkül, hogy hozzá kellene férnie egy titkos (visszafejtési) kulcshoz. A számítások eredményei titkosítottak, és csak a titkos kulcs tulajdonosa által láthatók. 

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató feltételezi, hogy rendelkezik egy Azure Machine Learningban regisztrált rendszerkép-besorolási modellel. Ha nem, regisztrálja a modellt egy [előképzésen](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl) alapuló modell használatával, vagy hozza létre a sajátját a [képbesorolási modell betanítása Azure Machine learning oktatóanyag](tutorial-train-models-with-aml.md)segítségével.

## <a name="configure-local-environment"></a>Helyi környezet konfigurálása

Jupyter-jegyzetfüzetben

1. Importálja a mintához szükséges Python-csomagokat.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. Telepítse a homomorphic titkosítási függvénytárat a biztonságos következtetéshez.

    > [!NOTE]
    > A `encrypted-inference` csomag jelenleg előzetes verzióban érhető el.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) a egy olyan függvénytár, amely a [Microsoft Seal](https://github.com/Microsoft/SEAL)alapján titkosított következtetésekhez tartalmaz kötéseket.

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>A következtetési környezet konfigurálása

Hozzon létre egy környezetet a következtetéshez, és adja hozzá `encrypted-inference` a csomagot Conda-függőségként.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>Titkosított viszonyítási webszolgáltatás üzembe helyezése

A modell üzembe helyezése az ACI-ban üzemeltetett webszolgáltatásként.

Az ACI-hoz megfelelő környezet kialakításához biztosítsa az alábbiakat:

* Pontozószkript a modell használatának bemutatásához
* Az ACI létrehozásához szükséges konfigurációs fájl
* Egy betanított modell

### <a name="create-scoring-script"></a>Pontozószkript létrehozása

Hozza létre a `score.py` webszolgáltatás által a következtetéshez használt pontozási szkriptet.

A pontozószkriptnek két függvényt kell tartalmaznia:

* Az `init()` függvényt, amely általában a modellt tölti be a globális objektumba. Ezt a függvényt csak egyszer kell futtatni, a Docker-tároló indításakor.
* A `run(input_data)` függvény a modell segítségével értékeket jelez elő a bemeneti adatok alapján. A futtatás bemenetei és kimenetei általában JSON-fájlokat használnak a szerializáláshoz vagy a deszerializáláshoz, de más formátumokat is támogatnak. A függvény beolvassa a homomorphic által feltöltött titkosítási alapú nyilvános kulcsokat.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>Konfigurációs fájl létrehozása

Hozzon létre egy telepítési konfigurációs fájlt, és adja meg az ACI-tárolóhoz szükséges processzorok számát, illetve a RAM gigabájtban mért mennyiségét. Bár ez a modelltől is függ, az alapértelmezettként megadott 1 mag és 1 gigabyte RAM számos modell használatához elegendő. Ha később többre lenne szüksége, akkor újra létre kell hoznia a rendszerképet, és újra üzembe kell helyeznie a szolgáltatást.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Üzembe helyezés az Azure Container Instances szolgáltatásban

Várható befejezési idő: **körülbelül 2-5 perc**

Konfigurálja, majd helyezze üzembe a rendszerképet. Az alábbi kód a következő lépéseket hajtja végre:

1. A modell által igényelt függőségeket tartalmazó környezeti objektum létrehozása a környezeti fájl használatával ( `myenv.yml` )
1. A modell webszolgáltatásként való üzembe helyezéséhez szükséges következtetési konfiguráció létrehozása a következő használatával:
   * A pontozófájl (`score.py`)
   * Az előző lépésben létrehozott környezeti objektum
1. Telepítse a modellt az ACI-tárolóba.
1. A webszolgáltatás HTTP-végpontjának lekérése.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Kérje le a pontozási webszolgáltatás REST-ügyfélhívásokat fogadó HTTP-végpontját. Ez a végpont bárkivel megosztható, aki tesztelni szeretné a webszolgáltatást vagy integrálni szeretné azt egy alkalmazásban.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>Tesztelési célú adatfeldolgozás

1. Töltse le a tesztet. Ebben az esetben a rendszer az adatkönyvtárba menti a *fájlokat*.

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. Töltse be a teszt adatait az *adatkönyvtárból* .

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>Titkosított előrejelzések készítése

Az előrejelzések beszerzéséhez használja az adatkészletet a modellel.

Titkosított előrejelzések készítése:

1. Hozzon létre egy új `EILinearRegressionClient` , egy homomorphic-alapú titkosítási ügyfelet és egy nyilvános kulcsot.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. Töltse fel a homomorphic-titkosítás által generált nyilvános kulcsokat a munkaterület alapértelmezett blob-tárolójába. Ezzel lehetővé válik a kulcsok megosztása a következtetési kiszolgálóval.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. A teszt adatai titkosítása

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. Használja az SDK `run` API-ját a szolgáltatás meghívásához, és adja meg a tesztelési adatkészletet a modellnek a jóslatok beszerzéséhez. El kell küldeni a kapcsolódási karakterláncot a blob Storage-ba, ahol a nyilvános kulcsokat feltöltötték.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. Használja az ügyfelet az eredmények visszafejtéséhez.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A mintában létrehozott webszolgáltatás törlése:

```python
service.delete()
```

Ha már nem tervezi a létrehozott Azure-erőforrások használatát, törölje őket. Ez megakadályozza, hogy a még futó, nem hasznosított erőforrások díját felszámoljuk. További információért tekintse meg ezt az útmutatót az [erőforrások](how-to-manage-workspace.md#clean-up-resources) törléséről.