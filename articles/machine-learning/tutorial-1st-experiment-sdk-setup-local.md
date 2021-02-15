---
title: 'Oktatóanyag: a Machine learning használatának első lépései – Python'
titleSuffix: Azure Machine Learning
description: Ebből az oktatóanyagból megtudhatja, hogyan futtathatja a Pythonhoz készült Azure Machine Learning SDK-t a személyes fejlesztési környezetben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: 5d61f330c823aff755a25e637bb58c132aa58100
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369125"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Oktatóanyag: a Azure Machine Learning első lépései a fejlesztési környezetben (4. rész)

Ebben a *négy részes oktatóanyag-sorozatban* megismerheti a Azure Machine learning alapjait, és az Azure Cloud platformon végezheti el a feladatok alapú Python gépi tanulás feladatait. 

Az oktatóanyag-sorozat 1. részében a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Telepítse a Azure Machine Learning SDK-t.
> * Állítsa be a kód címtár-struktúráját.
> * Hozzon létre egy Azure Machine Learning munkaterületet.
> * Konfigurálja a helyi fejlesztési környezetet.
> * Hozzon létre egy számítási fürtöt.

> [!NOTE]
> Ez az oktatóanyag-sorozat a **Batch-feladatok** elküldéséhez szükséges Azure Machine learning fogalmakat ismerteti – ez az a kód, amellyel a rendszer a háttérben futtatja a kódot, felhasználói beavatkozás nélkül. Ez akkor hasznos, ha a befejezett parancsfájlokat vagy kódokat többször szeretné futtatni, vagy a számítási igényű gépi tanulási feladatokhoz. Ha jobban érdeklik a felderítő munkafolyamatok, használhatja [a Jupyter vagy a RStudio-t egy Azure Machine learning számítási példányon](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálkozzon [Azure Machine learning](https://aka.ms/AMLFree).
- [Anaconda](https://www.anaconda.com/download/) vagy [Miniconda](https://www.anaconda.com/download/) a Python virtuális környezetek kezeléséhez és csomagok telepítéséhez.  
- Ha még nem ismeri a Conda használatát, tekintse meg [a Conda használatának első lépéseit](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html)ismertető témakört.

## <a name="install-the-azure-machine-learning-sdk"></a>Az Azure Machine Learning SDK telepítése

Az oktatóanyag során a Pythonhoz készült Azure Machine Learning SDK-t fogja használni. A Python függőségi problémák elkerüléséhez hozzon létre egy elkülönített környezetet. Ez az oktatóanyag-sorozat a Conda használatával hozza létre ezt a környezetet. Ha más megoldásokat szeretne használni (például `venv` ,, `virtualenv` vagy Docker), győződjön meg arról, hogy a Python-verziót használja >= 3,5 és < 3,9.

Ellenőrizze, hogy a Conda telepítve van-e a rendszeren:
    
```bash
conda --version
```
    
Ha a parancs hibát ad vissza `conda not found` , [töltse le és telepítse a Miniconda](https://docs.conda.io/en/latest/miniconda.html). 

Miután telepítette a Conda-t, egy terminál-vagy anaconda-parancssor használatával hozzon létre egy új környezetet:

```bash
conda create -n tutorial python=3.8
```

Ezután telepítse a Azure Machine Learning SDK-t a létrehozott Conda-környezetbe:

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> A Azure Machine Learning SDK telepítésének befejezéséhez körülbelül 2 percet vesz igénybe.
>
> Ha időtúllépési hibát kap, próbálkozzon `pip install --default-timeout=100 azureml-core` helyette.


> [!div class="nextstepaction"]
> [Telepítettem az SDK](?success=install-sdk#dir) - [t egy hibába ütközött](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>Könyvtár-struktúra létrehozása kód számára

Javasoljuk, hogy az oktatóanyaghoz az alábbi egyszerű címtár-struktúrát állítsa be:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-1.png" alt-text="könyvtár szerkezete: oktatóanyag legfelső szintjének a. azureml alkönyvtárral":::


- `tutorial`: A projekt legfelső szintű könyvtára.
- `.azureml`: Rejtett alkönyvtár a Azure Machine Learning konfigurációs fájljainak tárolásához.

Ha például egy bash-ablakban szeretné létrehozni ezt:

```bash
mkdir tutorial
cd tutorial
mkdir .azureml
```

> [!TIP]
> Ha grafikus ablakban szeretné létrehozni vagy megtekinteni a struktúrát, először engedélyezze a rejtett fájlok és mappák megtekintését és létrehozását:
>
> * A Mac-Finder ablakban a **Command + Shift + billentyűkombinációt használhatja.** a rejtett fájlok/mappák megjelenítésének váltásához.  
> * A Windows 10 fájlkezelőben tekintse meg a [rejtett fájlok és mappák megtekintését](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5)ismertető témakört. 
> * A Linux grafikus felületén használja a **CTRL + h billentyűkombinációt** vagy a **nézet** menüt, és jelölje be a jelölőnégyzetet a **rejtett fájlok megjelenítéséhez**.




> [!div class="nextstepaction"]
> [Létrehoztam egy könyvtárat](?success=create-dir#workspace) , amely egy [hibába ütközött](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Azure Machine Learning-munkaterület létrehozása

A munkaterület a Azure Machine Learning legfelső szintű erőforrása, és központi hely a következőhöz:

- Kezelheti az erőforrásokat, például a számítást.
- Tárolja az eszközöket, például a jegyzetfüzeteket, a környezeteket, az adatkészleteket, a folyamatokat, a modelleket és a végpontokat.
- Együttműködés a csapattagok más tagjaival.

A legfelső szintű címtárban `tutorial` adjon hozzá egy nevű új Python-fájlt `01-create-workspace.py` a következő kóddal. A paramétereket (név, előfizetés-azonosító, erőforráscsoport és [hely](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) a beállításokkal igazíthatja.

A kódot egy interaktív munkamenetben vagy Python-fájlként is futtathatja.

>[!NOTE]
> Ha helyi fejlesztési környezetet (például a számítógépet) használ *, a rendszer a következő* kód első futtatásakor a munkaterületre történő hitelesítést fogja kérni. Kövesse a képernyőn megjelenő utasításokat.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Az aktivált *tutorial1* Conda környezettel rendelkező ablakban futtassa ezt a kódot a `tutorial` címtárból.

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Ha a kód futtatásakor hibaüzenet jelenik meg, hogy nincs hozzáférése az előfizetéshez, tekintse meg a [Munkaterület létrehozása](how-to-manage-workspace.md?tab=python#create-multi-tenant) a hitelesítési lehetőségekkel kapcsolatos információkhoz című témakört.


A *01-Create-Workspace.py* sikeres futtatása után a mappa szerkezete a következőképpen fog kinézni:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-2.png" alt-text="A fájl config.jsekkor megjelenik a. azureml alkönyvtárában a 01-create-workspace.py futtatása után.":::

A fájl `.azureml/config.json` tartalmazza a Azure Machine learning-munkaterülethez való kapcsolódáshoz szükséges metaadatokat. Ez tartalmazza az előfizetés-AZONOSÍTÓját, az erőforráscsoportot és a munkaterület nevét. 

> [!NOTE]
> A tartalma `config.json` nem titok. Ezeket a részleteket részletesen megoszthatja.
>
> A Azure Machine Learning-munkaterülettel való interakcióhoz továbbra is szükséges a hitelesítés.

> [!div class="nextstepaction"]
> [Egy olyan munkaterületet hoztam létre](?success=create-workspace#cluster) [, amely egy hibába ütközött](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Azure Machine Learning számítási fürt létrehozása

Hozzon létre egy Python-szkriptet a `tutorial` legfelső szintű címtárban `02-create-compute.py` . Töltse fel a következő kóddal, hogy létrehozzon egy Azure Machine Learning számítási fürtöt, amely a nulla és négy csomópont között automatikusan méretezhető:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Az aktivált *tutorial1* Conda környezettel rendelkező ablakban futtassa a Python-fájlt:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> A fürt létrehozásakor 0 csomópont lesz kiépítve. A fürt *nem* jár költségekkel, amíg el nem küld egy feladatot. Ez a fürt leskálázást végez, ha 2 400 másodperc (40 perc) üresjáratban van.

A mappa szerkezete mostantól a következőképpen fog megjelenni:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-3.png" alt-text="02-create-compute.py hozzáadása az oktatóanyag könyvtárához":::

> [!div class="nextstepaction"]
> [Létrehoztam egy számítási fürtöt](?success=create-compute-cluster#next-steps) [, amely egy hibába ütközött](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>Megtekintés a Studióban

Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com) , és tekintse meg a létrehozott munkaterületet és számítási példányt.

1. Válassza ki a munkaterület létrehozásához használt **előfizetést** .
1. Válassza ki a létrehozott **Machine learning munkaterületet** , *oktatóanyag: ws*.
1. Miután a munkaterület betöltődik, a bal oldalon válassza a **számítás** lehetőséget.
1. A felső részen válassza a **számítási fürtök** lapot.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="Képernyőfelvétel: a számítási példány megtekintése a munkaterületen.":::

Ez a nézet megjeleníti a kiépített számítási fürtöt, valamint az üresjárati csomópontok, a foglalt csomópontok és a nem kiépített csomópontok számát.  Mivel még nem használta a fürtöt, az összes csomópontot jelenleg nem lehet kiépíteni.

## <a name="next-steps"></a>Következő lépések

Ebben a beállítási oktatóanyagban a következőket teheti:

- Létrehozott egy Azure Machine Learning munkaterületet.
- Állítsa be a helyi fejlesztési környezetet.
- Létrehozott egy Azure Machine Learning számítási fürtöt.

Az oktatóanyag egyéb részeiben a következőket fogja elsajátítani:

* 2. rész. A Pythonhoz készült Azure Machine Learning SDK használatával futtasson kódot a felhőben.
* 3. rész. Kezelheti a modell betanításához használt Python-környezetet.
* 4. rész. Adatok feltöltése az Azure-ba, és az adatok felhasználása a képzésben.

Folytassa a következő oktatóanyaggal, amely végigvezeti egy parancsfájlnak a Azure Machine Learning számítási fürtbe való beküldésének lépésein.

> [!div class="nextstepaction"]
> [Oktatóanyag: a "Helló világ!" futtatása Python-szkript az Azure-ban](tutorial-1st-experiment-hello-world.md)
