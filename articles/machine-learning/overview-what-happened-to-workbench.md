---
title: Mi történt a Workbenchcsel?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning egy integrált adattudományi megoldás a gépi tanulási alkalmazások felhőméretű modellezéséhez és üzembe helyezéséhez. A Workbench szolgáltatás ki lettvezetve.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: larryfr
author: BlackMist
ms.date: 03/05/2020
ms.openlocfilehash: 4c680be897c4c1bf2ccf20df1d34ab6f59f559f2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816151"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Mi történt az Azure Machine Learning Workbench alkalmazással?

A Azure Machine Learning Workbench alkalmazás és néhány más korai funkció elavult, és **a 2018.** szeptemberi kiadásban ki lett cserélve, hogy lehetővé tegye a továbbfejlesztett architektúra [felépítését.](concept-azure-machine-learning-architecture.md)

A felhasználói élmény javítása érdekében a kiadás számos jelentős frissítést tartalmaz, amelyekre az ügyfelek visszajelzései kérik. A kísérleti futtatástól a modell üzembe helyezéséig az alapvető funkciók nem változtak. Most azonban használhatja a robusztus Python SDK-t, az R <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK-t</a>és az [Azure CLI-t](reference-azure-machine-learning-cli.md) a gépi tanulási feladatok és folyamatok elvégzéséhez.

A korábbi verzióban létrehozott összetevők Azure Machine Learning a saját helyi vagy felhőbeli tárolójában vannak tárolva. Ezek a munkadarabok sohasem fognak eltűnni.

Ebből a cikkből megtudhatja, mi változott, és milyen hatással van a már meglévő munkára a Azure Machine Learning Workbench API-ját.

>[!Warning]
>Ez a cikk nem a Azure Machine Learning Studio-felhasználóknak való. Olyan ügyfelek számára Azure Machine Learning, akik telepítették a Workbench (előzetes verzió) alkalmazást, és/vagy kísérletezés és modellkezelés előzetes verziójú fiókjai vannak.


## <a name="what-changed"></a>Mi változott?

Az Azure Machine Learning legújabb kiadása a következő funkciókat tartalmazza:
+ Egyszerűsített [Azure-erőforrásmodell.](concept-azure-machine-learning-architecture.md)
+ Új [felhasználói felület a portálon](how-to-log-view-metrics.md) a kísérletek és a számítási célok kezeléséhez.
+ Egy új, átfogóbb Python <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK.</a>
+ Az új kiterjesztett [Azure CLI-bővítmény a](reference-azure-machine-learning-cli.md) gépi tanuláshoz.

Az [architektúra](concept-azure-machine-learning-architecture.md) a könnyű használat érdekében újra lett tervezve. Több Azure-erőforrás és -fiók helyett csak egy [Azure Machine Learning-munkaterület](concept-workspace.md) szükséges. Az [Azure Portalon](how-to-manage-workspace.md) gyorsan létrehozhat munkaterületeket. Egy munkaterület használatával több felhasználó tárolhat betanítás és üzembe helyezés számítási célokat, modellkísérleteket, Docker-rendszerképeket, üzembe helyezett modelleket stb.

Bár a jelenlegi kiadásban új továbbfejlesztett CLI- és SDK-ügyfelek is vannak, maga az asztali Workbench alkalmazás ki lett vezetve. A kísérletek a munkaterület irányítópultján [kezelhetők a Azure Machine Learning stúdió.](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal) Használja az irányítópultot a kísérlet előzményeinek beolvasására, a munkaterülethez rendelt számítási célok, a modellek és a Docker-rendszerképek kezelésére, vagy akár webszolgáltatások üzembe helyezésére.

<a name="timeline"></a>

## <a name="support-timeline"></a>Támogatási idővonal

2019. január 9-én a Machine Learning Workbench-, Azure Machine Learning-kísérletezés- és modellkezelés-fiókok támogatása, valamint a társított SDK és CLI támogatása véget ért.

A legújabb képességek az <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK,</a>a [CLI](reference-azure-machine-learning-cli.md)és a portál használatával érhetők [el.](how-to-manage-workspace.md)

## <a name="what-about-run-histories"></a>Mi a helyzet a futtatási előzményekkel?

A régebbi futtatás-korábbiak már nem érhetők el, így továbbra is a legújabb verzióban láthatja a futtatásokat.

A futtatás-történeteket már kísérleteknek **nevezzük.** A modell kísérleteit az SDK, a CLI vagy a Azure Machine Learning stúdió.

A portál munkaterületi irányítópultja csak az Microsoft Edge, a Chrome és a Firefox böngészőkben támogatott:

[![Online portál](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Kezdje el a modellek betanítását és a futtatás nyilvántartását az új CLI és SDK használatával. Az Oktatóanyag: Modellek betanítása a [következővel: Azure Machine Learning.](tutorial-train-models-with-aml.md)

## <a name="will-projects-persist"></a>Megmaradnak a projektek?

Nem fog elveszni sem kód, sem munka. A régebbi verzióban, a projektek helyi könyvtárral rendelkező felhőbeli entitások. A legújabb verzióban helyi könyvtárakat csatolhat a Azure Machine Learning munkaterülethez egy helyi konfigurációs fájllal. Tekintse meg a [legújabb architektúra diagramját.](concept-azure-machine-learning-architecture.md)

A projekt tartalmának nagy része már a helyi gépen volt. Ezért csak létre kell hoznia egy konfigurációs fájlt abban a könyvtárban, és hivatkozni kell rá a kódban a munkaterülethez való csatlakozáshoz. A fájlokat és szkripteket tartalmazó helyi könyvtár használatának folytatásához adja meg a könyvtár nevét az [experiment.submit](/python/api/azureml-core/azureml.core.experiment.experiment) Python-parancsban vagy a `az ml project attach` CLI-paranccsal.  Például:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Hozzon létre egy munkaterületet](how-to-manage-workspace.md) az első lépésekhez.

## <a name="what-about-my-registered-models-and-images"></a>Mi a helyzet a regisztrált modellekkel és rendszerképekkel?

A régi modelljegyzékben regisztrált modelleket migrálni kell az új munkaterületre, ha továbbra is használni szeretné őket. A modellek áttelepítéséhez töltse le a modelleket, és regisztrálja őket újra az új munkaterületen.

A régi rendszerkép-beállításjegyzékben létrehozott rendszerképek nem migrálhatóak közvetlenül az új munkaterületre. A legtöbb esetben a modell üzembe helyezhető rendszerkép létrehozása nélkül. Szükség esetén létrehozhat egy rendszerképet a modellhez az új munkaterületen. További információ: Gépi tanulási modellek [kezelése, regisztrálása, üzembe helyezése és figyelése.](concept-model-management-and-deployment.md)

## <a name="what-about-deployed-web-services"></a>Mi a helyzet az üzembe helyezett webszolgáltatásokkal?

Most, hogy a régi CLI támogatása véget ért, már nem helyezhet üzembe modelleket, és nem kezelheti az eredetileg üzembe helyezett webszolgáltatásokat a modellkezelés fiókjával. Ezek a webszolgáltatások azonban továbbra is működni fognak, amíg a Azure Container Service (ACS) továbbra is támogatott.

A legújabb verzióban a modellek webszolgáltatásként vannak telepítve Azure Container Instances (ACI) vagy Azure Kubernetes Service (AKS) fürtökben. FPGA-kban is üzembe helyezheti az FPGA-kban, és Azure IoT Edge.

További információt az alábbi cikkekben talál:
+ [Modellek üzembe helyezése](how-to-deploy-and-where.md)
+ [Oktatóanyag: Modellek üzembe helyezése Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Következő lépések

Ismerje meg a [legújabb architektúrát a Azure Machine Learning.](concept-azure-machine-learning-architecture.md)

A szolgáltatás áttekintéséhez olvassa el a Mi az a [Azure Machine Learning? témakört.](overview-what-is-azure-ml.md)

Hozza létre az első kísérletét az előnyben részesített módszerrel:

  + [Saját környezet használata](tutorial-1st-experiment-sdk-setup-local.md)
  + [Python-jegyzetfüzetek használata](tutorial-1st-experiment-sdk-setup.md)
  + [Az R Markdown használata](https://github.com/Azure/azureml-sdk-for-r) 
  + [Automatizált gépi tanulás használata](tutorial-designer-automobile-price-train-score.md) 
  + [A tervező húzással & funkcióinak használata](tutorial-first-experiment-automated-ml.md) 
  + [Az ML-bővítmény használata a cli-hez](tutorial-train-deploy-model-cli.md)
