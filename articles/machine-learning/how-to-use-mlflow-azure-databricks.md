---
title: MLflow követése Azure Databricks ML-kísérleteknél
titleSuffix: Azure Machine Learning
description: Állítsa be a MLflow-t a Azure Machine Learning használatával a metrikák és összetevők naplózásához Azure Databricks ml-kísérletekből.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a894a46ce7c78b65dde80c52f9516fcbe4d27bcb
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520999"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>Azure Databricks ML-kísérletek nyomon követése MLflow és Azure Machine Learning (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan engedélyezheti a MLflow követési URI-és naplózási API-JÁT, azaz a [MLflow-követést](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), amely a Azure DATABRICKS (ADB) kísérletek, a MLflow és a Azure Machine learning összekapcsolására szolgál.

A [MLflow](https://www.mlflow.org) egy nyílt forráskódú kódtár a gépi tanulási kísérletek életciklusának kezeléséhez. A MLFlow Tracking a MLflow egy olyan összetevője, amely naplózza és nyomon követi a képzések futtatási metrikáit és a modelleket. További információ a [Azure Databricks és a MLflow](/azure/databricks/applications/mlflow/). 

További MLflow és Azure Machine Learning funkciók integrálásával kapcsolatban tekintse meg a következő témakört: a [kísérlet nyomon követése a MLflow és a Azure Machine learning](how-to-use-mlflow.md) .

>[!NOTE]
> A nyílt forráskódú kódtár MLflow gyakran változnak. Ennek megfelelően a Azure Machine Learning és a MLflow-integráción keresztül elérhető funkciókat előzetes verziónak kell tekinteni, és a Microsoft nem támogatja teljes mértékben.

> [!TIP]
> A jelen dokumentumban található információk elsősorban olyan adatszakértők és fejlesztők számára készültek, akik figyelni szeretnék a modell betanítási folyamatát. Ha Ön olyan rendszergazda, aki a Azure Machine Learning erőforrás-használat és-események figyelését érdekli (például kvóták, befejezett betanítási futtatások vagy befejezett modellek üzembe helyezése), tekintse meg a [figyelés Azure Machine learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Előfeltételek

* Telepítse az `azureml-mlflow` csomagot. 
    * Ez a csomag automatikusan bevezeti a `azureml-core` [Azure Machine learning Python SDK](/python/api/overview/azure/ml/install)-t, amely biztosítja a kapcsolatot a MLflow a munkaterület eléréséhez.
* [Azure Databricks munkaterület és fürt](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).
* [Hozzon létre egy Azure Machine learning-munkaterület](how-to-manage-workspace.md).
    * Megtudhatja, [hogy mely hozzáférési engedélyek szükségesek a MLflow műveleteinek elvégzéséhez a munkaterületen](how-to-assign-roles.md#mlflow-operations).

## <a name="track-azure-databricks-runs"></a>Azure Databricks futtatások nyomon követése

A MLflow követése Azure Machine Learning lehetővé teszi a naplózott metrikák és összetevők tárolását a Azure Databricks a következőkre: 

* Azure Databricks munkaterület.
* Azure Machine Learning-munkaterület

Miután létrehozta a Azure Databricks-munkaterületet és a fürtöt, 

1. Telepítse a *azureml-mlflow* könyvtárat a PyPi-ből, hogy a fürt hozzáférjen a szükséges függvényekhez és osztályokhoz.

1. A kísérleti jegyzetfüzet beállítása.

1. A Azure Databricks munkaterület és a Azure Machine Learning munkaterület összekapcsolásához.

Ezekről a lépésekről további részleteket a következő szakaszban talál, így sikeresen futtathatja a MLflow-kísérleteket Azure Databricksokkal. 

## <a name="install-libraries"></a>Tárak telepítése

Ha a fürtön szeretné telepíteni a kódtárakat, navigáljon a **tárak** lapra, és válassza az **új telepítése** lehetőséget.

 ![mlflow az Azure databricks](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

A **csomag** mezőbe írja be a következőt: azureml-mlflow, majd válassza a telepítés lehetőséget. Szükség szerint ismételje meg ezt a lépést, hogy a kísérlethez további csomagokat telepítsen a fürtön.

 ![Azure DB install mlflow könyvtár](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>A jegyzetfüzet beállítása 

Miután beállította az ADB-fürtöt, 
1. A bal oldali navigációs ablaktáblán válassza a **munkaterületek** lehetőséget. 
1. Bontsa ki a munkaterületek legördülő menüt, és válassza az **Importálás** lehetőséget.
1. Húzással vagy tallózással keresse meg az ADB-munkaterületet, és válassza a kísérlet jegyzetfüzetet.
1. Válassza az **Importálás** lehetőséget. A kísérleti jegyzetfüzet automatikusan megnyílik.
1. A bal felső sarokban található notebook cím alatt válassza ki a kísérlethez csatolni kívánt fürtöt. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure Databricks és Azure Machine Learning munkaterületek összekötése

Az ADB-munkaterület Azure Machine Learning-munkaterülethez való csatolásával nyomon követheti a kísérlet adatait a Azure Machine Learning munkaterületen.

Az ADB-munkaterület új vagy meglévő Azure Machine Learning munkaterülethez való csatolása 
1. Jelentkezzen be [Azure Portalba](https://ms.portal.azure.com).
1. Navigáljon az ADB munkaterület **Áttekintés** lapjára.
1. Kattintson a jobb alsó sarokban található **Azure Machine learning munkaterület hivatkozásra** . 

 ![Az Azure DB és Azure Machine Learning munkaterületek összekapcsolása](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>MLflow követése a munkaterületeken

Miután létrehozta a munkaterületet, a MLflow nyomon követése automatikusan nyomon követhető a következő helyeken:

* A csatolt Azure Machine Learning munkaterület.
* Az eredeti ADB-munkaterülete. 

Az összes kísérlet a felügyelt Azure Machine Learning követési szolgáltatásban van.

A következő kódnak a kísérleti jegyzetfüzetben kell lennie a csatolt Azure Machine Learning munkaterület beszerzéséhez. 

Ez a kód, 

*  Lekéri az Azure-előfizetésének részleteit a Azure Machine Learning munkaterület létrehozásához. 

* Feltételezi, hogy rendelkezik egy meglévő erőforráscsoporthoz és Azure Machine Learning munkaterülettel, különben [létrehozhatja őket](how-to-manage-workspace.md). 

* Beállítja a kísérlet nevét. Az `user_name` itt látható a `user_name` Azure Databricks munkaterülethez kapcsolódóan.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>MLflow-követés beállítása csak a Azure Machine Learning munkaterület nyomon követéséhez

Ha inkább egy központi helyen szeretné kezelni a nyomon követett kísérleteket, beállíthatja, hogy a MLflow követése **csak** a Azure Machine learning munkaterületen legyen nyomon követhető. 

Adja meg a következő kódot a parancsfájlban:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

A betanítási szkriptben importálja `mlflow` a MLflow-naplózási API-k használatára, és kezdje meg a futtatási metrikák naplózását. A következő példa naplózza az alapkorszak elvesztésének mérőszámát. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>Modellek regisztrálása a MLflow

A modell betanítása után naplózhatja és regisztrálhatja a modelleket a háttér-nyomkövetési kiszolgálón a `mlflow.<model_flavor>.log_model()` metódussal. `<model_flavor>`, a modellhez társított keretrendszerre hivatkozik. [Ismerje meg, hogy milyen modell-ízek támogatottak](https://mlflow.org/docs/latest/models.html#model-api). 

Alapértelmezés szerint a háttér-nyomkövetési kiszolgáló a Azure Databricks munkaterület. Ha úgy döntött, hogy a [MLflow-követést csak a Azure Machine learning munkaterület nyomon követésére állítja be](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace), akkor a háttér-követési kiszolgáló a Azure Machine learning munkaterület.   

* **Ha a nevű regisztrált modell nem létezik**, a metódus regisztrálja az 1. verziót, és egy ModelVersion MLflow objektumot ad vissza. 

* **Ha már létezik ilyen nevű regisztrált modell**, a metódus létrehoz egy új modell-verziót, és visszaadja a Version objektumot. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>Végpontok létrehozása MLflow-modellekhez

Ha készen áll egy végpont létrehozására az ML-modellekhez. A következőképpen telepítheti a t, 

* Egy Azure Machine Learning Request-Response webszolgáltatás az interaktív pontozáshoz. Ez az üzembe helyezés lehetővé teszi a Azure Machine Learning-modell felügyeletének és az adateltolódás-észlelési képességeknek az éles modellekre való felhasználását és alkalmazását. 

* MLFlow, amely a streaming vagy batch folyamatokban használható Python functions vagy pandák UDF Azure Databricks munkaterületen.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Modellek üzembe helyezése Azure Machine Learning végpontokon 
A [mlflow. azureml. Deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) API-t használhatja modell üzembe helyezéséhez a Azure Machine learning munkaterületen. Ha a modellt csak a Azure Databricks munkaterületre regisztrálta, a [modellek regisztrálása MLflow](#register-models-with-mlflow) szakaszban leírtak szerint, akkor a `model_name` modellnek Azure Machine learning munkaterületre való regisztrálásához a paramétert kell megadnia. 

Azure Databricks futtatások a következő végpontokra telepíthetők. 
* [Azure Container-példány](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Azure Kubernetes Service](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Modellek üzembe helyezése az ADB-végpontokon a Batch-pontozáshoz 

Azure Databricks fürtöket kiválaszthatja a Batch pontozáshoz. A MLFlow modell be van töltve, és Spark pandák UDF-ként használja az új adatértékek kiértékeléséhez. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi a naplózott metrikák és összetevők használatát a munkaterületen, akkor a törlési képesség jelenleg nem érhető el. Ehelyett törölje a Storage-fiókot és-munkaterületet tartalmazó erőforráscsoportot, így nem számítunk fel díjat:

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.

   ![Törlés a Azure Portal](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet.

## <a name="example-notebooks"></a>Példajegyzetfüzetek

A [Azure Machine learning notebookokkal rendelkező MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) bemutatják és kibővítik a jelen cikkben ismertetett fogalmakat.

## <a name="next-steps"></a>Következő lépések
* [MLflow-modellek üzembe helyezése Azure-webszolgáltatásként](how-to-deploy-mlflow-models.md). 
* [A modellek kezelése](concept-model-management-and-deployment.md).
* [A kísérlet nyomon követése a MLflow és a Azure Machine learning](how-to-use-mlflow.md). 
* További információ a [Azure Databricks és a MLflow](/azure/databricks/applications/mlflow/).