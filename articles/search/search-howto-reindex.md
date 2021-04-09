---
title: Keresési index újraépítése
titleSuffix: Azure Cognitive Search
description: Új elemek hozzáadása, meglévő elemek vagy dokumentumok frissítése vagy elavult dokumentumok törlése egy teljes újraépítéssel vagy részleges indexeléssel egy Azure Cognitive Search index frissítéséhez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 47e9b80bb25b7ff14695cc67682265fe338ff76f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119101"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Index újraépítése az Azure Cognitive Searchban

Ez a cikk azt ismerteti, hogyan lehet újraépíteni az Azure Cognitive Search indexét, a szükséges körülményeket, valamint a folyamatban lévő lekérdezési kérelmek újraépítésének következményeit enyhítő javaslatokat.

Az *Újraépítés* az indexhez társított fizikai adatstruktúrák eldobására és újbóli létrehozására utal, beleértve az összes mező alapú fordított indexet. Az Azure Cognitive Searchban nem lehet eldobni és újból létrehozni az egyes mezőket. Az indexek újraépítéséhez az összes mezőt törölni kell, újból létre kell hozni egy meglévő vagy módosított index-séma alapján, majd újra fel kell tölteni az indexbe küldött adatokkal, vagy külső forrásokból kell kihúzni őket. 

Az indexek újraépítése gyakori a fejlesztés során, amikor az index kialakítását megismétlik, de előfordulhat, hogy újra kell építenie egy éles szintű indexet a szerkezeti módosítások elvégzéséhez, például összetett típusok hozzáadását vagy mezők hozzáadását a javaslatokhoz.

## <a name="rebuild-versus-refresh"></a>"Újraépítés", illetve "frissítés"

Az Újraépítés nem tévesztendő össze az indexek tartalmának új, módosított vagy törölt dokumentumokkal való frissítésével. A Search Corpus frissítése szinte minden keresési alkalmazásban megtörténik, és bizonyos helyzetekben, amelyeknek a frissítésére van szükség (például akkor, ha a keresési Corpus tükröznie kell egy online értékesítési alkalmazás leltározási változásait).

Ha nem módosítja az index szerkezetét, az indexet az első betöltéséhez használt módszerek használatával frissítheti.

* A leküldéses módú indexeléshez hívja a [Hozzáadás, frissítés vagy törlés dokumentumok](/rest/api/searchservice/addupdate-or-delete-documents) lehetőséget a módosítások egy indexbe való leküldéséhez.

* Az indexelő esetében az [Indexelő végrehajtását ütemezhet](search-howto-schedule-indexers.md) , és a Change-Tracking vagy timestamps használatával azonosíthatja a különbözetet. Ha a frissítéseket gyorsabban kell megtekinteni, mint amit a Scheduler kezelhet, használhatja a leküldéses módú indexelést.

## <a name="rebuild-conditions"></a>Újraépítés feltételei

Ha a következő feltételek bármelyike teljesül, dobja el és hozza létre újból az indexet. 

| Feltétel | Leírás |
|-----------|-------------|
| Mező definíciójának módosítása | Egy mezőnév, adattípus vagy adott [index-attribútum](/rest/api/searchservice/create-index) (kereshető, szűrhető, rendezhető) módosítása teljes újraépítést igényel. |
| Analizátor kiosztása egy mezőhöz | Az [elemzők](search-analyzers.md) definiálva vannak egy indexben, majd a mezőkhöz vannak rendelve. Bármikor hozzáadhat egy új Analyzer-definíciót egy indexhez, de a mező létrehozásakor csak az analizátort lehet *hozzárendelni* . Ez az **elemző** és a **indexAnalyzer** tulajdonság esetében is igaz. A **searchAnalyzer** tulajdonság kivétel (ezt a tulajdonságot egy meglévő mezőhöz rendelheti hozzá). |
| Elemző definíciójának frissítése vagy törlése egy indexben | Az indexben nem lehet törölni vagy módosítani egy meglévő Analyzer-konfigurációt (analizátor, tokenizer, token Filter vagy char Filter), kivéve, ha újra létrehozza a teljes indexet. |
| Mező hozzáadása egy javaslathoz | Ha már létezik egy mező, és hozzá kívánja adni egy [javaslat](index-add-suggesters.md) létrehozásához, újra kell építenie az indexet. |
| Mező törlése | Egy mező összes nyomának fizikai eltávolításához újra kell építenie az indexet. Ha egy azonnali Újraépítés nem praktikus, módosíthatja az alkalmazás kódját, hogy letiltsa a hozzáférést a "törölt" mezőhöz, vagy a [$Select lekérdezési paraméterrel](search-query-odata-select.md) kiválaszthatja, hogy mely mezők legyenek megjelenítve az eredményhalmazban. Fizikailag a mező meghatározása és tartalma a következő Újraépítés marad az indexben, amikor olyan sémát alkalmaz, amely kihagyja a szóban forgó mezőt. |
| Rétegek váltása | Ha nagyobb kapacitásra van szüksége, nincs helyben történő frissítés a Azure Portalban. Létre kell hozni egy új szolgáltatást, és az indexeket teljesen fel kell építeni az új szolgáltatásból. Ennek a folyamatnak az automatizálásához használhatja az [Azure Cognitive Search .net minta](https://github.com/Azure-Samples/azure-search-dotnet-samples) **-tárház index-Backup-Restore** mintakód elemét. Az alkalmazás biztonsági mentést készít az indexről egy sor JSON-fájlra, majd újból létrehozza az indexet egy Ön által megadott keresési szolgáltatásban.|

## <a name="update-conditions"></a>Frissítési feltételek

Számos más módosítás is végezhető a meglévő fizikai szerkezetek befolyásolása nélkül. A következő módosítások *nem* igényelnek index-újraépítést. Ezekben a változásokban az [index definícióját frissítheti](/rest/api/searchservice/update-index) a módosításaival.

+ Új mező hozzáadása
+ Lekérhető **attribútum beállítása** meglévő mezőben
+ Meglévő mező **searchAnalyzer** beállítása
+ Új elemző definíció hozzáadása egy indexhez
+ Pontozási profilok hozzáadása, frissítése vagy törlése
+ CORS-beállítások hozzáadása, frissítése vagy törlése
+ SynonymMaps hozzáadása, frissítése vagy törlése

Új mező hozzáadásakor a meglévő indexelt dokumentumok null értéket kapnak az új mezőhöz. A jövőbeli adatfrissítéskor a külső forrásadatok értékei lecserélik az Azure Cognitive Search által hozzáadott nullák értékét. Az index tartalmának frissítésével kapcsolatos további információkért lásd: [dokumentumok hozzáadása, frissítése vagy törlése](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="how-to-rebuild-an-index"></a>Index újraépítése

A fejlesztés során az index séma gyakran változik. Megtervezheti, hogy olyan indexeket hozzon létre, amelyek törölhetők, újra létrehozhatók, és gyorsan újratölthetők egy kis reprezentatív adathalmaz használatával.

A már üzemben lévő alkalmazások esetében javasoljuk, hogy hozzon létre egy új indexet, amely párhuzamosan fut egy meglévő indextel, hogy elkerülje a lekérdezés leállását. Az alkalmazás kódja átirányítást biztosít az új indexre.

Az indexelés nem fut a háttérben, és a szolgáltatás kiegyenlíti a további indexelést a folyamatban lévő lekérdezéseknél. Az indexelés során [figyelheti a lekérdezési kérelmeket](search-monitor-queries.md) a portálon, így biztosítva a lekérdezések időben történő végrehajtását.

1. Döntse el, hogy szükség van-e újraépítésre. Ha csak mezőket ad hozzá, vagy megváltoztatja az index egy részét, amely nem kapcsolódik a mezőkhöz, akkor előfordulhat, hogy a [definíciót csak a](/rest/api/searchservice/update-index) törlés, az újbóli létrehozás és a teljes újratöltés nélkül tudja frissíteni.

1. Az [index definíciójának beszerzése](/rest/api/searchservice/get-index) abban az esetben, ha későbbi referenciára van szüksége.

1. [A meglévő index eldobása](/rest/api/searchservice/delete-index), feltéve, hogy nem az új és a régi indexeket futtatja egymás mellett. 

   Az indexet célzó lekérdezések azonnal el lesznek dobva. Ne feledje, hogy az index törlése visszafordíthatatlan, és megsemmisíti a mezők gyűjteményének és egyéb szerkezetének fizikai tárhelyét. Ne felejtse el meggondolni a következményeit a kidobás előtt. 

1. [Hozzon létre egy átdolgozott indexet](/rest/api/searchservice/create-index), amelyben a kérelem törzse módosított vagy módosított mezőértékeket tartalmaz.

1. [Töltse be az indexet](/rest/api/searchservice/addupdate-or-delete-documents) külső forrásból származó dokumentumokkal.

Az index létrehozásakor a rendszer a fizikai tárterületet az index séma minden mezőjéhez lefoglalja, és minden kereshető mezőhöz létrehoz egy fordított indexet. A nem kereshető mezők szűrőkben vagy kifejezésekben használhatók, de nem rendelkeznek invertált indexekkel, és nem teljes szöveges vagy zavaros kereshetők. Az indexek újraépítésekor a rendszer törli ezeket a fordított indexeket, és újból létrehozza az Ön által megadott index-séma alapján.

Amikor betölti az indexet, az egyes mezők fordított indexe az egyes dokumentumokból származó összes egyedi, jogkivonattal ellátott szót feltölti, és a megfelelő dokumentum-azonosítókat képezi le. Ha például egy Hotels-adatkészletet indexel, a város mezőjéhez létrehozott fordított indexben a Seattle, a Portland és más kifejezések is szerepelhetnek. A City mezőben a Seattle vagy a Portland kifejezést tartalmazó dokumentumokat a rendszer a kifejezés mellett sorolja fel. A [hozzáadási, frissítési vagy törlési](/rest/api/searchservice/addupdate-or-delete-documents) művelet esetén a feltételek és a dokumentum-azonosítók listája ennek megfelelően frissül.

> [!NOTE]
> Ha szigorú SLA-követelményekkel rendelkezik, érdemes lehet egy új szolgáltatást kifejezetten ehhez a munkához kiépíteni, és a fejlesztés és az indexelés az éles indexből teljes mértékben elkülönítve történik. Egy különálló szolgáltatás a saját hardverén fut, ami kiküszöböli az erőforrás-tartalom esetleges lehetőségét. A fejlesztés befejezésekor hagyja az új indexet, és átirányítja a lekérdezéseket az új végpontra és indexre, vagy ha befejezte a kódot, akkor az eredeti Azure Cognitive Search szolgáltatáson közzéteheti a módosított indexet. Jelenleg nincs olyan mechanizmus, amely használatra kész indexet helyez át egy másik szolgáltatásba.

## <a name="check-for-updates"></a>Frissítések keresése

Az első dokumentum betöltését követően azonnal megkezdheti az index lekérdezését. Ha ismeri a dokumentum AZONOSÍTÓját, a [keresési dokumentum REST API](/rest/api/searchservice/lookup-document) az adott dokumentumot adja vissza. A szélesebb körű teszteléshez várnia kell, amíg az index teljesen be nem töltődik, majd a lekérdezések segítségével ellenőrizze a várt környezetet.

A [Search Explorer](search-explorer.md) vagy egy webes tesztelési eszköz, például a [Poster](search-get-started-rest.md) vagy a [Visual Studio Code](search-get-started-vs-code.md) segítségével keresheti a frissített tartalmat.

Ha hozzáadta vagy átnevezte a mezőt, a [$Select](search-query-odata-select.md) használatával adja vissza a mezőt: `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Nagyméretű adathalmazok indexelése nagy méretekben](search-howto-large-index.md)
+ [Indexelés a portálon](search-import-data-portal.md)
+ [Azure SQL Database indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexelő](search-howto-indexing-azure-tables.md)
+ [Biztonság az Azure Cognitive Search](search-security-overview.md)