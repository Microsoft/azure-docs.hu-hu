---
title: Python fejlesztői környezet beállítása
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python fejlesztői környezetek beállítása a Jupyter notebookok, a Visual Studio Code, a Azure Databricks és az adatelemzési Virtual Machines.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 03/22/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 7f35f9196fb4ab67de724ba73982fed69ac81361
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306840"
---
# <a name="set-up-a-python-development-environment-for-azure-machine-learning"></a>Python-fejlesztési környezet beállítása Azure Machine Learninghoz

Megtudhatja, hogyan konfigurálhat Azure Machine Learning Python-fejlesztési környezetet.

A következő táblázat a jelen cikkben tárgyalt összes fejlesztési környezetet tartalmazza, valamint az előnyeit és hátrányait.

| Környezet | Előnyök | Hátrányok |
| --- | --- | --- |
| [Helyi környezet](#local) | A fejlesztési környezet és a függőségek teljes körű vezérlése. Bármilyen felépíthető eszközzel, környezettel vagy tetszőleges IDE-val futtatható. | A kezdéshez tovább tart. Telepíteni kell a szükséges SDK-csomagokat, és a környezetnek is telepítve kell lennie, ha még nem rendelkezik ilyennel. |
| [A Data Science Virtual Machine (DSVM)](#dsvm) | A felhőalapú számítási példányhoz hasonlóan (a Python és az SDK előre telepítve van), de további népszerű adatelemzési és gépi tanulási eszközökkel előre telepítve van. Egyszerűen méretezhető és kombinálható más egyéni eszközökkel és munkafolyamatokkal. | Lassabban megkezdhető a felhőalapú számítási példányhoz képest. |
| [Azure Machine Learning számítási példány](#compute-instance) | A legegyszerűbb módszer a kezdéshez. A teljes SDK már telepítve van a munkaterület virtuális gépén, és a jegyzetfüzet-oktatóanyagok előre klónozottak, és készen állnak a futtatásra. | A fejlesztési környezet és a függőségek szabályozásának hiánya. A Linux rendszerű virtuális gépekkel kapcsolatos további költségek (a virtuális gép leállítható, ha nem használatban van a költségek elkerülése érdekében). Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Azure Databricks](how-to-configure-databricks-automl-environment.md) | Ideális megoldás a méretezhető Apache Spark platformon nagy léptékű, intenzív gépi tanulási munkafolyamatok futtatására. | A kísérleti gépi tanulás vagy a kisebb léptékű kísérletek és munkafolyamatok meggyilkolása. További költségek Azure Databricksért. Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/databricks/). |

Ez a cikk további használati tippeket is tartalmaz a következő eszközökhöz:

* Jupyter jegyzetfüzetek: Ha már használ Jupyter jegyzetfüzeteket, akkor az SDK-nak néhány extrával kell telepítenie.

* Visual Studio code: Ha Visual Studio Code-t használ, a [Azure Machine learning bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) kiterjedt nyelvi támogatást nyújt a Pythonhoz, valamint olyan funkciókat is kínál, amelyek sokkal kényelmesebb és hatékonyabban használják a Azure Machine learning.

## <a name="prerequisites"></a>Előfeltételek

* Azure Machine Learning munkaterület. Ha még nem rendelkezik ilyennel, létrehozhat egy Azure Machine Learning munkaterületet az [Azure Portal](how-to-manage-workspace.md), az [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)és a [Azure Resource Manager sablonok](how-to-create-workspace-template.md)segítségével.

### <a name="local-and-dsvm-only-create-a-workspace-configuration-file"></a><a id="workspace"></a> Csak helyi és DSVM: munkaterület konfigurációs fájljának létrehozása

A munkaterület-konfigurációs fájl egy JSON-fájl, amely közli az SDK-val, hogyan kommunikálhat a Azure Machine Learning munkaterülettel. A fájl neve *config.json*, és a formátuma a következő:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

A JSON-fájlnak a Python-szkripteket vagy Jupyter-jegyzetfüzeteket tartalmazó címtár-struktúrában kell lennie. Ez lehet ugyanabban a címtárban, egy *. azureml* nevű alkönyvtár vagy egy szülő könyvtárban.

Ha ezt a fájlt a kódból szeretné használni, használja a [`Workspace.from_config`](/python/api/azureml-core/azureml.core.workspace.workspace#from-config-path-none--auth-none---logger-none---file-name-none-) metódust. Ez a kód betölti az adatokat a fájlból, és csatlakozik a munkaterülethez.

Hozzon létre egy munkaterület-konfigurációs fájlt az alábbi módszerek egyikével:

* Azure Portal

    **Töltse le a fájlt**: a [Azure Portal](https://ms.portal.azure.com)válassza a  **config.jsletöltése** elemet a munkaterület **Áttekintés** szakaszában.

    ![Azure Portal](./media/how-to-configure-environment/configure.png)

* Python SDK Azure Machine Learning

    Hozzon létre egy parancsfájlt a Azure Machine Learning munkaterülethez való kapcsolódáshoz, és a [`write_config`](/python/api/azureml-core/azureml.core.workspace.workspace#write-config-path-none--file-name-none-) metódus használatával hozza létre a fájlt, és mentse *. azureml/config.js*. Győződjön meg arról, hogy a, a `subscription_id` `resource_group` és a és a együtt van lecserélve `workspace_name` .

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

## <a name="local-computer-or-remote-vm-environment"></a><a id="local"></a>Helyi számítógép vagy távoli virtuálisgép-környezet

Beállíthat egy környezetet helyi számítógépen vagy távoli virtuális gépen, például egy Azure Machine Learning számítási példányon vagy Data Science VMon. 

Helyi fejlesztési környezet vagy távoli virtuális gép konfigurálása:

1. Hozzon létre egy Python virtuális környezetet (virtualenv, Conda).

    > [!NOTE]
    > Bár ez nem kötelező, a [anaconda](https://www.anaconda.com/download/) vagy a [Miniconda](https://www.anaconda.com/download/) használata ajánlott a Python virtuális környezetek kezeléséhez és csomagok telepítéséhez.

    > [!IMPORTANT]
    > Ha Linux vagy macOS rendszerű, és a bashtől eltérő (például zsh) rendszerhéjat használ, előfordulhat, hogy bizonyos parancsok futtatásakor hibák jelentkeznek. A probléma megkerüléséhez a `bash` paranccsal indítson el egy új bash-rendszerhéjat, és futtassa a parancsokat.

1. Aktiválja az újonnan létrehozott Python virtuális környezetet.
1. Telepítse a [Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/install)-t.
1. Ha a helyi környezetet a Azure Machine Learning munkaterület használatára szeretné konfigurálni, [hozzon létre egy munkaterület-konfigurációs fájlt](#workspace) , vagy használjon egy meglévőt.

Most, hogy beállította a helyi környezetét, készen áll a Azure Machine Learning használatának megkezdésére. A kezdéshez tekintse meg a [Azure Machine learning Python első lépések útmutatóját](tutorial-1st-experiment-sdk-setup-local.md) .

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Jupyter-notebookok

Helyi Jupyter Notebook-kiszolgáló futtatásakor javasoljuk, hogy hozzon létre egy IPython-rendszermagot a Python virtuális környezethez. Ez segít a várt kernel-és csomag-importálási viselkedés biztosításában.

1. Környezet-specifikus IPython-kernelek engedélyezése

    ```bash
    conda install notebook ipykernel
    ```

1. Hozzon létre egy kernelt a Python virtuális környezethez. Ügyeljen arra, hogy a helyére `<myenv>` a Python-beli virtuális környezet nevét írja.

    ```bash
    ipython kernel install --user --name <myenv> --display-name "Python (myenv)"
    ```

1. A Jupyter Notebook-kiszolgáló elindítása

A Azure Machine Learning és a Jupyter notebookok megismeréséhez tekintse meg a [Azure Machine learning notebook-tárházat](https://github.com/Azure/MachineLearningNotebooks) .

> [!NOTE]
> A példák Közösség által vezérelt tárháza a következő címen érhető el: https://github.com/Azure/azureml-examples .

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

A Visual Studio Code használata a fejlesztéshez:

1. Telepítse a [Visual Studio Code](https://code.visualstudio.com/Download)-ot.
1. Telepítse a [Azure Machine learning Visual Studio Code bővítményt](tutorial-setup-vscode-extension.md) (előzetes verzió).

Miután telepítette a Visual Studio Code bővítményt, használja a következőre:

* [Azure Machine Learning-erőforrások kezelése](how-to-manage-resources-vscode.md)
* [Kapcsolódás Azure Machine Learning számítási példányhoz](how-to-set-up-vs-code-remote.md)
* [Kísérletek futtatása és hibakeresése](how-to-debug-visual-studio-code.md)
* [Betanított modellek üzembe helyezése](tutorial-train-deploy-image-classification-model-vscode.md).

## <a name="azure-machine-learning-compute-instance"></a><a id="compute-instance"></a>Azure Machine Learning számítási példány

A Azure Machine Learning [számítási példány](concept-compute-instance.md) egy biztonságos, felhőalapú Azure-munkaállomás, amely egy Jupyter notebook-kiszolgálóval, JupyterLab és egy teljes körűen felügyelt gépi tanulási környezettel biztosítja az adatszakértőket.

A számítási példányok telepítése és konfigurálása nem szükséges.  

Hozzon létre egyet bármikor a Azure Machine Learning munkaterületen belülről. Adjon meg csak egy nevet, és adjon meg egy Azure-beli virtuális gép típusát. Próbálja ki most ezt az [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md).

További információ a számítási példányokról, beleértve a csomagok telepítésének módját: [Azure Machine learning számítási példány létrehozása és kezelése](how-to-create-manage-compute-instance.md).

> [!TIP]
> Ha meg szeretné akadályozni, hogy a nem használt számítási példányok ne kelljen fizetni, [állítsa le a számítási példányt](how-to-create-manage-compute-instance.md#manage).

A Jupyter Notebook-kiszolgáló és a JupyterLab mellett számítási példányokat is használhat az [integrált jegyzetfüzet szolgáltatásban Azure Machine learning Studióban](how-to-run-jupyter-notebooks.md).

Azure Machine Learning a VS Code használatával a Visual Studio Code-bővítményt is használhatja a [távoli számítási példányokhoz való kapcsolódáshoz](how-to-set-up-vs-code-remote.md).

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Adatelemzési virtuális gép

A Data Science VM egy testreszabott virtuálisgép-rendszerkép, amelyet fejlesztési környezetként használhat. Ez az adatelemzési munkák számára készült, amelyek előre konfigurált eszközök és szoftverek, például:

  - Csomagok, mint például a TensorFlow, a PyTorch, a Scikit-Learn, a XGBoost és a Azure Machine Learning SDK
  - Népszerű adatelemzési eszközök, mint például a Spark standalone és a drill
  - Azure-eszközök, például az Azure CLI, a AzCopy és a Storage Explorer
  - Integrált fejlesztői környezetek (ide), például a Visual Studio Code és a Notebookshoz
  - Jupyter Notebook kiszolgáló

Az eszközök átfogóbb listáját a [Data Science VM eszközök útmutatójában](data-science-virtual-machine/tools-included.md)találja.

> [!IMPORTANT]
> Ha azt tervezi, hogy a Data Science VMt [számítási célként](concept-compute-target.md) szeretné használni a betanítási vagy következtetési feladatokhoz, csak az Ubuntu támogatott.

A Data Science VM használata fejlesztési környezetként:

1. Hozzon létre egy Data Science VM az alábbi módszerek egyikével:

    * [Ubuntu](data-science-virtual-machine/dsvm-ubuntu-intro.md) -vagy [Windows](data-science-virtual-machine/provision-vm.md) -DSVM létrehozásához használja a Azure Portal.
    * [Hozzon létre egy Data Science VM ARM-sablonok használatával](data-science-virtual-machine/dsvm-tutorial-resource-manager.md).
    * Az Azure parancssori felületének használata

        Ubuntu Data Science VM létrehozásához használja a következő parancsot:

        ```azurecli-interactive
        # create a Ubuntu Data Science VM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
        ```

        Windows DSVM létrehozásához használja a következő parancsot:

        ```azurecli-interactive
        # create a Windows Server 2016 DSVM in your resource group
        # note you need to be at least a contributor to the resource group in order to execute this command successfully
        az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
        ```

1. Aktiválja a Azure Machine Learning SDK-t tartalmazó Conda-környezetet.

    * Ubuntu Data Science VM esetén:

        ```bash
        conda activate py36
        ```

    * Windows Data Science VM esetén:

        ```bash
        conda activate AzureML
        ```

1. Ha a Data Science VM a Azure Machine Learning munkaterület használatára szeretné konfigurálni, [hozzon létre egy munkaterület-konfigurációs fájlt](#workspace) , vagy használjon egy meglévőt.

A helyi környezetekhez hasonlóan a Visual Studio Code és a [Azure Machine learning Visual Studio Code bővítmény](#vscode) is használható a Azure Machine learning való interakcióhoz.

További információ: [Adatelemzési Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).


## <a name="next-steps"></a>Következő lépések

- Azure Machine Learning [modell betanítása](tutorial-train-models-with-aml.md) a MNIST adatkészlettel.
- Tekintse [meg a Pythonhoz készült Azure Machine learning SDK-referenciát](/python/api/overview/azure/ml/intro). 
