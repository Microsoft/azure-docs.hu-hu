---
title: Projektek létrehozása és kezelése
description: Projekteket találhat, hozhat létre, kezelhet és törölhet a Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0c9102f8ca724e431bb478945c5f4ba0369643d6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714836"
---
# <a name="create-and-manage-projects"></a>Projektek létrehozása és kezelése

Ez a cikk bemutatja, hogyan hozhat létre, kezelhet és törölhet [projekteket.](migrate-services-overview.md) 

A Azure Migrate 2024. februárban kieső klasszikus Azure Migrate lesz. 2024. február után a Azure Migrate klasszikus verziója nem lesz támogatva, és a klasszikus projekt leltáradatai törlődnek. Ha klasszikus projekteket használ, törölje ezeket a projekteket, és kövesse a lépéseket egy új projekt létrehozásához. A klasszikus projektek és összetevők nem frissíthetőek a Azure Migrate. A [létrehozási](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) folyamat előtt tekintse meg a gyakori kérdéseket.

A Project az értékelni vagy migrált környezetből gyűjtött felderítési, felmérési és migrálási metaadatok tárolására használható. A projektekben nyomon követheti a felderített eszközöket, felméréseket hozhat létre, és vezényelheti az Azure-ba való migrálást.  

## <a name="verify-permissions"></a>Engedélyek ellenőrzése

Ellenőrizze, hogy rendelkezik-e a megfelelő engedélyekkel a projekt létrehozásához:

1. A Azure Portal nyissa meg a megfelelő előfizetést, és válassza a **Hozzáférés-vezérlés (IAM) lehetőséget.**
2. A **Hozzáférés ellenőrzése mezőben** keresse meg a megfelelő fiókot, és válassza ki az engedélyek megtekintéséhez. Közreműködői *vagy* *tulajdonosi* engedélyekkel kell rendelkeznie. 


## <a name="create-a-project-for-the-first-time"></a>Projekt létrehozása első alkalommal

Állítson be egy új projektet egy Azure-előfizetésben.

1. A Azure Portal keresse meg a *Azure Migrate.*
2. A **Services (Szolgáltatások)** mezőben válassza a **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen válassza a **Kiszolgálók értékelése és migrálása** lehetőséget.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="A kiszolgálók értékelésének és áttelepítésének lehetősége az Áttekintésben":::

4. A **Servers (Kiszolgálók)** mezőben válassza a **Create project (Projekt létrehozása) lehetőséget.**

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="A projekt létrehozásának elkezdő gombja":::

5. A **Projekt létrehozása mezőben** válassza ki az Azure-előfizetést és -erőforráscsoportot. Ha még nincs erőforráscsoportja, hozzon létre egyet.
6. A **Project Details (Projekt részletei)** alatt adja meg a projekt nevét és a földrajzi helyeket, ahol létre szeretné hozni a projektet.
    - A földrajzi hely csak a helyszíni kiszolgálókról gyűjtött metaadatok tárolására szolgál. A migráláshoz bármelyik célterületet kiválaszthatja. 
    - Tekintse át a nyilvános és kormányzati [felhők](migrate-support-matrix.md#supported-geographies-public-cloud) [támogatott földrajzi helyeket.](migrate-support-matrix.md#supported-geographies-azure-government) 


    > [!Note]
    > A Speciális **konfiguráció** szakaszban hozzon létre egy Azure Migrate privát végpontkapcsolattal. [További információ](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity) 

7. Válassza a **Létrehozás** lehetőséget.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Lap a projektbeállítások beviteléhez":::


Várjon néhány percet, amíg a projekt üzembe helyezése meg nem kezdődik.

## <a name="create-a-project-in-a-specific-region"></a>Projekt létrehozása egy adott régióban

A portálon kiválaszthatja a földrajzi helyeket, ahol létre szeretné hozni a projektet. Ha a projektet egy adott Azure-régióban szeretné létrehozni, használja a következő API-parancsot a projekt létrehozásához.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>További projektek létrehozása

Ha már rendelkezik egy projekttel, és szeretne létrehozni egy további projektet, tegye a következőket:  

1. Az [Azure nyilvános portálon vagy a](https://portal.azure.com) [Azure Government](https://portal.azure.us)keressen rá a **Azure Migrate.**
2. A Azure Migrate irányítópult kiszolgálók > **jobb** felső  sarkában válassza a módosítás lehetőséget.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Projekt módosítása":::

3. Új projekt létrehozásához kattintson **ide.**


## <a name="find-a-project"></a>Projekt megkerese

Keresse meg a projektet a következőképpen:

1. A [(Azure Portal)](https://portal.azure.com)között keressen rá a *Azure Migrate.*
2. A Azure Migrate kiszolgálók > **jobb** felső  sarkában válassza a módosítás lehetőséget.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Váltás meglévő projektre":::

3. Válassza ki a megfelelő előfizetést és projektet.


### <a name="find-a-classic-project"></a>Klasszikus projekt megkeresve

Ha a projektet a [](migrate-services-overview.md#azure-migrate-versions) projekt előző verziójában hozta Azure Migrate, keresse meg a következőképpen:

1. A [Azure Portal](https://portal.azure.com)keressen rá a *következőre: Azure Migrate.*
2. Ha az Azure Migrate létrehozott egy projektet az előző verzióban, megjelenik egy szalagcím, amely a régebbi projektekre hivatkozik. Válassza ki a szalagcímet.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Meglévő projektek elérése":::

3. Tekintse át a régi projektek listáját.


## <a name="delete-a-project"></a>Projekt törlése

Törölje a következőképpen:

1. Nyissa meg azt az Azure-erőforráscsoportot, amelyben a projekt létre lett hozva.
2. Az erőforráscsoport oldalán válassza a **Rejtett típusok megjelenítése lehetőséget.**
3. Válassza ki a törölni kívánt projektet és a hozzá tartozó erőforrásokat.
    - Az erőforrás típusa **Microsoft.Migrate/migrateprojects.**
    - Ha az erőforráscsoportot kizárólag a projekt használja, a teljes erőforráscsoportot törölheti.

Vegye figyelembe:

- Törléskor a rendszer a projektet és a felderített kiszolgálók metaadatait is törli.
- Ha a projekt régebbi verzióját Azure Migrate, nyissa meg azt az Azure-erőforráscsoportot, amelyben a projektet létrehozták. Válassza ki a törölni kívánt projektet (az erőforrás típusa **Migration project**).
- Ha függőségelemzést használ egy Azure Log Analytics-munkaterülettel:
    - Ha Log Analytics-munkaterületet csatolt a Server Assessment eszközhöz, a munkaterület nem törlődik automatikusan. Ugyanaz a Log Analytics-munkaterület több forgatókönyvben is használható.
    - Ha törölni szeretné a Log Analytics-munkaterületet, azt manuálisan tegye meg.
- A projekt törlése nem visszafordítható. Törölt objektumok nem állíthatók helyre.

### <a name="delete-a-workspace-manually"></a>Munkaterület manuális törlése

1. Keresse meg a projekthez csatolt Log Analytics-munkaterületet.

    - Ha még nem törölte a projektet, a munkaterületre mutató hivatkozást az **Essentials**  >  **Server Assessmentben találja.**
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="LA-munkaterület":::
       
    - Ha már törölte a projektet,  a bal oldali panelen válassza az Erőforráscsoportok lehetőséget, Azure Portal keresse meg a munkaterületet.
       
2. [A munkaterület törléséhez](../azure-monitor/logs/delete-workspace.md) kövesse az utasításokat.

## <a name="next-steps"></a>Következő lépések

Értékelési vagy [migrálási eszközök hozzáadása](how-to-migrate.md) projektekhez. [](how-to-assess.md)