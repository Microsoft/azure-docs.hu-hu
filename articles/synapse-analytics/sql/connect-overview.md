---
title: Kapcsolódás a szinapszis SQL-hez
description: Kapcsolódjon a szinapszis SQL-hez.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 3165b72ae5612aaeaa66a95299db97182fef2232
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677578"
---
# <a name="connect-to-synapse-sql"></a>Kapcsolódás a szinapszis SQL-hez
Kapcsolódjon a szinapszis SQL-képességhez az Azure szinapszis Analytics szolgáltatásban.

## <a name="supported-tools-for-serverless-sql-pool"></a>A kiszolgáló nélküli SQL-készlethez támogatott eszközök

A [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) teljes mértékben támogatott a 1.18.0 verziótól kezdődően. A SSMS részlegesen támogatott a 18,5-es verziótól kezdődően, csak a kapcsolódáshoz és a lekérdezésekhez használható.

> [!NOTE]
> Ha egy HRE-bejelentkezésnél a lekérdezés végrehajtása során több mint 1 óra van nyitva, a HRE-ra épülő lekérdezések sikertelenek lesznek. Ez magában foglalja a tárolás lekérdezését a HRE átmenő és az HRE-t használó utasításokkal (például külső szolgáltató létrehozása). Ez hatással van minden olyan eszközre, amely megtartja a kapcsolatokat, például a SSMS és a HIRDETÉSEKET tartalmazó lekérdezés-szerkesztőben. Azok az eszközök, amelyek új kapcsolatokat nyitnak meg egy lekérdezés végrehajtásához, mint például a szinapszis Studio, nem érintettek.

> A probléma megoldásához újraindíthatja a SSMS, vagy csatlakozhat a HIRDETÉSekhez, és leválaszthatja azokat. 

## <a name="find-your-server-name"></a>A kiszolgálónév lekérdezése

A következő példában a dedikált SQL-készlet kiszolgálójának neve: showdemoweu.sql.azuresynapse.net.
A kiszolgáló nélküli SQL-készlet kiszolgálójának neve a következő példában: showdemoweu-ondemand.sql.azuresynapse.net.

A teljes kiszolgálónév lekérdezése:

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Válassza a **szinapszis-munkaterületek** lehetőséget.
3. Válassza ki azt a munkaterületet, amelyhez csatlakozni szeretne.
4. Ugrás az áttekintéshez.
5. Keresse meg a teljes kiszolgálónevet.

## <a name="sql-pool"></a>**SQL-készlet**

![Teljes kiszolgálónév](./media/connect-overview/server-connect-example.png)

## <a name="serverless-sql-pool"></a>**kiszolgáló nélküli SQL-készlet**

![Teljes kiszolgálónév kiszolgáló nélküli SQL-készlete](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Támogatott illesztők és kapcsolati sztringek
A szinapszis SQL támogatja a [ADO.net](/dotnet/framework/data/adonet/), az [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows), a [php](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)és a [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)használatát. A legújabb verzió és dokumentáció megkereséséhez válasszon egyet az előző illesztőprogramok közül. Ha az illesztőprogramhoz tartozó kapcsolati karakterláncot szeretné automatikusan létrehozni a Azure Portalból, válassza az előző példában az **adatbázis-kapcsolati karakterláncok megjelenítése** lehetőséget. A következő néhány példa bemutatja, hogy néz ki a kapcsolati sztring az egyes illesztők esetében.

> [!NOTE]
> Javasoljuk, hogy a kapcsolat időkorlátjának 300 másodpercet adjon meg, hogy a kapcsolat rövid idejű kimaradások esetén is fennmaradjon.

### <a name="adonet-connection-string-example"></a>Minta ADO.NET kapcsolati sztring

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Minta ODBC kapcsolati sztring

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Minta PHP kapcsolati sztring

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Minta JDBC kapcsolati sztring

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Kapcsolati beállítások
A szinapszis SQL bizonyos beállításokat a kapcsolatok és az objektumok létrehozása során Szabványosít. Ezek a beállítások nem lehetnek felülbírálva, és a következők:

| Adatbázis-beállítások | Érték |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?view=azure-sqldw-latest&preserve-view=true) |hné |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?view=azure-sqldw-latest&preserve-view=true) |7 |

## <a name="recommendations"></a>Javaslatok

A kiszolgáló nélküli **SQL Pool** -lekérdezések végrehajtásához a javasolt eszközök a [Azure Data Studio](get-started-azure-data-studio.md) és az Azure szinapszis Studio.

## <a name="next-steps"></a>Következő lépések
A Visual Studióval végzett csatlakozásról és lekérdezésről lásd: [Lekérdezés a Visual Studióval](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). A hitelesítési lehetőségekkel kapcsolatos további információkért lásd: [hitelesítés a SZINAPSZIS SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)-hez.