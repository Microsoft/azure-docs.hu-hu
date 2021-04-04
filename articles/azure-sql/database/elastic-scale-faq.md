---
title: Rugalmas méretezés – gyakori kérdések
description: Gyakori kérdések Azure SQL Database rugalmas Skálázásról.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 51e15a8dc5e9f918c630397d6d6593f5bf561755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786904"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Rugalmas adatbázis-eszközök – gyakori kérdések (GYIK)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Ha egy vagy több-bérlős szegmens van, és nincs horizontális Felskálázási kulcs, hogyan tölthetők fel a séma adataihoz tartozó horizontális Felskálázási kulcs.

A séma-információs objektum csak egyesítési forgatókönyvek felosztására szolgál. Ha egy alkalmazás eredendően egybérlős, nincs szükség a felosztás egyesítése eszközre, így nincs szükség a séma-információs objektum feltöltésére.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Létrehoztam egy adatbázist, és már van egy szegmenses Map Manager, hogyan regisztrálhatom ezt az új adatbázist szegmensként

[A rugalmas adatbázis ügyféloldali függvénytárának használatával kapcsolatban lásd: szegmens hozzáadása egy alkalmazáshoz](elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Mennyibe kerül a rugalmas adatbázis eszközeinek díja

A rugalmas adatbázis-ügyfél függvénytárának használata nem jár semmilyen költséggel. A költségek csak azokra a Azure SQL Database adatbázisokra vannak felhalmozva, amelyeket a szegmensekhez és a szegmenses Térkép kezelőjéhez használ, valamint a felosztott egyesítési eszközhöz kiépített webes és feldolgozói szerepköröket is.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Miért nem működnek a hitelesítő adataim, amikor egy másik kiszolgálóról veszek fel egy szegmenst

Ne használja a hitelesítő adatokat "felhasználó azonosítója = username@servername " formában, hanem egyszerűen használja a "felhasználói azonosító = username" értéket.  Győződjön meg arról is, hogy a "username" bejelentkezési engedélyekkel rendelkezik a szegmensen.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Létre kell hozni egy szegmenses Térkép-kezelőt, és fel kell tölteni a szegmenseket minden alkalommal, amikor elkezdem az alkalmazásaim

Nem – a [ShardMapManagerFactory. CreateSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)egy egyszeri művelet, amely létrehozta a szegmenses Térkép-kezelőt.  Az alkalmazásnak a [ShardMapManagerFactory. TryGetSqlShardMapManager ()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) hívást kell használnia az alkalmazás indítási ideje alatt.  Alkalmazás-tartományhoz csak egy ilyen hívásnak kell lennie.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Kérdésem van a rugalmas adatbázis-eszközök használatával kapcsolatban, hogyan kaphatok választ

Kérjük, lépjen kapcsolatba velünk a [Microsoft Q&a SQL Database vonatkozó kérdés oldalán](/answers/topics/azure-sql-database.html).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Ha horizontális Felskálázási kulccsal szerezem be az adatbázis-csatlakozást, továbbra is lekérheti az ugyanazon a szegmensen lévő többi horizontális Felskálázási kulcsra vonatkozó adatlekérdezést.  Ez a kialakítás

A rugalmasan méretezhető API-k lehetővé teszik a megfelelő adatbázishoz való kapcsolódást a horizontális Felskálázási kulcshoz, de nem biztosítanak horizontális szűrést.  Adja hozzá a lekérdezés **Where** záradékát, hogy szükség esetén korlátozza a hatókört a megadott horizontális kulcsra.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Használhatok egy másik SQL Database-kiadást a saját szegmensem egyes szegmensei esetében

Igen, a szegmens egy önálló adatbázis, így az egyik szegmens lehet prémium kiadás, míg egy másik standard kiadás. Továbbá a szegmensek kiadása a szegmens élettartama során többször is fel-vagy leskálázást.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>A felosztott egyesítés eszköz kiépíti (vagy törli) az adatbázist egy megosztott vagy egyesítési művelet során

Nem. A **felosztási** műveletek esetében a célként megadott adatbázisnak léteznie kell a megfelelő sémával, és regisztrálni kell a szegmenses Térkép kezelőjével.  Az **egyesítési** műveletekhez törölnie kell a szegmenst a szegmens Térkép kezelőjéből, majd törölnie kell az adatbázist.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]