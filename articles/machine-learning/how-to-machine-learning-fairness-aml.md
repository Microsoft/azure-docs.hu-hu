---
title: A Python (előzetes verzió) ML-modelljeinek felmérése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állapíthatja meg és csökkentheti a gépi tanulási modellek tisztaságát a Fairlearn és a Azure Machine Learning Python SDK használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: 322b036fee840db58ed610795155af6c9e1320cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366963"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Azure Machine Learning használata a Fairlearn nyílt forráskódú csomaggal, amellyel mérhető az ML-modellek tisztasága (előzetes verzió)

Ebben a útmutatóban megtudhatja, hogyan használhatja a [Fairlearn](https://fairlearn.github.io/) nyílt forráskódú Python-csomagot Azure Machine learning a következő feladatok elvégzéséhez:

* Mérje fel a modell előrejelzésének méltányos mértékét. A gépi tanulással kapcsolatos méltányosság megismeréséhez tekintse meg a [méltányosság a gépi tanulással című cikket](concept-fairness-ml.md).
* Töltse fel, sorolja fel és töltse le a Azure Machine Learning studióhoz tartozó méltányos felmérési elemzéseket.
* Tekintse meg a tisztesség értékelésének irányítópultját Azure Machine Learning Studióban, hogy együttműködjön a modell (ek) méltányos elemzésével.

>[!NOTE]
> A méltányosság felmérése nem pusztán technikai gyakorlat. **Ez a csomag segít felmérni a gépi tanulási modellek igazságos értékelését, de csak a modell végrehajtásához és a döntések meghozatalához lehet döntéseket hozni.**  Habár ez a csomag segíti a mennyiségi mérőszámok azonosítását a méltányosság kiértékeléséhez, a gépi tanulási modellek fejlesztőinek is minőségi elemzést kell végezniük a saját modelljeik korrekt kiértékeléséhez.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning méltányos SDK 

A Azure Machine Learning méltányos SDK, amely `azureml-contrib-fairness` a Azure Machine Learningon belül integrálja a nyílt forráskódú Python-csomagot, a [Fairlearn](http://fairlearn.github.io)-t. Ha többet szeretne megtudni a Fairlearn Azure Machine Learningon belüli integrálásáról, tekintse meg ezeket a [minta jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). A Fairlearn kapcsolatos további információkért tekintse meg a [példa útmutató](https://fairlearn.github.io/master/auto_examples/) és a [minta jegyzetfüzetek](https://github.com/fairlearn/fairlearn/tree/master/notebooks)című részt. 

A és a csomagok telepítéséhez használja az alábbi parancsokat `azureml-contrib-fairness` `fairlearn` :
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
A Fairlearn újabb verziói a következő példában is működnek.



## <a name="upload-fairness-insights-for-a-single-model"></a>Egyetlen modell méltányos bepillantást tölthet fel

Az alábbi példa bemutatja, hogyan használható a méltányos csomag. Azure Machine Learning és a Azure Machine Learning Studióban tekintjük meg a méltányos értékelés irányítópultját.

1. Egy minta modell betanítása Jupyter Notebookban. 

    Az adatkészlet esetében a jól ismert felnőtt népszámlálás adatkészletet használjuk, amelyet a OpenML kapunk le. Tegyük fel, hogy a címkével kapcsolatban probléma merült fel, amely azt jelzi, hogy egy adott személy visszafizette-e az előző kölcsönt. Betanítunk egy modellt, amely azt jelzi, hogy a korábban láthatatlan személyek fizetnek-e hitelt. Ezt a modellt felhasználhatja a hitelek meghozatalához.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Jelentkezzen be Azure Machine Learningba, és regisztrálja a modellt.
   
    A méltányosság irányítópultja integrálható a regisztrált vagy nem regisztrált modellekkel. Regisztrálja Azure Machine Learning modelljét a következő lépésekkel:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Kiszámítja a méltányosság mérőszámait.

    Irányítópult-szótár létrehozása a Fairlearn `metrics` csomagjának használatával. A `_create_group_metric_set` metódushoz hasonló argumentumok tartoznak az irányítópult-konstruktorhoz, azzal a különbséggel, hogy az érzékeny funkciók szótárként lesznek átadva (annak érdekében, hogy a nevek elérhetők legyenek). A metódus meghívásakor meg kell adni az előrejelzés típusát (ebben az esetben a bináris besorolást is).

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Töltse fel az előre számított méltányosság mérőszámait.
    
    Most importálja `azureml.contrib.fairness` a csomagot a feltöltés végrehajtásához:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Hozzon létre egy kísérletet, majd futtasson, majd töltse fel az irányítópultot:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. A Azure Machine Learning Studióban található tisztesség-irányítópult megtekintése

    Ha elvégezte az előző lépéseket (a kinyert tisztasági bepillantást Azure Machine Learning), megtekintheti a tisztességes irányítópultot [Azure Machine learning Studióban](https://ml.azure.com). Ez az irányítópult a Fairlearn-ben megadott vizualizációs irányítópult, amely lehetővé teszi, hogy elemezze a különbségeket a bizalmas szolgáltatás alcsoportjai között (pl.: férfi és nő).
    Kövesse az alábbi elérési utakat a vizualizációs irányítópult eléréséhez Azure Machine Learning Studióban:

    * **Kísérletek panel (előzetes verzió)**
    1. Válassza a **kísérletek** lehetőséget a bal oldali ablaktáblán a Azure Machine learning futtatott kísérletek listájának megjelenítéséhez.
    1. Válasszon ki egy adott kísérletet a kísérlet összes futtatásának megtekintéséhez.
    1. Válasszon egy futtatást, majd a **tisztesség** fület a magyarázat vizualizáció irányítópultra.
    1. Miután kikerül a **tisztesség** lapon, kattintson a jobb oldali menüben a **méltányosság azonosítóra** .
    1. Konfigurálja az irányítópultot úgy, hogy kijelöli a méltányosság értékelését szolgáló oldalon a bizalmas attribútumot, a teljesítmény metrikáját és a méltányos minősítési mérőszámot.
    1. A diagram típusának átváltása az egyikről a másikra a **foglalási** ártalmak és **a szolgáltatás minőségi** károsodásának megfigyelése érdekében.



    [![A méltányosság irányítópult-kiosztása](./media/how-to-machine-learning-fairness-aml/dashboard-1.png)](./media/how-to-machine-learning-fairness-aml/dashboard-1.png#lightbox)
    
    [![A tisztesség irányítópult minőségi szolgáltatásának minősége](./media/how-to-machine-learning-fairness-aml/dashboard-2.png)](./media/how-to-machine-learning-fairness-aml/dashboard-2.png#lightbox)
    * **Modellek panel**
    1. Ha az előző lépések követésével regisztrálta az eredeti modellt, akkor a bal oldali ablaktáblán kiválaszthatja a **modelleket** .
    1. Válasszon ki egy modellt, majd a **tisztesség** lapon tekintse meg a magyarázó vizualizáció irányítópultját.

    Ha többet szeretne megtudni a vizualizációs irányítópultról és arról, hogy mit tartalmaz, tekintse meg a Fairlearn [felhasználói útmutatóját](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard).

## <a name="upload-fairness-insights-for-multiple-models"></a>Több modell méltányos bepillantást tölthet fel

Ha több modellt szeretne összehasonlítani, és látni szeretné, hogy a tisztesség értékelésük Miben különbözik, akkor több modellt is átadhat a vizualizációs irányítópulthoz, és összehasonlíthatja a teljesítmény-méltányos kompromisszumokat.

1. A modellek betanítása:
    
    Most létrehozunk egy második besorolást, amely egy támogatási vektoros gépi kalkulátoron alapul, és feltölt egy méltányos irányítópult-szótárt a Fairlearn `metrics` csomagjának használatával. Feltételezzük, hogy a korábban betanított modell továbbra is elérhető.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. A modellek regisztrálása

    Ezután regisztráljon mindkét modellt Azure Machine Learningon belül. A kényelmes használat érdekében az eredményeket egy olyan szótárban tárolja, amely a `id` regisztrált modell (formátumú karakterlánc) leképezi a prediktív azonosítóját `name:version` :

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. A Fairlearn irányítópult helyi betöltése

    A méltányos elemzések Azure Machine Learningba való feltöltése előtt megvizsgálhatja ezeket az előrejelzéseket egy helyileg meghívott Fairlearn-irányítópulton. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Kiszámítja a méltányosság mérőszámait.

    Irányítópult-szótár létrehozása a Fairlearn `metrics` csomagjának használatával.

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Töltse fel az előre számított méltányosság mérőszámait.
    
    Most importálja `azureml.contrib.fairness` a csomagot a feltöltés végrehajtásához:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Hozzon létre egy kísérletet, majd futtasson, majd töltse fel az irányítópultot:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Az előző szakaszhoz hasonlóan a Azure Machine Learning Studióban a fentiekben ismertetett elérési utak  egyikét is követheti a vizualizációs irányítópult eléréséhez, és a két modellt a méltányosság és a teljesítmény szempontjából hasonlíthatja össze.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Nem enyhített és enyhített tisztasági adatok feltöltése

Használhatja a Fairlearn [enyhítő algoritmusait](https://fairlearn.github.io/master/user_guide/mitigation.html), összehasonlíthatja a létrehozott, kinyert modell (eke) t az eredeti, nem enyhített modellel, és megtekintheti a teljesítmény/méltányosság elleni kompromisszumokat az összehasonlított modellek között.

Ha meg szeretné tekinteni egy példát, amely bemutatja a [Grid Search](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) -enyhítő algoritmus használatát (amely a különböző tisztességgel és teljesítményű adatforgalommal rendelkező, csökkenthető modellek gyűjteményét hozza létre), tekintse meg ezt a [minta jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Több modell kimutatása egyetlen futtatásban – lehetővé teszi a modellek összehasonlítását a méltányosság és a teljesítmény tekintetében. A modell összehasonlító diagramján megjelenő modellekre kattintva megtekintheti az adott modell részletes információit.


[![Modell összehasonlító Fairlearn irányítópultja](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Következő lépések

[További információ a modell tisztaságáról](concept-fairness-ml.md)

[Tekintse meg Azure Machine Learning a méltányos minta notebookok](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
