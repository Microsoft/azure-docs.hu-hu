---
title: Assessment Tools hozzáadása a Azure Migrate
description: Megtudhatja, hogyan adhat hozzá értékelési eszközöket a Azure Migrateban.
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 17113d167476c62a97031721b73183d0b0da18af
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95534994"
---
# <a name="add-assessment-tools"></a>Felmérőeszközök hozzáadása

Ez a cikk azt ismerteti, hogyan adhat hozzá értékelési eszközöket a [Azure Migrateban](./migrate-services-overview.md). 

- Ha szeretne felvenni egy értékelési eszközt, és még nem rendelkezik Azure Migrate-projekttel, kövesse ezt a [cikket](create-manage-projects.md).
- Ha felvettek egy ISV-eszközt, vagy az értékeléshez, [kövesse a lépéseket](prepare-isv-movere.md), hogy felkészüljön az eszközre.

## <a name="select-an-assessment-scenario"></a>Értékelési forgatókönyv kiválasztása

1. A Azure Migrate projektben kattintson az **Áttekintés** elemre.
2. Válassza ki az értékelési forgatókönyvet:

    - Az Azure-ba való áttelepítéshez szükséges gépek és munkaterhelések felderítéséhez és értékeléséhez válassza a **kiszolgálók felmérése és áttelepítése** lehetőséget.
    - A helyszíni SQL Server-adatbázisok értékeléséhez válassza az **adatbázisok felmérése és áttelepíteni** lehetőséget.
    - A helyszíni webalkalmazások értékeléséhez vagy átköltöztetéséhez válassza a **további Web Apps megismerése** lehetőséget  >  **Web Apps**.
    - A virtuális asztali infrastruktúra értékeléséhez válassza a **további**  >  **virtuális asztali infrastruktúra** megismerése lehetőséget.

    ![Az értékelési forgatókönyv kiválasztásának lehetőségei](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Kiszolgáló-értékelési eszköz kiválasztása 


1. Eszköz hozzáadása:

    - Ha a portálon a **kiszolgálók felmérése és áttelepítése** lehetőség használatával létrehozott egy Azure Migrate projektet, a rendszer automatikusan hozzáadja a Azure Migrate Server Assessment eszközt a projekthez. További értékelési eszközök hozzáadásához a **kiszolgálókon**, az **értékelési eszközök** mellett válassza a **további eszközök hozzáadása** elemet.
    
         ![További értékelési eszközök hozzáadására szolgáló gomb](./media/how-to-assess/add-assessment-tool.png)

    - Ha egy másik lehetőséggel hozta létre a projektet, és még nem rendelkezik Assessment-eszközökkel, akkor a **kiszolgálók**  >  **értékelési eszközei** lapon válassza a **kattintson ide**.

        ![Első értékelési eszköz hozzáadására szolgáló gomb](./media/how-to-assess/no-assessment-tool.png)

2. A **Azure Migrate**  >  **eszközök hozzáadása** területen válassza ki a hozzáadni kívánt eszközöket. Ezután válassza a **Hozzáadás eszköz** elemet.

    ![Kiértékelési eszközök kiválasztása a listából](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Adatbázis-értékelő eszköz kiválasztása

1. Eszköz hozzáadása:

    - Ha létrehozott egy Azure Migrate projektet az **adatbázis értékelése és áttelepítése** lehetőség használatával a portálon, a rendszer automatikusan hozzáadja az adatbázis-értékelési eszközt a projekthez. További értékelési eszközök hozzáadásához az **adatbázisokban** az **értékelési eszközök** elem mellett válassza a **további eszközök hozzáadása** elemet.

    - Ha egy másik lehetőség használatával hozott létre projektet, és még nem rendelkezik adatbázis-értékelési eszközzel, az **adatbázisok**  >  **felmérési eszközei** területen válassza a **kattintson ide**.

2. A **Azure Migrate**  >  **eszközök hozzáadása** területen válassza ki a hozzáadni kívánt eszközöket. Ezután válassza a **Hozzáadás eszköz** elemet.

    ![Adatbázis-értékelési eszközök kiválasztása a listából](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Webalkalmazás-értékelő eszköz kiválasztása

Ha Azure Migrate projektet hozott létre a portálon a **további**  >  **WebApps** lehetőséggel, a rendszer automatikusan hozzáadja a webalkalmazás-értékelő eszközt a projekthez. 


1. Ha a webalkalmazás-értékelő eszköz nincs a projektben, a **Web Apps**  >  **Assessment Tools** lapon **kattintson ide**.
    
    ![Web App Assessment-eszközök hozzáadása](./media/how-to-assess/no-web-app-assessment-tool.png)


2. A **Azure Migrate**  >  **eszközök hozzáadása** lapon válassza ki a webalkalmazás-értékelő eszközt. Ezután válassza a **Hozzáadás eszköz** elemet.

    ![Válassza ki az adatbázis-áttelepítési eszközt a listából](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Következő lépések

Helyszíni gépek felderítése a [VMware](./tutorial-discover-vmware.md) virtuális gépek, a [Hyper-V](./tutorial-discover-hyper-v.md)vagy a [fizikai kiszolgálók](./tutorial-discover-physical.md) Azure Migrate Server Assessment Tool használatával történő értékeléséhez