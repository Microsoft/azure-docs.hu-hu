---
title: '& értelmezése a Pythonban (előzetes verzió): ML modellek'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állapíthatja meg, hogy a gépi tanulási modell hogyan határozza meg a funkció fontosságát, és előrejelzéseket készít az Azure Machine Learning SDK használatakor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: fda1bc2ef0a112a8a32ba7c4caebf29028c8cdd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222751"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Az adatelemzési csomag használata a Python (előzetes verzió) &i előrejelzésekben



Ebben a útmutatóban megtudhatja, hogyan használhatja a Azure Machine Learning Python SDK értelmező csomagját a következő feladatok elvégzéséhez:


* Ismertesse a modell teljes viselkedését vagy egyéni előrejelzéseit helyi gépen.

* Értelmező módszerek engedélyezése a megtervezett funkciókhoz.

* Ismertesse a teljes modell és az egyéni előrejelzések viselkedését az Azure-ban.

* Vizualizációs irányítópult használatával kommunikálhat a modell magyarázatával.

* A modell mellett egy pontozási magyarázatot is üzembe helyezhet, hogy figyelembe vegye a magyarázatokat a következtetés során.



További információ a támogatott értelmező módszerekről és a gépi tanulási modellekről: Azure Machine Learning és [minta-jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) [modellezése](how-to-machine-learning-interpretability.md) .

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>A szolgáltatás fontossági értékének előállítása a személyes gépen 
Az alábbi példa azt mutatja be, hogyan használható a értelmező csomag a személyes gépen anélkül, hogy az Azure-szolgáltatásokkal kellene kapcsolatba lépnie.

1. Telepítse az `azureml-interpret` csomagot.
    ```bash
    pip install azureml-interpret
    ```

2. Egy minta modell betanítása helyi Jupyter Notebookban.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

3. A magyarázatot helyileg hívja meg.
   * Egy magyarázó objektum inicializálásához adja át a modelljét és a betanítási adatait az elmagyarázó konstruktorának.
   * Annak érdekében, hogy a magyarázatokat és a vizualizációkat részletesebben is elvégezze, dönthet úgy, hogy a szolgáltatás nevét és a kimeneti osztály nevét adja meg, ha a besorolást végzi.

   A következő kódrészletek bemutatják, hogyan hozható létre egy magyarázó objektum a `TabularExplainer` , a `MimicExplainer` és a `PFIExplainer` helyileg.
   * `TabularExplainer` a három SHAP-magyarázat egyikét hívja meg ( `TreeExplainer` , `DeepExplainer` , vagy `KernelExplainer` ).
   * `TabularExplainer` automatikusan kiválasztja a legmegfelelőbbet a használati esethez, de a három mögöttes magyarázatot közvetlenül is meghívhatja.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    vagy

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    vagy

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>A modell teljes viselkedésének ismertetése (globális magyarázat) 

Az alábbi példát követve megtekintheti az aggregált (globális) szolgáltatás fontossági értékeit.

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Egyéni előrejelzések magyarázata (helyi magyarázat)
A különböző datapoints az egyes funkciók fontossági értékeit az egyes példányok vagy példányok egy csoportjára vonatkozó magyarázatok meghívásával érheti el.
> [!NOTE]
> `PFIExplainer` a nem támogatja a helyi magyarázatokat.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Nyers funkciók átalakítása

Megadhatja, hogy a mesterséges funkciók helyett a nyers, nem átalakított funkciókra vonatkozó magyarázatokat kapjon. Ebben a beállításban a funkció átalakítási folyamatát átadja a magyarázatnak `train_explain.py` . Ellenkező esetben a magyarázó elemek a megfogalmazott funkciókra vonatkozó magyarázatokat biztosítanak.

A támogatott átalakítások formátuma megegyezik a [sklearn-pandák](https://github.com/scikit-learn-contrib/sklearn-pandas)című témakörben leírtak szerint. Általánosságban elmondható, hogy az átalakítások csak akkor támogatottak, ha egyetlen oszlopon működnek, így egyértelmű, hogy egy-a-többhöz.

Tekintse át a nyers funkciókra vonatkozó magyarázatot a `sklearn.compose.ColumnTransformer` vagy a beépített transzformátor-rekordok listájának használatával. A következő példa a-t használja `sklearn.compose.ColumnTransformer` .

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Ha szeretné futtatni a példát a beszerelt transzformátor-rekordok listájával, használja a következő kódot:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="generate-feature-importance-values-via-remote-runs"></a>Szolgáltatás fontossági értékeinek előállítása távoli futtatások használatával

Az alábbi példa bemutatja, hogyan használható az `ExplanationClient` osztály a modell-értelmező távoli futtatásokhoz való engedélyezéséhez. Elméletileg hasonló a helyi folyamathoz, a következők kivételével:

* `ExplanationClient`Töltse fel a értelmező környezetet a távoli Futtatás paranccsal.
* A környezet későbbi letöltése helyi környezetben.

1. Telepítse az `azureml-interpret` csomagot.
    ```bash
    pip install azureml-interpret
    ```
1. Hozzon létre egy képzési parancsfájlt egy helyi Jupyter Notebookban. Például: `train_explain.py`.

    ```python
    from azureml.interpret import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Állítson be Azure Machine Learning számítást számítási célként, és küldje be a betanítási futtatást. Útmutatásért lásd: [Azure Machine learning számítási fürtök létrehozása és kezelése](how-to-create-attach-compute-cluster.md) . A [példaként használható jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) is hasznosak lehetnek.

1. Töltse le a magyarázatot a helyi Jupyter Notebook.

    ```python
    from azureml.interpret import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```


## <a name="visualizations"></a>Vizualizációk

Miután letöltötte a magyarázatokat a helyi Jupyter Notebook, a vizualizáció irányítópultján megismerheti és értelmezheti a modelljét. A vizualizációs irányítópult widgetnek a Jupyter Notebook való betöltéséhez használja a következő kódot:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

A vizualizáció mind a mérnöki, mind a nyers funkciók magyarázatait támogatja. A nyers magyarázatok az eredeti adatkészlet szolgáltatásain alapulnak, és a megtervezett magyarázatok az adatkészlet azon szolgáltatásain alapulnak, amelyek az adathalmazt használják.

Ha az eredeti adatkészletre vonatkozó modellt próbál értelmezni, ajánlott nyers magyarázatot használni, mivel az egyes funkciók fontossága megegyezik az eredeti adatkészlet egy oszlopával. Az egyik forgatókönyv, ahol a megtervezett magyarázatok hasznosak lehetnek, az egyes kategóriáknak a kategorikus funkcióból való hatásának vizsgálatakor. Ha egy-egy gyors kódolást alkalmaz egy kategorikus szolgáltatásra, akkor az eredményül kapott magyarázatok eltérő fontossági értéket tartalmaznak kategóriánként, egy-egy gyors tervezésű funkció alapján. Ez akkor lehet hasznos, ha leszűkíti, hogy az adatkészlet mely része a modellnek.

> [!NOTE]
> A tervezés és a nyers magyarázatok egymás után vannak kiszámítva. Először a modell-és featurization folyamat alapján létrehoztak egy megtervezett magyarázatot. Ezt követően a rendszer a megjelenő magyarázat alapján hozza létre a nyers magyarázatot úgy, hogy összegyűjti az azonos nyers szolgáltatásból származó mérnök funkciók fontosságát.

### <a name="create-edit-and-view-dataset-cohorts"></a>Adathalmazok létrehozása, szerkesztése és megtekintése

A felső szalag a modell és az adatok összesített statisztikáit jeleníti meg. Az adatokat adatkészletbeli kohorszok vagy alcsoportok számára is kioszthatja és felszámíthatja, hogy kivizsgálja vagy összehasonlítsa a modell teljesítményét és magyarázatait ezen definiált alcsoportokon belül. Ha összehasonlítja az adatkészlet statisztikáit és magyarázatait ezekben az alcsoportokban, láthatja, hogy a lehetséges hibák miért történnek egy csoportban, és egy másikkal szemben.

[![Adatkészletek kohorszok létrehozása, szerkesztése és megtekintése](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>A teljes modell működésének megismerése (globális magyarázat) 

A magyarázatok irányítópultjának első három lapja a betanított modell átfogó elemzését, valamint annak előrejelzéseit és magyarázatait tartalmazza.

#### <a name="model-performance"></a>A modell teljesítménye
Értékelje ki a modell teljesítményét, és vizsgálja meg az előrejelzési értékek eloszlását, valamint a modell teljesítmény-metrikáinak értékeit. A modell további vizsgálatához tekintse meg a teljesítményének összehasonlító elemzését az adathalmaz különböző kohorszok vagy alcsoportjai között. Válassza a szűrők lehetőséget az y-érték és az x-érték között a különböző dimenziók közötti kivágáshoz. Megtekintheti a mérőszámokat, például a pontosságot, a pontosságot, a visszahívást, a hamis pozitív arányt (FPR) és a hamis negatív (FNR) értéket

[![A modell teljesítmény lapja a magyarázó vizualizációban](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Adatkészlet-kezelő
Az adatkészletek statisztikáit úgy tekintheti meg, hogy különböző szűrőket választ ki az X, Y és Color tengelyeken az adatok különböző dimenziók mentén történő szeleteléséhez. Hozzon létre adatkészletet a fentiekben, hogy elemezze az adatkészlet statisztikáit olyan szűrőkkel, mint az előre jelzett eredmény, az adatkészlet szolgáltatásai és a hibajelentések. A Graph-típusok módosításához használja a gráf jobb felső sarkában található fogaskerék ikont.

[![Az adatkészlet Explorer lapja a magyarázati vizualizációban](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Összesítő funkció fontossága
Ismerkedjen meg az általános modell-előrejelzéseket (más néven globális magyarázatot) érintő legfontosabb funkciókkal. A csúszka használatával jelenítheti meg a csökkenő funkciók fontossági értékeit. Válassza ki a legfeljebb három kohorszot, és tekintse meg a funkció fontossági értékeit egymás mellett. Kattintson a Graph valamelyik funkciójának egyikére, és tekintse meg, hogy a kiválasztott funkció milyen hatással van a modell előrejelzésére az alábbi függőségi ábrán.

[![Összesítő funkció fontossága lap a magyarázó vizualizációban](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Egyéni előrejelzések ismertetése (helyi magyarázat) 

A magyarázat lap negyedik lapja lehetővé teszi az egyéni Datapoint és az egyes funkciók fontosságának részletezését. Bármely adatponthoz betöltheti az egyes funkció fontossági területét, ha a fő pontdiagram bármelyik adatpontján rákattint, vagy kiválasztja a jobb oldali panel varázsló egy adott Datapoint.

|Telek|Description|
|----|-----------|
|Az egyes funkciók fontossága|Az egyéni előrejelzés legfontosabb funkcióit jeleníti meg. Segít bemutatni az alapul szolgáló modell helyi viselkedését egy adott adatponton.|
|What-If elemzése|Lehetővé teszi a kijelölt valós adatponthoz tartozó szolgáltatások értékének módosítását, és megfigyelheti az előrejelzési értékhez képest létrejövő változásokat egy feltételezett Datapoint az új szolgáltatás értékeivel való létrehozásával.|
|Egyéni feltételes várakozás (ICE)|Lehetővé teszi a szolgáltatás értékének a minimális értékről a maximális értékre való módosítását. Segít bemutatni, hogy az adatpont előrejelzése hogyan változik meg egy szolgáltatás módosításakor.|

[![Az egyes funkciók fontossága és a magyarázat irányítópultja](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Ezek a magyarázatok számos közelítésen alapulnak, és nem az előrejelzések oka. Az oksági következtetések szigorú matematikai szilárdsága nélkül nem javasoljuk, hogy a felhasználók a What-If eszköz funkcióinak perturbations alapuló valós döntéseket hozzanak. Ez az eszköz elsősorban a modell és a hibakeresés megismerésére szolgál.

### <a name="visualization-in-azure-machine-learning-studio"></a>Vizualizáció a Azure Machine Learning Studióban

Ha elvégezte a [távoli értelmezési](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) lépéseket (a generált magyarázat feltöltése Azure Machine learning futtatási előzményekre), megtekintheti a vizualizációs irányítópultot [Azure Machine learning Studióban](https://ml.azure.com). Ez az irányítópult a vizualizációk irányítópultjának a fent ismertetett egyszerűbb verziója. What-If a Datapoint-generáció és a jég-mintaterületek le vannak tiltva, mert a Azure Machine Learning Studióban nincs aktív számítási művelet, amely valós idejű számításokat végezhet.

Ha az adatkészlet, a globális és a helyi magyarázat elérhető, az adatok feltöltik az összes lapot. Ha csak globális magyarázat áll rendelkezésre, az egyes funkciók fontossága lap le lesz tiltva.

Kövesse az alábbi elérési utakat a vizualizációs irányítópult eléréséhez Azure Machine Learning Studióban:

* **Kísérletek** panel (előzetes verzió)
  1. Válassza a **kísérletek** lehetőséget a bal oldali ablaktáblán a Azure Machine learning futtatott kísérletek listájának megjelenítéséhez.
  1. Válasszon ki egy adott kísérletet a kísérlet összes futtatásának megtekintéséhez.
  1. Válasszon egy futtatást, majd a **magyarázatok lapot a** magyarázatok vizualizációs irányítópultján.

   [![Vizualizációs irányítópult összesített funkcióval a AzureML Studióban a kísérletek során](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* **Modellek** panel
  1. Ha regisztrálta az eredeti modelljét a [modellek Azure Machine learning használatával történő üzembe helyezésének](./how-to-deploy-and-where.md)lépéseivel, a bal oldali ablaktáblán kiválaszthatja a **modelleket** , és megtekintheti.
  1. Válasszon ki egy modellt, majd a **magyarázatok** lapon tekintse meg a magyarázó vizualizáció irányítópultját.

## <a name="interpretability-at-inference-time"></a>Tolmácsolás a következtetés időpontjában

A magyarázatot az eredeti modellel is üzembe helyezheti, és a következtetési idő alatt megadhatja az egyes funkciók fontossági értékeit (helyi magyarázatot) minden új Datapoint. A könnyebb súlyú pontozási magyarázatokat is kínáljuk, amelyek az értelmező teljesítményének javulására szolgálnak, ami jelenleg csak Azure Machine Learning SDK-ban támogatott. A könnyebb súlyozású pontozási elmagyarázó üzembe helyezésének folyamata hasonló a modellek üzembe helyezéséhez, és a következő lépéseket tartalmazza:

1. Hozzon létre egy magyarázat objektumot. Például a `TabularExplainer` következőket használhatja:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Hozzon létre egy pontozási magyarázatot a magyarázat objektummal.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Egy pontozási bemutató modellt használó rendszerkép konfigurálása és regisztrálása.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Választható lépésként lekérheti a pontozási magyarázatot a felhőből, és tesztelheti a magyarázatokat.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Telepítse a lemezképet egy számítási célra a következő lépésekkel:

   1. Ha szükséges, regisztrálja az eredeti előrejelzési modellt a [modellek üzembe helyezése Azure Machine learning](./how-to-deploy-and-where.md)használatával című témakörben ismertetett lépéseket követve.

   1. Hozzon létre egy pontozási fájlt.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Az üzembe helyezési konfiguráció meghatározása.

         Ez a konfiguráció a modell követelményeitől függ. A következő példa egy olyan konfigurációt határoz meg, amely egy CPU-mag és egy GB memóriát használ.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Hozzon létre egy fájlt környezeti függőségekkel.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Hozzon létre egy egyéni Docker, amelynek a g + + telepítve van.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. A létrehozott rendszerkép üzembe helyezése.
   
         Ez a folyamat körülbelül öt percet vesz igénybe.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Tesztelje az üzemelő példányt.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Karbantartás.

   Központilag telepített webszolgáltatás törléséhez használja a következőt: `service.delete()` .

## <a name="troubleshooting"></a>Hibaelhárítás

* A **ritka adatmennyiségek nem támogatottak**: a modell magyarázatának irányítópultja megszakítja a jelentős mennyiségű funkciót, ezért jelenleg nem támogatott a ritka adatformátum. Emellett az általános memóriával kapcsolatos problémák nagy adatkészletekkel és nagy számú funkcióval is felmerülhetnek. 

* Az **előrejelzési modellek nem támogatottak a modell magyarázatával**: az értelmezés, a legjobb modell magyarázata nem érhető el a következő algoritmusokat a legjobb modellként támogató AutoML-előrejelzési kísérletekhez: TCNForecaster, AutoArima, próféta, ExponentialSmoothing, Average, naiv, szezonális átlag és szezonális naiv. A AutoML előrejelzése regressziós modelleket tartalmaz, amelyek támogatják a magyarázatot. A magyarázat irányítópulton azonban az "egyéni funkciók fontossága" lap nem támogatott az előrejelzéshez az adatfolyamatok összetettsége miatt.

* Az **adatindex helyi magyarázata**: a magyarázat irányítópultja nem támogatja a helyi fontossági értékeket az eredeti ellenőrzési adatkészlet sorainak azonosítójára, ha az adatkészlet nagyobb, mint 5000 datapoints, mivel az irányítópult véletlenszerűen downsamples az adatokat. Az irányítópult azonban megjeleníti a nyers adatkészlet-szolgáltatás értékeit az irányítópulton az egyes szolgáltatások fontossága lap alatt átadott Datapoint vonatkozóan. A felhasználók a nyers adatkészlet szolgáltatás értékeinek megfelelő módon leképezhetők a helyi fontosságot az eredeti adatkészletbe. Ha az érvényesítési adatkészlet mérete kisebb, mint 5000 minta, a `index` AzureML Studio funkciója az érvényesítési adatkészletben szereplő indexnek fog megfelelni.

* A Studio: What-If és az egyéni feltételes várakozási (jég) mintaterületek **nem támogatottak** a Azure Machine learning Studióban a magyarázatok lapon, mivel a feltöltött magyarázatnak aktív számításra van szüksége az előrejelzések és a zaklatott funkciók valószínűségének újraszámításához. Jelenleg a Jupyter notebookok esetében támogatott, ha az SDK-val widgetet használ.


## <a name="next-steps"></a>Következő lépések

[További információ a modell értelmezéséről](how-to-machine-learning-interpretability.md)

[Tekintse meg Azure Machine Learning a értelmező minta notebookok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
