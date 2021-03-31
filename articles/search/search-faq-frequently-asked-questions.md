---
title: Gyakori kérdések (GYIK)
titleSuffix: Azure Cognitive Search
description: Választ kaphat a Microsoft Azure Cognitive Search szolgáltatással kapcsolatos gyakori kérdésekre, amelyek a felhőben üzemeltetett keresési szolgáltatás a Microsoft Azureon.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 9d6acdcf9487b2d1a5964d4ec686cd23666275b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88923092"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure Cognitive Search – gyakori kérdések (GYIK)

 Válaszok az Azure Cognitive Search kapcsolatos fogalmakkal, kóddal és forgatókönyvekkel kapcsolatos gyakori kérdésekre.

## <a name="platform"></a>Platform

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Miben különbözik az Azure Cognitive Search a teljes szöveges kereséstől az adatbázis-kezelő szolgáltatásban?

Az Azure Cognitive Search több adatforrást, [nyelvi elemzést is támogat számos nyelvhez](/rest/api/searchservice/language-support), [az egyéni elemzésekhez érdekes és szokatlan adatbevitelt](/rest/api/searchservice/custom-analyzers-in-azure-search), keresési rangsor-vezérlést [pontozási profilokon](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)keresztül, valamint felhasználói élményt biztosító funkciókkal, például typeahead, találatok kiemelésével és sokoldalú navigálással. Emellett más funkciókat is tartalmaz, például a szinonimákat és a részletes lekérdezési szintaxist, de ezek általában nem különböztetik meg a funkciókat.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Szüneteltethető az Azure Cognitive Search szolgáltatás, és nem lehet leállítani a számlázást?

A szolgáltatás nem szüneteltethető. A számítási és tárolási erőforrások a szolgáltatás létrehozásakor kizárólagos használatra vannak lefoglalva. Az erőforrások igény szerinti felszabadítása és visszaigénylése nem lehetséges.

## <a name="indexing-operations"></a>Indexelési műveletek

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Indexek és indexek áthelyezése, biztonsági mentése és visszaállítása

A fejlesztési fázisban érdemes lehet áthelyezni az indexet a keresési szolgáltatások között. Használhat például egy alapszintű vagy ingyenes díjszabási szintet az index kifejlesztéséhez, majd azt a standard vagy magasabb szintű felhasználásra kívánja áthelyezni az éles használat érdekében. 

Vagy előfordulhat, hogy biztonsági másolatot szeretne készíteni egy olyan fájlról, amelyről később vissza lehet állítani. 

Ezeket a műveleteket az [Azure Cognitive Search .net minta](https://github.com/Azure-Samples/azure-search-dotnet-samples) **-tárház index-Backup-Restore** mintakód-kódjával teheti meg. 

Az Azure Cognitive Search REST API használatával bármikor lekérheti az [index definícióját](/rest/api/searchservice/get-index) is.

A Azure Portalban jelenleg nincs beépített index-kinyerés, pillanatkép vagy biztonsági mentési visszaállítási funkció. A biztonsági mentési és visszaállítási funkciókat azonban egy későbbi kiadásban vesszük fontolóra. Ha meg szeretné jeleníteni a funkció támogatását, szavazzon a [felhasználói hangon](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Visszaállíthatom az indexet vagy a szolgáltatást a törlés után?

Nem, ha töröl egy Azure Cognitive Search indexet vagy szolgáltatást, azt nem lehet helyreállítani. Ha töröl egy Azure Cognitive Search szolgáltatást, a szolgáltatásban lévő összes index véglegesen törlődik. Ha olyan Azure-erőforráscsoportot töröl, amely egy vagy több Azure Cognitive Search-szolgáltatást tartalmaz, az összes szolgáltatás véglegesen törlődik.  

Az erőforrások, például az indexek, az indexelő, az adatforrások és a szakértelmével újbóli létrehozásához újra létre kell hozni őket a kódból. 

Az indexek újbóli létrehozásához a külső forrásokból származó adatok újraindexelése szükséges. Ezért javasoljuk, hogy őrizze meg az eredeti adatok fő másolatát vagy biztonsági mentését egy másik adattárban, például Azure SQL Database vagy Cosmos DB.

Alternatív megoldásként használhatja az [Azure Cognitive Search .net minta](https://github.com/Azure-Samples/azure-search-dotnet-samples) -tárházának **index-Backup-Restore** mintakód elemét, hogy biztonsági másolatot készítsen egy index-definícióról, és hogyan indexelje a PILLANATKÉPet egy sor JSON-fájlra. Később az eszköz és a fájlok segítségével visszaállíthatja az indexet, ha szükséges.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Indexelhető-e a SQL Database replikák ( [Azure SQL Database indexelő](./search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)alkalmazásra vonatkozik)

Az elsődleges vagy másodlagos replikák adatforrásként való használata nem vonatkozik a teljesen új indexek létrehozásakor. Az indexnek a növekményes frissítésekkel (módosított rekordok alapján) való frissítése azonban az elsődleges replikát igényli. Ez a követelmény SQL Databaseból származik, amely csak az elsődleges replikák változás-követését garantálja. Ha másodlagos replikákat próbál használni egy index-frissítési feladathoz, akkor nem garantáljuk az összes adat beszerzését.

## <a name="search-operations"></a>Keresési műveletek

### <a name="can-i-search-across-multiple-indexes"></a>Kereshetek több index között?

Nem, ez a művelet nem támogatott. A keresés mindig egyetlen indexre terjed ki.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Korlátozható a keresési index hozzáférése a felhasználói identitás alapján?

A szűrővel [biztonsági szűrőket](./search-security-trimming-for-azure-search.md) alkalmazhat `search.in()` . A szűrő jól együttműködik a [(z) Azure Active Directory (HRE) identitás-kezelési szolgáltatásokkal](./search-security-trimming-for-azure-search-with-aad.md) , hogy a keresési eredményeket a definiált felhasználói csoporttagság alapján vágja ki.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Miért van nulla egyezés az érvényes feltételek alapján?

A leggyakoribb eset az, hogy az egyes lekérdezési típusok a különböző keresési viselkedéseket és a nyelvi elemzések szintjét támogatják. A teljes szöveges keresés, amely a legelterjedtebb munkaterhelés, tartalmaz egy nyelvi elemzési szakaszt, amely lebontja a kifejezéseket a legfelső szintű űrlapokra. A lekérdezések elemzésének ezen aspektusa szélesebb körű, a lehetséges egyezésekre mutató, a jogkivonatok által megadottnál nagyobb számú változatot vet fel.

A helyettesítő karakteres, a zavaros és a regex lekérdezéseket azonban nem elemezze, például a reguláris kifejezéseket vagy a kifejezéseket, és gyenge visszahívást eredményezhet, ha a lekérdezés nem felel meg a szó elemzett formátumának a keresési indexben. A lekérdezések elemzésével és elemzésével kapcsolatos további információkért lásd: a [lekérdezési architektúra](./search-lucene-query-architecture.md).

### <a name="my-wildcard-searches-are-slow"></a>A helyettesítő karakteres keresések lassúak.

A legtöbb helyettesítő karakteres keresési lekérdezést, például az előtagot, a fuzzy és a regexet, a rendszer a keresési indexben lévő egyező kifejezésekkel együtt írja felül. A keresési index ellenőrzésének további feldolgozása a késéshez. Emellett a széles körű keresési lekérdezések, mint `a*` például a sok kifejezéssel valószínűleg újraírhatók, nagyon lassúak lehetnek. Az elvégezhető helyettesítő karakteres keresések esetében érdemes lehet [Egyéni elemzőt](/rest/api/searchservice/custom-analyzers-in-azure-search)definiálni.

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Miért rangsorolja a keresés a 1,0 konstans vagy egyenlő pontszámát minden találatot illetően?

Alapértelmezés szerint a keresési eredmények a [megfeleltetési feltételek statisztikai tulajdonságai](search-lucene-query-architecture.md#stage-4-scoring)alapján vannak kiértékelve, és az eredményhalmaz magas – alacsony értékre vannak rendezve. Néhány lekérdezési típus (helyettesítő karakter, előtag, regex) azonban mindig a teljes dokumentum pontszámával járul hozzá az állandó pontszámhoz. Ez a működésmód szándékos. Az Azure Cognitive Search állandó pontszámot ír elő, amely lehetővé teszi, hogy a lekérdezési kiterjesztésen keresztül megtalált egyezések szerepeljenek az eredményekben anélkül, hogy ez befolyásolná a rangsorolást.

Tegyük fel például, hogy a helyettesítő karakteres keresésben szereplő "Tour *" bemenet a "Tours", a "Tourettes" és a "turmalin" egyezéseket eredményez. Ezeknek az eredményeknek a jellegéből adódóan nem lehet ésszerűen következtetni, hogy mely feltételek sokkal értékesek, mint mások. Ezért figyelmen kívül hagyja a kifejezés gyakoriságát, ha a helyettesítő karakteres, előtag és regex típusú lekérdezéseket eredményez. A keresési eredmények részleges bevitel alapján állandó pontszámot kapnak, hogy elkerülje a potenciálisan váratlan egyezések torzítását.

## <a name="skillset-operations"></a>Készségkészlet-műveletek

### <a name="are-there-any-tips-or-tricks-to-reduce-cognitive-services-charges-on-ingestion"></a>Vannak tippek vagy trükkök a kognitív szolgáltatások betöltésének csökkentése érdekében?

Tisztában van azzal, hogy nem szeretné, hogy a beépített készségeket vagy az egyéni képességeket nem kívánja végrehajtani, mint amennyire feltétlenül szükség van, különösen akkor, ha több millió dokumentumot kell feldolgoznia. Ennek szem előtt tartásával a "növekményes gazdagodás" képességekkel készségkészlet a végrehajtást. Lényegében megadhat egy gyorsítótárbeli helyet (blob Storage-beli kapcsolódási karakterláncot), amely a "köztes" dúsítási lépések kimenetének tárolására szolgál majd.  Ez lehetővé teszi, hogy a dúsítási folyamat intelligens legyen, és csak a készségkészlet módosításakor szükséges dúsításokat alkalmazza. Ez természetesen az indexelési időt is menti, mivel a folyamat hatékonyabbá válik.

További információ a [növekményes dúsításról](cognitive-search-incremental-indexing-conceptual.md)

## <a name="design-patterns"></a>Tervezési minták

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Mi a legjobb megközelítés a honosított keresés megvalósításához?

A legtöbb ügyfél dedikált mezőket választ egy gyűjteményben, amikor különböző területi beállításokat (nyelveket) támogat ugyanabban az indexben. A területi beállításokkal megadott mezők segítségével megfelelő elemzőt rendelhet hozzá. Tegyük fel például, hogy a Microsoft francia analizátort egy francia sztringeket tartalmazó mezőhöz rendeli. Emellett leegyszerűsíti a szűrést. Ha tudja, hogy egy lekérdezés egy fr-fr oldalon van kezdeményezve, a keresési eredmények erre a mezőre korlátozhatók. Vagy hozzon létre egy [pontozási profilt](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , hogy a mező a relatív súlyozást adja. Az Azure Cognitive Search több mint [50 nyelvi elemzőt](./search-language-support.md) támogat, amelyek közül választhat.

## <a name="next-steps"></a>Következő lépések

Egy hiányzó funkcióval vagy funkcióval kapcsolatos kérdése van? Kérje a szolgáltatást a [felhasználói hangposta](https://feedback.azure.com/forums/263029-azure-search)webhelyén.

## <a name="see-also"></a>Lásd még

 [StackOverflow: Azure Cognitive Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)  
 [Mi az az Azure Cognitive Search?](search-what-is-azure-search.md)