---
title: Kapcsolati sztringek a Synapse SQL
description: Kapcsolati sztringek a Synapse SQL
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: fb68a15bb64640fd8aa18d2ee01fd4a03d809125
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565270"
---
# <a name="connection-strings-for-synapse-sql"></a>Kapcsolati sztringek a Synapse SQL

Számos különböző alkalmazás-protokollal Synapse SQL, például a [ADO.NET,](/dotnet/framework/data/adonet/)az [ODBC,](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows) [a PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)és a [JDBC protokollal.](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) Az alábbiakban néhány példát talál az egyes protokollok kapcsolati sztringire. 

A kapcsolati sztringet a Azure Portal is felépítheti.  Ha a kapcsolati sztringet a Azure Portal, lépjen az adatbázis panelre, és az *Alapvető* szolgáltatások alatt válassza az Adatbázis-kapcsolati sztringek *megjelenítése lehetőséget.*

## <a name="sample-adonet-connection-string"></a>Példa ADO.NET kapcsolati sztringre

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Minta ODBC kapcsolati sztring

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>PHP-kapcsolati sztringminta

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>JDBC kapcsolati sztringminta

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Fontolja meg a kapcsolat időtúllépésének 300 másodpercre való beállítását, hogy a kapcsolat rövid ideig ne érhető el.

## <a name="recommendations"></a>Javaslatok

Kiszolgáló nélküli **SQL-készletlekérdezések** végrehoz ajánlott eszközöket Azure Data Studio és [Azure Synapse](get-started-azure-data-studio.md) Studióban.

## <a name="next-steps"></a>Következő lépések

Az elemzések lekérdezésének első Visual Studio és más alkalmazásokkal: [Lekérdezés Visual Studio.](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)