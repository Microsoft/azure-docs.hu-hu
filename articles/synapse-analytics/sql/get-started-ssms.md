---
title: Csatlakozás Synapse SQL SQL Server Management Studio (SSMS) segítségével
description: A SQL Server Management Studio (SSMS) használatával csatlakozhat a Synapse SQL- és Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 222505b03aac5bfc4e8d00b8c8977bece34dee85
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567515"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Csatlakozás Synapse SQL SQL Server Management Studio (SSMS) segítségével
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

A SQL Server Management Studio [(SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) használatával kiszolgáló nélküli SQL-készleten vagy dedikált SQL-készleterőforrásokon keresztül csatlakozhat a Synapse SQL-hez, Azure Synapse Analytics lekérdezéseket is használhat. 

### <a name="supported-tools-for-serverless-sql-pool"></a>A kiszolgáló nélküli SQL-készlet támogatott eszközei

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) az 1.18.0-s verziótól kezdve teljes mértékben támogatott. Az SSMS a 18.5-ös verziótól kezdve részben támogatott, így csak a csatlakozáshoz és a lekérdezéshez használható.

> [!NOTE]
> Ha az AAD-bejelentkezés a lekérdezés végrehajtásakor több mint 1 órán keresztül nyitva van, az AAD-re támaszkodó lekérdezések sikertelenek lesznek. Ebbe beletartozik a tároló lekérdezése az AAD átmenő szolgáltatásával és az AAD-val kommunikáló utasítások használatával (például CREATE EXTERNAL PROVIDER). Ez minden olyan eszközre hatással van, amely nyitva tartja a kapcsolatot, például az SSMS és az ADS lekérdezésszerkesztőjével. A lekérdezés végrehajtásához új kapcsolatot megnyévő eszközökre nincs hatással, például a Synapse Studio.
> A probléma megoldásához újraindíthatja az SSMS-t, vagy csatlakozhat az ADS-hez, és leválaszthatja a kapcsolatot. .
## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg arról, hogy a következő előfeltételeknek kell megfeleltetve:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Dedikált SQL-készlethez egy meglévő adattárházra van szükség. Ennek létrehozásához [lásd: Dedikált SQL-készlet létrehozása.](../quickstart-create-sql-pool-portal.md) Kiszolgáló nélküli SQL-készlet esetén az egyik már ki van építve Beépített névvel a munkaterületen a létrehozáskor. 
* A teljes SQL Server neve. A név keresését lásd: [Csatlakozás](connect-overview.md)a Synapse SQL.

## <a name="connect"></a>Csatlakozás

### <a name="dedicated-sql-pool"></a>Dedikált SQL-készlet

Ha dedikált SQL Synapse SQL készlet használatával szeretne csatlakozni a virtuális hálózathoz, kövesse az alábbi lépéseket: 

1. Nyissa SQL Server Management Studio (SSMS) gombra. 
1. A Csatlakozás **kiszolgálóhoz** párbeszédpanelen töltse ki a mezőket, majd válassza a Csatlakozás **lehetőséget:** 
  
    ![Csatlakozás a Server 1-hez](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Kiszolgáló neve:** Adja meg **a korábban azonosított** kiszolgálónevet.
   * **Hitelesítés:** Válasszon hitelesítési típust, például SQL Server **hitelesítést** **vagy integrált Active Directory hitelesítést.**
   * **Felhasználónév és** **jelszó:** Adja meg a felhasználónevét és jelszavát, ha SQL Server a hitelesítést választotta.

1. Bontsa ki Azure SQL-kiszolgálót **a Object Explorer.** Megtekintheti a kiszolgálóhoz társított adatbázisokat, például az AdventureWorksDW mintaadatbázist. Az adatbázist kibontva láthatja a táblákat:
   
    ![Az AdventureWorksDW 1 megismerés](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="serverless-sql-pool"></a>Kiszolgáló nélküli SQL-készlet

Ha kiszolgáló nélküli SQL Synapse SQL készlet használatával szeretne csatlakozni a virtuális hálózathoz, kövesse az alábbi lépéseket: 

1. Nyissa SQL Server Management Studio (SSMS) gombra.
1. A Kapcsolódás **kiszolgálóhoz** párbeszédpanelen töltse ki a mezőket, majd válassza a Csatlakozás **lehetőséget:** 
   
    ![Csatlakozás a Server 2-hez](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Kiszolgáló neve:** Adja meg **a korábban azonosított** kiszolgálónevet.
   * **Hitelesítés:** Válasszon hitelesítési típust, például SQL Server **hitelesítést** **vagy Active Directory integrált hitelesítést:**
   * **Felhasználónév és** **jelszó:** Adja meg a felhasználónevét és jelszavát, ha SQL Server a hitelesítést választotta.
   * Válassza a **Kapcsolódás** lehetőséget.

4. A részletes megtekintéshez bontsa ki az Azure SQL-kiszolgálót. Megtekintheti a kiszolgálóhoz társított adatbázisokat. *Bontsa ki a bemutatót,* és tekintse meg a mintaadatbázis tartalmát.
   
    ![Az AdventureWorksDW 2 megismerés](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Mintalekérdezés futtatása

### <a name="dedicated-sql-pool"></a>Dedikált SQL-készlet

Most, hogy létrejött az adatbázis-kapcsolat, lekérdezheti az adatokat.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza az **Új lekérdezés** lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés – 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Futtassa a lekérdezést a következő `Execute` parancsikon kiválasztásával vagy használatával: `F5` .
   
    ![1. lekérdezés futtatása](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Tekintse meg a lekérdezés eredményeit. A következő példában a FactInternetSales tábla 60398 sort tartalmaz.
   
    ![Lekérdezési eredmények 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="serverless-sql-pool"></a>Kiszolgáló nélküli SQL-készlet

Most, hogy létrehozott egy adatbázis-kapcsolatot, lekérdezheti az adatokat.

1. Kattintson a jobb gombbal az adatbázisára az SQL Server Object Explorer alatt.
2. Válassza az **Új lekérdezés** lehetőséget. Megnyílik egy új lekérdezési ablak.
   
    ![Új lekérdezés – 2](./media/get-started-ssms/new-query.png)
3. Másolja a következő T-SQL-lekérdezést a lekérdezési ablakba:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Futtassa a lekérdezést a következő parancsikon `Execute` kiválasztásával vagy használatával: `F5` .
   
    ![A 2. lekérdezés futtatása](./media/get-started-ssms/execute-query.png)
5. Tekintse meg a lekérdezés eredményeit. Ebben a példában a usPopulationView nézet 3664512 sort tartalmaz.
   
    ![Lekérdezési eredmények 2](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy csatlakozhat és lekérdezhet, próbálja meg [vizualizálni az](get-started-power-bi-professional.md)adatokat az Power BI.

A környezet hitelesítésre való konfigurálás Azure Active Directory: [Hitelesítés](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)a Synapse SQL.

