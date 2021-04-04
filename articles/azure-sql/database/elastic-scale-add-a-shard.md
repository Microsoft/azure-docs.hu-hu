---
title: Szegmens hozzáadása rugalmas adatbázis-eszközök használatával
description: Rugalmas méretezési API-k használata új szegmensek egy szegmens készletbe való felvételéhez.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: efab0234d428a8283845946289cdd1e8a17ded26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792055"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Szegmens hozzáadása Elastic Database eszközök használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Szegmens hozzáadása új tartományhoz vagy kulcshoz

Az alkalmazásoknak gyakran új szegmenseket kell felvenniük ahhoz, hogy kezelni tudják az új kulcsokból vagy a kulcsok tartományokból várt adatok kezelését. Előfordulhat például, hogy egy, a bérlői azonosító által áthelyezett alkalmazásnak új szegmenst kell kiépítenie, vagy a havonta felhasznált adatokat egy új, minden új hónap kezdete előtt létre kell hozni.

Ha a kulcsok új tartománya már nem része egy meglévő leképezésnek, egyszerűen hozzáadhatja az új szegmenst, és hozzárendelheti az új kulcsot vagy tartományt a szegmenshez.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Példa: szegmens és a hozzá tartozó tartomány hozzáadása egy meglévő felosztási térképhez

Ez a példa a TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.net](/previous-versions/azure/dn823929(v=azure.100))) használja a CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), a CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) metódusokat), és létrehozza a ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) osztály egy példányát. Az alábbi példában egy **sample_shard_2** nevű adatbázis és a benne található összes szükséges séma objektum a (z) [300, 400) tartományhoz lett létrehozva.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

A .NET verzióban a PowerShellt is használhatja Alternatív megoldásként az új szegmenses Térkép-kezelő létrehozásához. [Itt](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)egy példa érhető el.

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Szegmens hozzáadása egy meglévő tartomány üres részéhez

Bizonyos esetekben előfordulhat, hogy már hozzárendelt egy tartományt egy szegmenshez, és részben kitöltötte az adathalmazt, de mostantól egy másik szegmensre irányítja. Például, ha a napi tartományba tartozik, és már 50 nap van hozzárendelve egy szegmenshez, de 24. napon belül, a jövőbeli adatnak egy másik szegmensbe kell esnie. A rugalmas adatbázis [felosztása és egyesítése eszköz](elastic-scale-overview-split-and-merge.md) elvégezheti ezt a műveletet, ha azonban nincs szükség adatáthelyezésre (például a napok tartományára vonatkozó adatok [25, 50), azaz a 25. nap, a 50 kizárólagos, de még nem létezik), ezt teljes egészében elvégezheti a szegmenses Térkép felügyeleti API-k közvetlen használatával.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Példa: tartomány felosztása és az üres rész kiosztása egy újonnan hozzáadott szegmenshez

A rendszer létrehozta a "sample_shard_2" nevű adatbázist és a benne található összes szükséges sémakezelő objektumot.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Fontos**: csak akkor használja ezt a technikát, ha biztos abban, hogy a frissített leképezés tartománya üres.  Az előző metódusok nem ellenőrzik az áthelyezett tartomány adatait, ezért a legjobb megoldás a kód ellenőrzésének belefoglalása.  Ha sorok találhatók az áthelyezett tartományban, a tényleges adateloszlás nem felel meg a frissített szegmenses térképnek. A művelet elvégzéséhez használja a [Split-Merge eszközt](elastic-scale-overview-split-and-merge.md) a következő esetekben.  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]