---
title: 'SSMS: kapcsolat és adatlekérdezés'
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Megtudhatja, hogyan csatlakozhat Azure SQL Database vagy SQL felügyelt példányhoz SQL Server Management Studio (SSMS) használatával. Ezután futtasson Transact-SQL (T-SQL) utasításokat az adatok lekérdezéséhez és szerkesztéséhez.
keywords: csatlakozás sql database-hez,sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/28/2020
ms.openlocfilehash: 60977b9388af3a93d0ebbbc6aad50628b79e0e44
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91598269"
---
# <a name="quickstart-use-ssms-to-connect-to-and-query-azure-sql-database-or-azure-sql-managed-instance"></a>Gyors útmutató: Azure SQL Database vagy Azure SQL felügyelt példányhoz való kapcsolódás és lekérdezés a SSMS használatával
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan használható SQL Server Management Studio (SSMS) Azure SQL Database vagy Azure SQL felügyelt példányhoz való kapcsolódáshoz, valamint néhány lekérdezés futtatásához.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következő elemek szükségesek:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).

- Egy adatbázis a Azure SQL Databaseban. Az alábbi rövid útmutatók segítségével hozhat létre és konfigurálhat egy adatbázist Azure SQL Databaseban:

  | Művelet | SQL Database | Felügyelt SQL-példány | Azure virtuális gépen futó SQL Server |
  |:--- |:--- |:---|:---|
  | Létrehozás| [Portál](single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) | [Portál](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Parancssori felület](scripts/create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](firewall-create-server-level-portal-quickstart.md)| [Kapcsolódás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)|
  |||[Kapcsolódás a webhelyről](../managed-instance/point-to-site-p2s-configure.md) | [Csatlakozás az SQL Serverhez](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) | [Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works visszaállítása vagy importálása a [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works visszaállítása vagy importálása a [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben található parancsfájlok az Adventure Works-adatbázis használatára íródnak. Felügyelt példány esetén importálnia kell az Adventure Works-adatbázist egy példány-adatbázisba, vagy módosítania kell a jelen cikkben szereplő parancsfájlokat a Wide World Importálós adatbázis használatára.

Ha csak néhány alkalmi lekérdezést szeretne futtatni a SSMS telepítése nélkül, tekintse meg a rövid útmutató [: a Azure Portal lekérdezési szerkesztőjének használata Azure SQL Database-adatbázis lekérdezéséhez](connect-query-portal.md).

## <a name="get-server-connection-information"></a>Kiszolgáló-kapcsolatok adatainak beolvasása

Az adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A rövid útmutató elvégzéséhez szüksége lesz a teljes [kiszolgálónévre](logical-servers.md) vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon a lekérdezni kívánt **adatbázishoz** vagy **felügyelt példányhoz** .

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett, SQL Database vagy a **gazdagép** melletti teljes kiszolgálónevet (vagy IP-címet) a felügyelt példányhoz az SQL felügyelt példányában vagy a virtuális gépen lévő SQL Server-példányban. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> Az Azure-beli virtuális gépen SQL Server kapcsolati információit lásd: [kapcsolódás SQL Serverhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="connect-to-your-database"></a>Csatlakozás az adatbázishoz

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

A SSMS-ben kapcsolódjon a kiszolgálóhoz.

> [!IMPORTANT]
> A kiszolgáló a 1433-es portot figyeli. Ha vállalati tűzfal mögött szeretné csatlakozni a kiszolgálóhoz, a tűzfalnak nyitva kell lennie a porton.

1. Nyissa meg az SSMS-t.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanel jelenik meg. Adja meg a következő információkat:

   | Beállítás      | Ajánlott érték    | Leírás |
   | ------------ | ------------------ | ----------- |
   | **Server type (Kiszolgáló típusa)** | Adatbázismotor | Szükséges érték. |
   | **Kiszolgáló neve** | A teljes kiszolgálónév | Valami hasonló: **servername.database.Windows.net**. |
   | **Hitelesítés** | SQL Server-hitelesítés | Ez az oktatóanyag SQL-hitelesítést használ. |
   | **Bejelentkezés** | Kiszolgáló-rendszergazdai fiók felhasználói azonosítója | A kiszolgáló létrehozásához használt kiszolgálói rendszergazdai fiók felhasználói azonosítója. |
   | **Jelszó** | A kiszolgáló-rendszergazdai fiók jelszava | A kiszolgáló létrehozásához használt kiszolgáló-rendszergazdai fiók jelszava. |
   ||||

   ![kapcsolódás a kiszolgálóhoz](./media/connect-query-ssms/connect.png)  

> [!NOTE]
> Ez az oktatóanyag SQL Server hitelesítést használ.

3. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen válassza a **Beállítások lehetőséget** . A **Kapcsolódás az adatbázishoz** legördülő menüben válassza a **mySampleDatabase** lehetőséget. Az [Előfeltételek szakaszban](#prerequisites) a rövid útmutató elvégzésével létrehoz egy MySampleDatabase nevű AdventureWorksLT-adatbázist. Ha a AdventureWorks-adatbázis munkapéldánya eltérő névvel rendelkezik, mint a mySampleDatabase, válassza ki helyette.

   ![csatlakozás kiszolgálón található adatbázishoz](./media/connect-query-ssms/options-connect-to-db.png)  

4. Válassza a **Kapcsolódás** lehetőséget. Megnyílik az Object Explorer ablak.

5. Az adatbázis objektumainak megtekintéséhez bontsa ki a **Databases** (Adatbázisok) lehetőséget, majd az adatbázis-csomópontot.

   ![mySampleDatabase objektumok](./media/connect-query-ssms/connected.png)  

## <a name="query-data"></a>Adatok lekérdezése

Futtassa ezt a [Select](/sql/t-sql/queries/select-transact-sql/) Transact-SQL-kódot az első 20 termék kategóriánkénti lekérdezéséhez.

1. A Object Explorer kattintson a jobb gombbal a **mySampleDatabase** elemre, és válassza az **Új lekérdezés** elemet. Megnyílik az adatbázishoz csatlakoztatott új lekérdezési ablak.

2. A lekérdezési ablakban illessze be a következő SQL-lekérdezést:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Az eszköztáron kattintson az **Execute (végrehajtás** ) elemre a lekérdezés futtatásához és az adatok lekéréséhez a `Product` és a `ProductCategory` táblákból.

    ![lekérdezés a tábla termék-és ProductCategory adatainak lekéréséhez](./media/connect-query-ssms/query2.png)

### <a name="insert-data"></a>Adat beszúrása

Futtassa ezt a [Insert](/sql/t-sql/statements/insert-transact-sql/) Transact-SQL-kódot egy új termék létrehozásához a `SalesLT.Product` táblában.

1. Cserélje le az előző lekérdezést ezzel az eggyel.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Válassza a **végrehajtás**  elemet egy új sor táblázatba való beszúrásához `Product` . Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)**.

#### <a name="view-the-result"></a>Az eredmény megtekintése

1. Cserélje le az előző lekérdezést ezzel az eggyel.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Válassza a **Végrehajtás** lehetőséget. A következő eredmény jelenik meg.

   ![a Product Table lekérdezés eredménye](./media/connect-query-ssms/result.png)

### <a name="update-data"></a>Adatok frissítése

Futtassa ezt a [frissítési](/sql/t-sql/queries/update-transact-sql) Transact-SQL-kódot az új termék módosításához.

1. Cserélje le az előző lekérdezést, amely a korábban létrehozott új rekordot adja vissza:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **végrehajtás** lehetőséget a tábla megadott sorának frissítéséhez `Product` . Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)**.

### <a name="delete-data"></a>Adat törlése

Futtassa ezt a [delete](/sql/t-sql/statements/delete-transact-sql/) Transact-SQL-kódot az új termék eltávolításához.

1. Cserélje le az előző lekérdezést ezzel az eggyel.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Válassza a **végrehajtás** lehetőséget a tábla megadott sorának törléséhez `Product` . Megjelenik az **üzenetek** ablaktábla **(1 sor érintett)**.

## <a name="next-steps"></a>Következő lépések

- További információ a SSMS: [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms/).
- A Azure Portal használatával történő kapcsolódáshoz és lekérdezéshez lásd: [Kapcsolódás és lekérdezés a Azure Portal SQL-lekérdezés szerkesztőjével](connect-query-portal.md).
- A Visual Studio Code használatával történő csatlakozásról és lekérdezésről lásd a [Visual Studio Code használatával végzett csatlakozásról és lekérdezésről](connect-query-vscode.md) szóló témakört.
- A .NET használatával történő csatlakozásról és lekérdezésről lásd a [.NET használatával végzett csatlakozásról és lekérdezésről](connect-query-dotnet-visual-studio.md) szóló témakört.
- A PHP-vel történő csatlakozásról és lekérdezésről lásd a [PHP-vel végzett csatlakozásról és lekérdezésről](connect-query-php.md) szóló témakört.
- A Node.js használatával történő csatlakozásról és lekérdezésről lásd a [Node.js használatával végzett csatlakozásról és lekérdezésről](connect-query-nodejs.md) szóló témakört.
- A Java használatával történő csatlakozásról és lekérdezésről lásd a [Java használatával végzett csatlakozásról és lekérdezésről](connect-query-java.md) szóló témakört.
- A Python használatával történő csatlakozásról és lekérdezésről lásd a [Python használatával végzett csatlakozásról és lekérdezésről](connect-query-python.md) szóló témakört.
- A Ruby használatával történő csatlakozásról és lekérdezésről lásd a [Ruby használatával végzett csatlakozásról és lekérdezésről](connect-query-ruby.md) szóló témakört.
