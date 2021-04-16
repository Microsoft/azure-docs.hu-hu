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
ms.openlocfilehash: 61fcbfa9d40ebb26485ce8160fa3b011935ab4d6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536307"
---
# <a name="creating-a-synapse-workspace"></a>Synapse-munkaterület létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre Synapse-munkaterületet, dedikált SQL-készletet és kiszolgáló nélküli Apache Spark készletet. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseit egy olyan erőforráscsoporthoz kell elérnie, amelyhez a Tulajdonos szerepkört **rendeli.** Hozza létre a Synapse-munkaterületet ebben az erőforráscsoportban.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Synapse-munkaterület létrehozása a Azure Portal

### <a name="start-the-process"></a>A folyamat elindítani
1. Nyissa meg [Azure Portal](https://portal.azure.com)következőt: , a keresősávba írja be a **Synapse-t** anélkül, hogy beírná az Enter billentyűt.
1. A keresési eredmények szolgáltatások alatt **válassza** a **Azure Synapse Analytics.**
1. Munkaterület **létrehozásához** válassza a Hozzáadás lehetőséget.

## <a name="basics-tab--project-details"></a>Alapvető beállítások lap > Project Details (Projekt részletei)
Töltse ki az alábbi mezőket:

1. **Előfizetés** – Válasszon ki egy előfizetést.
1. **Erőforráscsoport** – Bármilyen erőforráscsoportot használhat.
1. **Felügyelt erőforráscsoport** – Ezt hagyja üresen.

## <a name="basics-tab--workspace-details"></a>Alapvető beállítások lap > munkaterület részleteivel
Töltse ki az alábbi mezőket:

1. **Munkaterület neve** – Válasszon ki egy globálisan egyedi nevet. Ebben az oktatóanyagban a **myworkspace-t fogjuk használni.**
1. **Régió** – Válassza ki azt a régiót, ahol az ügyfélalkalmazásait/szolgáltatásait (például Azure-beli virtuális gép, Power BI, Azure Analysis Service) és az adatokat tartalmazó tárolókat (például Azure Data Lake Storage, Azure Cosmos DB elemzési tár) helyezte el.

> [!NOTE]
> Az ügyfélalkalmazásokkal vagy -tárolóval nem közös munkaterület számos teljesítménybeli probléma kiváltó oka lehet. Ha az adatokat vagy az ügyfeleket több régióban helyezi el, létrehozhat különálló munkaterületeket az adatokkal és ügyfelekkel közös régióban.

A **2. Data Lake Storage kiválasztása alatt:**

1. A **Fiók neve mezőben** válassza az **Új** létrehozása lehetőséget, és adja a **contosolake** vagy hasonló nevet az új tárfióknak, mivel a névnek egyedinek kell lennie.
1. Fájlrendszernév **szerint válassza a** Create New **(Új létrehozása)** lehetőséget, és adja neki a **users nevet.** Ezzel létrehoz egy users (felhasználók) nevű **Storage-tárolót.** A munkaterület ezt a tárfiókot fogja használni "elsődleges" tárfiókként a Spark-táblákhoz és a Spark-alkalmazásnaplókhoz.
1. Jelölje be a "Tárblobadatok közreműködője szerepkör hozzárendelése saját Data Lake Storage Gen2 fiókban" jelölőnégyzetet. 

## <a name="completing-the-process"></a>A folyamat befejezése
Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A munkaterület néhány percen belül készen áll.

> [!NOTE]
> Ha egy meglévő dedikált SQL-készletből (korábban SQL DW-ről) szeretné engedélyezni a munkaterületi funkciókat, tekintse meg a Munkaterület engedélyezése a dedikált [SQL-készlethez (korábban SQL DW) című dokumentációt.](./sql-data-warehouse/workspace-connected-create.md)


## <a name="open-synapse-studio"></a>Nyissa meg Synapse Studio

A Azure Synapse létrehozása után két módon nyithatja meg a Synapse Studio:

* Nyissa meg a Synapse-munkaterületet a  [Azure Portal,](https://portal.azure.com)majd a Synapse-munkaterület Áttekintés szakaszában válassza a Megnyitás lehetőséget az Open Synapse Studio (Megnyitás Synapse Studio) mezőben. 
* A hez `https://web.azuresynapse.net` lépve jelentkezzen be a munkaterületre.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Mintaadatok tárolása az elsődleges tárfiókban
Ebben az első lépésekben az NYX taxiadatok kis méretű, 100 ezer sorból vett mintaadatkészletét fogjuk használni. Először is helyezze el a munkaterülethez létrehozott elsődleges tárfiókban.

* Töltse le ezt a fájlt a számítógépre: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* A Synapse Studio nyissa meg az adatközpontot. 
* Válassza a **Csatolt lehetőséget.**
* A kategória **Azure Data Lake Storage Gen2** egy olyan nevű elemet fog látni, mint a **myworkspace ( Primary - contosolake )**.
* Válassza ki a **users (Primary) nevű tárolót.**
* Válassza **a Feltöltés** lehetőséget, és válassza ki a letöltött `NYCTripSmall.parquet` fájlt.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Elemzés kiszolgáló nélküli SQL-készlet használatával](get-started-analyze-sql-on-demand.md)
