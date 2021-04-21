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
ms.openlocfilehash: 4b7251be220c012ca51970863ac2eed55d46d711
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751147"
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
1. **Régió** – Válassza ki azt a régiót, ahová az ügyfélalkalmazásait/szolgáltatásait (például Azure-beli virtuális gép, Power BI, Azure Analysis Service) és az adatokat tartalmazó tárolókat (például Azure Data Lake Storage, Azure Cosmos DB elemzési tárhely) helyezte el.

> [!NOTE]
> Az ügyfélalkalmazásokkal vagy -tárolóval nem közös munkaterület számos teljesítménybeli probléma kiváltó oka lehet. Ha az adatok vagy az ügyfelek több régióban vannak elhelyezve, különálló munkaterületeket hozhat létre az adatokkal és az ügyfelekkel közös régióban.

A **2. Data Lake Storage kiválasztása alatt:**

1. A **Fiók neve mezőben** válassza az **Új** létrehozása lehetőséget, és adja a **contosolake** vagy hasonló nevet az új tárfióknak, mivel a névnek egyedinek kell lennie.
1. Fájlrendszernév **szerint válassza a** Create New **(Új létrehozása)** lehetőséget, és adja neki a **users nevet.** Ezzel létrehoz egy users (felhasználók) nevű **storage-tárolót.** A munkaterület ezt a tárfiókot fogja használni "elsődleges" tárfiókként a Spark-táblákhoz és a Spark-alkalmazásnaplókhoz.
1. Jelölje be a "Tárblobadatok közreműködője szerepkör hozzárendelése saját Data Lake Storage Gen2 fiókban" jelölőnégyzetet. 

## <a name="completing-the-process"></a>A folyamat befejezése
Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A munkaterület néhány percen belül készen áll.

> [!NOTE]
> Ha egy meglévő dedikált SQL-készletből (korábban SQL DW-ről) szeretné engedélyezni a munkaterületi funkciókat, tekintse meg a Munkaterület engedélyezése a dedikált [SQL-készlethez (korábban SQL DW) című dokumentációt.](./sql-data-warehouse/workspace-connected-create.md)


## <a name="open-synapse-studio"></a>Nyissa meg Synapse Studio

A Azure Synapse létrehozása után két módon nyithatja meg a Synapse Studio:

* Nyissa meg a Synapse-munkaterületet a  Azure Portal [területen,](https://portal.azure.com)a Synapse-munkaterület Áttekintés szakaszában válassza a Megnyitás lehetőséget a Megnyitás Synapse Studio mezőben. 
* A hez `https://web.azuresynapse.net` lépve jelentkezzen be a munkaterületre.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Mintaadatok tárolása az elsődleges tárfiókban
Ebben az első lépésekben az NYX taxiadatok kis méretű, 100 000 sorból vett mintaadatkészletét fogjuk használni. Először is helyezze el a munkaterülethez létrehozott elsődleges tárfiókban.

* Töltse le ezt a fájlt a számítógépre: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* A Synapse Studio nyissa meg az adatközpontot. 
* Válassza a **Csatolt lehetőséget.**
* A kategória **Azure Data Lake Storage Gen2** egy olyan nevű elemet fog látni, mint a **myworkspace ( Primary - contosolake )**.
* Válassza ki a **users (Primary) nevű tárolót.**
* Válassza **a Feltöltés** lehetőséget, és válassza ki a letöltött `NYCTripSmall.parquet` fájlt.

Az egyik feltöltött Parquet-fájl két egyenértékű URI-lal érhető el:
* `https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet` 
* `abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet`

Az oktatóanyagban következő példákban cserélje le a **contosolake** nevet a felhasználói felületen a munkaterülethez kiválasztott elsődleges tárfiók nevére.



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Elemzés kiszolgáló nélküli SQL-készlet használatával](get-started-analyze-sql-on-demand.md)
