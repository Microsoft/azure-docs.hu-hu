---
title: Projektek létrehozása és kezelése
description: Projektek keresése, létrehozása, kezelése és törlése Azure Migrateban.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: cb0ac41d469ad9a7670ce4b1bae23b315a17dc38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871080"
---
# <a name="create-and-manage-projects"></a>Projektek létrehozása és kezelése

Ez a cikk bemutatja, hogyan hozhat létre, kezelhet és törölhet [projekteket](migrate-services-overview.md). 

A klasszikus Azure Migrate kivonása február 2024. Február 2024 után a Azure Migrate klasszikus verziója már nem támogatott, és a klasszikus projektben lévő leltári metaadatokat törli a rendszer. Ha klasszikus projekteket használ, törölje ezeket a projekteket, és kövesse a lépéseket egy új projekt létrehozásához. A klasszikus projekteket és összetevőket nem lehet a Azure Migratera frissíteni. A létrehozási folyamat megkezdése előtt tekintse meg a [gyakori kérdéseket](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) .

A Project használatával tárolhatja az Ön által értékelt vagy áttelepíteni kívánt környezet felderítési, felmérési és áttelepítési metaadatait. Egy projektben nyomon követheti a felderített eszközöket, kiértékelheti az Azure-ba való áttelepítést, és elvégezheti az áttelepítést.  

## <a name="verify-permissions"></a>Engedélyek ellenőrzése

Győződjön meg arról, hogy rendelkezik a megfelelő engedélyekkel a projekt létrehozásához:

1. A Azure Portal nyissa meg a megfelelő előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése** lapon keresse meg a megfelelő fiókot, és válassza ki a megtekintési engedélyek lehetőséget. *Közreműködői* vagy *tulajdonosi* engedélyekkel kell rendelkeznie. 


## <a name="create-a-project-for-the-first-time"></a>Projekt létrehozása első alkalommal

Új projekt beállítása egy Azure-előfizetésben.

1. A Azure Portal keresse meg a *Azure Migrate*.
2. A **szolgáltatások** területen válassza a **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen válassza a **Kiszolgálók értékelése és migrálása** lehetőséget.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="A kiszolgálók értékelésére és áttelepítésére szolgáló lehetőség az áttekintésben":::

4. A **kiszolgálók** területen válassza a **create Project (projekt létrehozása**) lehetőséget.

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="A projekt létrehozásának megkezdéséhez szükséges gomb":::

5. A **projekt létrehozása** lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet.
    - A földrajzi hely csak a helyszíni kiszolgálókról összegyűjtött metaadatok tárolására szolgál. Bármelyik célhelyet kiválaszthatja az áttelepítéshez. 
    - Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

8. Válassza a **Létrehozás** lehetőséget.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="A projekt beállításainak bevitelére szolgáló oldal":::


Várjon néhány percet, amíg a projekt üzembe helyezése megtörténik.

## <a name="create-a-project-in-a-specific-region"></a>Projekt létrehozása egy adott régióban

A portálon kiválaszthatja a földrajzot, amelyben létre kívánja hozni a projektet. Ha a projektet egy adott Azure-régión belül szeretné létrehozni, a projekt létrehozásához használja a következő API-parancsot.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>További projektek létrehozása

Ha már rendelkezik projekttel, és szeretne létrehozni egy további projektet, tegye a következőket:  

1. Az [Azure nyilvános portálon](https://portal.azure.com) vagy [Azure Government](https://portal.azure.us)keresse meg a **Azure Migrate**.
2. A Azure Migrate irányítópulton > **kiszolgálók** lapon válassza a **módosítás** lehetőséget a jobb felső sarokban.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Projekt módosítása":::

3. Új projekt létrehozásához válassza a **kattintson ide**.


## <a name="find-a-project"></a>Projekt keresése

A következőképpen kereshet egy projektet:

1. A [Azure Portal](https://portal.azure.com)keresse meg a *Azure Migrate*.
2. A Azure Migrate irányítópulton > **kiszolgálók** lapon válassza a **módosítás** lehetőséget a jobb felső sarokban.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Váltás meglévő projektre":::

3. Válassza ki a megfelelő előfizetést és projektet.


### <a name="find-a-classic-project"></a>Klasszikus projekt keresése

Ha a projektet a Azure Migrate [előző verziójában](migrate-services-overview.md#azure-migrate-versions) hozta létre, a következőképpen keresheti meg:

1. A [Azure Portal](https://portal.azure.com)keresse meg a *Azure Migrate*.
2. Ha a Azure Migrate-irányítópulton létrehozott egy projektet az előző verzióban, megjelenik egy régebbi projektekre hivatkozó szalagcím. Válassza ki a szalagcímet.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Meglévő projektek elérése":::

3. Tekintse át a régi projektek listáját.


## <a name="delete-a-project"></a>Projekt törlése

A következőképpen törölheti:

1. Nyissa meg azt az Azure-erőforráscsoportot, amelyben a projekt létrejött.
2. Az erőforráscsoport lapon válassza a **rejtett típusok megjelenítése** elemet.
3. Válassza ki a törölni kívánt projektet és a hozzá tartozó erőforrásokat.
    - Az erőforrástípus a **Microsoft. migrál/migrateprojects**.
    - Ha az erőforráscsoportot kizárólag a projekt használja, akkor törölheti a teljes erőforráscsoportot.

Vegye figyelembe:

- A törlésekor a projekt és a felderített kiszolgálók metaadatait is törli a rendszer.
- Ha a Azure Migrate régebbi verzióját használja, nyissa meg azt az Azure-erőforráscsoportot, amelyben a projekt létrejött. Válassza ki a törölni kívánt projektet (az erőforrás típusa **áttelepítési projekt**).
- Ha függőségi elemzést használ egy Azure Log Analytics-munkaterülettel:
    - Ha Log Analytics munkaterületet csatolt a kiszolgáló-értékelési eszközhöz, a munkaterület nem törlődik automatikusan. Ugyanaz a Log Analytics munkaterület több forgatókönyv esetén is használható.
    - Ha törölni szeretné a Log Analytics munkaterületet, ezt manuálisan végezze el.
- A projekt törlése nem vonható vissza. A törölt objektumok nem állíthatók helyre.

### <a name="delete-a-workspace-manually"></a>Munkaterület törlése manuálisan

1. Tallózással keresse meg a projekthez csatolt Log Analytics munkaterületet.

    - Ha még nem törölte a projektet, a munkaterületre mutató hivatkozást a **Essentials**  >  **Server Assessment** webhelyen találja.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="LA munkaterület":::
       
    - Ha már törölte a projektet, válassza az **erőforráscsoportok** elemet a Azure Portal bal oldali ablaktábláján, és keresse meg a munkaterületet.
       
2. A munkaterület törléséhez [kövesse az utasításokat](../azure-monitor/logs/delete-workspace.md) .

## <a name="next-steps"></a>Következő lépések

[Felmérési](how-to-assess.md) vagy [áttelepítési](how-to-migrate.md) eszközök hozzáadása a projektekhez.