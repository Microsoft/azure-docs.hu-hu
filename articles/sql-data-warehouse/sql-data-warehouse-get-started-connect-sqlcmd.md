---
title: Csatlakozás az Azure SQL Data Warehouse-hoz az sqlcmd használatával | Microsoft Docs
description: Sqlcmd parancssori segédprogram használatával csatlakozhat, és az Azure SQL Data Warehouse lekérdezéséhez.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 72760c5123703a664695c1be4d286a38e96ecc3f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873326"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Csatlakozás az SQL Data Warehouse-hoz az sqlcmd használatával
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Az [sqlcmd][sqlcmd] parancssori segédprogramot az Azure SQL Data Warehouse lekérdezéséhez és az ahhoz való csatlakozáshoz használhatja.  

## <a name="1-connect"></a>1. Kapcsolódás
Az [sqlcmd][sqlcmd] használatának megkezdéséhez nyissa meg a parancssort, és írja be az **sqlcmd** kifejezést, majd a saját SQL Data Warehouse-adatbázisának kapcsolati sztringjét. A kapcsolati sztringben a következő paraméterekre van szükség:

* **Server (-S):** Az űrlap-kiszolgáló `<`kiszolgálónév`>`. database.windows.net
* **Database (-d):** Az adatbázis neve.
* **Enable Quoted azonosítók (-I):** Egy SQL Data Warehouse-példányhoz való csatlakozáshoz engedélyezni kell a határolójeles azonosítókat.

Az SQL Server-hitelesítés használatához meg kell adnia a felhasználónév/jelszó paramétereit:

* **User (-U):** Az űrlap a kiszolgálói felhasználó `<`felhasználó`>`
* **Jelszó (-P):** A felhasználóhoz tartozó jelszót.

A kapcsolati sztring például a következőképpen nézhet ki:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Az Azure Active Directory beépített hitelesítés használatához meg kell adnia az Azure Active Directory paramétereit:

* **Azure Active Directory Authentication (-G):** az Azure Active Directory használata a hitelesítéshez

A kapcsolati sztring például a következőképpen nézhet ki:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Az Active Directory használatával történő hitelesítéshez [engedélyeznie kell az Azure Active Directory-hitelesítést](sql-data-warehouse-authentication.md).
> 
> 

## <a name="2-query"></a>2. Lekérdezés
A kapcsolódás után kiadhatók a példányon a támogatott Transact-SQL utasítások.  Ebben a példában a lekérdezések elküldése interaktív módban történik.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

A következő példák bemutatják, hogyan lehet a lekérdezéseket kötegelt módban futtatni a -Q kapcsoló használatával vagy az SQL átirányításával az sqlcmd-re.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>További lépések
Az sqlcmd-ben elérhető további lehetőségek részleteit az [sqlcmd dokumentációjában][sqlcmd] tekintheti meg.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
