---
title: Query Azure Blockchain Workbench data using SQL Server Management Studio
description: Megtudhatja, hogy hogyan csatlakozhat az Azure Blockchain Workbench SQL Database-adatbázisához az SQL Server Management Studióból.
ms.date: 11/20/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: f87d1880c90202fa26b0477e3b4dfbed5965bb82
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326040"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Azure Blockchain Workbench-adatok használata az SQL Server Management Studióval

Microsoft SQL Server Management Studio provides the ability to rapidly write and test queries against Azure Blockchain Workbench's SQL DB. This section contains a step-by-step walkthrough of how to connect to Azure Blockchain Workbench's SQL Database from within SQL Server Management Studio.

## <a name="prerequisites"></a>Előfeltételek

* Töltse le az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) alkalmazást.

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
6. Right-click any of the database views in the Object Explorer.
7. Válassza a **Script View as** (Megtekintési szkript) lehetőséget.
8. Válassza a **SELECT to** (SELECT cél) lehetőséget.
9. Válassza a **New Query Editor Window** (Új Lekérdezésszerkesztő ablak) lehetőséget.
10. Új lekérdezést a **New Query** (Új lekérdezés) lehetőség választásával hozhat létre.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatbázisnézetek az Azure Blockchain Workbenchben](database-views.md)
