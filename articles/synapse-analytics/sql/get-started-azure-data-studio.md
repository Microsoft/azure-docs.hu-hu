---
title: Kapcsolódás a szinapszis SQLhoz a Azure Data Studio használatával
description: A Azure Data Studio használatával csatlakozhat a szinapszis SQL-hez az Azure szinapszis Analytics szolgáltatásban, és lekérdezheti azokat.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f82a1279a69bf91f37400d5614e8f7b121e72f47
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258897"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio"></a>Kapcsolódás a szinapszis SQLhoz a Azure Data Studio használatával

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

A [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használatával kapcsolódhat a szinapszis SQL-hez, és lekérdezheti őket az Azure szinapszis Analytics szolgáltatásban. 

## <a name="connect"></a>Kapcsolódás

A szinapszis SQLhoz való kapcsolódáshoz nyissa meg Azure Data Studio és válassza az **új kapcsolat** lehetőséget.

![Azure Data Studio megnyitása](./media/get-started-azure-data-studio/1-start.png)

Válassza a **Microsoft SQL Server** lehetőséget a **kapcsolattípus mezőben**.

A kapcsolathoz a következő paraméterek szükségesek:

* **Kiszolgáló:** Kiszolgáló az űrlapon `<Azure Synapse workspace name>` – OnDemand.SQL.azuresynapse.net
* **Adatbázis:** Adatbázis neve

> [!NOTE]
> Ha a kiszolgáló nélküli SQL- **készletet** szeretné használni, az URL-címnek a következőhöz hasonlóan kell kinéznie:
>
> - `<Azure Synapse workspace name>`– ondemand.sql.azuresynapse.net.
>
> Ha **DEDIKÁLT SQL-készletet** szeretne használni, az URL-címnek a következőképpen kell kinéznie:
>
> - `<Azure Synapse workspace name>`. sql.azuresynapse.net

**Hitelesítési típusként** válassza a **Windows-hitelesítés**, a **Azure Active Directory** vagy az **SQL-bejelentkezés** lehetőséget.

Ha az **SQL-bejelentkezést** hitelesítési típusként szeretné használni, adja hozzá a username/Password paramétert:

* **Felhasználó:** Kiszolgáló felhasználója az űrlapon `<User>`
* **Jelszó:** A felhasználóhoz tartozó jelszó

Azure Active Directory használatához ki kell választania a szükséges hitelesítési típust.

![AAD-hitelesítés](./media/get-started-azure-data-studio/3-aad-auth.png)

A következő képernyőképen a **Windows-hitelesítés** **kapcsolati adatai** láthatók:

![Windows-hitelesítés](./media/get-started-azure-data-studio/3-windows-auth.png)

Az alábbi képernyőképen az **SQL-bejelentkezés** használatával láthatók a **kapcsolat részletei** :

![SQL-bejelentkezés](./media/get-started-azure-data-studio/2-database-details.png)

A sikeres bejelentkezés után az alábbihoz hasonló irányítópultot kell látnia: ![ irányítópult](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Lekérdezés

A csatlakozást követően lekérdezheti a szinapszis SQLot a példányon támogatott [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) utasítások használatával. Első lépésként válassza az irányítópult nézet **Új lekérdezés** elemét.

![Új lekérdezés](./media/get-started-azure-data-studio/5-new-query.png)

Például a következő Transact-SQL-utasítás használatával lehet [lekérdezni a Parquet-fájlokat](query-parquet-files.md) a kiszolgáló nélküli SQL-készlet használatával:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Következő lépések 
Ismerkedjen meg a szinapszis SQL-hez való kapcsolódás egyéb módjaival: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Sqlcmd](get-started-connect-sqlcmd.md)

További információért látogasson el a [Azure Data Studio használatára az adatok összekapcsolásához és lekérdezéséhez egy DEDIKÁLT SQL-készlet használatával az Azure szinapszis Analyticsben](/sql/azure-data-studio/quickstart-sql-dw).
