---
title: Számítási példány létrehozása és kezelése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és kezelhet Azure Machine Learning számítási példányt. Fejlesztési környezetként vagy fejlesztési/tesztelési célokra számítási célként használható.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 5ac525ae062efca25601c9e63a5c8f16f2be29be
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861222"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Számítási példány létrehozása Azure Machine Learning kezelése

Megtudhatja, hogyan hozhat létre és kezelhet számítási példányt [a](concept-compute-instance.md) Azure Machine Learning munkaterületén.

Számítási példány használata teljes körűen konfigurált és felügyelt fejlesztési környezetként a felhőben. Fejlesztési és tesztelési célokra a példányt [](concept-compute-target.md#train) betanítás számítási célként vagy következtetési [célként is használhatja.](concept-compute-target.md#deploy)   Egy számítási példány több feladatot is futtathat párhuzamosan, és feladat-várólistával rendelkezik. Fejlesztési környezetként a számítási példányok nem megoszthatók a munkaterület más felhasználóival.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Új számítási példány létrehozása 
* Számítási példány kezelése (indítás, leállítás, újraindítás, törlés)
* A terminálablak elérése 
* R- vagy Python-csomagok telepítése
* Új környezetek vagy Jupyter-kernelek létrehozása

A számítási példányok biztonságosan futtatnak feladatokat egy virtuális hálózati [környezetben](how-to-secure-training-vnet.md)anélkül, hogy a vállalatoknak SSH-portokat kell megnyitniuk. A feladat tárolóba telepített környezetben fut, és a modell függőségeit egy Docker-tárolóba csomagolása. 

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md)(Munkaterület Azure Machine Learning létrehozása).

* Az [Azure CLI-bővítmény Machine Learning szolgáltatáshoz,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK-hoz](/python/api/overview/azure/ml/intro)vagy a [Azure Machine Learning Visual Studio Code-bővítményhez.](tutorial-setup-vscode-extension.md)

## <a name="create"></a>Létrehozás

**Becsült idő:** Körülbelül 5 perc.

A számítási példányok létrehozása a munkaterület egy egyszeres folyamata. A számítást felhasználhatja fejlesztői munkaállomásként vagy betanítási célként is. A munkaterülethez több számítási példány is csatolható.

A virtuálisgép-családra és a számítási példány létrehozására vonatkozó teljes regionális kvóta régiónkénti dedikált magok egységesek és meg vannak osztva Azure Machine Learning számítási fürtkvóta betanítása során. A számítási példány leállítása nem ad ki kvótát, hogy újra tudja-e indítani a számítási példányt. Vegye figyelembe, hogy a virtuális gép mérete a létrehozás után nem változtatható meg.

Az alábbi példa bemutatja, hogyan hozhat létre számítási példányt:

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

A példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő referenciadokumentumokat:

* [ComputeInstance osztály](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

További információkért tekintse meg az [az ml computetarget create computeinstance referenciát.](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_computeinstance)

# <a name="studio"></a>[Studio](#tab/azure-studio)

A munkaterületen a Azure Machine Learning stúdió hozzon létre egy új  számítási példányt a Számítás vagy a **Jegyzetfüzetek** szakaszban, amikor készen áll az egyik jegyzetfüzet futtatására.

További információ számítási példány létrehozásáról a Studióban: Számítási célok létrehozása a [Azure Machine Learning stúdió.](how-to-create-attach-compute-studio.md#compute-instance)

---

Számítási példányt is létrehozhat egy Azure Resource Manager [sablonnal.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance) 

### <a name="create-on-behalf-of-preview"></a>Létrehozás a nevében (előzetes verzió)

Rendszergazdaként létrehozhat egy számítási példányt egy adattudós nevében, és a következővel rendelheti hozzá a példányt:
* [Azure Resource Manager sablon.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)  A sablonban szükséges TenantID és ObjectID megkereső részleteiért lásd: Identitásobjektum azonosítójának megkeresása [a hitelesítési konfigurációhoz.](../healthcare-apis/fhir/find-identity-object-ids.md)  Ezeket az értékeket a portálon is Azure Active Directory találhatja.
* REST API

Az adattudósnak, aki számára létrehozza a számítási példányt, a következőkre van szüksége: [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../role-based-access-control/overview.md) engedélyek: 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

Az adattudós elindíthatja, leállíthatja és újraindíthatja a számítási példányt. A számítási példányt a következőre használhatja:
* Jupyter
* JupyterLab
* RStudio
* Integrált jegyzetfüzetek

## <a name="manage"></a>Kezelés

Számítási példányok elindítása, leállítása, újraindítása és törlése. A számítási példányok nem skálázódnak le automatikusan, ezért mindenképpen állítsa le az erőforrást a folyamatos költségek elkerülése érdekében.

> [!TIP]
> A számítási példány 120 GB-os operációsrendszer-lemezzel rendelkezik. Ha elfogy a [lemezterület,](how-to-access-terminal.md) a terminállal törölje legalább 1–2 GB-ot, mielőtt leállítja vagy újraindítja a számítási példányt.

# <a name="python"></a>[Python](#tab/python)

Az alábbi példákban a számítási példány neve **példány**

* Állapot lekérte

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Leállítás

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Indítás

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Újraindítás

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Törlés

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi példákban a számítási példány neve **példány**

* Leállítás

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    További információ: [az ml computetarget stop computeinstance.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop)

* Indítás 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    További információ: [az ml computetarget start computeinstance.](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_start)

* Újraindítás 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    További információ: [az ml computetarget restart computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart).

* Törlés

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    További információ: [az ml computetarget delete computeinstance.](/cli/azure/ml/computetarget#az_ml_computetarget_delete)

# <a name="studio"></a>[Studio](#tab/azure-studio)

A munkaterületen a Azure Machine Learning stúdió válassza a **Számítás,** majd a felül **található Számítási** példány lehetőséget.

![Számítási példány kezelése](./media/concept-compute-instance/manage-compute-instance.png)

A következő műveleteket végezheti el:

* Új számítási példány létrehozása 
* Frissítse a Számítási példányok lapot.
* Számítási példányok elindítása, leállítása és újraindítása.  A példányért mindig fizetnie kell, amikor fut. Állítsa le a számítási példányt, ha nem használja a költségek csökkentése érdekében. A számítási példány leállítása felszabadítja azt. Ezután indítsa el újra, amikor szüksége van rá.
* Számítási példány törlése.
* Szűrje a számítási példányok listáját, hogy csak a létrehozott példányok mutassanak.

A munkaterület minden létrehozott (vagy az Ön számára létrehozott) számítási példánya számára a következőt használhatja:

* A Jupyter, a JupyterLab és az RStudio elérése a számítási példányon
* SSH a számítási példányba. Az SSH-hozzáférés alapértelmezés szerint le van tiltva, de a számítási példány létrehozásakor engedélyezhető. Az SSH-hozzáférés nyilvános/titkos kulcsos mechanizmuson keresztüli. A lapon adatokat talál az SSH-kapcsolatról, például az IP-címet, a felhasználónevet és a portszámot.
* Egy adott számítási példány, például az IP-cím és a régió részleteinek lekérte.

---


[Az Azure RBAC](../role-based-access-control/overview.md) segítségével szabályozhatja, hogy a munkaterület mely felhasználói hozhatnak létre, törölhet, indíthat el, állíthatnak le és indíthat újra számítási példányokat. A munkaterület közreműködője és a tulajdonosi szerepkör minden felhasználója létrehozhat, törölhet, elindíthat, leállíthat és újraindíthat számítási példányokat a munkaterületen. Azonban csak egy adott számítási példány létrehozója, vagy a hozzárendelt felhasználó férhet hozzá a Jupyterhez, a JupyterLab-hez és az RStudióhoz az adott számítási példányon. A számítási példány egyetlen felhasználó számára van ki dedikáltan, aki gyökér hozzáféréssel rendelkezik, és jupyter/JupyterLab/RStudio-terminállal is rendelkezik. A számítási példány egyfelhasználós bejelentkezéssel fog bejelentkezni, és minden művelet az adott felhasználó identitását fogja használni az Azure RBAC-hez és a kísérletfuttatások forrásmegjelölését. Az SSH-hozzáférés vezérlése nyilvános/titkos kulcsos mechanizmussal történik.

Ezeket a műveleteket az Azure RBAC vezérelheti:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

## <a name="next-steps"></a>Következő lépések

* [A számításipéldány-terminál elérése](how-to-access-terminal.md)
* [Fájlok létrehozása és kezelése](how-to-manage-files.md)
* [Betanítás futtatása elküldése](how-to-set-up-training-targets.md)
