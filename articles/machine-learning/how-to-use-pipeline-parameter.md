---
title: A tervezőben lévő folyamat paramétereinek használata sokoldalú folyamatok létrehozásához
titleSuffix: Azure Machine Learning
description: Folyamat paramétereinek használata a Azure Machine Learning Designerben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/09/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 30ae737a170c337fe6be51521aeb358cdcebd44b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107336"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>A tervezőben lévő folyamat paramétereinek használata sokoldalú folyamatok létrehozásához

A folyamat paramétereinek használatával rugalmas folyamatokat hozhat létre a tervezőben. A folyamat paramétereinek használatával dinamikusan állíthatja be az értékeket futásidőben a folyamat logikája és az eszközök újbóli felhasználása érdekében.

A folyamat paramétereinek használata különösen hasznos a folyamat futtatása, a [modellek átképzése](how-to-retrain-designer.md)vagy a [kötegelt előrejelzések végrehajtása](how-to-run-batch-predictions-designer.md)során.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Folyamat paramétereinek létrehozása
> * Folyamat paramétereinek törlése és kezelése
> * Folyamatban van a folyamat futtatása a folyamat paramétereinek beállításakor

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

* A Designer bevezetéséhez fejezze be a [tervezői oktatóanyagot](tutorial-designer-automobile-price-train-score.md). 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>Folyamat létrehozása paraméter

A következő három módon hozható létre egy folyamat paraméter a tervezőben:
- Hozzon létre egy folyamat paramétert a beállítások panelen, és kösse azt egy modulhoz.
- Modul-paraméter előléptetése egy folyamat paraméteréhez.
- Adatkészlet előléptetése egy folyamat paraméteréhez

> [!NOTE]
> A feldolgozási paraméterek csak az alapszintű adattípusokat támogatják, például:, `int` `float` és `string` .

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>1. lehetőség: a beállítások panelen hozzon létre egy folyamat paramétert

Ebben a szakaszban egy folyamat paramétert hoz létre a beállítások panelen.

Ebben a példában egy folyamat paramétert hoz létre, amely meghatározza, hogy a folyamat hogyan töltse ki a hiányzó adategységeket a **tiszta hiányzó** adatmodul használatával.

1. A folyamat-tervezet neve mellett kattintson a **fogaskerék ikonra** a **Beállítások** panel megnyitásához.

1. A **folyamat paramétereinek** szakaszban válassza a **+** ikont.

1.  Adja meg a paraméter nevét és az alapértelmezett értéket. 

    Adja meg például a `replace-missing-value` paraméter neve és az `0` alapértelmezett érték értéket.

   ![Képernyőkép, amely bemutatja, hogyan lehet létrehozni egy folyamat paramétert](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


A folyamat paramétereinek létrehozása után csatolni kell azt a dinamikusan beállítani kívánt [modul-paraméterhez](#attach-module-parameter-to-pipeline-parameter) .

### <a name="option-2-promote-a-module-parameter"></a>2. lehetőség: modul-paraméter előléptetése

A modul értékének legegyszerűbb módja a modul paramétereinek előléptetése. Az alábbi lépésekkel előléptetheti a modul paramétereit egy folyamat paraméteréhez:

1. Válassza ki azt a modult, amelyhez csatlakoztatni kívánja a folyamat paraméterét.
1. A modul részletes paneljén válassza a megadni kívánt paramétert.
1. Válassza ki a megjelenő ellipsziseket (**..**.).
1. Válassza **a Hozzáadás a folyamathoz paramétert**.

    ![Képernyőkép a modul paraméterének előléptetéséről a folyamat Parameter1](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. Adja meg a paraméter nevét és az alapértelmezett értéket.
1. Válassza a **Mentés** lehetőséget

Mostantól bármikor megadhat új értékeket ehhez a paraméterhez a folyamat elküldésekor.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>3. lehetőség: adatkészlet előléptetése egy folyamat paraméteréhez

Ha változó adatkészletekkel szeretné elküldeni a folyamatot, elő kell mozdítania az adatkészletet egy folyamat paraméterének:

1. Válassza ki azt az adatkészletet, amelybe be szeretné kapcsolni a folyamat paraméterét.

1. Az adatkészlet részletezés paneljén kattintson a **beállítás a pipeline paraméterként** elemre.

   ![Az adatkészletnek a pipeline paraméterként való beállítását bemutató képernyőkép](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

Mostantól a folyamat következő futtatásakor megadhat egy másik adatkészletet a folyamat paraméter használatával.

## <a name="attach-and-detach-module-parameter-to-pipeline-parameter"></a>Modul paraméterének csatolása és leválasztása a folyamat paraméteréhez 

Ebből a szakaszból megtudhatja, hogyan csatolhatja és leválaszthatja a modul paraméterét a folyamat paraméteréhez.

### <a name="attach-module-parameter-to-pipeline-parameter"></a>Modul paraméterének csatolása a folyamat paraméteréhez

A duplikált modulok azonos modul-paramétereit ugyanahhoz a folyamat-paraméterhez csatolhatja, ha a folyamat futtatásakor egyszerre szeretné módosítani az értéket.

Az alábbi példa duplikált, **tiszta, hiányzó** adatmodult tartalmaz. Minden egyes **tiszta hiányzó** adatmodulhoz csatolja a **helyettesítési értéket** a folyamat paraméteréhez a **replace-missing-Value**:

1. Válassza ki a **tiszta hiányzó** adatmodult.

1. A modul részleteket tartalmazó ablaktábláján, a vászontól jobbra állítsa a **tisztítási módot** "egyéni helyettesítő értékre".

1. A **helyettesítő érték** mező mouseover.

1. Válassza ki a megjelenő ellipsziseket (**..**.).

1. Válassza ki a folyamat paramétert `replace-missing-value` .

   ![A folyamat paramétereinek csatolását bemutató képernyőkép](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

Sikeresen csatolta a **helyettesítési érték** mezőt a folyamat paraméteréhez. 


### <a name="detach-module-parameter-to-pipeline-parameter"></a>Modul paraméterének leválasztása a folyamat paraméteréhez

Ha a **helyettesítési értéket** csatolja a folyamat paraméterhez, az nem hajtható végre.

A modul paraméterét leválaszthatja a folyamat paraméterre úgy, hogy a modul paraméter melletti három pontra (**..**.) kattint, és kiválasztja a **Leválasztás a folyamatból paramétert**.

 ![Képernyőkép, amely a folyamat paraméterének csatolása után nem alkalmazható műveleteket jelenít meg](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)

## <a name="update-and-delete-pipeline-parameters"></a>Folyamat paramétereinek frissítése és törlése

Ebből a szakaszból megtudhatja, hogyan frissítheti és törölheti a folyamat paramétereit.

### <a name="update-pipeline-parameters"></a>Folyamat paramétereinek frissítése

A következő lépésekkel frissítheti a modul-folyamat paraméterét:

1. A vászon tetején válassza a fogaskerék ikont.
1. A **folyamat paramétereinek** szakaszban megtekintheti és frissítheti az összes folyamat paraméterének nevét és alapértelmezett értékét.

### <a name="delete-a-dataset-pipeline-parameter"></a>Adatkészlet-folyamat paraméterének törlése

A következő lépésekkel törölheti az adatkészlet-folyamat paramétereit:

1. Válassza ki az adatkészlet modulját.
1. Törölje a beállítást a **pipeline paraméterként**.


### <a name="delete-module-pipeline-parameters"></a>Modul-folyamat paramétereinek törlése

A következő lépésekkel törölheti a modul-folyamat paraméterét:

1. A vászon tetején válassza a fogaskerék ikont.

1. Válassza a folyamat paraméter melletti három pontot (**..**.).

    Ebben a nézetben látható, hogy mely modulokhoz van csatolva a folyamat paraméter.

    ![A modulra alkalmazott aktuális folyamat paraméterét bemutató képernyőkép](media/how-to-use-pipeline-parameter/delete-pipeline-parameter2.png)

1. Válassza a **paraméter törlése** lehetőséget a folyamat paramétereinek törléséhez.

    > [!NOTE]
    > Egy folyamat paraméterének törlésével az összes csatolt modul paraméterét le lehet választani, és a leválasztott modul paramétereinek értéke megtartja az aktuális folyamat paraméterének értékét.     

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>Folyamat futtatásának indítása folyamat-paraméterekkel 

Ebből a szakaszból megtudhatja, hogyan küldhet át egy folyamatot a folyamat paramétereinek beállítása közben.

### <a name="resubmit-a-pipeline-run"></a>Folyamat futtatásának újraküldése

Miután elküldte a folyamatot a folyamat paramétereinek használatával, újraküldheti a különböző paraméterekkel rendelkező folyamatokat:

1. Ugrás a folyamat részletei lapra. A **folyamat futtatása – áttekintés** ablakban megtekintheti az aktuális folyamat paramétereit és értékeit.

1. Válassza az **Újraküldés** lehetőséget.
1. A **telepítési folyamat futtatása** lapon adja meg az új folyamat paramétereit. 

![Képernyőfelvétel az újraküldési folyamatról a folyamat paramétereinek megjelenítése](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>Közzétett folyamatok használata

Az adatcsatorna-paraméterek használatára is közzéteheti a folyamatokat. A **közzétett folyamat** olyan folyamat, amely egy számítási erőforrásra lett telepítve, amelyet az ügyfélalkalmazások Rest-végponton keresztül hívhatnak meg.

A közzétett végpontok különösen hasznosak az újraképzéshez és a kötegelt előrejelzési forgatókönyvekhez. További információ: [modellek újratanítása a Designerben](how-to-retrain-designer.md) vagy [a kötegelt előrejelzések futtatása a tervezőben](how-to-run-batch-predictions-designer.md).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre folyamat-paramétereket a tervezőben. Ezután tekintse meg, hogyan használhatja a folyamat paramétereit a [modellek újratanításához](how-to-retrain-designer.md) vagy a [kötegelt előrejelzések](how-to-run-batch-predictions-designer.md)végrehajtásához.

Azt is megtudhatja, hogyan [használhatja a folyamatokat programozott módon az SDK-val](how-to-deploy-pipelines.md).
