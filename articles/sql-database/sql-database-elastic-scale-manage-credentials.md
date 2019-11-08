---
title: Hitelesítő adatok kezelése a rugalmas adatbázis ügyféloldali könyvtárában
description: A hitelesítő adatok megfelelő szintjének beállítása, a rendszergazda csak olvasható, rugalmas adatbázis-alkalmazások esetén
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91689a32a128584aade8081905e3d1aa3ecb0a97
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823581"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Az Elastic Database ügyféloldali kódtár eléréséhez használt hitelesítő adatok

Az [Elastic Database ügyféloldali függvénytár](sql-database-elastic-database-client-library.md) három különböző típusú hitelesítő adatot használ a szegmenses [Térkép kezelőjének](sql-database-elastic-scale-shard-map-management.md)eléréséhez. A szükségestől függően használja a hitelesítő adatokat a lehető legalacsonyabb szintű hozzáférési jogosultsággal.

* **Felügyeleti hitelesítő adatok**: a szegmenses Térkép kezelőjének létrehozásához vagy módosításához. (Lásd a [szószedetet](sql-database-elastic-scale-glossary.md).)
* **Hozzáférés a hitelesítő adatokhoz**: egy meglévő, a szegmensekkel kapcsolatos információk beszerzéséhez.
* **Kapcsolati hitelesítő adatok**: a szegmensekhez való kapcsolódáshoz.

Lásd még: [adatbázisok és bejelentkezések kezelése Azure SQL Databaseban](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>A felügyeleti hitelesítő adatok ismertetése

A felügyeleti hitelesítő adatok használatával **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) objektumot hozhat létre a szegmens térképeket kezelő alkalmazásokhoz. (Lásd például: szegmens [hozzáadása Elastic Database eszközökkel](sql-database-elastic-scale-add-a-shard.md) és [Adatfüggő útválasztással](sql-database-elastic-scale-data-dependent-routing.md)). A rugalmasan méretezhető ügyféloldali kódtár felhasználója létrehozza az SQL-felhasználókat és az SQL-bejelentkezéseket, és gondoskodik arról, hogy mindegyik megkapja az olvasási/írási engedélyeket a globális szegmenses Térkép-adatbázison és az összes szegmens adatbázison is. A rendszer ezeket a hitelesítő adatokat használja a globális szegmensek közötti Térkép és a helyi szegmens térképek megtartására, amikor a szegmenses Térkép módosításait hajtja végre. Például a felügyeleti hitelesítő adatokkal hozza létre a szegmens Map Manager-objektumot ( **GetSqlShardMapManager** használatával ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

A **smmAdminConnectionString** változó egy olyan, a felügyeleti hitelesítő adatokat tartalmazó kapcsolatok karakterlánca. A felhasználói azonosító és jelszó írási/olvasási hozzáférést biztosít mind a szegmenses Térkép adatbázisához, mind pedig az egyes szegmensekhez. A felügyeleti kapcsolatok karakterlánca tartalmazza a kiszolgáló nevét és az adatbázis nevét is, hogy azonosítsa a globális szegmenses Térkép-adatbázist. Az alábbi egy tipikus, a következőhöz tartozó kapcsolatok karakterlánca:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Ne használja a "username@server" formátumú értékeket, hanem csak a "username" értéket használja.  Ennek az az oka, hogy a hitelesítő adatoknak működniük kell a szegmenses Térkép-kezelő adatbázisán és az egyes szegmenseken, amelyek különböző kiszolgálókon lehetnek.

## <a name="access-credentials"></a>Hozzáférési hitelesítő adatok

Ha olyan alkalmazásban hoz létre egy szegmenses Térkép-kezelőt, amely nem felügyeli a szegmenses térképeket, olyan hitelesítő adatokat használjon, amelyek csak olvasási engedéllyel rendelkeznek a globális szegmenses térképen. A rendszer a globális szegmens-hozzárendelésből beolvasott adatokat a hitelesítő adatok alapján, az [adatkezelési útvonalhoz](sql-database-elastic-scale-data-dependent-routing.md) , valamint az ügyfélen található szegmenses térképi gyorsítótár feltöltéséhez használja. A hitelesítő adatok a **GetSqlShardMapManager**azonos hívási mintán keresztül érhetők el:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Vegye figyelembe, hogy a **smmReadOnlyConnectionString** használata a **nem rendszergazda** felhasználók nevében történő hozzáféréshez szükséges különböző hitelesítő adatok használatát tükrözi: ezek a hitelesítő adatok nem adhatnak írási engedélyeket a globális szegmenses térképhez.

## <a name="connection-credentials"></a>Kapcsolatok hitelesítő adatai

További hitelesítő adatokra akkor van szükség, ha a **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) módszert használja a horizontális Felskálázási kulcshoz társított szegmens eléréséhez. Ezeknek a hitelesítő adatoknak meg kell adniuk a csak olvasási hozzáféréshez szükséges engedélyeket a szegmensen lévő helyi szegmensek leképezési tábláihoz. Erre azért van szükség, hogy a szegmensben az Adatfüggő útválasztáshoz a kapcsolatok érvényesítését végezze. Ez a kódrészlet lehetővé teszi az adathozzáférést az Adatfüggő útválasztás kontextusában:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

Ebben a példában a **smmUserConnectionString** a felhasználói hitelesítő adatokhoz tartozó kapcsolatok karakterláncát tárolja. Az Azure SQL DB-hez itt a felhasználói hitelesítő adatokhoz tartozó jellemző a következő:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

A rendszergazdai hitelesítő adatokhoz hasonlóan a "username@server" formában ne használja az értékeket. Ehelyett egyszerűen használja a "username" kifejezést.  Azt is vegye figyelembe, hogy a kapcsolatok karakterlánca nem tartalmazza a kiszolgáló nevét és az adatbázis nevét. Ennek az az oka, hogy a **OpenConnectionForKey** -hívás automatikusan irányítja a kapcsolódást a megfelelő szegmensre a kulcs alapján. Ezért az adatbázis neve és a kiszolgáló neve nincs megadva.

## <a name="see-also"></a>Lásd még:

[Adatbázisok és bejelentkezések kezelése az Azure SQL Database-ben](sql-database-manage-logins.md)

[Az SQL Database-adatbázis védelme](sql-database-security-overview.md)

[rugalmas adatbázis-feladatok](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
