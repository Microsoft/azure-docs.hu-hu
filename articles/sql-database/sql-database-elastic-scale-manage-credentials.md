---
title: Az elastic database ügyfélkódtár a hitelesítő adatok kezelése |} A Microsoft Docs
description: A megfelelő szintű hitelesítő adatokat, a rendszergazdák csak olvasható, rugalmas adatbázis-alkalmazások beállítása
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: e6c7d682d9b16266208baeeff14168b3da157251
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584879"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Az Elastic Database ügyfélkódtár eléréséhez használt hitelesítő adatokat

A [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md) három különböző típusú hitelesítő adatok segítségével fér hozzá a [szilánkleképezés-kezelővel](sql-database-elastic-scale-shard-map-management.md). Attól függően, szükség van a hitelesítő adatok használata a legalacsonyabb hozzáférési szintet, lehetséges.

* **Felügyeleti hitelesítő adatok**: létrehozása vagy módosítása a szilánkleképezés-kezelővel. (Lásd a [szószedet](sql-database-elastic-scale-glossary.md).)
* **Hozzáférési hitelesítő adatokat**: egy meglévő szilánkleképezés-kezelővel szegmensek kapcsolatos információ eléréséhez.
* **Kapcsolat hitelesítő adatait**: szegmensek csatlakozni.

Lásd még: [adatbázisok és bejelentkezések Azure SQL Database kezelése](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Tudnivalók a felügyeleti hitelesítő adatok

Felügyeleti hitelesítő adatok segítségével hozzon létre egy **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) objektumot az alkalmazásokat, amelyek szegmenstérképet módosítására. (Lásd a [hozzáadása a rugalmas Adatbáziseszközök használatáról szegmensek](sql-database-elastic-scale-add-a-shard.md) és [Adatfüggő útválasztásnak](sql-database-elastic-scale-data-dependent-routing.md)). A felhasználó, a rugalmas méretezésű ügyfélkódtárral hoz létre az SQL-felhasználók és az SQL-bejelentkezésekben, és biztosítja, hogy minden egyes az olvasási/írási engedélyek a globális szegmenstérkép-adatbázis és az összes bontott adatbázisokat is kapnak. Ezek a hitelesítő adatok segítségével a globális szegmenstérkép és a helyi szegmenstérképet fenntartása, amikor a módosítások a szilánkleképezésbe menjenek végbe. Használja például a felügyeleti hitelesítő adatokat a szegmensek térkép manager-objektum létrehozása (használatával **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

A változó **smmAdminConnectionString** egy kapcsolati karakterlánc, amely tartalmazza a felügyeleti hitelesítő adatokat. A felhasználói Azonosítót és jelszót a szegmenstérkép-adatbázis és az egyes szegmensek olvasási/írási hozzáférést biztosítanak. A felügyeleti kapcsolati karakterláncot is tartalmaz, a kiszolgáló nevét és az adatbázis nevét a globális szegmenstérkép-adatbázis. Erre a célra a következő egy tipikus kapcsolati karakterlánc:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Ne használjon értékek formájában "username@server" – Ehelyett használja a "username" értéket.  Ennek az az oka a hitelesítő adatokat kell működniük a szegmenstérkép-kezelő adatbázis és a különböző kiszolgálókon, akkor az egyes szegmensek szemben.

## <a name="access-credentials"></a>Elérési hitelesítő adatok

Szilánk kezelő az létrehozásakor olyan alkalmazás, amely nem felügyelheti a szegmenstérképet, használja a hitelesítő adatokat, amelyeket a csak olvasási engedéllyel rendelkezik a globális szegmenstérkép. A globális szegmenstérkép ezeket a hitelesítő adatokat a lekért adatokat használja, a [Adatfüggő útválasztásnak](sql-database-elastic-scale-data-dependent-routing.md) és az ügyfélen lévő szegmensek térkép gyorsítótár adatokkal való feltöltése. A hitelesítő adatok megadását, a hívás minta keresztül **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Vegye figyelembe a használatát a **smmReadOnlyConnectionString** a különböző hitelesítő adatokat használjanak a más nevében a hozzáférés megfelelően **nem rendszergazdai** felhasználók: ezeket a hitelesítő adatokat kell biztosít írási engedéllyel a a globális szegmenstérkép.

## <a name="connection-credentials"></a>Kapcsolat hitelesítő adatait

További hitelesítő adatok szükségesek a használatakor a **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) módszer olyan szegmenskulcsot társított szegmensek eléréséhez. Ezeket a hitelesítő adatokat kell megadnia a csak olvasási hozzáférést a szegmens a helyi szegmens térkép táblázatokra vonatkozó engedélyek. Erre azért van szükség, a kapcsolat-ellenőrzéshez Adatfüggő útválasztás a szegmensben lévő. Ez a kódrészlet lehetővé teszi, hogy adatelérési Adatfüggő útválasztásnak környezetében:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

Ebben a példában **smmUserConnectionString** tárolja a felhasználói hitelesítő adatok kapcsolati karakterlánca. Az Azure SQL DB Íme a felhasználói hitelesítő adatokat egy tipikus kapcsolati karakterlánc:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Mivel a rendszergazdai hitelesítő adataival, ne használjon értékek formájában "username@server". Ehelyett használjon csak "felhasználónév".  Azt is vegye figyelembe, hogy a kapcsolati karakterlánc nem tartalmazza a kiszolgáló nevét és az adatbázis nevét. Ez azért van, a **OpenConnectionForKey** hívás automatikusan a kapcsolatot a kulcs alapján a megfelelő szegmenshez irányítja. Ezért az adatbázis nevét és a kiszolgáló neve nem tartozik.

## <a name="see-also"></a>Lásd még

[Adatbázisok és bejelentkezések kezelése az Azure SQL Database-ben](sql-database-manage-logins.md)

[Az SQL Database-adatbázis védelme](sql-database-security-overview.md)

[Ismerkedés a rugalmas adatbázis-feladatok](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
