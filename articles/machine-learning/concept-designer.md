---
title: ML modellek készítése tervezővel
titleSuffix: Azure Machine Learning
description: Ismerje meg a Azure Machine Learning tervezőjét alkotó feltételeket, fogalmakat és munkafolyamatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.custom: designer
ms.openlocfilehash: 7dcd2015a669d9640f4163e6decc96689bb4ca7b
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167060"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Mi az az Azure Machine Learning Designer (előzetes verzió)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

A Azure Machine Learning Designer lehetővé teszi, hogy vizuálisan összekapcsolja az [adatkészleteket](#datasets) és a [modulokat](#module) egy interaktív vásznon a gépi tanulási modellek létrehozásához. A tervező megismeréséhez tekintse meg a következő [oktatóanyagot: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)

![Azure Machine Learning Designer – példa](./media/concept-designer/designer-drag-and-drop.gif)

A tervező a Azure Machine Learning [munkaterületet](concept-workspace.md) használja a megosztott erőforrások rendszerezéséhez, például:

+ [Pipelines](#pipeline)
+ [Adathalmazok](#datasets)
+ [Számítási erőforrások](#compute)
+ [Regisztrált modellek](concept-azure-machine-learning-architecture.md#models)
+ [Közzétett folyamatok](#publish)
+ [Valós idejű végpontok](#deploy)

## <a name="model-training-and-deployment"></a>Modellek betanítása és üzembe helyezése

A tervező a gépi tanulási modellek készítésére, tesztelésére és üzembe helyezésére szolgáló vizualizációs vásznat biztosít. A tervezővel a következőket teheti:

+ Az [adatkészletek](#datasets) és [modulok](#module) húzása a vászonra.
+ A modulok összekapcsolásával hozzon létre egy [folyamat piszkozatot](#pipeline-draft).
+ A Azure Machine Learning munkaterületen lévő számítási erőforrásokkal elküldheti a [folyamat futtatását](#pipeline-run) .
+ Alakítsa át a **betanítási folyamatokat** a **folyamatok következtetésére**.
+ A folyamatokat [közzéteheti](#publish) a REST- **folyamat végpontján** egy olyan új folyamat elküldéséhez, amely különböző paraméterekkel és adatkészletekkel fut.
    + Közzétételi **folyamat** közzététele egy folyamat újrafelhasználásához a paraméterek és adatkészletek módosítása során.
    + Egy korábban betanított modell használatával közzétehet egy **Batch-következtetési** folyamatot, amellyel előrejelzéseket készíthet az új adatairól.
+ Valós idejű következtetéseket [helyezhet üzembe](#deploy) **egy valós** idejű végponton, hogy valós időben előrejelzéseket készítsen az új adatelemzésekről.

![Munkafolyamati diagram képzésekhez, kötegelt következtetésekhez és valós idejű következtetésekhez a Designerben](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Folyamat

A [folyamatok](concept-azure-machine-learning-architecture.md#ml-pipelines) adatkészleteket és analitikai modulokból állnak, amelyeket Ön is kapcsolódhat. A folyamatok számos felhasználási lehetőséggel rendelkeznek: létrehozhat egy olyan folyamatot, amely egyetlen modellt vagy egy több modellt is betanít. Létrehozhat egy olyan folyamatot, amely valós időben vagy kötegben készít előrejelzéseket, vagy olyan folyamatot hoz létre, amely csak az adattisztítást végzi el. A folyamatok lehetővé teszik a munkafolyamatok újrafelhasználását és a projektek rendszerezését.

### <a name="pipeline-draft"></a>Folyamat piszkozata

Amikor szerkeszt egy folyamatot a tervezőben, a **rendszer folyamatként**menti az előrehaladást. A folyamat piszkozatait bármikor szerkesztheti a modulok hozzáadásával vagy eltávolításával, a számítási célok konfigurálásával, a paraméterek létrehozásával és így tovább.

Egy érvényes folyamat a következő jellemzőkkel rendelkezik:

* Az adatkészletek csak modulokhoz csatlakozhatnak.
* A modulok csak adatkészletekhez vagy más modulokhoz csatlakozhatnak.
* A modulok összes bemeneti portjának csatlakoznia kell az adatfolyamhoz.
* Az egyes modulokhoz szükséges összes paramétert be kell állítani.

Amikor készen áll a folyamat piszkozatának futtatására, elküld egy folyamat futtatását.

### <a name="pipeline-run"></a>Folyamat futtatása

Minden alkalommal, amikor futtat egy folyamatot, a folyamat és annak eredményei a munkaterületen a **folyamat futtatásakor**tárolódnak. Visszatérhet bármelyik folyamat futtatásához, és megvizsgálhatja a hibaelhárítási és naplózási célokat. Egy folyamat futásának **klónozásával** létrehozhat egy új folyamat-piszkozatot a szerkesztéshez.

A folyamat-futtatások [kísérletekbe](concept-azure-machine-learning-architecture.md#experiments) vannak csoportosítva a futtatási előzmények rendszerezéséhez. Megadhatja a kísérletet minden egyes folyamat futtatásához. 

## <a name="datasets"></a>Adathalmazok

A Machine learning-adatkészlet megkönnyíti az adataihoz való hozzáférést és azokkal való munkavégzést. A tervezőben több minta adatkészletet is tartalmaz, hogy kísérletezzen a használatával. A szükségesnél több adatkészletet is [regisztrálhat](how-to-create-register-datasets.md) .

## <a name="module"></a>Modul

A modulok az adatokon végezhető algoritmusok. A tervező több modult is tartalmaz az adatok beáramlása funkcióból a képzésre, a pontozásra és az érvényesítési folyamatokra.

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paraméterei megjelennek a Tulajdonságok panelen a vászontól jobbra. A modell hangolásához módosíthatja a paramétereket a panelen. A tervezőben beállíthatja az egyes modulok számítási erőforrásait. 

![Modul tulajdonságai](./media/concept-designer/properties.png)

Ha segítségre van az elérhető gépi tanulási algoritmusok könyvtárában, tekintse át a következő témakört: [algoritmus & modul-hivatkozás áttekintése](algorithm-module-reference/module-reference.md). Az algoritmus kiválasztásával kapcsolatos segítségért tekintse meg a [Azure Machine learning algoritmust ismertető lapot](algorithm-cheat-sheet.md).

## <a name="compute-resources"></a><a name="compute"></a>Számítási erőforrások

A munkaterületen lévő számítási erőforrások használatával futtathatja a folyamatot, és üzemeltetheti a telepített modelleket valós idejű végpontok vagy folyamat-végpontok (a Batch-következtetések esetében). A támogatott számítási célok a következők:

| Számítási cél | Képzés | Üzembe helyezés |
| ---- |:----:|:----:|
| Azure Machine Learning számítás | ✓ | |
| Azure Machine Learning számítási példány | ✓ | |
| Azure Kubernetes Service | | ✓ |

A számítási célok a [Azure Machine learning munkaterülethez](concept-workspace.md)vannak csatolva. A számítási célokat a [Azure Machine learning Studióban](https://ml.azure.com)a munkaterületen kezelheti.

## <a name="deploy"></a>Üzembe helyezés

A valós idejű következtetések elvégzéséhez a folyamatot **valós idejű végpontként**kell üzembe helyezni. A valós idejű végpont egy külső alkalmazás és a pontozási modell közötti felületet hoz létre. A valós idejű végpontra irányuló hívás valós időben adja vissza az előrejelzési eredményeket az alkalmazásnak. A valós idejű végpontok hívásához át kell adni a végpont üzembe helyezésekor létrehozott API-kulcsot. A végpont egy REST-alapú, népszerű architektúra a webes programozási projektekhez.

A valós idejű végpontokat egy Azure Kubernetes Service-fürtre kell telepíteni.

A modell üzembe helyezésével kapcsolatos további információkért lásd [: oktatóanyag: Machine learning-modell üzembe helyezése a Designer](tutorial-designer-automobile-price-deploy.md)használatával.

## <a name="publish"></a>Közzététel

A folyamat egy folyamat **végpontján**is közzétehető. A valós idejű végponthoz hasonlóan a folyamat végpontja lehetővé teszi, hogy az új folyamat külső alkalmazásokból is fusson REST-hívásokat használva. Azonban a folyamat végpontján keresztül nem lehet valós időben elküldeni vagy fogadni az adataikat.

A közzétett folyamatok rugalmasak, felhasználhatók modellek betanítására és újraképzésére, [kötegelt következtetések elvégzésére](how-to-run-batch-predictions-designer.md), új adatok feldolgozására és sok más lehetőségre. Több folyamatot is közzétehet egyetlen folyamat-végponton, és megadhatja, hogy melyik folyamat melyik verzióját szeretné futtatni.

A közzétett folyamat az egyes modulokhoz tartozó folyamat piszkozatában definiált számítási erőforrásokon fut.

A tervező létrehoz egy [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) -objektumot, mint az SDK-t.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Áthelyezés a vizualizáció kezelőfelületéről a tervezőbe

A Visual Interface (előzetes verzió) frissítve lett, és mostantól Azure Machine Learning Designer (előzetes verzió). A tervező olyan folyamat-alapú háttérrendszer használatára lett újratervezve, amely teljesen integrálódik a Azure Machine Learning többi szolgáltatásával. 

A frissítések eredményeképpen a vizuális felület egyes fogalmai és kifejezései módosultak vagy átnevezve lettek. A legfontosabb koncepcionális változásokért tekintse meg az alábbi táblázatot. 

| Koncepció a Designerben | Korábban a vizuális felületen |
| ---- |:----:|
| Folyamat piszkozata | Experiment |
| Valós idejű végpont | Webszolgáltatás |

### <a name="migrating-to-the-designer"></a>Migrálás a tervezőbe

A meglévő vizuális felületi kísérleteket és webszolgáltatásokat átalakíthatja folyamatokra és valós idejű végpontokra a tervezőben. A Visual Interface-eszközök áttelepíthetők a következő lépésekkel:

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Következő lépések

* A prediktív elemzés és a gépi tanulás alapjai a [bemutatóban: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)
* Megtudhatja, hogyan módosíthatja a meglévő [tervezői mintákat](samples-designer.md) az igényeinek megfelelően.
