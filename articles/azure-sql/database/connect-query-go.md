---
title: Az Ugrás a lekérdezéshez használata
description: A Go használatával létrehozhat egy programot, amely egy adatbázishoz csatlakozik a Azure SQL Database vagy Azure SQL Managed Instance, és lekérdezéseket futtat.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 04/14/2021
ms.openlocfilehash: 1a479572ba8dbd68ccc072fce32446abcc9b873c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517780"
---
# <a name="quickstart-use-golang-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Rövid útmutató: Adatbázis lekérdezése a Golang használatával Azure SQL Database vagy Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ebben a rövid útmutatóban a [Golang](https://godoc.org/github.com/denisenkom/go-mssqldb) programozási nyelvvel fog csatlakozni egy adatbázishoz egy Azure SQL Database vagy Azure SQL Managed Instance. Ezután Transact-SQL-utasításokat fog futtatni az adatok lekérdezéséhez és módosításához. [A Golang](https://golang.org/) egy nyílt forráskódú programozási nyelv, amely megkönnyíti az egyszerű, megbízható és hatékony szoftverek felépítését.  

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Egy adatbázis a Azure SQL Database vagy Azure SQL Managed Instance. Az alábbi rövid útmutatók segítségével hozhat létre adatbázist:

  || SQL Database | Felügyelt SQL-példány | Azure virtuális gépen futó SQL Server |
  |:--- |:--- |:---|:---|
  | **Létrehozás**| [Portál](single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) | [Portál](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  | **Létrehozás** | [Parancssori felület](scripts/create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  | **Létrehozás** | [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | **Konfigurálás** | [Kiszolgálószintű IP-tűzfalszabály](firewall-create-server-level-portal-quickstart.md)| [Csatlakozás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)|
  | **Konfigurálás** ||[Helyszíni kapcsolatok](../managed-instance/point-to-site-p2s-configure.md) | [Csatlakozás egy SQL Server példányhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |**Adatok betöltése**|Az Adventure Works gyorsútmutatónként töltődik be|[Wide World Importers visszaállítása](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers visszaállítása](../managed-instance/restore-sample-database-quickstart.md) |
  | **Adatok betöltése** ||Adventure Works visszaállítása vagy importálása [BACPAC-fájlból](database-import.md) a [GitHubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works visszaállítása vagy importálása [BACPAC-fájlból](database-import.md) a [GitHubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|

  > [!IMPORTANT]
  > A cikkben leírt szkriptek az Adventure Works-adatbázis használatára vannak megírva. A SQL Managed Instance importálni kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a cikkben használt szkripteket a Wide World Importers-adatbázis használatára.

- A telepített operációs rendszerhez tartozó Golang és kapcsolódó szoftverek:

  - **macOS:** Telepítse a Homebrew-t és a Golangot. Lásd az [1.2. lépést](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
  - **Ubuntu:** Telepítse a Golangot. Lásd az [1.2. lépést](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
  - **Windows:** Telepítse a Golangot. Lásd az [1.2. lépést](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).

## <a name="get-server-connection-information"></a>Kiszolgáló kapcsolati információinak lekért adatai

Szerezze be az adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. A következő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy állomásnévre, adatbázisnévre és bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Lépjen az **SQL Database-adatbázisok vagy** felügyelt **SQL-példányok lapra.**

3. Az Áttekintés **lapon** tekintse át az adatbázis  kiszolgálóneve melletti teljes kiszolgálónevet az Azure SQL Database-ban, vagy a Gazdagép mellett található teljes kiszolgálónevet (vagy IP-címet) egy Azure-beli virtuális gépen Azure SQL Managed Instance vagy SQL Server mellett.  A kiszolgálónév vagy az állomásnév másoláshoz vigye fölé a kurzort, és válassza a **Másolás ikont.**

> [!NOTE]
> Az Azure-beli virtuális SQL Server kapcsolati információiért lásd: Csatlakozás SQL Server [példányhoz.](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-golang-project-and-dependencies"></a>Golang-projekt és függőségek létrehozása

1. A terminálból hozza létre az **SqlServerSample** nevű új projektmappát. 

   ```bash
   mkdir SqlServerSample
   ```

2. Lépjen az **SqlServerSample lapra,** és telepítse SQL Server Go-illesztőt.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Mintaadatok létrehozása

1. Egy szövegszerkesztőben hozzon létre egy **CreateTestData.sql** nevű fájlt az **SqlServerSample mappában.** A fájlba illessze be ezt a T-SQL-kódot, amely létrehoz egy sémát, egy táblát, és beszúr néhány sort.

   ```sql
   CREATE SCHEMA TestSchema;
   GO

   CREATE TABLE TestSchema.Employees (
     Id       INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
     Name     NVARCHAR(50),
     Location NVARCHAR(50)
   );
   GO

   INSERT INTO TestSchema.Employees (Name, Location) VALUES
     (N'Jared',  N'Australia'),
     (N'Nikita', N'India'),
     (N'Tom',    N'Germany');
   GO

   SELECT * FROM TestSchema.Employees;
   GO
   ```

2. A `sqlcmd` használatával csatlakozzon az adatbázishoz, és futtassa az újonnan létrehozott Azure SQL szkriptet. Helyettesítse be a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználónevet és a jelszót.

   ```bash
   sqlcmd -S <your_server>.database.windows.net -U <your_username> -P <your_password> -d <your_database> -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-the-database"></a>Kód beszúrása az adatbázis lekérdezéséhez

1. Hozzon létre egy **sample.go** nevű fájlt az **SqlServerSample** mappában.

2. Illessze be ezt a kódot a fájlba. Adja meg a kiszolgáló, az adatbázis, a felhasználónév és a jelszó értékeit. Ez a példa a Golang környezeti [metódusok segítségével](https://golang.org/pkg/context/) győződik meg arról, hogy van aktív kapcsolat.

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
       "errors"
   )

   var db *sql.DB

   var server = "<your_server.database.windows.net>"
   var port = 1433
   var user = "<your_username>"
   var password = "<your_password>"
   var database = "<your_database>"

   func main() {
       // Build connection string
       connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s;",
           server, user, password, port, database)

       var err error

       // Create connection pool
       db, err = sql.Open("sqlserver", connString)
       if err != nil {
           log.Fatal("Error creating connection pool: ", err.Error())
       }
       ctx := context.Background()
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal(err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createID, err := CreateEmployee("Jake", "United States")
       if err != nil {
           log.Fatal("Error creating Employee: ", err.Error())
       }
       fmt.Printf("Inserted ID: %d successfully.\n", createID)

       // Read employees
       count, err := ReadEmployees()
       if err != nil {
           log.Fatal("Error reading Employees: ", err.Error())
       }
       fmt.Printf("Read %d row(s) successfully.\n", count)

       // Update from database
       updatedRows, err := UpdateEmployee("Jake", "Poland")
       if err != nil {
           log.Fatal("Error updating Employee: ", err.Error())
       }
       fmt.Printf("Updated %d row(s) successfully.\n", updatedRows)

       // Delete from database
       deletedRows, err := DeleteEmployee("Jake")
       if err != nil {
           log.Fatal("Error deleting Employee: ", err.Error())
       }
       fmt.Printf("Deleted %d row(s) successfully.\n", deletedRows)
   }

   // CreateEmployee inserts an employee record
   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           err = errors.New("CreateEmployee: db is null")
           return -1, err
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := `
         INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name, @Location);
         select isNull(SCOPE_IDENTITY(), -1);
       `

       stmt, err := db.Prepare(tsql)
       if err != nil {
          return -1, err
       }
       defer stmt.Close()

       row := stmt.QueryRowContext(
           ctx,
           sql.Named("Name", name),
           sql.Named("Location", location))
       var newID int64
       err = row.Scan(&newID)
       if err != nil {
           return -1, err
       }

       return newID, nil
   }

   // ReadEmployees reads all employee records
   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           return -1, err
       }

       defer rows.Close()

       var count int

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // UpdateEmployee updates an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name = @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }

   // DeleteEmployee deletes an employee from the database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name = @Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>A kód futtatása

1. Futtassa a következő parancsot a parancssorban.

   ```bash
   go run sample.go
   ```

2. Ellenőrizze a kimenetet.

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 row(s) successfully.
   Updated 1 row(s) successfully.
   Deleted 1 row(s) successfully.
   ```

## <a name="next-steps"></a>Következő lépések

- [Az első adatbázis megtervezése a Azure SQL Database](design-first-database-tutorial.md)
- [Golang-illesztő SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Problémák jelentése és kérdezés](https://github.com/denisenkom/go-mssqldb/issues)

