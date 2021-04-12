---
title: Kapcsolódás a számítási példányhoz a Visual Studio Code-ban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan csatlakozhat egy Azure Machine Learning számítási példányhoz a Visual Studio Code-ban az interaktív Jupyter Notebook és a távoli fejlesztési feladatok futtatásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 04/08/2021
ms.openlocfilehash: 14f0d15d48193267c224f3497c24651ca3249b0b
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028590"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Kapcsolódás Azure Machine Learning számítási példányhoz a Visual Studio Code-ban (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan csatlakozhat egy Azure Machine Learning számítási példányhoz a Visual Studio Code használatával.

Az [Azure Machine learning számítási példány](concept-compute-instance.md) egy teljes körűen felügyelt felhőalapú munkaállomás az adatszakértők számára, és felügyeleti és vállalati készültségi képességeket biztosít a rendszergazdák számára.

A Visual Studio Code-ból kétféleképpen lehet csatlakozni a számítási példányokhoz:

* Távoli számítási példány. Ez a lehetőség teljes funkcionalitású fejlesztési környezetet biztosít a gépi tanulási projektek létrehozásához.
* Távoli Jupyter Notebook-kiszolgáló. Ez a beállítás lehetővé teszi a számítási példányok távoli Jupyter Notebook kiszolgálóként való beállítását.

## <a name="configure-a-remote-compute-instance"></a>Távoli számítási példány konfigurálása

A távoli számítási példányok fejlesztéshez való konfigurálásához néhány előfeltételre van szükség.

* Azure Machine Learning Visual Studio Code-bővítményt. További információ: [Azure Machine learning Visual Studio Code Extension telepítési útmutatója](tutorial-setup-vscode-extension.md).
* Azure Machine Learning munkaterület. [Hozzon létre egy új munkaterületet a Visual Studio Code Azure Machine learning használatával,](how-to-manage-resources-vscode.md#create-a-workspace) ha még nem rendelkezik ilyennel.
* Azure Machine Learning számítási példány. [Hozzon létre egy új számítási példányt az Azure Machine learning Visual Studio Code bővítménnyel,](how-to-manage-resources-vscode.md#create-compute-instance) ha még nem rendelkezik ilyennel.

A távoli számítási példányhoz való kapcsolódáshoz:

# <a name="vs-code"></a>[VS Code](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Azure Machine Learning bővítmény

1. A VS Code-ban indítsa el a Azure Machine Learning bővítményt.
1. Bontsa ki a bővítményben a **számítási példányok** csomópontot.
1. Kattintson a jobb gombbal arra a számítási példányra, amelyhez csatlakozni szeretne, és válassza a **Kapcsolódás a számítási példányhoz** lehetőséget.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Kapcsolódás számítási példányhoz Visual Studio Code Azure ML-bővítmény" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>Parancs paletta

1. A VS Code-ban nyissa meg a parancssort a **> parancs-paletta megtekintése** lehetőség kiválasztásával.
1. A szövegmezőbe írja be az **Azure ml: Kapcsolódás számítási példányhoz** lehetőséget.
1. Válassza ki előfizetését.
1. Válassza ki a munkaterületet.
1. Válassza ki a számítási példányt, vagy hozzon létre egy újat.

# <a name="studio"></a>[Studio](#tab/studio)

Navigáljon a [ml.Azure.com](https://ml.azure.com)

> [!IMPORTANT]
> Ha a Visual Studio Code-ból szeretne csatlakozni a távoli számítási példányhoz, győződjön meg arról, hogy a Azure Machine Learning studióba bejelentkezett fiók ugyanaz, mint a Visual Studio Code-ban.

### <a name="compute"></a>Compute

1. A **számítás** lap kiválasztása
1. Az *alkalmazás URI* oszlopában válassza ki a **vs Code** elemet ahhoz a számítási példányhoz, amelyhez csatlakozni szeretne.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="Kapcsolódás a számítási példányhoz VS Code Azure ML Studio" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Jegyzetfüzet

1. Válassza a **Jegyzetfüzet** fület
1. A *Jegyzetfüzet* lapon válassza ki a szerkeszteni kívánt fájlt.
1. Válassza **a szerkesztők > szerkesztés a vs Code-ban (előzetes verzió)** lehetőséget.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="Kapcsolódás a számítási példányhoz VS Code Azure ML notebook" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

---

A távoli számítási példányhoz új ablak jelenik meg. Távoli számítási példányhoz való kapcsolódás megkísérlése esetén a következő feladatok zajlanak le:

1. Engedélyezés. Egyes ellenőrzések végrehajtásával meggyőződhet arról, hogy a felhasználó megpróbál-e csatlakozni a számítási példány használatára.
1. A VS Code távoli kiszolgáló telepítve van a számítási példányon.
1. Létrejön egy WebSocket-kapcsolat a valós idejű interakcióhoz.

A kapcsolatok létrejötte után a rendszer megőrzi azt. A rendszer a munkamenet elején állítja ki a tokent, amely automatikusan frissül, hogy fenntartsa a kapcsolatot a számítási példánnyal.

A távoli számítási példányhoz való kapcsolódás után a következő paranccsal használhatja a szerkesztőt:

* [Fájlok létrehozása és kezelése a távoli számítási példányon vagy fájlmegosztáson](https://code.visualstudio.com/docs/editor/codebasics).
* A [vs Code integrált terminál](https://code.visualstudio.com/docs/editor/integrated-terminal) használatával [parancsokat és alkalmazásokat futtathat a távoli számítási példányon](how-to-access-terminal.md).
* [Parancsfájlok és alkalmazások hibakeresése](https://code.visualstudio.com/Docs/editor/debugging)
* [A VS Code használata a git-adattárak kezeléséhez](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Számítási példány konfigurálása távoli jegyzetfüzet-kiszolgálóként

A számítási példányok távoli Jupyter Notebook kiszolgálóként való konfigurálásához néhány előfeltételre van szükség:

* Azure Machine Learning Visual Studio Code-bővítményt. További információ: [Azure Machine learning Visual Studio Code Extension telepítési útmutatója](tutorial-setup-vscode-extension.md).
* Azure Machine Learning munkaterület. [Hozzon létre egy új munkaterületet a Visual Studio Code Azure Machine learning használatával,](how-to-manage-resources-vscode.md#create-a-workspace) ha még nem rendelkezik ilyennel.

Kapcsolódás számítási példányhoz:

1. Nyisson meg egy Jupyter Notebook a Visual Studio Code-ban.
1. Ha az integrált jegyzetfüzet-élmény betöltődik, válassza a **Jupyter-kiszolgáló** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning távoli Jupyter Notebook kiszolgáló legördülő menüjének elindítása](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Azt is megteheti, hogy a parancs-palettát is használja:

    1. Nyissa meg a parancssort úgy, hogy kijelöli a menüsávban látható **> a parancs-palettát** .
    1. Adja meg a szövegmezőt `Azure ML: Connect to Compute instance Jupyter server` .

1. Válassza ki `Azure ML Compute Instances` a Jupyter-kiszolgáló beállításai közül a listából.
1. Válassza ki az előfizetését az előfizetések listájából. Ha korábban már konfigurálta az alapértelmezett Azure Machine Learning munkaterületet, ez a lépés kimarad.
1. Válassza ki a munkaterületet.
1. Válassza ki a számítási példányt a listából. Ha még nem rendelkezik ilyennel, válassza az **új Azure ml Compute-példány létrehozása** lehetőséget, és kövesse az utasításokat, és hozzon létre egyet.
1. A módosítások életbe léptetéséhez újra kell töltenie a Visual Studio Code-ot.
1. Nyisson meg egy Jupyter Notebook, és futtasson egy cellát.

> [!IMPORTANT]
> A kapcsolat létrehozásához egy cellát **kell** futtatnia.

Ezen a ponton továbbra is futtathat cellákat a Jupyter Notebook.

> [!TIP]
> A Python-szkripteket (. Jupyter) is használhatja. További információt a [Visual Studio Code Python interaktív dokumentációjában](https://code.visualstudio.com/docs/python/jupyter-support-py)talál.

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a Visual Studio Code Remote-t, a Visual Studio Code-ból távoli számításként használhat számítási példányt a [kód interaktív hibakereséséhez](how-to-debug-visual-studio-code.md).

[Oktatóanyag: az első ml-modell betanítása](tutorial-1st-experiment-sdk-train.md) azt mutatja be, hogyan használható a számítási példány egy integrált jegyzetfüzettel.
