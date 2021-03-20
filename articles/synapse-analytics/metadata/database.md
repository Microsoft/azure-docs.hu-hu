---
title: Megosztott adatbázis
description: Az Azure szinapszis Analytics olyan megosztott metaadat-modellt biztosít, amelyben egy adatbázis kiszolgáló nélküli Apache Spark készletben való létrehozása lehetővé teszi a kiszolgáló nélküli SQL-készletből és az SQL Pool-motorokból való elérhetővé tételét.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 93602e522338166abac98c3e4a198e1aff392d21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934968"
---
# <a name="azure-synapse-analytics-shared-database"></a>Az Azure szinapszis Analytics megosztott adatbázisa

Az Azure szinapszis Analytics lehetővé teszi, hogy a különböző számítási munkaterület-hajtóművek megosszák az adatbázisokat és a táblákat. Jelenleg a Apache Spark-készleteken létrehozott adatbázisok és parketta-táblázatok automatikusan meg lesznek osztva a kiszolgáló nélküli SQL-készlet motorral.

A Spark-feladatokkal létrehozott adatbázisok a munkaterület összes jelenlegi és jövőbeli Spark-készletével azonos névvel lesznek láthatóak, beleértve a kiszolgáló nélküli SQL-készlet motorját. Nem adhat hozzá egyéni objektumokat (külső táblákat, nézeteket, eljárásokat) közvetlenül ebben a replikált adatbázisban a kiszolgáló nélküli SQL-készlettel.

A meghívott Spark alapértelmezett adatbázis `default` a kiszolgáló nélküli SQL-készlet kontextusában is látható lesz a nevű adatbázisként `default` .

Mivel az adatbázisok aszinkron módon lesznek szinkronizálva a kiszolgáló nélküli SQL-készletbe, a rendszer késést okoz, amíg meg nem jelenik.

## <a name="manage-a-spark-created-database"></a>Spark által létrehozott adatbázis kezelése

A Spark segítségével kezelheti a Spark által létrehozott adatbázisokat. Például törölheti azt egy Spark-készlet feladatokon keresztül, és létrehozhat táblákat a Sparkból.

Ha kiszolgáló nélküli SQL-készlettel hoz létre objektumokat egy Spark által létrehozott adatbázisban, vagy megpróbálja eldobni az adatbázist, a művelet sikeres lesz. Az eredeti Spark-adatbázis azonban nem módosul.

## <a name="how-name-conflicts-are-handled"></a>A névütközés kezelésének módja

Ha a Spark-adatbázis neve ütközik egy meglévő kiszolgáló nélküli SQL Pool-adatbázis nevével, az utótagot a rendszer a kiszolgáló nélküli SQL-készletben adja hozzá a Spark-adatbázishoz. A kiszolgáló nélküli SQL-készlet utótagja `_<workspace name>-ondemand-DefaultSparkConnector` .

Ha például egy nevű Spark `mydb` -adatbázis jön létre az Azure szinapszis munkaterületen, `myws` és egy kiszolgáló nélküli SQL Pool-adatbázis már létezik, akkor a kiszolgáló nélküli SQL-készletben található Spark-adatbázist a név használatával kell hivatkozni `mydb_myws-ondemand-DefaultSparkConnector` .

> [!CAUTION]
> Vigyázat: ezt a viselkedést nem kell függőséget kihasználnia.

## <a name="security-model"></a>Biztonsági modell

A Spark-adatbázisok és-táblák, valamint az SQL-motorban szinkronizált ábrázolások az alapul szolgáló tárolási szinten lesznek biztosítva.

Az adatbázist létrehozó rendszerbiztonsági tag ennek az adatbázisnak a tulajdonosa, és minden jogosultsággal rendelkezik az adatbázishoz és annak objektumaihoz.

Ahhoz, hogy egy rendszerbiztonsági tag, például egy felhasználó vagy egy biztonsági csoport hozzáférhessen egy adatbázishoz, adja meg a megfelelő POSIX-mappát és-engedélyeket a címtárban található alapul szolgáló mappákhoz és fájlokhoz `warehouse` . 

Ahhoz például, hogy egy rendszerbiztonsági tag beolvasson egy táblát egy adatbázisban, a címtár adatbázis mappájától kezdődő összes mappának rendelkeznie kell az `warehouse` `X` `R` adott rendszerbiztonsági tag számára, és hozzá kell rendelnie az engedélyeket. Emellett a fájloknak (például a tábla alapjául szolgáló adatfájloknak) `R` engedélyre van szükségük. 

Ha egy rendszerbiztonsági tag lehetővé teszi objektumok létrehozását vagy objektumok eldobását egy adatbázisban, további `W` engedélyekre van szükség a mappában található mappákban és fájlokban `warehouse` .

## <a name="examples"></a>Példák

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Spark-adatbázis létrehozása és kapcsolódás kiszolgáló nélküli SQL-készlettel

Először hozzon létre egy nevű új Spark `mytestdb` -adatbázist egy olyan Spark-fürt használatával, amelyet már létrehozott a munkaterületen. Ezt például a Spark C# jegyzetfüzet és a következő .NET for Spark-utasítás használatával érheti el:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Rövid késleltetés után megtekintheti az adatbázist a kiszolgáló nélküli SQL-készletből. Futtassa például a következő utasítást a kiszolgáló nélküli SQL-készletből.

```sql
SELECT * FROM sys.databases;
```

Ellenőrizze, hogy `mytestdb` szerepel-e az eredmények között.

## <a name="next-steps"></a>Következő lépések

- [További információ az Azure szinapszis Analytics megosztott metaadatairól](overview.md)
- [További információ az Azure szinapszis Analytics megosztott metaadatait tartalmazó tábláiról](table.md)
