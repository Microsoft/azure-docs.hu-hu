---
title: A külső táblák definícióinak Apache Spark szinkronizálása a kiszolgáló nélküli SQL-készletben
description: A Spark-táblák lekérdezése kiszolgáló nélküli SQL-készlettel – áttekintés
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 057a69881b8b407e5d75fa3510ca1c3eb1830bc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96446495"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>Az Azure szinapszis külső táblázat-definícióinak Apache Spark szinkronizálása a kiszolgáló nélküli SQL-készletben

A kiszolgáló nélküli SQL-készlet képes automatikusan szinkronizálni a metaadatokat a Apache Sparkból. A kiszolgáló nélküli Apache Spark-készletekben található minden adatbázishoz létre fog hozni egy kiszolgáló nélküli SQL-készletet tartalmazó adatbázist. 

A Parquet és az Azure Storage-on alapuló Spark külső táblák esetében egy külső tábla jön létre egy kiszolgáló nélküli SQL Pool-adatbázisban. Így leállíthatja a Spark-készleteket, és továbbra is lekérdezheti a Spark külső táblákat a kiszolgáló nélküli SQL-készletből.

Ha egy tábla a Sparkban van particionálva, a tárolóban lévő fájlok mappák szerint vannak rendezve. A kiszolgáló nélküli SQL-készlet partíciós metaadatokat használ, és csak a lekérdezéshez kapcsolódó mappákat és fájlokat fogja használni.

A metaadatok szinkronizálását a rendszer automatikusan konfigurálja az Azure szinapszis munkaterületen üzembe helyezendő összes kiszolgáló nélküli Apache Spark-készlethez. Azonnal megkezdheti a Spark külső táblázatok lekérdezését.

Az Azure Storage-ban található összes Spark Parquet külső tábla egy olyan dbo-sémában szerepel, amely egy kiszolgáló nélküli SQL Pool-adatbázisnak felel meg. 

A Spark külső táblák lekérdezései esetében futtasson egy külső [spark_table] célt szolgáló lekérdezést. A következő példa futtatása előtt győződjön meg arról, hogy megfelelő [hozzáféréssel rendelkezik ahhoz a Storage-fiókhoz](develop-storage-files-storage-access-control.md) , amelyben a fájlok találhatók.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Az oszlopokhoz tartozó Spark külső táblás parancsok hozzáadása, eldobása vagy módosítása nem jelenik meg a kiszolgáló nélküli SQL-készlet külső táblájában.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Adattípusok Apache Spark SQL-adattípusok leképezéséhez

| Spark adattípus | SQL-adattípus               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| Rövid típus      | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | valós szám                        |
| DoubleType      | float                       |
| DecimalType     | tizedes tört                     |
| TimestampType   | datetime2                   |
| DateType        | dátum                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar (max) \* (JSON-ba)\** |
| MapType         | varchar (max) \* (JSON-ba)\** |
| StructType      | varchar (max) \* (JSON-ba)\** |

\* A használt rendezés Latin1_General_100_BIN2_UTF8.

\** A ArrayType, a MapType és a StructType JSON-ként jelennek meg.



## <a name="next-steps"></a>Következő lépések

Folytassa a [storage Access Control](develop-storage-files-storage-access-control.md) cikkével, amely további információt biztosít a Storage hozzáférés-vezérléséről.
