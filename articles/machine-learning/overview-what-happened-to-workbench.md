---
title: Mi történt a Workbenchcsel?
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning egy integrált adatelemzési megoldás, amely ML-alkalmazások modellezésére és üzembe helyezésére használható a Felhőbeli méretekben. A Workbench szolgáltatás ki lett vonva.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: larryfr
author: BlackMist
ms.date: 03/05/2020
ms.openlocfilehash: c800901ae139b16e75613b0cc8de3a9485186896
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521373"
---
# <a name="what-happened-to-azure-machine-learning-workbench"></a>Mi történt az Azure Machine Learning Workbench alkalmazással?

A Azure Machine Learning Workbench alkalmazást és néhány más korai szolgáltatást elavultak, és lecserélték a **szeptember 2018** -es kiadásban, hogy a fejlettebb [architektúra](concept-azure-machine-learning-architecture.md)legyen.

Az élmény javítása érdekében a kiadás számos jelentős frissítést tartalmaz, amelyeket az ügyfél visszajelzése kér. A kísérlet alapfunkciói a modell üzembe helyezése során nem változtak. Most azonban használhatja a robusztus <a href="/python/api/overview/azure/ml/intro" target="_blank">PYTHON SDK</a>-t, az R SDK-t és az [Azure CLI](reference-azure-machine-learning-cli.md) -t a gépi tanulási feladatok és folyamatok elvégzéséhez.

A Azure Machine Learning korábbi verziójában létrehozott összetevők többsége a saját helyi vagy Felhőbeli tárolójában tárolódik. Ezek a munkadarabok sohasem fognak eltűnni.

Ebből a cikkből megtudhatja, hogy mi változott, és hogyan befolyásolja a már meglévő munkáját a Azure Machine Learning Workbench és az API-kkal.

>[!Warning]
>Ez a cikk nem Azure Machine Learning Studio felhasználók számára készült. Olyan Azure Machine Learning ügyfelek számára készült, akik telepítették a Workbench (előzetes verzió) alkalmazást, és/vagy kísérletezési és modell-felügyeleti előzetes fiókkal rendelkeznek.


## <a name="what-changed"></a>Mi változott?

A Azure Machine Learning legújabb kiadása a következő funkciókat tartalmazza:
+ Egy [egyszerűsített Azure-erőforrás modell](concept-azure-machine-learning-architecture.md).
+ [Új portál felhasználói felület](how-to-track-experiments.md) a kísérletek és számítási célok kezeléséhez.
+ Egy új, átfogóbb Python <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>.
+ Az új bővített [Azure CLI-bővítmény](reference-azure-machine-learning-cli.md) a gépi tanuláshoz.

Az [architektúra](concept-azure-machine-learning-architecture.md) újratervezése egyszerű használat érdekében. Több Azure-erőforrás és -fiók helyett csak egy [Azure Machine Learning-munkaterület](concept-workspace.md) szükséges. Az [Azure Portalon](how-to-manage-workspace.md) gyorsan létrehozhat munkaterületeket. Munkaterületek használatával több felhasználó is tárolhatja a képzési és üzembe helyezési számítási célokat, a modellek kísérleteit, a Docker-rendszerképeket, a telepített modelleket stb.

Bár új továbbfejlesztett CLI-és SDK-ügyfelek találhatók a jelenlegi kiadásban, maga a Desktop Workbench alkalmazás is ki lett vonva. A kísérletek a [Azure Machine learning Studio munkaterület-irányítópultján](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal)kezelhetők. Használja az irányítópultot a kísérlet előzményeinek beolvasására, a munkaterülethez rendelt számítási célok, a modellek és a Docker-rendszerképek kezelésére, vagy akár webszolgáltatások üzembe helyezésére.

<a name="timeline"></a>

## <a name="support-timeline"></a>Támogatási idővonal

Január 9 2019-én a Machine Learning Workbench, Azure Machine Learning-kísérletezés és modellkezelés-fiókok, valamint a hozzájuk kapcsolódó SDK-és CLI-támogatással zárult.

Az <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK</a>, a [CLI](reference-azure-machine-learning-cli.md)és a [portál](how-to-manage-workspace.md)használatával minden legújabb funkció elérhető.

## <a name="what-about-run-histories"></a>Mi a helyzet a futtatási előzményekkel?

A régebbi futtatási előzmények már nem érhetők el, így továbbra is megtekintheti a futtatásokat a legújabb verzióban.

A futtatási előzmények mostantól **kísérletek**. A modell kísérleteit összegyűjtheti, és az SDK, a CLI vagy a Azure Machine Learning Studio használatával is megismerheti azokat.

A portál munkaterület-irányítópultja csak a Microsoft Edge, a Chrome és a Firefox böngészőkben támogatott:

[![Online portál](./media/overview-what-happened-to-workbench/image001.png)](./media/overview-what-happened-to-workbench/image001.png#lightbox)

Az új parancssori felület és az SDK használatával megkezdheti a modellek betanítását és a futtatási előzmények nyomon követését. Megtudhatja, hogyan használható az [oktatóanyag: modellek betanítása Azure Machine learning](tutorial-train-models-with-aml.md)használatával.

## <a name="will-projects-persist"></a>Megmaradnak a projektek?

Nem fog elveszni sem kód, sem munka. A régebbi verzióban, a projektek helyi könyvtárral rendelkező felhőbeli entitások. A legújabb verzióban helyi konfigurációs fájllal csatolja a helyi címtárakat a Azure Machine Learning munkaterülethez. Tekintse meg a [legújabb architektúra diagramját](concept-azure-machine-learning-architecture.md).

A projekt tartalmának nagy része már a helyi gépen van. Ezért csak létre kell hoznia egy konfigurációs fájlt a könyvtárban, és hivatkoznia kell a kódban a munkaterülethez való kapcsolódásra. A fájlokat és parancsfájlokat tartalmazó helyi könyvtár használatának folytatásához adja meg a könyvtár nevét a ["Experiment. submit"](/python/api/azureml-core/azureml.core.experiment.experiment) Python-parancsban vagy a `az ml project attach` CLI-parancs használatával.  Például:
```python
run = exp.submit(source_directory=script_folder,
                 script='train.py', run_config=run_config_system_managed)
```

[Hozzon létre egy munkaterületet](how-to-manage-workspace.md) az első lépésekhez.

## <a name="what-about-my-registered-models-and-images"></a>Mi a helyzet a regisztrált modellekkel és képekkel?

A régi modell beállításjegyzékében regisztrált modelleket át kell telepíteni az új munkaterületre, ha továbbra is használni szeretné őket. A modellek áttelepítéséhez töltse le a modelleket, majd regisztrálja újra az új munkaterületen.

A régi rendszerkép beállításjegyzékében létrehozott rendszerképeket nem lehet közvetlenül áttelepíteni az új munkaterületre. A legtöbb esetben a modell lemezkép létrehozása nélkül is üzembe helyezhető. Ha szükséges, létrehozhat egy rendszerképet a modellhez az új munkaterületen. További információkért lásd: [gépi tanulási modellek kezelése, regisztrálása, üzembe helyezése és figyelése](concept-model-management-and-deployment.md).

## <a name="what-about-deployed-web-services"></a>Mi a helyzet az üzembe helyezett webszolgáltatásokkal?

A régi CLI támogatásának befejezése után már nem telepíthet újra modelleket, és nem felügyelheti a modellkezelés-fiókkal eredetileg telepített webszolgáltatásokat. Ezek a webszolgáltatások azonban továbbra is működni fognak, amíg a Azure Container Service (ACS) továbbra is támogatott.

A legújabb verzióban a modellek webszolgáltatásként lesznek telepítve Azure Container Instances (ACI) vagy Azure Kubernetes Service (ak) fürtökhöz. A FPGA és a Azure IoT Edge is üzembe helyezhető.

További információt a következő cikkekben talál:
+ [A modellek üzembe helyezésének helye és módja](how-to-deploy-and-where.md)
+ [Oktatóanyag: modellek üzembe helyezése Azure Machine Learning](tutorial-deploy-models-with-aml.md)

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a [Azure Machine learning legújabb architektúrával](concept-azure-machine-learning-architecture.md).

A szolgáltatás áttekintéséhez olvassa el a [Mi az Azure Machine learning?](overview-what-is-azure-ml.md)című témakört.

Hozza létre első kísérletét a kívánt módszerrel:

  + [Saját környezet használata](tutorial-1st-experiment-sdk-setup-local.md)
  + [Python-jegyzetfüzetek használata](tutorial-1st-experiment-sdk-setup.md)
  + [R-Markdown használata](https://github.com/Azure/azureml-sdk-for-r) 
  + [Automatikus gépi tanulás használata](tutorial-designer-automobile-price-train-score.md) 
  + [A tervező drag & drop képességeinek használata](tutorial-first-experiment-automated-ml.md) 
  + [A ML-bővítmény használata a parancssori felülethez](tutorial-train-deploy-model-cli.md)
