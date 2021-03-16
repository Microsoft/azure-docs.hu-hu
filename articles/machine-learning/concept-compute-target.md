---
title: Mik a számítási célok
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan jelölhet ki számítási erőforrást vagy környezetet a modell betanításához vagy üzembe helyezéséhez Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: b4422c664071087ccae73a5b6f642b53dcca74bf
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574645"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Mik a számítási célok az Azure Machine Learningben?

A *számítási cél* egy kijelölt számítási erőforrás vagy környezet, amelyben futtathatja a betanítási szkriptet, vagy üzemeltetheti a szolgáltatás központi telepítését. Ez a hely lehet a helyi számítógép vagy egy felhőalapú számítási erőforrás. A számítási célok használata megkönnyíti a számítási környezet későbbi módosítását anélkül, hogy módosítani kellene a kódot.

Egy tipikus modell-fejlesztési életciklus esetén a következőket teheti:

1. Első lépésként fejlesztheti és kísérletezheti kis mennyiségű adaton. Ebben a szakaszban a számítási célként a helyi környezetet, például a helyi számítógépet vagy a felhőalapú virtuális gépet (VM) használja.
1. Vertikális felskálázás nagyobb mennyiségű adatokra, vagy elosztott képzések elvégzése az alábbi [képzési számítási célok](#train)egyikével.
1. Miután a modell elkészült, üzembe helyezheti egy webüzemeltetési környezetben vagy IoT-eszközön az egyik ilyen [üzembe helyezési számítási célponttal](#deploy).

A számítási célokhoz használt számítási erőforrások egy [munkaterülethez](concept-workspace.md)vannak csatolva. A helyi gépen kívüli számítási erőforrásokat a munkaterület felhasználói osztják meg.

## <a name="training-compute-targets"></a><a name="train"></a> Számítási célok betanítása

A Azure Machine Learning különböző számítási célok esetében eltérő támogatással rendelkezik. Egy tipikus modell fejlesztési életciklusa kis mennyiségű adat fejlesztésével vagy kísérletezésével kezdődik. Ebben a szakaszban helyi környezetet, például helyi számítógépet vagy felhőalapú virtuális gépet használhat. Ha nagyobb mennyiségű adatkészletre vagy elosztott képzésre van szüksége, használja a Azure Machine Learning számítást, és hozzon létre egy önálló vagy több csomópontos fürtöt, amely minden egyes futtatásakor elküldi az egyes műveleteket. Saját számítási erőforrást is csatolhat, bár a különböző forgatókönyvek támogatása eltérő lehet.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

További információ a [betanítási műveletek számítási célra való beküldéséről](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Kiszámítási célok a következtetéshez

A modell központi telepítésének üzemeltetéséhez a következő számítási erőforrások használhatók.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

A következtetések elvégzése során a Azure Machine Learning létrehoz egy Docker-tárolót, amely a modell és a hozzájuk tartozó erőforrások használatához szükséges. Ezt a tárolót a rendszer a következő telepítési forgatókönyvek egyikében használja:

* A valós idejű következtetésekhez használt *webszolgáltatásként* . A webszolgáltatás központi telepítései a következő számítási célok egyikét használják:

    * [Helyi számítógép](how-to-attach-compute-targets.md#local)
    * [Azure Machine Learning számítási példány](how-to-create-manage-compute-instance.md)
    * [Azure Container Instances](how-to-attach-compute-targets.md#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Azure Functions (előzetes verzió). A functions szolgáltatásban való üzembe helyezés csak a Azure Machine Learningra támaszkodik a Docker-tároló felépítéséhez. Innen a functions használatával telepíthető. További információ: [Machine learning-modell üzembe helyezése Azure functions (előzetes verzió)](how-to-deploy-functions.md).

* Olyan _Batch-következtetési_ végpontként, amely a kötegek rendszeres feldolgozásához használatos. A Batch-következtetések [Azure Machine learning számítási fürtöket](how-to-create-attach-compute-cluster.md)használnak.

* Egy _IoT-eszközre_ (előzetes verzió). A IoT-eszközre történő központi telepítés csak az Azure Machine Learningra támaszkodik a Docker-tároló felépítéséhez. Innen a Azure IoT Edge használatával telepíthető. További információ: [telepítés IoT Edge modulként (előzetes verzió)](../iot-edge/tutorial-deploy-machine-learning.md).

Megtudhatja, [hol és hogyan helyezheti üzembe a modellt egy számítási célra](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning számítás (felügyelt)

A felügyelt számítási erőforrásokat Azure Machine Learning hozza létre és kezeli. Ez a számítás a gépi tanulási munkaterhelésekre van optimalizálva. Azure Machine Learning számítási fürtök és [számítási példányok](concept-compute-instance.md) az egyetlen felügyelt számítások.

A következő esetekben hozhat létre Azure Machine Learning számítási példányokat vagy számítási fürtöket:

* [Azure Machine learning Studio](how-to-create-attach-compute-studio.md).
* A Python SDK és parancssori felület:
    * [Számítási példány](how-to-create-manage-compute-instance.md).
    * [Számítási fürt](how-to-create-attach-compute-cluster.md).
* Az [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (előzetes verzió).
* Egy Azure Resource Manager sablon. Példa sablonra: [Azure Machine learning számítási fürt létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Gépi tanulási [bővítmény az Azure CLI-hez](reference-azure-machine-learning-cli.md#resource-management).

A létrehozáskor ezek a számítási erőforrások automatikusan a munkaterület részét képezik, más típusú számítási céloktól eltérően.


|Képesség  |Számítási fürt  |Számítási példány  |
|---------|---------|---------|
|Egy vagy több csomópontos fürt     |    **&check;**       |         |
|Minden alkalommal, amikor elküld egy futtatást     |     **&check;**      |         |
|Fürt automatikus kezelése és feladatütemezés     |   **&check;**        |     **&check;**      |
|A processzor-és a GPU-erőforrások támogatása     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Ha egy számítási *fürt* üresjáratban van, az autoskálázás 0 csomópontra történik, így nem kell fizetnie, ha nincs használatban. A számítási *példányok* mindig be vannak kapcsolva, és nem méretezhetők le. Ha nem használja, [állítsa le a számítási példányt](how-to-create-manage-compute-instance.md#manage) a többletköltség elkerülése érdekében.

### <a name="supported-vm-series-and-sizes"></a>Támogatott VM-sorozatok és-méretek

Ha Azure Machine Learning felügyelt számítási erőforráshoz kiválasztja a csomópont méretét, az Azure-ban elérhető virtuálisgép-méretek közül választhat. Az Azure számos méretet kínál a különböző számítási feladatokhoz használható Linux és Windows rendszerekhez. További információ: [VM-típusok és-méretek](../virtual-machines/sizes.md).

A virtuális gépek méretének kiválasztására néhány kivétel és korlátozás vonatkozik:

* Egyes virtuálisgép-sorozatok nem támogatottak Azure Machine Learningban.
* Néhány virtuálisgép-sorozat korlátozott. Ha korlátozott adatsorozatot szeretne használni, forduljon az ügyfélszolgálathoz, és igényeljen kvóta-növekedést az adatsorozathoz. Az ügyfélszolgálattal való kapcsolatfelvételsel kapcsolatos információkért lásd az [Azure támogatási lehetőségeit](https://azure.microsoft.com/support/options/).

A támogatott adatsorozatokkal és korlátozásokkal kapcsolatos további információkért tekintse meg a következő táblázatot.

| **Támogatott VM-sorozat**  | **Korlátozások** | **Kategória** | **Támogató** |
|------------|------------|------------|------------|
| T | Nincsenek. | Általános célú | Számítási fürtök és példány |
| DDSv4 | Nincsenek. | Általános célú | Számítási fürtök és példány |
| Dv2 | Nincsenek. | Általános célú | Számítási fürtök és példány |
| Dv3 | Nincsenek.| Általános célú | Számítási fürtök és példány |
| DSv2 | Nincsenek. | Általános célú | Számítási fürtök és példány |
| DSv3 | Nincsenek.| Általános célú | Számítási fürtök és példány |
| EAv4 | Nincsenek. | Memóriaoptimalizált | Számítási fürtök és példány |
| Ev3 | Nincsenek. | Memóriaoptimalizált | Számítási fürtök és példány |
| FSv2 | Nincsenek. | Számításra optimalizált | Számítási fürtök és példány |
| H | Nincsenek. | Nagy teljesítményű számítás | Számítási fürtök és példány |
| HB | Jóváhagyást igényel. | Nagy teljesítményű számítás | Számítási fürtök és példány |
| HBv2 | Jóváhagyást igényel. |  Nagy teljesítményű számítás | Számítási fürtök és példány |
| HCS FRISSÍTŐÜGYNÖK | Jóváhagyást igényel. |  Nagy teljesítményű számítás | Számítási fürtök és példány |
| M | Jóváhagyást igényel. | Memóriaoptimalizált | Számítási fürtök és példány |
| NC | Nincsenek. |  GPU | Számítási fürtök és példány |
| NC – promo | Nincsenek. | GPU | Számítási fürtök és példány |
| NCsv2 | Jóváhagyást igényel. | GPU | Számítási fürtök és példány |
| NCsv3 | Jóváhagyást igényel. | GPU | Számítási fürtök és példány |  
| NDs | Jóváhagyást igényel. | GPU | Számítási fürtök és példány | 
| NDv2 | Jóváhagyást igényel. | GPU | Számítási fürtök és példány | 
| NV | Nincsenek. | GPU | Számítási fürtök és példány | 
| NVv3 | Jóváhagyást igényel. | GPU | Számítási fürtök és példány | 


Habár a Azure Machine Learning támogatja ezeket a virtuálisgép-sorozatokat, előfordulhat, hogy az összes Azure-régióban nem érhetők el. Annak ellenőrzéséhez, hogy elérhetők-e a virtuálisgép-sorozatok, tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

> [!NOTE]
> A Azure Machine Learning nem támogatja az Azure-beli számítási műveletek által támogatott összes virtuálisgép-méretet. Az elérhető virtuálisgép-méretek listázásához használja az alábbi módszerek egyikét:
> * [REST API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable/2020-08-01/examples/ListVMSizesResult.json)
> * [Python SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
>

### <a name="compute-isolation"></a>Számítási elkülönítés

Azure Machine Learning a számítási egységek olyan virtuálisgép-méreteket biztosítanak, amelyek egy adott hardvereszközhöz vannak elkülönítve, és egyetlen ügyfélhez vannak hozzárendelve. Az elkülönített virtuálisgép-méretek olyan számítási feladatokhoz használhatók, amelyek nagy fokú elkülönítést igényelnek a többi ügyfél munkaterheléséhez olyan okokból, amelyek megfelelnek a megfelelőségi és szabályozási követelményeknek. Az elkülönített méret kihasználása garantálja, hogy a virtuális gép az adott kiszolgálópéldány esetében csak egy fut.

A jelenlegi elkülönített VM-ajánlatok a következők:

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3 *

*RDMA-kompatibilis

További információ az elkülönítésről: [elkülönítés az Azure nyilvános felhőben](../security/fundamentals/isolation-choices.md).

## <a name="unmanaged-compute"></a>Nem felügyelt számítás

A nem felügyelt számítási célt *nem* a Azure Machine learning felügyeli. Ezt a számítási célt a Azure Machine Learningon kívül hozza létre, majd csatolja a munkaterülethez. A nem felügyelt számítási erőforrások további lépéseket igényelhetnek a gépi tanulási feladatok teljesítményének fenntartása vagy javítása érdekében.

## <a name="next-steps"></a>Következő lépések

Az alábbiak végrehajtásának módját ismerheti meg:
* [Számítási cél használata a modell betanításához](how-to-set-up-training-targets.md)
* [Modell üzembe helyezése számítási célra](how-to-deploy-and-where.md)
