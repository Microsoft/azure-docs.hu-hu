---
title: Oktatóanyag – Python-parancsfájlok futtatása Data Factory használatával
description: Megtudhatja, hogyan futtathat Python-parancsfájlokat egy folyamat részeként a Azure Batch használatával Azure Data Factory.
author: pkshultz
ms.devlang: python
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: peshultz
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 241a47ccf9021c6065fea907b4d9914744a64972
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461691"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Oktatóanyag: Python-parancsfájlok futtatása Azure Data Factory használatával Azure Batch

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hitelesítés Batch- és Storage-fiókokkal
> * Parancsfájl fejlesztése és futtatása Pythonban
> * Számításicsomópont-készlet létrehozása alkalmazás futtatásához
> * A Python számítási feladatainak beosztása
> * Az elemzési folyamat monitorozása
> * Hozzáférés a naplófájlokhoz

Az alábbi példa olyan Python-szkriptet futtat, amely egy blob Storage-tárolóból fogad CSV-bemenetet, végrehajt egy adatkezelési folyamatot, és egy külön blob Storage-tárolóba írja a kimenetet.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

* Telepített [Python](https://www.python.org/downloads/) -eloszlás helyi teszteléshez.
* Az [Azure-Storage-blob](https://pypi.org/project/azure-storage-blob/) `pip` csomag.
* Az [iris.csv adatkészlet](https://github.com/Azure-Samples/batch-adf-pipeline-tutorial/blob/master/iris.csv)
* Egy Azure Batch-fiók és egy társított Azure Storage-fiók. A Batch-fiókok Storage-fiókokhoz való létrehozásával és összekapcsolásával kapcsolatos további információkért tekintse meg [a Batch-fiók létrehozása](quick-create-portal.md#create-a-batch-account) című témakört.
* Egy Azure Data Factory-fiók. A adat-előállító létrehozásával kapcsolatos további információkért tekintse meg Azure Portal az adat-előállító [létrehozása](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) című témakört.
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Batch-készlet létrehozása Batch Explorer használatával

Ebben a szakaszban a Batch Explorer használatával hozza létre az Azure-beli adatfeldolgozó-folyamat által használt batch-készletet. 

1. Jelentkezzen be Batch Explorer Azure-beli hitelesítő adataival.
1. Válassza ki a Batch-fiókját
1. Hozzon létre egy készletet a bal oldali sávban található **készletek** kiválasztásával, majd a keresési űrlap fölé a **Hozzáadás** gombra kattintva. 
    1. Válassza ki az azonosítót és a megjelenítendő nevet. `custom-activity-pool`Ezt a példát fogjuk használni.
    1. Állítsa a skálázási típust **rögzített méretre**, és állítsa a dedikált csomópontok száma értéket a 2 értékre.
    1. Az **adatelemzés** területen válassza a **Windows Dsvm** operációs rendszerként lehetőséget.
    1. Válassza ki `Standard_f2s_v2` a virtuális gép méretét.
    1. Engedélyezze az indítási feladatot, és adja hozzá a parancsot `cmd /c "pip install azure-storage-blob pandas"` . A felhasználói identitás az alapértelmezett **készlet felhasználója** maradhat.
    1. Válassza az **OK** lehetőséget.

## <a name="create-blob-containers"></a>BLOB-tárolók létrehozása

Itt olyan blob-tárolókat hoz létre, amelyek a bemeneti és kimeneti fájljait fogják tárolni az OCR batch-feladathoz.

1. Jelentkezzen be Storage Explorer Azure-beli hitelesítő adataival.
1. A Batch-fiókhoz csatolt Storage-fiók használatával hozzon létre két BLOB-tárolót (egyet a bemeneti fájlokhoz, egyet a kimeneti fájlokhoz) a [blob-tároló létrehozása](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container)című témakör lépéseit követve.
    * Ebben a példában a bemeneti tárolót `input` és a kimeneti tárolót hívjuk `output` .
1. Töltse fel a [`iris.csv`](https://github.com/Azure-Samples/batch-adf-pipeline-tutorial/blob/master/iris.csv) beviteli tárolóba a `input` Storage Explorer használatával a [Blobok blob-tárolóban való kezelésének](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) lépéseit követve.

## <a name="develop-a-script-in-python"></a>Parancsfájl fejlesztése a Pythonban

A következő Python-szkript betölti az `iris.csv` adatkészletet a `input` tárolóból, végrehajt egy adatkezelési folyamatot, és visszamenti az eredményeket a `output` tárolóba.

``` python
# Load libraries
from azure.storage.blob import BlobClient
import pandas as pd

# Define parameters
connectionString = "<storage-account-connection-string>"
containerName = "output"
outputBlobName  = "iris_setosa.csv"

# Establish connection with the blob storage account
blob = BlobClient.from_connection_string(conn_str=connectionString, container_name=containerName, blob_name=outputBlobName)

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Take a subset of the records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv(outputBlobName, index = False)

with open(outputBlobName, "rb") as data:
    blob.upload_blob(data)
```

Mentse a parancsfájlt, `main.py` és töltse fel az **Azure Storage** - `input` tárolóba. A blob-tárolóba való feltöltés előtt ügyeljen arra, hogy a funkcionalitását helyileg tesztelje és érvényesítse:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Azure Data Factory folyamat beállítása

Ebben a szakaszban egy folyamatot hoz létre és érvényesít a Python-parancsfájl használatával.

1. Az adat-előállító létrehozásához kövesse a [jelen cikk](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)"adat-előállító létrehozása" című szakaszának lépéseit.
1. A **gyári erőforrások** mezőben válassza a + (plusz) gombot, majd válassza a **folyamat** elemet.
1. Az **általános** lapon állítsa be a folyamat nevét "Python futtatása" értékre.

    ![Az Általános lapon állítsa be a folyamat nevét "Python futtatása" értékre.](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. A **tevékenységek** mezőben bontsa ki a **Batch szolgáltatás** elemet. Húzza az egyéni tevékenységet a **tevékenységek** eszközkészletből a folyamat tervező felületére. Töltse ki az alábbi lapokat az egyéni tevékenységhez:
    1. Az általános **lapon** adja meg a **TestPipeline** nevet az ![ Általános lapon, és adja meg a testPipeline nevet.](./media/run-python-batch-azure-data-factory/create-custom-task.png)
    1. A **Azure batch** lapon adja hozzá az előző lépésekben létrehozott **Batch-fiókot** , és **tesztelje a kapcsolódást** , hogy az sikeres legyen.
    ![A Azure Batch lapon adja hozzá az előző lépésekben létrehozott batch-fiókot, majd tesztelje a kapcsolatokat.](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)
    1. A **Beállítások** lapon:
        1. Adja meg a **parancsot** a következőképpen: `python main.py` .
        1. Az **erőforráshoz társított szolgáltatáshoz** adja hozzá az előző lépésekben létrehozott Storage-fiókot. Ellenőrizze, hogy a kapcsolódás sikeres volt-e.
        1. A **mappa elérési útja** mezőben válassza ki a Python-parancsfájlt és a hozzá tartozó bemeneti adatokat tartalmazó **Azure Blob Storage** tároló nevét. Ezzel letölti a kiválasztott fájlokat a tárolóból a készlet csomópont példányaira a Python-szkript végrehajtása előtt.

        ![A mappa elérési útja lapon válassza ki az Azure Blob Storage tároló nevét.](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)

1. A folyamat beállításainak érvényesítéséhez a vászon fölött kattintson az **Érvényesítés** elemre a folyamat eszköztárán. Győződjön meg róla, hogy a folyamat érvényesítése sikerült. Az érvényesítés kimenetének bezárásához kattintson a &gt;&gt; (jobbra mutató nyíl) gombra.
1. A folyamat teszteléséhez kattintson a **hibakeresés** elemre, és győződjön meg róla, hogy az megfelelően működik-e.
1. A folyamat közzétételéhez kattintson a **Közzététel** gombra.
1. Kattintson az **aktiválás** gombra a Python-szkript batch-folyamat részeként való futtatásához.

    ![Kattintson az aktiválás gombra a Python-szkript batch-folyamat részeként való futtatásához](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>A naplófájlok figyelése

Ha a parancsfájl végrehajtásával figyelmeztetéseket vagy hibákat állít elő, a kijelentkezés `stdout.txt` vagy a `stderr.txt` naplózott kimenet további információi láthatók.

1. Batch Explorer bal oldalán válassza a **feladatok** lehetőséget.
1. Válassza ki az adatok előállítója által létrehozott feladatot. Ha a készletet elnevezte `custom-activity-pool` , válassza a elemet `adfv2-custom-activity-pool` .
1. Kattintson arra a feladatra, amelynél hiba történt a kilépési kóddal.
1. Megtekintheti `stdout.txt` és `stderr.txt` diagnosztizálhatja a problémát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Bár magukért a feladatokért és tevékenységekért nem kell fizetnie, a számítási csomópontokért igen. Ezért ajánlott csak szükség szerint lefoglalni a készleteket. A készlet törlésekor a rendszer a csomópont összes tevékenységének kimenetét is törli. A bemeneti és kimeneti fájlok azonban megmaradnak a Storage-fiókban. Ha már nincs rá szükség, törölheti a Batch-fiókot és a Storage-fiókot is.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hitelesítés Batch- és Storage-fiókokkal
> * Parancsfájl fejlesztése és futtatása Pythonban
> * Számításicsomópont-készlet létrehozása alkalmazás futtatásához
> * A Python számítási feladatainak beosztása
> * Az elemzési folyamat monitorozása
> * Hozzáférés a naplófájlokhoz

A Azure Data Factoryról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Azure Data Factory áttekintése](../data-factory/introduction.md)
