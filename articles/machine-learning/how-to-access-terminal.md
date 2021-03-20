---
title: Számítási példányok termináljának elérése a munkaterületen
titleSuffix: Azure Machine Learning
description: Használja a terminált egy számítási példányon a git-műveletekhez, a csomagok telepítéséhez és a kernelek hozzáadásához.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101333"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>A számítási példányok termináljának elérése a munkaterületen

A munkaterületen lévő számítási példányok terminálját a következőre érheti el:

* A git és a Version fájlokból származó fájlokat használjon. Ezeket a fájlokat a munkaterület fájlrendszerében tárolja a rendszer, nem csak egyetlen számítási példányra korlátozódik.
* Telepítse a csomagokat a számítási példányra.
* Hozzon létre további kerneleket a számítási példányon.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).
* Machine Learning munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Hozzáférés egy terminálhoz

A terminál elérése:

1. Nyissa meg a munkaterületét [Azure Machine learning Studióban](https://ml.azure.com).
1. A bal oldalon válassza a **jegyzetfüzetek** lehetőséget.
1. Válassza ki a **megnyitott terminál** -rendszerképet.

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Terminál ablakának megnyitása":::

1. Ha egy számítási példány fut, megjelenik az adott számítási példányhoz tartozó terminál ablak.
1. Ha egy számítási példány nem fut, a jobb oldalon található **számítási szakasz használatával** indítsa el vagy hozzon létre egy számítási példányt.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Számítási példány elindítása vagy létrehozása":::

A fenti lépéseken kívül a következőt is elérheti a terminálról:

* RStudio: válassza a bal felső sarokban található **terminál** fület.
* Jupyter labor: válassza ki a **terminál** csempét az indító lap **másik** címsorában.
* Jupyter: a fájlok lapon válassza a jobb felső sarokban található **új>terminál** elemet.
* SSH-t a gépre, ha engedélyezte az SSH-hozzáférést a számítási példány létrehozásakor.

## <a name="copy-and-paste-in-the-terminal"></a>Másolás és beillesztés a terminálon

> * Windows: `Ctrl-Insert` Másolás és használat `Ctrl-Shift-v` vagy `Shift-Insert` Beillesztés.
> * Mac OS: `Cmd-c` a másoláshoz és `Cmd-v` a beillesztéshez.
> * A FireFox/IE nem támogatja megfelelően a vágólap-engedélyeket.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> A git-és fájlverzió-fájlok használata

A terminál összes git-műveletének elérése. A rendszer az összes git-fájlt és-mappát a munkaterület fájlrendszerében fogja tárolni. Ez a tárterület lehetővé teszi, hogy ezeket a fájlokat a munkaterületen lévő bármely számítási példányból használhassa.

> [!NOTE]
> A **~/cloudfiles/Code/Users** mappában bárhol hozzáadhatja fájljait és mappáit, így azok az összes Jupyter-környezetben láthatók lesznek.

További információ a [git-adattáraknak a munkaterület-fájlrendszerbe való klónozásáról](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

## <a name="install-packages"></a>Csomagok telepítése

 Csomagok telepítése egy terminál-ablakból. Telepítse a Python-csomagokat a **python 3,6-AzureML-** környezetbe.  Telepítse az R-csomagokat az **r** -környezetbe.

A csomagokat közvetlenül Jupyter Notebook vagy RStudio is telepítheti:

* A RStudio a jobb alsó sarokban található **csomagok** fület vagy a bal felső sarokban található **konzol** fület használják.  
* Python: telepítési kód hozzáadása és végrehajtása Jupyter Notebook cellában.

> [!NOTE]
> Jegyzetfüzeten belüli csomagkezelő esetén a **% pip** vagy **% Conda** Magic functions használatával automatikusan telepíthet csomagokat a **jelenleg futó kernelre**, nem pedig **! pip** vagy **! Conda** , amely az összes csomagra vonatkozik (beleértve a jelenleg futó kernelen kívüli csomagokat is)

## <a name="add-new-kernels"></a>Új kernelek hozzáadása

> [!WARNING]
>  A számítási példány testreszabása során győződjön meg arról, hogy nem törli a **azureml_py36** Conda-környezetet vagy a **Python 3,6-azureml** kernelt. Ez a Jupyter-/JupyterLab-funkciókhoz szükséges

Új Jupyter-kernel hozzáadása a számítási példányhoz:

1. Hozzon létre egy új környezetet a terminálablak használatával.  Az alábbi kód például a következőt hozza létre `newenv` :

    ```shell
    conda create --name newenv
    ```

1. Aktiválja a környezetet.  Például a létrehozása után `newenv` :

    ```shell
    conda activate newenv
    ```

1. Telepítse a pip és a ipykernel csomagot az új környezetbe, és hozzon létre egy kernelt az adott Conda env számára

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

A [rendelkezésre álló Jupyter-kernelek](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) bármelyike telepíthető.

## <a name="manage-terminal-sessions"></a>Terminál-munkamenetek kezelése

 Válassza az **aktív munkamenetek megtekintése** lehetőséget a terminál eszköztárán az összes aktív terminál-munkamenet listájának megtekintéséhez. Ha nincsenek aktív munkamenetek, ez a lap le lesz tiltva.

A számítási példány erőforrásainak megőrzése érdekében zárjunk be minden nem használt munkamenetet.