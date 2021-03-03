---
title: Ismerkedés az Azure Percept fejlett fejlesztésével a felhőben
description: Ismerkedjen meg a felhőalapú fejlett fejlesztéssel a Jupyter notebookok és a Azure Machine Learning használatával
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664798"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Ismerkedés a fejlett fejlesztéssel a felhőben Jupyter notebookok és Azure Machine Learning használatával

Ez a cikk végigvezeti egy Azure Machine Learning munkaterület beállításán, az előre konfigurált minta Jupyter Notebook a munkaterületen való feltöltésének, a számítási példány létrehozásának és a jegyzetfüzet celláinak a munkaterületen belüli futtatásának folyamatán.

A [Jegyzetfüzet](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) egy előre betanított TensorFlow-modellel (MobileNetSSDV2Lite) végzi a tanulást a Pythonban lévő AzureML, és egy egyéni adatkészlet segítségével felismeri a tálakat.

A jegyzetfüzetből megtudhatja, hogyan kezdheti el a [kókuszdiót](https://cocodataset.org/#home), hogy csak az érdeklődési osztályra (Bowls) szűrje, majd a megfelelő formátumba alakítsa át. Azt is megteheti, hogy a nyílt forráskódú [VoTT 2](https://github.com/microsoft/VoTT) címkézési eszközzel létrehozza és címkézi a határoló MEZŐKET a Pascal VOC formátumban.

Az egyéni adatkészlet modellének átképzése után a modell ezután üzembe helyezhető az Azure Percept DK-ben a modul Twin Update metódusának használatával.

Ezután megtekintheti a modell következtetéseit, ha megtekinti az élő RTSP streamet az Azure Percept-vízióban. A modell újraképzése és üzembe helyezése a felhőben található jegyzetfüzeten belül történik.

## <a name="prerequisites"></a>Előfeltételek

- [Előfizetés Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [Az Azure Percept DK és az Azure Percept jövőképe SoM csatlakoztatva](./overview-azure-percept-dk.md)
- [Az Azure PERCEPT DK helyszíni](./quickstart-percept-dk-set-up.md) betöltési élménye befejeződött

## <a name="download-azure-percept-github-repository"></a>Az Azure Percept GitHub-adattár letöltése

1. Nyissa meg az [Azure Percept GitHub-tárházat](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).

1. A tárház klónozása vagy a ZIP-fájl letöltése. A képernyő felső részén kattintson a **kód**  ->  **zip letöltése** elemre.

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="GitHub-letöltési képernyő.":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Azure Machine Learning portál és jegyzetfüzet beállítása

1. Nyissa meg a [Azure Machine learning portált](https://ml.azure.com).

1. Válassza ki a címtárat, az Azure-előfizetést és a Machine Learning munkaterületet a legördülő menüből, és kattintson az első **lépések** elemre.

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Azure ML első lépések képernyője.":::

    Ha még nem rendelkezik Azure Machine Learning munkaterülettel, kattintson az **Új munkaterület létrehozása** lehetőségre. Az új böngésző lapon tegye a következőket:

    1. Válassza ki Azure-előfizetését.
    1. Válassza ki az erőforráscsoportot.
    1. Adja meg a munkaterület nevét.
    1. Válassza ki a régiót.
    1. Válassza ki a munkaterület kiadását.
    1. Kattintson az **Áttekintés és létrehozás** elemre.
    1. A következő oldalon tekintse át a kiválasztott beállításokat, majd kattintson a **Létrehozás** gombra.

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Munkaterület-létrehozási képernyő az Azure ML-ben.":::

    Engedélyezzen néhány percet a munkaterület létrehozásához. A munkaterület létrehozásának befejezése után zöld pipa jelek jelennek meg az erőforrások mellett, és a **központi telepítés** a Machine learning Services áttekintése oldal tetején található.

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="Munkaterület-létrehozás megerősítése." lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    Miután befejeződött a munkaterület létrehozása, térjen vissza a Machine learning-portál lapra, és kattintson az első **lépések** elemre.

1. A Machine learning-munkaterület kezdőlapján kattintson a **jegyzetfüzetek** elemre a bal oldali ablaktáblán.

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Azure ML-Kezdőlap.":::

1. A **saját fájlok** lapon kattintson a függőleges nyílra a. ipynb fájl feltöltéséhez.

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="A fájl feltöltése ikon kiemelése.":::

1. Navigáljon a [Transferlearningusing_SSDLiteV2 Model. ipynb fájlra](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) , és válassza ki az Azure Percept GitHub-tárház helyi példányát. Kattintson a **Megnyitás** gombra. A **fájlok feltöltése** ablakban jelölje be a **Megbízom a fájlból** elem melletti jelölőnégyzetet, majd kattintson a **feltöltés** gombra.

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="Fájl kiválasztása képernyő":::

1. A jobb felső menüsorban keresse meg a **számítási** állapotot. Ha nem talál számítást, kattintson az **+** ikonra egy új számítás létrehozásához.

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="A számítási állapot + ikon kiemelve." lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. Az **új számítási példány** ablakban adja meg a **számítási nevet**, válassza ki a **virtuális gép típusát**, és válassza ki a **virtuális gép méretét**. Kattintson a **Létrehozás** lehetőségre.

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="Új számítási példány létrehozási képernyője.":::

    Miután rákattintott a **Létrehozás** gombra, a **számítási** állapot egy kék kört és egy **\<your compute name> -létrehozási** értéket fog megjeleníteni

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="Számítási állapot, ha a számítási létrehozás még folyamatban van.":::

    A **számítási** állapot egy zöld kört fog megjeleníteni, és a számítási létrehozás befejezése után **\<your compute name> fut** .

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="A számítási állapotot bemutató számítási állapot befejeződött.":::

1. Miután a számítás fut, válassza a **Python 3,6-AzureML** kernel elemet a ikon melletti legördülő menüből **+** .

## <a name="working-with-the-notebook"></a>A jegyzetfüzet használata

Most már készen áll a notebook futtatására az egyéni Bowl-detektor betanításához és a fejlesztői készlet való üzembe helyezéséhez. Győződjön meg arról, hogy a Jegyzetfüzet minden celláját egyenként futtatja, mivel egyes cellákhoz a parancsfájl végrehajtása előtt bemeneti paramétereket kell megadni. A bemeneti paramétereket igénylő cellákat közvetlenül a jegyzetfüzetben lehet szerkeszteni. Cella futtatásához kattintson a cella bal oldalán található lejátszás ikonra:

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="Jegyzetfüzet kiemelése cella ikonja" lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>Következő lépések

További Azure Machine Learning Service-példákat bemutató jegyzetfüzetekhez tekintse meg [ezt a](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml) tárházat
