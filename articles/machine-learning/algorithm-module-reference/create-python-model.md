---
title: 'Python-modell létrehozása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre egyéni modellezési vagy adatfeldolgozási modult a Azure Machine Learning Python modell létrehozása moduljában.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/18/2020
ms.openlocfilehash: 03f80ebeb7b30d8c0fe14060335541d77b7523b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898625"
---
# <a name="create-python-model-module"></a>Python-modell modul létrehozása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ismerje meg, hogyan hozhat létre egy nem betanított modellt egy Python-szkriptből a Python-modell létrehozása modul használatával. A modellt a Azure Machine Learning Designer-környezetben található Python-csomagban szereplő bármelyik tanulón alapozhatja. 

A modell létrehozása után a [betanítási modell](train-model.md) segítségével betaníthatja a modellt egy adatkészleten, például a Azure Machine learning bármely más tanulója számára. A betanított modell átadható a [pontszám modellnek](score-model.md) az előrejelzések készítéséhez. Ezután mentheti a betanított modellt, és közzéteheti a pontozási munkafolyamatot webszolgáltatásként.

> [!WARNING]
> Jelenleg nem lehet csatlakozni a modulhoz a **modell hiperparaméterek beállítása** -moduljának finomhangolásához, illetve a [modell kiértékeléséhez](evaluate-model.md)egy Python-modell pontozásos eredményeinek átadásához. Ha be kell állítania a hiperparaméterek beállítása, vagy ki kell értékelnie a modellt, a [Python parancsfájl](execute-python-script.md) -futtatási moduljának használatával egyéni Python-szkriptet is írhat.


## <a name="configure-the-module"></a>A modul konfigurálása

A modul használatához a Python közbenső vagy szakértői ismerete szükséges. A modul támogatja a Azure Machine Learning-ban már telepített Python-csomagokban található bármely tanuló használatát. Tekintse meg az előre telepített Python-csomagok listáját a [Python-szkript végrehajtása](execute-python-script.md)című témakörben.

> [!NOTE]
> Kérjük, legyen nagyon körültekintő a parancsfájl írásakor, és ellenőrizze, hogy nincs-e szintaktikai hiba, például egy nem deklarált objektum vagy egy nem importált modul használata.

> [!NOTE]
> Emellett az előre telepített modulok listáját is külön kell fizetnie a [Python-szkript végrehajtásához](execute-python-script.md). Csak az előre telepített modulok importálása. Ne telepítsen további csomagokat, például a "pip install xgboost"-et ebben a parancsfájlban, ellenkező esetben a rendszer az adatfolyam-modulok modelljeinek olvasásakor hibát jelez.
  
Ez a cikk bemutatja, hogyan használható a **Python-modell létrehozása** egyszerű folyamattal. Itt látható a folyamat ábrája:

![Python-modell létrehozási diagramja](./media/module/create-python-model.png)

1. Válassza a **Python-modell létrehozása** lehetőséget, és szerkessze a szkriptet a modellezés vagy az adatkezelési folyamat megvalósításához. A modellt a Azure Machine Learning-környezetben található Python-csomagban szereplő bármelyik tanulón alapozhatja.

> [!NOTE]
> Kérjük, vegye figyelembe a parancsfájl mintakód megjegyzéseit, és győződjön meg arról, hogy a szkript szigorúan követi a követelményt, beleértve az osztály nevét, a metódusokat és a metódus-aláírást is. A megsértés a kivételeket fogja eredményezni. 

   A kétosztályos naiv Bayes-osztályozó következő mintakód a népszerű *sklearn* -csomagot használja:

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

2. Csatlakoztassa a **Python modell létrehozása** modult, amelyet az imént hozott létre a modell és a **pontszám modell** **betanításához** .

3. Ha ki kell értékelnie a modellt, vegyen fel egy [Python-parancsfájlt](execute-python-script.md) , és szerkessze a Python-szkriptet.

   A következő parancsfájl minta-értékelési kód:

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function MUST have two input arguments:
   #   Param<dataframe1>: a pandas.DataFrame
   #   Param<dataframe2>: a pandas.DataFrame
   def azureml_main(dataframe1 = None, dataframe2 = None):
    
       from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
       import pandas as pd
       import numpy as np
    
       scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
       ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
       ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
       probabilities = scores["probabilities"]
    
       accuracy, precision, recall, auc = \
       accuracy_score(ytrue, ypred),\
       precision_score(ytrue, ypred),\
       recall_score(ytrue, ypred),\
       roc_auc_score(ytrue, probabilities)
    
       metrics = pd.DataFrame();
       metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
       metrics["Value"] = [accuracy, precision, recall, auc]
    
       return metrics,

   ```

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 