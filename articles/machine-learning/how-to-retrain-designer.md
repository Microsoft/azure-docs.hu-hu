---
title: Modellek újratanítása a tervezőben a folyamat paramétereinek használatával
titleSuffix: Azure Machine Learning
description: Modellek újratanítása a közzétett folyamatokkal és folyamat-paraméterekkel Azure Machine Learning Designerben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 03/06/2021
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: ed5f59fc1d751f92a7c016aa3586497659f778fd
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962951"
---
# <a name="use-pipeline-parameters-to-retrain-models-in-the-designer"></a>Modellek újratanítása a tervezőben a folyamat paramétereinek használatával


Ebben a útmutatóban megismerheti, hogyan lehet a Azure Machine Learning Designer használatával újratanítani a gépi tanulási modelleket a folyamat paramétereinek használatával. A közzétett folyamatok segítségével automatizálhatja a munkafolyamatot, és paramétereket állíthat be a modell új adatokra való betanításához. A folyamat paramétereinek segítségével újra felhasználhatja a meglévő folyamatokat a különböző feladatokhoz.  

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Gépi tanulási modell betanítása.
> * Hozzon létre egy folyamat paramétert.
> * A betanítási folyamat közzététele.
> * A modell újratanítása új paraméterekkel.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület
* A How-to sorozat 1. részének befejezése, [az adatátalakítás a tervezőben](how-to-designer-transform-data.md)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

A cikk azt is feltételezi, hogy a tervezőben már van néhány ismerete a folyamatok létrehozásához. Az interaktív bevezetéshez végezze el az [oktatóanyagot](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Mintavételezési folyamat

A cikkben használt folyamat a tervezői kezdőlapon a minta-adatcsatorna [bevételeinek előrejelzése](samples-designer.md#classification) módosított változata. A folyamat a minta adatkészlet helyett az [adat importálása](algorithm-module-reference/import-data.md) modult használja, hogy megmutassa, hogyan taníthatja be a modelleket a saját adatai alapján.

![Képernyőfelvétel a módosított mintavételi folyamatról az Adatimportálási modul kiemelésével](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Folyamat paraméterének létrehozása

A folyamat paramétereinek használatával sokoldalú folyamatokat hozhat létre, amelyek később is elindíthatók a különböző paraméterek értékeivel. A gyakori forgatókönyvek az adatkészletek frissítése vagy valamilyen Hyper-paraméterek az átképzéshez. Folyamat paramétereinek létrehozása a változók dinamikus beállításához futásidőben. 

A folyamat paraméterei hozzáadhatók az adatforráshoz vagy a modul paramétereinek egy folyamathoz. A folyamat újraküldését követően megadhatja a paraméterek értékeit.

Ebben a példában egy rögzített értékről egy paraméterre módosítja a betanítási adatok elérési útját, hogy a modell a különböző adatokon is áttanítható legyen. A használati esetnek megfelelően további modul-paramétereket is hozzáadhat a folyamat paramétereinek.

1. Válassza az **adatimportálás** modult.

    > [!NOTE]
    > Ez a példa az Adatimportálási modul használatával fér hozzá a regisztrált adattárban található adategységekhez. Azonban a hasonló lépéseket követve alternatív adathozzáférési mintákat is használhat.

1. A modul részletei ablaktáblán, a vászontól jobbra válassza ki az adatforrást.

1. Adja meg az adatai elérési útját. A **tallózási útvonalat** is kiválaszthatja a fájl faszerkezetének tallózásához. 

1. Az **elérési út** mezőre, majd a megjelenő **path (útvonal** ) mező fölé kattintva válassza ki a megjelenő ellipsziseket.

1. Válassza **a Hozzáadás a folyamathoz paramétert**.

1. Adja meg a paraméter nevét és az alapértelmezett értéket.

   ![Képernyőkép, amely bemutatja, hogyan lehet létrehozni egy folyamat paramétert](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Kattintson a **Mentés** gombra.

   > [!NOTE]
   > Leválaszthatja a modul paraméterét a modul részletes ablaktábláján található folyamat paraméterből is, hasonlóan a folyamat paramétereinek hozzáadásához.
   >
   > A folyamat paramétereinek vizsgálatához és szerkesztéséhez válassza a folyamat piszkozatának címe melletti **Beállítások** fogaskerék ikont. 
   >    - A leválasztás után törölheti a folyamat paramétert a **setings** ablaktáblán.
   >    - A **Beállítások** ablaktáblán hozzáadhat egy folyamat paramétert is, majd alkalmazhatja azt valamilyen modul-paraméterre.

1. A folyamat futtatásának elküldése.

## <a name="publish-a-training-pipeline"></a>Betanítási folyamat közzététele

Egy folyamat közzététele egy folyamat végpontján, hogy a jövőben könnyedén újra fel lehessen használni a folyamatokat. A folyamat végpontja létrehoz egy REST-végpontot a folyamat későbbi meghívásához. Ebben a példában a folyamat végpontja lehetővé teszi, hogy újra felhasználja a folyamatot a modell más adattípusra való újratanításához.

1. Válassza a **Közzététel** a tervező vászon felett lehetőséget.
1. Válasszon ki vagy hozzon létre egy folyamat-végpontot.

   > [!NOTE]
   > Több folyamat is közzétehető egyetlen végponton. Egy adott végpont minden folyamata egy verziószámot kap, amelyet megadhat a folyamat végpontjának meghívásakor.

1. Válassza a **Közzététel** lehetőséget.

## <a name="retrain-your-model"></a>A modell újratanítása

Most, hogy már rendelkezik egy közzétett betanítási folyamattal, felhasználhatja a modell új adataira való átképzéséhez. Elküldheti a futtatásokat egy folyamat-végpontról a Studio munkaterületről vagy programozott módon.

### <a name="submit-runs-by-using-the-studio-portal"></a>Futtatások küldése a Studio portál használatával

A következő lépések végrehajtásával elküldheti a paraméteres folyamat végpontját a Studio-portálról:

1. Ugrás a **végpontok** lapra a Studio-munkaterületen.
1. Válassza a **folyamat végpontjai** lapot. Ezután válassza ki a folyamat végpontját.
1. Válassza a **közzétett folyamatok** fület. Ezután válassza ki a futtatni kívánt folyamat verzióját.
1. Válassza a **Küldés** lehetőséget.
1. A telepítés párbeszédpanelen megadhatja a Futtatás paramétereinek értékeit. Ebben a példában frissítse az adatelérési utat a modell betanításához a nem USA-beli adatkészlet használatával.

![Képernyőkép, amely bemutatja, hogyan állíthat be egy paraméteres folyamat futtatását a tervezőben](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Futtatások küldése kód használatával

Az Áttekintés panelen megtalálhatja a közzétett folyamat REST-végpontját. A végpont meghívásával Újrataníthatja a közzétett folyamatot.

A REST-hívások elvégzéséhez szüksége lesz egy OAuth 2,0 tulajdonosi típusú hitelesítési fejlécre. További információ a munkaterület hitelesítésének beállításáról és a paraméteres REST-hívás létrehozásáról: [Azure Machine learning folyamat létrehozása a Batch pontozáshoz](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre egy paraméteres betanítási folyamat végpontját a tervező használatával.

Az előrejelzések készítésére szolgáló modellek üzembe helyezésének teljes bemutatása a [tervezői oktatóanyagban](tutorial-designer-automobile-price-train-score.md) a regressziós modell betanítása és üzembe helyezése című témakörben olvasható.

Az SDK használatával történő futtatási folyamat végpontjának közzétételéhez és elküldéséhez tekintse meg [ezt a cikket](how-to-deploy-pipelines.md).
