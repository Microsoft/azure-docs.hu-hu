---
title: Kapcsolódás dedikált SQL-készlethez (korábban SQL DW) a SSMS
description: A SQL Server Management Studio (SSMS) használatával csatlakozhat egy dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 950cb4c40a534f252ec8b0daa5a57eb87c098450
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450473"
---
# <a name="connect-to-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Kapcsolódás dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analyticsben SQL Server Management Studio (SSMS) használatával

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

A SQL Server Management Studio (SSMS) használatával csatlakozhat egy dedikált SQL-készlethez (korábbi nevén SQL DW), és lekérdezheti azokat.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Egy meglévő dedikált SQL-készlet. A létrehozáshoz tekintse meg [a DEDIKÁLT SQL-készlet (korábban SQL DW) létrehozását](create-data-warehouse-portal.md)ismertető témakört.
* SQL Server Management Studio (SSMS) telepítve. [Töltse le](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ingyenesen a SSMS, ha még nem rendelkezik vele.
* Az Azure SQL-kiszolgáló teljes neve. Ezen információk megkereséséhez tekintse meg a [DEDIKÁLT SQL-készlet (korábban SQL DW)](sql-data-warehouse-connect-overview.md)című témakört.

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. kapcsolódás a dedikált SQL-készlethez (korábban SQL DW)

1. Nyissa meg az SSMS-t.
2. A **fájl**-  >  **csatlakozási Object Explorer** kiválasztásával nyissa meg Object Explorer.

    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.

   ![Csatlakozás kiszolgálóhoz](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * A **kiszolgáló neve**. Adja meg a korábban azonosított **kiszolgálónevet**.
   * **Hitelesítés**. Válassza az **SQL Server Authentication** (SQL Server-hitelesítés) vagy az **Active Directory Integrated Authentication** (Active Directory beépített hitelesítés) lehetőséget.
   * **Felhasználónév** és **Jelszó**. Amennyiben az SQL Server-hitelesítést választotta, adja meg felhasználónevét és jelszavát.
   * Kattintson a **Csatlakozás** gombra.
4. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.

   ![Az AdventureWorksDW áttekintése](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. minta lekérdezés futtatása

Most, hogy létrejött a kapcsolat az adatbázissal, ideje lefuttatni egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza az **Új lekérdezés** lehetőséget. Megnyílik egy új lekérdezési ablak.

   ![Új lekérdezés](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Futtassa a lekérdezést a `Execute` következő parancsikonra kattintva vagy annak használatával: `F5` .

   ![A lekérdezés futtatása](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.

   ![Lekérdezés eredményei](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>További lépések

Most, hogy tud-e kapcsolatot létesíteni és lekérdezni, próbálja meg [az Power bi segítségével megjeleníteni az adatmegjelenítést](sql-data-warehouse-get-started-visualize-with-power-bi.md). A környezet Azure Active Directory hitelesítéshez való konfigurálásával kapcsolatban lásd: [hitelesítés DEDIKÁLT SQL-készletbe](sql-data-warehouse-authentication.md).
