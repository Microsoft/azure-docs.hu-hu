---
title: Mi az a munkaterület?
titleSuffix: Azure Machine Learning
description: A munkaterület a munkaterület legfelső szintű erőforrása a Azure Machine Learning. Megőrzi az összes betanítás előzményeit, naplókat, metrikákat, kimenetet és a szkriptek pillanatképét.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2020
ms.openlocfilehash: 215da0e38045a2e66a4a11b54204c26e7720815c
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719060"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Mi az az Azure Machine Learning-munkaterület?

A munkaterület a munkaterület legfelső szintű erőforrása a Azure Machine Learning, így központosított helyet biztosít a munkaterület használata során létrehozott Azure Machine Learning.  A munkaterület megőrzi az összes betanítás előzményeit, beleértve a naplókat, a metrikákat, a kimenetet és a szkriptek pillanatképét. Ezzel az információval meghatározhatja, hogy melyik betanítás futtatja a legjobb modellt.  

Ha már van egy modell, amit szeretne, regisztrálja azt a munkaterületen. Ezután a regisztrált modell- és pontozási szkriptek használatával üzembe helyezheti az Azure Container Instances, Azure Kubernetes Service vagy egy helyszínen programozható kaputömböt (FPGA) REST-alapú HTTP-végpontként. A modellt modulként egy Azure IoT Edge eszközre is üzembe helyezheti.

## <a name="taxonomy"></a>Taxonómia 

A munkaterület taxonómiát az alábbi ábra szemlélteti:

[![Munkaterület-taxonónia](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Az ábrán a munkaterület következő összetevői láthatóak:

+ A munkaterületek a Azure Machine Learning [futtatásához](concept-compute-instance.md)szükséges Python-környezettel konfigurált számítási példányokat és felhőerőforrásokat Azure Machine Learning.

+ [A felhasználói szerepkörökkel](how-to-assign-roles.md) megoszthatja munkaterületét más felhasználókkal, csapatokkal vagy projektekkel.
+ [A számítási célok](concept-azure-machine-learning-architecture.md#compute-targets) a kísérletek futtatására használhatók.
+ A munkaterület létrehozásakor a társított [erőforrások](#resources) is létrejönnek.
+ [A](concept-azure-machine-learning-architecture.md#experiments) kísérletek betanítás futtatásokat tartalmaznak a modellek felépítéséhez.  
+ [A folyamatok](concept-azure-machine-learning-architecture.md#ml-pipelines) újrahasználható munkafolyamatok a modell betanítása és újraképezése érdekében.
+ [Az adatkészletek](concept-azure-machine-learning-architecture.md#datasets-and-datastores) segítik a modell betanítása és a folyamat létrehozása során használt adatok kezelését.
+ Miután létrehozott egy modellt, amit üzembe szeretne helyezni, létre kell hoznia egy regisztrált modellt.
+ A regisztrált modell és egy pontozószkent használatával hozzon létre egy üzembe [helyezési végpontot.](concept-azure-machine-learning-architecture.md#endpoints)

## <a name="tools-for-workspace-interaction"></a>Eszközök a munkaterületek közötti interakcióhoz

A munkaterületet a következő módokon használhatja:

> [!IMPORTANT]
> Az alább megjelölt (előzetes verzió) eszközök jelenleg nyilvános előzetes verzióban állnak rendelkezésre.
> Az előzetes verzió szolgáltatói szerződés nélkül érhető el, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+ A weben:
    + [Azure Machine Learning stúdió ](https://ml.azure.com) 
    + [Azure Machine Learning Designer](concept-designer.md) 
+ Bármely Python-környezetben a [pythonhoz készült Azure Machine Learning SDK-val.](/python/api/overview/azure/ml/intro)
+ Bármely R-környezetben, amely Azure Machine Learning [SDK for R (előzetes verzió)](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ A parancssorban a parancssori felület Azure Machine Learning [használatával](./reference-azure-machine-learning-cli.md)
+ [Azure Machine Learning VS Code-bővítmény](how-to-manage-resources-vscode.md#workspaces)


## <a name="machine-learning-with-a-workspace"></a>Gépi tanulás munkaterülettel

A gépi tanulási feladatok a munkaterületre olvasnak és/vagy írnak munkatermékeket.

+ Kísérlet futtatása modell betanításhoz – a kísérlet futtatás eredményeit írja a munkaterületre.
+ Automatizált gépi tanulási folyamat használata modell betanításhoz – a betanítás eredményeit írja a munkaterületre.
+ Modell regisztrálása a munkaterületen.
+ Modell üzembe helyezése – a regisztrált modellel hoz létre üzembe helyezést.
+ Újrahasználható munkafolyamatok létrehozása és futtatása.
+ Gépi tanulási összetevők, például kísérletek, folyamatok, modellek, üzemelő példányok megtekintése.
+ Modellek nyomon követése és figyelése.

## <a name="workspace-management"></a>Munkaterület-kezelés

A következő munkaterület-felügyeleti feladatokat is elvégezheti:

| Munkaterület-kezelési feladat   | Portál              | Studio | Python SDK / R SDK       | parancssori felület        | VS Code
|---------------------------|---------|---------|------------|------------|------------|
| Munkaterület létrehozása        | **&check;**     | | **&check;** | **&check;** | **&check;** |
| Munkaterület hozzáférésének kezelése    | **&check;**   || |  **&check;**    ||
| Számítási erőforrások létrehozása és kezelése    | **&check;**   | **&check;** | **&check;** |  **&check;**   ||
| Notebook virtuális gép létrehozása |   | **&check;** | |     ||

> [!WARNING]
> A munkaterület Azure Machine Learning előfizetésbe vagy a tulajdonában lévő előfizetés új bérlőre való áthelyezése nem támogatott. Ez hibákat okozhat.

## <a name="create-a-workspace"></a><a name='create-workspace'></a> Munkaterület létrehozása

A munkaterületek többféleképpen is létrehozhatóak:  

* Az [Azure Portal](how-to-manage-workspace.md?tabs=azure-portal#create-a-workspace) kattintással használható felülethez használhatja, hogy végigveszi az egyes lépéseket.
* A [Azure Machine Learning SDK for Python használatával](how-to-manage-workspace.md?tabs=python#create-a-workspace) használat közben hozhat létre munkaterületet Python-szkriptek vagy Jupyter-notebookok használatával
* A Azure Resource Manager [sablont](how-to-create-workspace-template.md) vagy a [Azure Machine Learning CLI-t](reference-azure-machine-learning-cli.md) akkor használhatja, ha automatizálni vagy testre kell szabni a létrehozást a vállalati biztonsági szabványoknak megfelelően.
* Ha a Visual Studio Code-ban dolgozik, használja a [VS Code-bővítményt.](how-to-manage-resources-vscode.md#create-a-workspace)

> [!NOTE]
> A munkaterület neve nem érzékeny a kis- és a kis- és a kis- és a nagy- és a kis- és a nagy

## <a name="associated-resources"></a><a name="resources"></a> Társított erőforrások

Amikor létrehoz egy új munkaterületet, az automatikusan több, a munkaterület által használt Azure-erőforrást is létrehoz:

+ [Azure Storage-fiók:](https://azure.microsoft.com/services/storage/)A munkaterület alapértelmezett adattárolója.  A számítási példányokkal használt Jupyter Azure Machine Learning is itt vannak tárolva. 
  
  > [!IMPORTANT]
  > Alapértelmezés szerint a tárfiók egy általános célú v1-fiók. Ezt a munkaterület létrehozása után általános [célú v2-re](../storage/common/storage-account-upgrade.md) frissítheti. Az általános célú v2 verzióra való frissítés után ne engedélyezze a hierarchikus névteret a tárfiókon.

  Meglévő Azure Storage-fiók használata esetén az nem lehet BlobStorage vagy prémium szintű fiók (Premium_LRS és Premium_GRS). Emellett nem lehet hierarchikus névtér (amely a Azure Data Lake Storage Gen2). A munkaterület alapértelmezett tárfiókja nem támogatja  sem a prémium szintű tárolókat, sem a hierarchikus névtereket. Használhat prémium szintű tárolót vagy hierarchikus névteret nem _alapértelmezett tárfiókokkal._
  
+ [Azure Container Registry:](https://azure.microsoft.com/services/container-registry/)Regisztrálja a betanítás során és a modell üzembe helyezésekor használt Docker-tárolókat. A költségek minimalizálása érdekében az ACR **az** üzembe helyezési lemezképek létrehozása előtt töltődik be.

+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)A modellek monitorozási információit tárolja.

+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)A számítási célok által használt titkos adatokat és a munkaterülethez szükséges egyéb bizalmas adatokat tárolja.

> [!NOTE]
> Ehelyett használhatja a meglévő Azure-erőforráspéldányokat, ha a [munkaterületet](how-to-manage-workspace.md?tabs=python#create-a-workspace)a Python SDK-val, az R [SDK-val](https://azure.github.io/azureml-sdk-for-r/reference/create_workspace.html)vagy a Azure Machine Learning CLI-val hozza létre egy [ARM-sablon használatával.](how-to-create-workspace-template.md)

<a name="wheres-enterprise"></a>

## <a name="what-happened-to-enterprise-edition"></a>Mi történt az Enterprise kiadással?

2020 szeptemberében az Enterprise edition munkaterületeken elérhető összes képesség mostantól az Alapszintű kiadású munkaterületeken is elérhető. Az új nagyvállalati munkaterületek már nem lesznek létrehozva.  A paramétert Azure Resource Manager SDK-, CLI- vagy Azure Resource Manager hívások továbbra is működni `sku` fognak, de egy Alapszintű munkaterület lesz kiépítve.

December 21-től az összes Enterprise kiadás alapszintű kiadásra lesz beállítva, amely ugyanazokkal a képességekkel rendelkezik. A folyamat során nem történik állásidő. 2021. január 1-től a Enterprise kiadás hivatalosan kivezetjük. 

Mindkét kiadásban az ügyfelek felelnek a felhasznált Azure-erőforrások költségeiért, és nem kell további díjakat fizetniük a Azure Machine Learning. További részleteket [az Azure Machine Learning díjszabását](https://azure.microsoft.com/pricing/details/machine-learning/) tartalmazó oldalon talál.

## <a name="next-steps"></a>Következő lépések

A további Azure Machine Learning lásd:

+ [Azure Machine Learning áttekintés](overview-what-is-azure-ml.md)
+ [Munkaterület létrehozása és kezelése](how-to-manage-workspace.md)
+ [Oktatóanyag: Az Azure Machine Learning első lépések a fejlesztési környezetben](tutorial-1st-experiment-sdk-setup-local.md)
+ [Oktatóanyag: Első lépések az első gépi tanulási kísérlet létrehozásához egy számítási példányon](tutorial-1st-experiment-sdk-setup.md)
+ [Oktatóanyag: Az első besorolási modell létrehozása automatizált gépi tanulással](tutorial-first-experiment-automated-ml.md) 
+ [Oktatóanyag: Autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)
