---
title: Adatok gyűjtése az üzemi modelleken
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan gyűjthet adatokat az Azure Kubernetes Service (ak) fürtön üzembe helyezett Azure Machine Learning modellből.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: f42a2f9e606eaa0475f2e35c479e99545ecf8193
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521900"
---
# <a name="collect-data-from-models-in-production"></a>Adatok gyűjtése üzemi modellből

Ez a cikk bemutatja, hogyan gyűjthet adatokat egy Azure Kubernetes Service (ak) fürtön üzembe helyezett Azure Machine Learning modellből. Ezután az összegyűjtött adatokat az Azure Blob Storage tárolja.

A gyűjtemény engedélyezése után az összegyűjtött adatok segítenek a következőkben:

* [Figyelje az adatok eltolódásait](how-to-monitor-datasets.md) az összegyűjtött éles adatokon.

* Összegyűjtött adatok elemzése [Power bi](#powerbi) vagy [Azure Databricks](#databricks) használatával

* Jobb döntéseket hozhat a modell újratanítása vagy optimalizálása során.

* A modell újratanítása az összegyűjtött adatokkal.

## <a name="what-is-collected-and-where-it-goes"></a>A begyűjtött és a hová kerül

A következő adatokat gyűjthetjük össze:

* Bemeneti adatok modellezése egy AK-fürtben üzembe helyezett webszolgáltatásokból. A hanghangok, a képek és a videók gyűjtése *nem* történik meg.
  
* Modellek előrejelzése éles bemeneti adatok használatával.

>[!NOTE]
> Ezen az adathalmazon jelenleg nem része az előösszesítésnek és az előszámításoknak.

A rendszer menti a kimenetet a blob Storage-ban. Mivel az adatait hozzáadja a blob Storage-hoz, kiválaszthatja kedvenc eszközét az elemzés futtatásához.

A blob kimeneti adatelérési útja a következő szintaxist követi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> A 0.1.0 A16 verziónál korábbi Pythonhoz készült Azure Machine Learning SDK verziójában az `designation` argumentum neve `identifier` . Ha a kódot egy korábbi verzióval fejlesztette ki, azt ennek megfelelően kell frissítenie.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://aka.ms/AMLFree) a virtuális gép létrehozásának megkezdése előtt.

- Telepíteni kell egy Azure Machine Learning munkaterületet, a parancsfájlokat tartalmazó helyi könyvtárat és a Pythonhoz készült Azure Machine Learning SDK-t. A telepítésének megismeréséhez tekintse meg [a fejlesztési környezet konfigurálása](how-to-configure-environment.md)című témakört.

- Szüksége van egy betanított gépi tanulási modellre, amelyet AK-ra kell telepíteni. Ha nem rendelkezik modellel, tekintse meg a [betanítási modell besorolása](tutorial-train-models-with-aml.md) című oktatóanyagot.

- Szüksége van egy AK-fürtre. További információ a létrehozásáról és telepítéséről: a [telepítés és a hol](how-to-deploy-and-where.md).

- [Állítsa be a környezetet](how-to-configure-environment.md) , és telepítse a [Azure Machine learning monitoring SDK](/python/api/overview/azure/ml/install)-t.

## <a name="enable-data-collection"></a>Az adatgyűjtés engedélyezése

Az [adatgyűjtést](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector) a Azure Machine learning vagy más eszközökön keresztül üzembe helyezett modelltől függetlenül is engedélyezheti.

Az adatgyűjtés engedélyezéséhez a következőket kell tennie:

1. Nyissa meg a pontozási fájlt.

1. Adja hozzá a következő kódot a fájl elejéhez:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Deklarálja az adatgyűjtés változóit a `init` függvényben:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    A *correlationId* egy opcionális paraméter. Nem kell használni, ha a modell nem igényli. A *correlationId* használata megkönnyíti a többi adattal, például a *LoanNumber* vagy a *Vevőkód* használatával való leképezést.
    
    A rendszer később az *azonosító* paramétert használja a mappa struktúrájának létrehozásához a blobban. Felhasználhatja a feldolgozott adatokból származó nyers adatok megkülönböztetésére is.

1. Adja hozzá a következő sornyi kódot a `run(input_df)` függvényhez:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Az adatgyűjtést *nem* lehet automatikusan **true** értékre állítani, ha a szolgáltatást az AK-ban helyezi üzembe. Frissítse a konfigurációs fájlt, ahogy az az alábbi példában is látható:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    A szolgáltatás figyelésének Application Insights a konfiguráció módosításával is engedélyezheti:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Új rendszerkép létrehozásához és a Machine learning-modell üzembe helyezéséhez tekintse meg a [hogyan kell üzembe helyezni és hol](how-to-deploy-and-where.md).

1. Adja hozzá az "Azure-monitoring" pip-csomagot a webszolgáltatás-környezet Conda:
  ```Python
    env = Environment('webserviceenv')
    env.python.conda_dependencies = CondaDependencies.create(conda_packages=['numpy'],pip_packages=['azureml-defaults','azureml-monitoring','inference-schema[numpy-support]'])
  ```


## <a name="disable-data-collection"></a>Adatgyűjtés letiltása

Bármikor leállíthatja az adatgyűjtést. A Python-kód használatával tiltsa le az adatgyűjtést.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Az adatai ellenőrzése és elemzése

A blob Storage-ban összegyűjtött adatok elemzéséhez kiválaszthatja a kívánt eszközt.

### <a name="quickly-access-your-blob-data"></a>BLOB-adatai gyors elérése

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com).

1. Nyissa meg a munkaterületet.

1. Válassza a **tárterület** lehetőséget.

    [![Válassza ki a tárolási beállítást](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Kövesse a blob kimeneti adatelérési útját a következő szintaxissal:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> A modell adatai elemzése Power BI használatával

1. Töltse le és nyissa meg [Power bi Desktop](https://www.powerbi.com).

1. Válassza **az adatlekérdezés** lehetőséget, és válassza az [**Azure Blob Storage**](/power-bi/desktop-data-sources)lehetőséget.

    [![Power BI blob beállítása](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Adja meg a Storage-fiók nevét, és adja meg a Storage-kulcsát. Ezt az információt a blobban található **Beállítások**  >  **hozzáférési kulcsok** lehetőség kiválasztásával érheti el.

1. Válassza ki a **modell** adattárolót, és válassza a **Szerkesztés** lehetőséget.

    [![Power BI-navigátor](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. A lekérdezés-szerkesztőben kattintson a **Name (név** ) oszlopban a Storage-fiók hozzáadása lehetőségre.

1. Adja meg a modell elérési útját a szűrőben. Ha csak egy adott évből vagy hónapból származó fájlokra szeretne rákeresni, egyszerűen bontsa ki a szűrő elérési útját. Ha például csak a márciusi adatmegjelenítést szeretné megkeresni, használja a szűrő elérési útját:

   /modeldata/ \<subscriptionid> / \<resourcegroupname> / \<workspacename> / \<webservicename> / \<modelname> / \<modelversion> / \<designation> / \<year> /3

1. A **név** értékek alapján szűrheti a megfelelő adatokat. Ha a jóslatokat és a bemeneti adatokat tárolta, mindegyikhez létre kell hoznia egy lekérdezést.

1. A fájlok egyesítéséhez kattintson a **tartalom** oszlop fejléce melletti lefelé mutató dupla nyílra.

    [![Power BI tartalom](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Válassza az **OK** lehetőséget. Az adatelőre betöltött sorok.

    [![Power BI fájlok egyesítése](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Válassza **a Bezárás és alkalmaz** lehetőséget.

1. Ha hozzáadta a bemeneteket és az előrejelzéseket, a táblákat a rendszer automatikusan **kérelemazonosító** -értékek alapján rendezi.

1. Megkezdheti az egyéni jelentések összeállítását a modell adatain.

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> A modell adatai elemzése Azure Databricks használatával

1. Hozzon létre egy [Azure Databricks munkaterületet](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).

1. Nyissa meg a Databricks-munkaterületet.

1. A Databricks munkaterületen válassza az **adatok feltöltése** lehetőséget.

    [![Databricks adatok feltöltése lehetőség kiválasztása](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Válassza az **új tábla létrehozása** lehetőséget, és válassza az **egyéb adatforrások**  >  **Azure Blob Storage**  >  **tábla létrehozása jegyzetfüzetben** elemet.

    [![Databricks-tábla létrehozása](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Frissítse az adatai helyét. Alább bemutatunk egy példát:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks-telepítő](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Az adatai megtekintéséhez és elemzéséhez kövesse a sablon lépéseit.

## <a name="next-steps"></a>Következő lépések

A gyűjtött adatokra vonatkozó [adateltolódás észlelése](how-to-monitor-datasets.md) .