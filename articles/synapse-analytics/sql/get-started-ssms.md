---
title: Kapcsolódás a szinapszis SQLhoz a SQL Server Management Studio (SSMS) használatával
description: SQL Server Management Studio (SSMS) használatával kapcsolódhat a szinapszis SQL-hez az Azure szinapszis Analytics szolgáltatásban, és lekérdezheti azokat.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: da698a1a8d91273321d4633abd683a06cb4cf403
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96451621"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Kapcsolódás a szinapszis SQLhoz a SQL Server Management Studio (SSMS) használatával
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

A [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) használatával kapcsolódhat és lekérdezheti a szinapszis SQL-et az Azure szinapszis Analytics szolgáltatásban a kiszolgáló nélküli SQL-készlet vagy a dedikált SQL Pool-erőforrások segítségével. 

### <a name="supported-tools-for-serverless-sql-pool"></a>A kiszolgáló nélküli SQL-készlethez támogatott eszközök

A [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) teljes mértékben támogatott a 1.18.0 verziótól kezdődően. A SSMS részlegesen támogatott a 18,5-es verziótól kezdődően, csak a kapcsolódáshoz és a lekérdezésekhez használható.

> [!NOTE]
> Ha a HRE-bejelentkezés a lekérdezés végrehajtása során több mint 1 órát nyit meg, akkor a HRE-ra támaszkodó összes lekérdezés sikertelen lesz. Ez magában foglalja a tárolás lekérdezését a HRE átmenő és az HRE-t használó utasításokkal (például külső szolgáltató létrehozása). Ez hatással van minden olyan eszközre, amely megtartja a kapcsolatokat, például a SSMS és a HIRDETÉSEKET tartalmazó lekérdezés-szerkesztőben. A lekérdezés végrehajtásához új kapcsolódást megnyitó eszközök nem érintettek, például a szinapszis Studio.
> A probléma megoldásához újraindíthatja a SSMS, vagy csatlakozhat a HIRDETÉSekhez, és leválaszthatja azokat. .
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Dedikált SQL-készlet esetén egy meglévő adattárházra van szükség. A létrehozáshoz tekintse meg [a DEDIKÁLT SQL-készlet létrehozása](../quickstart-create-sql-pool-portal.md)című témakört. A kiszolgáló nélküli SQL-készlethez az egyik a munkaterületen, a létrehozáskor már beépített, beépített néven van kiépítve. 
* A teljesen minősített SQL Server neve. A név megkereséséhez lásd: [Kapcsolódás a SZINAPSZIS SQL](connect-overview.md)-hez.

## <a name="connect"></a>Csatlakozás

### <a name="dedicated-sql-pool"></a>Dedikált SQL-készlet

A következő lépésekkel csatlakozhat a szinapszis SQL-hez a dedikált SQL-készlet használatával: 

1. Nyissa meg SQL Server Management Studio (SSMS). 
1. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a mezőket, majd válassza a **Kapcsolódás** lehetőséget: 
  
    ![Kapcsolódás az 1. kiszolgálóhoz](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Kiszolgáló neve**: adja meg a korábban azonosított **kiszolgálónevet** .
   * **Hitelesítés**: válasszon hitelesítési típust, például **SQL Server hitelesítést** vagy **Active Directory integrált hitelesítést**.
   * **Felhasználónév** és **jelszó**: adja meg felhasználónevét és jelszavát, ha SQL Server hitelesítés lett kiválasztva.

1. Bontsa ki az Azure-SQL Server **Object Explorer**. Megtekintheti a kiszolgálóhoz társított adatbázisokat, például a minta AdventureWorksDW-adatbázist. Az adatbázis kibontásával megjelenítheti a táblákat:
   
    ![Ismerkedés a AdventureWorksDW 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="serverless-sql-pool"></a>Kiszolgáló nélküli SQL-készlet

Ha kiszolgáló nélküli SQL-készlettel szeretne csatlakozni a szinapszis SQL-hez, kövesse az alábbi lépéseket: 

1. Nyissa meg SQL Server Management Studio (SSMS).
1. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a mezőket, majd válassza a **Kapcsolódás** lehetőséget: 
   
    ![Kapcsolódás a 2. kiszolgálóhoz](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Kiszolgáló neve**: adja meg a korábban azonosított **kiszolgálónevet** .
   * **Hitelesítés**: válasszon hitelesítési típust, például **SQL Server hitelesítést** vagy **Active Directory integrált hitelesítést**:
   * **Felhasználónév** és **jelszó**: adja meg felhasználónevét és jelszavát, ha SQL Server hitelesítés lett kiválasztva.
   * Válassza a **Kapcsolódás** lehetőséget.

4. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. Bontsa ki a *bemutatót* a mintaadatbázis tartalmának megtekintéséhez.
   
    ![A 2. AdventureWorksDW megismerése](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Mintalekérdezés futtatása

### <a name="dedicated-sql-pool"></a>Dedikált SQL-készlet

Most, hogy létrejött az adatbázis-kapcsolatok, lekérdezheti az adatforrást.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza az **Új lekérdezés** lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![1. új lekérdezés](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Futtassa a lekérdezést a következő parancsikon kiválasztásával `Execute` vagy használatával: `F5` .
   
    ![1. lekérdezés futtatása](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Tekintse meg a lekérdezés eredményeit. A következő példában a FactInternetSales tábla 60398 sorral rendelkezik.
   
    ![Lekérdezés eredményei 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="serverless-sql-pool"></a>Kiszolgáló nélküli SQL-készlet

Most, hogy létrehozta az adatbázis-kapcsolatokat, lekérdezheti az adatforrást.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza az **Új lekérdezés** lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![2. új lekérdezés](./media/get-started-ssms/new-query.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Futtassa a lekérdezést a következő parancsikon kiválasztásával `Execute` vagy használatával: `F5` .
   
    ![A 2. lekérdezés futtatása](./media/get-started-ssms/execute-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a usPopulationView nézet 3664512 sorral rendelkezik.
   
    ![Lekérdezés eredménye 2](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy tud-e kapcsolatot létesíteni és lekérdezni, próbálja meg [az Power bi segítségével megjeleníteni az adatmegjelenítést](get-started-power-bi-professional.md).

A környezet Azure Active Directory hitelesítéshez való konfigurálásával kapcsolatban lásd: [hitelesítés a SZINAPSZIS SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)-ben.

