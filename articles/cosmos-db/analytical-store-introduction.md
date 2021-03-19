---
title: Mi az Azure Cosmos DB analitikus tároló?
description: Ismerkedjen meg Azure Cosmos DB tranzakciós (sor-alapú) és analitikai (oszlop alapú) tárolóval. A nagyméretű számítási feladatokhoz és a tranzakciós tárolóból az analitikai tárolóba történő automatikus szinkronizálás előnyei, valamint a teljesítményre gyakorolt hatás
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: bca4eb7f5f266a639916c0f8e520f025d259c39b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577359"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Mi az Azure Cosmos DB analitikus tároló?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

A Azure Cosmos DB Analytical Store egy teljesen elkülönített oszlop, amely lehetővé teszi, hogy a nagy léptékű elemzéseket a Azure Cosmos DB működési adatain keresztül engedélyezze, anélkül, hogy ez hatással lenne a tranzakciós munkaterhelésekre. 

Azure Cosmos DB tranzakciós tároló a séma-agnosztikus, és lehetővé teszi, hogy a tranzakciós alkalmazásait a séma vagy az indexelés kezelése nélkül lehessen megismételni. Ezzel szemben Azure Cosmos DB Analytical Store sematikus az analitikai lekérdezési teljesítmény optimalizálása érdekében. Ez a cikk az analitikai tár részletes ismertetését ismerteti.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Kihívások az operatív adatok nagy méretű elemzésével

Egy Azure Cosmos DB tárolóban lévő többmodelles operatív adatértékek belsőleg tárolódnak egy indexelt sorokon alapuló "tranzakciós tárolóban". A sor tárolási formátuma lehetővé teszi a gyors tranzakciós olvasást és írást az ezredmásodpercekben megadott válaszidő és az operatív lekérdezések esetében. Ha az adatkészlet nagy mennyiségű, az összetett analitikus lekérdezések költségesek lehetnek az ebben a formátumban tárolt adatok kiépített átviteli sebessége alapján. A kiépített átviteli sebesség nagy mértékben felhasználható a valós idejű alkalmazások és szolgáltatások által használt tranzakciós munkaterhelések teljesítményére.

A nagy mennyiségű adatok elemzéséhez a rendszer kinyeri az operatív adatok Azure Cosmos DB tranzakciós tárolójából, és egy külön adatrétegben tárolja azokat. Az adatraktárban és az adattárban lévő adattárolók például megfelelő formátumban tárolódnak. Ezek az adatok később nagy léptékű elemzésekhez használatosak, és a számítási motor, például a Apache Spark-fürtök használatával elemezhetők. Az analitikus tárolás és a számítási rétegek az operatív adatokból való elkülönítése további késleltetést eredményez, mivel az ETL (kinyerés, átalakítás, betöltés) folyamatok ritkábban futnak a tranzakciós számítási feladatokra gyakorolt lehetséges hatás csökkentése érdekében.

Az ETL-folyamatok akkor is bonyolultak lesznek, ha az operatív információkra vonatkozó frissítéseket az újonnan betöltött működési adatmennyiség kezelésekor hasonlítják össze. 

## <a name="column-oriented-analytical-store"></a>Oszlop-orientált analitikai tár

Azure Cosmos DB analitikus tároló foglalkozik a hagyományos ETL-folyamatokkal kapcsolatos bonyolultsági és késési kihívásokkal. Azure Cosmos DB az analitikus tároló automatikusan szinkronizálhatja az operatív adatait egy külön oszlopos tárolóba. Az oszlopos tároló formátuma alkalmas a nagyméretű analitikai lekérdezések optimalizált módon történő elvégzésére, ami az ilyen lekérdezések késésének javítását eredményezi.

Az Azure szinapszis-hivatkozás használatával most már nem ETL HTAP-megoldásokat hozhat létre az Azure szinapszis Analytics Azure Cosmos DB analitikus áruházához való közvetlen hivatkozással. Lehetővé teszi a közel valós idejű, nagy léptékű elemzések futtatását az operatív adatain.

## <a name="features-of-analytical-store"></a>Az analitikai tároló funkciói 

Amikor engedélyezi az analitikai tárolót egy Azure Cosmos DB tárolón, a tárolóban lévő operatív adatai alapján létrejön egy új, belső tároló. Ez az oszlop tárolja az adott tárolóhoz tartozó sor-orientált tranzakciós tárolótól függetlenül. Az operatív adataihoz tartozó lapkákat, frissítéseket és törléseket a rendszer automatikusan szinkronizálja az analitikus tárolóba. Az adatszinkronizáláshoz nincs szükség az adatmódosítási csatornára vagy ETL-ra.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>A működési adatok elemzési számítási feladatainak tárolására szolgáló oszlop

Az analitikai számítási feladatok jellemzően a kiválasztott mezők összesítéseit és szekvenciális vizsgálatait foglalják magukban. Az adatok egy oszlop – fő sorrendbe való tárolásával az analitikai tároló lehetővé teszi, hogy az egyes mezőkhöz tartozó értékek egy csoportja együtt legyen szerializálva. Ez a formátum csökkenti az adott mezőkhöz tartozó statisztikák vizsgálatához vagy számításához szükséges IOPS. Jelentősen javítja a lekérdezések válaszideje a nagy adathalmazokon végzett vizsgálatok során. 

Ha például az operatív táblák formátuma a következő:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Példa operatív táblázatra" border="false":::

A sor tároló a fenti, soronként szerializált, soros formában megőrzött adatmennyiséget tárolja a lemezen. Ez a formátum lehetővé teszi a gyorsabb tranzakciós olvasási, írási és működési lekérdezéseket, például a "a Product1 vonatkozó információk visszaadása". Mivel azonban az adatkészlet nagy mértékben növekszik, és ha összetett analitikai lekérdezéseket szeretne futtatni az adaton, költséges lehet. Ha például a "berendezés" kategóriába tartozó termék értékesítési trendjeit szeretné lekérni különböző üzleti egységeken és hónapokon keresztül, akkor összetett lekérdezést kell futtatnia. Az adathalmaz nagyméretű vizsgálata költséges lehet a kiépített átviteli sebesség szempontjából, és hatással lehet a valós idejű alkalmazásokat és szolgáltatásokat használó tranzakciós számítási feladatok teljesítményére is.

Az analitikus tároló, amely egy oszlopos tároló, alkalmasabb az ilyen lekérdezésekhez, mert hasonló adatmezőket szerializál, és csökkenti a lemez IOPS.

Az alábbi képen a tranzakciós sor tárolója és az analitikai oszlop tárolója látható Azure Cosmos DBban:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Tranzakciós sorok tárolása és analitikai oszlopainak tárolása Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Leválasztott teljesítmény az analitikai számítási feladatokhoz

Az analitikus lekérdezések miatt nincs hatással a tranzakciós számítási feladatok teljesítményére, mivel az analitikai tároló elkülönül a tranzakciós tárolótól.  Az analitikai tárolónak nincs szüksége elkülönített kérelmekre (RUs).

### <a name="auto-sync"></a>Automatikus szinkronizálás

Az automatikus szinkronizálás a Azure Cosmos DB teljes körűen felügyelt képességeire utal, ahol a lapkák, a frissítések és az operatív adatok törlését automatikusan szinkronizálja a tranzakciós tárolóból az analitikai tárolóba közel valós időben. Az automatikus szinkronizálás késése általában 2 percen belül megtörténik. Ha a megosztott átviteli sebességű adatbázis nagy mennyiségű tárolóval rendelkezik, az egyes tárolók automatikus szinkronizálási késése magasabb lehet, és akár 5 percet is igénybe vehet. Szeretnénk többet megtudni, hogy ez a késés hogyan illeszkedik a forgatókönyvekhez. Ehhez kérjük, lépjen kapcsolatba a [Azure Cosmos db csapatával](mailto:cosmosdbsynapselink@microsoft.com).

Az automatikus szinkronizálási képesség az analitikai tárolóval együtt a következő alapvető előnyöket nyújtja:

### <a name="scalability--elasticity"></a>Méretezhetőség & rugalmasság

A horizontális particionálással a Azure Cosmos DB tranzakciós tároló rugalmasan méretezheti a tárterületet és az átviteli sebességet anélkül, hogy leállás nélkül lenne. A tranzakciós tárolóban a horizontális particionálás rugalmas & rugalmasságot biztosít az automatikus szinkronizálásban, így biztosítva, hogy az adatkezelés közel valós időben történjen az analitikai tárolóba. Az adatszinkronizálás a tranzakciós adatforgalomtól függetlenül történik, függetlenül attól, hogy 1000 művelet/mp vagy 1 000 000 művelet/mp, és nem befolyásolja a tranzakciós tárolóban kiépített átviteli sebességet. 

### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Séma frissítéseinek automatikus kezelése

Azure Cosmos DB tranzakciós tároló a séma-agnosztikus, és lehetővé teszi, hogy a tranzakciós alkalmazásait a séma vagy az indexelés kezelése nélkül lehessen megismételni. Ezzel szemben Azure Cosmos DB Analytical Store sematikus az analitikai lekérdezési teljesítmény optimalizálása érdekében. Az automatikus szinkronizálási képességgel Azure Cosmos DB felügyeli a séma következtetéseit a tranzakciós tároló legújabb frissítésein.  Emellett a séma-ábrázolást is kezeli az analitikus tárolóban, amely magában foglalja a beágyazott adattípusok kezelését.

Ahogy a séma fejlődik, és az új tulajdonságok idővel bővülnek, az analitikai tároló automatikusan a tranzakciós tároló összes korábbi sémájában egyesítő sémát jelenít meg.

#### <a name="schema-constraints"></a>Séma megkötései

A következő megkötések alkalmazhatók a Azure Cosmos DB lévő operatív adatra, amikor az analitikai tároló automatikusan kikövetkeztet és helyesen jelöli meg a sémát:

* Legfeljebb 1000 tulajdonságot tartalmazhat a séma bármely beágyazási szintjén, valamint a maximálisan megengedett 127-os mélységet.
  * Az analitikai tárolóban csak az első 1000-tulajdonságok jelennek meg.
  * Az analitikai tárolóban csak az első 127 beágyazott szint jelenik meg.

* Míg a JSON-dokumentumok (és Cosmos DB gyűjtemények/tárolók) az egyediség szempontjából érzékenyek a kis-és nagybetűkre, az analitikai tároló nem.

  * **Ugyanebben a dokumentumban:** Az azonos szintű tulajdonságok neveinek egyedinek kell lenniük, ha a kis-és nagybetűket összehasonlítják. A következő JSON-dokumentum például azonos szinten található: "Name" és "Name". Habár érvényes JSON-dokumentum, nem felel meg az egyediségi megkötésnek, ezért a rendszer nem fogja teljes mértékben megjeleníteni az analitikai tárolóban. Ebben a példában a "név" és a "név" a kis-és nagybetűket megkülönböztető módon hasonlítja össze. Csak `"Name": "fred"` az analitikus tárolóban lesz megjelenítve, mert ez az első előfordulás. És `"name": "john"` egyáltalán nem fog megjelenni.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **Különböző dokumentumokban:** Ugyanazon a szinten és ugyanazzal a névvel, de különböző esetekben a tulajdonságok ugyanabban az oszlopban lesznek ábrázolva, az első előfordulás nevének formátuma alapján. A következő JSON-dokumentumok például `"Name"` és `"name"` ugyanazon a szinten vannak. Mivel az első dokumentum formátuma `"Name"` , ez a tulajdonság neve lesz az analitikus tárolóban. Ez azt jelenti, hogy az analitikus tárolóban az oszlop neve lesz `"Name"` . `"fred"`A és `"john"` a is képviselteti magát az `"Name"` oszlopban.


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* A gyűjtemény első dokumentuma határozza meg a kezdeti analitikai tárolási sémát.
  * A dokumentum első szintjén lévő tulajdonságok oszlopként lesznek megjelenítve.
  * A kezdeti sémánál nagyobb tulajdonságokkal rendelkező dokumentumok új oszlopokat hoznak majd ki az analitikus tárolóban.
  * Az oszlopok nem távolíthatók el.
  * Egy gyűjtemény összes dokumentumának törlése nem állítja vissza az analitikai tároló sémáját.
  * Nincs séma-verziószámozás. A tranzakciós áruházból kikövetkeztetett utolsó verzió az, amit az analitikus tárolóban fog látni.

* Jelenleg nem támogatjuk az Azure szinapszis Spark olvasási oszlopának nevét, amely üres karaktereket (szóközöket) tartalmaz.

* Eltérő viselkedés várható az értékek tekintetében `NULL` :
  * Az Azure Szinapszisban található Spark-készletek ezeket az értékeket 0 (nulla) értékként fogják beolvasni.
  * Az Azure Szinapszisban az SQL Server nélküli készletek ezeket az értékeket fogják beolvasni `NULL` .

* A hiányzó oszlopok tekintetében eltérő viselkedés várható:
  * Az Azure Szinapszisban található Spark-készletek ezeket az oszlopokat jelölik `undefined` .
  * Az Azure Szinapszisban az SQL Server nélküli készletek ezeket az oszlopokat jelölik `NULL` .

#### <a name="schema-representation"></a>Séma ábrázolása

Az elemzési tárban két módon lehet ábrázolni a sémákat. Mindkettő kompromisszumos megoldást jelent az oszlopos ábrázolás egyszerűsége, a polimorf sémák kezelése és a lekérdezés egyszerűsége szempontjából:

* Jól definiált séma-ábrázolás
* Teljes hűségű séma ábrázolása

> [!NOTE]
> Az SQL (Core) API-fiókok esetében, ha az analitikai tároló engedélyezve van, az analitikus tárolóban az alapértelmezett séma-ábrázolás jól definiálva van. Míg a MongoDB-fiókok Azure Cosmos DB API-hoz tartozó alapértelmezett séma az analitikus tárolóban teljes körű megbízhatósági sémát ábrázol. Ha olyan forgatókönyvekkel rendelkezik, amelyek eltérő séma-ábrázolást igényelnek, mint az egyes API-k alapértelmezett ajánlata, akkor a [Azure Cosmos db csapata](mailto:cosmosdbsynapselink@microsoft.com) számára engedélyezze azt.

**Jól definiált séma-ábrázolás**

A jól definiált séma-ábrázolás egy egyszerű táblázatos ábrázolást hoz létre a séma-agnosztikus adatokat a tranzakciós tárolóban. A jól definiált séma-ábrázolás a következő szempontokat mutatja be:

* Egy tulajdonságnak mindig ugyanaz a típusa több elemen belül.

  * Például `{"a":123} {"a": "str"}` nem rendelkezik jól definiált sémával, mert `"a"` néha egy sztring, és néha egy szám. Ebben az esetben az analitikai tároló regisztrálja az adattípust az adattípusként `"a"` `“a”` a tároló élettartamában lévő első előfordulási elemben. A dokumentum továbbra is szerepelni fog az analitikus tárolóban, de azok az elemek, amelyekben a különböző adattípusok `"a"` nem változnak.
  
    Ez az állapot nem vonatkozik a null tulajdonságokra. Például `{"a":123} {"a":null}` még mindig jól definiálva van.

* A tömbök típusának egyetlen ismétlődő típust kell tartalmaznia.

  * Például `{"a": ["str",12]}` nem egy jól definiált séma, mert a tömb egész és sztring típusú kombinációt tartalmaz.

> [!NOTE]
> Ha a Azure Cosmos DB analitikus tároló a jól definiált séma-ábrázolást követi, és a fenti specifikációt bizonyos elemek megszegik, akkor ezek az elemek nem lesznek felszámítva az analitikai tárolóba.

**Teljes hűségű séma ábrázolása**

A teljes körű megbízhatósági séma ábrázolásának célja, hogy kezelje a séma-független operatív adatban található polimorf sémák teljes szélességét. Ebben a séma-ábrázolásban egyetlen elem sem kerül el az analitikus tárolóból, még akkor sem, ha a jól definiált sémák megkötései (azaz a vegyes adattípus-mezők és a vegyes adattípusok tömbök) nem sérülnek.

Ez úgy érhető el, hogy az operatív adatok levél tulajdonságait lefordítja az analitikai tárolóba, külön oszlopokkal, a tulajdonságban lévő értékek adattípusa alapján. A levél tulajdonságainak neve az analitikai tároló sémájában lévő utótagként van kiterjesztve úgy, hogy a lekérdezések kétértelműség nélkül is elérhetők legyenek.

Tegyük fel például, hogy a tranzakciós tárolóban a következő minta dokumentum szerepel:

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

A `streetNo` beágyazott objektumon belüli Leaf tulajdonság `address` oszlopként fog szerepelni az analitikai tároló sémájában `address.object.streetNo.int32` . Az adattípust a rendszer utótagként adja hozzá az oszlophoz. Így ha egy másik dokumentum kerül be a tranzakciós tárolóba, ahol a Leaf tulajdonság értéke `streetNo` "123" (Megjegyzés: karakterlánc), az analitikai tároló sémája automatikusan a korábban írt oszlop típusának módosítása nélkül fejlődik. Az analitikai tárolóhoz hozzáadott új oszlop, `address.object.streetNo.string` amelyben a "123" érték van tárolva.

**Az utótagok megfeleltetésének adattípusa**

Itt látható az összes tulajdonság adattípusa és az utótag-ábrázolások az analitikus tárolóban:

|Eredeti adattípus  |Utótag  |Példa  |
|---------|---------|---------|
| Dupla |  ".float64" |    24,99|
| Tömb | ". Array" |    ["a", "b"]|
|Bináris | ". Binary" |0|
|Logikai    | ". bool"   |Igaz|
|Int32  | ". Int32"  |123|
|Int64  | ". Int64"  |255486129307|
|Null   | ". null"   | null|
|Sztring|    ". String" | „ABC”|
|Időbélyeg |    ". Timestamp" |  Időbélyeg (0, 0)|
|DateTime   |". Date"    | ISODate ("2020-08-21T07:43:07.375 Z")|
|ObjectId   |". objectId"    | ObjectId ("5f3f7b59330ec25c132623a2")|
|Dokumentum   |". Object" |    {"a": "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>A korábbi adatmennyiségek költséghatékony archiválása

Az adatbontás az adatok elkülönítését jelenti a különböző forgatókönyvekhez optimalizált tárolási infrastruktúra között. Ezzel javítva a végpontok közötti adatverem teljes teljesítményét és költséghatékonyságát. Az analitikai tárolóval a Azure Cosmos DB mostantól támogatja a tranzakciós tárolóból származó adatok automatikus leválasztását a különböző adatelrendezésekkel rendelkező analitikai tárolóba. A tranzakciós tárolóhoz képest a tárolási költségeket tekintve optimalizált analitikai tároló lehetővé teszi, hogy a korábbi elemzések során jóval nagyobb mennyiségű működési adat legyen megtartva.

Miután az analitikai tároló engedélyezve lett a tranzakciós munkaterhelések adatmegőrzési igényei alapján, beállíthatja, hogy a "tranzakciós tár élettartama (tranzakciós TTL)" tulajdonsága automatikusan törölje a rekordokat a tranzakciós tárolóból egy adott időszak után. Hasonlóképpen, az "analitikai tár élettartama (analitikai TTL)" lehetővé teszi az analitikus tárolóban megőrzött adatok életciklusának kezelését a tranzakciós tárolótól függetlenül. Az analitikai tár engedélyezésével és a TTL-tulajdonságok konfigurálásával zökkenőmentesen elvégezheti a két áruház adatmegőrzési időszakának meghatározását.

### <a name="global-distribution"></a>Globális terjesztés

Ha globálisan elosztott Azure Cosmos DB-fiókkal rendelkezik, a tárolóhoz tartozó analitikai tároló engedélyezése után a fiók minden régiójában elérhető lesz.  Az operatív adatai minden változása globálisan replikálódik minden régióban. Az elemzési lekérdezéseket hatékonyan futtathatja az adatai legközelebbi regionális példányán Azure Cosmos DBban.

### <a name="security"></a>Biztonság

Az analitikai tárolóval megegyező hitelesítés megegyezik egy adott adatbázis tranzakciós tárolójával. A hitelesítéshez elsődleges vagy csak olvasható kulcsokat is használhat. A szinapszis Studióban használhatja a társított szolgáltatást, hogy megakadályozza a Azure Cosmos DB kulcsok beillesztését a Spark-jegyzetfüzetekben. A társított szolgáltatáshoz való hozzáférés mindenki számára elérhető, aki hozzáfér a munkaterülethez.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Több Azure szinapszis Analytics-futtatókörnyezet támogatása

Az analitikai tároló úgy van optimalizálva, hogy skálázhatóságot, rugalmasságot és teljesítményt nyújtson az analitikus számítási feladatokhoz, és ez nem függ a számítási futtatási időktől. A tárolási technológia önállóan felügyelhető, hogy manuális erőfeszítések nélkül optimalizálja az elemzési számítási feladatokat.

Az analitikai tárolási rendszer az analitikai számítási rendszerből való leválasztásával a Azure Cosmos DB analitikus tárolóban lévő adatok az Azure szinapszis Analytics által támogatott különböző elemzési futtatókörnyezetekkel párhuzamosan kérhetők le. Napjainkban az Azure szinapszis Analytics Apache Spark és kiszolgáló nélküli SQL-készletet támogat Azure Cosmos DB analitikus tárolóval.

> [!NOTE]
> Az analitikus áruházból csak az Azure szinapszis Analytics futtatási idejét tudja olvasni. A tranzakciós tárolóba visszaírhatja az adatait a kiszolgálóként szolgáló rétegként.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Árképzési

Az analitikus tároló egy fogyasztáson alapuló díjszabási modellt követ, amelyben a következőkért kell fizetnie:

* Tárolás: az analitikus tárolóban megőrzött adatmennyiség, beleértve az analitikus TTL által meghatározott múltbeli adatmennyiséget.

* Analitikus írási műveletek: az operatív adatok frissítéseinek teljes körű kezelése a tranzakciós tárolóból az analitikai tárolóba (automatikus szinkronizálás)

* Analitikai olvasási műveletek: az Azure szinapszis Analytics Spark-készlet és a kiszolgáló nélküli SQL-készlet futtatási ideje alapján végrehajtott olvasási műveletek az analitikus tárolón.

Az analitikus tároló díjszabása nem azonos a tranzakciós áruház díjszabási modelljével. Az analitikai tár nem rendelkezik kiépített RUs-koncepcióval. Tekintse meg a [Azure Cosmos db díjszabási oldalát](https://azure.microsoft.com/pricing/details/cosmos-db/), amely részletesen ismerteti az analitikus tároló díjszabási modelljét.

Ahhoz, hogy egy Azure Cosmos DB-tárolón az analitikai tárolót engedélyező magas szintű költségbecslés legyen elérhető, használhatja a [Azure Cosmos db Capacity Plannert](https://cosmos.azure.com/capacitycalculator/) , és megbecsülheti az analitikai tárolási és írási műveletek költségeit. Az analitikus olvasási műveletek költségei az elemzési számítási feladatok jellemzőitől függenek, de magas szintű becslésként az analitikai tárolóban található 1 TB-os adatok vizsgálata általában 130 000 analitikai olvasási műveletet eredményez, és $0,065-os költséget eredményez.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analitikus élettartam (TTL)

Az elemzési élettartam jelzi, hogy mennyi ideig kell megőrizni az adott tároló adatait az elemzési tárban. 

Ha az analitikai tároló engedélyezve van, a beszúrja, frissíti, törli az operatív adatokba automatikusan szinkronizálja a tranzakciós tárolóból az analitikai tárolóba, a tranzakciós TTL-konfigurációtól függetlenül. A működési adatoknak az analitikai tárolóban való megőrzését a tároló szintjén található analitikai TTL-érték szabályozhatja az alább megadott módon:

A tárolók analitikai ÉLETTARTAMa a következő `AnalyticalStoreTimeToLiveInSeconds` tulajdonsággal van beállítva:

* Ha az érték "0", hiányzik (vagy NULL értékre van állítva): az analitikai tároló le van tiltva, és nem replikálódnak adatok a tranzakciós tárolóból az analitikai tárolóba

* Ha van, és az érték "-1" értékű: az analitikai tároló megőrzi az összes korábbi adatát, függetlenül a tranzakciós tárolóban lévő adatok megőrzésének. Ez a beállítás azt jelzi, hogy az analitikai tároló a működési adatok végtelen megőrzésével rendelkezik

* Ha van, és az érték egy "n" pozitív számra van beállítva: az elemek a tranzakciós tároló utolsó módosításának időpontját követően lejárnak az "n" analitikai tárolóban. Ez a beállítás kihasználható, ha az elemzési tárolóban korlátozott ideig szeretné megőrizni az operatív adatait, függetlenül a tranzakciós tárolóban lévő adatok megőrzésének.

Néhány megfontolandó szempont:

*   Miután az analitikai tár engedélyezve lett egy analitikai TTL-értékkel, később más érvényes értékre is frissítheti. 
*   Míg a tranzakciós TTL beállítható a tárolóban vagy az elem szintjén, az analitikai élettartam jelenleg csak a tároló szintjén állítható be.
*   A működési adatok megőrzését az analitikus tárolóban az analitikai TTL >= tranzakciós élettartam beállításával érheti el a tároló szintjén.
*   Az analitikai tár a tranzakciós tároló tükrözésére az analitikai TTL = tranzakciós TTL beállításával végezhető el.

Ha az analitikai tárolót egy tárolón engedélyezi:

* A Azure Portal az analitikai TTL beállítás az alapértelmezett-1 értékre van állítva. Ezt az értéket "n" másodpercre módosíthatja, ha a Adatkezelő alatt navigál a tároló beállításai között. 
 
* Az Azure SDK-ból vagy a PowerShellből vagy a CLI-ből az analitikai élettartam beállítás engedélyezve lehet az-1 vagy az "n" értékre. 

További információ: [az analitikai élettartam konfigurálása egy tárolón](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Következő lépések

További információért lásd a következő dokumentumokat:

* [Azure Cosmos DB-hez készült Azure Synapse Link](synapse-link.md)

* [Az Azure Cosmos DB-hez készült Azure Synapse Link használatának első lépései](configure-synapse-link.md)

* [Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban](synapse-link-frequently-asked-questions.md)

* [Az Azure Cosmos DB-hez készült Azure Synapse Link használati esetei](synapse-link-use-cases.md)
