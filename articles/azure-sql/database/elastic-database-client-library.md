---
title: Skálázható felhőalapú adatbázisok készítése
description: Méretezhető .NET-adatbázis-alkalmazásokat hozhat létre az Elastic Database ügyféloldali kódtár használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/25/2018
ms.openlocfilehash: bfe5dc00ba0255520c04ea85157f0b8bdc71b590
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84050225"
---
# <a name="building-scalable-cloud-databases"></a>Skálázható felhőalapú adatbázisok készítése
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az adatbázisok horizontális felskálázása könnyen megvalósítható a Azure SQL Database méretezhető eszközeivel és szolgáltatásaival. Az **Elastic Database ügyféloldali kódtár** használatával méretezhető adatbázisok hozhatók létre és kezelhetők. Ez a funkció lehetővé teszi, hogy könnyedén fejlesszen több száz vagy akár több ezer, a Azure SQL Databaseban található adatbázisok használatával.

Letöltés:

* A könyvtár Java-verziója: [Maven Central adattár](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A könyvtár .NET-verziója: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Dokumentáció

1. [Ismerkedés az Elastic Database-eszközökkel](elastic-scale-get-started.md)
2. [Elastic Database funkciók](elastic-scale-introduction.md)
3. [A szilánkleképezés kezelése](elastic-scale-shard-map-management.md)
4. [Meglévő adatbázisok migrálása a vertikális felskálázáshoz](elastic-convert-to-use-elastic-tools.md)
5. [Adatfüggő útválasztás](elastic-scale-data-dependent-routing.md)
6. [Több szegmenses lekérdezések](elastic-scale-multishard-querying.md)
7. [Szegmens hozzáadása Elastic Database eszközök használatával](elastic-scale-add-a-shard.md)
8. [Több-bérlős alkalmazások Elastic Database eszközökkel és sor szintű biztonsággal](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)
9. [Ügyféloldali függvénytár-alkalmazások frissítése](elastic-scale-upgrade-client-library.md) 
10. [Rugalmas lekérdezések áttekintése](elastic-query-overview.md)
11. [Elastic Database eszközök szószedete](elastic-scale-glossary.md)
12. [Ügyféloldali kódtár Elastic Database Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Elastic Database ügyféloldali kódtár jól öltözött](elastic-scale-working-with-dapper.md)
14. [Felosztás és egyesítés eszköz](elastic-scale-overview-split-and-merge.md)
15. [Teljesítményszámlálók a szilánkleképezés-kezelőhöz](elastic-database-client-library.md) 
16. [Elastic Database-eszközökre vonatkozó gyakori kérdések](elastic-scale-faq.md)

## <a name="client-capabilities"></a>Ügyfél-képességek

A horizontális felskálázást *használó alkalmazások* horizontálisan a fejlesztő és a rendszergazda számára is kihívást jelent. Az ügyféloldali kódtár leegyszerűsíti a felügyeleti feladatokat azáltal, hogy olyan eszközöket biztosít, amelyek lehetővé teszik a fejlesztők és a rendszergazdák számára a kibővített adatbázisok kezelését. Egy tipikus példában számos adatbázis létezik, amely a "szegmensek" néven is ismert. Az ügyfelek ugyanabban az adatbázisban találhatók, és az ügyfelek egy-egy adatbázisa (egybérlős séma). Az ügyféloldali kódtár a következő funkciókat tartalmazza:

- Szegmenses társítások **kezelése**: a rendszer létrehozza a "szegmens Map Manager" nevű speciális adatbázist. A szegmensek közötti társítások kezelése lehetővé teszi, hogy az alkalmazások a szegmensekkel kapcsolatos metaadatokat kezeljék. A fejlesztők ezt a funkciót használhatják az adatbázisok szegmensként való regisztrálásához, leírják az egyes horizontális kulcsok vagy a kulcsok tartományának leképezéseit az adatbázisokra, és karbantartják ezeket a metaadatokat, mivel az adatbázisok száma és összetétele változik a kapacitás változásainak megfelelően. A Elastic Database ügyféloldali kódtár nélkül nagy időt kell fordítani a felügyeleti kód megírására a horizontális skálázás megvalósítása során. Részletekért lásd: a szegmenses [hozzárendelések kezelése](elastic-scale-shard-map-management.md).

- **Adatfüggő útválasztás**: Képzelje el az alkalmazásba érkező kérést. A kérelem horizontálisan megjelenő kulcs értéke alapján az alkalmazásnak meg kell határoznia a megfelelő adatbázist a kulcs értéke alapján. Ezután megnyílik egy kapcsolódás az adatbázishoz a kérelem feldolgozásához. Az Adatfüggő útválasztás lehetővé teszi, hogy a kapcsolatokat egyetlen egyszerű hívással nyissa meg az alkalmazás szegmenses térképére. Az Adatfüggő útválasztás egy másik, az Elastic Database ügyféloldali függvénytárban található funkció által lefedett infrastruktúra-kód. Részletekért lásd: [az Adatfüggő útválasztás](elastic-scale-data-dependent-routing.md).
- **Több szegmenses lekérdezések (MSQ)**: több szegmenses lekérdezés működik, ha egy kérelem több (vagy az összes) szegmenst is magában foglal. Egy több szegmensből álló lekérdezés ugyanazt a T-SQL-kódot hajtja végre az összes szegmensen vagy egy szegmensen. A résztvevő szegmensek eredményeit egyesítjük egy átfogó eredménybe az Unió összes szemantikai funkciójával. Az ügyféloldali függvénytáron keresztül közzétett funkciók számos feladatot kezelnek, többek között a következők: a kapcsolatok kezelése, a szálak kezelése, a hibák kezelése és a közbenső eredmények feldolgozása. A MSQ legfeljebb száz szegmenst tud lekérdezni. Részletekért lásd: [több szegmenses lekérdezés](elastic-scale-multishard-querying.md).

A Elastic Database-eszközöket használó ügyfelek általában teljes T-SQL-funkciókat kaphatnak, amikor a szegmensek közötti helyi műveleteket elküldik, és nem a saját szemantikai feladatait.



## <a name="next-steps"></a>Következő lépések

- Elastic Database ügyféloldali kódtár ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.net](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) – a könyvtár **letöltéséhez** .

- [Ismerkedés a Elastic Database eszközökkel](elastic-scale-get-started.md) – az ügyfél funkcióit bemutató **minta alkalmazás** kipróbálása.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.net](https://github.com/Azure/elastic-db-tools)) – a kódhoz való hozzájárulást tesz elérhetővé.
- [Azure SQL Database rugalmas lekérdezés áttekintése](elastic-query-overview.md) – rugalmas lekérdezések használata.

- Adatáthelyezés a kibővített [felhőalapú adatbázisok között](elastic-scale-overview-split-and-merge.md) – a **felosztott egyesítés eszköz** használatával kapcsolatos utasításokért.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

