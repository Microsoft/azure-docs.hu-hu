---
title: Erőforrások létrehozása és kezelése VS Code-bővítmény (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és kezelhet Azure Machine Learning erőforrásokat a Visual Studio Code Azure Machine Learning használatával.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 11/16/2020
ms.openlocfilehash: f8eb18b190b72381f1a93575eb39b3d19d8d431b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96010673"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>Azure Machine Learning erőforrások kezelése a VS Code bővítménnyel (előzetes verzió)

Ismerje meg, hogyan kezelheti Azure Machine Learning erőforrásait a VS Code bővítménnyel.

![Azure Machine Learning VS Code-bővítmény](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-előfizetés. Ha még nem rendelkezik ilyennel, regisztráljon a [Azure Machine learning ingyenes vagy fizetős verziójának](https://aka.ms/AMLFree)kipróbálásához.
- Visual Studio Code. Ha nincs telepítve, [telepítse](https://code.visualstudio.com/docs/setup/setup-overview).
- VS Code Azure Machine Learning bővítmény. A bővítmény telepítéséhez kövesse a [Azure Machine learning vs Code bővítmény telepítési útmutatóját](tutorial-setup-vscode-extension.md#install-the-extension) .

Az alábbi folyamatok feltételezik, hogy a Visual Studio Code Azure Machine Learning nézetében van. A bővítmény indításához válassza az **Azure** ikont a vs Code tevékenység sávján.

## <a name="workspaces"></a>Munkaterületek

További információ: [munkaterületek](concept-workspace.md).

### <a name="create-a-workspace"></a>Munkaterület létrehozása

1. A Azure Machine Learning nézetben kattintson a jobb gombbal az előfizetés csomópontjára, és válassza a **Munkaterület létrehozása** lehetőséget.
1. A parancssorban:
    1. Adja meg a munkaterület nevét
    1. Azure-előfizetés kiválasztása
    1. Válasszon ki vagy hozzon létre egy új erőforráscsoportot a munkaterület kiépítéséhez
    1. Válassza ki azt a helyet, ahová a munkaterületet kiépíteni szeretné.

A munkaterület létrehozásának alternatív módszerei a következők:

- Nyissa meg a parancs paletta **nézetét > a Command paletta** parancsot, és írja be az **Azure ml: Create Workspace (munkaterület létrehozása**) szöveget.
- Kattintson a `+` Azure Machine learning nézet tetején található ikonra.
- Hozzon létre egy új munkaterületet, amikor a rendszer kéri, hogy válasszon ki egy munkaterületet más erőforrások kiépítés során.

### <a name="remove-a-workspace"></a>Munkaterület eltávolítása

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Kattintson a jobb gombbal az eltávolítani kívánt munkaterületre.
1. Válassza ki, hogy el szeretné-e távolítani a következőket:
    - *Csak a munkaterület*: Ez a beállítás **csak** a munkaterület Azure-erőforrását törli. Az erőforráscsoport, a Storage-fiókok és minden más olyan erőforrás, amelyhez a munkaterület hozzá lett csatolva, még mindig az Azure-ban található.
    - *Társított erőforrásokkal*: Ez a lehetőség törli a munkaterületet **és** az ahhoz társított összes erőforrást.

## <a name="datastores"></a>Adattárak

A VS Code bővítmény jelenleg a következő típusú adattárolókat támogatja:

- Azure-fájlmegosztás
- Azure Blob Storage

Munkaterületek létrehozásakor létrejön egy adattár az egyes típusokhoz.

[További információ:](concept-data.md#datastores)adattárolók.

### <a name="create-a-datastore"></a>Adattár létrehozása

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki azt a munkaterület-csomópontot, amelyben létre szeretné hozni az adattárt.
1. Kattintson **a jobb** gombbal az adattárolók csomópontra, majd válassza az **adattár regisztrálása** lehetőséget.
1. A parancssorban:
    1. Adja meg az adattár nevét.
    1. Válassza ki az adattár típusát.
    1. Válassza ki a tárolási erőforrást. Kiválaszthat egy, a munkaterülethez társított tárolási erőforrást, vagy választhat az Azure-előfizetésekben található bármely érvényes tárolási erőforrás közül.
    1. Válassza ki azt a tárolót, ahol az adatai a korábban kiválasztott tárolási erőforráson belül vannak.
1. A VS Code-ban megjelenik egy konfigurációs fájl. Ha elégedett a konfigurációs fájllal, válassza a **Mentés és folytatás** lehetőséget, vagy nyissa meg a vs Code parancs-palettát (**tekintse meg > Command paletta**), és írja be az **Azure ml: mentés és folytatás** gombot.

### <a name="manage-a-datastore"></a>Adattár kezelése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a munkaterület csomópontot.
1. Bontsa ki az **adattár** csomópontot a munkaterületen belül.
1. Válassza ki a kívánt adattárt:
    - *Beállítás alapértelmezettként*. A kísérletek futtatásakor ez lesz a használni kívánt adattár.
    - *Csak olvasható beállítások ellenőrzése*.
    - *Modify*. Módosítsa a hitelesítési típust és a hitelesítő adatokat. A támogatott hitelesítési típusok közé tartozik a fiók kulcsa és az SAS-token.

## <a name="datasets"></a>Adathalmazok

A bővítmény jelenleg a következő adatkészlet-típusokat támogatja:

- *Táblázatos*: lehetővé teszi, hogy az adatokat egy DataFrame (pandák vagy PySpark) megvalósuljon.
- *Fájl*: fájl vagy fájl gyűjteménye. Lehetővé teszi fájlok letöltését vagy csatlakoztatását a számítási feladatokhoz.

További információ: [adatkészletek](concept-data.md#datasets)

### <a name="create-dataset"></a>Adatkészlet létrehozása

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki azt a munkaterület-csomópontot, amelyben létre szeretné hozni az adattárt.
1. Kattintson a jobb gombbal az **adatkészletek** csomópontra, majd válassza az **adatkészlet létrehozása** lehetőséget.
1. A parancssorban:
    1. Válassza ki az adatkészlet típusát
    1. Annak megadása, hogy az adatai a számítógépen vagy a weben találhatók-e
    1. Adja meg az adatai helyét. Ez lehet egyetlen fájl vagy az adatfájlokat tartalmazó könyvtár.
    1. Válassza ki azt az adattárat, amelybe fel szeretné tölteni az adatait.
    1. Adjon meg egy előtagot, amely segít azonosítani az adatkészletet az adattárban.

### <a name="version-datasets"></a>Verziók adatkészletei

Gépi tanulási modellek létrehozásakor az adatváltozások esetében érdemes lehet az adatkészlet verzióját használni. Ehhez a VS Code bővítményben:

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a munkaterület csomópontot.
1. Bontsa ki az **adatkészletek** csomópontot.
1. Kattintson a jobb gombbal a verziót használni kívánt adatkészletre, és válassza az **új verzió létrehozása** lehetőséget.
1. A parancssorban:
    1. Válassza ki az adatkészlet típusát
    1. Megadhatja, hogy az adatai a számítógépen vagy a weben találhatók-e.
    1. Adja meg az adatai helyét. Ez lehet egyetlen fájl vagy az adatfájlokat tartalmazó könyvtár.
    1. Válassza ki azt az adattárat, amelybe fel szeretné tölteni az adatait.
    1. Adjon meg egy előtagot, amely segít azonosítani az adatkészletet az adattárban.

### <a name="view-dataset-properties"></a>Adatkészlet tulajdonságainak megtekintése

Ezzel a beállítással megtekintheti az adott adatkészlethez társított metaadatokat. Ehhez a VS Code bővítményben:

1. Bontsa ki a munkaterület csomópontot.
1. Bontsa ki az **adatkészletek** csomópontot.
1. Kattintson a jobb gombbal a megvizsgálni kívánt adatkészletre, és válassza az **adatkészlet tulajdonságainak megtekintése** lehetőséget. Ekkor megjelenik egy konfigurációs fájl, amely az adatkészlet legújabb verziójának tulajdonságait mutatja.

> [!NOTE]
> Ha az adatkészlet több verzióját is tartalmazza, úgy is dönthet, hogy csak egy adott verzió adatkészlet-tulajdonságait szeretné megtekinteni. Ehhez bontsa ki az adatkészlet csomópontot, és hajtsa végre a jelen szakaszban ismertetett lépéseket a fontos verzióban.

### <a name="unregister-datasets"></a>Adatkészletek regisztrációjának törlése

Ha el szeretné távolítani egy adatkészletet és annak összes verzióját, törölje a regisztrációját. Ehhez a VS Code bővítményben:

1. Bontsa ki a munkaterület csomópontot.
1. Bontsa ki az **adatkészletek** csomópontot.
1. Kattintson a jobb gombbal a regisztrálni kívánt adatkészletre, és válassza az **adatkészlet regisztrációjának törlése** lehetőséget.

## <a name="environments"></a>Környezetek

További információ: [környezetek](concept-environments.md).

### <a name="create-environment"></a>Környezet létrehozása

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki azt a munkaterület-csomópontot, amelyben létre szeretné hozni az adattárt.
1. Kattintson a jobb gombbal a **környezetek** csomópontra, és válassza a **környezet létrehozása** lehetőséget.
1. A parancssorban:
    1. Adja meg a környezet nevét
    1. Adja meg a környezet konfigurációját:
        - *Kurátori környezetek*: előre konfigurált környezetek Azure Machine Learningban. A környezet további testreszabásához módosítsa a `dependencies` tulajdonságot a JSON-fájlban. További információ a [kurátori környezetekről](resource-curated-environments.md).
        - *Conda-függőségek fájlja*: Anaconda-környezetek esetén a környezeti definícióját tartalmazó fájl adható meg.
        - *Pip-követelmények fájlja*: a pip-környezetekhez meg lehet adni a környezet definícióját tartalmazó fájlt.
        - *Meglévő Conda-környezet*: Ez a beállítás a helyi számítógép Conda környezeteit keresi, és megpróbál létrehozni egy környezetet a kiválasztott környezetből.
        - *Egyéni*: saját csatornák és függőségek definiálása
    1. Megnyílik egy konfigurációs fájl a szerkesztőben. Ha elégedett a konfigurációval, válassza a **Mentés és folytatás** lehetőséget, vagy nyissa meg a vs Code parancs-palettát (**tekintse meg > Command paletta**), és írja be az **Azure ml: mentés és folytatás** gombot.

### <a name="view-environment-configurations"></a>Környezeti konfigurációk megtekintése

Egy adott környezet függőségeinek és konfigurációinak megtekintése a bővítményben:

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a munkaterület csomópontot.
1. Bontsa ki a **környezetek** csomópontot.
1. Kattintson a jobb gombbal a megtekinteni kívánt környezetre, és válassza a **környezet megtekintése** lehetőséget.

### <a name="edit-environment-configurations"></a>Környezeti konfigurációk szerkesztése

Egy adott környezet függőségeinek és konfigurációinak szerkesztése a bővítményben:

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **környezetek** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a megtekinteni kívánt környezetre, és válassza a **környezet szerkesztése** lehetőséget.
1. Ha elégedett a konfigurációval, a módosítások elvégzése után válassza a **Mentés és folytatás** lehetőséget, vagy nyissa meg a vs Code parancs-palettát (**tekintse meg > Command paletta**), és írja be az **Azure ml: Save és Continue** parancsot.

## <a name="experiments"></a>Kísérletek

További információ: [kísérletek](concept-azure-machine-learning-architecture.md#experiments).

### <a name="create-experiment"></a>Kísérlet létrehozása

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a munkaterület csomópontot.
1. Kattintson a jobb gombbal a **kísérletek** csomópontra a munkaterületen, majd válassza a **kísérlet létrehozása** lehetőséget.
1. A parancssorban adja meg a kísérlet nevét.

### <a name="run-experiment"></a>Kísérlet futtatása

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **kísérletek** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a futtatni kívánt kísérletre.
1. Válassza a **kísérlet futtatása** ikont a tevékenység sávján.
1. Válassza ki, hogy helyileg vagy távolról kívánja-e futtatni a kísérletet. A kísérletek helyi futtatásával és hibakeresésével kapcsolatos további információkért tekintse meg a [hibakeresési útmutatót](how-to-debug-visual-studio-code.md) .
1. Válassza ki az előfizetését.
1. Válassza ki az Azure-ML-munkaterület a kísérlet futtatásához.
1. Válassza ki a kísérletet.
1. Válasszon ki vagy hozzon létre egy számítási lehetőséget a kísérlet futtatásához.
1. Válasszon ki vagy hozzon létre egy futtatási konfigurációt a kísérlethez.

Azt is megteheti, hogy kijelöli a **kísérlet futtatása** gombot a bővítmény tetején, és a kísérletben futtatja a kísérletet.

### <a name="view-experiment"></a>Kísérlet megtekintése

A kísérlet Azure Machine Learning Studioban való megtekintése:

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **kísérletek** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a megtekinteni kívánt kísérletre, és válassza a **kísérlet megtekintése** lehetőséget. 
1. Megjelenik egy üzenet, amely arra kéri, hogy nyissa meg a kísérlet URL-címét Azure Machine Learning Studióban. Válassza a **Megnyitás** lehetőséget.

### <a name="track-run-progress"></a>Futtatási folyamat követése

A kísérlet futtatásakor érdemes megtekinteni a folyamat előrehaladását. A Azure Machine Learning Studióban futtatott Futtatás előrehaladásának nyomon követése a bővítményből:

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **kísérletek** csomópontot a munkaterületen belül.
1. Bontsa ki a kísérlet csomópontot, amelyen nyomon szeretné követni a folyamat előrehaladását.
1. Kattintson a jobb gombbal a Futtatás elemre, és válassza a **megtekintés Azure Portal**.
1. Megjelenik egy üzenet, amely arra kéri, hogy nyissa meg a futtatási URL-címet Azure Machine Learning Studióban. Válassza a **Megnyitás** lehetőséget.

### <a name="download-run-logs--outputs"></a>Futtatási naplók letöltése & kimenetek

A Futtatás befejezése után előfordulhat, hogy le szeretné tölteni a naplókat és az eszközöket, például a kísérlet részeként létrehozott modellt.

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **kísérletek** csomópontot a munkaterületen belül.
1. Bontsa ki a kísérlet csomópontot, amelyen nyomon szeretné követni a folyamat előrehaladását.
1. Kattintson a jobb gombbal a futtatásra:
    - A kimenetek letöltéséhez válassza a **kimenetek letöltése** lehetőséget.
    - A naplók letöltéséhez válassza a **naplók letöltése** lehetőséget.

### <a name="view-run-metadata"></a>Futtatási metaadatok megtekintése

A bővítményben ellenőrizheti a metaadatokat, például a futtatási konfigurációt, valamint a Futtatás részleteit.

## <a name="compute-instances"></a>Számítási példányok

További információ: [számítási példányok](concept-compute-instance.md).

### <a name="create-compute-instance"></a>Számítási példány létrehozása

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki azt a munkaterület-csomópontot, amelyben a számítási példányt létre szeretné hozni.
1. Kattintson a jobb gombbal a **számítási példányok** csomópontra, és válassza a **számítási példány létrehozása** lehetőséget.
1. A parancssorban:
    1. Adja meg a számítási példány nevét.
    1. Válassza ki a virtuális gép méretét a listából.
    1. Válassza ki, hogy engedélyezni kívánja-e az SSH-hozzáférést.
        1. Ha engedélyezi az SSH-hozzáférést, meg kell adnia a nyilvános SSH-kulcsot vagy a kulcsot tartalmazó fájlt is. További információ: [ssh-kulcsok létrehozása és használata az Azure](../virtual-machines/linux/mac-create-ssh-keys.md)-ban.

### <a name="stop-or-restart-compute-instance"></a>Számítási példány leállítása vagy újraindítása

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **számítási példány** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a leállítani vagy újraindítani kívánt számítási példányra, majd válassza a **számítási példány leállítása** vagy a **számítási példány újraindítása** lehetőséget.

### <a name="view-compute-instance-configuration"></a>Számítási példány konfigurációjának megtekintése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **számítási példány** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal az ellenőrizni kívánt számítási példányra, és válassza a **számítási példány tulajdonságainak megtekintése** lehetőséget.

### <a name="delete-compute-instance"></a>Számítási példány törlése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **számítási példány** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a törölni kívánt számítási példányra, majd válassza a **számítási példány törlése** lehetőséget.

## <a name="compute-clusters"></a>Számítási fürtök

A bővítmény a következő számítási típusokat támogatja:

- Számítási fürt Azure Machine Learning
- Azure Kubernetes Service

További információ: [számítási célok](concept-compute-target.md#train).

### <a name="create-compute"></a>Számítás létrehozása

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki azt a munkaterület-csomópontot, amelyben létre szeretné hozni a számítási fürtöt.
1. Kattintson a jobb gombbal a **számítási fürtök** csomópontra, és válassza a **számítás létrehozása** lehetőséget.
1. A parancssorban:
    1. Számítási típus kiválasztása
    1. Válassza ki a virtuális gép méretét. További információ a [virtuális gépek méreteiről](../virtual-machines/sizes.md).
    1. Adja meg a számítás nevét.

### <a name="view-compute-configuration"></a>Számítási konfiguráció megtekintése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **számítási fürtök** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a megtekinteni kívánt számításra, majd válassza a **számítási tulajdonságok megtekintése** lehetőséget.

### <a name="edit-compute-scale-settings"></a>Számítási méretezési beállítások szerkesztése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **számítási fürtök** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a szerkeszteni kívánt számítási feladatokra, majd válassza a **számítás szerkesztése** lehetőséget.
1. A rendszer a szerkesztőben megnyílik egy konfigurációs fájl a számítási feladatokhoz. Ha elégedett a konfigurációval, válassza a **Mentés és folytatás** lehetőséget, vagy nyissa meg a vs Code parancs-palettát (**tekintse meg > Command paletta**), és írja be az **Azure ml: mentés és folytatás** gombot.

### <a name="delete-compute"></a>Számítás törlése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **számítási fürtök** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a törölni kívánt számításra, majd válassza a **számítás törlése** lehetőséget.

### <a name="create-run-configuration"></a>Futtatási konfiguráció létrehozása

Futtatási konfiguráció létrehozása a bővítményben:

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **számítási fürtök** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal arra a számítási célra, amelyben a futtatási konfigurációt létre szeretné hozni, majd válassza a **futtatási konfiguráció létrehozása** lehetőséget.
1. A parancssorban:
    1. Adja meg a számítási cél nevét
    1. Válasszon ki vagy hozzon létre egy új környezetet.
    1. Írja be a futtatni kívánt parancsfájl nevét, vagy nyomja le az **ENTER** billentyűt a böngészőben a helyi számítógépen lévő parancsfájlhoz.
    1. Választható Válassza ki, hogy szeretne-e adathivatkozást létrehozni a betanítási futtatáshoz. Ezzel a művelettel megkérdezi, hogy definiáljon-e egy adatkészletet a futtatási konfigurációban.
        1. Válasszon az egyik regisztrált adatkészletből, hogy az adatkészlethez tartozó konfigurációs fájl futtatásához a szerkesztőben nyíljon meg. Ha elégedett a konfigurációval, válassza a **Mentés és folytatás** lehetőséget, vagy nyissa meg a vs Code parancs-palettát (**tekintse meg > Command paletta**), és írja be az **Azure ml: mentés és folytatás** gombot.
    1. Ha elégedett a konfigurációval, válassza a **Mentés és folytatás** lehetőséget, vagy nyissa meg a vs Code parancs-palettát (**tekintse meg > Command paletta**), és írja be az **Azure ml: mentés és folytatás** gombot.

### <a name="edit-run-configuration"></a>Futtatási konfiguráció szerkesztése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a számítási fürt csomópontot a munkaterület **számítási fürtök** csomópontjában.
1. Kattintson a jobb gombbal a szerkeszteni kívánt futtatási konfigurációra, majd válassza a **futtatási konfiguráció szerkesztése** lehetőséget.
1. A szerkesztőben megnyílik a futtatási konfigurációhoz tartozó konfigurációs fájl. Ha elégedett a konfigurációval, válassza a **Mentés és folytatás** lehetőséget, vagy nyissa meg a vs Code parancs-palettát (**tekintse meg > Command paletta**), és írja be az **Azure ml: mentés és folytatás** gombot.

### <a name="delete-run-configuration"></a>Futtatási konfiguráció törlése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a munkaterület csomópontot.
1. Bontsa ki a számítási fürt csomópontját a **számítási fürtök** csomóponton belül.
1. Kattintson a jobb gombbal a szerkeszteni kívánt futtatási konfigurációra, majd válassza a **futtatási konfiguráció törlése** lehetőséget.

## <a name="models"></a>Modellek

További információ: [modellek](concept-azure-machine-learning-architecture.md#models)

### <a name="register-model"></a>Modell regisztrálása

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a munkaterület csomópontot.
1. Kattintson a jobb gombbal a **modellek** csomópontra, és válassza a **modell regisztrálása** lehetőséget.
1. A parancssorban:
    1. Adja meg a modell nevét
    1. Válassza ki, hogy a modell fájl vagy mappa-e.
    1. Keresse meg a modellt a helyi számítógépen.
    1. Egy konfigurációs fájl a modellhez a szerkesztőben. Ha elégedett a konfigurációval, válassza a **Mentés és folytatás** lehetőséget, vagy nyissa meg a vs Code parancs-palettát (**tekintse meg > Command paletta**), és írja be az **Azure ml: mentés és folytatás** gombot.

### <a name="view-model-properties"></a>Modell tulajdonságainak megtekintése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **modellek** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal arra a modellre, amelynek a tulajdonságait látni szeretné, majd válassza a **modell tulajdonságainak megtekintése** lehetőséget. Megnyílik egy fájl a modell tulajdonságait tartalmazó szerkesztőben.

### <a name="download-model"></a>Modell letöltése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **modellek** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a letölteni kívánt modellre, majd válassza a **Model file letöltése** lehetőséget.

### <a name="delete-a-model"></a>Modell törlése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **modellek** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a törölni kívánt modellre, majd válassza a **modell eltávolítása** lehetőséget.

## <a name="endpoints"></a>Végpontok

A VS Code bővítmény a következő telepítési célokat támogatja:

- Azure Container Instances
- Azure Kubernetes Service

További információ: webszolgáltatás- [végpontok](concept-azure-machine-learning-architecture.md#web-service-endpoint).

### <a name="create-deployments"></a>Központi telepítések létrehozása

> [!NOTE]
> A központi telepítés létrehozása jelenleg csak Conda környezetekben működik.

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a munkaterület csomópontot.
1. Kattintson a jobb gombbal a **végpontok** csomópontra, majd válassza a **szolgáltatás telepítése** lehetőséget.
1. A parancssorban:
    1. Válassza ki, hogy már regisztrált modellt vagy helyi modellt szeretne használni.
    1. Modell kiválasztása
    1. Válassza ki azt a telepítési célt, amelyre telepíteni szeretné a modellt.
    1. Adja meg a modell nevét.
    1. Adja meg a modell Pontozásakor futtatandó parancsfájlt.
    1. Adjon meg egy Conda-függőségi fájlt.
    1. A központi telepítés konfigurációs fájlja megjelenik a szerkesztőben. Ha elégedett a konfigurációval, válassza a **Mentés és folytatás** lehetőséget, vagy nyissa meg a vs Code parancs-palettát (**tekintse meg > Command paletta**), és írja be az **Azure ml: mentés és folytatás** gombot.

> [!NOTE]
> Azt is megteheti, hogy a jobb gombbal egy regisztrált modellre kattint a *modellek* csomópontban, és kiválasztja a **szolgáltatás telepítése a regisztrált modellből** lehetőséget.

### <a name="delete-deployments"></a>Központi telepítések törlése

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **végpontok** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal az eltávolítani kívánt központi telepítésre, majd válassza a **szolgáltatás eltávolítása** lehetőséget.
1. Megjelenik egy üzenet, amely megerősíti, hogy el kívánja távolítani a szolgáltatást. Kattintson az **OK** gombra.

### <a name="manage-deployments"></a>Központi telepítések kezelése

A központi telepítések létrehozása és törlése mellett megtekintheti és szerkesztheti a telepítéshez társított beállításokat is.

1. Bontsa ki a munkaterületet tartalmazó előfizetés csomópontot.
1. Bontsa ki a **végpontok** csomópontot a munkaterületen belül.
1. Kattintson a jobb gombbal a felügyelni kívánt központi telepítésre:
    - A beállítások szerkesztéséhez válassza a **szolgáltatás szerkesztése** lehetőséget.
        - A központi telepítés konfigurációs fájlja megjelenik a szerkesztőben. Ha elégedett a konfigurációval, válassza a **Mentés és folytatás** lehetőséget, vagy nyissa meg a vs Code parancs-palettát (**tekintse meg > Command paletta**), és írja be az **Azure ml: mentés és folytatás** gombot.
    - A központi telepítés konfigurációs beállításainak megtekintéséhez válassza a **szolgáltatás tulajdonságainak megtekintése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

[Képbesorolási modell betanítása](tutorial-train-deploy-image-classification-model-vscode.md) a vs Code bővítménnyel.