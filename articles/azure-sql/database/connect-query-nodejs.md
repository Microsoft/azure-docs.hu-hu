---
title: Adatbázis lekérdezése Node.js használatával
description: Hogyan használható a Node.js egy olyan program létrehozásához, amely Azure SQL Database vagy Azure SQL felügyelt példányban található adatbázishoz kapcsolódik, és T-SQL-utasítások használatával kérdezi le.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2, devx-track-js
ms.openlocfilehash: 44530577972839aacb803d1722fa97716088fa0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91325436"
---
# <a name="quickstart-use-nodejs-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Rövid útmutató: adatbázis lekérdezése Node.js használatával Azure SQL Database vagy az Azure SQL felügyelt példányain
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ebben a rövid útmutatóban a Node.js segítségével csatlakozhat egy adatbázishoz, és lekérdezheti azokat.

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  | Művelet | SQL Database | Felügyelt SQL-példány | Azure virtuális gépen futó SQL Server |
  |:--- |:--- |:---|:---|
  | Létrehozás| [Portál](single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) | [Portál](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Parancssori felület](scripts/create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](firewall-create-server-level-portal-quickstart.md)| [Kapcsolódás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)|
  |||[Helyszíni kapcsolat](../managed-instance/point-to-site-p2s-configure.md) | [Kapcsolódás SQL Server-példányhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) | [Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works visszaállítása vagy importálása [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works visszaállítása vagy importálása [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||


- [Node.js](https://nodejs.org)kapcsolódó szoftverek

  # <a name="macos"></a>[macOS](#tab/macos)

  Telepítse a Homebrew-t és a Node.js-t, majd telepítse az ODBC-illesztőt és a SQLCMD-t a **1,2** -es és **1,3** Node.js-as lépések használatával a [MacOS rendszeren SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Telepítse Node.js, majd telepítse az ODBC-illesztőt és az SQLCMD Node.js-t a **1,2** -es és a **1,3** -es lépések használatával az [Ubuntu-SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Telepítse a csokoládét és a Node.js, majd telepítse az ODBC-illesztőt és a SQLCMD-t az **1,2** -es és a **1,3** -Node.js es lépések használatával a [Windows SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> A cikkben található parancsfájlok az **Adventure Works** -adatbázis használatára íródnak.

> [!NOTE]
> Dönthet úgy is, hogy egy Azure SQL felügyelt példányt használ.
>
> A létrehozásához és konfigurálásához használja a [Azure Portal](../managed-instance/instance-create-quickstart.md), a [PowerShellt](../managed-instance/scripts/create-configure-managed-instance-powershell.md)vagy a [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)-t, majd állítsa be a helyszíni vagy [a](../managed-instance/point-to-site-p2s-configure.md) [virtuális gép](../managed-instance/connect-vm-instance-configure.md) kapcsolatát.
>
> Az betöltéssel kapcsolatban lásd: [visszaállítás a BACPAC](database-import.md) az [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -fájllal, vagy lásd: [a Wide World](../managed-instance/restore-sample-database-quickstart.md)importing-adatbázis visszaállítása.

## <a name="get-server-connection-information"></a>Kiszolgáló-kapcsolatok adatainak beolvasása

A Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok lekérése. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Nyissa meg az **SQL-adatbázisok**  vagy az SQL- **felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett Azure SQL Database vagy a **gazdagép** melletti teljes kiszolgálónevet (vagy IP-címet) egy Azure SQL felügyelt példányhoz, vagy SQL Server az Azure virtuális gépen. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> SQL Server Azure-beli virtuális gépen való kapcsolódásával kapcsolatos információkért lásd: [kapcsolódás SQL Serverhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-the-project"></a>A projekt létrehozása

Nyisson meg egy parancssort, és hozzon létre egy *sqltest* nevű mappát. Nyissa meg a létrehozott mappát, és futtassa a következő parancsot:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-the-database"></a>Kód hozzáadása az adatbázis lekérdezéséhez

1. A kedvenc szövegszerkesztőben hozzon létre egy új fájlt, *sqltest.js*.

1. Cserélje le a tartalmát a következő kódra. Ezután adja hozzá a kiszolgáló, az adatbázis, a felhasználó és a jelszó megfelelő értékeit.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });

      connection.execSql(request);
    }
    ```

> [!NOTE]
> A példa a **AdventureWorksLT** minta adatbázisát használja Azure SQL Databaseban.

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa a programot.

    ```bash
    node sqltest.js
    ```

1. Ellenőrizze, hogy az első 20 sor vissza lett-e jelenítve, és az alkalmazás ablakának lezárása.

## <a name="next-steps"></a>Következő lépések

- [Microsoft Node.js illesztőprogram az SQL Serverhez](/sql/connect/node-js/node-js-driver-for-sql-server)

- Kapcsolat és lekérdezés Windows/Linux/macOS rendszeren [.net Core](connect-query-dotnet-core.md), [Visual Studio Code](connect-query-vscode.md)vagy [SSMS](connect-query-ssms.md) (csak Windows rendszeren)

- [A .NET Core használatának első lépései Windows/Linux/macOS rendszeren a parancssor használatával](/dotnet/core/tutorials/using-with-xplat-cli)

- Az első adatbázis megtervezése Azure SQL Database [.net](design-first-database-csharp-tutorial.md) vagy [SSMS](design-first-database-tutorial.md) használatával
