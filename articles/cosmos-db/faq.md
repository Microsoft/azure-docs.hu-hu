---
title: Gyakori kérdések a Azure Cosmos DB különböző API-król
description: Választ kaphat a Azure Cosmos DBával, egy globálisan elosztott, többmodelles adatbázis-szolgáltatással kapcsolatos gyakori kérdésekre. A kapacitás, a teljesítményszint és a skálázás megismerése.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 81c959467c4bb3700120917cf150773fb18e866d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359829"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Gyakori kérdések az Azure Cosmos DB különböző API-jaival kapcsolatban
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Melyek a Azure Cosmos DB tipikus használati esetei?

A Azure Cosmos DB jó választás az új webes, mobil-, játék-és IoT-alkalmazások számára, amelyekben az automatikus méretezés, a kiszámítható teljesítmény, az ezredmásodperces válaszidő gyors sorrendje és a séma nélküli adatmennyiség lekérdezése fontos. Azure Cosmos DB a gyors fejlesztéshez és az alkalmazás-adatmodellek folyamatos ismétlésének támogatásához is alkalmas. A felhasználó által létrehozott tartalmat és adatokat kezelő alkalmazások a [Azure Cosmos db gyakori használati esetei](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hogyan nyújt Azure Cosmos DB kiszámítható teljesítményt?

A [kérési egység](request-units.md) (ru) a Azure Cosmos db teljesítményének mértéke. A 1RU átviteli sebessége megfelel egy 1 KB-os dokumentum lekérésének átviteli sebességének. Azure Cosmos DB összes művelete, beleértve az olvasásokat, az írásokat, az SQL-lekérdezéseket és a tárolt eljárások végrehajtását, a művelet végrehajtásához szükséges átviteli sebesség alapján determinisztikus RU-értékkel rendelkezik. A CPU-t, az IO-t és a memóriát, valamint azt, hogy az egyes alkalmazások hogyan befolyásolják az alkalmazás átviteli sebességét, egyetlen RU-mérték alapján is meggondolhatja magát.

Az egyes Azure Cosmos-tárolók kiosztott átviteli sebességgel konfigurálhatók a másodpercenkénti adatátviteli sebesség alapján. Bármilyen méretű alkalmazás esetében az egyes kérelmeket az RU-értékek mérésére, valamint egy tároló kiépítésére használhatja az összes kérelem összes kérelmi egységének kezeléséhez. Az alkalmazás igényeinek megfelelően méretezheti vagy méretezheti a tároló átviteli sebességét is. Ha további információt szeretne a kérelmekkel kapcsolatos egységekről, és segítségre van szüksége a tároló igényeinek meghatározásához, próbálkozzon az [átviteli sebesség kalkulátorával](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hogyan támogatja az Azure Cosmos DB a különböző adatmodelleket, például a kulcs/érték, az oszlopos, a dokumentum és a gráf használatát?

A kulcs/érték (tábla), az oszlopos, a dokumentum és a Graph adatmodellek natív módon támogatottak az ARS (atomok, rekordok és sequences) kialakításával, amely Azure Cosmos DB épül. Az atomok, rekordok és a különböző adatmodellek könnyen leképezhetők és kiállíthatók. A modellekhez tartozó API-k mostantól elérhetők (SQL-, MongoDB-, tábla-és Gremlin), és a további adatmodellekhez kapcsolódó egyebek a jövőben lesznek elérhetők.

Azure Cosmos DB rendelkezik egy séma agnosztikus indexelési motorral, amely képes automatikusan indexelni az összes betöltött adatot anélkül, hogy a fejlesztőtől sémát vagy másodlagos indexet kellene igényelni. A motor a logikai indexek (fordított, oszlopos, fa) készletére támaszkodik, amely leválasztja a tárolási elrendezést az indexből, és lekérdezi az alrendszereket. Cosmos DB emellett bővíthető módon támogatja a vezetékes protokollok és API-k készletét, és hatékonyan lefordíthatja őket az alapszintű adatmodellre (1) és a logikai indexek elrendezéseit (2), így az egyedi módon képes támogatni több adatmodell natív használatát.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Használhatok több API-t az adataim eléréséhez?

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Ahol a többmodelles Azure Cosmos DB több API-t és több adatmodellt is támogat, a különböző API-k különböző adatformátumokat használnak a Storage és a Wire protokoll számára. Például az SQL JSON-t használ, a MongoDB pedig az BSON-t használja, a Table a EDM-t használja, a Cassandra pedig a CQL, a Gremlin JSON formátumot használ Ennek eredményeképpen azt javasoljuk, hogy ugyanazt az API-t használja az adott fiókban lévő adathoz való hozzáféréshez.

Az egyes API-k egymástól függetlenül működnek, kivéve a Gremlin és az SQL API-t, amelyek együttműködnek egymással.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Megfelelő-e Azure Cosmos DB HIPAA?

Igen, Azure Cosmos DB HIPAA-kompatibilis. A HIPAA az egyéni és beazonosítható egészségügyi adatok használatára, nyilvánosságra hozatalára és védelmére vonatkozó követelményeket állapítja meg. További információ: [Microsoft adatvédelmi központ](/compliance/regulatory/offering-hipaa-hitech).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Mik a Azure Cosmos DB tárolási korlátai?

A tárolók által Azure Cosmos DB tárolt adatmennyiségnek nincs korlátja.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Mik a Azure Cosmos DB átviteli sebességének korlátai?

A tároló által támogatott átviteli sebességek teljes mennyisége nem korlátozható Azure Cosmos DBban. A legfontosabb elképzelés az, hogy a számítási feladatokat egyenletesen, elég nagy számú partíciós kulcs között ossza el.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Titkosítva vannak-e a közvetlen és az átjáró csatlakozási módjai?

Igen, mindkét mód mindig teljes mértékben titkosítva van.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Mennyibe kerül a Azure Cosmos DB?

További részletekért tekintse meg a [Azure Cosmos db díjszabását ismertető](https://azure.microsoft.com/pricing/details/cosmos-db/) oldalt. Azure Cosmos DB a használati díjakat a kiosztott tárolók száma, a tárolók online állapotának száma és az egyes tárolók kiépített átviteli sebessége határozza meg.

### <a name="is-a-free-account-available"></a>Elérhető-e ingyenes fiók?

Igen, az időkorlátot díjmentesen, kötelezettségvállalás nélkül is regisztrálhat. A regisztrációhoz látogasson el a kipróbálás Azure Cosmos DB ingyen, vagy olvassa el a [következőt](https://azure.microsoft.com/try/cosmosdb/) : [Try Azure Cosmos db FAQ](#try-cosmos-db).

Ha még nem ismeri az Azure-t, regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/), amely 30 napot és egy kreditet biztosít az összes Azure-szolgáltatás kipróbálásához. Ha Visual Studio-előfizetéssel rendelkezik, jogosult [ingyenes Azure-kreditek](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) használatára is bármely Azure-szolgáltatásban.

Az [Azure Cosmos db Emulator](local-emulator.md) használatával az alkalmazást ingyenesen, Azure-előfizetés létrehozása nélkül fejlesztheti és tesztelheti az alkalmazását. Amikor már elégedett az alkalmazás működésével az Azure Cosmos DB Emulatorban, átválthat az Azure Cosmos DB-fiók használatára a felhőben.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hogyan Kérhetek további segítséget a Azure Cosmos DB?

Ha technikai kérdést szeretne feltenni, tegye közzé a következő két kérdés-válasz fórum egyikét:

* [Microsoft Q&egy kérdés oldal](/answers/topics/azure-cosmos-db.html)
* [Stack overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). A Stack Overflow a programozási kérdésekre legmegfelelőbb. Győződjön meg arról, hogy a kérdés [a témában](https://stackoverflow.com/help/on-topic) van, és a [lehető legtöbb részletet biztosít, így a kérdés egyértelmű és megválaszolható](https://stackoverflow.com/help/how-to-ask).

Új szolgáltatások igényléséhez hozzon létre egy új kérelmet a [felhasználói hangon](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Azure Cosmos DB-előfizetések kipróbálása

Az előfizetések, a díjmentes és a kötelezettségvállalások nélkül is élvezheti a korlátozott Azure Cosmos DB élményt. Ha szeretne regisztrálni egy try Azure Cosmos DB-előfizetésre, keresse fel [ingyen a kipróbálás Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) , és használjon személyes Microsoft-fiók (MSA). Ez az előfizetés külön az [ingyenes Azure-próbaverziótól](https://azure.microsoft.com/free/), és az ingyenes Azure-próbaverzióval vagy egy Azure fizetős előfizetéssel együtt használható.

Próbálja meg Azure Cosmos DB előfizetéseket a felhasználói AZONOSÍTÓhoz társított Azure Portal következő egyéb előfizetések között.

A Azure Cosmos DB-előfizetések kipróbálásához a következő feltételek érvényesek:

* A fiókhoz való hozzáférés a személyes Microsoft-fiókokhoz (MSA) is biztosítható. Ne használjon Azure Active Directory-(Azure AD-) fiókokat vagy a vállalati Azure AD-bérlőhöz tartozó fiókokat, ezért korlátozásokkal rendelkezhetnek, amelyek letiltják a hozzáférést a hozzáférés engedélyezéséhez.
* Egy, az SQL, a Gremlin API és a Table accounts-előfizetések által [kiosztott tároló](./set-throughput.md#set-throughput-on-a-container) .
* Akár három, a MongoDB-fiókokra vonatkozó előfizetéshez tartozó [kiosztott gyűjtemény](./set-throughput.md#set-throughput-on-a-container) .
* Egy [átviteli sebesség kiépített adatbázisa](./set-throughput.md#set-throughput-on-a-database) előfizetéssel. Az átviteli sebesség kiépített adatbázisai tetszőleges számú tárolót tartalmazhatnak.
* 10 GB tárolókapacitás.
* A globális replikáció a következő Azure- [régiókban](https://azure.microsoft.com/regions/)érhető el: USA középső régiója, Észak-Európa és Délkelet-Ázsia
* A maximális átviteli sebesség 5 K RU/s a tároló szintjén kiépítve.
* Az adatbázis szintjén kiépített 20 K/s maximális átviteli sebesség.
* Az előfizetések 30 nap elteltével lejárnak, és legfeljebb 31 napra bővíthetők. A lejárat után a rendszer törli a benne foglalt adatokat.
* Nem hozhatók létre Azure-támogatási jegyek az Azure Cosmos DB-fiókok kipróbálásához; azonban támogatást biztosítanak a meglévő támogatási csomagokkal rendelkező előfizetőknek.

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB beállítása

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hogyan regisztrálni a Azure Cosmos DBra?

A Azure Cosmos DB a Azure Portalben érhető el. Először regisztráljon egy Azure-előfizetésre. A regisztráció után hozzáadhat egy Azure Cosmos DB fiókot az Azure-előfizetéséhez.

### <a name="what-is-a-primary-key"></a>Mi az elsődleges kulcs?

Az elsődleges kulcs egy biztonsági jogkivonat, amely hozzáfér a fiók összes erőforrásához. A kulccsal rendelkező személyeknek olvasási és írási hozzáférésük van az adatbázis-fiók összes erőforrásához. Az elsődleges kulcsok terjesztésekor körültekintően járjon el. Az elsődleges elsődleges kulcs és a másodlagos elsődleges kulcs a [Azure Portal][azure-portal] **kulcsok** paneljén érhető el. A kulcsokkal kapcsolatos további információkért lásd: [Hozzáférési kulcsok megtekintése, másolása és újragenerálása](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Mik azok a régiók, amelyeknek a PreferredLocations beállítható?

A PreferredLocations érték bármely olyan Azure-régióhoz beállítható, amelyben Cosmos DB elérhető. Az elérhető régiók listáját az [Azure-régiók](https://azure.microsoft.com/regions/)című részben tekintheti meg.

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Van valami, amit tudni kell, ha az Azure-adatközpontok révén a világ különböző pontjain osztja el az adatelosztást?

Azure Cosmos DB az összes Azure-régióban megtalálható az [Azure-régiók](https://azure.microsoft.com/regions/) lapon megadott módon. Mivel ez az alapszolgáltatás, minden új adatközpont Azure Cosmos DB jelenléttel rendelkezik.

Amikor beállítja a régiót, ne feledje, hogy Azure Cosmos DB a szuverén és a kormányzati felhők tiszteletben tartását. Ez azt eredményezi, hogy ha egy [szuverén régióban](https://azure.microsoft.com/global-infrastructure/)hoz létre fiókot, akkor az adott [szuverén régióból](https://azure.microsoft.com/global-infrastructure/)nem lehet replikálni. Hasonlóképpen nem engedélyezheti a replikációt más szuverén helyekre egy külső fiókból.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Lehetséges a tárolók átviteli sebességének átváltása az adatbázis szintű átviteli sebesség kiépítés esetén? Vagy fordítva

A tároló-és adatbázis-szintű átviteli sebesség kiépítés külön ajánlat, és az ilyen típusú váltáshoz a forrásról a célhelyre kell áttelepítenie az adatok egyikét. Ez azt jelenti, hogy létre kell hoznia egy új adatbázist vagy egy új tárolót, majd át kell telepítenie az adatátvitelt [tömeges végrehajtó kódtár](bulk-executor-overview.md) vagy [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)használatával.

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Támogatja az Azure CosmosDB a Time Series-elemzést?

Igen, az Azure CosmosDB a Time Series-elemzést is támogatja, itt az [idősorozat-minta](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Ez a minta azt mutatja be, hogyan használható a Change feed az összesített nézetek idősorozat-adatokat tartalmazó létrehozásához. Ezt a megközelítést a Spark streaming vagy egy másik adatfolyam-adatfeldolgozó használatával terjesztheti ki.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>A Azure Cosmos DB szolgáltatási kvóták és az átviteli sebesség korlátai

További információkért tekintse meg a Azure Cosmos DB [szolgáltatási kvótákat](concepts-limits.md) és az [összes korlátot a tárolóban és az adatbázis](set-throughput.md#comparison-of-models) cikkeiben.

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>Gyakran ismételt kérdések az SQL API-ról

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hogyan megkezdeni az SQL API fejlesztését?

Először regisztrálnia kell egy Azure-előfizetésre. Az Azure-előfizetésre való regisztráció után hozzáadhat egy SQL API-tárolót az Azure-előfizetéséhez. Azure Cosmos DB fiók hozzáadásával kapcsolatos útmutatásért lásd: [Azure Cosmos Database-fiók létrehozása](create-sql-api-dotnet.md#create-account).

[SDK-k](sql-api-sdk-dotnet.md) a .NET, Python, Node.js, JavaScript és Java esetében érhetők el. A fejlesztők a REST- [alapú http API](/rest/api/cosmos-db/) -kkal is használhatják a különböző platformokról és nyelvekről származó Azure Cosmos db erőforrásokat.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Elérhető néhány kész minta a kezdő kezdéshez?

Az SQL API [.net](sql-api-dotnet-samples.md)-, [Java](https://github.com/Azure/azure-documentdb-java)-, [Node.js](sql-api-nodejs-samples.md)-és [Python](sql-api-python-samples.md) SDK-minták a githubon érhetők el.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Támogatja az SQL API-adatbázis a séma nélküli adatgyűjtést?

Igen, az SQL API lehetővé teszi, hogy az alkalmazások a séma-definíciók és a javaslatok nélkül tárolják az tetszőleges JSON-dokumentumokat. Az Azure Cosmos DB SQL-lekérdezési felületen keresztül azonnal elérhetők az adatlekérdezések.

### <a name="does-the-sql-api-support-acid-transactions"></a>Támogatja az SQL API a savas tranzakciókat?

Igen, az SQL API támogatja a JavaScript által tárolt eljárásként és eseményindítóként kifejezett dokumentumok közötti tranzakciókat. A tranzakciók az egyes tárolók egyetlen partícióján vannak kiképezve, és a savas szemantika "All vagy Nothing" néven lett végrehajtva. Ha a kivételeket a JavaScript alkalmazás kódjának kiszolgálóoldali végrehajtása hajtja végre, a teljes tranzakció vissza lesz állítva. 

### <a name="what-is-a-container"></a>Mi az a tároló?

A tároló dokumentumok egy csoportja és a hozzájuk társított JavaScript-alkalmazás logikája. A tároló számlázható entitás, ahol a [költségeket](performance-levels.md) az átviteli sebesség és a felhasznált tárterület határozza meg. A tárolók egy vagy több partíciót vagy kiszolgálót is kiterjedhetnek, és méretezhetők a gyakorlatilag korlátlan mennyiségű tárterület vagy átviteli sebesség kezelésére.

* Az SQL API esetében az erőforrás neve tároló.
* A MongoDB-fiókok Cosmos DB API-ját egy tároló képezi le egy gyűjteményhez.
* A Cassandra és a Table API fiókok esetében egy tároló képezi le a táblát.
* A Gremlin API-fiókok esetében a tároló egy gráfhoz rendeli a diagramot.

A tárolók a Azure Cosmos DB számlázási entitásai is. Minden egyes tároló számlázása óránként történik, a kiosztott átviteli sebesség és a felhasznált tárolóhely alapján. További információ: [Azure Cosmos db díjszabása](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Hogyan hozható létre adatbázis?

Az adatbázisokat a [Azure Portal](https://portal.azure.com)használatával hozhatja létre a [tároló hozzáadása](create-sql-api-java.md#add-a-container), a [Azure Cosmos db SDK](sql-api-sdk-dotnet.md)-k egyike vagy a [REST API](/rest/api/cosmos-db/)-k című témakörben leírtak szerint.

### <a name="how-do-i-set-up-users-and-permissions"></a>Hogyan állíthatom be a felhasználókat és engedélyeket?

Felhasználókat és engedélyeket a [Cosmos db API SDK](sql-api-sdk-dotnet.md) -k vagy a [REST API](/rest/api/cosmos-db/)-k egyikével hozhat létre.

### <a name="does-the-sql-api-support-sql"></a>Támogatja az SQL API az SQL-t?

Az SQL API-fiókok által támogatott SQL-lekérdezési nyelv a SQL Server által támogatott lekérdezési funkció továbbfejlesztett részhalmaza. A Azure Cosmos DB SQL-lekérdezési nyelv gazdag hierarchikus és a kapcsolódó operátorokat és bővíthetőséget biztosít a JavaScript-alapú, felhasználó által definiált függvények (UDF-k) segítségével. A JSON-nyelvtan lehetővé teszi a JSON-dokumentumok faszerkezetű, címkézett csomópontokkal történő modellezését, amelyeket a Azure Cosmos DB automatikus indexelési módszer és az Azure Cosmos DB SQL-lekérdezési dialektusa használ. További információ az SQL-nyelvtan használatáról: [SQL-lekérdezési][query] cikk.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Támogatja az SQL API az SQL aggregációs funkciókat?

Az SQL API a nagy késleltetésű összesítést az összesítő függvények `COUNT` , a,, `MIN` `MAX` `AVG` és `SUM` az SQL-nyelvtan használatával bármilyen méretben támogatja. További információ: [összesítő függvények](sql-query-aggregate-functions.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hogyan biztosítja az SQL API az egyidejűséget?

Az SQL API támogatja az optimista Egyidejűség-vezérlést (OCC) a HTTP-entitások címkéi vagy Etagek használatával. Minden SQL API-erőforrás rendelkezik egy ETag, és a ETag minden alkalommal be van állítva a kiszolgálón, amikor frissülnek a dokumentumok. A ETag fejléc és az aktuális érték szerepel az összes válaszüzenetben. A Etagek használható a If-Match fejléccel, hogy a kiszolgáló döntse el, hogy frissíteni kell-e egy erőforrást. A If-Match érték az a ETag érték, amelyet ellenőrizni kell. Ha a ETag értéke megegyezik a kiszolgáló ETag értékével, az erőforrás frissül. Ha a ETag már nem aktuális, a kiszolgáló elutasítja a műveletet "HTTP 412 előfeltételi hiba" hibakódtal. Az ügyfél ezután visszakéri az erőforrást az erőforrás aktuális ETag értékének beolvasásához. Emellett a Etagek is használható az If-None-Match fejléctel annak megállapításához, hogy szükség van-e egy erőforrás visszahívására.

Ha optimista párhuzamosságot szeretne használni a .NET-ben, használja a [AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition) osztályt. .NET-minta esetén tekintse meg a [program. cs](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/DocumentManagement/Program.cs) részt a githubon a DocumentManagement-mintában.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hogyan végre tranzakciókat az SQL API-ban?

Az SQL API támogatja a nyelvre épülő tranzakciókat a JavaScript által tárolt eljárások és eseményindítók használatával. A szkripteken belüli összes adatbázis-művelet a pillanatkép-elkülönítés alatt fut. Ha egypartíciós tároló, a végrehajtás hatóköre a tárolóra terjed ki. Ha a tároló particionálva van, a végrehajtás a tárolón belül ugyanazzal a partíciós kulccsal rendelkező dokumentumokra terjed ki. A tranzakció kezdetekor pillanatkép készül a dokumentumverziókról (ETag-ek), és csak akkor lesznek véglegesítve, ha a parancsfájl sikeres. Ha a JavaScript hibát jelez, a tranzakció vissza lesz állítva. További információ: [kiszolgálóoldali JavaScript-programozás Azure Cosmos db számára](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hogyan szúrhatók be dokumentumok a Cosmos DBba?

A dokumentumokat a következő módszerek egyikével lehet tömegesen beszúrni Azure Cosmos DBba:

* A tömeges végrehajtó eszköz a [tömeges végrehajtó .net-kódtár használata](bulk-executor-dot-net.md) és a [tömeges végrehajtó Java-függvénytár](bulk-executor-java.md) használata című témakörben leírtak szerint
* Az adatáttelepítési eszköz a [Azure Cosmos db adatbázis-áttelepítési eszközében](import-data.md)leírtak szerint.
* Tárolt eljárások a [Azure Cosmos db kiszolgálóoldali JavaScript-programozása](stored-procedures-triggers-udfs.md)című témakörben leírtak szerint.

### <a name="does-the-sql-api-support-resource-link-caching"></a>Támogatja az SQL API az erőforrás-hivatkozás gyorsítótárazását?

Igen, mivel Azure Cosmos DB egy REST-szolgáltatás, az erőforrás-hivatkozások nem változtathatók meg, és a gyorsítótárba helyezhetők. Az SQL API-ügyfelek megadhatnak egy "If-None-Match" fejlécet bármilyen erőforrás-szerű dokumentumhoz vagy tárolóhoz való olvasáshoz, majd a kiszolgáló verziójának módosítása után frissíthetik a helyi másolatokat.

### <a name="is-a-local-instance-of-sql-api-available"></a>Elérhető-e az SQL API helyi példánya?

Igen. A [Azure Cosmos db Emulator](local-emulator.md) a Cosmos db szolgáltatás kiváló hűségű emulációját biztosítja. Támogatja a Azure Cosmos DB azonos funkciókat, beleértve a JSON-dokumentumok létrehozását és lekérdezését, a gyűjtemények üzembe helyezését és méretezését, valamint a tárolt eljárások és triggerek végrehajtását. Az Azure Cosmos DB Emulator használatával fejlesztheti és tesztelheti az alkalmazásokat, és globális szinten helyezheti üzembe őket az Azure-ban azáltal, hogy egyetlen konfigurációs módosítást végez a Azure Cosmos DBhoz tartozó kapcsolódási végponton.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>A portálon található adatkezelőből kikerekített dokumentum hosszú, lebegőpontos értékének meghatározása.

Ez a JavaScript egyik korlátozása. A JavaScript dupla pontosságú lebegőpontos formátumú számokat használ az IEEE 754-ben megadott értékekkel, és biztonságosan képes tárolni a számokat – (2<sup>53</sup> – 1) és 2<sup>53</sup>-1 (például 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Hol engedélyezettek az engedélyek az objektum-hierarchiában?

Az engedélyek ResourceTokens használatával történő létrehozása a tároló szintjén és annak leszármazottai (például dokumentumok, mellékletek) esetében engedélyezett. Ez azt jelenti, hogy az adatbázis vagy a fiók szintjén jelenleg nem engedélyezett az engedély létrehozása.

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Következő lépések

A más API-kkal kapcsolatos gyakori kérdések megismeréséhez lásd:

* [Azure Cosmos db API-MongoDB](mongodb-api-faq.md) kapcsolatos gyakori kérdések
* Gyakori kérdések a [GREMLIN API-ról Azure Cosmos db](gremlin-api-faq.md)
* Gyakori kérdések [a Cassandra APIról Azure Cosmos db](cassandra-faq.md)
* Gyakori kérdések [a Table APIról Azure Cosmos db](table-api-faq.md)