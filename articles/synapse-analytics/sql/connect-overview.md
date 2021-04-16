---
title: Csatlakozás Synapse SQL
description: Kapcsolódás a Synapse SQL.
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: f5682302ea0fa83c04a8560ba3f0f98ea075e072
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565542"
---
# <a name="connect-to-synapse-sql"></a>Csatlakozás Synapse SQL
Kapcsolódj a Synapse SQL képességhez a Azure Synapse Analytics.

## <a name="supported-tools-for-serverless-sql-pool"></a>A kiszolgáló nélküli SQL-készlet támogatott eszközei

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) az 1.18.0-s verziótól kezdve teljes mértékben támogatott. Az SSMS a 18.5-ös verziótól kezdve részben támogatott, így csak a csatlakozáshoz és a lekérdezéshez használható.

> [!NOTE]
> Ha egy AAD-bejelentkezés a lekérdezés végrehajtásakor több mint 1 órán keresztül van megnyitva, az AAD-re támaszkodó lekérdezések meghiúsulnak. Ebbe beletartozik a tároló lekérdezése az AAD átmenő szolgáltatásával és az AAD-val kommunikáló utasításokkal (például CREATE EXTERNAL PROVIDER). Ez minden olyan eszközre hatással van, amely nyitva tartja a kapcsolatokat, például az SSMS és az ADS lekérdezésszerkesztőjét. Azok az eszközök, amelyek új kapcsolatokat nyitnak meg egy lekérdezés végrehajtásához, például Synapse Studio, nem érintik.

> A probléma megoldásához újraindíthatja az SSMS-t, vagy csatlakozhat az ADS-hez, és leválaszthatja a kapcsolatot. 

## <a name="find-your-server-name"></a>A kiszolgálónév lekérdezése

A következő példában a dedikált SQL-készlet kiszolgálóneve: showdemoweu.sql.azuresynapse.net.
A kiszolgáló nélküli SQL-készlet kiszolgálóneve a következő példában: showdemoweu-ondemand.sql.azuresynapse.net.

A teljes kiszolgálónév lekérdezése:

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. A **Synapse-munkaterületeken válassza a lehetőséget.**
3. Válassza ki azt a munkaterületet, amelyhez csatlakozni szeretne.
4. Ugrás az áttekintésre.
5. Keresse meg a teljes kiszolgálónevet.

## <a name="sql-pool"></a>**SQL-készlet**

![Teljes kiszolgálónév](./media/connect-overview/server-connect-example.png)

## <a name="serverless-sql-pool"></a>**kiszolgáló nélküli SQL-készlet**

![Teljes kiszolgálónév kiszolgáló nélküli SQL-készlet](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Támogatott illesztők és kapcsolati sztringek
Synapse SQL a [következő ADO.NET:](/dotnet/framework/data/adonet/), [ODBC,](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows) [PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)és [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server). A legújabb verzió és dokumentáció megkereséhez válasszon egyet a fenti illesztőprogramok közül. Ha automatikusan létre kell hoznia a kapcsolati sztringet az  Ön által használt illesztő számára a Azure Portal válassza az előző példában látható Adatbázis-kapcsolati sztringek megjelenítése lehetőséget. A következő néhány példa bemutatja, hogy néz ki a kapcsolati sztring az egyes illesztők esetében.

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
Synapse SQL egyes beállításokat szabványosít a kapcsolat és az objektum létrehozása során. Ezek a beállítások nem bírálhatóak felül, és a következők lehetnek:

| Adatbázis-beállítások | Érték |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?view=azure-sqldw-latest&preserve-view=true) |hné |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?view=azure-sqldw-latest&preserve-view=true) |7 |

## <a name="recommendations"></a>Javaslatok

Kiszolgáló nélküli **SQL-készletlekérdezések** végrehoz ajánlott eszközöket Azure Data Studio [és](get-started-azure-data-studio.md) Azure Synapse Studióban.

## <a name="next-steps"></a>Következő lépések
A Visual Studióval végzett csatlakozásról és lekérdezésről lásd: [Lekérdezés a Visual Studióval](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). További információ a hitelesítési lehetőségekről: [Hitelesítés](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)Synapse SQL.