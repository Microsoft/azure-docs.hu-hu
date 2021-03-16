---
title: Mi az Azure Machine Learning Studio?
description: A Studio egy webes portál Azure Machine Learning munkaterületekhez. A Studio a kód nélküli és a kód első lépéseit kombinálja egy inkluzív adatelemzési platformon.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
adobe-target: true
ms.openlocfilehash: 48c4b2a73628ab2105e23054d747e28acc105d01
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563188"
---
# <a name="what-is-azure-machine-learning-studio"></a>Mi az Azure Machine Learning Studio?

Ebből a cikkből megtudhatja, hogyan Azure Machine Learning Studio, az adattudós-fejlesztőknek szóló webes portál [Azure Machine learning](overview-what-is-azure-ml.md). A Studio a kód nélküli és a kód első lépéseit kombinálja egy inkluzív adatelemzési platformon.

Ebből a cikkből megtudhatja:
>[!div class="checklist"]
> - [Gépi tanulási projektek](#author-machine-learning-projects) készítése a Studióban.
> - [Eszközök és erőforrások kezelése](#manage-assets-and-resources) a Studióban.
> - A [Azure Machine learning Studio és ml Studio (klasszikus)](#ml-studio-classic-vs-azure-machine-learning-studio)közötti különbségek.

Javasoljuk, hogy használja az operációs rendszerével kompatibilis böngésző legújabb verzióját. A következő böngészők támogatottak:
  * Microsoft Edge (az új Microsoft Edge, legújabb verzió. Nem a Microsoft Edge örökölt)
  * Safari (csak Mac, legújabb verzió)
  * Chrome (legújabb verzió)
  * Firefox (legújabb verzió)

## <a name="author-machine-learning-projects"></a>Gépi tanulási projektek készítése

A Studio a projekt típusától és a felhasználói élmény szintjétől függően több szerzői tapasztalatot kínál.

+ **Jegyzetfüzetek**

  Saját kódot írhat és futtathat felügyelt [Jupyter notebook-kiszolgálókon](how-to-run-jupyter-notebooks.md) , amelyek közvetlenül a studióba vannak integrálva. 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="Képernyőfelvétel: kód írása és futtatása jegyzetfüzetben":::

+ **Azure Machine Learning Designer**

  A Designer használatával a gépi tanulási modelleket bármilyen kód írása nélkül betaníthatja és üzembe helyezheti. Az adatkészletek és modulok húzásával ML-folyamatokat hozhat létre. Próbálja ki a [tervezői oktatóanyagot](tutorial-designer-automobile-price-train-score.md).

    ![Azure Machine Learning Designer – példa](media/concept-designer/designer-drag-and-drop.gif)

+ **Automatikus gépi tanulás felhasználói felülete**

  Megtudhatja, hogyan hozhat létre egy könnyen használható kezelőfelülettel rendelkező [AUTOMATIZÁLT ml-kísérleteket](tutorial-first-experiment-automated-ml.md) . 

  [![Azure Machine Learning Studio navigációs panelje](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Adatfelirat**

    A [Azure Machine learning adatcímkéző](how-to-create-labeling-projects.md) használatával hatékonyan koordinálhatja az adatcímkéző projekteket.

## <a name="manage-assets-and-resources"></a>Eszközök és erőforrások kezelése

A gépi tanulási eszközöket közvetlenül a böngészőben kezelheti. Az eszközök az SDK és a Studio között azonos munkaterületen vannak megosztva zökkenőmentes felhasználói élmény érdekében. A Studio használatával kezelheti a következőket:

- Modellek
- Adathalmazok
- Adattárak
- Számítási erőforrások
- Notebooks
- Kísérletek
- Naplók futtatása
- Pipelines 
- Folyamat végpontjai

Még ha tapasztalt fejlesztő is, a Studio leegyszerűsítheti a munkaterület-erőforrások kezelését.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (klasszikus) vs Azure Machine Learning Studio

Az 2015-as kiadásban megjelent **ml Studio (klasszikus)** volt az első drag-and-drop Machine learning-szerkesztő. 

A **ml Studio (klasszikus)** egy önálló szolgáltatás, amely csak vizuális élményt nyújt. A Studio (klasszikus) nem működik együtt Azure Machine Learningokkal.

A **Azure Machine learning** egy különálló és modernizált szolgáltatás, amely teljes adatelemzési platformot biztosít. Támogatja mind a Code-First, mind az alacsony kódú élményt.

A **Azure Machine learning Studio** *egy Azure Machine learning* webportálja, amely alacsony kódú és kód nélküli lehetőségeket tartalmaz a projektek készítéséhez és az eszközök kezeléséhez. 

Azt javasoljuk, hogy az új felhasználók ML Studio (klasszikus) helyett **Azure Machine learning** válasszanak az adatelemzési eszközök legújabb tartományához. Ha Ön egy meglévő ML Studio (klasszikus) felhasználó, érdemes [áttelepítenie a Azure Machine Learningra](classic/migrate-overview.md).

Íme néhány előny a Azure Machine Learningre való áttéréshez:

- Méretezhető számítási fürtök nagy léptékű képzéshez.
- Vállalati biztonság és irányítás.
- Együttműködés a népszerű nyílt forráskódú eszközökkel.
- Végpontok közötti MLOps.

### <a name="feature-comparison"></a>Szolgáltatások összehasonlítása

[!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

* **Hiányzó felhasználói felületi elemek a Studióban** Az Azure szerepköralapú hozzáférés-vezérléssel korlátozható a Azure Machine Learningkal végrehajtható műveletek korlátozása. Ezek a korlátozások megakadályozhatják, hogy a felhasználói felület elemei megjelenjenek a Azure Machine Learning Studióban. Ha például olyan szerepkört rendelt hozzá, amely nem tud számítási példányt létrehozni, akkor a számítási példány létrehozásának lehetősége nem fog megjelenni a Studióban. További információ: [felhasználók és szerepkörök kezelése](how-to-assign-roles.md).

## <a name="next-steps"></a>Következő lépések

Látogasson el a [studióba](https://ml.azure.com), vagy Ismerje meg a különböző szerzői lehetőségeket a következő oktatóanyagokkal:  

- + [Ismerkedés a saját fejlesztési környezetével](tutorial-1st-experiment-sdk-setup-local.md)
  + [Jupyter notebookok használata számítási példányokon a & üzembe helyezési modelljeinek betanításához](tutorial-1st-experiment-sdk-setup.md)
  + [Az automatizált gépi tanulás használata a & üzembe helyezési modelljeinek betanításához](tutorial-first-experiment-automated-ml.md)  
  + [A Designer használata & üzembe helyezési modelljeinek betanításához](tutorial-designer-automobile-price-train-score.md)
  + [A Studio használata biztonságos virtuális hálózaton](how-to-enable-studio-virtual-network.md)