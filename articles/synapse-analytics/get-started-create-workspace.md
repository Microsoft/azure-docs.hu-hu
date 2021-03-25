---
title: 'Gyors útmutató: az első lépések – szinapszis-munkaterület létrehozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet, egy dedikált SQL-készletet és egy kiszolgáló nélküli Apache Spark készletet.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 695ad1463bb207e9dd61d7b0147adbe15b44df37
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108060"
---
# <a name="creating-a-synapse-workspace"></a>Szinapszis-munkaterület létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet, egy dedikált SQL-készletet és egy kiszolgáló nélküli Apache Spark készletet. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek elvégzéséhez hozzáféréssel kell rendelkeznie egy olyan erőforráscsoporthoz, amelyhez hozzá van rendelve a **tulajdonosi** szerepkör. Hozza létre a szinapszis munkaterületet ebben az erőforráscsoportban.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Szinapszis-munkaterület létrehozása a Azure Portalban

### <a name="start-the-process"></a>A folyamat elindítása
1. Nyissa meg a [Azure Portal](https://portal.azure.com), a keresősávban adja meg a **szinapszisot** az ENTER billentyű lenyomása nélkül.
1. A keresési eredmények között, a **szolgáltatások** területen válassza az **Azure szinapszis Analytics** lehetőséget.
1. Munkaterület létrehozásához válassza a **Hozzáadás** lehetőséget.

## <a name="basics-tab--project-details"></a>Alapismeretek lap > projekt részletei
Töltse ki az alábbi mezőket:
    1. **Előfizetés** – válasszon ki egy előfizetést.
    1. **Erőforráscsoport** – bármely erőforráscsoport használata.
    1. **Erőforráscsoport** – hagyja üresen ezt a mezőt.


## <a name="basics-tab--workspace-details"></a>Alapismeretek lap > munkaterület részletei
Töltse ki a következő mezőket: 1. **Munkaterület neve** – tetszőleges globálisan egyedi nevet választhat. Ebben az oktatóanyagban a **sajátmunkaterület**-t fogjuk használni.
1. **Régió** – válasszon régiót.
1. **2. generációs Data Lake Storage kiválasztása** 1. Kattintson az **előfizetéshez** tartozó gombra.
1. A **fiók neve** területen kattintson az **új létrehozása** elemre, és nevezze el az új Storage-fiókot **contosolake** , vagy ehhez hasonló névnek kell lennie.
1. A **fájlrendszer neve** területen kattintson az **új létrehozása** elemre, és nevezze el a **felhasználók** nevet. Ekkor létrejön egy **felhasználó** nevű tároló. A munkaterület ezt a Storage-fiókot fogja használni az "elsődleges" Storage-fiók a Spark-táblák és a Spark-alkalmazások naplói számára.
1. Jelölje be a "a Storage blob adatközreműködői szerepkör kiosztása a Data Lake Storage Gen2 fiókhoz" jelölőnégyzetet. 

## <a name="completing-the-process"></a>A folyamat befejezése
Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A munkaterület pár percen belül elkészül.

> [!NOTE]
> Ha egy meglévő dedikált SQL-készletből (korábban SQL DW) kívánja engedélyezni a munkaterület-szolgáltatásokat, tekintse át a [munkaterület engedélyezése a DEDIKÁLT SQL-készlethez (korábban SQL DW) című témakört](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>A szinapszis Studio megnyitása

Az Azure szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:

* Nyissa meg a szinapszis munkaterületet a [Azure Portal](https://portal.azure.com), a szinapszis munkaterület **Áttekintés** szakaszában, a **Megnyitás elemre kattintva** nyissa meg a szinapszis Studio megnyitása mezőt.
* Lépjen a `https://web.azuresynapse.net` munkaterületre, és jelentkezzen be.











## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Elemzés kiszolgáló nélküli SQL-készlet használatával](get-started-analyze-sql-on-demand.md)
