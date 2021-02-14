---
title: Fejlesztés a AutoML & Azure Databricks
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állíthat be fejlesztési környezetet Azure Machine Learning és Azure Databricks. Használja az Azure ML SDK-kat Databricks és Databricks a AutoML-mel.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: daeb0d666c0dfb8306663da1d8d59dfba6adc359
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516923"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Fejlesztési környezet beállítása Azure Databricks-és AutoML a Azure Machine Learning 

Megtudhatja, hogyan konfigurálhat olyan fejlesztési környezetet Azure Machine Learningban, amely Azure Databricks és automatizált ML-t használ.

A Azure Databricks ideális megoldás a nagy léptékű, intenzív gépi tanulási munkafolyamatok futtatására az Azure-felhő skálázható Apache Spark platformján. Együttműködési jegyzetfüzet-alapú környezetet biztosít CPU vagy GPU-alapú számítási fürttel.

További információ a gépi tanulási fejlesztési környezetekről: a [Python fejlesztői környezet beállítása](how-to-configure-environment.md).


## <a name="prerequisite"></a>Előfeltétel

Azure Machine Learning munkaterület. Ha még nem rendelkezik ilyennel, létrehozhat egy Azure Machine Learning munkaterületet az [Azure Portal](how-to-manage-workspace.md), az [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)és a [Azure Resource Manager sablonok](how-to-create-workspace-template.md)segítségével.


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks Azure Machine Learning és AutoML

A Azure Databricks a Azure Machine Learning és annak AutoML képességeivel integrálódik. 

Használhatja Azure Databricks:

+ Modell betanítása a Spark MLlib használatával és a modell üzembe helyezése ACI/AK-ra.
+ [Automatizált gépi tanulási](concept-automated-ml.md) képességekkel az Azure ml SDK használatával.
+ Számítási cél egy [Azure Machine learning folyamatból](concept-ml-pipelines.md).

## <a name="set-up-a-databricks-cluster"></a>Databricks-fürt beállítása

Hozzon létre egy [Databricks-fürtöt](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Egyes beállítások csak akkor érvényesek, ha az SDK-t a Databricks-on lévő automatizált gépi tanuláshoz telepíti.

**A fürt létrehozása néhány percet vesz igénybe.**

Használja ezeket a beállításokat:

| Beállítás |A következőre érvényes:| Érték |
|----|---|---|
| Fürt neve |mindig| yourclustername |
| Databricks Runtime verziója |mindig| Futtatókörnyezet 7,1 (Scala 2,21, Spark 3.0.0) – nem ML|
| Python-verzió |mindig| 3 |
| Feldolgozó típusa <br>(meghatározza az egyidejű ismétlések maximális számát) |Automatizált ML<br>csak| A memóriára optimalizált virtuális gép előnyben részesített |
| Feldolgozók |mindig| 2 vagy magasabb |
| Automatikus skálázás engedélyezése |Automatizált ML<br>csak| Törölje a jelet a  |

A folytatás előtt várjon, amíg a fürt fut.

## <a name="add-the-azure-ml-sdk-to-databricks"></a>Az Azure ML SDK hozzáadása a Databricks-hez

Ha a fürt fut, [hozzon létre egy függvénytárat](https://docs.databricks.com/user-guide/libraries.html#create-a-library) , hogy csatolja a megfelelő Azure Machine learning SDK-csomagot a fürthöz. 

Az automatikus ML használatához ugorjon [Az Azure ml SDK és a AutoML hozzáadásához](#add-the-azure-ml-sdk-with-automl-to-databricks).


1. Kattintson a jobb gombbal arra a munkaterület-mappára, ahol a könyvtárat tárolni szeretné. Válassza a  >  **könyvtár** létrehozása lehetőséget.
    
    > [!TIP]
    > Ha régi SDK-verzióval rendelkezik, törölje a fürtöt a telepített tárak közül, és váltson a kukába. Telepítse az új SDK-verziót, és indítsa újra a fürtöt. Ha az újraindítás után probléma merül fel, válassza le és csatlakoztassa újra a fürtöt.

1. Válassza a következő lehetőséget (más SDK-telepítések nem támogatottak)

   |SDK- &nbsp; csomag &nbsp; extrái|Forrás|PyPi &nbsp; neve&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks| Python-tojás vagy PyPI feltöltése | azureml – SDK [databricks]|

   > [!WARNING]
   > Más SDK-extrák nem telepíthetők. Válassza a csak a [ `databricks` ] lehetőséget.

   * Ne válassza **az Automatikus csatolás az összes fürthöz** lehetőséget.
   * Válassza a  **csatolás** elemet a fürt neve mellett.

1. A hibák figyelése, amíg az állapot a **csatolt** értékre módosul, ami több percet is igénybe vehet.  Ha ez a lépés meghiúsul:

   Próbálja meg újraindítani a fürtöt a alábbiak szerint:
   1. A bal oldali ablaktáblán válassza a **fürtök** lehetőséget.
   1. A táblázatban válassza ki a fürt nevét.
   1. A **tárak** lapon válassza az **Újraindítás** lehetőséget.

   A sikeres telepítés a következőhöz hasonlóan néz ki: 

  ![Azure Machine Learning SDK a Databricks-hez](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Adja hozzá az Azure ML SDK-t a AutoML és a Databricks
Ha a fürt Databricks Runtime 7,1-as vagy újabb verzióval lett létrehozva (*nem* ml), futtassa a következő parancsot a jegyzetfüzet első cellájában a pénzmosás SDK telepítéséhez.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Az 7,0-es és alacsonyabb Databricks Runtime esetén telepítse a Azure Machine Learning SDK-t az [init parancsfájllal](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).

### <a name="automl-config-settings"></a>AutoML konfigurációs beállításai

A AutoML config Azure Databricks használatakor adja hozzá a következő paramétereket:

- ```max_concurrent_iterations``` a fürt munkavégző csomópontjainak száma alapján történik.
- ```spark_context=sc``` az alapértelmezett Spark-környezeten alapul.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>Azure Databricks-vel dolgozó ML-jegyzetfüzetek

Próbálja ki:
+ Habár több jegyzetfüzet is elérhető, **csak ezek a [minta-jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) működnek együtt Azure Databricksokkal.**

+ Importálja ezeket a mintákat közvetlenül a munkaterületről. Lásd alább: válassza az importálás importálás ![ ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ panelt](./media/how-to-configure-environment/azure-db-import.png)

+ Megtudhatja, hogyan [hozhat létre egy folyamatot a Databricks, mint a betanítási számításokat](./how-to-create-machine-learning-pipelines.md).

## <a name="troubleshooting"></a>Hibaelhárítás

* **Hiba a csomagok telepítésekor**

    Azure Machine Learning SDK telepítése sikertelen Azure Databricks Ha további csomagok vannak telepítve. Bizonyos csomagok, például a `psutil` , ütközéseket okozhatnak. A telepítési hibák elkerülése érdekében telepítse a csomagokat a könyvtár verziószámának lefagyasztásával. Ez a probléma a Databricks és nem a Azure Machine Learning SDK-val kapcsolatos. Előfordulhat, hogy ezt a problémát más kódtárak is megtapasztalják. Példa:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Azt is megteheti, hogy init-parancsfájlokat használ, ha a Python-kódtárakkal együtt tartja a telepítési problémákat. Ez a megközelítés nem támogatott hivatalosan. További információ: [fürtökre kiterjedő init-parancsfájlok](/azure/databricks/clusters/init-scripts#cluster-scoped-init-scripts).

* **Importálási hiba: a név nem importálható `Timedelta` innen `pandas._libs.tslibs`**: Ha az automatikus gépi tanulást használja, futtassa a következő két sort a jegyzetfüzetben:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Importálási hiba: nincs "pandák. Core. Indexes" nevű modul**: Ha a következő hibaüzenetet látja, amikor automatikus gépi tanulást használ:

    1. Futtassa ezt a parancsot két csomag telepítéséhez a Azure Databricks-fürtön:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Válassza le, majd csatlakoztassa újra a fürtöt a jegyzetfüzethez.
    
    Ha ezek a lépések nem oldják meg a problémát, próbálja meg újraindítani a fürtöt.

* **FailToSendFeather**: Ha `FailToSendFeather` Azure Databricks-fürtön lévő adatolvasáskor hibaüzenet jelenik meg, tekintse át a következő megoldásokat:
    
    * `azureml-sdk[automl]`A csomag frissítése a legújabb verzióra.
    * Adja hozzá a `azureml-dataprep` 1.1.8 vagy újabb verziót.
    * Adja hozzá a `pyarrow` 0,11-es vagy újabb verziót.
  

## <a name="next-steps"></a>Következő lépések

- Azure Machine Learning [modell betanítása](tutorial-train-models-with-aml.md) a MNIST adatkészlettel.
- Tekintse [meg a Pythonhoz készült Azure Machine learning SDK-referenciát](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).