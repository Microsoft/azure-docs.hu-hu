---
title: SQL Server példányok felderítése egy meglévő Azure Migrate projektben
description: Megtudhatja, hogyan derítheti fel SQL Server példányokat egy meglévő Azure Migrate projektben.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 2de60880b511e43ffb2949a15fec2cf2a94f62fa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567152"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>SQL Server példányok felderítése meglévő projektekben 

Ez a cikk azt ismerteti, hogyan lehet felderíteni SQL Server példányokat és adatbázisokat egy olyan [Azure Migrate](./migrate-services-overview.md) projektben, amelyet az Azure SQL Assessment funkció előzetes verziójának létrehozása előtt hoztak létre.

SQL Server példányok és a helyszíni gépeken futó adatbázisok felderítése segít azonosítani és testre szabni az Azure SQL áttelepítési útvonalát. A Azure Migrate berendezés a tartományi hitelesítő adatokkal végzi el ezt a felderítést, vagy SQL Server hitelesítő adatokat, amelyek hozzáférnek a megcélozott kiszolgálókon futó SQL Server példányokhoz és adatbázisokhoz. Ez a felderítési folyamat ügynök nélküli, nincs telepítve a célkiszolgálón.

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg az alábbiakról:
    - A régió SQL Assessment szolgáltatásának bejelentése előtt létrehozott egy [Azure Migrate projektet](./create-manage-projects.md)
    - A [Azure Migrate: felderítési és értékelési](./how-to-assess.md) eszköz hozzáadása egy projekthez
- Tekintse át [az alkalmazás-felderítési támogatást és a követelményeket](./migrate-support-matrix-vmware.md#vmware-requirements).
-  Győződjön meg arról, hogy az App-Discovery alkalmazást futtató kiszolgálókon telepítve van a PowerShell 2,0-es vagy újabb verziója, a VMware-eszközök pedig (a 10.2.0-nál újabb verziók).
- Az Azure Migrate berendezés telepítésére [vonatkozó követelmények](./migrate-appliance.md) megtekintése.
- Győződjön meg arról, hogy rendelkezik az előfizetésben [szükséges szerepkörökkel](./create-manage-projects.md#verify-permissions) az erőforrások létrehozásához.
- Gondoskodjon arról, hogy a készülék hozzáférjen az internethez

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>SQL Server példányok és adatbázisok felderítésének engedélyezése

1. A Azure Migrate projektben vagy
    - Válassza a **nincs engedélyezve** lehetőséget a hub csempén, vagy az   :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="SQL-felderítéssel nem rendelkező Azure Migrate hub-csempét":::
    - Válassza a **nincs engedélyezve** lehetőséget a kiszolgáló felderítése lap SQL-példányok oszlopában,   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Azure Migrate a felderített kiszolgálók panelt, amelyen az SQL-felderítés nincs engedélyezve":::
2. A SQL Server-példányok és-adatbázisok felderítése szakasz a következő lépésekkel jár:
    - Válassza a **frissítés** lehetőséget a szükséges erőforrás létrehozásához.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Azure Migrate készülék frissítésének gombja":::
    - Ellenőrizze, hogy a készüléken futó szolgáltatások frissítve vannak-e a legújabb verzióra. Ehhez indítsa el a készülék Configuration Managert a berendezés-kiszolgálóról, és válassza a berendezés-szolgáltatások megtekintése lehetőséget a telepítési előfeltételek panelen.
        - A készülék és annak összetevői automatikusan frissülnek :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="a készülék verziójának vizsgálatával"::: .
    - A készülék Configuration Manager hitelesítő adatok kezelése és felderítési források paneljén adja meg azokat a tartományi vagy SQL Server hitelesítő adatokat, amelyek rendszergazdai hozzáféréssel rendelkeznek a SQL Server példányon és a felderített adatbázisokon.
    Kihasználhatja a készülék automatikus hitelesítő adatok leképezése szolgáltatását, vagy manuálisan is leképezheti a hitelesítő adatokat a megfelelő kiszolgálóra az [itt](./tutorial-discover-vmware.md#start-continuous-discovery)kijelölt módon.

    Néhány Megjegyzés:
    - Győződjön meg arról, hogy a szoftver leltározása már engedélyezve van, vagy adjon meg tartományi vagy nem tartományi hitelesítő adatokat az engedélyezéshez. SQL Server példányok felderítéséhez a szoftver leltározását kell elvégezni.
    - A készülék megkísérli érvényesíteni a tartományi hitelesítő adatokat az AD-vel a hozzáadásuk után. Győződjön meg arról, hogy a berendezés-kiszolgáló hálózati vonallal rendelkezik a hitelesítő adatokhoz társított AD-kiszolgálóval. A SQL Server hitelesítéshez társított hitelesítő adatok nincsenek érvényesítve.

3. A kívánt hitelesítő adatok hozzáadása után válassza a felderítés indítása lehetőséget a vizsgálat megkezdéséhez.

> [!Note]
>Az Azure SQL-elemzések létrehozása előtt engedélyezze az SQL Discovery futtatását. Ha SQL Server példányok és adatbázisok felderítését nem lehet befejezni, a megfelelő példányok **ismeretlenként** vannak megjelölve az értékelési jelentésben.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan hozhat létre [Azure SQL-értékelést](./how-to-create-azure-sql-assessment.md)
- További információ az [Azure SQL-értékelésekről](./concepts-azure-sql-assessment-calculation.md)