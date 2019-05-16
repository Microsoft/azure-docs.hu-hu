---
title: PHP használata Azure SQL-adatbázis lekérdezéséhez |} A Microsoft Docs
description: Hogyan használható a PHP egy olyan program létrehozásához, amely egy Azure SQL Database-adatbázishoz kapcsolódik, és T-SQL-utasítások használatával lekérdezni.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: c1ecd298afb7b4e955ec1633fc19162917ee8726
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65754577"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Gyors útmutató: PHP használata Azure SQL-adatbázis lekérdezéséhez

Ez a cikk bemutatja, hogyan használható [PHP](https://php.net/manual/en/intro-whatis.php) csatlakozni egy Azure SQL Database-adatbázishoz. Ezután használhatja a T-SQL-utasítások használatával adatokat lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

Ez a minta, győződjön meg arról, hogy rendelkezik a következő előfeltételek vonatkoznak:

- Azure SQL-adatbázis. Az alábbi rövid útmutatókban hozhat létre, és válassza az Azure SQL Database egy adatbázis is használja:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [kiszolgálószintű IP-tűzfalszabály](sql-database-server-level-firewall-rule.md)| [Kapcsolat egy virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Helyszíni kapcsolat](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|Az Adventure Works betöltött száma a rövid útmutató|[Állítsa vissza a Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Állítsa vissza vagy importálása az Adventure Works [BACPAC](sql-database-import.md) fájlt [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Ebben a cikkben a parancsfájlok az Adventure Works adatbázisa használatához készültek. Felügyelt példánnyal Ha az Adventure Works adatbázisa importálása-példány adatbázis, vagy módosítsa a parancsfájlokat ebben a cikkben a Wide World Importers-adatbázis használatára.

- PHP-kapcsolódó szoftverek az operációs rendszer telepítve:

  - **MacOS**, telepítse a PHP, az ODBC-illesztőprogramját, majd telepítse a PHP-illesztőprogram SQL Serverhez készült. Lásd: [1, 2 és 3. lépés](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, telepítse a PHP, az ODBC-illesztőprogramját, majd telepítse a PHP-illesztőprogram SQL Serverhez készült. Lásd: [1, 2 és 3. lépés](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, a PHP telepítése az IIS Express és a chocolatey-t, majd telepítse az ODBC-illesztőt és az Sqlcmd-t. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>Az SQL server-kapcsolati adatok lekéréséhez

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információkat kaphat. A következő eljárások szüksége a kiszolgáló teljes nevét vagy a gazdagépnév, az adatbázis neve és a bejelentkezési adatait.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Keresse meg a **SQL-adatbázisok** vagy **SQL felügyelt példányai** lapot.

3. A a **áttekintése** lapon, tekintse át a teljes kiszolgálónevet melletti **kiszolgálónév** egy önálló adatbázis vagy a kiszolgáló teljes neve melletti **gazdagép** számára egy felügyelt a példány. Másolja ki a kiszolgáló nevét vagy az állomásnevet, rámutatnak, és válassza a **másolási** ikonra.

## <a name="add-code-to-query-database"></a>Adja hozzá a kódot az adatbázis lekérdezése

1. Egy tetszőleges szövegszerkesztőben hozza létre a *sqltest.php* nevű új fájlt.  

1. Cserélje le annak tartalmát az alábbira. Majd adja hozzá a megfelelő értékeket a kiszolgáló, adatbázis, a felhasználó és a jelszavát.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa az alkalmazást.

   ```bash
   php sqltest.php
   ```

1. Ellenőrizze az első 20 sort adja vissza, és az alkalmazás ablak bezárásához.

## <a name="next-steps"></a>További lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)

- [SQL Serverre készült Microsoft PHP-illesztőprogramok](https://github.com/Microsoft/msphpsql/)

- [Problémák jelentése és kérdezés](https://github.com/Microsoft/msphpsql/issues)

- [Példa újrapróbálkozási: Az SQL-PHP logikára csatlakoztatása](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
