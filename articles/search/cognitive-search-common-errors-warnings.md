---
title: Indexelőkkel kapcsolatos hibák és figyelmeztetések
titleSuffix: Azure Cognitive Search
description: Ez a cikk olyan gyakori hibákkal és figyelmeztetésekkel kapcsolatos információkat és megoldásokat tartalmaz, amelyeket az AI az Azure Cognitive Search-ban való gyarapítása során felmerülhet.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 3ba0abe8510291351c10ba085ba7e42b8197d886
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553238"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Általános indexelő hibák és figyelmeztetések hibaelhárítása Az Azure Cognitive Search

Ez a cikk a gyakori hibákkal és figyelmeztetésekkel kapcsolatos információkat és figyelmeztetéseket tartalmaz, amelyeket az indexelés és az AI-bővítés során találkozhat az Azure Cognitive Searchban.

Az indexelés leáll, ha a hibák száma meghaladja a ["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Ha azt szeretné, hogy az indexelő figyelmen kívül hagyják ezeket a hibákat (és ugorjon a "sikertelen dokumentumok" elemre), akkor érdemes lehet frissíteni az `maxFailedItems` `maxFailedItemsPerBatch` [itt](/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)leírtakat.

> [!NOTE]
> Minden sikertelen dokumentum, valamint a dokumentum kulcsa (ha elérhető) az indexelő végrehajtási állapotában hibát jelez. Az [index API](/rest/api/searchservice/addupdate-or-delete-documents) használatával manuálisan is feltöltheti a dokumentumokat egy későbbi időpontban, ha úgy állította be az indexelő, hogy eltűri a hibákat.

A cikkben szereplő hibaüzenetek segíthetnek a hibák megoldásában, így az indexelés a folytatáshoz is használható.

A figyelmeztetések nem állíthatják le az indexelést, de olyan feltételeket jeleznek, amelyek váratlan eredményekhez vezethetnek. Függetlenül attól, hogy végrehajtja-e a műveletet, vagy nem függ az adatoktól és a forgatókönyvtől.

Az API-verziótól kezdődően az `2019-05-06` elemszintű indexelő hibái és a figyelmeztetések strukturálva vannak, így az okok és a következő lépések nagyobb átláthatóságot biztosítanak. A következő tulajdonságokat tartalmazzák:

| Tulajdonság | Leírás | Példa |
| --- | --- | --- |
| kulcs | A hibát vagy figyelmeztetést érintő dokumentum AZONOSÍTÓját. | https: \/ /coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | Annak a műveletnek a neve, amelyben a hiba vagy a figyelmeztetés történt. Ezt a következő struktúra hozza létre: [category]. [Alkategória]. [resourceType]. ResourceName | DocumentExtraction. azureblob. myBlobContainerName alkoholtartalom-növelés. WebApiSkill. mySkillName vetítés. SearchIndex. OutputFieldMapping. myOutputFieldName vetítés. SearchIndex. MergeOrUpload. myIndexName vetítés. KnowledgeStore. table. myTableName |
| message | A hiba vagy figyelmeztetés magas szintű leírása. | A képesség nem hajtható végre, mert a webes API-kérelem sikertelen volt. |
| Részletek | További részletek, amelyek hasznosak lehetnek a probléma diagnosztizálásához, például a WebApi-válaszhoz, ha az egyéni képességet nem sikerült végrehajtani. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 forrás, függvény `2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` ... fennmaradó verem nyomkövetése... |
| documentationLink | A kapcsolódó dokumentációra mutató hivatkozás, amely részletes információkat tartalmaz a hibakereséshez és a probléma megoldásához. Ez a hivatkozás gyakran a lap alábbi részeinek egyikére mutat. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>Hiba: a dokumentum nem olvasható

Az indexelő nem tudta olvasni a dokumentumot az adatforrásból. Ez az alábbiak miatt fordulhat elő:

| Ok | Részletek/példa | Feloldás |
| --- | --- | --- |
| Inkonzisztens mezők típusai különböző dokumentumokban | "Az érték típusa nem egyezik az oszlop típusával. Nem sikerült tárolni `'{47.6,-122.1}'` a szerzők oszlopban.  A várt típus: JArray. "  "Hiba történt az adattípusok nvarchar való konvertálásakor."  "A konverzió meghiúsult a (z)" 12 hónapos "nvarchar érték" int "adattípusra való konvertálásakor."  "Aritmetikai túlcsordulási hiba történt a kifejezés átalakítása int adattípusra." | Ügyeljen arra, hogy az egyes mezők típusa azonos legyen a különböző dokumentumok között. Ha például az első dokumentum `'startTime'` mező egy datetime típusú, a másodikban pedig egy karakterlánc, akkor a hiba a következő lesz:. |
| hibák az adatforrás mögöttes szolgáltatásában | (Cosmos DB) `{"Errors":["Request rate is large"]}` | Ellenőrizze, hogy a tárolási példány kifogástalan állapotú-e. Előfordulhat, hogy módosítania kell a skálázást/particionálást. |
| átmeneti problémák | Átviteli szintű hiba történt a kiszolgáló eredményeinek fogadása során. (szolgáltató: TCP-szolgáltató, hiba: 0 – a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Hiba: nem sikerült kinyerni a dokumentum tartalmát vagy metaadatait
A blob-adatforrással rendelkező indexelő nem tudta kinyerni a tartalmat vagy a metaadatokat a dokumentumból (például egy PDF-fájlt). Ez az alábbiak miatt fordulhat elő:

| Ok | Részletek/példa | Feloldás |
| --- | --- | --- |
| a blob mérete meghaladja a méretkorlátot | A dokumentum mérete `'150441598'` bájt, ami meghaladja az `'134217728'` aktuális szolgáltatási szinten a dokumentumok kinyeréséhez szükséges maximális bájtot. | [BLOB-indexelési hibák](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| a blob nem támogatott tartalomtípust tartalmaz | A dokumentum típusa nem támogatott. `'image/png'` | [BLOB-indexelési hibák](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| a blob titkosított | A dokumentumot nem lehetett feldolgozni – titkosított vagy jelszóval védett. | A blobot a blob- [beállításokkal](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)lehet kihagyni. |
| átmeneti problémák | "Hiba történt a blob feldolgozásakor: a kérés megszakadt: a kérést megszakították." "A dokumentum időkorlátja lejárt a feldolgozás során." | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>Hiba: a dokumentum nem elemezhető
Az indexelő beolvassa a dokumentumot az adatforrásból, de hiba történt a dokumentum tartalmának a megadott mező-hozzárendelési sémába való konvertálása során. Ez az alábbiak miatt fordulhat elő:

| Ok | Részletek/példa | Feloldás |
| --- | --- | --- |
| A dokumentum kulcsa hiányzik | A dokumentum kulcsa nem lehet hiányzik vagy üres. | Győződjön meg arról, hogy az összes dokumentum rendelkezik érvényes dokumentum-kulcsokkal. A dokumentum kulcsa úgy van meghatározva, hogy a "Key" tulajdonságot az [index definíciójának](/rest/api/searchservice/create-index#request-body)részeként állítja be. Az indexelő ezt a hibát fogja kibocsátani, ha a "kulcs" jelölésű tulajdonság nem található egy adott dokumentumban. |
| A dokumentum kulcsa érvénytelen | A dokumentum kulcsa nem lehet hosszabb 1024 karakternél | Módosítsa a dokumentum kulcsát az érvényesítési követelmények teljesítéséhez. |
| A mező leképezése nem alkalmazható egy mezőre | A leképezési függvény nem alkalmazható `'functionName'` a mezőre `'fieldName'` . A tömb nem lehet null. Paraméter neve: bájtok | Ellenőrizze az indexelő által definiált [mező-hozzárendeléseket](search-indexer-field-mappings.md) , és hasonlítsa össze a hibás dokumentum megadott mezőjének értékével. Előfordulhat, hogy módosítania kell a mező-hozzárendeléseket vagy a dokumentum-adattípust. |
| Nem olvasható be a mező értéke | Nem lehetett beolvasni az oszlop értékét az `'fieldName'` indexnél `'fieldIndex'` . Átviteli szintű hiba történt a kiszolgáló eredményeinek fogadása során. (szolgáltató: TCP-szolgáltató, hiba: 0 – a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat.) | Ezek a hibák általában az adatforrás mögöttes szolgáltatásával kapcsolatos váratlan kapcsolódási problémák miatt jelentkeznek. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>Hiba: nem sikerült leképezni a (z) "" kimeneti mezőt `xyz` a deszerializálási hiba miatti keresési indexhez a (z) "" leképezési funkció alkalmazása során `abc`
Lehetséges, hogy a kimeneti leképezés meghiúsult, mert a kimeneti adat formátuma nem megfelelő a használt leképezési függvényhez. Ez a hiba például a Base64Encode-leképezési függvény bináris adatértékre való alkalmazása esetén fog megjelenni. A probléma megoldásához futtassa újra az Indexelő szolgáltatást a leképezési funkció megadása nélkül, vagy győződjön meg arról, hogy a leképezési függvény kompatibilis a kimeneti mező adattípusával. Részletekért lásd a [kimeneti mezők leképezése](cognitive-search-output-field-mapping.md) című témakört.

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>Hiba: nem sikerült végrehajtani a képességet
Az indexelő nem tudott futtatni egy képességet a készségkészlet.

| Ok | Részletek/példa | Feloldás |
| --- | --- | --- |
| Átmeneti kapcsolódási problémák | Átmeneti hiba történt. Próbálkozzon újra később. | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |
| Lehetséges termék hibája | Váratlan hiba történt. | Ez ismeretlen osztályt jelez, és a termék meghibásodását is jelentheti. Kérjen segítséget a [támogatási jegyen](https://ms.portal.azure.com/#create/Microsoft.Support) . |
| A rendszer hibát észlelt a végrehajtás során | (Egyesítési képességből) Egy vagy több eltolási érték érvénytelen, és nem elemezhető. Elemek beszúrása a szöveg végére | A probléma megoldásához használja a hibaüzenetben található információkat. Ilyen hiba esetén a megoldáshoz beavatkozás szükséges. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Hiba: nem sikerült végrehajtani a képességet, mert a webes API-kérelem sikertelen volt.
A szaktudás végrehajtása nem sikerült, mert a webes API-hívás sikertelen volt. Ez az osztály általában egyéni szaktudás használata esetén fordul elő, ebben az esetben a probléma megoldásához hibakeresést kell végeznie az Egyéni kódban. Ha ehelyett a hiba egy beépített képességből származik, a probléma megoldásához a hibaüzenetben tájékozódhat.

A probléma hibakeresése során ügyeljen arra, hogy a szakértelemmel kapcsolatos összes [figyelmeztetést](#warning-skill-input-was-invalid) figyelni tudja. Előfordulhat, hogy a webes API-végpont meghiúsul, mert az indexelő váratlan bemenetet küld.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Hiba: a képesség nem hajtható végre, mert a webes API-ügyességi válasz érvénytelen.
A szaktudás végrehajtása nem sikerült, mert a webes API hívása érvénytelen választ adott vissza. Ez az osztály általában egyéni szaktudás használata esetén fordul elő, ebben az esetben a probléma megoldásához hibakeresést kell végeznie az Egyéni kódban. Ha ehelyett a hiba egy beépített képességből származik, akkor kérjen segítséget a [támogatási jegyen](https://ms.portal.azure.com/#create/Microsoft.Support) .

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Hiba: a szakértelem nem lett végrehajtva az időkorláton belül
Ez a hibaüzenet két esetben fordulhat elő, amelyek mindegyikét másképp kell kezelni. Kövesse az alábbi utasításokat attól függően, hogy milyen képességgel tért vissza ez a hiba.

### <a name="built-in-cognitive-service-skills"></a>Beépített kognitív szolgáltatásokkal kapcsolatos ismeretek
Számos beépített kognitív képesség, például a nyelvfelismerés, az entitások felismerése vagy az OCR, a kognitív szolgáltatás API-végpontja támogatja. Időnként átmeneti problémák léptek fel ezekkel a végpontokkal, és a kérés időtúllépést eredményez. Átmeneti problémák esetén nincs szükség jogorvoslatra, kivéve a várakozást, és próbálkozzon újra. Enyhítő megoldásként érdemes lehet úgy beállítani az indexelő, hogy [menetrend szerint fusson](search-howto-schedule-indexers.md). Az ütemezett indexelés megkeresi, hogy hol maradt. Az átmeneti problémák megoldása érdekében az indexelést és a kognitív képességek feldolgozását folytatni kell a következő ütemezett futtatáskor.

Ha továbbra is ezt a hibát látja egy beépített kognitív szakértelmet tartalmazó dokumentumon, egy [támogatási jegyet](https://ms.portal.azure.com/#create/Microsoft.Support) kell benyújtania a segítség kéréséhez, mivel ez nem várható.

### <a name="custom-skills"></a>Egyéni készségek
Ha az Ön által létrehozott egyéni szakértelem időtúllépési hibába ütközik, néhány dolog kipróbálható. Először tekintse át az egyéni szaktudást, és győződjön meg arról, hogy nem ragadt meg egy végtelen hurokban, és hogy az eredmény következetesen tér vissza. Miután meggyőződött róla, hogy a helyzet megtörtént, állapítsa meg, hogy milyen végrehajtási idő van a szakértelemben. Ha nem adott meg explicit módon `timeout` értéket az egyéni képzettségi definícióban, akkor az alapértelmezett érték `timeout` 30 másodperc. Ha 30 másodperc nem elég hosszú a szaktudás végrehajtásához, akkor magasabb értéket adhat meg `timeout` az egyéni szakértelem-definícióban. Íme egy példa egy egyéni képesség-definícióra, amelyben az időtúllépés 90 másodpercre van állítva:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

A paraméter számára beállítható maximális érték `timeout` 230 másodperc.  Ha az egyéni képesség nem hajtható végre következetesen 230 másodpercen belül, érdemes lehet csökkenteni az `batchSize` Egyéni képességet, hogy kevesebb dokumentum legyen feldolgozható egyetlen végrehajtáson belül.  Ha már beállította az `batchSize` 1 értéket, újra kell írnia a képességet, hogy 230 másodpercen belül el tudja végezni a végrehajtást, vagy más módon felosztja azt több egyéni képességre, hogy az egyetlen egyéni képesség végrehajtásának ideje legfeljebb 230 másodperc legyen. További információért tekintse át az [Egyéni szakértelem dokumentációját](cognitive-search-custom-skill-web-api.md) .

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Hiba: nem sikerült a (z) " `MergeOrUpload` " | " `Delete` " dokumentum a keresési indexhez

A dokumentum olvasása és feldolgozása megtörtént, de az indexelő nem tudja felvenni a keresési indexbe. Ez az alábbiak miatt fordulhat elő:

| Ok | Részletek/példa | Feloldás |
| --- | --- | --- |
| Egy mező túl nagy kifejezést tartalmaz | A dokumentum egy kifejezése nagyobb az [32 KB-os korlátnál](search-limits-quotas-capacity.md#api-request-limits) | Ezt a korlátozást elkerülheti, ha úgy látja, hogy a mező nem szűrhető, sokoldalú vagy rendezhető.
| A dokumentum túl nagy az indexeléshez | A dokumentum nagyobb, mint az [API-kérelmek maximális mérete](search-limits-quotas-capacity.md#api-request-limits) | [Nagyméretű adathalmazok indexelése](search-howto-large-index.md)
| A dokumentum túl sok objektumot tartalmaz a gyűjteményben | A dokumentum egy gyűjteménye meghaladja az [összes összetett gyűjtemény maximális elemeit](search-limits-quotas-capacity.md#index-limits) . a dokumentum, amelynek a kulcsa a `'1000052'` `'4303'` gyűjtemények (JSON-tömbök). A legtöbb `'3000'` objektum a teljes dokumentumban lévő gyűjteményekben szerepelhet. Távolítsa el az objektumokat a gyűjteményből, majd próbálja meg újból indexelni a dokumentumot. " | Javasoljuk, hogy csökkentse a dokumentum összetett gyűjteményének méretét a korlát alá, és elkerülje a nagy tárterület-kihasználtságot.
| Hiba történt a célként megadott indexhez való kapcsolódáskor (amely az újrapróbálkozások után is fennmarad), mert a szolgáltatás más terhelés alá esik, például lekérdezés vagy indexelés. | Nem sikerült kapcsolatot létesíteni az index frissítésével. A Search szolgáltatás nagy terhelés alatt áll. | [A keresési szolgáltatás vertikális felskálázása](search-capacity-planning.md)
| A keresési szolgáltatás javítás alatt áll a szolgáltatás frissítésére, vagy a topológia újrakonfigurálásának közepén található. | Nem sikerült kapcsolatot létesíteni az index frissítésével. A keresési szolgáltatás jelenleg nem érhető el, a keresési szolgáltatás pedig átmeneti állapotba kerül. | Állítsa be a szolgáltatást legalább 3 replikával az 99,9%-os rendelkezésre álláshoz az [SLA dokumentációjában](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Hiba történt az alapul szolgáló számítási/hálózati erőforrásban (ritka) | Nem sikerült kapcsolatot létesíteni az index frissítésével. Ismeretlen hiba történt. | Az indexelő úgy konfigurálható, hogy a sikertelen állapotból való kiválasztáshoz [ütemezett ütemtervet futtasson](search-howto-schedule-indexers.md) .
| A rendszer nem ismerte fel a célként megadott indexre vonatkozó indexelési kérelmet a hálózati problémák miatti időtúllépési időszakon belül. | Nem lehet időben kapcsolatot létesíteni a keresési indexszel. | Az indexelő úgy konfigurálható, hogy a sikertelen állapotból való kiválasztáshoz [ütemezett ütemtervet futtasson](search-howto-schedule-indexers.md) . Ha ez a hiba nem szűnik meg, csökkentse az indexelő [köteg méretének](/rest/api/searchservice/create-indexer#parameters) csökkentését.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Hiba: a dokumentum nem indexelhető, mert a dokumentum egyes adatértékei nem érvényesek.

A dokumentumot az indexelő elolvasta és feldolgozta, de az index mezőinek és az indexelő által feldolgozt adatnak a konfigurációjának eltérése miatt nem lehetett felvenni a keresési indexbe. Ez az alábbiak miatt fordulhat elő:

| Ok | Részletek/példa
| --- | ---
| Az indexelő által kinyert mező (k) adattípusa nem kompatibilis a megfelelő cél index mező adatmodelljével. | A (z) "888" kulccsal rendelkező dokumentumban levő "adatmező"_érvénytelen "EDM_. String" értékű. A várt típus: "Collection (EDM. String)". |
| Nem sikerült kibontani a JSON-entitásokat egy karakterlánc-értékből. | Nem sikerült elemezni a (z) ""_típusú ""_ EDM. String "" érték "MEZŐjét JSON-objektumként. Hiba: "az érték elemzése után a rendszer váratlan karaktert észlelt:" ". Elérési _út_ útvonala, 1. sor, pozíció: 3162. |
| Nem sikerült kibontani a JSON-entitások gyűjteményét egy karakterlánc-értékből.  | Nem sikerült értelmezni a (z) ""_típusú ""_ EDM. String "" érték "MEZŐjét JSON-tömbként. Hiba: "az érték elemzése után a rendszer váratlan karaktert észlelt:" ". Elérési út: "[0]", 1. sor, 27. pozíció |
| Ismeretlen típus lett felderítve a forrásbizonylat dokumentumban. | Ismeretlen típus (_ismeretlen_) nem indexelhető |
| A forrás dokumentumban nem kompatibilis jelölés szerepel a földrajzi pontokhoz. | A WKT pont karakterlánca nem támogatott. Ehelyett használjon GeoJson pont literálokat |

Ebben az esetben tekintse át a [támogatott adattípusokat](/rest/api/searchservice/supported-data-types) és [adattípus-leképezést az indexelő](/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) számára, és győződjön meg arról, hogy helyesen hozza létre az index-sémát, és beállította a megfelelő [Indexelő mező-hozzárendeléseket](search-indexer-field-mappings.md). A hibaüzenet olyan részleteket tartalmaz, amelyek segítségével nyomon követheti az eltérés forrását.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Hiba: az integrált módosítás-követési szabályzat nem használható, mert a tábla összetett elsődleges kulccsal rendelkezik.

Ez az SQL-táblákra vonatkozik, és általában akkor fordul elő, ha a kulcs vagy összetett kulcsként van definiálva, vagy ha a tábla egyedi fürtözött indexet definiált (például egy SQL-indexben, nem Azure Search index). A fő ok az, hogy a Key attribútum úgy módosul, hogy egy [egyedi fürtözött index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)esetében az összetett elsődleges kulcs legyen. Ebben az esetben győződjön meg arról, hogy az SQL-táblához nem tartozik egyedi fürtözött index, vagy ha a Key mezőt egy olyan mezőhöz rendeli, amely garantáltan nem rendelkezik ismétlődő értékekkel.

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Hiba: a dokumentum nem dolgozható fel az indexelő maximális futási idején belül

Ez a hiba akkor fordul elő, ha az indexelő nem tudja befejezni egyetlen dokumentum feldolgozását az adatforrásból az engedélyezett végrehajtási időn belül. A szakértelmével használata esetén a [maximális futási idő](search-limits-quotas-capacity.md#indexer-limits) rövidebb. Ha ez a hiba akkor fordul elő, ha a maxFailedItems értéke nem 0, az indexelő megkerüli a dokumentumot a jövőben, hogy az indexelés előrehaladást érjen el. Ha nem engedheti meg, hogy kihagyjon bármilyen dokumentumot, vagy ha konzisztensen látja ezt a hibát, érdemes lehet kisebb dokumentumokra feltörni a dokumentumokat, hogy a részleges előrehaladás egyetlen indexelő végrehajtáson belül is elvégezhető legyen.

<a Name = "nem-Project-Document></a>

## <a name="error-could-not-project-document"></a>Hiba: nem sikerült a Project Document

Ez a hiba akkor fordul elő, amikor az indexelő az [adatait egy tudásbázisba](knowledge-store-projection-overview.md) kísérli meg, és hiba történt a kísérlet során.  Ez a hiba lehet konzisztens és javítható, vagy előfordulhat, hogy átmeneti hiba történt a kivetítési kimeneti fogadónál, amelyet várnia kell, és újra kell próbálkoznia a feloldáshoz.  Az alábbiakban az ismert meghibásodási állapotok és a lehetséges megoldások halmaza látható.

| Ok | Részletek/példa | Feloldás |
| --- | --- | --- |
| Nem lehetett frissíteni a leképezési blobot `'blobUri'` a tárolóban. `'containerName'` |A megadott tároló nem létezik. | Az indexelő ellenőrzi, hogy a megadott tárolót korábban hozták-e létre, és szükség esetén létrehozza-e, de csak egyszer hajtja végre ezt az ellenőrzést az indexelő futtatásakor. Ez a hiba azt jelenti, hogy valami törölte a tárolót a lépés után.  A hiba elhárításához próbálja meg a következőt: ne csak a Storage-fiók adatait adja meg, várjon, amíg az indexelő befejeződik, majd futtassa újra az indexelő. |
| Nem lehetett frissíteni a leképezési blobot `'blobUri'` a tárolóban. `'containerName'` |Az adatátvitel nem írható a szállítási kapcsolatban: a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat. | Ez várhatóan átmeneti hiba az Azure Storage-ban, ezért az indexelő újbóli futtatásával kell megoldani. Ha konzisztensen Tapasztalja ezt a hibát, küldjön egy [támogatási jegyet](https://ms.portal.azure.com/#create/Microsoft.Support) , hogy később is megvizsgálható legyen.  |
| Nem lehetett frissíteni `'projectionRow'` a sort a táblában `'tableName'` | A kiszolgáló foglalt. | Ez várhatóan átmeneti hiba az Azure Storage-ban, ezért az indexelő újbóli futtatásával kell megoldani. Ha konzisztensen Tapasztalja ezt a hibát, küldjön egy [támogatási jegyet](https://ms.portal.azure.com/#create/Microsoft.Support) , hogy később is megvizsgálható legyen.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>Figyelmeztetés: a szakértelem bemenete érvénytelen volt.
A szakértelem bemenete hiányzik, nem megfelelő típusú, vagy más módon érvénytelen. A figyelmeztető üzenet a következő hatást fogja jelezni:
1) Nem hajtható végre a szaktudás
2) A végrehajtott szaktudás, de nem várt eredmények

A kognitív képességek kötelező bemenetekkel és opcionális bemenetekkel rendelkeznek. A [kinyerési képesség](cognitive-search-skill-keyphrases.md) például két kötelező bemenettel rendelkezik `text` , `languageCode` és nem választható bemenetek. Az egyéni szaktudás bemenetei mindegyike opcionális bemenetnek minősül.

Ha bármilyen szükséges bemenet hiányzik, vagy ha bármelyik bemenet nem megfelelő típusú, a rendszer kihagyja a szakértelmet, és figyelmeztetést generál. A kihagyott képességek nem eredményeznek kimeneteket, így ha más ismeretek is a kihagyott képességek kimenetét használják, további figyelmeztetések is készíthetők.

Ha egy opcionális bemenet hiányzik, a rendszer továbbra is futtatja a képességet, de a hiányzó bevitel miatt váratlan kimenetet eredményezhet.

Mindkét esetben ez a figyelmeztetés az adatszerkezet miatt várható. Ha például egy olyan dokumentummal rendelkezik, amely a mezőkkel rendelkező személyekre vonatkozó információkat tartalmaz, és olyan dokumentumokra is rendelkezhet, `firstName` `middleName` `lastName` amelyekhez nem tartozik bejegyzés `middleName` . Ha `middleName` a folyamat egy képességének bemenetként adja át az adatokat, akkor az várhatóan előfordulhat, hogy ez a képzettségi ráfordítás egy kis ideig hiányzik. Ki kell értékelnie az adatait és a forgatókönyvet annak meghatározásához, hogy szükséges-e valamilyen művelet a figyelmeztetés eredményeképpen.

Ha hiányzó bemenet esetén szeretné megadni az alapértelmezett értéket, a [feltételes képesség](cognitive-search-skill-conditional.md) használatával létrehozhat egy alapértelmezett értéket, majd használhatja a [feltételes képesség](cognitive-search-skill-conditional.md) kimenetét a szaktudás bemenetének megfelelően.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Ok | Részletek/példa | Feloldás |
| --- | --- | --- |
| A szakértelem bemenete nem megfelelő típusú. | "A szükséges képzettségi bevitel nem a várt típusú `String` . Név: `text` , forrás: `/document/merged_content` . "  "A szükséges szakértelem-bevitel nem a várt formátumú. Név: `text` , forrás: `/document/merged_content` . "  "Nem lehet megismételni a nem tömböt `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` ."  "Nem lehet kijelölni `0` a nem tömbben `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` " | Bizonyos készségek várhatóan adott típusok bemeneteit számítják fel, például a [hangulati képességek](cognitive-search-skill-sentiment.md) `text` egy sztringnek számítanak. Ha a bemenet nem sztring értéket ad meg, akkor a rendszer nem hajtja végre, és nem hoz létre kimenetet. Győződjön meg arról, hogy az adatkészletben a bemeneti értékek egységesek a típusban, vagy használjon [egyéni webes API-képességet](cognitive-search-custom-skill-web-api.md) a bemenet előfeldolgozásához. Ha a képességet egy tömbben ismétli meg, ellenőrizze a szakértelem kontextusát, és `*` a megfelelő pozícióban adja meg a bemenetet. A környezetnek és a bemeneti forrásnak általában a `*` tömbhöz kell végződnie. |
| Hiányzik a szaktudás bemenete | "Hiányzik a szükséges képzettségi bevitel. Név: `text` , forrás: `/document/merged_content` "" hiányzó érték `/document/normalized_images/0/imageTags` . "  "Nem lehet kijelölni `0` a hosszú tömbben `/document/pages` `0` ." | Ha az összes dokumentum ezt a figyelmeztetést kapja, valószínűleg van egy elírás a bemeneti elérési utakon, és az elérési úton ellenőrizze, hogy az elérési út tartalmazza-e az adott tulajdonság nevét, és hogy az `*` adatforrásból származó dokumentumok tartalmazzák-e a szükséges adatokat. |
| A képzettségi nyelvi kód bemenete érvénytelen. | A képzettségi bemenet `languageCode` a következő nyelvi kódokat tartalmazhatja `X,Y,Z` , amelyek közül legalább egy érvénytelen. | További [Részletek:](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Figyelmeztetés: a (z) "languageCode" képzettségi bemenethez a következő nyelvi kódok szerepelnek: "X, Y, Z", amelyek közül legalább az egyik érvénytelen.
Egy vagy több `languageCode` alsóbb rétegbeli képesség opcionális bemenetére átadott érték nem támogatott. Ez akkor fordulhat elő, ha a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) kimenetét átadja a következő szakismereteknek, a kimenet pedig több nyelvből áll, mint amennyit az adott alsóbb rétegbeli készségek támogatnak.

Vegye figyelembe, hogy ehhez hasonló figyelmeztetés jelenik meg, ha egy érvénytelen `countryHint` bemenet kerül át a LanguageDetectionSkill. Ha ez történik, ellenőrizze, hogy az adatforrásból az adott bemenethez használt mező érvényes ISO 3166-1 Alpha-2 2 betűs országkódot tartalmaz-e. Ha egyes értékek érvényesek, és néhány érvénytelen, folytassa a következő útmutatással, de cserélje le a `languageCode` `countryHint` és `defaultLanguageCode` a with `defaultCountryHint` kifejezésre a használati esetnek megfelelően.

Ha tudja, hogy az adatkészlet egy adott nyelven van, távolítsa el a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) és a `languageCode` szaktudás bemenetét, és használja `defaultLanguageCode` inkább a skill paramétert, feltéve, hogy az adott szakértelmet támogatja a nyelv.

Ha tudja, hogy az adatkészlet több nyelvet tartalmaz, és így szüksége van a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) és a `languageCode` bemenetre, vegyen fel egy [ConditionalSkill](cognitive-search-skill-conditional.md) , hogy a szöveget olyan nyelvekkel szűrje, amelyek nem támogatottak a szövegben az alárendelt képességbe való átadás előtt.  Íme egy példa arra, hogy ez hogyan néz ki a EntityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Íme néhány hivatkozás a jelenleg támogatott nyelvekhez minden olyan szaktudáshoz, amely a következő hibaüzenetet eredményezheti:
* [Text Analytics támogatott nyelvek](../cognitive-services/text-analytics/language-support.md) (a [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), a [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), a [SentimentSkill](cognitive-search-skill-sentiment.md)és a [PIIDetectionSkill](cognitive-search-skill-pii-detection.md)esetében)
* [Fordító által támogatott nyelvek](../cognitive-services/translator/language-support.md) (a [szöveg TranslationSkill](cognitive-search-skill-text-translation.md))
* [Szöveges SplitSkill](cognitive-search-skill-textsplit.md) Támogatott nyelvek: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>Figyelmeztetés: a szaktudás bemenete csonkolt
A kognitív képességek az egyszerre elemezhető szöveg hosszát korlátozzák. Ha ezen képességek szövegének bevitele meghaladja ezt a korlátot, a rendszer csonkolja a szöveget, hogy megfeleljen a határértéknek, majd végrehajtja a dúsítást a csonkolt szövegen. Ez azt jelenti, hogy a rendszer végrehajtja a képességet, de az összes adatát nem.

Az alábbi példában szereplő LanguageDetectionSkill a `'text'` beviteli mező kiválthatja ezt a figyelmeztetést, ha az meghaladja a karakteres korlátot. A képzettséggel kapcsolatos [dokumentációban](cognitive-search-predefined-skills.md)megtalálja a képességek bemeneti korlátait.

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Ha biztosítani szeretné, hogy az összes szöveg elemzése megtörténjen, érdemes lehet a [felosztott képességet](cognitive-search-skill-textsplit.md)használni.

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Figyelmeztetés: a webes API skill-válasza figyelmeztetéseket tartalmaz
Az indexelő egy képességet tudott futtatni a készségkészlet, de a webes API-kérelemtől kapott válasz a végrehajtás során figyelmeztetéseket jelzett. Tekintse át a figyelmeztetéseket, és Ismerje meg, hogy milyen hatással van az adatai, és hogy van-e szükség beavatkozásra.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Figyelmeztetés: az indexelő jelenlegi konfigurációja nem támogatja a növekményes előrehaladást

Ez a figyelmeztetés csak Cosmos DB adatforrások esetén fordul elő.

Az indexelés során fellépő növekményes előrehaladás biztosítja, hogy ha az indexelő végrehajtását átmeneti hibák vagy végrehajtási időkorlát miatt megszakítja, az indexelő elvégezheti a következő futása után, hogy a teljes gyűjteményt ne kelljen újból indexelni. Ez különösen fontos a nagyméretű gyűjtemények indexelése során.

A befejezetlen indexelési feladatok folytatásának lehetősége úgy van kiértékelve, hogy az oszlop alapján rendezve legyenek a dokumentumok `_ts` . Az indexelő az időbélyeg használatával határozza meg, hogy melyik dokumentumot kell felvenni a következőre. Ha az `_ts` oszlop hiányzik, vagy ha az indexelő nem tudja megállapítani, hogy egy egyéni lekérdezés van-e rendezve, az indexelő elindul, és ez a figyelmeztetés jelenik meg.

Ennek a viselkedésnek a felülbírálása, a növekményes előrehaladás engedélyezése és a figyelmeztetés letiltása a `assumeOrderByHighWatermarkColumn` konfigurációs tulajdonság használatával.

További információ: [növekményes folyamat és egyéni lekérdezések](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Figyelmeztetés: a kivetítés során egyes adatvesztések elvesznek. A (Z) "Y" tábla "X" sora túl hosszú karakterlánc-tulajdonságot tartalmaz.

A [Table Storage szolgáltatás](https://azure.microsoft.com/services/storage/tables) korlátozásokkal rendelkezik, hogy az [entitások mekkora tulajdonságai](/rest/api/storageservices/understanding-the-table-service-data-model#property-types) lehetnek. A karakterláncok legfeljebb 32 000 karakterből állhatnak. Ha az 32 000 karakternél hosszabb karakterlánc-tulajdonságú sorok vannak kialakítva, csak az első 32 000 karaktert őrzi meg a rendszer. A probléma megkerüléséhez Kerülje a sorok kivetítését 32 000 karakternél hosszabb karakterlánc-tulajdonságokkal.

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Figyelmeztetés: a kinyert szöveg X karakterre csonkítva
Az indexek megszabják, hogy az egyes dokumentumokból milyen mennyiségű szöveget lehet kinyerni. Ez a korlát a következő árképzési szinttől függ 32 000: az ingyenes szint, a 64 000 az alapszintű, a 4 000 000 a standard, a 8 000 000 a standard S2 esetében, a standard S3 pedig a 16 000 000. A csonkolt szöveg nem lesz indexelve. A figyelmeztetés elkerüléséhez próbálkozzon szét a dokumentumok nagy mennyiségű szöveggel több, kisebb dokumentumba való bontásával. 

További információ: [Indexelő korlátai](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Figyelmeztetés: a (z) "X" kimeneti mező nem képezhető le a keresési indexre
A nem létező/null értékű adatokat hivatkozó kimeneti mezők leképezései minden dokumentumhoz figyelmeztetést hoznak létre, és üres index mezőt eredményeznek. A probléma megoldásához ellenőrizze a kimeneti mező leképezési forrásának elérési útját a lehetséges elírásokhoz, vagy állítson be egy alapértelmezett értéket a [feltételes képesség](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)használatával. Részletekért lásd a [kimeneti mezők leképezése](cognitive-search-output-field-mapping.md) című témakört.

| Ok | Részletek/példa | Feloldás |
| --- | --- | --- |
| Nem lehet megismételni a nem tömböt | "Nem lehet megismételni a nem tömböt `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` ." | Ez a hiba akkor fordul elő, ha a kimenet nem tömb. Ha úgy gondolja, hogy a kimenetnek tömbnek kell lennie, ellenőrizze a kimeneti Forrás mezőben megadott elérési utat a hibákhoz. Előfordulhat például, hogy egy hiányzó vagy egy extra `*` szerepel a forrás mező nevében. Az is lehetséges, hogy az ehhez a képességhez megadott bemenet null értékű, ami üres tömböt eredményez. A [szakértelem bemenetének](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) hasonló részleteit a szakasz érvénytelen szakaszban találta.    |
| Nem lehet kiválasztani `0` a nem tömbben | "Nem lehet kijelölni `0` a nem tömbben `/document/pages` " lehetőséget. | Ez akkor fordulhat elő, ha a szaktudás kimenete nem állít elő tömböt, és a kimeneti forrás mező neve tömb indextel vagy `*` annak elérési útjával rendelkezik. Ellenőrizze a kimeneti forrás mezőiben megadott elérési utakat és a mező értékét a megadott mezőnév alapján. A [szakértelem bemenetének](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) hasonló részleteit a szakasz érvénytelen szakaszban találta.  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Figyelmeztetés: az adatváltozás-észlelési házirend az "X" kulcs oszlop használatára van konfigurálva.
Az [adatváltozás-észlelési szabályzatok](/rest/api/searchservice/create-data-source#data-change-detection-policies) konkrét követelményekkel rendelkeznek a változás észleléséhez használt oszlopokra vonatkozóan. Ezen követelmények egyike az, hogy ez az oszlop minden alkalommal frissül, amikor módosul a forrás eleme. Egy másik követelmény, hogy az oszlop új értéke nagyobb, mint az előző érték. A kulcs oszlopai nem felelnek meg ennek a követelménynek, mert nem változnak minden frissítésnél. A probléma megkerüléséhez válasszon egy másik oszlopot a Change észlelési házirendhez.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Figyelmeztetés: úgy tűnik, hogy a dokumentum szövege UTF-16 kódolású, de hiányzik egy byte Order jel.

Az [Indexelő elemzési módjának](/rest/api/searchservice/create-indexer#blob-configuration-parameters) tudnia kell, hogy a szöveg hogyan legyen kódolva az elemzés előtt. A kódolási szövegek két leggyakoribb módja az UTF-16 és az UTF-8. Az UTF-8 egy változó hosszúságú kódolás, amelyben minden karakter 1 bájt és 4 bájt hosszú lehet. Az UTF-16 egy rögzített hosszúságú kódolás, amelyben minden karakter 2 bájt hosszúságú. Az UTF-16 két különböző változatot tartalmaz: "big endian" és "Little endian". A szöveges kódolást egy "byte Order Mark", egy bájtos sorozat határozza meg a szöveg előtt.

| Encoding | Byte Order Mark |
| --- | --- |
| UTF-16 big endian | 0xFE 0xFF |
| UTF-16 kis endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Ha nincs megadva a bájtok sorrendje, a rendszer az UTF-8 kódolású szöveget feltételezi.

A figyelmeztetés megkerüléséhez határozza meg, hogy mi a blob szöveges kódolása, és adja hozzá a megfelelő bájtos megrendelési jelet.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Figyelmeztetés: Cosmos DB "X" gyűjteménynek van egy lusta indexelési házirendje. Egyes adatvesztések elveszhetnek

A [lusta](../cosmos-db/index-policy.md#indexing-mode) indexelési házirendekkel rendelkező gyűjteményeket nem lehet következetesen lekérdezni, így az indexelő adatai hiányoznak. A figyelmeztetés megkerüléséhez módosítsa az indexelési házirendet Konzisztensre.

## <a name="warning-the-document-contains-very-long-words-longer-than-64-characters-these-words-may-result-in-truncated-andor-unreliable-model-predictions"></a>Figyelmeztetés: a dokumentum nagyon hosszú szavakat tartalmaz (64 karakternél hosszabb). Ezek a szavak csonkolt és/vagy megbízhatatlan modellek előrejelzését eredményezhetnek.

Ezt a figyelmeztetést a Text Analytics szolgáltatás adja át.  Bizonyos esetekben nyugodtan figyelmen kívül hagyhatja ezt a figyelmeztetést, például ha a dokumentum hosszú URL-címet tartalmaz (ami valószínűleg nem kulcsfontosságú kifejezés vagy a vezetési hangulat stb.).  Ügyeljen arra, hogy ha egy szó 64 karakternél hosszabb, akkor a rendszer az 64 karakterre csonkolja a modell előrejelzéseit.