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
ms.date: 12/31/2020
ms.openlocfilehash: 94d069a283249f2880743ba911c32bf3821d28c8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171483"
---
# <a name="creating-a-synapse-workspace"></a>Szinapszis-munkaterület létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet, egy dedikált SQL-készletet és egy kiszolgáló nélküli Apache Spark készletet. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek elvégzéséhez hozzáféréssel kell rendelkeznie egy olyan erőforráscsoporthoz, amelyhez hozzá van rendelve a **tulajdonosi** szerepkör. Hozza létre a szinapszis munkaterületet ebben az erőforráscsoportban.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Szinapszis-munkaterület létrehozása a Azure Portalban

1. Nyissa meg a [Azure Portal](https://portal.azure.com), a keresősávban adja meg a **szinapszisot** az ENTER billentyű lenyomása nélkül.
1. A keresési eredmények között, a **szolgáltatások** területen válassza az **Azure szinapszis Analytics** lehetőséget.
1. Munkaterület létrehozásához válassza a **Hozzáadás** lehetőséget.
1. Az **alapbeállítások** lap **projekt részletei** területén adja meg a következő mezőket:
      1. **Előfizetés** – válasszon ki egy előfizetést.
      2. **Erőforráscsoport** – bármely erőforráscsoport használata.
      3. **Erőforráscsoport** – hagyja üresen ezt a mezőt.
1. Az **alapok** lap **munkaterület részletei** területén adja meg a következő mezőket:
      1. **Munkaterület neve** – tetszőleges globálisan egyedi nevet választhat. Ebben az oktatóanyagban a **sajátmunkaterület**-t fogjuk használni.
      1. **Régió** – válasszon régiót.
      1. **2. generációs Data Lake Storage kiválasztása**
        1. Kattintson az **előfizetéshez** tartozó gombra.
        1. A **fiók neve** területen kattintson az **új létrehozása** elemre, és nevezze el az új Storage-fiókot **contosolake** , vagy ehhez hasonló névnek kell lennie.
        1. A **fájlrendszer neve** területen kattintson az **új létrehozása** elemre, és nevezze el a **felhasználók** nevet. Ekkor létrejön egy **felhasználó** nevű tároló. A munkaterület ezt a Storage-fiókot fogja használni az "elsődleges" Storage-fiók a Spark-táblák és a Spark-alkalmazások naplói számára.
        1. Jelölje be a "a Storage blob adatközreműködői szerepkör kiosztása a Data Lake Storage Gen2 fiókhoz" jelölőnégyzetet. 
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A munkaterület pár percen belül elkészül.

> [!NOTE]
> Ha egy meglévő dedikált SQL-készletből (korábban SQL DW) kívánja engedélyezni a munkaterület-szolgáltatásokat, tekintse át a [munkaterület engedélyezése a DEDIKÁLT SQL-készlethez (korábban SQL DW) című témakört](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>A szinapszis Studio megnyitása

Az Azure szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:

* Nyissa meg a szinapszis munkaterületet a [Azure Portal](https://portal.azure.com), a szinapszis munkaterület **Áttekintés** szakaszában, a **Megnyitás elemre kattintva** nyissa meg a szinapszis Studio megnyitása mezőt.
* Lépjen a `https://web.azuresynapse.net` munkaterületre, és jelentkezzen be.


## <a name="the-built-in-serverless-sql-pool"></a>A beépített kiszolgáló nélküli SQL-készlet

Minden munkaterülethez egy **beépített, beépített** kiszolgáló nélküli SQL-készlet tartozik. Ez a készlet nem törölhető. A kiszolgáló nélküli SQL-készletek lehetővé teszik az SQL használatát anélkül, hogy a kapacitást dedikált SQL-készletekkel kellene fenntartania. A dedikált SQL-készletektől eltérően a kiszolgáló nélküli SQL-készlet számlázása a lekérdezés futtatásához beolvasott adatmennyiségen alapul, nem a készlethez lefoglalt kapacitás számán.


## <a name="create-a-dedicated-sql-pool"></a>Dedikált SQL-készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza az   >  **SQL-készletek** kezelése lehetőséget.
1. **Új** kiválasztása
1. Az **SQL-készlet neve** területen válassza a **SQLPOOL1** lehetőséget.
1. A **teljesítmény szintjén** válassza a **DW100C** lehetőséget.
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A dedikált SQL-készlet néhány percen belül elkészül. A dedikált SQL-készlet egy dedikált SQL Pool-adatbázishoz van társítva, amely más néven **SQLPOOL1**.

A dedikált SQL-készletek számlázható erőforrásokat használnak, amíg aktívak. A készletet később is szüneteltetheti a költségek csökkentése érdekében.

> [!NOTE] 
> Amikor új dedikált SQL-készletet (korábban SQL DW-t) hoz létre a munkaterületen, megnyílik a dedikált SQL Pool kiépítési oldal. A kiépítés a logikai SQL-kiszolgálón történik.


## <a name="create-a-serverless-apache-spark-pool"></a>Kiszolgáló nélküli Apache Spark készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza a   >  **Apache Spark készletek** kezelése lehetőséget.
1. **Új** kiválasztása 
1. **Apache Spark a készlet neve** mezőbe írja be a következőt: **Spark1**.
1. A **csomópont mérete** mezőben adja meg a **kis** értéket.
1. Ha a **csomópontok száma** a minimum 3 és a maximum 3 értéket állítja be
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A Apache Spark-készlet néhány másodpercen belül elkészül.

A Spark-készlet azt mutatja be, hogy az Azure szinapszis hány Spark-erőforrást használ. Ön csak a felhasznált erőforrásokért fizet. Ha aktívan abbahagyja a készlet használatát, az erőforrások automatikusan időtúllépést és újrahasznosítást végeznek.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Elemzés dedikált SQL-készlet használatával](get-started-analyze-sql-pool.md)
