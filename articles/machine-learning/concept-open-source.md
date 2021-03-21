---
title: Nyílt forráskódú gépi tanulási integráció
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatók a nyílt forráskódú Python Machine learning-keretrendszerek a Azure Machine Learning teljes gépi tanulási megoldásainak betanításához, üzembe helyezéséhez és kezeléséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223074"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Nyílt forráskódú integráció Azure Machine Learning projektekkel

A Azure Machine Learning teljes gépi tanulási folyamatát kiszervezheti, üzembe helyezheti és kezelheti a nyílt forráskódú Python Machine learning-kódtárak és-platformok használatával.  A fejlesztői eszközök, például a Jupyter notebookok és a Visual Studio Code segítségével kihasználhatja a Azure Machine Learning meglévő modelljeit és parancsfájlait.  

Ebből a cikkből megtudhatja, hogyan tekintheti át ezeket a nyílt forráskódú kódtárakat és platformokat.

## <a name="train-open-source-machine-learning-models"></a>Nyílt forráskódú gépi tanulási modellek betanítása

A Machine learning-betanítási folyamat a feladatok elvégzéséhez vagy a probléma megoldásához szükséges algoritmusok alkalmazásával jár. A probléma függvényében különböző, a feladathoz és az adatokhoz legjobban illeszkedő algoritmusokat is választhat. A gépi tanulás különböző ágaival kapcsolatos további információkért tekintse meg a [Deep learning vs Machine learning című cikket](./concept-deep-learning-vs-machine-learning.md) , valamint a [Machine learning-algoritmust tartalmazó Cheat lapot](algorithm-cheat-sheet.md).

### <a name="preserve-data-privacy-using-differential-privacy"></a>Adatvédelem megőrzése differenciált adatvédelem használatával

A Machine learning-modellek betanításához szükség van az adatvédelemre. Előfordulhat, hogy az adatok érzékenyek, és fontos, hogy az adatok biztonságosak és magánjellegűek legyenek. A differenciális adatvédelem egy olyan technika, amellyel megőrizheti az adatkészletekben található információk titkosságát. További tudnivalókat az adatvédelem [megőrzéséről](concept-differential-privacy.md)szóló cikkben talál. 

A nyílt forráskódú különbözeti adatkezelési segédanyagok, például a [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) segítenek [megőrizni az adatok védelmét](how-to-differential-privacy.md) Azure Machine learning-megoldásokban.

### <a name="classical-machine-learning-scikit-learn"></a>Klasszikus gépi tanulás: scikit – Learn

A klasszikus gépi tanulási algoritmusokkal kapcsolatos olyan feladatokhoz, mint a besorolás, a fürtözés és a regresszió, használhat valami hasonlót, mint például a Scikit-Learn. A virág besorolási modell betanításának megismeréséhez tekintse meg a [hogyan kell betanítani a Scikit-Learn című cikket](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Neurális hálózatok: PyTorch, TensorFlow, kerasz

A "neurális hálózatok" néven ismert nyílt forráskódú gépi tanulási algoritmusok, amelyek a gépi tanulás egy részét képezik, hasznosak lehetnek a Azure Machine Learning mély tanulási modelljeinek betanításához.

A nyílt forráskódú mélyreható tanulási keretrendszerek és útmutatók a következők:

 *  [PyTorch](https://github.com/pytorch/pytorch): [mély tanulási képbesorolási modell betanítása az adatátviteli tanulással](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [kézzel írt számjegyek felismerése a TensorFlow használatával](how-to-train-tensorflow.md)
 *  [Kerasz](https://github.com/keras-team/keras): [neurális hálózat létrehozása a rendszerképek elemzéséhez a kerasz használatával](how-to-train-keras.md)

A mély tanulási modellek a semmiből való betanítása gyakran nagy mennyiségű időt, adatmennyiséget és számítási erőforrást igényel. A betanítási folyamat az átvitelsel való tanulással is felhasználható. Az átvitel a tanulás olyan technika, amely az egyik probléma megoldását egy másik, de kapcsolódó problémára is kiszolgálja. Ez azt jelenti, hogy egy meglévő modellt is igénybe vehet. A tanulással kapcsolatos további információkért tekintse meg a [Deep learning vs Machine learning című cikket](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) .

### <a name="reinforcement-learning-ray-rllib"></a>Megerősítő tanulás: Ray RLLib

A megerősítő tanulás olyan mesterséges intelligencia-módszer, amely a modelleket a műveletek, az állapotok és a jutalmak használatával hajtja végre: a megerősítő tanulási ügynökök olyan előre definiált műveletek készletét tanulják meg, amelyek a környezet aktuális állapota alapján maximalizálják a megadott díjakat. 

A [Ray RLLib](https://github.com/ray-project/ray) projekt olyan szolgáltatásokat tartalmaz, amelyek lehetővé teszik a nagy skálázhatóságot a betanítási folyamat során. Az iterációs folyamat egyszerre és erőforrás-igényes, mivel a megerősítő tanulási ügynökök megpróbálják megtanulni a feladatok megvalósításának optimális módját.  A Ray RLLib natív módon támogatja a Deep learning-keretrendszereket, például a TensorFlow és a PyTorch.  

Ha meg szeretné tudni, hogyan használható a Ray RLLib Azure Machine Learning használatával, tekintse meg a [megerősítő tanulási modell betanítása](how-to-use-reinforcement-learning.md)című témakört.

### <a name="monitor-model-performance-tensorboard"></a>Modell teljesítményének figyelése: TensorBoard

Egy vagy több modell betanításához a kívánt mérőszámok megjelenítésére és vizsgálatára van szükség, hogy a modell a várt módon legyen végrehajtva. A [TensorBoard Azure Machine learning segítségével nyomon követheti és megjelenítheti a kísérleti mérőszámokat](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>A értelmezhető és a tisztességes modellek keretrendszere

A gépi tanulási rendszerek a társadalom különböző területein, például a banki, az oktatási és az egészségügyi területeken vannak használatban. Ezért fontos, hogy ezek a rendszerek a nem szándékolt következmények elkerülése érdekében az előrejelzések és a javaslatok számára is legyenek felelősek.

Nyílt forráskódú keretrendszerek, például a [InterpretML](https://github.com/interpretml/interpret/) és a Fairlearn (a https://github.com/fairlearn/fairlearn) Azure Machine learning együttműködve átláthatóbbá és méltányos gépi tanulási modelleket hozhatnak létre.

A tisztességes és értelmezhető modellek létrehozásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Modellértelmezhetőség az automatizált gépi tanulásban](how-to-machine-learning-interpretability.md)
- [A gépi tanulási modellek értelmezése és magyarázata](how-to-machine-learning-interpretability-aml.md)
- [AutoML-modellek ismertetése](how-to-machine-learning-interpretability-automl.md)
- [A gépi tanulási modellek tisztaságának enyhítése](concept-fairness-ml.md)
- [A Azure Machine Learning használata az assets modellhez](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Modell üzembe helyezése

A modellek betanítása és előkészítése után ki kell választania a üzembe helyezésének módját. Azure Machine Learning különböző telepítési célokat biztosít. További információ: a [cikk helye és üzembe helyezése](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>Modell-formátumok szabványosítása a ONNX

A képzés után a modell tartalma, például a megismert paraméterek szerializálva és fájlba menthetők. Mindegyik keretrendszer saját szerializálási formátummal rendelkezik. A különböző keretrendszerek és eszközök használatakor az azt jelenti, hogy a keretrendszer követelményeinek megfelelően kell üzembe helyeznie a modelleket. A folyamat egységesítéséhez használhatja az Open neurális hálózati Exchange (ONNX) formátumot. A ONNX a mesterséges intelligencia modelljeinek nyílt forráskódú formátuma. A ONNX támogatja a keretrendszerek közötti együttműködési képességet. Ez azt jelenti, hogy a modelleket a számos népszerű Machine learning-keretrendszer (például a PyTorch) egyikében betaníthatja, ONNX formátumba alakíthatja át, és a ONNX modellt más, például ML.NET-keretrendszerben is használhatja.

A ONNX és a ONNX modellek használatáról további információt a következő cikkekben talál:

- [ML modellek létrehozása és felgyorsítása a ONNX](concept-onnx.md)
- [ONNX-modellek használata .NET-alkalmazásokban](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Modellek becsomagolása és üzembe helyezése tárolóként

A tároló-technológiák (például a Docker) a modellek webszolgáltatásként való üzembe helyezésének egyik módja. A tárolók olyan platformot és erőforrást biztosítanak, amely a reprodukálható szoftverek környezetének kialakításához és előkészítéséhez használható. Ezekkel az alapvető technológiákkal [előre konfigurált környezeteket](./how-to-use-environments.md), [előre konfigurált tároló-lemezképeket](./how-to-deploy-custom-docker-image.md) vagy egyéni gépeket használhat a gépi tanulási modellek, például a [Kubernetes-fürtök](./how-to-deploy-azure-kubernetes-service.md?tabs=python)üzembe helyezéséhez. A GPU-intenzív munkafolyamatok esetében olyan eszközöket használhat, mint például az NVIDIA Triton-kiszolgáló, hogy [előrejelzéseket készítsen a GPU](how-to-deploy-with-triton.md?tabs=python)-k használatával.

### <a name="secure-deployments-with-homomorphic-encryption"></a>Biztonságos központi telepítések homomorphic titkosítással

A központi telepítések biztonságossá tétele fontos részét képezi a telepítési folyamatnak. A [titkosított hivatkozó szolgáltatások üzembe helyezéséhez](how-to-homomorphic-encryption-seal.md)használja a `encrypted-inference` nyílt forráskódú Python-könyvtárat. A `encrypted inferencing` csomag a [Microsoft Seal](https://github.com/Microsoft/SEAL)(homomorphic encryption Library) alapján kötéseket biztosít.

## <a name="machine-learning-operations-mlops"></a>Machine Learning műveletek (MLOps)

A Machine Learning Operations (MLOps), amely a gépi tanuláshoz használt DevOps, lehetővé teszi, hogy átláthatóbbá, rugalmas és reprodukálható gépi tanulási munkafolyamatokat építsen ki. Tekintse meg a [Mi az MLOps című cikket](./concept-model-management-and-deployment.md) a MLOps-ről. 

A DevOps eljárások, például a folyamatos integráció (CI) és a folyamatos üzembe helyezés (CD) használatával automatizálhatja a teljes gépi tanulási életciklust, és megtalálhatja az irányítási adatai körét. Megadhatja a [gépi tanulás CI/CD-folyamatát a GitHub-műveletekben](./how-to-github-actions-machine-learning.md) Azure Machine learning képzési és üzembe helyezési feladatok futtatásához. 

A szoftveres függőségek, a metrikák, a metaadatok, az adatok és a modellek verziószámozása fontos részét képezi a MLOps folyamatnak, így átlátható, reprodukálható és naplózható folyamatok hozhatók létre. Ehhez a feladathoz használhatja [a MLFlow Azure Machine learning](how-to-use-mlflow.md) , valamint a [gépi tanulási modellek Azure Databricks](./how-to-use-mlflow-azure-databricks.md). A MLflow- [modelleket Azure-webszolgáltatásként is üzembe helyezheti](how-to-deploy-mlflow-models.md). 
