---
title: A Spark-összekötő használata Microsoft Azure SQL-sel és SQL Server
description: Megtudhatja, hogyan használhatja a Spark-összekötőt az Azure SQL Database, az Azure SQL felügyelt példányával és a SQL Serverával.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 09/02/2020
ms.openlocfilehash: 2e01e74f5086f7f1eb7e85661fbd35f452d8dae8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790202"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Valós idejű big data elemzések felgyorsítása a Spark-összekötő használatával
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> A Sep 2020-től kezdve ez az összekötő nem marad aktívan fenntartva. A [SQL Server és az Azure SQL Apache Spark-összekötője](/sql/connect/spark/connector) azonban mostantól elérhető, és támogatja a Python és az R-kötéseket, a könnyebben használható felületet a tömeges adatbeszúráshoz és számos más tökéletesítéshez. Nyomatékosan javasoljuk, hogy az új összekötő kiértékelését és használatát ne használja. A régi összekötő (ez az oldal) adatait csak archiválási célokra őrzi meg a rendszer.

A Spark-összekötő lehetővé teszi, hogy a Azure SQL Database, az Azure SQL felügyelt példányain lévő adatbázisok, és SQL Server a Spark-feladatokhoz bemeneti adatforrásként vagy kimeneti adatfogadóként működjenek. Lehetővé teszi a valós idejű tranzakciós adatmennyiségek kihasználása big data elemzésekben, és az ad hoc lekérdezések és jelentéskészítés eredményének fenntartása. A beépített JDBC-összekötőhöz képest ez az összekötő lehetővé teszi, hogy tömegesen helyezzen be adatait az adatbázisba. A soros és a soros beszúrást is lehetővé teszi, amely 10x-vel gyorsabb teljesítményt nyújt. A Spark-összekötő támogatja Azure Active Directory (Azure AD) hitelesítést a Azure SQL Database és az Azure SQL felügyelt példányához való csatlakozáshoz, így az adatbázist az Azure AD-fiókjával is csatlakoztathatja a Azure Databricks. Hasonló felületeket biztosít a beépített JDBC-összekötővel. A meglévő Spark-feladatok egyszerűen áttelepíthetők az új összekötő használatára.

## <a name="download-and-build-a-spark-connector"></a>Spark-összekötő letöltése és létrehozása

A régi összekötő GitHub-tárháza, amelyet korábban az oldalról csatoltak, nem marad aktívan fenntartva. Ehelyett határozottan javasoljuk az [új összekötő](https://github.com/microsoft/sql-spark-connector)kiértékelését és használatát.

### <a name="official-supported-versions"></a>Hivatalos támogatott verziók

| Összetevő                             | Verzió                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 vagy újabb           |
| Scala                                 | 2,10 vagy újabb            |
| Microsoft JDBC-illesztőprogram SQL Serverhez  | 6,2 vagy újabb             |
| Microsoft SQL Server                  | SQL Server 2008 vagy újabb |
| Azure SQL Database                    | Támogatott                |
| Felügyelt Azure SQL-példány            | Támogatott                |

A Spark-összekötő a Microsoft JDBC-illesztőt használja a SQL Serverhoz az adatáthelyezéshez a Spark Worker-csomópontok és adatbázisok között:

A adatfolyam a következő:

1. A Spark főcsomópontja SQL Database vagy SQL Server található adatbázisokhoz csatlakozik, és egy adott táblából vagy egy adott SQL-lekérdezés használatával tölti be az adatait.
2. A Spark-főkiszolgáló a feldolgozó csomópontok számára az átalakításhoz az adatok elosztását végzi.
3. A munkavégző csomópont olyan adatbázisokhoz csatlakozik, amelyekhez a rendszer csatlakozik SQL Databasehoz, és SQL Server és beírja az adatot az adatbázisba. A felhasználó dönthet úgy, hogy sor-sor beszúrást vagy tömeges beszúrást használ.

Az alábbi ábra az adatfolyamot ábrázolja.

   ![A diagram a leírt folyamatot mutatja be, egy főcsomóponttal, amely közvetlenül csatlakozik az adatbázishoz, és csatlakozik három feldolgozó csomóponthoz, amely csatlakozik az adatbázishoz.](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>A Spark-összekötő létrehozása

Az összekötő projekt jelenleg a mavent használja. Ha függőségek nélkül szeretné felépíteni az összekötőt, futtassa a következőt:

- MVN tiszta csomag
- Töltse le a JAR legújabb verzióit a kiadási mappából.
- A SQL Database Spark JAR belefoglalása

## <a name="connect-and-read-data-using-the-spark-connector"></a>Csatlakozás és adatolvasás a Spark-összekötő használatával

Kapcsolódhat SQL Database adatbázisokhoz, és a Spark-feladatokból SQL Serverheti az adatok olvasását és írását. Egy DML-vagy DDL-lekérdezést is futtathat SQL Database és SQL Server adatbázisaiban.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Adatok beolvasása az Azure SQL-ből és SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>Adatok beolvasása az Azure SQL-ből és SQL Server a megadott SQL-lekérdezéssel

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-and-sql-server"></a>Adatírás az Azure SQL-be és SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>DML-vagy DDL-lekérdezés futtatása az Azure SQL-ben és SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-from-spark-using-azure-ad-authentication"></a>Kapcsolat a Sparkból az Azure AD-hitelesítéssel

Az Azure AD-hitelesítés használatával kapcsolódhat Azure SQL Databasehoz és az SQL felügyelt példányához. Az Azure AD-hitelesítés használatával központilag kezelheti az adatbázis-felhasználók identitásait, és a SQL Server hitelesítés alternatívájaként is.

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Csatlakozás a ActiveDirectoryPassword hitelesítési móddal

#### <a name="setup-requirement"></a>Telepítési követelmény

Ha a ActiveDirectoryPassword hitelesítési módot használja, le kell töltenie az [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) -t és annak függőségeit, és fel kell vennie őket a Java Build elérési útjába.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-an-access-token"></a>Csatlakozás hozzáférési token használatával

#### <a name="setup-requirement"></a>Telepítési követelmény

Ha a hozzáférési jogkivonat-alapú hitelesítési módot használja, le kell töltenie az [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) -t és annak függőségeit, és fel kell vennie azokat a Java-Build elérési útjába.

Lásd: [Azure Active Directory hitelesítés használata hitelesítéshez](authentication-aad-overview.md) , amelyből megtudhatja, hogyan szerezhet be hozzáférési jogkivonatot az adatbázishoz Azure SQL Database vagy az Azure SQL felügyelt példányain.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-using-bulk-insert"></a>Adatírás tömeges beszúrással

A hagyományos JDBC összekötő adatbevitelt végez az adatbázisba sor-sor beszúrással. Használhatja a Spark-összekötőt az Azure SQL-ba való adatíráshoz és a SQL Server tömeges Beszúrás használatával. Nagy mértékben javítja az írási teljesítményt nagyméretű adathalmazok betöltésekor, vagy az adattöltést olyan táblákba, ahol az oszlopok tárolására szolgáló index van használatban.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/**
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, töltse le a Spark-összekötőt az [Azure-sqldb-Spark GitHub adattárból](https://github.com/Azure/azure-sqldb-spark) , és fedezze fel a további forrásokat a tárházban:

- [Minta Azure Databricks notebookok](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Mintául szolgáló parancsfájlok (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Érdemes áttekinteni a [Apache Spark SQL, a DataFrames és az adatkészletek útmutatóját](https://spark.apache.org/docs/latest/sql-programming-guide.html) , valamint a [Azure Databricks dokumentációját](/azure/azure-databricks/)is.