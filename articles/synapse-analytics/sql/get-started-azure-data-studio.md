---
title: Csatlakozás Synapse SQL Azure Data Studio
description: A Azure Data Studio csatlakozhat, és lekérdezéseket Synapse SQL a Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 1529e45bd7f799a727a29c8c2e26f7ed77c4e2a0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565593"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio"></a>Csatlakozás Synapse SQL Azure Data Studio

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

A Azure Data Studio [csatlakozhat,](/sql/azure-data-studio/download-azure-data-studio?view=azure-sqldw-latest&preserve-view=true) és lekérdezéseket Synapse SQL a Azure Synapse Analytics. 

## <a name="connect"></a>Csatlakozás

A kapcsolathoz való Synapse SQL nyissa meg a Azure Data Studio, és válassza az **Új kapcsolat lehetőséget.**

![Nyissa meg Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Válassza **Microsoft SQL Server** **kapcsolattípusként a következőt:**.

A kapcsolathoz a következő paraméterekre van szükség:

* **Kiszolgáló:** Kiszolgáló `<Azure Synapse workspace name>` -ondemand.sql.azuresynapse.net
* **Adatbázis:** Adatbázis neve

> [!NOTE]
> Ha kiszolgáló nélküli **SQL-készletet szeretne használni,** az URL-címnek így kell kinéznie:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Ha dedikált SQL-készletet **szeretne használni,** az URL-címnek így kell kinéznie:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Válassza **a Windows-hitelesítés,** **Azure Active Directory**, vagy **az SQL-bejelentkezés** hitelesítési **típust.**

Ha **sql-bejelentkezést szeretne** használni hitelesítési típusként, adja hozzá a felhasználónév/jelszó paramétereket:

* **Felhasználó:** Kiszolgálófelhasználó az űrlapon `<User>`
* **Jelszó:** A felhasználóhoz társított jelszó

A Azure Active Directory meg kell választania a szükséges hitelesítési típust.

![AAD-hitelesítés](./media/get-started-azure-data-studio/3-aad-auth.png)

Az alábbi képernyőképen a **Windows-hitelesítés kapcsolati** **adatai láthatóak:**

![Windows-hitelesítés](./media/get-started-azure-data-studio/3-windows-auth.png)

Az alábbi képernyőképen a **Kapcsolat részletei az SQL-bejelentkezés** **használatával látható:**

![SQL-bejelentkezés](./media/get-started-azure-data-studio/2-database-details.png)

A sikeres bejelentkezés után a következő irányítópultnak kell lennie: ![ Irányítópult](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Lekérdezés

A csatlakozás után a támogatott [Transact-SQL Synapse SQL (T-SQL)](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) utasításokkal lekérdezheti a példányokat. Az **első lépésekhez** válassza az Új lekérdezés lehetőséget az irányítópult nézetben.

![Új lekérdezés](./media/get-started-azure-data-studio/5-new-query.png)

A következő Transact-SQL-utasítással például lekérdezhet [Parquet-fájlokat](query-parquet-files.md) kiszolgáló nélküli SQL-készlet használatával:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Következő lépések 
Ismerkedés a további kapcsolódási Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)

További [információ: Azure Data Studio csatlakozás és adatlekérdezés](/sql/azure-data-studio/quickstart-sql-dw)dedikált SQL-készlet használatával a Azure Synapse Analytics szolgáltatásban.
