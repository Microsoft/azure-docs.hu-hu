---
title: '& Train models készítése'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan taníthatja ki a modelleket Azure Machine Learning használatával. Ismerkedjen meg a különböző képzési módszerekkel, és válassza ki a megfelelőt a projekthez.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 05/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: de3d9aa60322cc3e6e189f6f16c35d6f42c0cf61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102500410"
---
# <a name="train-models-with-azure-machine-learning"></a>Modellek betanítása Azure Machine Learning

A Azure Machine Learning számos lehetőséget kínál a modellek betanítására, az SDK-t használó, az SDK-val alacsony kódú megoldások, például az automatizált gépi tanulás és a vizuális tervező használatával. Az alábbi lista segítségével meghatározhatja, hogy melyik betanítási módszer legyen a legmegfelelőbb:

+ [Pythonhoz készült Azure Machine learning SDK](#python-sdk): a Python SDK számos módszert kínál a különböző képességekkel rendelkező modellek betanításához.

    | Betanítási módszer | Leírás |
    | ----- | ----- |
    | [Konfiguráció futtatása](#run-configuration) | A **modellek betanításának tipikus módja** egy képzési parancsfájl használata és a konfiguráció futtatása. A futtatási konfiguráció biztosítja a modell betanításához használt képzési környezet konfigurálásához szükséges információkat. Megadhatja a betanítási parancsfájlt, a számítási célt és az Azure ML-környezetet a futtatási konfigurációban, és futtathatja a betanítási feladatot. |
    | [Automatizált gépi tanulás](#automated-machine-learning) | Az automatizált gépi tanulás lehetővé teszi a **modellek széles körű adatelemzési és programozási ismeretek nélküli tanítását**. Az adatelemzési és-programozási hátterű felhasználók számára lehetővé teszi az idő és az erőforrások megtakarítását az algoritmus kiválasztásának és a hiperparaméter hangolásának automatizálásával. Az automatizált gépi tanulás használatakor nem kell aggódnia a futtatási konfiguráció definiálásával kapcsolatban. |
    | [Gépi tanulási folyamat](#machine-learning-pipeline) | A folyamatok nem egy másik betanítási módszer, hanem a **munkafolyamatok moduláris, újrafelhasználható lépésekkel való definiálásának módja**, amely magában foglalhatja a munkafolyamatok részét képező képzést is. A gépi tanulási folyamatokban az automatizált gépi tanulás és a konfiguráció futtatása a modellek betanításához. Mivel a folyamatok nem kifejezetten a képzésre összpontosítottak, a folyamat használatának okai a többi tanítási módszernél változatosabbek. Általában a következőket lehet használni:<br>* Olyan **felügyelet nélküli folyamatokat szeretne ütemezni** , mint például a hosszan futó betanítási feladatok vagy az adatok előkészítése.<br>* **Több olyan lépést** is használhat, amely heterogén számítási erőforrásokon és tárolási helyeken is össze van hangolva.<br>* Használja a folyamatot **újrafelhasználható sablonként** adott forgatókönyvekhez, például az újraképzéshez vagy a kötegelt pontozáshoz.<br>* A munkafolyamathoz tartozó **adatforrások, bemenetek és kimenetek nyomon követése és verziószáma** .<br>* A munkafolyamatot **különböző csapatok valósítják meg, amelyek egymástól függetlenül működnek**. A lépések ezután összekapcsolhatók egy folyamattal a munkafolyamat megvalósításához. |

+ [Azure Machine learning SDK for r (előzetes verzió)](#r-sdk-preview): az r-hez készült SDK a reticulate csomagot használja a Azure Machine learning Python SDK-hoz való kötéshez. Ez lehetővé teszi a Python SDK-ban megvalósított alapvető objektumok és módszerek elérését bármely R-környezetből.

+ **Tervező**: a Azure Machine learning Designer egyszerű belépési pontot biztosít a gépi tanuláshoz a fogalmak vagy a kis kódolási élményt biztosító felhasználók számára. Lehetővé teszi a modellek betanítását egy fogd és vidd webes KEZELŐFELÜLET használatával. A Python-kódokat használhatja a terv részeként, vagy betaníthatja a modelleket anélkül, hogy kódot kellene írnia.

+ **CLI**: a Machine learning parancssori felülete parancsokat biztosít a Azure Machine Learningekkel kapcsolatos gyakori feladatokhoz, és gyakran használják **parancsfájlok futtatására és automatizálására**. Ha például létrehozta a betanítási parancsfájlt vagy folyamatot, a CLI-vel elindíthatja a betanítást egy adott időpontban, vagy a betanításhoz használt adatfájlokat is frissíti. A betanítási modellek a betanítási feladatokat elküldő parancsokat biztosítanak. A futtatási konfigurációkkal vagy folyamatokkal küldhet feladatokat.

A képzési módszerek mindegyike különböző típusú számítási erőforrásokat használhat a betanításhoz. Ezeket az erőforrásokat együttesen [__számítási céloknak__](concept-azure-machine-learning-architecture.md#compute-targets)nevezzük. A számítási cél lehet egy helyi gép vagy egy felhőalapú erőforrás, például egy Azure Machine Learning számítási, Azure-HDInsight vagy távoli virtuális gép.

## <a name="python-sdk"></a>Python SDK

A Pythonhoz készült Azure Machine Learning SDK lehetővé teszi a gépi tanulási munkafolyamatok létrehozását és futtatását Azure Machine Learning használatával. Interaktív Python-munkamenetből, Jupyter-jegyzetfüzetből, Visual Studio Code-ból vagy más IDE-ből is használhatja a szolgáltatást.

* [Mi a Pythonhoz készült Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)
* [Az SDK telepítése vagy frissítése](/python/api/overview/azure/ml/install)
* [Fejlesztési környezet konfigurálása Azure Machine Learninghoz](how-to-configure-environment.md)

### <a name="run-configuration"></a>Konfiguráció futtatása

Azure Machine Learning használatával általános betanítási feladatok határozhatók meg a [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig). A szkript futtatási konfigurációját a rendszer a modellnek a számítási célra való betanítására szolgáló betanítási szkripttel együtt használja.

A futtatási konfigurációt a helyi számítógép esetében is elindíthatja, majd igény szerint átválthat egy felhőalapú számítási célra. A számítási cél módosításakor csak a használt futtatási konfigurációt kell módosítania. A Futtatás a betanítási feladatról is naplóz adatokat, például a bemeneteket, a kimeneteket és a naplókat.

* [Mi az a futtatási konfiguráció?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Oktatóanyag: az első ML-modell betanítása](tutorial-1st-experiment-sdk-train.md)
* [Példák: Jupyter Notebook és Python-példák a betanítási modellekre](https://github.com/Azure/azureml-examples)
* [Útmutató: képzések futtatásának konfigurálása](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatizált gépi tanulás

Adja meg az iterációkat, a hiperparaméter beállításait, a featurization és az egyéb beállításokat. A betanítás során Azure Machine Learning a különböző algoritmusokat és paramétereket párhuzamosan kísérli meg. A képzés leáll, ha eléri a megadott kilépési feltételeket.

> [!TIP]
> A Python SDK mellett a [Azure Machine learning Studióban](https://ml.azure.com)is használhatja az automatikus ml-t.

* [Mi az automatizált gépi tanulás?](concept-automated-ml.md)
* [Oktatóanyag: Az első besorolási modell létrehozása automatizált gépi tanulással](tutorial-first-experiment-automated-ml.md)
* [Oktatóanyag: Automatizált gépi tanulás használata a taxiutak árának előrejelzéséhez](tutorial-auto-train-models.md)
* [Példák: Jupyter Notebook a gépi tanulásra vonatkozó példák](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Útmutató: automatizált ML-kísérletek konfigurálása a Pythonban](how-to-configure-auto-train.md)
* [Útmutató: az idősorozat-előrejelzési modell alapképzése](how-to-auto-train-forecast.md)
* [Útmutató: gépi tanulási kísérletek létrehozása, feltárása és üzembe helyezése Azure Machine Learning Studióval](how-to-use-automated-ml-for-ml-models.md)

### <a name="machine-learning-pipeline"></a>Gépi tanulási folyamat

A gépi tanulási folyamatokban a korábban említett tanítási módszerek is használhatók. A folyamatok többet mutatnak a munkafolyamatok létrehozásáról, így többek között a modellek betanítását is magukban foglalják. Egy folyamat során a modelleket automatizált gépi tanulás vagy futtatási konfigurációk használatával taníthatja be.

* [Mik a Azure Machine Learning ML-folyamatok?](concept-ml-pipelines.md)
* [Gépi tanulási folyamatokat hozhat létre és futtathat Azure Machine Learning SDK-val](./how-to-create-machine-learning-pipelines.md)
* [Oktatóanyag: Azure Machine Learning-folyamatok használata a Batch pontozáshoz](tutorial-pipeline-batch-scoring-classification.md)
* [Példák: példák a gépi tanulási folyamatokra Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Példák: folyamat automatikus gépi tanulással](https://aka.ms/pl-automl)

### <a name="understand-what-happens-when-you-submit-a-training-job"></a>Megtudhatja, mi történik a betanítási feladatok elküldésekor

Az Azure betanítási életciklusa a következőkből áll:

1. A Project mappában lévő fájlok tömörítése, figyelmen kívül hagyva a _. amlignore_ vagy _. gitignore_ fájlban megadott fájlokat.
1. A számítási fürt méretezése 
1. A Docker kiépítése vagy letöltése a számítási csomópontra 
    1. A rendszer a következőképpen számítja ki a kivonatot: 
        - A kiinduló rendszerkép 
        - Egyéni Docker-lépések (lásd: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](./how-to-deploy-custom-docker-image.md))
        - A Conda-definíció YAML (lásd: [létrehozás & a szoftveres környezetek használata Azure Machine learning](./how-to-use-environments.md))
    1. A rendszer ezt a kivonatot használja a munkaterület Azure Container Registry (ACR) keresésének kulcsaként.
    1. Ha nem található, akkor a globális ACR-beli egyezést keresi
    1. Ha nem található, a rendszer létrehoz egy új rendszerképet (amely gyorsítótárazza és regisztrálva lesz a munkaterület ACR-ben)
1. A tömörített projektfájl letöltése a számítási csomóponton lévő ideiglenes tárhelyre
1. Projektfájl kicsomagolása
1. A számítási csomópont végrehajtása `python <entry script> <arguments>`
1. A `./outputs` munkaterülethez társított Storage-fiókba írt naplók, modellező fájlok és egyéb fájlok mentése
1. A számítási felskálázás, beleértve az ideiglenes tárolók eltávolítását is 

Ha úgy dönt, hogy a helyi gépen ("konfigurálás helyi futtatásként") van betanítva, nem szükséges a Docker használata. Ha úgy dönt, hogy helyileg használja a Docker-t (lásd a következő szakaszt: [ml folyamat konfigurálása](./how-to-debug-pipelines.md) példaként).

## <a name="r-sdk-preview"></a>R SDK (előzetes verzió)

Az R SDK lehetővé teszi az R nyelv használatát a Azure Machine Learning. Az SDK a reticulate csomagot használja a Azure Machine Learning Python SDK-hoz való kötéshez. Ez hozzáférést biztosít a Python SDK-ban megvalósított alapvető objektumokhoz és módszerekhez bármilyen R-környezetből.

További információért tekintse át a következő cikkeket:

* [Az R-hez készült Azure Machine Learning SDK-referenciák](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

A Designer lehetővé teszi a modellek betanítását egy fogd és vidd felületen a böngészőben.

+ [Mi a tervező?](concept-designer.md)
+ [Oktatóanyag: autó árának előrejelzése](tutorial-designer-automobile-price-train-score.md)

## <a name="many-models-solution-accelerator"></a>Számos modell megoldás-gyorsító

A [számos Modelles megoldás-gyorsító](https://aka.ms/many-models) (előzetes verzió) a Azure Machine Learningra épít, és lehetővé teszi több száz vagy akár több ezer gépi tanulási modell betanítását, üzemeltetését és felügyeletét.

Például az __egyes példányok vagy egyedi__ modellek létrehozása a következő helyzetekben lehet jobb eredményekhez vezethet:

* Az egyes tárolók értékesítésének előrejelzése
* Prediktív karbantartás több száz olajos kutak esetében
* Egyéni felhasználói élmény testreszabása.

További információkért lásd a GitHubon a [különböző modellek megoldás-gyorsító](https://aka.ms/many-models) című témakört.

## <a name="cli"></a>parancssori felület

A Machine learning parancssori felület egy bővítmény az Azure CLI-hez. Platformfüggetlen CLI-parancsokat biztosít a Azure Machine Learning használatához. Általában a CLI segítségével automatizálhatja a feladatokat, például a gépi tanulási modellek betanítását.

* [A CLI-bővítmény használata Azure Machine Learning](reference-azure-machine-learning-cli.md)
* [MLOps az Azure-ban](https://github.com/microsoft/MLOps)

## <a name="vs-code"></a>VS Code

A VS Code bővítmény használatával futtathatja és kezelheti a betanítási feladatokat. További információt a [vs Code erőforrás-kezelési útmutatója](how-to-manage-resources-vscode.md#experiments) című témakörben talál.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [konfigurálhat egy tanítási futtatást](how-to-set-up-training-targets.md).