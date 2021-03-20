---
title: Áttelepítési eszközök hozzáadása a Azure Migrate
description: Megtudhatja, hogyan adhat hozzá áttelepítési eszközöket a Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 97051e97ec9868f6941b579241e16e62fdd2162b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751784"
---
# <a name="add-migration-tools"></a>Migrálási eszközök hozzáadása

Ez a cikk bemutatja, hogyan adhat hozzá áttelepítési eszközöket a [Azure Migrate](./migrate-services-overview.md).

- Ha szeretne hozzáadni egy áttelepítési eszközt, és még nem állított be Azure Migrate projektet, kövesse ezt a [cikket](create-manage-projects.md).
- Ha egy ISV-eszközt adott hozzá az áttelepítéshez, [kövesse a lépéseket](prepare-isv-movere.md), hogy felkészítse az eszközt.

## <a name="select-a-migration-scenario"></a>Áttelepítési forgatókönyv kiválasztása

1. A Azure Migrate projektben kattintson az **Áttekintés** elemre.
2. Válassza ki a használni kívánt áttelepítési forgatókönyvet:

    - Ha gépeket és számítási feladatokat szeretne áttelepíteni az Azure-ba, válassza a **kiszolgálók felmérése és áttelepíteni** lehetőséget.
    - A helyszíni adatbázisok átköltöztetéséhez válassza az **adatbázisok felmérése és áttelepíteni** lehetőséget.
    - Ha helyszíni webalkalmazásokat szeretne áttelepíteni, válassza a **további Web Apps megismerése** lehetőséget  >  .
    - Ha az adatmezővel szeretne áttelepíteni az Azure-ba az adataikat, válassza a **további** adatmezők feltárása lehetőséget  >  .

    ![Az áttelepítési forgatókönyv kiválasztásának lehetőségei](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>Kiszolgáló áttelepítési eszközének kiválasztása

1. Eszköz hozzáadása:

    - Ha a portálon a **kiszolgálók felmérése és áttelepítése** lehetőséggel létrehozott egy Azure Migrate projektet, a rendszer automatikusan hozzáadja a Azure Migrate Server áttelepítési eszközt a projekthez. További áttelepítési eszközök hozzáadásához a **kiszolgálókon** az **áttelepítési eszközök** mellett válassza a **további eszközök hozzáadása** lehetőséget.
    
         ![További áttelepítési eszközök hozzáadására szolgáló gomb](./media/how-to-migrate/add-migration-tools.png)

    - Ha egy másik lehetőséggel hozta létre a projektet, és még nem rendelkezik áttelepítési eszközökkel, a **kiszolgálók**  >  **áttelepítési eszközök** területén válassza a **kattintson ide**.

    ![Az első áttelepítési eszközök hozzáadására szolgáló gomb](./media/how-to-migrate/no-migration-tool.png)

2. A **Azure Migrate**  >  **eszközök hozzáadása** területen válassza ki a hozzáadni kívánt eszközöket. Ezután válassza a **Hozzáadás eszköz** elemet.

    ![Kiértékelési eszközök kiválasztása a listából](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>Adatbázis-áttelepítési eszköz kiválasztása

Ha létrehozott egy Azure Migrate projektet az **adatbázis értékelése és áttelepítése** lehetőség használatával a portálon, a rendszer automatikusan hozzáadja az adatbázis-áttelepítési eszközt a projekthez. 

1. Ha az adatbázis-áttelepítési eszköz nincs a projektben, az **adatbázisok**  >  **felmérési eszközök** területén válassza a **kattintson ide**.
    
    ![Adatbázis-áttelepítési eszközök hozzáadása](./media/how-to-migrate/no-database-migration-tool.png)


2. A **Azure Migrate**  >  **eszközök hozzáadása** területen válassza ki az adatbázis-áttelepítési eszközt. Ezután válassza a **Hozzáadás eszköz** elemet.

    ![Válassza ki az adatbázis-áttelepítési eszközt a listából](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Webalkalmazás-áttelepítési eszköz kiválasztása

Ha Azure Migrate projektet hozott létre a portálon a **további**  >  **WebApps** lehetőséggel, a rendszer automatikusan hozzáadja a webalkalmazás-áttelepítési eszközt a projekthez. 

1. Ha a webalkalmazás-áttelepítési eszköz nincs a projektben, a **Web Apps**  >  **Assessment Tools** elemnél válassza a **kattintson ide**.

    ![Webalkalmazás-áttelepítési eszközök hozzáadása](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. A **Azure Migrate**  >  **eszközök hozzáadása** lapon válassza ki a webalkalmazás-áttelepítési eszközt. Ezután válassza a **Hozzáadás eszköz** elemet.

    ![Válassza ki a WebApp Assessment Tools elemet a listáról](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Azure Data Box megrendelése

Nagy mennyiségű adatok Azure-ba történő átmásolásához rendelhet egy Azure Data Box az offline adatforgalomhoz.

1. Az **Áttekintés** területen válassza a **továbbiak megismerése** lehetőséget.
2. A **továbbiak** lehetőségnél válassza az **adatmező** lehetőséget.
3. A **Data Box használatának első lépéseiben** válassza ki azt az előfizetést és erőforráscsoportot, amelyet a Data Box rendeléséhez szeretne használni.
4. Az **átvitel típusa** az Azure-ba való importálás. Adja meg azt az országot, amelyben az adatok találhatók, valamint azt az Azure-régiót, amelybe az adatok átvitelét el szeretné helyezni. 
5. A beállítások mentéséhez kattintson az **alkalmaz** gombra.

## <a name="next-steps"></a>Következő lépések

A [Hyper-V](tutorial-migrate-hyper-v.md) vagy a [VMware](tutorial-migrate-vmware.md) virtuális gépek Azure Migrate Server áttelepítési eszközével próbálja ki az áttelepítést.
