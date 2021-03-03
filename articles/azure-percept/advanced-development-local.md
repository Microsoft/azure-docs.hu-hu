---
title: Ismerkedjen meg helyileg az Azure Percept speciális fejlesztésével
description: Ismerkedés a helyi gépi tanulás fejlesztésével a VS Code + Jupyter Notebooks on AzureML használatával
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664857"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>Ismerkedés a Machine learning-fejlesztéssel a VS Code + Jupyter jegyzetfüzetek használatával a AzureML

Ez a cikk végigvezeti egy Azure Machine Learning munkaterület beállításán, a számítási példányok létrehozásán, a Visual Studio Code fejlesztői környezetnek a helyi gépen való beállításán, valamint egy előre konfigurált minta Jupyter notebookon belüli futtatásának folyamatán a VS Code-ban.

A [Jegyzetfüzet](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) egy előre betanított TensorFlow-modellel (MobileNetSSDV2Lite) végzi a tanulást a Pythonban lévő AzureML, és egy egyéni adatkészlet segítségével felismeri a tálakat.

A jegyzetfüzetből megtudhatja, hogyan kezdheti el a [kókuszdió-adatkészletből](https://cocodataset.org/#home), hogy csak az érdeklődési osztályra (Bowls) legyen kiszűrve, majd a megfelelő formátumba alakítsa át. Azt is megteheti, hogy a nyílt forráskódú [VoTT 2](https://github.com/microsoft/VoTT) címkézési eszközzel létrehozza és címkézi a határoló MEZŐKET a Pascal VOC formátumban.

Az egyéni adatkészlet modellének átképzése után a modell üzembe helyezhető az Azure Percept DK-ben a modul Twin Update metódusának használatával. Ezután megtekintheti a modell következtetéseit, ha megtekinti az élő RTSP streamet az Azure Percept-vízióban. A modell-átképzést és az üzembe helyezést a távoli számítási példányon keresztül hajtja végre a jegyzetfüzeten belül.

## <a name="prerequisites"></a>Előfeltételek

- [Előfizetés Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [Az Azure Percept DK és az Azure Percept jövőképe SoM csatlakoztatva](./overview-azure-percept-dk.md)
- [Az Azure PERCEPT DK helyszíni](./quickstart-percept-dk-set-up.md) betöltési élménye befejeződött

## <a name="download-azure-percept-github-repository"></a>Az Azure Percept GitHub-adattár letöltése

1. Nyissa meg az [Azure PERCEPT DK GitHub-tárházat](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).
1. A tárház klónozása vagy a ZIP-fájl letöltése. A képernyő felső részén kattintson a **kód**  ->  **zip letöltése** elemre.

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="GitHub-letöltési képernyő.":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Azure Machine Learning munkaterület és távoli számítási példány létrehozása

1. Nyissa meg a [Azure Machine learning portált](https://ml.azure.com).
1. Válassza ki a címtárat, az Azure-előfizetést és a Machine Learning munkaterületet a legördülő menüből, és kattintson az első **lépések** elemre.

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Azure ML első lépések képernyője.":::

    Ha még nem rendelkezik Azure Machine Learning munkaterülettel, kattintson az **Új munkaterület létrehozása** lehetőségre. Az új böngésző lapon tegye a következőket:

    1. Válassza ki Azure-előfizetését.
    1. Válassza ki az erőforráscsoportot.
    1. Adja meg a munkaterület nevét.
    1. Válassza ki a régiót.
    1. Válassza ki a munkaterület kiadását.
    1. Kattintson az **Áttekintés és létrehozás** elemre.
    1. A következő oldalon tekintse át a kiválasztott beállításokat, majd kattintson a **Létrehozás** gombra.

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Munkaterület-létrehozási képernyő az Azure ML-ben.":::

    Engedélyezzen néhány percet a munkaterület létrehozásához. A munkaterület létrehozásának befejezése után zöld pipa jelek jelennek meg az erőforrások mellett, és a **központi telepítés** a Machine learning Services áttekintése oldal tetején található.

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="Munkaterület-létrehozás megerősítése." lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    Miután befejeződött a munkaterület létrehozása, térjen vissza a Machine learning-portál lapra, és kattintson az első **lépések** elemre.

1. A Machine learning-munkaterület kezdőlapján kattintson a **számítás** elemre a bal oldali ablaktáblán.

1. Ha nincs meglévő számítási példány, hozzon létre egy új CPU-vagy GPU-számítási lehetőséget az **új** lehetőségre kattintva. Az **új számítási példány** ablakban adja meg a **számítási nevet**, válassza ki a **virtuális gép típusát**, és válassza ki a **virtuális gép méretét**. Kattintson a **Létrehozás** lehetőségre.

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="Új számítási példány létrehozási képernyője.":::

    Ha a **Létrehozás** gombra kattint, a számítási példány létrehozása néhány percet vesz igénybe. A **számítási** állapot egy zöld kört fog megjeleníteni, és a számítási létrehozás befejezése után **\<your compute name> fut** . Ez a számítási példány futtatja a Jupyter-kiszolgálót, és a rendszer ezt az oktatóanyagot használja.

## <a name="visual-studio-code-development-environment-setup"></a>A Visual Studio Code fejlesztői környezet beállítása

1. Telepítse a szükséges eszközöket.

    1. 1. módszer:

        A [dev Tools Pack telepítőjének](./dev-tools-installer.md) használatával telepítse a következő csomagokat:

        - Visual Studio Code
        - Python 3,5, 3,6 vagy 3,7
        - Miniconda3
        - Intel OpenVino Toolkit 2020,2

        Megjegyzés: az Intel OpenVino Toolkit választható csomagként jelenik meg a fejlesztői eszközcsomag telepítőben, de az Azure Percept DK Development Kit Azure Percept-víziójának SoM-vel való együttműködéséhez szükséges.

    1. 2. lehetőség:

        Ha nem szeretné használni a dev Tools Pack telepítőjét, manuálisan telepítse a következő csomagokat az alábbi hivatkozásokra kattintva, majd a megadott letöltési és telepítési útmutatót követve:

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3,5, 3,6 vagy 3,7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Intel OpenVino Toolkit 2020,2](https://docs.openvinotoolkit.org/)

    Megjegyzés: Ha már telepítette a teljes anaconda-disztribúciót, nem kell telepítenie a Miniconda. Ha nem szeretné, hogy a anaconda vagy a Miniconda ne használja, létrehozhat egy Python virtuális környezetet, és telepítheti az AI-modell fejlesztéséhez szükséges csomagokat a pip használatával.

1. Indítsa el a Visual Studio Code-ot.
1. Adatelemzési környezet beállítása:

    - 1. lehetőség – Kapcsolódás meglévő vagy új gépi tanulási távoli számítási példányhoz, amely már rendelkezik a kurátori ML-csomagokkal. Ebben az **oktatóanyagban ezt a lehetőséget** fogjuk használni.

    - 2. lehetőség – Conda-környezet beállítása helyi gépen.
        1. Nyisson meg egy Anaconda-vagy Miniconda-parancssort, és futtassa a következő parancsot egy **myenv** nevű környezet létrehozásához a megadott csomagokkal:

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Az anaconda-környezetek létrehozásával és kezelésével kapcsolatos további információkért tekintse meg az [anaconda dokumentációját](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).

1. VS Code-munkaterület létrehozása:

    1. Hozzon létre egy mappát a megfelelő helyen, hogy a Visual Studio Code-munkaterületként szolgáljon. Nevezze el **sajátmunkaterület**.
    1. Nyissa meg a **sajátmunkaterület** a Visual Studio Code-ban a **fájl**  >  **megnyitása mappa**  >  **kiválasztása mappa** elemre kattintva.
    1. A Fájlkezelőben navigáljon a [Transferlearningusing_SSDLiteV2 Model. ipynbb fájlra](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) az Azure Percept DK GitHub-tárház helyi példányáról. Másolja ezt a jegyzetfüzet-fájlt a sajátmunkaterület mappába.

## <a name="azure-integration-with-visual-studio-code"></a>Azure-integráció a Visual Studio Code-nal

1. Ha még nem tette meg, regisztráljon a [Azure Machine learning ingyenes vagy fizetős verziójára](https://aka.ms/AMLFree).

1. Telepítse az alábbi Azure-bővítményeket a VS Code-hoz:
    - [Azure Machine learning bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).
    - Python bennfentesek bővítmény. A vs Code-ban nyissa **meg a** következőt:  ->  **Command paletta**. A Command paletta mezőben adja meg és válassza a **Python: váltás a belső napi csatornára** lehetőséget.
    - [Azure-fiók kiterjesztése](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Ha a rendszer kéri, töltse be újra az ablakot a VS Code-ban.
    - [Azure IoT hub Toolkit-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).
    - [Azure IoT Edge bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

1. Jelentkezzen be Azure-fiókjába a Visual Studio Code-ban az erőforrások kiépítéséhez és az Azure-beli számítási feladatok futtatásához.
    1. Nyissa meg a parancssort a Visual Studio Code -ban  >  a menüsávon a **parancs paletta** megtekintése lehetőség választásával.
    1. Adja meg az **Azure-t: Jelentkezzen** be a parancssorba a bejelentkezési folyamat elindításához.

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Azure bejelentkezési képernyő." lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. Aktiválja a Python-bővítményt és az Azure-fiókot úgy, hogy a VS Code bal oldalán található Azure ikonra kattint.

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="Azure-ikon a VS Code-ban.":::

    1. Nyissa meg a Transferlearningusing_SSDLiteV2 Model_VSCode. ipynb notebookot a **sajátmunkaterület** mappából.
    1. Nyissa meg a parancs-palettát. Adja meg és válassza **a Python: helyi vagy távoli Jupyter-kiszolgáló megadása a kapcsolatokhoz** lehetőséget.
    1. Ekkor meg kell jelennie a választható kapcsolódási lehetőségek listájának. Válassza az **Azure ml Compute-példányok** lehetőséget.

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="Azure ML számítási példány beállításai a VS Code-ban.":::

    1. Az utasításokat követve válassza ki az előfizetést, a munkaterületet és a távoli számítási példányt. Válassza ki az oktatóanyag során korábban létrehozott munkaterületet és távoli számítási példányt. Lehetősége van új számítási példány létrehozására is.
    1. A számítási példány kiválasztása után a rendszer felszólítja a VS Code-ablak újratöltésére. Az Újratöltés után válassza ki a **Python 3,6-AzureML** kernelt. Most már futtathatja a jegyzetfüzet egyik celláját is. A jegyzetfüzetek cellájának futtatása a jegyzetfüzet és a számítási példány közötti kapcsolatot hozza létre. A rendszer frissíti a jegyzetfüzet eszköztárát, hogy tükrözze azt a számítási példányt, amelyen dolgozik.

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="Kernel kiválasztása a VS Code-ban." lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>A jegyzetfüzet használata

Most már készen áll a notebook futtatására az egyéni Bowl-detektor VS Code-ban való betanításához és a fejlesztői készlet való üzembe helyezéséhez. Győződjön meg arról, hogy a Jegyzetfüzet minden celláját egyenként futtatja, mivel egyes cellákhoz a parancsfájl végrehajtása előtt bemeneti paramétereket kell megadni. A bemeneti paramétereket igénylő cellákat közvetlenül a jegyzetfüzetben lehet szerkeszteni. Cella futtatásához kattintson a cella bal oldalán található lejátszás ikonra:

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="Jegyzetfüzet kiemelése cella ikonja":::

## <a name="next-steps"></a>Következő lépések

További Azure Machine Learning Service-példákat bemutató jegyzetfüzetekhez tekintse meg [ezt a](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)tárházat.
