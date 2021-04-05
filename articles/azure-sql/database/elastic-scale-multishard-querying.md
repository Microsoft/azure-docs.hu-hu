---
title: Szilánkokra bontott adatbázisok lekérdezése
description: Lekérdezéseket futtathat a rugalmas adatbázis-ügyféloldali kódtár használatával a szegmensek között.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5a0dd12efb9d94bda264b3bd04b05cdc3df917e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786632"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Többrétegű lekérdezés rugalmas adatbázis-eszközök használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="overview"></a>Áttekintés

A [Elastic Database eszközökkel](elastic-scale-introduction.md)létrehozhat szilánkokra osztott adatbázis-megoldásokat. A **több szegmenses lekérdezés** olyan feladatokhoz használható, mint például az adatgyűjtés/jelentéskészítés, amelyek több szegmensre kiterjedő lekérdezés futtatását igénylik. (Ezzel ellentétben az [adatoktól függő útválasztással](elastic-scale-data-dependent-routing.md), amely az összes munkát egyetlen szegmensen hajtja végre.)

1. Szerezzen be egy **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) vagy **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) a **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), a **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) vagy a **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) metódus használatával. Lásd: [ShardMapManager létrehozása](elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) és [RangeShardMap vagy ListShardMap beszerzése](elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Hozzon létre egy **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)) objektumot.
3. Hozzon létre egy **MultiShardStatement vagy MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Állítsa a **CommandText tulajdonságot** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) egy T-SQL-parancsra.
5. Futtassa a parancsot a **ExecuteQueryAsync vagy a ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) metódus meghívásával.
6. Tekintse meg az eredményeket a **MultiShardResultSet vagy a MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) osztály használatával.

## <a name="example"></a>Példa

A következő kód a több szegmenses lekérdezés használatát mutatja be a *myShardMap* nevű adott **ShardMap** használatával.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

A fő különbség a több szegmens közötti kapcsolatok kialakítása. Ha a **SqlConnection** egy különálló adatbázison működik, a **MultiShardConnection** a szegmensek **_ _gyűjteményét veszi fel_*bemenetként. Feltöltheti a szegmensek gyűjteményét egy szegmenses térképről. Ezt követően a lekérdezés a szegmensek gyűjteményén fut az _* Union összes** szemantikai használatával, egyetlen átfogó eredmény összeállításához. Opcionálisan annak a szegmensnek a neve, amelyből a sor származik, hozzáadható a kimenethez a parancs **ExecutionOptions** tulajdonságának használatával.

Jegyezze fel a **myShardMap. GetShards ()** hívását. Ez a metódus lekéri az összes szegmenst a szegmenses térképről, és egyszerű módszert kínál a lekérdezések futtatására az összes releváns adatbázisban. Egy több szegmensből álló lekérdezéshez tartozó szegmensek gyűjteménye tovább finomítható úgy, hogy a **myShardMap. GetShards ()** hívása által visszaadott gyűjteményen keresztül LINQ-lekérdezést hajt végre. A részleges eredményekkel kapcsolatos szabályzattal együtt a több szegmensből álló lekérdezésekben a jelenlegi képesség úgy lett kialakítva, hogy a TEN akár több száz szegmens között is működjön.

A többrétegű lekérdezések korlátozása jelenleg a lekérdezett szegmensek és shardletek érvényesítésének hiánya. Míg az adat-függő útválasztás ellenőrzi, hogy egy adott szegmens a lekérdezés időpontjában a szegmenses Térkép részét képezi-e, a többrétegű lekérdezések nem hajtják végre ezt az ellenőrzést. Ez olyan többszegmensű lekérdezések futtatását eredményezheti, amelyeket a rendszer eltávolított az adatbázisokból.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Több szegmensből álló lekérdezések és felosztási egyesítési műveletek

A többrétegű lekérdezések nem ellenőrzik, hogy a lekérdezett adatbázisban lévő shardletek részt vesznek-e a folyamatban lévő felosztott egyesítési műveletekben. (Lásd: [skálázás a Elastic Database felosztási-egyesítési eszköz használatával](elastic-scale-overview-split-and-merge.md).) Ez inkonzisztenciát eredményezhet, ha ugyanazon shardletbe sorai több adatbázishoz is megjelennek ugyanabban a több szegmenses lekérdezésben. Vegye figyelembe ezeket a korlátozásokat, és vegye figyelembe, hogy a több szegmensre kiterjedő lekérdezések végrehajtása során a folyamatban lévő felosztási műveletek és a szegmensek közötti változások is módosulnak.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]