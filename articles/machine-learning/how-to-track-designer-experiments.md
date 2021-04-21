---
title: Naplómetrikák a tervezőben
titleSuffix: Azure Machine Learning
description: Az Azure ML tervezőkísérleteinek figyelése. Engedélyezze a naplózást a Python-szkript végrehajtása modullal, és tekintse meg a naplózott eredményeket a studióban.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: 13a3b86514428b0219aaf671260c07b4e197d2de
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817300"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Naplózás engedélyezése Azure Machine Learning tervezői folyamatokban


Ebből a cikkből megtudhatja, hogyan adhat naplózási kódot a tervezői folyamatokhoz. Azt is megtudhatja, hogyan lehet megtekinteni ezeket a naplókat a Azure Machine Learning stúdió webportálon.

A metrikák SDK-beli szerzői élmény használatával való naplózásával kapcsolatos további információkért lásd: Azure ML-kísérletek futtatásának és [metrikamonitorozása.](how-to-log-view-metrics.md)

## <a name="enable-logging-with-execute-python-script"></a>Naplózás engedélyezése Python-szkript végrehajtásával

A [Python-szkript végrehajtása modullal](./algorithm-module-reference/execute-python-script.md) engedélyezheti a naplózást a tervezői folyamatokban. Bár ezzel a munkafolyamattal bármilyen értéket naplózhat, különösen hasznos, ha a Modell kiértékelése modulból naplóz metrikákat a modell teljesítményének nyomon követéséhez a futtatás során. 

Az alábbi példa bemutatja, hogyan naplózhatja két betanított modell átlagos négyzetes hibaértékét az Evaluate Model (Modell kiértékelése) és az Execute Python Script (Python-szkript végrehajtása) modulokkal.

1. Egy __Python-szkript végrehajtása modul csatlakoztatása__ a Modell __kiértékelése modul kimenetéhez.__

    ![A Python-szkript végrehajtása modul csatlakoztatása a Modell kiértékelése modulhoz](./media/how-to-log-view-metrics/designer-logging-pipeline.png)

1. Illessze be az alábbi kódot a __Python-szkript__ végrehajtása kódszerkesztőbe, hogy naplózhatja a betanított modell átlagos abszolút hibaértékét. Hasonló mintát használhat bármely más érték naplózásához a tervezőben:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.
        parent_run = Run.get_context().parent
        
        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        parent_run.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])
        # Log right output port result of Evaluate Model. The following line should be deleted if you only connect one Score Module to the` left port of Evaluate Model module.
        parent_run.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])

        return dataframe1,
    ```
    
Ez a kód az Azure Machine Learning Python SDK-t használja az értékek naplózásához. A Run.get_context() segítségével lekérte az aktuális futtatás környezetét. Ezután ebben a környezetben naplózza az értékeket a run.parent.log() metódussal. A használatával `parent` naplóz értékeket a szülő folyamatfuttassa a modul futtatása helyett.

A Python SDK értékek naplózásához való használatával kapcsolatos további információkért lásd: Naplózás engedélyezése [az Azure ML betanításfuttaiban.](how-to-log-view-metrics.md)

## <a name="view-logs"></a>Naplók megtekintése

Miután a folyamat futása befejeződött,  a kísérletek Mean_Absolute_Error használhatja.

1. Lépjen a **Kísérletek szakaszra.**
1. Válassza ki a kísérletet.
1. Válassza ki a megtekinteni kívánt kísérlet futtatását.
1. Válassza a **Metrikák** lehetőséget.

    ![Futtatás metrika megtekintése a Studióban](./media/how-to-log-view-metrics/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a naplókat a tervezőben. A következő lépésekért tekintse meg a következő kapcsolódó cikkeket:


* További információ a tervezői folyamatok hibaelhárításáról: Gépi tanulási [folyamatok & hibaelhárítása.](how-to-debug-pipelines.md#azure-machine-learning-designer)
* Ismerje meg, hogyan naplózható metrikák naplózása a Python SDK használatával az SDK szerzői élményében: Naplózás engedélyezése [Az Azure ML betanításai futtatásaiban.](how-to-log-view-metrics.md)
* Megtudhatja, hogyan használhatja [a Python-szkript végrehajtását](./algorithm-module-reference/execute-python-script.md) a tervezőben.
