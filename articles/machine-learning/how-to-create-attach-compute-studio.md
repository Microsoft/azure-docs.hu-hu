---
title: Képzések létrehozása & számítások üzembe helyezése (Studio)
titleSuffix: Azure Machine Learning
description: A Studio használatával képzési és üzembe helyezési számítási erőforrásokat (számítási célokat) hozhat létre a gépi tanuláshoz
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: 4194c71823e1affde1dcae47fd7e64668b57c0cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149358"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Számítási célok létrehozása a modell betanításához és üzembe helyezéséhez Azure Machine Learning Studióban

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet számítási célokat az Azure Machine Studióban.  Számítási célokat a használatával is létrehozhat és kezelhet:

* Azure Machine Learning learning SDK vagy CLI bővítmény a Azure Machine Learning
  * [Számítási példány](how-to-create-manage-compute-instance.md)
  * [Számítási fürt](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes Service-fürt](how-to-create-attach-kubernetes.md)
  * [Egyéb számítási erőforrások](how-to-attach-compute-targets.md)
* A [vs Code bővítmény](how-to-manage-resources-vscode.md#compute-clusters) a Azure Machine Learninghoz.


## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes fiókot a virtuális gép létrehozásának megkezdése előtt. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma
* [Azure Machine learning munkaterület](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Mi a számítási cél? 

A Azure Machine Learning segítségével különböző erőforrásokra és környezetekre is betaníthatja a modellt, együttesen [__számítási célok__](concept-azure-machine-learning-architecture.md#compute-targets)néven. A számítási cél lehet egy helyi gép vagy egy felhőalapú erőforrás, például egy Azure Machine Learning számítási, Azure-HDInsight vagy távoli virtuális gép.  Emellett számítási célokat is létrehozhat a modell telepítéséhez a ["hol és hogyan kell üzembe helyezni a modelleket"](how-to-deploy-and-where.md)című cikkben leírtak szerint.

## <a name="view-compute-targets"></a><a id="portal-view"></a>Számítási célok megtekintése

A munkaterülethez tartozó összes számítási cél megjelenítéséhez kövesse az alábbi lépéseket:

1. Navigáljon [Azure Machine learning studióhoz](https://ml.azure.com).
 
1. A __kezelés__ területen válassza a __számítás__ lehetőséget.

1. A felső lapfülek kiválasztásával jelenítheti meg az egyes számítási célkitűzéseket.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Számítási célok listájának megtekintése":::

## <a name="create-compute-target"></a><a id="portal-create"></a>Számítási cél létrehozása

Az előző lépések végrehajtásával tekintheti meg a számítási célok listáját. Ezután az alábbi lépéseket követve hozhat létre számítási célt:

1. Válassza ki a létrehozni kívánt számítási típusnak megfelelő felső fület.

1. Ha nincsenek számítási célok, válassza a  **Létrehozás** lehetőséget a lap közepén.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Számítási cél létrehozása":::

1. Ha megjelenik a számítási erőforrások listája, válassza a **+ új** elemet a lista fölött.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Új kiválasztása":::


1. Töltse ki az űrlapot a számítási típushoz:

  * [Számítási példány](#compute-instance)
  * [Számítási fürtök](#amlcompute)
  * [Fürtök következtetése](#inference-clusters)
  * [Csatolt számítás](#attached-compute)

1. Válassza a __Létrehozás__ lehetőséget.

1. A létrehozási művelet állapotának megtekintéséhez válassza ki a listából a számítási célt:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Számítási állapot megtekintése egy listából":::


### <a name="compute-instance"></a>Számítási példány

A számítási példány létrehozásához kövesse a [fenti lépéseket](#portal-create) .  Ezután töltse ki az űrlapot a következőképpen:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Új számítási példány létrehozása":::


|Mező  |Leírás  |
|---------|---------|
|Számítási név     |  <li>A név megadása kötelező, és legfeljebb 3 – 24 karakter hosszúságú lehet.</li><li>Az érvényes karakterek a kis-és nagybetűk, a számjegyek és a karakterek  **-** .</li><li>A névnek betűvel kell kezdődnie</li><li>A névnek egyedinek kell lennie az Azure-régióban lévő összes számításban. Ha a választott név nem egyedi, akkor riasztás jelenik meg.</li><li>Ha **-**  a karakter használatban van, akkor a névben legalább egy betűt kell követnie.</li>     |
|Virtuális gép típusa |  Válassza a CPU vagy a GPU lehetőséget. Ez a típus nem módosítható a létrehozás után     |
|Virtuális gép mérete     |  A támogatott virtuálisgép-méretek a régióban korlátozottak lehetnek. Tekintse meg a [rendelkezésre állási listát](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|SSH-hozzáférés engedélyezése/letiltása     |   Az SSH-hozzáférés alapértelmezés szerint le van tiltva.  Az SSH-hozzáférés nem lehet. a létrehozás után módosult. Győződjön meg arról, hogy engedélyezi a hozzáférést, ha interaktívan szeretne hibakeresést végezni a [vs Code Remote](how-to-set-up-vs-code-remote.md) használatával   |
|Speciális beállítások     |  Választható. Virtuális hálózat konfigurálása. Válassza ki az **erőforráscsoportot**, a **virtuális hálózatot** és az **alhálózatot** , hogy létrehozza a számítási példányt egy Azure-Virtual Network (vnet) belül. További információ: a vnet vonatkozó [hálózati követelmények](./how-to-secure-training-vnet.md) .  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> Számítási fürtök

Hozzon létre egy vagy több csomópontos számítási fürtöt a képzéshez, a Batch-következtetésekhez vagy a megerősítő tanulási munkaterhelésekhez. A számítási fürt létrehozásához kövesse a [fenti lépéseket](#portal-create) .  Ezután töltse ki az űrlapot a következőképpen:


|Mező  |Leírás  |
|---------|---------|
|Számítási név     |  <li>A név megadása kötelező, és legfeljebb 3 – 24 karakter hosszúságú lehet.</li><li>Az érvényes karakterek a kis-és nagybetűk, a számjegyek és a karakterek  **-** .</li><li>A névnek betűvel kell kezdődnie</li><li>A névnek egyedinek kell lennie az Azure-régióban lévő összes számításban. Ha a választott név nem egyedi, akkor riasztás jelenik meg.</li><li>Ha **-**  a karakter használatban van, akkor a névben legalább egy betűt kell követnie.</li>     |
|Virtuális gép típusa |  Válassza a CPU vagy a GPU lehetőséget. Ez a típus nem módosítható a létrehozás után     |
|Virtuális gép prioritása | Válasszon **dedikált** vagy **alacsony prioritást**.  Az alacsony prioritású virtuális gépek olcsóbbak, de nem garantálják a számítási csomópontokat. Lehetséges, hogy a előzik.
|Virtuális gép mérete     |  A támogatott virtuálisgép-méretek a régióban korlátozottak lehetnek. Tekintse meg a [rendelkezésre állási listát](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Csomópontok minimális száma | A kiépíteni kívánt csomópontok minimális száma. Ha dedikált számú csomópontot szeretne használni, itt állítsa be ezt a számot. A minimális érték 0 értékre állításával pénzt takaríthat meg, így nem fog fizetni minden csomópontért, ha a fürt üresjáratban van. |
|Csomópontok maximális száma | A kiépíteni kívánt csomópontok maximális száma. A számítás a feladatok elküldésekor a csomópontok maximális száma alapján automatikusan méretezhető. |
|Speciális beállítások     |  Választható. Virtuális hálózat konfigurálása. Válassza ki az **erőforráscsoportot**, a **virtuális hálózatot** és az **alhálózatot** , hogy létrehozza a számítási példányt egy Azure-Virtual Network (vnet) belül. További információ: a vnet vonatkozó [hálózati követelmények](./how-to-secure-training-vnet.md) .   [Felügyelt identitásokat](#managed-identity) is csatolhat az erőforrásokhoz való hozzáférés biztosításához     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Felügyelt identitás beállítása

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

A fürt létrehozásakor vagy a számítási fürt részleteinek szerkesztésekor a **Speciális beállítások** lapon állítsa be **a felügyelt identitás hozzárendelése** beállítást, és adjon meg egy rendszer által hozzárendelt identitást vagy felhasználó által hozzárendelt identitást.

#### <a name="managed-identity-usage"></a>Felügyelt identitás használata

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>Fürtök következtetése

> [!IMPORTANT]
> Az Azure Kubernetes Service Azure Machine Learning több konfigurációs lehetőséggel rendelkezik. Bizonyos forgatókönyvek, például a hálózatkezelés, további telepítést és konfigurálást igényelnek. Az ak-nak az Azure ML-vel való használatával kapcsolatos további információkért lásd: [Azure Kubernetes Service-fürt létrehozása és csatolása](how-to-create-attach-kubernetes.md).

Hozzon létre vagy csatoljon egy Azure Kubernetes Service-(ak-) fürtöt a nagy méretű következtetésekhez. A [fenti lépések](#portal-create) segítségével hozza létre az AK-fürtöt.  Ezután töltse ki az űrlapot a következőképpen:


|Mező  |Leírás  |
|---------|---------|
|Számítási név     |  <li>A név megadása kötelező. A névnek 2 és 16 karakter közöttinek kell lennie. </li><li>Az érvényes karakterek a kis-és nagybetűk, a számjegyek és a karakterek  **-** .</li><li>A névnek betűvel kell kezdődnie</li><li>A névnek egyedinek kell lennie az Azure-régióban lévő összes számításban. Ha a választott név nem egyedi, akkor riasztás jelenik meg.</li><li>Ha **-**  a karakter használatban van, akkor a névben legalább egy betűt kell követnie.</li>     |
|Kubernetes Service | Válassza az **új létrehozása** lehetőséget, és töltse ki a többi űrlapot.  Vagy válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő AK-fürtöt az előfizetésből.
|Region |  Válassza ki azt a régiót, ahol a fürtöt létre kívánja hozni |
|Virtuális gép mérete     |  A támogatott virtuálisgép-méretek a régióban korlátozottak lehetnek. Tekintse meg a [rendelkezésre állási listát](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Fürt célja  | **Éles** vagy **fejlesztési-tesztelési** lehetőség kiválasztása |
|Csomópontok száma | A virtuális gép (vCPU) által megszorozott csomópontok száma nem lehet nagyobb, mint 12. |
| Hálózati konfiguráció | A **speciális** elemre kattintva hozhatja létre a számítást egy meglévő virtuális hálózaton belül. A virtuális hálózatban található AK-val kapcsolatos további információkért lásd: [hálózati elkülönítés a betanítás során, valamint a magánhálózati végpontokkal és a virtuális hálózatokkal való következtetés](./how-to-secure-inferencing-vnet.md). |
| SSL-konfiguráció engedélyezése | Ezzel a beállítással konfigurálhatja az SSL-tanúsítványt a számítási feladatokhoz |

### <a name="attached-compute"></a>Csatolt számítás

Az Azure Machine Learning munkaterületen kívül létrehozott számítási célok használatához csatolni kell őket. A számítási cél csatolása a munkaterület számára elérhetővé válik.  A **csatolt számítások** használatával számítási célt csatolhat a **betanításhoz**.  A **következtetési fürtök** használatával CSATOLHAT egy AK-fürtöt a **következtetésekhez**.

A [fenti lépések](#portal-create) végrehajtásával csatolhat egy számítási feladatokhoz.  Ezután töltse ki az űrlapot a következőképpen:

1. Adja meg a számítási cél nevét. 
1. Válassza ki a csatolni kívánt számítási típust. Nem minden számítási típus csatlakoztatható Azure Machine Learning studióból. A képzéshez jelenleg használható számítási típusok a következők:
    * Azure-beli virtuális gép (Data Science Virtual Machine csatlakoztatásához)
    * Azure Databricks (gépi tanulási folyamatokban való használatra)
    * Azure Data Lake Analytics (gépi tanulási folyamatokban való használatra)
    * Azure HDInsight

1. Töltse ki az űrlapot, és adja meg a szükséges tulajdonságok értékeit.

    > [!NOTE]
    > A Microsoft azt javasolja, hogy SSH-kulcsokat használjon, amelyek biztonságosabbak a jelszavaknál. A jelszavak ki vannak téve a találgatásos támadásoknak. Az SSH-kulcsok titkosítási aláírásokra támaszkodnak. Az Azure Virtual Machines használható SSH-kulcsok létrehozásával kapcsolatos információkért tekintse meg a következő dokumentumokat:
    >
    > * [SSH-kulcsok létrehozása és használata Linux vagy macOS rendszeren](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [SSH-kulcsok létrehozása és használata Windows rendszeren](../virtual-machines/linux/ssh-from-windows.md)

1. Válassza a __csatolás__ lehetőséget. 


## <a name="next-steps"></a>Következő lépések

A cél létrehozása és a munkaterülethez való csatolása után a [futtatási konfigurációban](how-to-set-up-training-targets.md) egy `ComputeTarget` objektummal használhatja azt:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* A számítási erőforrás segítségével [elküldheti a betanítási futtatást](how-to-set-up-training-targets.md).
* [Oktatóanyag: a betanítási modell](tutorial-train-models-with-aml.md) felügyelt számítási célt használ a modellek betanításához.
* Ismerje meg, hogy miként lehet [hatékonyan hangolni a hiperparaméterek beállítása](how-to-tune-hyperparameters.md) a jobb modellek létrehozásához.
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe a modelleket](how-to-deploy-and-where.md).
* [Azure Machine Learning használata az Azure Virtual Networks használatával](./how-to-network-security-overview.md)