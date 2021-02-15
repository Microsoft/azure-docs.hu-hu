---
title: 'Oktatóanyag: Jupyter-jegyzetfüzetek (Python) – első lépések'
titleSuffix: Azure Machine Learning
description: Jupyter Notebook oktatóanyagok beállítása. Hozzon létre egy munkaterületet, klónozott jegyzetfüzeteket a munkaterületen, és hozzon létre egy számítási példányt, amelyen futtatja a jegyzetfüzeteket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: b597c483354a4bc1763e7b6a4bb3727aaaaf0c51
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369203"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Oktatóanyag: első lépések a Azure Machine Learning Jupyter-jegyzetfüzetekben

Ebben az oktatóanyagban elvégezheti a Azure Machine Learning használatának első lépéseit a [felügyelt felhőalapú munkaállomásokon (számítási példányon)](concept-compute-instance.md)lévő Jupyter notebookok használatával. Ez az oktatóanyag az összes többi Jupyter Notebook oktatóanyag előfutára.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy [Azure Machine learning munkaterületet](concept-workspace.md) más Jupyter notebook oktatóanyagokban való használathoz.
> * Az oktatóanyagok notebookjának klónozása a munkaterületen lévő mappába.
> * Hozzon létre egy felhőalapú számítási példányt a Azure Machine Learning Python SDK-val telepített és előre konfigurált.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning munkaterület a felhőben található alapvető erőforrás, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használható. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi a szolgáltatásban.

Ha már rendelkezik Azure Machine Learning munkaterülettel, ugorjon a [notebook klónozása mappába](#clone) .  

[A munkaterület többféleképpen is létrehozható](how-to-manage-workspace.md).  Ebben az oktatóanyagban egy munkaterületet hoz létre az Azure-erőforrások kezeléséhez használható webalapú konzolon Azure Portal használatával.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Jegyezze fel a *munkaterületet* és az *előfizetést*. Ezekre az információkra szüksége lesz, hogy a megfelelő helyen hozza létre a kísérletet.

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>Jegyzetfüzet futtatása a munkaterületen

Azure Machine Learning tartalmaz egy felhőalapú notebook-kiszolgálót a munkaterületen egy install-Free és előre konfigurált felülettel. Ha a környezet, a csomagok és a függőségek felügyeletét szeretné használni, használja [saját környezetét](tutorial-1st-experiment-sdk-setup-local.md) .

 Kövesse ezt a videót, vagy használja a részletes lépéseket az oktatóanyag notebookjának a munkaterületről történő klónozásához és futtatásához.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a> Jegyzetfüzet-mappa klónozása

Hajtsa végre a következő kísérlet beállítását, és futtassa a lépéseket Azure Machine Learning Studióban. Ez az összevont felület olyan gépi tanulási eszközöket tartalmaz, amelyekkel adatelemzési forgatókönyvek végezhetők el az adatelemző szakemberek számára az összes képzettségi szinten.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/).

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

1. A bal oldalon válassza a **jegyzetfüzetek** lehetőséget.

1. A felső részen válassza a **minták** lapot.

1. Nyissa meg a **Python** mappát.

1. Nyissa meg a mappát egy verziószámmal. Ez a szám a Python SDK jelenlegi kiadását jelöli.

1. Kattintson az **oktatóanyagok** mappa jobb oldalán található **...** gombra, majd válassza a **klónozás** lehetőséget.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="A klón oktatóanyagok mappát megjelenítő képernyőkép.":::

1. A mappák listája megjeleníti azokat a felhasználókat, akik hozzáférnek a munkaterülethez. Válassza ki azt a mappát, amelybe az **oktatóanyagok**  mappát el szeretné klónozott.

### <a name="open-the-cloned-notebook"></a><a name="open"></a>A klónozott jegyzetfüzet megnyitása

1. Nyissa meg a **felhasználói fájlok** szakaszban bezárt **oktatóanyagok** mappát.

    > [!IMPORTANT]
    > A jegyzetfüzeteket megtekintheti a **Samples** mappában, de onnan nem futtathat jegyzetfüzetet. Jegyzetfüzet futtatásához győződjön meg arról, hogy a **felhasználói fájlok** szakaszban megnyitta a jegyzetfüzet klónozott verzióját.
    
1. Válassza ki az **IMG-besorolás-part1-Training. ipynb** fájlt az **oktatóanyagok/rendszerkép-besorolás-mnist-adat** mappában.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Képernyőkép, amely az Open oktatóanyagok mappát mutatja.":::

1. A felső sávon válassza ki a jegyzetfüzet futtatásához használni kívánt számítási példányt. Ezek a virtuális gépek előre konfigurálva vannak a [Azure Machine learning futtatásához szükséges összes](concept-compute-instance.md#contents)szolgáltatással.

1. Ha nem található virtuális gép, válassza a **+ Hozzáadás** elemet a számítási példány virtuális gép létrehozásához.

    1. Amikor létrehoz egy virtuális gépet, kövesse az alábbi szabályokat:
 
        + A név megadása kötelező, és a mező nem lehet üres.
        + A névnek egyedinek (kis-és nagybetűket nem megkülönböztető módon) kell lennie a munkaterület vagy a számítási példány Azure-régiójában lévő összes meglévő számítási példányon. Ha a választott név nem egyedi, akkor riasztást kap.
        + Az érvényes karakterek a kisbetűk és a kisbetűk, a 0 és 9 közötti számok, valamint a kötőjel (-) karakter.
        + A névnek 3 – 24 karakter hosszúnak kell lennie.
        + A névnek betűvel kell kezdődnie, nem lehet szám vagy kötőjel karakter.
        + Kötőjel karakter használata esetén a kötőjel után legalább egy betűt kell követnie. Például a test-, test-0, test-01 érvénytelen, míg a test-a0, test-0a érvényes példányok.

    1. Válassza ki a virtuális gép méretét a rendelkezésre álló lehetőségek közül. Az oktatóanyagok esetében az alapértelmezett virtuális gép jó választás.

    1. Ezután kattintson a **Létrehozás** elemre. A virtuális gép beállítása körülbelül öt percet vesz igénybe.

1. Ha a virtuális gép elérhető, megjelenik a felső eszköztáron. Most már futtathatja a jegyzetfüzetet az eszköztáron az **összes futtatása** lehetőséggel, vagy a **SHIFT + ENTER billentyűkombinációt** a jegyzetfüzet kódjában.

Ha egyéni widgetekkel rendelkezik, vagy szívesebben szeretné használni a Jupyter vagy a JupyterLab, válassza a jobb szélen a **Jupyter** legördülő listát. Ezután válassza a **Jupyter** vagy a **JupyterLab** lehetőséget. Megnyílik az új böngészőablak.

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a fejlesztési környezetet, folytassa a modell betanítását Jupyter Notebook.

> [!div class="nextstepaction"]
> [Oktatóanyag: képosztályozási modellek betanítása MNIST-adatokkal és scikit-Learn](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Ha nem tervezi a további oktatóanyagok követését, állítsa le a Felhőbeli notebook Server virtuális gépet, ha nem használja a költségeket.

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
