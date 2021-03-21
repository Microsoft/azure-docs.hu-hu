---
title: Machine Learning webszolgáltatás-végpontokról származó adatok figyelése és gyűjtése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan gyűjthet adatokat a webszolgáltatási végpontokra telepített modellekről az Azure Kubernetes szolgáltatásban (ak) vagy Azure Container Instances (ACI).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: blackmist
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 2740a86c5ff68e851d592533b48dc8ee60d817ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98070810"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Adatok ML-webszolgáltatási végpontokról való monitorozása és gyűjtése


Ebből a cikkből megtudhatja, hogyan gyűjthet adatokat a webszolgáltatási végpontokra telepített modellekről az Azure Kubernetes szolgáltatásban (ak) vagy Azure Container Instances (ACI). Az [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) használatával gyűjtsön egy végpontból a következő adatokat:
* Kimeneti adatok
* Válaszok
* Kérelmek díjai, válaszideje és meghibásodási aránya
* Függőségi arányok, válaszidő és sikertelenség aránya
* Kivételek

Az [enable-app-Insight-in-producting-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook bemutatja a cikkben szereplő fogalmakat.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
 
## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Egy Azure Machine Learning munkaterület, egy helyi könyvtár, amely tartalmazza a parancsfájlokat, és a Azure Machine Learning SDK for Python telepítve van. További információ: [fejlesztői környezet konfigurálása](how-to-configure-environment.md).

* Egy betanított gépi tanulási modell. További tudnivalókat a [Képképzések besorolási modellje](tutorial-train-models-with-aml.md) című oktatóanyagban talál.

<a name="python"></a>

## <a name="configure-logging-with-the-python-sdk"></a>A naplózás konfigurálása a Python SDK-val

Ebből a szakaszból megtudhatja, hogyan engedélyezheti az alkalmazás-betekintés naplózását a Python SDK használatával. 

### <a name="update-a-deployed-service"></a>Központilag telepített szolgáltatás frissítése

Egy meglévő webszolgáltatás frissítéséhez kövesse az alábbi lépéseket:

1. Azonosítsa a szolgáltatást a munkaterületen. A értéke a `ws` munkaterület neve.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. A szolgáltatás frissítése és az Azure Application Insights engedélyezése

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Egyéni Nyomkövetések naplózása a szolgáltatásban

> [!IMPORTANT]
> Az Azure Application Insights csak a legfeljebb 64 kb adattartalmakat naplózza. Ha eléri ezt a korlátot, megjelenhetnek a hibák, például a memóriából, vagy nem lehet információt naplózni. Ha a naplózni kívánt adatok nagyobb 64 kb, akkor ehelyett a blob Storage-tárolóban kell tárolnia az [adatok gyűjtése az éles modellekben](how-to-enable-data-collection.md)című témakörben leírtak alapján.
>
> Összetettebb helyzetekben, mint például a modell követése egy AK-alapú környezetben, javasoljuk, hogy használjon egy külső gyártótól származó könyvtárat (például [OpenCensus](https://opencensus.io)).

Az egyéni Nyomkövetések naplózásához kövesse a normál üzembe helyezési folyamatot az AK vagy ACI esetében a dokumentum [üzembe helyezése és helye című témakörben](how-to-deploy-and-where.md) . Ezután kövesse az alábbi lépéseket:

1. Frissítse a pontozási fájlt úgy, hogy a következtetések beírásával adatokat küld az Application Insightsnak az előnézetben. Az összetettebb információk, például a kérések adatai és a válasz egy JSON-struktúrát használnak. 

    A következő példa a `score.py` modell inicializálását, bemenetét és kimenetét jeleníti meg a következtetések során, valamint a hibák előfordulásának idejét.

    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. Frissítse a szolgáltatás konfigurációját, és győződjön meg arról, hogy Application Insights.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Hozzon létre egy rendszerképet, és telepítse azt AK-ra vagy ACI-ra. További információ: [a telepítés és a hol](how-to-deploy-and-where.md).


### <a name="disable-tracking-in-python"></a>A nyomkövetés letiltása a Pythonban

Az Azure Application Insights letiltásához használja a következő kódot:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="configure-logging-with-azure-machine-learning-studio"></a>Naplózás konfigurálása Azure Machine Learning Studióval

Az Azure Application Insights a Azure Machine Learning studióból is engedélyezhető. Ha készen áll a modell webszolgáltatásként való üzembe helyezésére, a következő lépésekkel engedélyezheti a Application Insights:

1. Jelentkezzen be a studióba a címen https://ml.azure.com .
1. Nyissa meg a **modelleket** , és válassza ki a telepíteni kívánt modellt.
1. Válassza a  **+ telepítés** lehetőséget.
1. Töltse ki a **modell üzembe helyezése** űrlapot.
1. Bontsa ki a **speciális** menüt.

    ![Űrlap üzembe helyezése](./media/how-to-enable-app-insights/deploy-form.png)
1. Jelölje be **a diagnosztika és az adatgyűjtés engedélyezése Application Insights**.

    ![Az alkalmazás-felismerés engedélyezése](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Metrikák és naplók megtekintése

### <a name="query-logs-for-deployed-models"></a>Telepített modellek naplófájljainak lekérdezése

A valós idejű végpontok naplófájljai a vásárlói adatmennyiség. A `get_logs()` függvény használatával lekérheti a naplókat egy korábban telepített webszolgáltatásból. Előfordulhat, hogy a naplók részletes információkat tartalmaznak az üzembe helyezés során felmerülő hibákról.

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Ha több Bérlővel rendelkezik, előfordulhat, hogy a következő hitelesítő kódot kell megadnia, mielőtt `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

### <a name="view-logs-in-the-studio"></a>Naplók megtekintése a Studióban

Az Azure Application Insights a szolgáltatás naplóit a Azure Machine Learning munkaterülettel azonos erőforráscsoporthoz tárolja. Az alábbi lépésekkel tekintheti meg az adatait a Studióval:

1. Lépjen a Azure Machine Learning munkaterületére a [Studióban](https://ml.azure.com/).
1. Válassza a **végpontok** lehetőséget.
1. Válassza ki a telepített szolgáltatást.
1. Válassza ki a **Application Insights URL-** hivatkozást.

    [![Application Insights URL-cím keresése](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. Application Insights az **Áttekintés** lapon vagy a __figyelés__ szakaszban válassza a __naplók__ lehetőséget.

    [![A figyelés áttekintése lap](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. A score.py-fájlból naplózott információk megtekintéséhez tekintse meg a __nyomkövetési__ táblázatot. A következő lekérdezés olyan naplókat keres, amelyekben a __bemeneti__ érték naplózva lett:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![nyomkövetési adat](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Az Azure Application Insights használatáról további információt a [Mi az Application Insights?](../azure-monitor/app/app-insights-overview.md)című témakörben talál.

## <a name="web-service-metadata-and-response-data"></a>Webszolgáltatás metaadatainak és válaszideje

> [!IMPORTANT]
> Az Azure Application Insights csak a legfeljebb 64 kb adattartalmakat naplózza. Ha eléri ezt a korlátot, akkor előfordulhat, hogy olyan hibákat lát, mint a memória, vagy nem lehet információt naplózni.

A webszolgáltatás-kérelem adatainak naplózásához adjon hozzá `print` utasításokat a score.py-fájlhoz. Minden `print` utasítás egy bejegyzést eredményez az üzenet alatt található Application Insights nyomkövetési táblában `STDOUT` . Application Insights a `print` kimutatás kimeneteit a  `customDimensions` és a `Contents` nyomkövetési táblában tárolja. A JSON-karakterláncok nyomtatása hierarchikus adatstruktúrát hoz létre a nyomkövetési kimenetben `Contents` .

## <a name="export-data-for-retention-and-processing"></a>Adatok exportálása megőrzésre és feldolgozásra

>[!Important]
> Az Azure Application Insights csak a blob Storage-ba való exportálást támogatja. A megvalósítás korlátaival kapcsolatos további információkért lásd: [telemetria exportálása az alkalmazásból](../azure-monitor/app/export-telemetry.md#continuous-export-advanced-storage-configuration).

Az adatok egy blob Storage-fiókba való exportálásához Application Insights [folyamatos exportálást](../azure-monitor/app/export-telemetry.md) használhat, ahol megadhatja az adatmegőrzési beállításokat. Application Insights JSON formátumban exportálja az adatmennyiséget. 

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Folyamatos exportálás":::

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti a naplózást és megtekintheti a webszolgáltatási végpontok naplóit. Próbálja ki ezeket a cikkeket a következő lépésekhez:


* [Modell üzembe helyezése AK-fürtön](./how-to-deploy-azure-kubernetes-service.md)

* [Modell üzembe helyezése Azure Container Instances](./how-to-deploy-azure-container-instance.md)

* [MLOps: modellek kezelése, üzembe helyezése és monitorozása a Azure Machine learning](./concept-model-management-and-deployment.md) segítségével többet tudhat meg az éles modellekben gyűjtött adatok kihasználásáról. Ezek az adatai segíthetnek a gépi tanulási folyamat folyamatos fejlesztésében.