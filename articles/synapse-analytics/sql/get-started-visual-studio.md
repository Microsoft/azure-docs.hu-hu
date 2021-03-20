---
title: A szinapszis SQL összekötése és lekérdezése a Visual Studióval és a SSDT
description: A Visual Studio használatával lekérdezheti a dedikált SQL-készletet az Azure szinapszis Analytics segítségével.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: ef8e2a3d1a6b78e8f2b6b9a900ed2485c1a4a5d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96451594"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Csatlakozás a Synapse SQL-hez a Visual Studióval és az SSDT-vel

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [Sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

A Visual Studio használatával lekérdezheti a dedikált SQL-készletet az Azure szinapszis Analytics segítségével. Ez a metódus a SQL Server Data Tools (SSDT) bővítményt használja a Visual Studio 2019-ben. 

> [!NOTE]
> A SSDT nem támogatja a kiszolgáló nélküli SQL-készletet.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag használatához a következő összetevőket kell használnia:

* Egy meglévő dedikált SQL-készlet. Ha még nem rendelkezik ilyennel, tekintse meg a [DEDIKÁLT SQL-készlet létrehozása](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) az előfeltételek elvégzéséhez című témakört.
* SSDT a Visual Studióhoz. Ha rendelkezik a Visual Studióval, valószínűleg már rendelkezik ezzel az összetevővel. A telepítés menetéről és a beállításokról [A Visual Studio és az SSDT telepítése](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című cikkben olvashat bővebben.
* Az Azure SQL-kiszolgáló teljes neve. A kiszolgáló nevének megkereséséhez lásd: [Kapcsolódás DEDIKÁLT SQL-készlethez](connect-overview.md).

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1. kapcsolódás dedikált SQL-készlethez
1. Nyissa meg a Visual Studio 2019 alkalmazást.
2. Nyissa meg a SQL Server Object Explorer a **nézet** SQL Server Object Explorer lehetőség kiválasztásával  >  .
   
    ![SQL Server Object Explorer](./media/get-started-visual-studio/open-ssdt.png)
3. Kattintson az **Add SQL Server** (SQL Server hozzáadása) ikonra.
   
    ![SQL Server hozzáadása](./media/get-started-visual-studio/add-server.png)
4. Töltse ki az összes mezőt a Connect to Server (Csatlakozás a kiszolgálóhoz) ablakban.
   
    ![Csatlakozás kiszolgálóhoz](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Kiszolgáló neve**: adja meg a korábban azonosított **kiszolgálónevet** .
   * **Hitelesítés**: válassza **SQL Server hitelesítés** vagy **Active Directory integrált hitelesítés** lehetőséget:
   * **Felhasználónév** és **jelszó**: adja meg felhasználónevét és jelszavát, ha SQL Server hitelesítés lett kiválasztva.
   * Kattintson a **Csatlakozás** gombra.
5. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki az AdventureWorksDW elemet a mintaadatbázis tábláinak megtekintéséhez.
   
    ![Az AdventureWorksDW áttekintése](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. minta lekérdezés futtatása
Most, hogy létrejött egy kapcsolódás az adatbázishoz, meg kell írnia egy lekérdezést.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza az **Új lekérdezés** lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés](./media/get-started-visual-studio/new-query2.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. A lekérdezés futtatásához kattintson a zöld nyílra, vagy használja a következő billentyűparancsot: `CTRL` + `SHIFT` + `E` .
   
    ![A lekérdezés futtatása](./media/get-started-visual-studio/run-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a FactInternetSales táblának 60 398 sora van.
   
    ![Lekérdezés eredményei](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy tud-e kapcsolatot létesíteni és lekérdezni, próbálja meg [az Power bi segítségével megjeleníteni az adatmegjelenítést](get-started-power-bi-professional.md).
A környezet Azure Active Directory hitelesítéshez való konfigurálásával kapcsolatban lásd: [hitelesítés DEDIKÁLT SQL-készletbe](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 