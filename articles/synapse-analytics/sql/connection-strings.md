---
title: A szinapszis SQL-hez kapcsolódó kapcsolatok karakterláncai (előzetes verzió)
description: A szinapszis SQL-hez kapcsolódó kapcsolatok karakterláncai (előzetes verzió)
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 42a1110b089d5edf9793bf2bb2c699a717a4b1a4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004969"
---
# <a name="connection-strings-for-synapse-sql-preview"></a>A szinapszis SQL-hez kapcsolódó kapcsolatok karakterláncai (előzetes verzió)

A szinapszis SQL (előzetes verzió) szolgáltatáshoz több különböző alkalmazás-protokoll, például a, a [ADO.net](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), az [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), a [php](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)és a [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx)is csatlakozhat. Az alábbiakban néhány példát láthat a kapcsolatok sztringre az egyes protokollokhoz. 

A Azure Portal használatával is létrehozhatja a kapcsolódási karakterláncot.  Ha a kapcsolati karakterláncot a Azure Portal használatával szeretné felépíteni, navigáljon az adatbázis panelre az *alapvető* erőforrások területen kattintson az *adatbázis-kapcsolati karakterláncok megjelenítése*elemre.

## <a name="sample-adonet-connection-string"></a>Minta ADO.NET-kapcsolatok karakterlánca

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Minta ODBC-kapcsolatok karakterlánca

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Minta PHP-beli kapcsolatok karakterlánca

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Példa JDBC-kapcsolatok karakterláncára

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Ügyeljen arra, hogy a kapcsolat időtúllépése 300 másodpercre legyen beállítva, hogy a kapcsolat ne maradjon le rövid ideig.

## <a name="recommendations"></a>Javaslatok

Az **igény szerinti SQL-** lekérdezések végrehajtásához ajánlott eszközök a [Azure Data Studio](get-started-azure-data-studio.md) és az Azure szinapszis Studio.

## <a name="next-steps"></a>Következő lépések

Az elemzés a Visual Studióval és más alkalmazásokkal való lekérdezésének megkezdéséhez lásd: [lekérdezés a Visual Studióval](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
