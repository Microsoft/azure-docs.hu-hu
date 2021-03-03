---
title: Az adatimportálás és-exportálás kiszolgáló nélküli Apache Spark készletek és SQL-készletek között
description: Ez a cikk azt ismerteti, hogyan használható az egyéni összekötő az adatok dedikált SQL-készletek és kiszolgáló nélküli Apache Spark készletek közötti áthelyezéséhez.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 66a804f645ee56cce62053a51e6aa4fc25b3c1a9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671321"
---
# <a name="introduction"></a>Bevezetés

Az Azure szinapszis Apache Spark a szinapszis SQL connectorhoz úgy lett kialakítva, hogy hatékonyan vigye át az adatátvitelt a kiszolgáló nélküli Apache Spark készletek és az Azure-beli dedikált SQL-készletek között. Az Azure szinapszis Apache Spark, hogy a szinapszis SQL Connector csak dedikált SQL-készleteken működik, nem működik a kiszolgáló nélküli SQL-készlettel.

> [!WARNING]
> A **sqlanalytics ()** függvény neve módosult a következőre: **synapsesql ()**. A sqlanalytics függvény továbbra is működni fog, de a rendszer elavulttá válik.  A jövőbeli fennakadások elkerülése érdekében módosítsa a **sqlanalytics ()** és a **synapsesql ()** függvényt.

## <a name="design"></a>Tervezés

A Spark-készletek és az SQL-készletek közötti adatátvitel a JDBC használatával végezhető el. Azonban a két elosztott rendszer, például a Spark és az SQL-készletek miatt a JDBC általában szűk keresztmetszetet jelent a soros adatátvitel során.

Az Azure szinapszis Apache Spark Pool to szinapszis SQL Connector a Apache Spark adatforrások implementációja. A dedikált SQL-készletekben lévő Azure Data Lake Storage Gen2t és a bázist használja a Spark-fürt és a szinapszis dedikált SQL-példánya közötti adatátvitelhez.

![Összekötő-architektúra](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Hitelesítés az Azure szinapszis Analyticsben

A rendszerek közötti hitelesítés zökkenőmentesen elérhető az Azure szinapszis Analytics szolgáltatásban. A jogkivonat-szolgáltatás a Azure Active Directory használatával csatlakozik a Storage-fiókhoz vagy az adatraktár-kiszolgálóhoz való hozzáféréshez szükséges biztonsági jogkivonatok beszerzéséhez.

Ezért nem kell hitelesítő adatokat létrehoznia, vagy megadnia azokat az összekötő API-ban, amíg az Azure AD-Auth konfigurálva van a Storage-fiókban és az adatraktár-kiszolgálón. Ha nem, akkor megadható az SQL-hitelesítés. További részleteket a [használati](#usage) szakaszban talál.

## <a name="constraints"></a>Korlátozások

- Ez az összekötő csak a Scalaben működik.
- A pySpark a [Python használata](#use-pyspark-with-the-connector) című szakaszban talál részleteket.
- Ez az összekötő nem támogatja az SQL-nézetek lekérdezését.

## <a name="prerequisites"></a>Előfeltételek

- **Db_exporter** szerepkör tagjának kell lennie abban az adatbázisban/SQL-készletben, amelybe be kívánja vinni az adatátvitelt.
- A Storage blob adatközreműködői szerepkör tagjának kell lennie az alapértelmezett Storage-fiókban.

Felhasználók létrehozásához kapcsolódjon az SQL-készlet adatbázisához, és kövesse az alábbi példákat:

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Szerepkör társítása:

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## <a name="usage"></a>Használat

Az importálási utasítások nem szükségesek, ezeket a rendszer előre importálja a jegyzetfüzet felhasználói felületén.

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-attached-within-the-workspace"></a>Adatok átvitele a munkaterületen belül csatolt, dedikált SQL-készletbe vagy onnan

> [!NOTE]
> **A notebook-élményben nem szükséges importálás**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API olvasása

```scala
val df = spark.read.synapsesql("<DBName>.<Schema>.<TableName>")
```

A fenti API a belső (felügyelt) és az SQL-készletben található külső táblák esetében is működik.

#### <a name="write-api"></a>API írása

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

Az írási API létrehozza a táblát a dedikált SQL-készletben, majd meghívja a albase-et az adatok betöltéséhez.  A tábla nem létezhet a dedikált SQL-készletben, vagy a rendszer hibaüzenetet küld, amely szerint a "már létezik objektum neve..."

TableType-értékek

- Állandók. belső felügyelt tábla dedikált SQL-készletben
- Konstansok. külső külső tábla dedikált SQL-készletben

SQL-készlet – felügyelt tábla

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

SQL Pool – külső tábla

Egy dedikált SQL-készlet külső táblájába való íráshoz külső adatforrásra és külső FÁJLFORMÁTUMra van szükség a dedikált SQL-készleten.  További információért olvassa el a [külső adatforrás](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) és a [külső fájlformátumok](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) létrehozása a dedikált SQL-készletben című témakört.  Az alábbi példák egy külső adatforrás és a külső fájlformátumok létrehozására szolgálnak a dedikált SQL-készletben.

```sql
--For an external table, you need to pre-create the data source and file format in dedicated SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

A külső HITELESÍTŐADAT-objektum nem szükséges, ha Azure Active Directory átmenő hitelesítést használ a Storage-fiókhoz.  Győződjön meg arról, hogy a Storage-fiókban a "Storage blob adatközreműködői" szerepkör tagja.

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    synapsesql("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-or-database-outside-the-workspace"></a>Adatok átvitele egy dedikált SQL-készletből vagy-adatbázisból a munkaterületen kívül

> [!NOTE]
> A notebook-élményben nem szükséges importálás

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API olvasása

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API írása

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-sql-auth-instead-of-azure-ad"></a>SQL-hitelesítés használata az Azure AD helyett

#### <a name="read-api"></a>API olvasása

Az összekötő jelenleg nem támogatja a jogkivonat-alapú hitelesítés használatát egy olyan dedikált SQL-készleten, amely a munkaterületen kívül esik. Az SQL-hitelesítést kell használnia.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API írása

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-pyspark-with-the-connector"></a>A PySpark használata az összekötővel

> [!NOTE]
> Ez a példa csak a jegyzetfüzettel kapcsolatos, szem előtt tartott felülettel van megadva.

Tegyük fel, hogy rendelkezik egy "pyspark_df" dataframe, amelyet szeretne írni a DW-be.

Hozzon létre egy ideiglenes táblát a PySpark dataframe használatával:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

A PySpark notebookon a Magics használatával futtasson egy Scala-cellát:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

scala_df.write.synapsesql("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Hasonlóképpen, az olvasási forgatókönyvben olvassa el az adataikat a Scala használatával, majd írja be egy ideiglenes táblába, és használja a Spark SQL-et a PySpark-ben, hogy lekérdezze a temp táblát egy dataframe.

## <a name="allow-other-users-to-use-the-azure-synapse-apache-spark-to-synapse-sql-connector-in-your-workspace"></a>Az Azure szinapszis Apache Spark használatának engedélyezése más felhasználók számára a munkaterületen található szinapszis SQL-összekötő használatához

A munkaterülethez csatlakoztatott ADLS Gen2 Storage-fiókban tárolnia kell a blob-adattulajdonost, hogy mások ne tudják módosítani a hiányzó engedélyeket. Ellenőrizze, hogy a felhasználó rendelkezik-e hozzáféréssel a munkaterülethez és a jegyzetfüzetek futtatásához szükséges engedélyekhez.

### <a name="option-1"></a>1\. lehetőség

- A felhasználó tárolási blob-adatközreműködőinek/tulajdonosának tétele

### <a name="option-2"></a>2\. lehetőség

- A következő ACL-eket kell megadnia a mappa struktúrájához:

| Mappa | / | szinapszis | munkaterületek  | \<workspacename> | sparkpools | \<sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Hozzáférési engedélyek | --X | --X | --X | --X | --X | --X | – WX |
| Alapértelmezett engedélyek | ---| ---| ---| ---| ---| ---| ---|

- Az összes mappát le kell tudnia adni a "szinapszis" mappából, és a Azure Portal lefelé haladva. A root "/" mappa ACL-jéhez kövesse az alábbi utasításokat.

- Csatlakozás a munkaterülethez csatlakoztatott Storage-fiókhoz Storage Explorer az Azure AD használatával
- Válassza ki a fiókját, és adja meg a munkaterület ADLS Gen2 URL-címét és alapértelmezett fájlrendszerét
- Ha megjelenik a felsorolt Storage-fiók, kattintson a jobb gombbal a Listázás munkaterületre, és válassza a "hozzáférés kezelése" lehetőséget.
- Adja hozzá a felhasználót a/mappához a "végrehajtás" hozzáférési engedéllyel. Válassza az OK lehetőséget

> [!IMPORTANT]
> Ügyeljen arra, hogy ne válassza az "alapértelmezett" lehetőséget, ha nem kívánja.


## <a name="next-steps"></a>Következő lépések

- [Dedikált SQL-készlet létrehozása a Azure Portal használatával](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Új Apache Spark-készlet létrehozása a Azure Portal használatával](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
