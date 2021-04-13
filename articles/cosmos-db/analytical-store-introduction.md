---
title: Mi az az Azure Cosmos DB elemzési tár?
description: A Azure Cosmos DB (soralapú) és analitikai (oszlopalapú) tárolók megismerása. Az elemzési tár előnyei, a nagy léptékű számítási feladatok teljesítményre gyakorolt hatása, valamint a tranzakciós tárolóból az elemzési tárba való automatikus adatszinkronizálás
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: eaabc663ba243423bddf7ef6abfe41182e06b4f9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364606"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Mi az Azure Cosmos DB elemzési tároló?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB elemzési adattár egy teljesen elkülönített oszloptároló, amely lehetővé teszi a nagy léptékű elemzéseket a felhőbeli operatív Azure Cosmos DB anélkül, hogy ez hatással lenne a tranzakciós számítási feladatokra. 

Azure Cosmos DB tranzakciós tároló sématől független, és lehetővé teszi a tranzakciós alkalmazások iterálását séma- vagy indexkezelés nélkül. Ezzel szemben a Azure Cosmos DB elemzési tároló az elemzési lekérdezési teljesítményre való optimalizálás érdekében van sémában. Ez a cikk az elemzési tárhellyel kapcsolatos részletes információkat ismerteti.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Az operatív adatok nagy léptékű elemzésének kihívásai

A többmodelles üzemeltetési adatok egy Azure Cosmos DB tárolóban egy indexelt soralapú "tranzakciós tárolóban" tárolódnak. A sortároló formátuma lehetővé teszi a gyors tranzakciós olvasásokat és írásokat az ezredmásodpercekben megrendelt válaszidőkben, valamint a műveleti lekérdezéseket. Ha az adatkészlet mérete megnő, az összetett elemzési lekérdezések költségesek lehetnek az ebben a formátumban tárolt adatokhoz kiépített átviteli sebesség tekintetében. A kiépített átviteli sebesség magas használata hatással van a valós idejű alkalmazások és szolgáltatások által használt tranzakciós számítási feladatok teljesítményére.

A nagy mennyiségű adat elemzéséhez hagyományosan az operatív adatokat Azure Cosmos DB a tranzakciós tárolóból, és egy külön adatrétegben tárolják. Az adatokat például egy adattárházban vagy data lake-ben tárolják egy megfelelő formátumban. Ezeket az adatokat később nagy léptékű elemzésekhez, majd számítási motorral, például az adatfürtök Apache Spark elemezni. Az elemzési tárterület és a számítási rétegek operatív adatoktól való elkülönítése további késést eredményezhet, mivel az ETL-folyamatok (kinyerés, átalakítás, betöltés) ritkábban futnak a tranzakciós számítási feladatokra gyakorolt potenciális hatás minimalizálása érdekében.

Az ETL-folyamatok az operatív adatok frissítésének kezelésekor is összetetté válnak, szemben a csak az újonnan betöltött operatív adatok kezelésével. 

## <a name="column-oriented-analytical-store"></a>Oszloporientált elemzési tároló

Azure Cosmos DB elemzési tároló a hagyományos ETL-folyamatok összetettségi és késési kihívásaival foglalkozik. Azure Cosmos DB elemzési tár képes automatikusan szinkronizálni az operatív adatokat egy külön oszloptárolóba. Az oszloptároló formátuma megfelelő a nagy léptékű elemzési lekérdezések optimalizált módon való hajtható végre, ami javítja az ilyen lekérdezések késését.

A Azure Synapse Link használatával mostantól ETL-t nem használó HTAP-megoldásokat építhet ki, ha közvetlenül hivatkozik egy Azure Cosmos DB elemzési Azure Synapse Analytics. Segítségével közel valós idejű, nagy léptékű elemzéseket futtathat az operatív adatokon.

## <a name="features-of-analytical-store"></a>Az elemzésitár funkciói 

Ha engedélyezi az elemzési tárolót egy Azure Cosmos DB tárolón, a rendszer belsőleg létrehoz egy új oszloptárolót a tárolóban lévő működési adatok alapján. Ez az oszloptároló az ehhez a tárolóhoz kapcsolódó sororientált tranzakciós tárolótól elkülönítve lesz megőrzve. Az operatív adatok beszúrása, frissítése és törlése automatikusan szinkronizálódik az elemzési tárba. Az adatok szinkronizálása nem szükséges a változáscsatornához vagy az ETL-hez.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Oszloptár az üzemeltetési adatokon futtatott elemzési számítási feladatokhoz

Az elemzési számítási feladatok általában aggregációkat és a kiválasztott mezők egymást követő vizsgálatait foglalják magukban. Az adatok oszlopos sorrendben való tárolásával az elemzési tároló lehetővé teszi az egyes mezők értékeinek egy csoportjának együttes szerializálását. Ez a formátum csökkenti az adott mezők statisztikáinak vizsgálatához vagy kiszámításához szükséges IOPS-t. Ez jelentősen javítja a nagy adatkészletek vizsgálatának lekérdezési válaszideit. 

Ha például az operatív táblák formátuma a következő:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Példa operatív táblára" border="false":::

A sortároló a fenti adatokat sorbanként szerializált formában tárolja a lemezen. Ez a formátum gyorsabb tranzakciós olvasásokat, írásokat és műveleti lekérdezéseket tesz lehetővé, például a "Return information about Product1" (A Product1 termékre vonatkozó információk visszaküldése) lehetőséget. Ha azonban az adatkészlet mérete nagy, és összetett elemzési lekérdezéseket szeretne futtatni az adatokon, az költséges lehet. Ha például a "Berendezések" kategóriába tartozó termék értékesítési trendjeit szeretné lekérdezni különböző üzleti egységekben és hónapokban, egy összetett lekérdezést kell futtatnia. Az adatkészlet nagy méretű átvizsgálása költséges lehet a kiépített átviteli sebesség szempontjából, és hatással lehet a valós idejű alkalmazásokat és szolgáltatásokat szolgáltató tranzakciós számítási feladatok teljesítményére is.

Az elemzésitár, amely egy oszloptároló, jobban megfelel az ilyen lekérdezésekhez, mert hasonló adatmezőket szerializál, és csökkenti a lemez IOPS-ét.

Az alábbi képen a tranzakcióssor-tároló és az elemzési oszloptároló látható a Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Tranzakciós sortároló vs elemzési oszloptároló a Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Leválasztott teljesítmény elemzési számítási feladatokhoz

Az elemzési lekérdezések nem befolyásolják a tranzakciós számítási feladatok teljesítményét, mivel az elemzési tároló elkülönül a tranzakciós tárolótól.  Az elemzési tárolóhoz nem szükséges külön kérelemegységet (RU-t) lefoglalni.

### <a name="auto-sync"></a>Automatikus szinkronizálás

Az automatikus szinkronizálás a Azure Cosmos DB teljes körűen felügyelt képességére utal, ahol a rendszer a beszúrásokat, a frissítéseket és az operatív adatok törlését automatikusan szinkronizálja a tranzakciós tárolóból az elemzési tárba közel valós időben. Az automatikus szinkronizálás késése általában 2 percen belül meg fog csökkenni. Nagy számú tárolóval megosztott átviteli sebességet biztosító adatbázisok esetén az egyes tárolók automatikus szinkronizálásának késése magasabb lehet, és akár 5 percet is igénybe vehet. Szeretnénk többet megtudni arról, hogyan illeszkedik ez a késés az Ön forgatókönyveibe. Ennek érdekében forduljon a Azure Cosmos DB [csapathoz.](mailto:cosmosdbsynapselink@microsoft.com)

Az automatikus szinkronizálás és az elemzési tár a következő fő előnyöket biztosítja:

### <a name="scalability--elasticity"></a>Méretezhetőség és & rugalmasság

A horizontális particionálás használatával a Azure Cosmos DB tárolók állásidő nélkül, rugalmasan méretezheti a tárterületet és az átviteli sebességet. A tranzakciós tároló horizontális particionálásának skálázhatósága & az automatikus szinkronizálás rugalmasságát, így közel valós időben szinkronizálhatja az adatokat az elemzési tárba. Az adatszinkronizálás a tranzakciós forgalom átviteli sebességétől függetlenül megtörténik, függetlenül attól, hogy 1000 művelet/mp vagy 1 millió művelet/mp, és ez nem befolyásolja a tranzakciós tárolóban kiépített átviteli sebességet. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Sémafrissítések automatikus kezeléséhez

Azure Cosmos DB tranzakciós tároló sématől független, és lehetővé teszi a tranzakciós alkalmazások iterálását séma- vagy indexkezelés nélkül. Ezzel szemben a Azure Cosmos DB elemzési tároló az elemzési lekérdezési teljesítményre való optimalizálás érdekében van sémában. Az automatikus szinkronizálási funkcióval a Azure Cosmos DB felügyeli a séma következtetését a tranzakciós tároló legújabb frissítései alapján.  Emellett az elemzési tároló sémarezentációját is kezeli használatra, beleértve a beágyazott adattípusok kezelését is.

Ahogy a séma fejlődik, és idővel új tulajdonságokat ad hozzá, az elemzési tároló automatikusan egy uniós sémát hoz létre a tranzakciós tároló összes előzménysémában.

#### <a name="schema-constraints"></a>Sémakorlátozások

Ha engedélyezi az elemzési tároló számára, hogy automatikusan kivezénylje és megfelelően reprezentálja a sémát, a következő korlátozások vonatkoznak a Azure Cosmos DB adatokra:

* A séma beágyazási szintjén legfeljebb 1000 tulajdonság és legfeljebb 127 beágyazási mélység lehet.
  * Az elemzési tároló csak az első 1000 tulajdonságot ábrázolja.
  * Az elemzési tárolóban csak az első 127 beágyazott szint lesz ábrázolva.

* Bár a JSON-dokumentumok (Cosmos DB gyűjtemények/tárolók) megkülönböztetik a kis- és nagybetűket az egyediség szempontjából, az elemzési tároló nem.

  * **Ugyanabban a dokumentumban:** Az azonos szinten található tulajdonságneveknek egyedinek kell lennie, ha a kis- és a kis- és a nagy A következő JSON-dokumentumban például a "Név" és a "név" azonos szinten van. Bár ez egy érvényes JSON-dokumentum, nem felel meg az egyediség korlátozásának, ezért nem lesz teljes egészében ábrázolva az elemzési tárolóban. Ebben a példában a "Név" és a "név" ugyanaz, mint a kis- és a nagy- és a kis- és a nagy- és kis- és nagy között. Csak az elemzési tárolóban lesz `"Name": "fred"` ábrázolva, mert ez az első előfordulás. A `"name": "john"` és a nem is lesz ábrázolva.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **Különböző dokumentumokban:** Az azonos szintű és azonos nevű tulajdonságok, de különböző esetekben ugyanazon az oszlopon belül lesznek ábrázolva az első előfordulás névformátumának használatával. Az alábbi JSON-dokumentumokban például a `"Name"` és `"name"` a azonos szintű. Mivel az első dokumentumformátum , ezt fogja használni a tulajdonság nevének ábrázolása az `"Name"` elemzési tárolóban. Más szóval az elemzésitárban az oszlop neve `"Name"` lesz. Az `"fred"` `"john"` oszlopban a és a is lesz `"Name"` ábrázolva.


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* A gyűjtemény első dokumentuma a kezdeti elemzésitár-sémát határozza meg.
  * A dokumentum első szintjén lévő tulajdonságok oszlopokként fognak ábrázolni.
  * A kezdeti sémánál több tulajdonsággal kapcsolatos dokumentumok új oszlopokat hoznak létre az elemzési tárolóban.
  * Az oszlopok nem távolíthatók el.
  * A gyűjteményben található összes dokumentum törlése nem állítja alaphelyzetbe az elemzésitár-sémát.
  * A séma verziószámozása nem stb. A tranzakciós tárolóból kikövetkeztetett utolsó verzió az elemzési tárolóban látható.

* Jelenleg nem támogatjuk az üres Azure Synapse (szóközöket) tartalmazó Spark-oszlopok olvasását.

* Eltérő viselkedést várnak el az explicit értékek `null` tekintetében:
  * A Spark-Azure Synapse ezeket az értékeket `0` (nulla) jelölik.
  * Az sql serverless pools in Azure Synapse úgy olvassa be ezeket az értékeket, mintha a gyűjtemény első dokumentuma ugyanennél a tulajdonságnál egy `NULL` `non-numeric` adattípusú értéket tartalmaz.
  * Az sql serverless pools in Azure Synapse (nulla) értékként olvassa be ezeket az értékeket, ha a gyűjtemény első dokumentumában ugyanennek a tulajdonságnak van egy `0` `numeric` adattípusú értéke.

* A hiányzó oszlopok esetében eltérő viselkedés várható:
  * A spark-Azure Synapse ezeket az oszlopokat a következőként `undefined` képviselik: .
  * A kiszolgáló nélküli SQL-készletek Azure Synapse ezeket az oszlopokat a következőként képviselik: `NULL` .

#### <a name="schema-representation"></a>Sémaábrázolás

Az elemzési tárban két módon lehet ábrázolni a sémákat. Mindkettő kompromisszumos megoldást jelent az oszlopos ábrázolás egyszerűsége, a polimorf sémák kezelése és a lekérdezés egyszerűsége szempontjából:

* Jól definiált sémarezentáció
* Teljes hűségű sémaredrezentáció

> [!NOTE]
> Az SQL (Core) API-fiókok esetében, ha az elemzési tároló engedélyezve van, az elemzési tároló alapértelmezett sémarezentációja jól definiálva van. Míg a MongoDB-fiókokhoz Azure Cosmos DB API esetében az elemzési tároló alapértelmezett sémarezentációja egy teljes körű sémarerezentáció. Ha az egyes API-k alapértelmezett ajánlataitól eltérő sémarerezentációra [](mailto:cosmosdbsynapselink@microsoft.com) van szükség, akkor az engedélyezéshez Azure Cosmos DB a csapathoz.

**Jól definiált sémarezentáció**

A jól definiált sémareplikáció a sématől független adatok egyszerű táblázatos ábrázolása a tranzakciós tárolóban. A séma jól meghatározott reprezentációja a következő szempontokat tartalmazza:

* Egy tulajdonságnak mindig ugyanaz a típusa több elem között.
* Csak 1 típusváltást engedélyezünk, null értékről bármely más adattípusra. Az első nem null előfordulás határozza meg az oszlop adattípusát.

  * A például nem rendelkezik jól meghatározott sémával, mert `{"a":123} {"a": "str"}` `"a"` néha sztring, néha szám. Ebben az esetben az elemzési tároló a adattípusát regisztrálja adattípusként a tároló élettartamának első alkalommal előforduló `"a"` `“a”` elemében. A dokumentum továbbra is szerepelni fog az elemzési tárolóban, de azok az elemek, amelyek adattípusa `"a"` eltér, nem.
  
    Ez a feltétel nem vonatkozik a null értékű tulajdonságokra. A például `{"a":123} {"a":null}` még mindig jól definiálva van.

* A tömbtípusoknak egyetlen ismétlődő típust kell tartalmaznia.

  * A például nem jól meghatározott séma, mert a tömb egész számokból és sztringtípusokból `{"a": ["str",12]}` áll.

> [!NOTE]
> Ha a Azure Cosmos DB adattár követi a jól meghatározott sémarezentációt, és a fenti specifikációt egyes elemek megsértik, akkor ezek az elemek nem fognak szerepelni az elemzési tárolóban.

* A jól meghatározott sémák különböző típusainak különböző viselkedését várja el:
  * A spark-Azure Synapse ezeket az értékeket a következőként fogják `undefined` ábrázolni: .
  * A kiszolgáló nélküli SQL-Azure Synapse ezeket az értékeket a következőként fogják ábrázolni: `NULL` .


**Teljes hűségű sémarezentáció**

A teljes fidelitási séma-ábrázolás úgy lett kialakítva, hogy a sémafüggetlen működési adatokban a polimorf sémák teljes egészét kezelni tudja. Ebben a sémarezentációban a rendszer akkor sem dob el elemeket az elemzési tárolóból, ha megsérti a jól meghatározott sémakorlátozásokat (amelyek nem vegyes adattípusú mezők vagy vegyes adattípusú tömbök).

Ez úgy érhető el, hogy az operatív adatok levéltulajdonságait az elemzési tárolóba fordítja le különböző oszlopokkal a tulajdonság értékeinek adattípusa alapján. A levéltulajdonságok nevei az elemzésitár-séma utótagjaként az adattípusokkal bővülnek, így kétértelmű lekérdezések is lehetnek.

Vegyük például a következő mintadokumentumot a tranzakciós tárolóban:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

A beágyazott objektum levéltulajdonságát az elemzésitár-sémában `streetNo` `address` oszlopként kell `address.object.streetNo.int32` ábrázolni. Az adattípus utótagként lesz hozzáadva az oszlophoz. Így ha egy másik dokumentumot adnak a tranzakciós tárolóhoz, ahol a levéltulajdonság értéke "123" (figyelje meg, hogy sztring), az elemzési tároló sémája automatikusan fejlődik anélkül, hogy módosítanának egy korábban megírt oszlop `streetNo` típusát. Az elemzési tárolóhoz hozzáadott új oszlop, ahol az `address.object.streetNo.string` "123" érték van tárolva.

**Adattípus az utótag leképezéséhez**

Az alábbi térkép az elemzési tárolóban található összes tulajdonságadattípust és azok utótag-ábrázolásait ábrázolja:

|Eredeti adattípus  |Utótag  |Példa  |
|---------|---------|---------|
| Dupla |  ".float64" |    24.99|
| Tömb | ".array" |    ["a", "b"]|
|Bináris | ".binary" |0|
|Logikai    | ".bool"   |Igaz|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Null   | ".null"   | null|
|Sztring|    ".string" | „ABC”|
|Időbélyeg |    ".timestamp" |  Időbélyeg(0, 0)|
|DateTime   |".date"    | ISODate("2020-08-21T07:43:07.375Z")|
|ObjectId   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|Dokumentum   |".object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>Előzményadatok költséghatékony archiválása

Az adatrétegezés az adatok elkülönítését jelenti a különböző forgatókönyvekhez optimalizált tárolási infrastruktúrák között. Ezáltal javul a teljes adatsor általános teljesítménye és költséghatékonysága. Az elemzési táras Azure Cosmos DB mostantól támogatja az adatok automatikus rétegezését a tranzakciós tárolóból a különböző adatelrendezésű elemzési tárolókba. A tranzakciós tárhoz képest a tárolási költségek szempontjából optimalizált elemzési tár lehetővé teszi a működési adatok sokkal hosszabb horizontának megőrzését az előzményelemzéshez.

Miután engedélyezte az elemzési tárolót, a tranzakciós számítási feladatok adatmegőrzési igényei alapján konfigurálhatja a Tranzakciós tárolói idő (Tranzakciós TTL) tulajdonságot úgy, hogy egy bizonyos idő után automatikusan törölve legyen a rekordok a tranzakciós tárolóból. Hasonlóképpen, az elemzésitár élettartamának (elemzési élettartam) lehetővé teszi az elemzési tárolóban megőrzött adatok életciklusának kezelését a tranzakciós tárolótól függetlenül. Az elemzési tároló engedélyezésével és az TTL-tulajdonságok konfigurálásával zökkenőmentesen rétegezést és adatmegőrzési időszakot határozhat meg a két tárolóhoz.

### <a name="global-distribution"></a>Globális terjesztés

Ha globálisan elosztott fiókkal Azure Cosmos DB, miután engedélyezi az elemzési tárolót egy tárolóhoz, az a fiók minden régiójában elérhető lesz.  Az operatív adatok módosításai globálisan replikálva vannak az összes régióban. Az elemzési lekérdezéseket hatékonyan futtathatja az adatok legközelebbi regionális példányán a Azure Cosmos DB.

### <a name="security"></a>Biztonság

Az elemzési tárolóval való hitelesítés ugyanaz, mint egy adott adatbázis tranzakciós tárolója. A hitelesítéshez elsődleges vagy csak olvasható kulcsokat használhat. A Spark-jegyzetfüzetek Synapse Studio szolgáltatás használatával megakadályozhatja a Azure Cosmos DB-kulcsok beillesztését. A linked service-hez való hozzáférés mindenki számára elérhető, aki hozzáféréssel rendelkezik a munkaterülethez.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Több Azure Synapse Analytics támogatása

Az elemzési tároló úgy van optimalizálva, hogy skálázhatóságot, rugalmasságot és teljesítményt nyújtson az elemzési számítási feladatokhoz anélkül, hogy a számítási feladatok futási időtől függenének. A tárolási technológia önálló kezelése révén manuális munka nélkül optimalizálhatja az elemzési számítási feladatokat.

Ha leválasztja az elemzési tárrendszert az elemzési számítási rendszerről, az Azure Cosmos DB elemzési tárban található adatok lekérdezhetőek egyidejűleg a Azure Synapse Analytics. A mai naptól a Azure Synapse Analytics támogatja Apache Spark sql-készleteket és Azure Cosmos DB elemzési tárolóval.

> [!NOTE]
> Az elemzésitárból csak a futási idő Azure Synapse Analytics olvashat. Az adatokat kiszolgáló rétegként visszaírhatja a tranzakciós tárolóba.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Árképzés

Az elemzési tára egy használatalapú díjszabási modellt követ, amelyben a következőkért kell fizetnie:

* Tárolás: az elemzési tárban havonta megőrzött adatok mennyisége, beleértve az elemzési TTL által meghatározott előzményadatokat is.

* Elemzési írási műveletek: az operatív adatok frissítésének teljes körűen felügyelt szinkronizálása az elemzési tárba a tranzakciós tárolóból (automatikus szinkronizálás)

* Elemzési olvasási műveletek: a Spark-készletből az elemzési Azure Synapse Analytics és a kiszolgáló nélküli SQL-készlet futási időiből végrehajtott olvasási műveletek.

Az elemzésitár díjszabása elkülönül a tranzakciós tároló díjszabási modelljétől. Az elemzési tárolóban nem oszlott el a kiépített ru-k fogalma. Az [elemzési Azure Cosmos DB díjszabási modelljével](https://azure.microsoft.com/pricing/details/cosmos-db/)kapcsolatos részletes információkért lásd a Azure Cosmos DB oldalon.

Az Azure Cosmos DB-tároló elemzési tárának engedélyezéséhez szükséges magas szintű költségbecsléshez használhatja az [Azure Cosmos DB Capacity Plannert,](https://cosmos.azure.com/capacitycalculator/) és megbecsülheti az elemzési tárolási és írási műveletek költségeit. Az elemzési olvasási műveletek költségei az elemzési számítási feladatok jellemzőitől függnek, de magas szintű becslésként az elemzési tárolóban található 1 TB adatvizsgálat általában 130 000 elemzési olvasási műveletet eredményez, és 0,065 dollár költséget eredményez.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Elemzési idő –live (TTL)

Az elemzési élettartam jelzi, hogy mennyi ideig kell megőrizni az adott tároló adatait az elemzési tárban. 

Ha engedélyezve van az elemzési tár, a rendszer a tranzakciós TTL-konfigurációtól függetlenül automatikusan szinkronizálja az operatív adatok beszúrásokat, frissítéseket és törléseket a tranzakciós tárból az elemzési tárba. Ezen működési adatok elemzési tárolóban való megőrzése az elemzési TTL-értékkel szabályozható a tároló szintjén, az alábbiak szerint:

A tárolóra vonatkozó elemzési TTL a tulajdonság használatával van `AnalyticalStoreTimeToLiveInSeconds` beállítva:

* Ha az érték "0", hiányzik (vagy null értékre van állítva): az elemzési tároló le van tiltva, és a tranzakciós tárolóból nem replikál adatokat az elemzési tárolóba

* Ha jelen van, és az érték "-1" értékre van állítva: az elemzési tároló az összes előzményadatot megőrzi, függetlenül a tranzakciós tárolóban tárolt adatok megőrzésétől. Ez a beállítás azt jelzi, hogy az elemzési tároló az operatív adatok végtelen megőrzésével rendelkezik

* Ha jelen van, és az érték egy "n" pozitív számra van állítva: az elemek az elemzési tárolóban az utolsó módosítás időpontja után másodperccel lejárnak a tranzakciós tárolóban. Ez a beállítás akkor hasznos, ha az elemzési tárolóban korlátozott ideig szeretné megőrizni az operatív adatokat, függetlenül attól, hogy a tranzakciós tárolóban tárolt adatokat meg szeretné-e őrizni

Néhány megfontolandó szempont:

*   Miután az elemzési tárolót elemzési TTL-értékkel engedélyezte, később frissítheti egy másik érvényes értékre. 
*   Bár a tranzakciós TTL beállítható tároló- vagy elemszinten, az elemzési TTL-t csak a tároló szintjén lehet beállítani.
*   A működési adatok hosszabb megőrzését érheti el az elemzési tárolóban, ha az elemzési élettartamot (TTL) >= tranzakciós TTL-t a tároló szintjén.
*   Az elemzési tároló úgy használhatja a tranzakciós tárolót, hogy az elemzési TTL = tranzakciós TTL beállításával tükrözi a tranzakciós tárolót.

Ha engedélyezi az elemzési tárolót egy tárolón:

* A Azure Portal elemzési TTL beállítás alapértelmezett -1 értékre van állítva. Ezt az értéket "n" másodpercre módosíthatja, ha a tárolóbeállításokra navigál az Adatkezelő. 
 
* Az Azure SDK, a PowerShell vagy a parancssori felület használatával az elemzési TTL-beállítás a -1 vagy az n érték beállításával engedélyezhető. 

További tudnivalókért lásd az [elemzési TTL tárolókon való konfigurálását.](configure-synapse-link.md#create-analytical-ttl)

## <a name="next-steps"></a>Következő lépések

További tudnivalókért tekintse meg a következő dokumentumokban:

* [Azure Cosmos DB-hez készült Azure Synapse Link](synapse-link.md)

* [Az Azure Cosmos DB-hez készült Azure Synapse Link használatának első lépései](configure-synapse-link.md)

* [Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban](synapse-link-frequently-asked-questions.md)

* [Az Azure Cosmos DB-hez készült Azure Synapse Link használati esetei](synapse-link-use-cases.md)
