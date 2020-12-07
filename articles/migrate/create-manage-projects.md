---
title: Azure Migrate-projektek létrehozása és kezelése
description: Projektek keresése, létrehozása, kezelése és törlése Azure Migrateban.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 445e08b255e5b4dd67dd1c6a47c8df6ce59df5bd
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753824"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate-projektek létrehozása és kezelése

Ez a cikk bemutatja, hogyan hozhat létre, kezelhet és törölhet [Azure Migrate](migrate-services-overview.md) projekteket.

Azure Migrate-projekttel a rendszer az Ön által értékelt vagy áttelepíteni kívánt környezetből gyűjtött felderítési, értékelési és áttelepítési metaadatok tárolására szolgál. Egy projektben nyomon követheti a felderített eszközöket, kiértékelheti az Azure-ba való áttelepítést, és elvégezheti az áttelepítést.  

## <a name="verify-permissions"></a>Engedélyek ellenőrzése

Győződjön meg arról, hogy megfelelő engedélyekkel rendelkezik Azure Migrate projekt létrehozásához:

1. A Azure Portal nyissa meg a megfelelő előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése** lapon keresse meg a megfelelő fiókot, és válassza ki a megtekintési engedélyek lehetőséget. *Közreműködői* vagy *tulajdonosi* engedélyekkel kell rendelkeznie. 


## <a name="create-a-project-for-the-first-time"></a>Projekt létrehozása első alkalommal

Hozzon létre egy új Azure Migrate projektet egy Azure-előfizetésben.

1. A Azure Portal keresse meg a *Azure Migrate*.
2. A **szolgáltatások** területen válassza a **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen válassza a **Kiszolgálók értékelése és migrálása** lehetőséget.

    ![A kiszolgálók értékelésére és áttelepítésére szolgáló lehetőség az áttekintésben](./media/create-manage-projects/assess-migrate-servers.png)

4. A **kiszolgálók** területen válassza a **create Project (projekt létrehozása**) lehetőséget.

    ![A projekt létrehozásának megkezdéséhez szükséges gomb](./media/create-manage-projects/create-project.png)

5. A **projekt létrehozása** lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet.
    - A földrajzi hely csak a helyszíni gépekről összegyűjtött metaadatok tárolására szolgál. Bármelyik célhelyet kiválaszthatja az áttelepítéshez. 
    - Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

8. Kattintson a **Létrehozás** gombra.

   ![A projekt beállításainak bevitelére szolgáló oldal](./media/create-manage-projects/project-details.png)


Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik.

## <a name="create-a-project-in-a-specific-region"></a>Projekt létrehozása egy adott régióban

A portálon kiválaszthatja a földrajzot, amelyben létre kívánja hozni a projektet. Ha a projektet egy adott Azure-régión belül szeretné létrehozni, a projekt létrehozásához használja a következő API-parancsot.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>További projektek létrehozása

Ha már rendelkezik Azure Migrate-projekttel, és szeretne egy további projektet létrehozni, tegye a következőket:  

1. Az [Azure nyilvános portálon](https://portal.azure.com) vagy [Azure Government](https://portal.azure.us)keresse meg a **Azure Migrate**.
2. A Azure Migrate irányítópulton > **kiszolgálók** lapon válassza a **módosítás** lehetőséget a jobb felső sarokban.

   ![Azure Migrate projekt módosítása](./media/create-manage-projects/switch-project.png)

3. Új projekt létrehozásához válassza a **kattintson ide**.


## <a name="find-a-project"></a>Projekt keresése

A következőképpen kereshet egy projektet:

1. A [Azure Portal](https://portal.azure.com)keresse meg a *Azure Migrate*.
2. A Azure Migrate irányítópulton > **kiszolgálók** lapon válassza a **módosítás** lehetőséget a jobb felső sarokban.

    ![Váltás meglévő Azure Migrate projektre](./media/create-manage-projects/switch-project.png)

3. Válassza ki a megfelelő előfizetést és Azure Migrate projektet.


### <a name="find-a-legacy-project"></a>Örökölt projekt keresése

Ha a projektet a Azure Migrate [előző verziójában](migrate-services-overview.md#azure-migrate-versions) hozta létre, a következőképpen keresheti meg:

1. A [Azure Portal](https://portal.azure.com)keresse meg a *Azure Migrate*.
2. Ha a Azure Migrate-irányítópulton létrehozott egy projektet az előző verzióban, megjelenik egy régebbi projektekre hivatkozó szalagcím. Válassza ki a szalagcímet.

    ![Meglévő projektek elérése](./media/create-manage-projects/access-existing-projects.png)

3. Tekintse át a régi projektek listáját.


## <a name="delete-a-project"></a>Projekt törlése

A következőképpen törölheti:

1. Nyissa meg azt az Azure-erőforráscsoportot, amelyben a projekt létrejött.
2. Az erőforráscsoport lapon válassza a **rejtett típusok megjelenítése** elemet.
3. Válassza ki a törölni kívánt áttelepíthető projektet és a hozzá tartozó erőforrásokat.
    - Az erőforrástípus a **Microsoft. migrál/migrateprojects**.
    - Ha az erőforráscsoportot kizárólag az Azure Migrate-projekt használja, akkor törölheti a teljes erőforráscsoportot.

Vegye figyelembe:

- A törléskor a rendszer a projekt és a felderített gépek metaadatait is törli.
- Ha a Azure Migrate régebbi verzióját használja, nyissa meg azt az Azure-erőforráscsoportot, amelyben a projekt létrejött. Válassza ki a törölni kívánt áttelepítési projektet (az erőforrás típusa **áttelepítési projekt**).
- Ha függőségi elemzést használ egy Azure Log Analytics-munkaterülettel:
    - Ha Log Analytics munkaterületet csatolt a kiszolgáló-értékelési eszközhöz, a munkaterület nem törlődik automatikusan. Ugyanaz a Log Analytics munkaterület több forgatókönyv esetén is használható.
    - Ha törölni szeretné a Log Analytics munkaterületet, ezt manuálisan végezze el.
- A projekt törlése nem vonható vissza. A törölt objektumok nem állíthatók helyre.

### <a name="delete-a-workspace-manually"></a>Munkaterület törlése manuálisan

1. Tallózással keresse meg a projekthez csatolt Log Analytics munkaterületet.

    - Ha nem törölte a Azure Migrate projektet, a munkaterületre mutató hivatkozást az **Essentials**-  >  **kiszolgáló értékelése** területen találja.
       ![LA munkaterület ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Ha már törölte a Azure Migrate projektet, válassza ki az **erőforráscsoportok** elemet a Azure Portal bal oldali ablaktábláján, és keresse meg a munkaterületet.
       
2. A munkaterület törléséhez [kövesse az utasításokat](../azure-monitor/platform/delete-workspace.md) .

## <a name="next-steps"></a>További lépések

[Felmérési](how-to-assess.md) vagy [áttelepítési](how-to-migrate.md) eszközök hozzáadása Azure Migrate projektekhez.
