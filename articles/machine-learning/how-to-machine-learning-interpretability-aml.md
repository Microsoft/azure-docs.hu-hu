---
title: Gépi tanulási modellek & értelmezése Pythonban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan kérhető le magyarázat arra, hogy a gépi tanulási modell hogyan határozza meg a funkciók fontosságát, és hogyan tesz előrejelzéseket az Azure Machine Learning SDK használatakor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: 2d60c6dbedb24847b95ce268bedafcb073421319
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576516"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Az értelmezhetőségi csomaggal ismertetheti a gépi tanulási modelleket és & Pythonban (előzetes verzió)

Ebből az útmutatóból megtudhatja, hogyan használhatja az Azure Machine Learning Python SDK értelmezhetőségi csomagját a következő feladatok végrehajtásához:


* Helyileg ismertesse a modell teljes viselkedését vagy egyéni előrejelzéseit a személyes gépen.

* Értelmezhetőségi technikák engedélyezése a tervezett funkciókhoz.

* A teljes modell és az egyéni előrejelzések viselkedésének magyarázata az Azure-ban.

* Vizualizációs irányítópulton használhatja a modell magyarázatát.

* Üzembe helyezhet egy pontozó magyarázó pontot a modell mellett, hogy megfigyelje a magyarázatokat a következtetés során.



A támogatott értelmezési technikákkal és gépi tanulási [](how-to-machine-learning-interpretability.md) modellekkel kapcsolatos további információkért lásd: Modellértelmezhetőség Azure Machine Learning [notebookok esetében.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Fontosságérték létrehozása a személyes gépen 
Az alábbi példa bemutatja, hogyan használható az értelmezhetőségi csomag a személyes gépen anélkül, hogy kapcsolatba lépünk az Azure-szolgáltatásokkal.

1. Telepítse az `azureml-interpret` csomagot.
    ```bash
    pip install azureml-interpret
    ```

2. Betanít egy mintamodellt egy helyi Jupyter Notebook.

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

3. Hívja meg helyileg az magyarázót.
   * Egy magyarázó objektum inicializálásához adja át a modellt és néhány betanításadatot az magyarázó konstruktorának.
   * A magyarázatok és vizualizációk informatívabb megjelenítéséhez besorolás esetén megadhatja a szolgáltatásneveket és a kimeneti osztályneveket.

   A következő kódblokkok azt mutatják be, hogyan példányosulhat egy magyarázó objektum a `TabularExplainer` , a és a helyi `MimicExplainer` `PFIExplainer` példányával.
   * `TabularExplainer` az alatta lévő három SHAP-magyarázó `TreeExplainer` (, `DeepExplainer` , vagy ) valamelyikét hívja `KernelExplainer` meg.
   * `TabularExplainer` A automatikusan kiválasztja a legmegfelelőbbet az Adott esethez, de a három mögöttes magyarázó mindegyikét közvetlenül is meg lehet hívni.

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

### <a name="explain-the-entire-model-behavior-global-explanation"></a>A modell teljes viselkedésének magyarázata (globális magyarázat) 

Az összesített (globális) funkció fontos értékeinek lekért értékeivel kapcsolatban tekintse meg az alábbi példát.

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

### <a name="explain-an-individual-prediction-local-explanation"></a>Egyéni előrejelzés magyarázata (helyi magyarázat)
A különböző adatpontok egyéni fontosságértékének lehívása az egyes példányok vagy példányok egy csoportjának magyarázatainak hívása alapján.
> [!NOTE]
> `PFIExplainer` nem támogatja a helyi magyarázatokat.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Nyers jellemzőátalakítások

Dönthet úgy, hogy magyarázatokat kap a nyers, nem átformált funkciókról, és nem a tervezett funkciókról. Ehhez a lehetőséghez át kell adni a funkcióátalakítási folyamatot a magyarázónak `train_explain.py` a következőben: . Ellenkező esetben a magyarázó a megtervezett jellemzőkre vonatkozó magyarázatokat ad meg.

A támogatott átalakítások formátuma megegyezik az [sklearn-pandas fájlban leírt formátummal.](https://github.com/scikit-learn-contrib/sklearn-pandas) Az átalakítások általában akkor támogatottak, ha egyetlen oszlopon működnek, így egyértelmű, hogy egy-a-többök.

Magyarázat a nyers jellemzőkről egy vagy egy illeszthető transzformátor-lista `sklearn.compose.ColumnTransformer` használatával. Az alábbi példa a következőt `sklearn.compose.ColumnTransformer` használja: .

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

Ha a példát az illeszthető transzformátor-tupupok listájával szeretné futtatni, használja a következő kódot:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Funkció fontos értékeinek létrehozása távoli futtatásokkal

Az alábbi példa bemutatja, hogyan engedélyezheti a modell értelmezhetőségét a távoli `ExplanationClient` futtatáshoz a osztály használatával. Ez fogalmilag hasonló a helyi folyamathoz, kivéve a következőt:

* Az `ExplanationClient` értelmezhetőségi környezet feltöltése a távoli futtatásban a használatával.
* Töltse le a környezetet később egy helyi környezetben.

1. Telepítse az `azureml-interpret` csomagot.
    ```bash
    pip install azureml-interpret
    ```
1. Hozzon létre egy betanító szkriptet egy helyi Jupyter Notebook. Például: `train_explain.py`.

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

1. Állítson be egy számítási Azure Machine Learning számítási célként, és küldje el a betanítás futtatását. Útmutatásért [lásd Azure Machine Learning számítási fürtök létrehozása és](how-to-create-attach-compute-cluster.md) kezelése. Hasznosnak találhatja a [példajegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) is.

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

Miután letöltötte a magyarázatokat a helyi Jupyter Notebook, a magyarázatok irányítópultján található vizualizációk segítségével megértheti és értelmezheti a modellt. A magyarázatok irányítópult-vezérlő betöltéséhez a Jupyter Notebook a következő kódot használja:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

A vizualizációk mind a tervezett, mind a nyers jellemzők magyarázatát támogatják. A nyers magyarázatok az eredeti adatkészlet jellemzőin alapulnak, a megtervezett magyarázatok pedig az adatkészlet funkcióin alapulnak, és jellemzőkitervezést alkalmaznak.

Amikor az eredeti adatkészletre vonatkozó modellt próbál értelmezni, ajánlott nyers magyarázatokat használni, mivel az egyes funkciók fontossága az eredeti adatkészlet egyik oszlopának felel meg. Az egyik forgatókönyv, ahol a megtervezett magyarázatok hasznosak lehetnek, ha megvizsgáljuk egy kategorikus jellemző egyes kategóriáinak hatását. Ha egy one-hot kódolást alkalmaznak egy kategorikus jellemzőre, akkor az eredményül kapott megtervezett magyarázatok kategóriánként eltérő fontosságértéket fognak tartalmazni, egy-egy one-hot-mérnöki jellemző esetén. Ez hasznos lehet, ha leszűkíti, hogy az adatkészlet melyik része a leg informatívabb a modell számára.

> [!NOTE]
> A kitervezett és nyers magyarázatok számítása egymást követő módon történt. Először a modell és a featurizálási folyamat alapján hozunk létre egy megtervezett magyarázatot. Ezután a nyers magyarázat a megtervezett magyarázat alapján jön létre, és összefűszi az ugyanattól a nyers jellemzőtől származó tervezett jellemzők fontosságát.

### <a name="create-edit-and-view-dataset-cohorts"></a>Adatkészlet-kohorszok létrehozása, szerkesztése és megtekintése

A felső menüszalag a modell és az adatok általános statisztikáit jeleníti meg. Az adatokat adatcsoport-kohorszokra vagy alcsoportokra szeletelheti, így megvizsgálhatja vagy összehasonlíthatja a modell teljesítményét és magyarázatát ezekben a meghatározott alcsoportokban. Ha összehasonlítja az adatkészletek statisztikáit és magyarázatát az egyes alcsoportokban, akkor érzékelheti, hogy miért történnek lehetséges hibák az egyik csoportban a másikkal szemben.

[![Adatkészletek kohorszok létrehozása, szerkesztése és megtekintése](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>A modell teljes viselkedésének magyarázata (globális magyarázat) 

A magyarázat irányítópultjának első három lapja átfogó elemzést nyújt a betanított modellről, valamint annak előrejelzéseit és magyarázatait.

#### <a name="model-performance"></a>A modell teljesítménye
A modell teljesítményének kiértékeléséhez vizsgálja meg az előrejelzési értékek eloszlását és a modell teljesítménymetrikák értékeit. Tovább vizsgálhatja a modellt, ha összehasonlítja annak teljesítményét az adatkészlet különböző kohorszai vagy alcsoportjai esetében. A különböző dimenziók közötti átvágáshoz válassza az y-value és az x-value mellett lévő szűrőket. Megtekintheti az olyan metrikákat, mint a pontosság, a pontosság, a felidézés, a téves pozitív arány (FPR) és a téves negatív arány (FNR).

[![Modellteljesítmény lap a magyarázatvizualizációban](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Dataset Explorer
Az adatok különböző dimenziók mentén való szeletelése érdekében az X, az Y és a szín tengely mentén különböző szűrők kiválasztásával tárja fel az adatkészlet statisztikáit. Hozzon létre fent adatkészlet-kohorszokat az adatkészletek statisztikáinak elemzéséhez olyan szűrőkkel, mint az előrejelzett eredmény, az adatkészlet jellemzői és a hibacsoportok. A diagram jobb felső sarkában lévő fogaskerék ikonnal módosíthatja a gráftípusokat.

[![A magyarázatvizualizáció Dataset Explorer lapja](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>A funkció fontosságának összesítése
Ismerje meg a legfontosabb jellemzőket, amelyek hatással vannak az általános modell-előrejelzésekre (más néven globális magyarázat). A csúszkával a jellemző fontosságértékek csökkenő sorrendbe állíthatók. Jelöljön ki legfeljebb három kohorsztot, hogy a fontosságukat egymás mellett lássa. Kattintson a diagram bármelyik funkciósávra, hogy lássa, hogy a kiválasztott jellemzőértékek hogyan befolyásolják a modell előrejelzését az alábbi függőségi diagramon.

[![A funkció fontosságának összesítése lap a magyarázatok vizualizációban](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Az egyéni előrejelzések magyarázata (helyi magyarázat) 

A magyarázat lap negyedik lapján részletesen is megtekintheti az egyes adatpontokat és azok egyes funkciók fontosságát. Az egyes adatpontok fontosságdiagramját betöltheti úgy, hogy a fő pontdiagram bármelyik adatpontjára kattint, vagy kiválaszt egy adott adatpontot a jobb oldalon található panelvarázslóban.

|Telek|Description|
|----|-----------|
|Egyéni funkciók fontossága|Egy adott előrejelzés legfontosabb jellemzőit jeleníti meg. Segít az alapul szolgáló modell helyi viselkedésének szemléltetésére egy adott adatponton.|
|What-If elemzése|Lehetővé teszi a kiválasztott valós adatpont jellemzőértékének módosításait, és egy feltételezett adatpont új jellemzőértékekkel való generálása révén megfigyelheti az előrejelzési érték változásait.|
|Egyéni feltételes elvárás (ICE)|Lehetővé teszi a funkcióértékek minimális értékről maximális értékre való változását. Bemutatja, hogyan változik az adatpont előrejelzése, amikor egy jellemző megváltozik.|

[![Az egyes funkciók fontossága és a Magyarázat irányítópult What-if (Lehetőség) lapja](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Ezek számos közelítésen alapuló magyarázatok, és nem az előrejelzések "oka". Az okozati következtetés szigorú matematikai robusztussága nélkül nem javasoljuk a felhasználóknak, hogy valós döntéseket hozzanak az eszköz What-If alapján. Ez az eszköz elsősorban a modell és a hibakeresés megértésére használható.

### <a name="visualization-in-azure-machine-learning-studio"></a>Vizualizáció Azure Machine Learning stúdió

Ha befejezte [](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) a távoli értelmezhetőségi lépéseket (a létrehozott magyarázatok feltöltése a Azure Machine Learning Futtatás előzményeibe), a magyarázatok irányítópultján megtekintheti a vizualizációkat a [Azure Machine Learning stúdió.](https://ml.azure.com) Ez az irányítópult az irányítópult-widget egyszerűbb verziója, amely a Jupyter notebookban jön létre. What-If adatpontok létrehozása és az ICE-ábrázolások le vannak tiltva, mivel a Azure Machine Learning stúdió nem létezik olyan aktív számítás, amely képes lenne valós idejű számításokat végezni.

Ha az adatkészlet, a globális és a helyi magyarázatok elérhetők, az adatok az összes lapot kitöltik. Ha csak egy globális magyarázat érhető el, az Egyéni funkció fontossága lap le lesz tiltva.

Kövesse az alábbi útvonalak valamelyikét a magyarázatok irányítópultjának eléréséhez a Azure Machine Learning stúdió:

* **Kísérletek** panel (előzetes verzió)
  1. A **bal oldali** panelen válassza a Kísérletek lehetőséget a futtatott kísérletek listájának Azure Machine Learning.
  1. Válasszon ki egy adott kísérletet a kísérlet összes futtatásának megtekintéséhez.
  1. Válasszon ki egy futtatás, majd a **Magyarázatok** lapot a magyarázatvizualizációs irányítópulton.

   [![Vizualizációs irányítópult az AzureML Studióban a funkciók összesített fontosságával a kísérletekben](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* **Modellek** panel
  1. Ha regisztrálta az eredeti modellt a Deploy [models with Azure Machine Learning](./how-to-deploy-and-where.md)(Modellek üzembe helyezése a Azure Machine Learning használatával) dokumentum lépéseit követve, a bal oldali panelen a **Models** (Modellek) lehetőséget választva megtekintheti.
  1. Válasszon ki egy modellt, majd a **Magyarázatok** lapot a magyarázatok irányítópultjának megtekintéséhez.

## <a name="interpretability-at-inference-time"></a>Értelmezhetőség dedokenciaidőben

Üzembe helyezheti a magyarázót az eredeti modellel együtt, és a következtetéskor használhatja arra, hogy minden új adatponthoz meg tudja adni az egyes funkciók fontosságértékét (helyi magyarázat). Kisebb súlyozású pontozási magyarázókat is kínálunk, hogy a dedukenciaidőben javítsuk az értelmezhetőségi teljesítményt, ami jelenleg csak az SDK-ban Azure Machine Learning támogatott. A kisebb súlyozású pontozó magyarázó üzembe helyezésének folyamata hasonló a modell üzembe helyezéséhez, és a következő lépéseket tartalmazza:

1. Magyarázatobjektum létrehozása. Használhatja például a `TabularExplainer` következőt:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Pontozó magyarázó létrehozása a magyarázat objektummal.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. A pontozás magyarázó modelljét használó rendszerkép konfigurálása és regisztrálása.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Választható lépésként lekérheti a pontozási magyarázó adatokat a felhőből, és tesztelheti a magyarázatokat.

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

1. Telepítse a rendszerképet egy számítási célon az alábbi lépésekkel:

   1. Ha szükséges, regisztrálja az eredeti előrejelzési modellt a Modellek üzembe helyezése a következővel: [Azure Machine Learning.](./how-to-deploy-and-where.md)

   1. Pontozófájl létrehozása.

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

         Ez a konfiguráció a modell követelményeitől függ. Az alábbi példa egy olyan konfigurációt határoz meg, amely egy processzormagot és egy GB memóriát használ.

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

   1. Hozzon létre egy egyéni dockerfile-t a g++ telepítéssel.

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

1. Az üzembe helyezés tesztelése.

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

1. A tisztítás.

   Az üzembe helyezett webszolgáltatás törléséhez használja a `service.delete()` parancsot.

## <a name="troubleshooting"></a>Hibaelhárítás

* **Ritka adatok nem támogatottak:** A modellmagaló irányítópult sok funkcióval jelentősen leáll/lelassul, ezért jelenleg nem támogatjuk a ritka adatformátumot. Emellett általános memória-problémák merülnek fel a nagy méretű adatkészletekkel és sok jellemzővel kapcsolatban. 

* A modell magyarázatai által nem támogatott előrejelzési **modellek:** Az értelmezhetőség, a legjobb modell magyarázata nem érhető el az AutoML-előrejelzési kísérletekhez, amelyek a következő algoritmusokat javasolják a legjobb modellként: TCNForecaster, AutoArima, Following, ExponentialSmoemeting, Average, Naive, Seasonal Naive és Seasonal Naive. Az AutoML-előrejelzés olyan regressziós modellekkel rendelkezik, amelyek támogatják a magyarázatokat. A magyarázatok irányítópultján azonban az "Egyéni funkció fontossága" lap csak az adat-folyamatok összetettsége miatt nem támogatott az előrejelzéshez.

* **Az** adatindex helyi magyarázata: A magyarázat-irányítópult nem támogatja a helyi fontosságértékek és az eredeti ellenőrzési adatkészlet sorazonosítóinak a 5000 adatpontnál nagyobb adatpontokkal való összeszámítását, mivel az irányítópult véletlenszerűen lefelé mintavétele az adatokat. Az irányítópult azonban nyers adat adatkészlet-funkciók értékeit jeleníti meg az irányítópultnak az Egyéni funkció fontossága lapon átadott egyes adatpontokhoz. A felhasználók a nyers adatkészlet jellemzőértékeivel leképezhetik a helyi fontosságokat az eredeti adatkészletre. Ha az ellenőrzési adatkészlet mérete kisebb, mint 5000 minta, az AzureML Studio szolgáltatása megfelel az ellenőrzési adatkészletben lévő `index` indexnek.

* A **What-if/ICE-ábrázolás** nem támogatott a Studióban: az What-If és az egyéni feltételes elvárás (ICE) ábrázolásai nem támogatottak az Azure Machine Learning stúdió Explanations (Magyarázatok) lapon, mivel a feltöltött magyarázathoz aktív számításra van szükség az előrejelzések újraszámítása és a zavarba került jellemzők valószínűségének újraszámítása érdekében. Ez jelenleg a Jupyter-notebookok esetében támogatott, ha az SDK-t használó widgetként fut.


## <a name="next-steps"></a>Következő lépések

[További információ a modell értelmezhetőségével kapcsolatban](how-to-machine-learning-interpretability.md)

[Tekintse meg az Azure Machine Learning mintákat tartalmazó jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
