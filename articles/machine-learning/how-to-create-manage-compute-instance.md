---
title: Számítási példány létrehozása és kezelése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és kezelhet egy Azure Machine Learning számítási példányt. Fejlesztési vagy tesztelési célokra használható számítási célként.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 5aaaf2000fb8310a5208447f8eaf8546feda90fb
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509319"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning számítási példány létrehozása és kezelése

Megtudhatja, hogyan hozhat létre és kezelhet [számítási példányokat](concept-compute-instance.md) a Azure Machine learning munkaterületen.

Számítási példányt használhat a felhőben teljes mértékben konfigurált és felügyelt fejlesztési környezetként. A fejlesztéshez és teszteléshez használhatja a példányt [tanítási számítási célként](concept-compute-target.md#train) vagy egy [következtetési célra](concept-compute-target.md#deploy)is.   Egy számítási példány több feladatot is futtathat párhuzamosan, és feladat-várólistával rendelkezik. Fejlesztési környezetként a számítási példányok nem oszthatók meg a munkaterület más felhasználóival.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Új számítási példány létrehozása 
* A számítási példányok kezelése (indítás, Leállítás, újraindítás, törlés)
* A terminál ablakának elérése 
* R-vagy Python-csomagok telepítése
* Új környezetek vagy Jupyter-kernelek létrehozása

A számítási példányok biztonságosan futtathatnak feladatokat egy [virtuális hálózati környezetben](how-to-secure-training-vnet.md)anélkül, hogy a vállalatoknak SSH-portokat kellene megnyitnia. A feladatot egy tároló környezetben hajtja végre a rendszer, és a modell függőségeit egy Docker-tárolóban csomagolja. 

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

* Az [Azure CLI-bővítmény Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [Azure Machine learning Python SDK](/python/api/overview/azure/ml/intro)-hoz vagy a [Azure Machine learning Visual Studio Code bővítményhez](tutorial-setup-vscode-extension.md).

## <a name="create"></a>Létrehozás

**Becsült idő**: körülbelül 5 perc.

A számítási példány létrehozása a munkaterület egyszeri folyamata. Felhasználhatja a számítási kapacitást fejlesztési munkaállomásként vagy kiszámítási célként a képzéshez. Több számítási példány is csatolható a munkaterülethez.

A számítási példányok létrehozásakor a dedikált magok régiónként, a virtuálisgép-család kvótája és a teljes regionális kvóta alapján, valamint az Azure Machine Learning betanítása számítási fürt kvótáját egyesítjük és megosztva. A számítási példány leállítása nem mentesíti a kvótát, hogy biztosan újra tudja indítani a számítási példányt. Megjegyzés: a számítási példányok virtuálisgép-méretének módosítása a létrehozás után nem lehetséges.

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

Az ebben a példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő dokumentációt:

* [ComputeInstance osztály](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget. Create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

További információ: az [ml computetarget Create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext_azure_cli_ml_az_ml_computetarget_create_computeinstance) Reference.

# <a name="studio"></a>[Studio](#tab/azure-studio)

A Azure Machine Learning Studio munkaterületén hozzon létre egy új számítási példányt a **számítási** szakaszból vagy a **jegyzetfüzetek** szakaszban, amikor készen áll az egyik jegyzetfüzet futtatására.

A számítási példányok Studióban történő létrehozásával kapcsolatos információkért lásd: [számítási célok létrehozása Azure Machine learning Studióban](how-to-create-attach-compute-studio.md#compute-instance).

---

Létrehozhat egy [Azure Resource Manager sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)rendelkező számítási példányt is. 

### <a name="create-on-behalf-of-preview"></a>Létrehozás a következő nevében (előzetes verzió)

Rendszergazdaként létrehozhat egy számítási példányt egy adattudós nevében, és hozzárendelheti a példányt a következőhöz:
* [Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  A sablonban szükséges TenantID és ObjectID megkeresésével kapcsolatos részletekért lásd a [hitelesítési konfiguráció azonosító objektum-azonosítóinak megkeresése](../healthcare-apis/find-identity-object-ids.md)című témakört.  Ezeket az értékeket a Azure Active Directory portálon is megtalálhatja.
* REST API

A számítási példányt a következő [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../role-based-access-control/overview.md) engedélyekkel kell létrehoznia: 
* *Microsoft. MachineLearningServices/munkaterületek/számítások/indítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/leállítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/újraindítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/applicationaccess/művelet*

Az adattudós elindíthatja, leállíthatja és újraindíthatja a számítási példányt. A számítási példány a következőhöz használható:
* Jupyter
* JupyterLab
* RStudio
* Integrált jegyzetfüzetek

## <a name="manage"></a>Kezelés

Számítási példány elindítása, leállítása, újraindítása és törlése. A számítási példányok nem méretezhetők le automatikusan, ezért ügyeljen arra, hogy a folyamatos költségek elkerülése érdekében állítsa le az erőforrást.

# <a name="python"></a>[Python](#tab/python)

Az alábbi példákban a számítási példány neve **példány**

* Állapot beolvasása

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

    További információ: [az ml computetarget stop computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

* Indítás 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    További információ: [az ml computetarget Start computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

* Újraindítás 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    További információ: [az ml computetarget restart computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

* Törlés

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    További információ: [az ml computetarget delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Azure Machine Learning Studio munkaterületén válassza a **számítás**, majd a felül található **számítási példány** elemet.

![Számítási példány kezelése](./media/concept-compute-instance/manage-compute-instance.png)

A következő műveleteket hajthatja végre:

* Új számítási példány létrehozása 
* Frissítse a számítási példányok lapot.
* Számítási példány elindítása, leállítása és újraindítása.  A példányért kell fizetnie, amikor fut. Állítsa le a számítási példányt, ha nem használja azt a Cost csökkentése érdekében. Egy számítási példány leállítása felszabadítja azt. Ezután indítsa el újra, amikor szüksége van rá.
* Számítási példány törlése.
* A számítási példányok listájának szűrése csak a létrehozott adatok megjelenítéséhez.

A munkaterületen létrehozott minden számítási példányhoz (vagy az Ön számára létrehozott) a következőket teheti:

* Hozzáférés Jupyter, JupyterLab, RStudio a számítási példányon
* Az SSH-t a számítási példányba. Az SSH-hozzáférés alapértelmezés szerint le van tiltva, de a számítási példány létrehozási idején is engedélyezhető. Az SSH-hozzáférés a nyilvános/titkos kulcs mechanizmusán keresztül történik. A lapon megadhatja az SSH-kapcsolat adatait, például az IP-címet, a felhasználónevet és a portszámot.
* Egy adott számítási példány, például az IP-cím és a régió részletes adatainak beolvasása.

---

Az [Azure RBAC](../role-based-access-control/overview.md) lehetővé teszi annak szabályozását, hogy a munkaterület mely felhasználói hozhatnak létre, törölhetnek, indíthatnak le, állíthatnak le, indíthatnak újra egy számítási példányt. A munkaterület közreműködői és tulajdonosi szerepkörben lévő összes felhasználó létrehozhatja, törölheti, elindíthatja, leállíthatja és újraindíthatja a számítási példányokat a munkaterületen. Azonban csak egy adott számítási példány létrehozója, vagy a felhasználó nevében létrejött, a Jupyter, a JupyterLab és a RStudio hozzáférése engedélyezett a számítási példányon. A számítási példányok egyetlen, rendszergazdai hozzáféréssel rendelkező felhasználóhoz vannak hozzárendelve, és a Jupyter/JupyterLab/RStudio-en keresztül is csatlakozhatnak. A számítási példánynak egyfelhasználós bejelentkezéssel kell rendelkeznie, és minden művelet a felhasználó identitását fogja használni az Azure RBAC és a kísérlet futtatásához. Az SSH-hozzáférés a nyilvános/titkos kulcs mechanizmusán keresztül vezérelhető.

Ezeket a műveleteket az Azure RBAC is szabályozhatja:
* *Microsoft. MachineLearningServices/munkaterületek/számítások/olvasás*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/törlés*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/indítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/leállítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/újraindítás/művelet*

## <a name="next-steps"></a>Következő lépések

* [A számítási példány termináljának elérése](how-to-access-terminal.md)
* [Fájlok létrehozása és kezelése](how-to-manage-files.md)
* [Betanítási Futtatás beküldése](how-to-set-up-training-targets.md)
