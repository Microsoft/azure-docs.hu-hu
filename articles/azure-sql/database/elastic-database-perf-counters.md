---
title: Teljesítményszámlálók a szegmenses Térkép kezelőjének nyomon követéséhez
description: ShardMapManager osztály és az Adatfüggő útválasztási teljesítményszámlálók
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: 3bfbf56b6e5f2be33b407945490531e6e2e8ac47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92781260"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Teljesítményszámlálók létrehozása a szegmenses Térkép kezelőjé teljesítményének nyomon követéséhez
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A teljesítményszámlálók segítségével nyomon követheti az [Adatfüggő útválasztási](elastic-scale-data-dependent-routing.md) műveletek teljesítményét. Ezek a teljesítményszámlálók a Teljesítményfigyelőben, a "Elastic Database: szegmens felügyelet" kategóriában érhetők el.

Rögzítheti egy szegmenses [Térkép kezelőjének](elastic-scale-shard-map-management.md)teljesítményét, különösen az [Adatfüggő útválasztás](elastic-scale-data-dependent-routing.md)használata esetén. A számlálók a Microsoft. Azure. SqlDatabase. ElasticScale. Client osztály metódusával jönnek létre.  


**A legújabb verzióhoz:** Lépjen a [Microsoft. Azure. SqlDatabase. ElasticScale. Client webhelyre](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Lásd még [az alkalmazás frissítése a legújabb rugalmas adatbázis-ügyféloldali kódtár használatára](elastic-scale-upgrade-client-library.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

* A teljesítmény kategóriájának és számlálóinak létrehozásához a felhasználónak a helyi **rendszergazdák** csoport tagjának kell lennie az alkalmazást üzemeltető gépen.  
* A teljesítményszámláló-példányok létrehozásához és a számlálók frissítéséhez a felhasználónak a **rendszergazdák** vagy a **Teljesítményfigyelő felhasználói** csoport tagjának kell lennie.

## <a name="create-performance-category-and-counters"></a>Teljesítmény kategória és számlálók létrehozása

A számlálók létrehozásához hívja meg a [ShardMapManagementFactory osztály](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)CreatePerformanceCategoryAndCounters metódusát. Csak egy rendszergazda hajthatja végre a metódust:

`ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()`

[Ezt](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) a PowerShell-szkriptet használhatja a metódus végrehajtásához is.
A metódus a következő teljesítményszámlálókat hozza létre:  

* **Gyorsítótárazott leképezések**: a szegmenses térképhez gyorsítótárazott leképezések száma.
* **DDR-műveletek/mp**: az adatszegmensi térképhez kapcsolódó adattovábbítási műveletek aránya. Ez a számláló akkor frissül, ha a [OpenConnectionForKey ()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) hívása sikeres csatlakozást eredményez a cél szegmenshez.
* **Keresési gyorsítótár látogatottságának leképezése/mp**: a gyorsítótár keresési műveleteinek aránya a szegmenses leképezésben.
* **Leképezési keresési gyorsítótár-kihagyás/mp**: a sikertelen gyorsítótár-keresési műveletek gyakorisága a szegmenses leképezésben való leképezésekhez.
* A (z) **gyorsítótárban hozzáadott vagy frissített leképezések**: a szegmenshez tartozó Térkép gyorsítótárban való hozzáadásának vagy frissítésének gyakorisága.
* **Hozzárendelések eltávolítva a gyorsítótárból/másodpercből**: a szegmensek közötti Térkép gyorsítótárában lévő leképezések eltávolításának gyakorisága.

A teljesítményszámlálók minden egyes gyorsítótárazott szegmens-leképezéshez jönnek létre.  

## <a name="notes"></a>Jegyzetek

A következő események indítják el a teljesítményszámlálók létrehozását:  

* A [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) inicializálása a [lelkes betöltéssel](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), ha a ShardMapManager tartalmaz bármely szegmenses térképet. Ezek közé tartoznak a [GetSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) és a [TryGetSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) metódusok.
* Egy szegmenses Térkép sikeres keresése ( [GetShardMap ()](/previous-versions/azure/dn824215(v=azure.100)), [GetListShardMap ()](/previous-versions/azure/dn824212(v=azure.100)) vagy [GetRangeShardMap ()](/previous-versions/azure/dn824173(v=azure.100))használatával).
* A CreateShardMap () használatával sikerült létrehozni a szegmenses leképezést.

A teljesítményszámlálókat a rendszer az összes gyorsítótárazási művelettel frissíti a szegmens térképen és leképezéseken. A szegmens-hozzárendelés sikeres eltávolítása a DeleteShardMap () használatával a teljesítményszámlálók példányának törlését eredményezi.  

## <a name="best-practices"></a>Ajánlott eljárások

* A teljesítmény kategóriájának és számlálóinak létrehozását csak egyszer kell végrehajtani a ShardMapManager objektum létrehozása előtt. A CreatePerformanceCategoryAndCounters () parancs minden végrehajtása törli az előző számlálókat (az összes példány által jelentett adatvesztést), és újakat hoz létre.  
* A teljesítményszámláló példányai folyamat alapján jönnek létre. A rendszer a gyorsítótárból a szegmensek összes összeomlását vagy eltávolítását eredményezi, és törli a teljesítményszámlálók példányainak törlését.  

### <a name="see-also"></a>Lásd még

[Az Elastic Database szolgáltatásainak áttekintése](elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->