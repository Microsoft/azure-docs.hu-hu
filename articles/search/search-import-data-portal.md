---
title: Adatimportálás keresési indexbe az Azure Portal használatával
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan használhatja az adatok importálása varázslót a Azure Portal az Azure-adatok Cosmos DB, blob Storage, Table Storage, SQL Database, SQL felügyelt példány és SQL Server Azure-beli virtuális gépeken való bejárásához.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cff009d5d1e187e8d0330fadca530b57b3e3d21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935211"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Az Azure Cognitive Search adatimportálási varázslója

A Azure Portal **adatimportálási** varázslót biztosít az Azure Cognitive Search irányítópultján a prototípus-készítéshez és az indexek betöltéséhez. Ez a cikk a varázsló, bemenetek és kimenetek, valamint egyes használati információk használatának előnyeit és korlátozásait ismerteti. A beépített mintaadatok használatával kapcsolatos gyakorlati útmutatásért lásd az [Azure Cognitive Search index létrehozása a Azure Portal](search-get-started-portal.md) rövid útmutató segítségével című témakört.

A varázsló által végrehajtott műveletek a következők:

1 – kapcsolódás támogatott Azure-adatforráshoz.

2 – hozzon létre egy index sémát, amely a mintavételi forrásadatok alapján lett kikövetkeztetve.

3 – igény szerint hozzáadhat AI-bővítéseket a tartalom és a struktúra kinyeréséhez vagy létrehozásához.

4 – a varázsló futtatása objektumok létrehozásához, az adatimportáláshoz, az ütemterv és egyéb konfigurációs beállítások megadásához.

A varázsló a keresési szolgáltatásba mentett objektumok számát jeleníti meg, amelyekhez hozzáférhet a programozott módon vagy más eszközökhöz.

## <a name="advantages-and-limitations"></a>Előnyök és korlátozások

A kód írása előtt használhatja a varázslót a prototípus-készítéshez és a próba-koncepció teszteléséhez. A varázsló külső adatforrásokhoz csatlakozik, a kezdeti index létrehozásához az adatmintákat, majd JSON-dokumentumként importálja azokat az Azure Cognitive Search-beli indexbe. 

A mintavétel az a folyamat, amellyel az index sémája következtetve van, és bizonyos korlátozásokkal rendelkezik. Az adatforrás létrehozásakor a varázsló felvesz egy mintát a dokumentumokból, hogy eldöntse, hogy az adatforráshoz mely oszlopok tartoznak. Nem minden fájl olvasása történik, mivel ez akár órákat is igénybe vehet a nagy méretű adatforrások esetében. A dokumentumok, a forrás metaadatainak, például a mezőnév vagy a típus kiválasztásával mezőket hozhat létre egy index sémában. A forrásadatok összetettsége alapján előfordulhat, hogy módosítania kell a kezdeti sémát a pontosság kedvéért, vagy ki kell terjesztenie a teljességre. A módosításokat a tárgymutató-definíció oldalán is elvégezheti.

Összességében a varázsló használatának előnyei egyértelműek: ha teljesülnek a követelmények, a lekérdezhető indexek perceken belül is megtekinthetők. A varázsló a többek között az indexelés bonyolultságát, például a JSON-dokumentumok küldését is lehetővé teszi.

Az ismert korlátozások a következőképpen vannak Összefoglalva:

+ A varázsló nem támogatja az ismétlést vagy az újbóli használatát. A varázsló minden lépése új indexet, készségkészlet és indexelő konfigurációt hoz létre. A varázslóban csak az adatforrások maradhatnak meg és használhatók újra. Más objektumok szerkesztéséhez vagy pontosításához a REST API-kat vagy a .NET SDK-t kell használnia a szerkezetek lekéréséhez és módosításához.

+ A forrás tartalmának egy támogatott Azure-adatforrásban kell lennie.

+ A mintavétel a forrásadatok egy részhalmazán alapul. Nagyméretű adatforrások esetén lehetséges, hogy a varázsló kihagyja a mezőket. Előfordulhat, hogy ki kell terjesztenie a sémát, vagy ki kell javítania a késleltetett adattípusokat, ha a mintavételezés nem elegendő.

+ A portálon elérhető AI-gazdagítás néhány beépített képességre korlátozódik. 

+ A varázsló által létrehozható [Tudásbázis](knowledge-store-concept-intro.md)néhány alapértelmezett kivetítésre korlátozódik. Ha menteni szeretné a varázsló által létrehozott dúsított dokumentumokat, a blob-tároló és a táblák alapértelmezett névvel és struktúrával rendelkeznek.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>Adatforrás bemenete

Az **adatok importálása** varázsló egy külső adatforráshoz csatlakozik, amely az Azure Cognitive Search indexelő által biztosított belső logikát használja, amely a forrásként való mintavételre, a metaadatok olvasására, a tartalom és a szerkezet olvasására szolgáló dokumentációra, valamint a tartalmak JSON-ként való szerializálására szolgál az azure-Cognitive Search való későbbi importáláshoz.

Csak egyetlen táblából, adatbázis-nézetből vagy ezzel egyenértékű adatszerkezetből importálhat, azonban a struktúra hierarchikus vagy beágyazott alstruktúrákat is tartalmazhat. További információkért lásd: [komplex típusok modellezése](search-howto-complex-data-types.md).

A varázsló futtatása előtt létre kell hoznia ezt az egyetlen táblát vagy nézetet, és tartalmaznia kell a tartalmat. Nyilvánvaló okok miatt nem érdemes az **adatimportálás** varázslót üres adatforráson futtatni.

|  Kiválasztás | Leírás |
| ---------- | ----------- |
| **Meglévő adatforrás** |Ha már van definiálva indexelő a keresési szolgáltatásban, lehet, hogy van egy meglévő adatforrás-definíciója, amelyet újra használhat. Az Azure Cognitive Searchban az adatforrás-objektumokat csak az indexelő használják. Az adatforrás-objektumokat programozott módon vagy az **adatimportálás** varázslóval is létrehozhatja, és igény szerint újra felhasználhatja őket.|
| **Példák**| Az Azure Cognitive Search két beépített minta adatforrást biztosít, amelyek az oktatóanyagokban és a gyors útmutatókban használhatók: egy ingatlan SQL Database-adatbázis és egy Cosmos DB-on üzemeltetett Hotels-adatbázis. A Hotels-minta alapján történő átjáráshoz tekintse meg az [index létrehozása a Azure Portal](search-get-started-portal.md) rövid útmutatóban. |
| [**Azure SQL Database vagy SQL felügyelt példány**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |A szolgáltatás neve, az olvasási engedéllyel rendelkező adatbázis-felhasználó hitelesítő adatai, valamint az adatbázis neve megadható vagy az oldalon, vagy az ADO.NET kapcsolati sztring használatával. A tulajdonságok megtekintéséhez vagy testreszabásához válassza ki a kapcsolati sztring lehetőséget. <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet.|
| **Azure-beli virtuális gépeken futtatott SQL Server** |Adja meg a teljes szolgáltatásnevet, a felhasználói azonosítót és a jelszót, valamint az adatbázist a kapcsolatok karakterláncként. Az adatforrás használatához a helyi tárolóban rendelkeznie kell egy korábban telepített tanúsítvánnyal, amely titkosítja a kapcsolatot. Útmutatásért lásd: [SQL VM-kapcsolódás az Azure Cognitive Searchhoz](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Az oldalon meg kell adni a sorhalmazt biztosító táblát vagy nézetet. Ez a lehetőség a sikeres csatlakozás után jelenik meg: egy legördülő listából választhatja ki a kívánt elemet. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|A követelmények a következőket tartalmazzák: a fiók, az adatbázis és a gyűjtemény. Az index tartalmazza a gyűjteményben szereplő összes dokumentumot. Megadhat egy lekérdezést, amely lelapul vagy szűrheti a sorhalmazt, vagy hagyja üresen a lekérdezést. A varázslóban nincs szükség lekérdezésre.|
| [**Azure-Blob Storage**](search-howto-indexing-azure-blob-storage.md) |A követelmények a következőket tartalmazzák: a tárolási fiók és egy tároló. Szükség esetén, ha csoportosítási célból a blob-nevek egy virtuális elnevezési konvenciót követnek, akkor a név virtuáliskönyvtár-részét a tárolóban található mappa neveként is megadhatja. A további információkat [a Blob Storage indexelését](search-howto-indexing-azure-blob-storage.md) ismertető témakör tartalmazza. |
| [**Azure-Table Storage**](search-howto-indexing-azure-tables.md) |A követelmények a következőket tartalmazzák: a tárolási fiók és egy táblanév. Szükség esetén megadhat egy lekérdezést a táblák részhalmazának beolvasásához. A további információkat [a Table Storage indexelését](search-howto-indexing-azure-tables.md) ismertető témakör tartalmazza. |

## <a name="wizard-output"></a>Varázsló kimenete

A varázsló a háttérben hozza létre, konfigurálja és hívja meg a következő objektumokat. A varázsló futtatása után megkeresheti a kimenetét a portál oldalain. A szolgáltatás Áttekintés lapja indexek, indexelő, adatforrások és szakértelmével listáját tartalmazza. Az index-definíciók teljes JSON-ben megtekinthetők a portálon. Más definíciók esetén a [REST API](/rest/api/searchservice/) használatával kérhet le konkrét objektumokat.

| Objektum | Description | 
|--------|-------------|
| [Adatforrás](/rest/api/searchservice/create-data-source)  | Megtartja a kapcsolódási adatokat a forrásadatok, beleértve a hitelesítő adatokat is. Az adatforrás-objektumok kizárólag indexelő alkalmazással használhatók. | 
| [Index](/rest/api/searchservice/create-index) | A teljes szöveges kereséshez és más lekérdezésekhez használt fizikai adatstruktúra. | 
| [Készségkészlet](/rest/api/searchservice/create-skillset) | A tartalmak manipulálására, átalakítására és alakítására vonatkozó utasítások teljes készlete, beleértve a képfájlokból származó információk elemzését és kinyerését. A nagyon egyszerű és korlátozott szerkezetek kivételével egy Cognitive Services erőforrásra mutató hivatkozást tartalmaz, amely a dúsítást biztosítja. Szükség esetén az is tartalmazhatja az áruház definícióját.  | 
| [Indexelő](/rest/api/searchservice/create-indexer)  | Egy olyan konfigurációs objektum, amely egy adatforrást, egy célként megadott indexet, egy opcionális készségkészlet, választható ütemtervet és opcionális konfigurációs beállításokat ad meg a hibák és a Base-64 kódoláshoz. |


## <a name="how-to-start-the-wizard"></a>A varázsló elindítása

Az adatok importálása varázsló a szolgáltatás Áttekintés lapjának parancssáv használatával indítható el.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a keresési szolgáltatás lapot az irányítópulton, vagy [Keresse meg a szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a szolgáltatás listában.

2. A felül található szolgáltatás áttekintése lapon kattintson az **adatimportálás** elemre.

   ![Adatimportálási parancs a portálon](./media/search-import-data-portal/import-data-cmd2.png "Az adatimportálás varázsló elindítása")

Más Azure-szolgáltatásokból is indíthat **importálási adatok** , például a Azure Cosmos db, a Azure SQL Database, az SQL felügyelt példánya és az Azure Blob Storage. A szolgáltatás áttekintése lapon a bal oldali navigációs panelen keresse meg az **Azure Cognitive Search hozzáadása** részt.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>Index-séma szerkesztése vagy befejezése a varázslóban

A varázsló létrehoz egy hiányos indexet, amely a bemeneti adatforrásból beszerzett dokumentumokkal lesz feltöltve. Funkcionális index esetén győződjön meg arról, hogy a következő elemek vannak meghatározva.

1. A mezőlista elkészült? Vegyen fel új mezőket a kihagyott mintavételezésből, és távolítsa el azokat, amelyek nem adhatnak értéket keresési élményhez, vagy nem használhatók [szűrő kifejezésben](search-query-odata-filter.md) vagy [pontozási profilban](index-add-scoring-profiles.md).

1. Megfelelő az adattípus a bejövő adattípusokhoz? Az Azure Cognitive Search támogatja az [Entity adatmodell-(EDM-) adattípusokat](/rest/api/searchservice/supported-data-types). Az Azure SQL-adatok esetében van olyan [leképezési diagram](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) , amely egyenértékű értékeket határoz meg. További hátteret a [mező-hozzárendelések és átalakítások](search-indexer-field-mappings.md)című témakörben talál.

1. Van egy mezője, amely *kulcsként* szolgálhat? A mezőnek EDM. stringnek kell lennie, és egyedi módon kell azonosítania a dokumentumot. A kapcsolódó adatértékek esetében előfordulhat, hogy az elsődleges kulcshoz van rendelve. A Blobok esetében ez lehet a következő: `metadata-storage-path` . Ha a mezőértékek szóközöket vagy kötőjeleket tartalmaznak, a **Speciális beállítások** területen a **Base-64 kódolási kulcs** beállítását kell megadnia **a következő** karakterek érvényesítési ellenőrzésének mellőzéséhez.

1. Attribútumok beállítása annak meghatározására, hogy az adott mező hogyan használható egy indexben. 

   Használja ki az időt ezzel a lépéssel, mert az attribútumok határozzák meg az index mezőinek fizikai kifejezését. Ha később módosítani kívánja az attribútumokat, még a programozott módon is, szinte mindig el kell dobnia és újra létre kell hoznia az indexet. A **kereshető** és lekérhető  alapvető attribútumok [elhanyagolható hatással vannak a tárterületre](search-what-is-an-index.md#index-size). A szűrők engedélyezése és a javaslatok használata növelheti a tárolási követelményeket. 
   
   + A **kereshetővé** teszi a teljes szöveges keresést. Az ingyenes űrlapos lekérdezésekben vagy a lekérdezési kifejezésekben használt összes mezőnek rendelkeznie kell ezzel az attribútummal. A rendszer fordított indexeket hoz létre minden **kereshetőként** megjelölt mezőhöz.

   + A **beolvasható** érték a keresési eredményekben lévő mezőt adja vissza. A keresési eredményekhez tartalmat biztosító minden mezőnek rendelkeznie kell ezzel az attribútummal. A mező beállítása nem befolyásolja az index méretét.

   + **Szűrhetővé** teszi, hogy a mező hivatkozhat a szűrési kifejezésekben. A **$Filter**  kifejezésben használt összes mezőnek rendelkeznie kell ezzel az attribútummal. A szűrési kifejezések pontos egyezéseket biztosítanak. Mivel a szöveges karakterláncok érintetlenek maradnak, további tárterületre van szükség a Verbatim-tartalom befogadásához.

   + A **többtényezős** Navigálás lehetővé teszi a mező számára a sokoldalú navigációt. Csak a **szűrőként** megjelölt mezők jelölhetők **meg.**

   + A **rendezhető** beállítás lehetővé teszi, hogy a mező egy sorba legyen használva. Az **$OrderBy** -kifejezésben használt összes mezőnek rendelkeznie kell ezzel az attribútummal.

1. Szüksége van a [lexikális analízisre](search-lucene-query-architecture.md#stage-2-lexical-analysis)? A **kereshető** EDM. String mezők esetében megadhat egy **elemzőt** , ha többnyelvű indexelést és lekérdezést szeretne használni. 

   Az alapértelmezett érték a *standard Lucene* , de Ön a *Microsoft angol nyelvét* is kiválaszthatja, ha a Microsoft Analyzert speciális lexikális feldolgozási folyamatra kívánja használni, például a szabálytalan főnév és a művelet-űrlapok feloldására. A portálon csak nyelvi elemzők adhatók meg. Az egyéni analizátorok vagy a nem nyelvi analizátorok, például a kulcsszavak, a mintázatok és így tovább, programozott módon hajthatók végre. További információ az elemzők használatáról: [nyelvi elemzők hozzáadása](search-language-support.md).

1. Szüksége van typeahead funkcióra automatikus kiegészítés vagy javasolt eredmények formájában? Jelölje be a **javaslat** a jelölőnégyzetet a [typeahead lekérdezési javaslatainak és az automatikus kiegészítésnek](index-add-suggesters.md) a kijelölt mezőkön való engedélyezéséhez. A javaslat a tokenes kifejezések számát adja hozzá az indexhez, így több tárterületet használ fel.


## <a name="next-steps"></a>Következő lépések

A varázsló előnyeinek és korlátainak megismeréséhez a legjobb módszer a lépés. Az alábbi rövid útmutató végigvezeti az egyes lépéseken.

> [!div class="nextstepaction"]
> [Azure Cognitive Search-index létrehozása a Azure Portal használatával](search-get-started-portal.md)