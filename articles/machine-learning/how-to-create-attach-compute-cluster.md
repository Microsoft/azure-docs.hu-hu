---
title: Számítási fürtök létrehozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre számítási fürtöket a Azure Machine Learning munkaterületen. A számítási fürtöt számítási célként használhatja képzéshez vagy következtetésekhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 1e3549a6f5f4f9d7f6a6da574378c90c20e42dcf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169572"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Azure Machine Learning számítási fürt létrehozása

Megtudhatja, hogyan hozhat létre és kezelhet egy [számítási fürtöt](concept-compute-target.md#azure-machine-learning-compute-managed) a Azure Machine learning munkaterületen.

Azure Machine Learning számítási fürt használatával a felhőben lévő CPU-vagy GPU-számítási csomópontok fürtön keresztül terjesztheti ki a képzéseket vagy a Batch-alapú következtetéseket. A GPU-ket tartalmazó virtuálisgép-méretekkel kapcsolatos további információkért lásd: [GPU-optimalizált virtuális gépek méretei](../virtual-machines/sizes-gpu.md). 

Ebből a cikkből megtudhatja, hogyan:

* Számítási fürt létrehozása
* Csökkentse a számítási fürt költségeit
* [Felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) beállítása a fürthöz

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

* Az [Azure CLI-bővítmény Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [Azure Machine learning Python SDK](/python/api/overview/azure/ml/intro)-hoz vagy a [Azure Machine learning Visual Studio Code bővítményhez](tutorial-setup-vscode-extension.md).

## <a name="what-is-a-compute-cluster"></a>Mi az a számítási fürt?

Azure Machine Learning számítási fürt felügyelt számítási infrastruktúra, amely lehetővé teszi, hogy egyszerűen hozzon létre egy vagy több csomópontos számítási módszert. A számítás a munkaterület-régión belül jön létre olyan erőforrásként, amely a munkaterület más felhasználóival is megoszthatók. A számítási feladatok automatikusan méretezhetők, ha egy feladatot elküldenek, és egy Azure-Virtual Network helyezhetők el. A számítás egy tároló környezetbe kerül, és a modell függőségeit egy [Docker-tárolóban](https://www.docker.com/why-docker)csomagolja.

A számítási fürtök biztonságos módon futtathatják a feladatokat egy [virtuális hálózati környezetben](how-to-secure-training-vnet.md)anélkül, hogy a vállalatoknak SSH-portokat kellene megnyitnia. A feladatot egy tároló környezetben hajtja végre a rendszer, és a modell függőségeit egy Docker-tárolóban csomagolja. 

## <a name="limitations"></a>Korlátozások

* A jelen dokumentumban felsorolt forgatókönyvek némelyike __előzetesként__ van megjelölve. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül is elérhetők, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Jelenleg csak a fürtök létrehozását (és nem frissítését) támogatja a ARM-sablonok [ https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/workspaces/computes?tabs=json ] használatával. A számítások frissítéséhez javasoljuk, hogy az SDK-t, a CLI-t vagy az UX-t használja most.

* Azure Machine Learning a számítások alapértelmezett korlátai, például a lefoglalt magok száma. További információ: [Az Azure-erőforrások kezelése és kvóták igénylése](how-to-manage-quotas.md).

* Az Azure lehetővé teszi az erőforrások _zárolásának_ elhelyezését, így azok nem törölhetők vagy csak olvashatók. Ne __alkalmazzon erőforrás-zárolást a munkaterületet tartalmazó erőforráscsoporthoz__. Ha a munkaterületet tartalmazó erőforráscsoporthoz zárolást alkalmaz, az Azure ML számítási fürtök méretezési műveletei nem lesznek érvényesek. Az erőforrások zárolásával kapcsolatos további információkért lásd: [erőforrások zárolása a váratlan változások megelőzése érdekében](../azure-resource-manager/management/lock-resources.md).

> [!TIP]
> A fürtök általában legfeljebb 100 csomópontot tudnak méretezni, ha elegendő kvóta van a szükséges magok számához. Alapértelmezés szerint a fürtök a csomópontok közötti kommunikációt engedélyezik a fürt csomópontjai között, így például támogatják az MPI-feladatokat. A fürtöket azonban a csomópontok 1000S is méretezheti úgy, hogy egyszerűen [egy támogatási jegyet emelnek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)fel, és lehetővé teszi az előfizetés, a munkaterület vagy egy adott fürt számára a csomópontok közötti kommunikáció letiltását. 


## <a name="create"></a>Létrehozás

**Becsült idő**: körülbelül 5 perc.

Azure Machine Learning a számítások újra felhasználhatók a futtatások között. A számítás a munkaterület más felhasználóival is megoszthatók, és a futtatások között megmaradnak, automatikusan felfelé vagy lefelé skálázást, a csomópontok száma és a fürtön beállított max_nodes alapján. A min_nodes beállítás szabályozza a rendelkezésre álló minimális csomópontokat.

A számítási fürt létrehozásakor a dedikált magok régiónként, a virtuálisgép-család kvótája és a teljes regionális kvóta alapján, egységes és megosztott a Azure Machine Learning betanítása a számítási példányokra vonatkozó kvótával. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

A számítási műveletek nem a használat során nulla csomópontra vannak lebontva.   A dedikált virtuális gépek a feladatok igény szerinti futtatásához jönnek létre.
    
# <a name="python"></a>[Python](#tab/python)

Állandó Azure Machine Learning számítási erőforrás Pythonban való létrehozásához válassza a **vm_size** és **max_nodes** tulajdonságokat. A Azure Machine Learning ezután az intelligens alapértelmezett értékeket használja a többi tulajdonsághoz. 
    
* **vm_size**: a Azure Machine learning számítás által létrehozott csomópontok virtuálisgép-családja.
* **max_nodes**: azon csomópontok maximális száma, amelyeknek az autoskálázása Azure Machine learning számítási feladatok futtatásakor.


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Azure Machine Learning számítás létrehozásakor több speciális tulajdonság is konfigurálható. A tulajdonságok lehetővé teszik a rögzített méretű állandó fürt vagy az előfizetéshez tartozó meglévő Azure-Virtual Network létrehozását.  A részletekért tekintse meg a [AmlCompute osztályt](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) .


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

További információ: [az ml computetarget Create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

# <a name="studio"></a>[Studio](#tab/azure-studio)

A számítási fürtök a Studióban történő létrehozásával kapcsolatos információkért lásd: [számítási célok létrehozása a Azure Machine learning Studióban](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Csökkentse a számítási fürt költségeit

Dönthet úgy is, hogy [alacsony prioritású virtuális gépeket](concept-plan-manage-cost.md#low-pri-vm) használ a számítási feladatok egy részének vagy egészének futtatásához. Ezek a virtuális gépek nem rendelkeznek garantált rendelkezésre állással, és a használat közben előzik is lehetnek. Újra kell indítania egy előzik-feladatot. 

Egy alacsony prioritású virtuális gép megadásához használja a következő módszerek egyikét:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Állítsa be a `vm-priority` következőket:
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

A Studióban válassza az **alacsony prioritású** virtuális gép létrehozása lehetőséget.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Felügyelt identitás beállítása

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Felügyelt identitás konfigurálása a létesítési konfigurációban:  

    * Rendszerhez rendelt felügyelt identitás:
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        ```
    
    * Felhasználó által hozzárendelt felügyelt identitás:
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Felügyelt identitás hozzáadása meglévő számítási fürthöz 
    
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

Lásd: [felügyelt identitás beállítása a Studióban](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Felügyelt identitás használata

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Előfordulhat, hogy néhány felhasználó létrehozta Azure Machine Learning munkaterületét a Azure Portal, mielőtt a GA-kiadás nem tud AmlCompute létrehozni a munkaterületen. Felvehet egy támogatási kérést a szolgáltatásra, vagy létrehozhat egy új munkaterületet a portálon vagy az SDK-ban, hogy azonnal feloldja a zárolást.

Ha a Azure Machine Learning számítási fürt a csomópont állapotának átméretezése (0 – > 0) miatt megakadt, akkor ezt az Azure-erőforrás zárolása okozhatja.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Következő lépések

A számítási fürt használatával:

* [Betanítási Futtatás beküldése](how-to-set-up-training-targets.md) 
* [Futtassa a Batch-következtetést](./tutorial-pipeline-batch-scoring-classification.md).
