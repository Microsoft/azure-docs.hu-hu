---
title: MLflow követése ML-kísérleteknél
titleSuffix: Azure Machine Learning
description: Állítsa be a MLflow-t a Azure Machine Learning használatával a mérőszámok és az összetevők a ML modellekből való naplózásához, és telepítse az ML-modelleket webszolgáltatásként.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 02684ba91c207357e15684870a6fa0ceab3e17ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520965"
---
# <a name="train-and-track-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>ML-modellek betanítása és nyomon követése MLflow és Azure Machine Learning (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan engedélyezheti a MLflow követési URI-és naplózási API-JÁT, azaz a [MLflow-követést](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), hogy összekapcsolja a Azure Machine Learningt a MLflow-kísérletek háttereként. 

A támogatott képességek a következők: 

+ A kísérlet metrikáinak és összetevőinek nyomon követése és naplózása a [Azure Machine learning munkaterületen](./concept-azure-machine-learning-architecture.md#workspace). Ha már használja a kísérletek MLflow követését, a munkaterület központosított, biztonságos és skálázható helyet biztosít a képzési mérőszámok és modellek tárolásához.

+ Betanítási feladatok elküldése [MLflow-projektekkel](https://www.mlflow.org/docs/latest/projects.html) Azure Machine learning háttér-támogatással (előzetes verzió). A feladatokat helyileg is elküldheti Azure Machine Learning nyomon követheti, vagy áttelepítheti a futtatásokat a felhőbe, például egy [Azure Machine learning-számításon](./how-to-create-attach-compute-cluster.md)keresztül.

+ Modellek követése és kezelése a MLflow és a Azure Machine Learning modell beállításjegyzékében.

A [MLflow](https://www.mlflow.org) egy nyílt forráskódú kódtár a gépi tanulási kísérletek életciklusának kezeléséhez. A MLFlow Tracking a MLflow egy olyan összetevője, amely naplózza és nyomon követi a képzési műveletek mérőszámait és modelljeit, függetlenül a kísérlet környezetében – helyileg a számítógépén, egy távoli számítási célponton, egy virtuális gépen vagy egy [Azure Databricks fürtön](how-to-use-mlflow-azure-databricks.md). 

>[!NOTE]
> A nyílt forráskódú kódtár MLflow gyakran változnak. Ennek megfelelően a Azure Machine Learning és a MLflow-integráción keresztül elérhető funkciókat előzetes verziónak kell tekinteni, és a Microsoft nem támogatja teljes mértékben.

Az alábbi ábra azt szemlélteti, hogy a MLflow nyomon követésével nyomon követheti a kísérlet futtatási metrikáit, és az Azure Machine Learning munkaterületen tárolhatja a modell összetevőit.

![mlflow az Azure Machine learning-diagrammal](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> A jelen dokumentumban található információk elsősorban olyan adatszakértők és fejlesztők számára készültek, akik figyelni szeretnék a modell betanítási folyamatát. Ha Ön olyan rendszergazda, aki a Azure Machine Learning erőforrás-használat és-események figyelését érdekli (például kvóták, befejezett betanítási futtatások vagy befejezett modellek üzembe helyezése), tekintse meg a [figyelés Azure Machine learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow és Azure Machine Learning ügyfelek összehasonlítása

 A következő táblázat összefoglalja azokat a különböző ügyfeleket, amelyek használhatják a Azure Machine Learningt, valamint a hozzájuk tartozó funkciókra vonatkozó képességeiket.

 A MLflow követése olyan metrikai naplózási és összetevő-tárolási funkciókat kínál, amelyek csak a [Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/intro)-n keresztül érhetők el.

| Képesség | MLflow követés & üzemelő példány | Python SDK Azure Machine Learning |  Azure Machine Learning CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Munkaterület kezelése |   | ✓ | ✓ | ✓ |
| Adattárak használata  |   | ✓ | ✓ | |
| Naplómetrikák      | ✓ | ✓ |   | |
| Összetevők feltöltése | ✓ | ✓ |   | |
| Metrikák megtekintése     | ✓ | ✓ | ✓ | ✓ |
| Számítások kezelése   |   | ✓ | ✓ | ✓ |
| Modellek üzembe helyezése    | ✓ | ✓ | ✓ | ✓ |
|Modell teljesítményének figyelése||✓|  |   |
| Adateltérés észlelése |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Előfeltételek

* Telepítse az `azureml-mlflow` csomagot. 
    * Ez a csomag automatikusan bevezeti a `azureml-core` [Azure Machine learning Python SDK](/python/api/overview/azure/ml/install)-t, amely biztosítja a kapcsolatot a MLflow a munkaterület eléréséhez.
* [Hozzon létre egy Azure Machine learning-munkaterület](how-to-manage-workspace.md).
    * Megtudhatja, [hogy mely hozzáférési engedélyek szükségesek a MLflow műveleteinek elvégzéséhez a munkaterületen](how-to-assign-roles.md#mlflow-operations).

## <a name="track-local-runs"></a>Helyi futtatások nyomon követése

A MLflow követése Azure Machine Learning lehetővé teszi a naplózott metrikák és összetevők tárolását a helyi futtatásokból a Azure Machine Learning-munkaterületre.

Importálja a `mlflow` és [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) osztályokat a MLflow követési URI-ja eléréséhez és a munkaterület konfigurálásához.

A következő kódban a `get_mlflow_tracking_uri()` metódus egyedi nyomkövetési URI-címeket rendel a munkaterülethez, `ws` és `set_tracking_uri()` a MLflow nyomon követi az adott címnek megfelelő URI-t.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>A nyomkövetési URI-azonosító legfeljebb egy órán belül érvényes. Ha némi üresjárati idő után újraindítja a szkriptet, a get_mlflow_tracking_uri API-val új URI-t kap.

Állítsa be a MLflow-kísérlet nevét a (z) értékre, `set_experiment()` és indítsa el a képzést a alkalmazással `start_run()` . Ezután `log_metric()` aktiválja a MLflow naplózási API-t, és kezdje meg a betanítási futtatási metrikák naplózását.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Távoli futtatások nyomon követése

A távoli futtatások lehetővé teszik, hogy a modelleket nagyobb teljesítményű számításokra, például GPU-t használó virtuális gépekre vagy Machine Learning Compute fürtökre tanítsa. A különböző számítási lehetőségek megismeréséhez lásd: [számítási célok használata a modell betanításához](how-to-set-up-training-targets.md) .

A MLflow követése Azure Machine Learning lehetővé teszi a naplózott metrikák és összetevők tárolását a távoli futtatásokból a Azure Machine Learning-munkaterületre. A MLflow-követési kóddal rendelkező futtatásakor a rendszer automatikusan naplózza a munkaterületre a metrikákat. 

A következő példában a Conda `mlflow` -környezet `azureml-mlflow` pip-csomagokat tartalmaz. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

A szkriptben konfigurálja a számítási és képzési környezetét a [`Environment`](/python/api/azureml-core/azureml.core.environment.environment) osztállyal. Ezután hozza létre a számítási  [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) célként a távoli számítási célt.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Ennek a számítási és betanítási futtatási konfigurációnak a használatával `Experiment.submit()` elküldheti a futtatást a metódussal. Ez a metódus automatikusan beállítja a MLflow követési URI-t, és a naplózást a MLflow a munkaterületre irányítja.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Betanítás MLflow-projektekkel

A [MLflow-projektek](https://mlflow.org/docs/latest/projects.html) lehetővé teszik a kód rendszerezését és leírását, hogy más adatszakértők (vagy automatizált eszközök) fussanak. A Azure Machine Learning MLflow-projektjei lehetővé teszik a képzési programok nyomon követését és kezelését a munkaterületen. 

Ez a példa azt mutatja be, hogyan lehet helyileg beküldeni a MLflow-projekteket Azure Machine Learning követéssel.

Telepítse a `azureml-mlflow` csomagot úgy, hogy a MLflow-követést helyi Azure Machine Learningon használja a kísérletekhez. A kísérletek egy Jupyter Notebook vagy Kódszerkesztő használatával futhatnak.

```shell
pip install azureml-mlflow
```

Importálja a `mlflow` és [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) osztályokat a MLflow követési URI-ja eléréséhez és a munkaterület konfigurálásához.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Állítsa be a MLflow-kísérlet nevét a (z) értékre, `set_experiment()` és indítsa el a képzést a alkalmazással `start_run()` . Ezután a használatával `log_metric()` aktiválja a MLflow naplózási API-t, és kezdje meg a betanítási futtatási metrikák naplózását.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Hozza létre a háttér-konfigurációs objektumot az integrációhoz szükséges információk tárolására, például a számítási célra és a használni kívánt felügyelt környezet típusára.

```python
backend_config = {"USE_CONDA": False}
```
Vegye fel a `azureml-mlflow` csomagot pip-függőségként a környezet konfigurációs fájljába, hogy nyomon követhesse a mérőszámokat és a kulcsfontosságú összetevőket a munkaterületen. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Küldje el a helyi futtatást, és győződjön meg róla, hogy beállította a paramétert `backend = "azureml" ` . Ezzel a beállítással helyileg is elküldheti a futtatásokat, és az automatikus kimeneti nyomon követés, a naplófájlok, a pillanatképek és a kinyomtatott hibák további támogatását a munkaterületen teheti meg. 

A futtatások és mérőszámok megtekintése a [Azure Machine learning Studióban](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Metrikák és összetevők megtekintése a munkaterületen

A MLflow-naplózás metrikái és összetevői a munkaterületen maradnak. Ha bármikor meg szeretné tekinteni őket, keresse meg a munkaterületet, és keresse meg a kísérletet név szerint a munkaterületen a [Azure Machine learning Studióban](https://ml.azure.com).  Vagy futtassa az alábbi kódot. 

```python
run.get_metrics()
```

## <a name="manage-models"></a>Modellek kezelése 

Regisztrálja és kövesse a modelleket a [Azure Machine learning modell beállításjegyzékével](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) , amely támogatja a MLflow-modell beállításjegyzékét. Azure Machine Learning modellek a MLflow modell sémájának megfelelően vannak igazítva, így ezek a modellek egyszerűen exportálhatók és importálhatók különböző munkafolyamatokban. A MLflow kapcsolódó metaadatok, például a, a Run ID a nyomkövetéshez regisztrált modellel is címkével rendelkeznek. A felhasználók elküldhetik a MLflow-futtatásokból készített betanítási, regisztrálási és üzembe helyezési modelleket. 

Ha egy lépésben szeretné üzembe helyezni és regisztrálni az üzemi használatra kész modellt, tekintse meg a [MLflow-modellek üzembe helyezése és regisztrálása](how-to-deploy-mlflow-models.md)című témakört.

Egy modell egy futtatásból való regisztrálásához és megtekintéséhez kövesse az alábbi lépéseket:

1. A Futtatás befejezése után hívja meg a `register_model()` metódust.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Tekintse meg a regisztrált modellt a munkaterületen a [Azure Machine learning Studióval](overview-what-is-machine-learning-studio.md).

    A következő példában a regisztrált modell `my-model` MLflow követési metaadatokat jelölt ki. 

    ![regisztráció – mlflow – modell](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. **Az összetevők lapon** megtekintheti a MLflow-modell sémához (Conda. YAML, MLmodel, Model. PKL) igazított összes modellt.

    ![modell – séma](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Válassza a MLmodel lehetőséget a Futtatás által generált MLmodel-fájl megtekintéséhez.

    ![MLmodel – séma](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem tervezi a naplózott metrikák és összetevők használatát a munkaterületen, akkor a törlési képesség jelenleg nem érhető el. Ehelyett törölje a Storage-fiókot és-munkaterületet tartalmazó erőforráscsoportot, így nem számítunk fel díjat:

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal szélen.

   ![Törlés a Azure Portal](./media/how-to-use-mlflow/delete-resources.png)

1. Válassza ki a listában az Ön által létrehozott erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be az erőforráscsoport nevét. Ezután válassza a **Törlés** elemet.

## <a name="example-notebooks"></a>Példajegyzetfüzetek

Az [Azure ml jegyzetfüzetekkel rendelkező MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) bemutatják és kibővítik a jelen cikkben ismertetett fogalmakat.

> [!NOTE]
> A mlflow-t használó példák Közösség által vezérelt tárháza a következő címen érhető el: https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Következő lépések

* [Modellek üzembe helyezése a MLflow](how-to-deploy-mlflow-models.md).
* Figyelje az [adateltolódáshoz](./how-to-enable-data-collection.md)használt üzemi modelleket.
* [Nyomon követheti Azure Databricks futtatását a MLflow](how-to-use-mlflow-azure-databricks.md).
* [A modellek kezelése](concept-model-management-and-deployment.md).