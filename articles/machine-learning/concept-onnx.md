---
title: 'ONNX: nagy teljesítményű, platformfüggetlen következtetés'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható a nyílt neurális hálózati Exchange (ONNX) a gépi tanulási modell következtetésének optimalizálásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: 5a1ed6c43e4d3a96e0e19d7be50e1e16bcab780e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98071238"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX és Azure Machine Learning: ML modellek létrehozása és felgyorsítása

Ismerje meg, hogyan használható a [nyílt neurális hálózati Exchange](https://onnx.ai) (ONNX) a gépi tanulási modell következtetésének optimalizálásához. A következtetés vagy a modell pontozása az a fázis, ahol az üzembe helyezett modellt az előrejelzéshez használják, leggyakrabban a termelési adatforgalomban. 

A gépi tanulási modellek a következtetésekhez való optimalizálása (vagy a modell pontozása) nehéz, mivel a modell és a következtetési könyvtár összehangolása szükséges a legtöbb hardveres funkció kihasználásához. A probléma rendkívül nehéz lesz, ha a különböző típusú platformokon (felhő/Edge, CPU/GPU stb.) optimális teljesítményt szeretne kapni, mivel mindegyik különböző képességekkel és jellemzőkkel rendelkezik. A bonyolultság akkor növekszik, ha számos különböző keretrendszerből származó modellekkel rendelkezik, amelyek számos különböző platformon futnak. Nagyon sok időt kell igénybe venni a keretrendszerek és a hardverek különböző kombinációinak optimalizálására. Egy olyan megoldás, amelyet egyszer kell betanítani az előnyben részesített keretrendszerben, és a felhőben vagy az Edge-ben bárhol futnia kell. Itt jön a ONNX.

A Microsoft és a partnerek egyik közössége nyílt szabványként hozta létre a gépi tanulási modelleket képviselő ONNX. A [számos keretrendszerből](https://onnx.ai/supported-tools) származó modellek, például a TensorFlow, a PyTorch, a SciKit, a kerasz, a chainer, a MXNet, a MATLAB és a SparkML, a szabványos ONNX formátumba exportálhatók vagy konvertálhatók. Ha a modellek ONNX formátumúak, különböző platformokon és eszközökön is futtathatók.

A [ONNX Runtime](https://onnxruntime.ai) egy nagy teljesítményű, a ONNX-modellek éles környezetben történő üzembe helyezéséhez használható. A felhő és a peremhálózat számára egyaránt optimalizált, és Linux, Windows és Mac rendszereken is működik. A C++ nyelven írt C, Python, C#, Java és JavaScript (Node.js) API-kat is tartalmaz különböző környezetekben való használathoz. Az ONNX Runtime támogatja mind a DNN, mind a hagyományos ML-modelleket, és integrálható a különböző hardveres gyorssegédekkel, például a TensorRT NVidia GPU-k, az Intel processzorok OpenVINO, a Windows DirectML és sok más szolgáltatással. A ONNX Runtime használatával kihasználhatja a nagy teljesítményű optimalizálási, tesztelési és folyamatos fejlesztéseket.

A ONNX futtatókörnyezet nagy léptékű Microsoft-szolgáltatásokban, például a Bing, az Office és az Azure Cognitive Servicesban használatos. A teljesítménnyel kapcsolatos nyereségek számos tényezőtől függenek, de ezek a Microsoft-szolgáltatások __átlagosan 2x teljesítménybeli nyereséget észleltek a CPU__-ban. A szolgáltatások Azure Machine Learningán kívül a ONNX Runtime más, Machine Learning munkaterheléseket támogató termékekben is fut, beleértve a következőket:
+ Windows: a futtatókörnyezet a Windows [Machine learning](/windows/ai/windows-ml/) részeként van beépítve, és több száz millió eszközön fut. 
+ Azure SQL termékcsalád: natív pontozás futtatása az [Azure SQL Edge](../azure-sql-edge/onnx-overview.md) -ben és az [Azure SQL felügyelt példányain](../azure-sql/managed-instance/machine-learning-services-overview.md).
+ ML.NET: [ONNX modellek futtatása a ml.net-ben](/dotnet/machine-learning/tutorials/object-detection-onnx).


[![ONNX-folyamatábra, amely bemutatja a képzést, a konvertereket és az üzembe helyezést](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX-modellek beolvasása

A ONNX-modelleket többféleképpen is beszerezheti:
+ Új ONNX-modell betanítása a Azure Machine Learningban (lásd a cikk alján található példákat) vagy az [automatizált Machine learning képességek](concept-automated-ml.md#automl--onnx) használatával
+ Meglévő modell átalakítása más formátumból ONNX (lásd az [oktatóanyagokat](https://github.com/onnx/tutorials)) 
+ Előre betanított ONNX-modell beszerzése a [ONNX Model Zoo](https://github.com/onnx/models) -ból
+ Testreszabott ONNX-modell létrehozása az [Azure Custom Vision szolgáltatásból](../cognitive-services/custom-vision-service/index.yml) 

Számos modell, beleértve a képbesorolást, az objektumok észlelését és a ONNX modellként is megjeleníthető. Ha olyan hibába ütközne, amely nem alakítható át sikeresen, akkor a megfelelő átalakítóból egy olyan problémát kell megadnia, amelyet Ön használt. A probléma megoldása előtt továbbra is használhatja a meglévő Format modellt.

## <a name="deploy-onnx-models-in-azure"></a>ONNX-modellek üzembe helyezése az Azure-ban

A Azure Machine Learning segítségével üzembe helyezheti, kezelheti és figyelheti a ONNX-modelljeit. A normál [üzembe helyezési munkafolyamat](concept-model-management-and-deployment.md) és a ONNX futtatókörnyezet használatával létrehozhat egy felhőben üzemeltetett Rest-végpontot. Tekintse meg a cikk végén található példa Jupyter jegyzetfüzeteket a kipróbáláshoz. 

### <a name="install-and-use-onnx-runtime-with-python"></a>A ONNX Runtime telepítése és használata a Python használatával

A ONNX Runtime Python-csomagjai a [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)) szolgáltatásban érhetők el. A telepítés előtt olvassa el a [rendszerkövetelményeket](https://github.com/Microsoft/onnxruntime#system-requirements) . 

 A Pythonhoz készült ONNX Runtime telepítéséhez használja az alábbi parancsok egyikét: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

A ONNX Runtime a Python-szkriptben való meghívásához használja a következőt:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

A modellt kísérő dokumentáció általában megadja a modell használatának bemeneteit és kimeneteit. A modell megjelenítéséhez olyan vizualizációs eszközt is használhat, mint például a [Netron](https://github.com/lutzroeder/Netron) . A ONNX Runtime lehetővé teszi a modell metaadatainak, bemenetének és kimenetének lekérdezését is:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

A modell kiszámításához használja `run` a és a pass értéket a visszaadni kívánt kimenetek listájában (hagyja üresen, ha az összeset meg szeretné jeleníteni) és a bemeneti értékek egy térképét. Az eredmény a kimenetek listája.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

A Python API teljes leírását a [ONNX futásidejű](https://aka.ms/onnxruntime-python)dokumentációjában találhatja meg.    

## <a name="examples"></a>Példák
Lásd: [útmutató – azureml/Deployment/Onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) , például Onnx-modellek létrehozásához és üzembe helyezéséhez használható Python-jegyzetfüzetek.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

A más nyelveken használatos minták a [ONNX Runtime githubon](https://github.com/microsoft/onnxruntime/tree/master/samples)találhatók.

## <a name="more-info"></a>További információ

További információ a **ONNX** vagy a projekthez való hozzájárulásról:
+ [ONNX projekt webhelye](https://onnx.ai)
+ [ONNX-kód a GitHubon](https://github.com/onnx/onnx)

További információ a **ONNX futtatókörnyezetről** vagy a projekthez való hozzájárulásról:
+ [ONNX futásidejű projekt webhelye](https://onnxruntime.ai)
+ [ONNX futásidejű GitHub-tárház](https://github.com/Microsoft/onnxruntime)