---
title: Azure Blockchain Workbench-adatbázis lekérdezése SQL Server Management Studio használatával
description: Megtudhatja, hogy hogyan csatlakozhat az Azure Blockchain Workbench SQL Database-adatbázisához az SQL Server Management Studióból.
ms.date: 11/20/2019
ms.topic: how-to
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: 16e7f9a6c36ea42e1d0a4144e680baebee5a6c21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009466"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Azure Blockchain Workbench-adatok használata az SQL Server Management Studióval

A Microsoft SQL Server Management Studio képes gyorsan írni és tesztelni az Azure Blockchain Workbench SQL DB-vel kapcsolatos lekérdezéseket. Ez a szakasz egy lépésenkénti útmutatót tartalmaz, amely bemutatja, hogyan csatlakozhat az Azure Blockchain Workbench SQL Database a SQL Server Management Studioon belülről.

## <a name="prerequisites"></a>Előfeltételek

* [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)letöltése.

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Az SQL Server Management Studio csatlakoztatása az Azure Blockchain Workbench adataihoz

1. Nyissa meg az SQL Server Management Studio alkalmazást, és válassza a **Connect** (Csatlakozás) lehetőséget.
2. Válassza a **Database Engine** (Adatbázismotor) lehetőséget.

    ![Adatbázismotor](./media/data-sql-management-studio/database-engine.png)

3. A **Connect to Server** (Csatlakozás kiszolgálóhoz) párbeszédpanelen adja meg az adatbázisa hitelesítő adatait.

    Ha az Azure Blockchain Workbench telepítési folyamata során létrehozott hitelesítő adatokat használja, akkor a felhasználónév **dbadmin**, a jelszó pedig a telepítés során megadott jelszó.

    ![SQL-adatbázis hitelesítő adatainak megadása](./media/data-sql-management-studio/sql-creds.png)

   1. Az SQL Server Management Studio képes megjeleníteni az adatbázisokat, adatbázisnézeteket és tárolt eljárásokat az Azure Blockchain Workbench adatbázisából.

      ![Adatbázisok listája](./media/data-sql-management-studio/db-list.png)

5. Az adatbázisnézetekhez tartozó adatok megtekintéséhez automatikusan létrehozhat egy SELECT utasítást az alábbi lépések végrehajtásával.
6. Kattintson a jobb gombbal bármelyik adatbázis-nézetre a Object Explorer.
7. Válassza a **Script View as** (Megtekintési szkript) lehetőséget.
8. Válassza a **SELECT to** (SELECT cél) lehetőséget.
9. Válassza a **New Query Editor Window** (Új Lekérdezésszerkesztő ablak) lehetőséget.
10. Új lekérdezést a **New Query** (Új lekérdezés) lehetőség választásával hozhat létre.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatbázisnézetek az Azure Blockchain Workbenchben](database-views.md)
