---
title: Mi az a munkaterület?
titleSuffix: Azure Machine Learning
description: A munkaterület a Azure Machine Learning legfelső szintű erőforrása. Megőrzi az összes betanítási Futtatás előzményeit, naplókkal, metrikákkal, kimenettel és a parancsfájlok pillanatképével.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: bf2419799ad6c229c6ffb891bc588479e134d427
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500393"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Mi az az Azure Machine Learning-munkaterület?

A munkaterület a Azure Machine Learning legfelső szintű erőforrása, amely központosított helyet biztosít a Azure Machine Learning használatakor létrehozott összes összetevővel való együttműködéshez.  A munkaterület megőrzi az összes betanítási Futtatás előzményeit, beleértve a naplókat, a metrikákat, a kimenetet és a parancsfájlok pillanatképét. Ezekkel az információkkal meghatározhatja, hogy melyik betanítási Futtatás a legjobb modellt állítja elő.  

Ha egy modellt szeretne, regisztrálja azt a munkaterületen. Ezután a regisztrált modell-és pontozási szkriptekkel üzembe helyezheti az Azure Container Instances, az Azure Kubernetes Service-t vagy egy mezőre programozható Gate array (FPGA)-t REST-alapú HTTP-végpontként. A modellt egy Azure IoT Edge eszközre is üzembe helyezheti modulként.

## <a name="taxonomy"></a>Elnevezési rendszert 

A munkaterület taxonómiaét az alábbi ábra szemlélteti:

[![Munkaterület besorolása](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

A diagram a munkaterület következő összetevőit jeleníti meg:

+ A munkaterületek tartalmazhatnak [Azure Machine learning számítási példányokat](concept-compute-instance.md), a Azure Machine learning futtatásához szükséges Python-környezettel konfigurált Felhőbeli erőforrásokat.

+ A [felhasználói szerepkörök](how-to-assign-roles.md) lehetővé teszik a munkaterület más felhasználókkal, csapatokkal vagy projektekkel való megosztását.
+ A kísérletek futtatásához [számítási célokat](concept-azure-machine-learning-architecture.md#compute-targets) kell használni.
+ A munkaterület létrehozásakor a [társított erőforrások](#resources) is létrejönnek Önnek.
+ A [kísérletek](concept-azure-machine-learning-architecture.md#experiments) olyan képzések, amelyeket a modellek létrehozásához használhat.  
+ A [folyamatok](concept-azure-machine-learning-architecture.md#ml-pipelines) újrafelhasználható munkafolyamatok a modell betanításához és átképzéséhez.
+ Az [adatkészletek](concept-azure-machine-learning-architecture.md#datasets-and-datastores) segítséget nyújtanak a modell betanításához és a folyamat létrehozásához használt adat kezeléséhez.
+ Ha már rendelkezik egy olyan modellel, amelyet telepíteni szeretne, hozzon létre egy regisztrált modellt.
+ Hozzon létre egy [telepítési végpontot](concept-azure-machine-learning-architecture.md#endpoints)a regisztrált modell és egy pontozási parancsfájl használatával.

## <a name="tools-for-workspace-interaction"></a>A munkaterület-interakció eszközei

A munkaterületet a következő módokon használhatja:

> [!IMPORTANT]
> Az alábbi megjelölésű eszközök (előzetes verzió) jelenleg nyilvános előzetes verzióban érhetők el.
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ A weben:
    + [Azure Machine Learning Studio ](https://ml.azure.com) 
    + [Azure Machine Learning Designer](concept-designer.md) 
+ Bármely Python-környezetben a [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro)-val.
+ Bármilyen R-környezetben az [r-hez készült Azure Machine learning SDK-val (előzetes verzió)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ A parancssorban a Azure Machine Learning [CLI bővítmény](./reference-azure-machine-learning-cli.md) használatával
+ [Azure Machine Learning VS Code-bővítmény](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Gépi tanulás munkaterülettel

A gépi tanulási feladatok beolvassák és/vagy leírják a munkaterületet.

+ Futtasson egy kísérletet a modell betanításához – a kísérlet futtatása az eredményeket a munkaterületre.
+ Az automatizált ML használatával betaníthatja a modelleket – a betanítási eredményeket a munkaterületre írja.
+ Modell regisztrálása a munkaterületen.
+ Modell üzembe helyezése – a regisztrált modell használatával hozza létre a központi telepítést.
+ Újrafelhasználható munkafolyamatok létrehozása és futtatása.
+ Megtekintheti a gépi tanulási összetevőket, például a kísérleteket, a folyamatokat, a modelleket, a központi telepítéseket.
+ Modellek nyomon követése és figyelése.

## <a name="workspace-management"></a>Munkaterület-kezelés

A következő munkaterület-felügyeleti feladatokat is végrehajthatja:

| Munkaterület-felügyeleti feladat   | Portál              | Studio | Python SDK/R SDK       | parancssori felület        | VS Code
|---------------------------|---------|---------|------------|------------|------------|
| Munkaterület létrehozása        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Munkaterület-hozzáférés kezelése    | **&check;**   || |  **&check;**    ||
| Számítási erőforrások létrehozása és kezelése    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Jegyzetfüzet-alapú virtuális gép létrehozása |   | **&check;** | |     ||

> [!WARNING]
> Ha áthelyezi a Azure Machine Learning munkaterületet egy másik előfizetésbe, vagy áthelyezi a tulajdonosi előfizetést egy új bérlőre, nem támogatott. Ez hibákhoz vezethet.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Munkaterület létrehozása

Több módon is létrehozhat egy munkaterületet:  

* A pont-és kattintási felület [Azure Portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) az egyes lépések lépésein végezhető el.
* A [Pythonhoz készült Azure Machine learning SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace) használatával létrehozhat egy munkaterületet a Python-parancsfájlok vagy Jupyter-jegyzetfüzetek segítségével
* Ha automatizálni vagy testre szeretné szabni a vállalati biztonsági szabványokkal való létrehozást, használjon [Azure Resource Manager sablont](how-to-create-workspace-template.md) vagy a [Azure Machine learning CLI](reference-azure-machine-learning-cli.md) -t.
* Ha a Visual Studio Code-ban dolgozik, használja a [vs Code bővítményt](how-to-manage-resources-vscode.md#create-a-workspace).

> [!NOTE]
> A munkaterület neve megkülönbözteti a kis-és nagybetűket.

## <a name="associated-resources"></a><a name="resources"></a> Társított erőforrások

Amikor új munkaterületet hoz létre, az automatikusan létrehoz több Azure-erőforrást, amelyet a munkaterület használ:

+ [Azure Storage-fiók](https://azure.microsoft.com/services/storage/): a munkaterület alapértelmezett adattára.  A Azure Machine Learning számítási példányokhoz használt Jupyter-jegyzetfüzeteket is itt tárolja. 
  
  > [!IMPORTANT]
  > Alapértelmezés szerint a Storage-fiók egy általános célú v1-fiók. Ezt az [általános célú v2-re frissítheti](../storage/common/storage-account-upgrade.md) a munkaterület létrehozása után. Az általános célú v2-re való frissítés után ne engedélyezze a hierarchikus névteret a Storage-fiókban.

  Meglévő Azure Storage-fiók használatához nem lehet prémium szintű fiók (Premium_LRS és Premium_GRS). Emellett nem lehet hierarchikus névtér (Azure Data Lake Storage Gen2). A munkaterület _alapértelmezett_ Storage-fiókja nem támogatja a Premium Storage-t vagy a hierarchikus névtereket. A Premium Storage vagy a hierarchikus névtér _nem alapértelmezett Storage-_ fiókokkal használható.
  
+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): regisztrálja a betanítás során használt Docker-tárolókat, és amikor üzembe helyezi a modellt. A költségek csökkentése érdekében az ACR a telepítési lemezképek létrehozása előtt **betöltődik** .

+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): a modellekkel kapcsolatos figyelési információkat tárolja.

+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): a számítási célok és a munkaterület által igényelt egyéb bizalmas információk által használt titkokat tárolja.

> [!NOTE]
> Ehelyett használhatja a meglévő Azure-beli erőforrás-példányokat, amikor az [ARM-sablonnal](how-to-create-workspace-template.md)létrehozza a munkaterületet a [Python SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace), az [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)vagy a Azure Machine learning CLI használatával.

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Mi történt a Enterprise Edition kiadással

Szeptember 2020-ától az Enterprise Edition-munkaterületeken elérhető összes funkció már elérhető az alapszintű kiadás-munkaterületeken is. Az új vállalati munkaterületek már nem hozhatók létre.  A paramétert használó SDK-, CLI-vagy Azure Resource Manager-hívások `sku` továbbra is működni fognak, de alapszintű munkaterület lesz kiépítve.

A december 21-én kezdődően az Enterprise Edition-munkaterületek automatikusan az alapszintű kiadásra lesznek beállítva, amely ugyanazokkal a képességekkel rendelkezik. A folyamat során nem történik leállás. 2021. január 1-jén a nagyvállalati kiadás hivatalosan megszűnik. 

Mindkét kiadásban az ügyfelek felelősek a felhasznált Azure-erőforrások költségeiért, és nem kell fizetniük a Azure Machine Learningért járó további díjakért. További részletekért tekintse meg a [Azure Machine learning díjszabási oldalát](https://azure.microsoft.com/pricing/details/machine-learning/) .

## <a name="next-steps"></a>Következő lépések

A Azure Machine Learning megkezdéséhez tekintse meg a következőt:

+ [Azure Machine Learning áttekintése](overview-what-is-azure-ml.md)
+ [Munkaterület létrehozása és kezelése](how-to-manage-workspace.md)
+ [Oktatóanyag: a Azure Machine Learning első lépései a fejlesztési környezetben](tutorial-1st-experiment-sdk-setup-local.md)
+ [Oktatóanyag: az első ML-kísérlet létrehozása számítási példányon](tutorial-1st-experiment-sdk-setup.md)
+ [Oktatóanyag: Az első besorolási modell létrehozása automatizált gépi tanulással](tutorial-first-experiment-automated-ml.md) 
+ [Oktatóanyag: Autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)
