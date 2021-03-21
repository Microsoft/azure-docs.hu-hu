---
title: Naplózási mérőszámok a Designerben
titleSuffix: Azure Machine Learning
description: Az Azure ML Designer-kísérletek monitorozása. Engedélyezze a naplózást a Python parancsfájl-futtatási moduljának használatával, és tekintse meg a naplózott eredményeket a Studióban.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 01/11/2021
ms.topic: conceptual
ms.custom: designer
ms.openlocfilehash: b940f5c9bd14bcec404827daaef666da802d969b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98065252"
---
# <a name="enable-logging-in-azure-machine-learning-designer-pipelines"></a>Azure Machine Learning Designer-folyamatok naplózásának engedélyezése


Ebből a cikkből megtudhatja, hogyan adhat hozzá naplózási kódot a tervezői folyamatokhoz. Azt is megtudhatja, hogyan tekintheti meg ezeket a naplókat a Azure Machine Learning Studio webes portál használatával.

További információ a metrikák az SDK-létrehozási funkcióval való naplózásáról: az [Azure ml-kísérletek futtatásának és metrikáinak monitorozása](how-to-track-experiments.md).

## <a name="enable-logging-with-execute-python-script"></a>A naplózás engedélyezése a Python-szkript végrehajtásával

A [Python-szkript végrehajtása](./algorithm-module-reference/execute-python-script.md) modul használatával engedélyezheti a naplózást a Designer-folyamatokban. Bár bármilyen értéket naplózhat ezzel a munkafolyamattal, különösen hasznos a mérőszámok naplózása a __modell kiértékelése__ modulból a modell teljesítményének nyomon követéséhez a futtatások között.

Az alábbi példa bemutatja, hogyan naplózhatja a két betanított modell közepes négyzetes hibáját a modell kiértékelése és a Python parancsfájl-modulok végrehajtása segítségével.

1. A __modell kiértékelése__ modul kimenetéhez csatlakoztasson egy __Python parancsfájl__ -modult.

    ![A modell modul kiértékeléséhez kapcsolja össze a Python parancsfájl-modult](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Illessze be a következő kódot a __Python parancsfájl__ -szerkesztő végrehajtása mezőbe, hogy naplózza a betanított modell átlagos abszolút hibáját. A tervező bármely más értékének naplózásához hasonló mintát használhat:

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
    
Ez a kód a Azure Machine Learning Python SDK-t használja az értékek naplózásához. Run.get_context () használatával kéri le az aktuális Futtatás környezetét. Ezután a Run. Parent. log () metódussal naplózza az értékeket a környezetbe. A használatával a rendszer az `parent` értékeket a fölérendelt folyamat futtatására használja a modul futtatása helyett.

További információ a Python SDK-nak az értékek naplózására való használatáról: [a naplózás engedélyezése az Azure ml betanítási futtatásával](how-to-track-experiments.md).

## <a name="view-logs"></a>Naplók megtekintése

A folyamat futásának befejeződése után megtekintheti a *Mean_Absolute_Error* a kísérletek lapon.

1. Navigáljon a **kísérletek** szakaszhoz.
1. Válassza ki a kísérletet.
1. Válassza ki a megtekinteni kívánt kísérlet futtatását.
1. Válassza a **Metrikák** lehetőséget.

    ![A Studio futtatási metrikáinak megtekintése](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a naplókat a tervezőben. A következő lépésekért tekintse meg az alábbi kapcsolódó cikkeket:


* Ismerje meg, hogyan lehet a tervezői folyamatokat elhárítani: [hibakeresés & ml-folyamatok hibaelhárítása](how-to-debug-pipelines.md#azure-machine-learning-designer).
* Megtudhatja, hogyan használhatja a Python SDK-t a mérőszámok naplózására az SDK-létrehozási élményben: a [naplózás engedélyezése az Azure ml betanítási futtatásával](how-to-track-experiments.md).
* Megtudhatja, hogyan használhatja a [Python-szkriptek futtatását](./algorithm-module-reference/execute-python-script.md) a tervezőben.
