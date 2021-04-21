---
title: Mi a Azure Machine Learning
description: Azure Machine Learning egy integrált adattudományi megoldás adatszakértők és MLops számára a gépi tanulási alkalmazások felhőméretű modellezéséhez és üzembe helyezéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.author: larryfr
author: BlackMist
ms.date: 04/08/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: cd395f0ff29eed1e09f5bdac882482b201052c90
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814826"
---
# <a name="what-is-azure-machine-learning"></a>Mi az Azure Machine Learning?

Ebből a cikkből megtudhatja, Azure Machine Learning gépi tanulási modellek betanítása, üzembe helyezése, automatizálása, kezelése és nyomon követése egy felhőalapú környezet. 

Azure Machine Learning gépi tanuláshoz használható, a klasszikus gépi tanulástól a mély tanuláson át a felügyeltig és a felügyelet nélküli tanulásig. Akár Python- vagy R-kódot szeretne írni az SDK-val, akár [](#build-ml-models-in-the-studio)a studióban kódírás/kevés kód kóddal való munka mellett, gépi tanulási és mély tanulási modelleket is Azure Machine Learning-munkaterület. 

Kezdje meg a betanításokat a helyi gépen, majd horizontálisan felskálskálás a felhőbe. 

A szolgáltatás emellett olyan népszerű mélytanulási és megerősítési nyílt forráskódú eszközökkel is együttműködik, mint a PyTorch, a TensorFlow, a scikit-learn és a Ray RLlib. 

> [!Tip]
> **Ingyenes próbaverzió!**  Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a Azure Machine Learning [ingyenes vagy Azure Machine Learning](https://aka.ms/AMLFree) verzióját. Azure-szolgáltatásokra elkölthető krediteket kap. A kreditek felhasználása után megtarthatja a fiókját, és tovább használhatja azt az [ingyenes Azure-szolgáltatásokkal](https://azure.microsoft.com/free/). A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez.


## <a name="what-is-machine-learning"></a>Mit jelent a gépi tanulás?

A Machine Learning egy olyan adatelemzési módszer, amely lehetővé teszi, hogy a számítógépek a meglévő adatokból tanulva jövőbeni viselkedéseket, kimeneteket és trendeket jelezhessenek előre. A gépi tanulással a számítógépek anélkül tanulnak, hogy explicit módon programozták volna.

A gépi tanulás által biztosított előrejelzéseket felhasználva intelligensebbé tehetők az alkalmazások és az eszközök. Ha például online vásárol, a gépi tanulás segít ajánlást kínálni más termékekre a megvásárolt termékek alapján. Vagy ha ellopják a hitelkártyáját, a gépi tanulás összeveti a tranzakciót egy tranzakció-adatbázissal, és segít a csalás észlelésében. Ha robotporszívóra bízza a szoba kitakarítását, a gépi tanulás segít eldönteni, hogy a feladat el lett-e végezve.

## <a name="machine-learning-tools-to-fit-each-task"></a>Gépi tanulási eszközök az egyes feladatokhoz 

Azure Machine Learning biztosítja a fejlesztők és adattudósok gépi tanulási munkafolyamatokhoz szükséges összes eszközét, beleértve a következőket:
+ A [Azure Machine Learning tervezője:](tutorial-designer-automobile-price-train-score.md)húzza át az n-drop modulokat a kísérletek felépítéséhez, majd folyamatok alacsony kódú környezetben való üzembe helyezéséhez.

+ Jupyter-notebookok: [](https://github.com/Azure/MachineLearningNotebooks) használja a példajegyzetfüzeteket, vagy hozzon létre saját jegyzetfüzeteket a PythonHOZ készült <a href="/python/api/overview/azure/ml/intro" target="_blank">SDK-minták</a> gépi tanuláshoz való használatához. 

+ R-szkriptek vagy -jegyzetfüzetek, amelyekben az <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK for R</a> használatával írhat saját kódot, vagy az R-modulokat használhatja a tervezőben.

+ A [Többmodelles](https://aka.ms/many-models) megoldásgyorsító (előzetes verzió) a Azure Machine Learning, és lehetővé teszi gépi tanulási modellek száz, vagy akár több ezer gépi tanulási modell betanítát, működtetését és kezelését.

+ [A Visual Studio Code (előzetes verzió)](how-to-set-up-vs-code-remote.md) gépi tanulási bővítménye teljes körű fejlesztési környezetet biztosít a gépi tanulási projektek fejlesztésére és kezelésére.

+ [A Machine Learning CLI](reference-azure-machine-learning-cli.md) egy Azure CLI-bővítmény, amely parancsokat biztosít a Azure Machine Learning erőforrások kezeléséhez a parancssorból.

+ [Integráció olyan nyílt](concept-open-source.md) forráskódú keretrendszerekkel, mint a PyTorch, a TensorFlow és a scikit-learn, valamint számos más, a teljes gépi tanulási folyamat betanítása, üzembe helyezése és kezelése.

+ [Megerősítési](how-to-use-reinforcement-learning.md) tanulás Ray RLlib-el

Akár az [MLflow-t is](how-to-use-mlflow.md) használhatja metrikák nyomon követésére és modellek üzembe helyezésére, vagy a Kubeflow-ra a végpontok között futó [munkafolyamatok felépítéséhez.](https://www.kubeflow.org/docs/azure/)

## <a name="build-ml-models-in-python-or-r"></a>Gépi tanulási modellek összeállítása Python vagy R használatával

Kezdje meg a betanításokat a helyi gépen az Azure Machine Learning <a href="/python/api/overview/azure/ml/intro" target="_blank">Python SDK</a> vagy az R <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK használatával.</a> Ezután horizontálisan felskálásokat lehet a felhőbe méretezni. 

Számos rendelkezésre [](how-to-create-attach-compute-studio.md)álló számítási cél (például a Azure Machine Learning Compute és az [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks), a fejlett [hiperparaméter-finomhangolási](how-to-tune-hyperparameters.md)szolgáltatásokkal pedig a felhő segítségével gyorsabban építhet jobb modelleket.

A modell [betanítását és finomhangolását az](tutorial-auto-train-models.md) SDK használatával is automatizálhatja.

## <a name="build-ml-models-in-the-studio"></a>Gépi tanulási modellek összeállítása a studióban

[Azure Machine Learning stúdió](https://studio.azureml.net) egy webportál a Azure Machine Learning a modell betanítását, üzembe helyezését és eszközkezelését alacsony kódszámú és kód nélkül is használható lehetőségekhez. A studio integrálható a Azure Machine Learning SDK-val a zökkenőmentes élmény érdekében. További információ: Mi az a [Azure Machine Learning stúdió.](overview-what-is-machine-learning-studio.md)

+ **Azure Machine Learning Designer**

  Gépi [tanulási modellek betanítása](concept-designer.md) és üzembe helyezése kódírás nélkül a tervezővel. Az első [lépésekhez próbálja](tutorial-designer-automobile-price-train-score.md) ki a tervezői oktatóanyagot. 

  ![Animált gif a tervező húzással Azure Machine Learning felületéről](media/concept-designer/designer-drag-and-drop.gif)

+ **Kísérletek nyomon követése**

  Megtudhatja, hogyan [követheti nyomon és vizualizálhatja az adattudományi kísérleteket](how-to-track-monitor-analyze-runs.md) a studióban. 

    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-history.png" alt-text="Futtatás részletei a Azure Machine Learning stúdió":::


+ **És még sok más...**

  Látogasson Azure Machine Learning stúdió [a](https://studio.azureml.net)ml.azure.com webhelyre.


## <a name="mlops-deploy--lifecycle-management"></a>MLOps: & életciklus-kezelés üzembe helyezése
Ha rendelkezik a megfelelő modellel, könnyedén használhatja azt egy webszolgáltatásban, egy IoT-eszközön vagy egy Power BI. További információkért tekintse meg az üzembe helyezésről és a [hol való telepítésről tájékoztató cikket.](how-to-deploy-and-where.md)

Ezután a Pythonhoz készült Azure Machine Learning [SDK,](/python/api/overview/azure/ml/)a Azure Machine Learning stúdió vagy a Machine Learning CLI használatával [kezelheti az](https://ml.azure.com)üzembe [helyezett modelleket.](reference-azure-machine-learning-cli.md)

Ezek a modellek valós időben vagy [](how-to-consume-web-service.md) [aszinkron](./tutorial-pipeline-batch-scoring-classification.md) módon, nagy mennyiségű adaton használhatók fel és kaphatnak előrejelzéseket.

A fejlett [gépi tanulási folyamatokkal](concept-ml-pipelines.md)pedig az adatok előkészítésétől, a modellek betanításán és értékelésén át az üzembe helyezésig minden lépésen együttműködhet. A folyamatok a következőt teszik lehetővé:

* A gépi tanulási folyamat automatizálása a felhőben
* Újra felhasználja az összetevőket, és csak szükség esetén futtassa újra a lépéseket
* Különböző számítási erőforrások használata az egyes lépésben
* Kötegelt pontozási tevékenységek futtatása

Ha szkriptekkel szeretné automatizálni a gépi tanulási munkafolyamatot, a [Machine Learning parancssori](reference-azure-machine-learning-cli.md) felület olyan parancssori eszközöket biztosít, amelyek általános feladatokat hajtnak végre, például egy betanítási futtatás elküldése vagy egy modell üzembe helyezése.

Az alkalmazás használatának Azure Machine Learning lásd: [További lépések.](#next-steps)

## <a name="integration-with-other-services"></a>Integráció más szolgáltatásokkal

Azure Machine Learning azure-platform más szolgáltatásaival is együttműködik, és olyan nyílt forráskódú eszközökkel is integrálható, mint a Git és az MLFlow.

+ Számítási célok, __például Azure Kubernetes Service__, __Azure Container Instances__, __Azure Databricks__, __Azure Data Lake Analytics__, __és Azure HDInsight.__ A számítási célokkal kapcsolatos további információkért lásd: Mik azok a [számítási célok?](concept-compute-target.md).
+ __Azure Event Grid:__. További információ: [Consume Azure Machine Learning events](./how-to-use-event-grid.md)..
+ __Azure Monitor:__. További információ: [Monitoring Azure Machine Learning.](monitor-azure-machine-learning.md)
+ Olyan adattárak, mint az __Azure Storage-fiókok,__ __Azure Data Lake Storage__, __Azure SQL Database,__ __Azure Database for PostgreSQL__, __és Azure Open Datasets.__ További információ: Adatok elérése az [Azure Storage-szolgáltatásokban](how-to-access-data.md) és [Adatkészletek létrehozása a Azure Open Datasets.](how-to-create-register-datasets.md)
+ __Azure Virtual Networks.__ További információ: Virtuális hálózatok [elkülönítése és adatvédelmi áttekintése.](how-to-network-security-overview.md)
+ __Azure Pipelines__. További információ: [Gépi tanulási modellek betanítása és üzembe helyezése.](/azure/devops/pipelines/targets/azure-machine-learning)
+ __Git-adattár naplói.__ További információ: [Git-integráció.](concept-train-model-git-integration.md)
+ __MLFlow__. További információ: [MLflow](how-to-use-mlflow.md)  a metrikák nyomon követéséhez és Mlflow-modellek üzembe helyezése [webszolgáltatásként](how-to-deploy-mlflow-models.md) 
+ __Kubeflow__. További információkért lásd: végpontok között futó [munkafolyamatok létrehozása.](https://www.kubeflow.org/docs/azure/)

### <a name="secure-communications"></a>Biztonságos kommunikáció

Az Azure Storage-fiók, a számítási célok és más erőforrások biztonságosan használhatók egy virtuális hálózaton belül a modellek betanítása és a dedokencia végrehajtása érdekében. További információ: Virtuális hálózatok [elkülönítése és adatvédelmi áttekintése.](how-to-network-security-overview.md)

## <a name="next-steps"></a>Következő lépések

- Hozza létre az első kísérletét a választott módszerrel:
- + [Első lépések saját fejlesztési környezetben](tutorial-1st-experiment-sdk-setup-local.md)
  + [Jupyter-notebookok használata számítási példányon gépi tanulási modellek & betanítása](tutorial-1st-experiment-sdk-setup.md)
  + [Automatizált gépi tanulás használata gépi tanulási modellek & üzembe helyezéséhez](tutorial-first-experiment-automated-ml.md) 
  + [Erőforrások kezelése a Visual Studio Code-ban](how-to-manage-resources-vscode.md)
  + [Képbesorolási modell betanítása és üzembe helyezése a Visual Studio Code használatával](tutorial-train-deploy-image-classification-model-vscode.md)
  + [A tervező húzással való & használatával betaníthatja az & üzembe helyezést](tutorial-designer-automobile-price-train-score.md) 
  + [Modell betanítás és üzembe helyezése a Machine Learning CLI használatával](tutorial-train-deploy-model-cli.md)

- Ismerje meg a [gépi tanulási folyamatokat](concept-ml-pipelines.md) a gépi tanulási forgatókönyvek létrehozásához, optimalizálásához és felügyeletéhez.

- Olvassa el a részletes Azure Machine Learning [és fogalmakat.](concept-azure-machine-learning-architecture.md)
