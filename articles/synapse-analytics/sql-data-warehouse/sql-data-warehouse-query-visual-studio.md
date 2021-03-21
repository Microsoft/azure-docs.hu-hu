---
title: Kapcsolódás dedikált SQL-készlethez (korábban SQL DW) a VSTS
description: Dedikált SQL-készlet (korábban SQL DW) lekérdezése az Azure szinapszis Analytics és a Visual Studio használatával.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0baf2396b7c5af103f0b3aa223d0bccf725babbe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584142"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Kapcsolódás dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analyticsben a Visual Studióval és a SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

A Visual Studióval mindössze néhány perc alatt lekérdezheti az Azure Szinapszisban található dedikált SQL-készletet (korábban SQL DW). Ez a metódus a SQL Server Data Tools (SSDT) bővítményt használja a Visual Studio 2019-ben. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Egy meglévő dedikált SQL-készlet (korábban SQL DW). A létrehozáshoz tekintse meg [a DEDIKÁLT SQL-készlet (korábban SQL DW) létrehozását](create-data-warehouse-portal.md)ismertető témakört.
* SSDT a Visual Studióhoz. Ha rendelkezik a Visual Studióval, valószínűleg már rendelkezik SSDT a Visual Studióval. A telepítés menetéről és a beállításokról [A Visual Studio és az SSDT telepítése](sql-data-warehouse-install-visual-studio.md) című cikkben olvashat bővebben.
* Az Azure SQL-kiszolgáló teljes neve. Ezen információk megkereséséhez lásd: [Kapcsolódás DEDIKÁLT SQL-készlethez (korábban SQL DW)](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. kapcsolódás a dedikált SQL-készlethez (korábban SQL DW)
1. Nyissa meg a Visual Studio 2019 alkalmazást.
2. SQL Server Object Explorer megnyitásához válassza a **nézet**  >  **SQL Server Object Explorer** lehetőséget.
   
    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Kattintson az **Add SQL Server** (SQL Server hozzáadása) ikonra.
   
    ![SQL Server hozzáadása](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.
   
    ![Csatlakozás kiszolgálóhoz](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * A **kiszolgáló neve**. Adja meg a korábban azonosított **kiszolgálónevet**.
   * **Hitelesítés**. Válassza az **SQL Server Authentication** (SQL Server-hitelesítés) vagy az **Active Directory Integrated Authentication** (Active Directory beépített hitelesítés) lehetőséget.
   * **Felhasználónév** és **Jelszó**. Amennyiben az SQL Server-hitelesítést választotta, adja meg felhasználónevét és jelszavát.
   * Kattintson a **Csatlakozás** gombra.
5. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. minta lekérdezés futtatása
Most, hogy létrejött a kapcsolat az adatbázissal, ideje lefuttatni egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza az **Új lekérdezés** lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. A lekérdezés futtatásához kattintson a zöld nyílra, vagy használja a következő billentyűparancsot: `CTRL` + `SHIFT` + `E` .
   
    ![A lekérdezés futtatása](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy tud-e kapcsolatot létesíteni és lekérdezni, próbálja meg [az Power bi segítségével megjeleníteni az adatmegjelenítést](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect).

A környezet Azure Active Directory hitelesítéshez való konfigurálásával kapcsolatban lásd: [hitelesítés DEDIKÁLT SQL-készletbe (korábban SQL DW)](sql-data-warehouse-authentication.md).