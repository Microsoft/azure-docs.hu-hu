---
title: Csatlakozás dedikált SQL-készlethez (korábban SQL DW) a VSTS használatával
description: Dedikált SQL-készlet (korábban SQL DW) lekérdezése a Azure Synapse Analytics a Visual Studio.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 926e95887f8d6aa164908a4107656074142a969e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566460"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Csatlakozás dedikált SQL-készlethez (korábban SQL DW) Azure Synapse Analytics Visual Studio SSDT használatával

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

A Visual Studio használatával néhány perc alatt lekérdezheti a dedikált SQL-készletet (korábban SQL DW Azure Synapse n belül. Ez a metódus a SQL Server Data Tools (SSDT) bővítményt használja Visual Studio 2019-ben. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Egy meglévő dedikált SQL-készlet (korábban SQL DW). Ennek létrehozásához [lásd: Dedikált SQL-készlet létrehozása (korábban SQL DW).](create-data-warehouse-portal.md)
* SSDT a Visual Studióhoz. Ha már rendelkezik Visual Studio, valószínűleg már rendelkezik SSDT-vel a Visual Studio. A telepítés menetéről és a beállításokról [A Visual Studio és az SSDT telepítése](sql-data-warehouse-install-visual-studio.md) című cikkben olvashat bővebben.
* Az Azure SQL-kiszolgáló teljes neve. Ezen információkért lásd: Csatlakozás dedikált [SQL-készlethez (korábban SQL DW).](sql-data-warehouse-connect-overview.md)

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. Csatlakozás a dedikált SQL-készlethez (korábban SQL DW)
1. Nyissa meg Visual Studio 2019-es Visual Studio.
2. Nyissa SQL Server Object Explorer a Nézet **gombra**  >  **SQL Server Object Explorer.**
   
    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Kattintson az **Add SQL Server** (SQL Server hozzáadása) ikonra.
   
    ![SQL Server hozzáadása](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.
   
    ![Csatlakozás kiszolgálóhoz](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Kiszolgálónév.** Adja meg a korábban azonosított **kiszolgálónevet**.
   * **Hitelesítés:**. Válassza az **SQL Server Authentication** (SQL Server-hitelesítés) vagy az **Active Directory Integrated Authentication** (Active Directory beépített hitelesítés) lehetőséget.
   * **Felhasználónév** és **Jelszó**. Amennyiben az SQL Server-hitelesítést választotta, adja meg felhasználónevét és jelszavát.
   * Kattintson a **Csatlakozás** gombra.
5. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Mintalekérdezés futtatása
Most, hogy létrejött a kapcsolat az adatbázissal, ideje lefuttatni egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza az **Új lekérdezés** lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Futtassa a lekérdezést a zöld nyílra kattintva, vagy használja a következő parancsikont: `CTRL` + `SHIFT` + `E` .
   
    ![A lekérdezés futtatása](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy tud csatlakozni és lekérdezni, próbálja meg [vizualizálni az](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)adatokat az Power BI.

A környezet hitelesítésre való Azure Active Directory lásd: Hitelesítés dedikált [SQL-készletben (korábban SQL DW).](sql-data-warehouse-authentication.md)