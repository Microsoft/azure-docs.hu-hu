---
title: Számítási fürtök létrehozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre számítási fürtöt a Azure Machine Learning munkaterületén. A számítási fürtöt betanításra vagy következtetésre használja számítási célként.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 2d23e073a43d61a501e93e0288f222ef26407744
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538238"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Számítási Azure Machine Learning létrehozása

Megtudhatja, hogyan hozhat létre és kezelhet számítási [fürtöt](concept-compute-target.md#azure-machine-learning-compute-managed) a Azure Machine Learning munkaterületén.

A számítási Azure Machine Learning betanítási vagy kötegelt következtetési folyamat a felhőben lévő CPU- vagy GPU-alapú számítási csomópontok fürtön való elosztására használható. A GPU-kat is magában foglaló virtuálisgép-méretekkel kapcsolatos további információkért lásd: [GPU-optimalizált virtuálisgép-méretek.](../virtual-machines/sizes-gpu.md) 

Ebből a cikkből megtudhatja, hogyan:

* Számítási fürt létrehozása
* A számítási fürt költségeinek csökkentésével
* Felügyelt identitás [beállítása](../active-directory/managed-identities-azure-resources/overview.md) a fürthöz

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információkért lásd: Create an Azure Machine Learning workspace (Munkaterület [Azure Machine Learning létrehozása).](how-to-manage-workspace.md)

* Az [Azure CLI-bővítmény Machine Learning szolgáltatáshoz,](reference-azure-machine-learning-cli.md) [a Azure Machine Learning Python SDK-hoz](/python/api/overview/azure/ml/intro)vagy a [Azure Machine Learning Visual Studio Code-bővítményhez.](tutorial-setup-vscode-extension.md)

* Ha a Python SDK-t használja, [állítsa be a fejlesztési környezetet egy munkaterülettel.](how-to-configure-environment.md)  A környezet beállítása után csatolja a munkaterülethez a Python-szkriptben:

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>Mi az a számítási fürt?

Azure Machine Learning számítási fürt egy felügyelt számítási infrastruktúra, amely lehetővé teszi egyetlen vagy több csomópontos számítás egyszerű létrehozását. A számítás a munkaterület régiójában jön létre olyan erőforrásként, amely megosztható a munkaterület más felhasználóival. A számítás automatikusan felméretez egy feladat beküldtekor, és egy Azure-beli virtuális Virtual Network. A számítás tárolóba telepített környezetben fut, és a modell függőségeit egy [Docker-tárolóba csomagolása.](https://www.docker.com/why-docker)

A számítási fürtök biztonságosan futtatnak feladatokat egy virtuális hálózati [környezetben](how-to-secure-training-vnet.md)anélkül, hogy a vállalatoknak SSH-portokat kell megnyitniuk. A feladat tárolóba telepített környezetben fut, és a modell függőségeit egy Docker-tárolóba csomagolása. 

## <a name="limitations"></a>Korlátozások

* A dokumentumban felsorolt forgatókönyvek némelyike előzetes verzióként van __megjelölve.__ Az előzetes verziójú funkciókat szolgáltatói szerződés nélkül biztosítjuk, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Jelenleg csak a fürtök ARM-sablonokon keresztüli létrehozását (és frissítését) támogatjuk [ https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/workspaces/computes?tabs=json ]. A számítás frissítéséhez jelenleg az SDK, a CLI vagy a felhasználói felület használatát javasoljuk.

* Azure Machine Learning Compute alapértelmezett korlátokkal rendelkezik, például a lefoglalható magok száma. További információ: [Azure-erőforrások kvótáinak kezelése és kérése.](how-to-manage-quotas.md)

* Az Azure-ral _zárolhatja_ az erőforrásokat, így azok nem törölhetők vagy csak olvashatók. __Ne alkalmazon erőforrás-zárolást a munkaterületet tartalmazó erőforráscsoportra.__ Ha zárolást alkalmaz a munkaterületet tartalmazó erőforráscsoportra, az megakadályozza az Azure ML számítási fürtök skálázásának műveleteit. További információ az erőforrások zárolásról: [Erőforrások zárolása a váratlan módosítások megelőzése érdekében.](../azure-resource-manager/management/lock-resources.md)

> [!TIP]
> A fürtök általában legfeljebb 100 csomópontot skáláznak fel, ha elegendő kvóta áll a szükséges magok számára. Alapértelmezés szerint a fürtök úgy vannak beállítva, hogy engedélyezve legyen a csomópontok közötti kommunikáció a fürt csomópontjai között az MPI-feladatok támogatásához például. A fürtök azonban több mint 1000 csomópontra skálázásával egyszerűen egy támogatási jegyet hoz [létre,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)és kérelmezheti, hogy engedélyezze az előfizetés, a munkaterület vagy egy adott fürt felsorolását a csomópontok közötti kommunikáció letiltására.


## <a name="create"></a>Létrehozás

**Becsült idő:** Körülbelül 5 perc.

Azure Machine Learning Compute újra felhasználható a futtatáskor. A számítás megosztható a munkaterület más felhasználóival, és megőrzheti őket a futtatás között, automatikusan fel- vagy leméretezheti a csomópontokat az elküldött futtatások száma és a fürtön beállított max_nodes alapján. A min_nodes beállítás szabályozza az elérhető minimális csomópontokat.

A virtuálisgép-családra és a számítási fürtök létrehozására vonatkozó teljes regionális kvóta régiónkénti dedikált magok egységesítettek és meg vannak osztva a Azure Machine Learning számításipéldány-kvótával. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

A számítás automatikusan nulla csomópontra skáláz, ha nincs használva.   Dedikált virtuális gépeket hoz létre a feladatok szükség szerint való futtatásához.
    
# <a name="python"></a>[Python](#tab/python)


Állandó számítási erőforrás pythonos Azure Machine Learning létrehozásához adja meg a számítási **vm_size** **és** max_nodes tulajdonságokat. Azure Machine Learning ezután intelligens alapértelmezett értékeket használ a többi tulajdonsághoz.
    
* **vm_size:** A Compute által létrehozott Azure Machine Learning család.
* **max_nodes:** Az automatikus skálázható csomópontok maximális száma, amíg a számítási feladat Azure Machine Learning történik.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

A Compute szolgáltatás létrehozásakor számos speciális tulajdonságot Azure Machine Learning konfigurálhat. A tulajdonságok segítségével rögzített méretű állandó fürtöt hozhat létre, vagy egy meglévő Azure-Virtual Network az előfizetésben.  További részleteket [az AmlCompute osztályban](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) talál.


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

További információ: [az ml computetarget create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

# <a name="studio"></a>[Studio](#tab/azure-studio)

A számítási fürtök studióban való létrehozásával kapcsolatos információkért lásd: Számítási célok létrehozása a [Azure Machine Learning stúdió.](how-to-create-attach-compute-studio.md#amlcompute)

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> A számítási fürt költségeinek csökkentésével

Dönthet úgy is, hogy [alacsony](concept-plan-manage-cost.md#low-pri-vm) prioritású virtuális gépeket használ néhány vagy az összes számítási feladat futtatásához. Ezek a virtuális gépek nem garantálják a rendelkezésre állást, és használat közben előfordulhat, hogy a rendszer előtagként van bevetve. Újra kell indítania egy előtagú feladatot. 

Alacsony prioritású virtuális gép megadásához használja az alábbi módszerek bármelyikét:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Állítsa be a `vm-priority` következőt:
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

A studióban válassza az **Alacsony prioritás** lehetőséget a virtuális gép létrehozásakor.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Felügyelt identitás beállítása

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Felügyelt identitás konfigurálása a kiépítési konfigurációban:  

    * Rendszer által hozzárendelt felügyelt identitás létrehozva egy nevű munkaterületen `ws`
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * Felhasználó által hozzárendelt felügyelt identitás létrehozva egy nevű munkaterületen `ws`
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Felügyelt identitás hozzáadása egy nevű meglévő számítási fürthöz `cpu_cluster`
    
    * Rendszer által hozzárendelt felügyelt identitás:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Felhasználó által hozzárendelt felügyelt identitás:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Új felügyelt számítási fürt létrehozása felügyelt identitással

  * Felhasználó által hozzárendelt felügyelt identitás

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Rendszer által hozzárendelt felügyelt identitás

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Felügyelt identitás hozzáadása meglévő fürthöz:

    * Felhasználó által hozzárendelt felügyelt identitás
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Rendszer által hozzárendelt felügyelt identitás

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Lásd: [Felügyelt identitás beállítása a Studióban.](how-to-create-attach-compute-studio.md#managed-identity)

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Felügyelt identitás használata

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Előfordulhat, hogy egyes felhasználók, akik az Azure Machine Learning-munkaterületüket az Azure Portal ga ga kiadása előtt hozták létre, nem biztos, hogy létre tudják hozni az AmlCompute-et ezen a munkaterületen. Támogatási kérést hozhat létre a szolgáltatáshoz, vagy létrehozhat egy új munkaterületet a portálon vagy az SDK-val, hogy azonnal feloldja a blokkolást.

Ha a Azure Machine Learning számítási fürt elakadt a csomópont állapotának átméretezésekor (0 –> 0), ezt az Azure-erőforrászárolás okozhatja.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Következő lépések

A számítási fürt a következőre használható:

* [Betanítás futtatása elküldése](how-to-set-up-training-targets.md) 
* [Futtassa a batch-következtetést.](./tutorial-pipeline-batch-scoring-classification.md)
