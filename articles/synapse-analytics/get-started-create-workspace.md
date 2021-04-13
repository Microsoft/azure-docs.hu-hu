---
title: 'Rövid útmutató: Első lépések – Synapse-munkaterület létrehozása'
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre Synapse-munkaterületet, dedikált SQL-készletet és kiszolgáló nélküli Apache Spark készletet.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: b22954edf4f3a5a935c470326aa43bd24ee2d708
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366062"
---
# <a name="creating-a-synapse-workspace"></a>Synapse-munkaterület létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre Synapse-munkaterületet, dedikált SQL-készletet és kiszolgáló nélküli Apache Spark készletet. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseit úgy tudja végrehajtani, hogy hozzáféréssel rendelkezik egy olyan erőforráscsoporthoz, amelyhez a **Tulajdonos szerepkört rendelte.** Hozza létre a Synapse-munkaterületet ebben az erőforráscsoportban.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Synapse-munkaterület létrehozása a Azure Portal

### <a name="start-the-process"></a>A folyamat elindítani
1. Nyissa meg [Azure Portal,](https://portal.azure.com)majd a keresősávba írja be a **Synapse kifejezéseket** anélkül, hogy az Enter billentyűt nyomná.
1. A keresési eredményekben a **Szolgáltatások alatt válassza** a Azure Synapse Analytics. 
1. Munkaterület **létrehozásához** válassza a Hozzáadás lehetőséget.

## <a name="basics-tab--project-details"></a>Alapvető beállítások lap > Project Details (Projekt részletei)
Töltse ki az alábbi mezőket:

1. **Előfizetés** – Válasszon ki egy előfizetést.
1. **Erőforráscsoport** – Bármilyen erőforráscsoportot használhat.
1. **Felügyelt erőforráscsoport** – Ezt hagyja üresen.

## <a name="basics-tab--workspace-details"></a>Alapvető beállítások lap > munkaterület részleteivel
Töltse ki az alábbi mezőket:

1. **Munkaterület neve** – Válasszon ki egy globálisan egyedi nevet. Ebben az oktatóanyagban a **myworkspace térben fogunk dolgozni.**
1. **Régió** – Válasszon ki egy régiót.

A **2. Data Lake Storage kiválasztása alatt:**

1. Fióknév **szerint válassza** az **Új** létrehozása lehetőséget, és nevezze el az új tárfiókot **contosolake** vagy hasonló néven, mivel a névnek egyedinek kell lennie.
1. Fájlrendszernév **szerint válassza az** Új létrehozása **lehetőséget,** és adja neki a **következő** nevet: . Ezzel létrehoz egy users (felhasználók) nevű **tárolót.** A munkaterület ezt a tárfiókot fogja használni "elsődleges" tárfiókként a Spark-táblákhoz és a Spark-alkalmazásnaplókhoz.
1. Jelölje be a "Tárfiók-blobadatok közreműködője szerepkör hozzárendelése saját Data Lake Storage Gen2 fiókban" jelölőnégyzetet. 

## <a name="completing-the-process"></a>A folyamat befejezése
Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A munkaterület néhány perc alatt készen áll.

> [!NOTE]
> A munkaterület funkcióinak meglévő dedikált SQL-készletből (korábban SQL DW) való engedélyezéséről lásd: Munkaterület engedélyezése a dedikált SQL-készlethez [(korábban SQL DW).](./sql-data-warehouse/workspace-connected-create.md)


## <a name="open-synapse-studio"></a>Nyissa meg Synapse Studio

A Azure Synapse létrehozása után két módon nyithatja meg a Synapse Studio:

* Nyissa meg a Synapse-munkaterületet a  Azure Portal [területen,](https://portal.azure.com)a Synapse-munkaterület Áttekintés szakaszában válassza a Megnyitás lehetőséget az Open Synapse Studio (Megnyitás Synapse Studio) mezőben. 
* A munkaterületre `https://web.azuresynapse.net` lépve jelentkezzen be.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Mintaadatok tárolása az elsődleges tárfiókba
Ebben az első lépések útmutatóban egy kis, 100 ezer sorból vett mintaadatkészletet fogunk használni a NYX Taxi Cab adataiból. Először is helyezze el a munkaterülethez létrehozott elsődleges tárfiókban.

* Töltse le ezt a fájlt a számítógépre: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* A Synapse Studio nyissa meg az Adatközpontot. 
* Válassza a **Csatolt lehetőséget.**
* Az **Azure Data Lake Storae Gen2** kategóriában egy **myworkspace ( Primary - contosolake )** nevű elem látható.
* Válassza ki a **users (Primary) nevű tárolót.**
* Válassza **a Feltöltés** lehetőséget, és válassza ki a letöltött `NYCTripSmall.parquet` fájlt.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Elemzés kiszolgáló nélküli SQL-készlet használatával](get-started-analyze-sql-on-demand.md)
