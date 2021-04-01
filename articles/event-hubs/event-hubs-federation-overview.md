---
title: Többhelyes és többrégiós összevonás – Azure Event Hubs | Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure Event Hubs többhelyes és többrégiós összevonásáról.
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 12ef895c8b16fe18ed02ebf01d17624ac71c2f3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97861454"
---
# <a name="multi-site-and-multi-region-federation"></a>Többhelyes és többrégiós összevonás

Számos kifinomult megoldás esetében ugyanaz az esemény-adatfolyamok elérhetővé válnak több helyen történő felhasználáshoz, vagy az esemény-adatfolyamokat több helyen kell gyűjteni, majd egy adott helyre kell összevonni őket a felhasználáshoz. Az esemény-adatfolyamok bővítésére és csökkentésére is szükség van, vagy az Event Format konverziót is meg kell adni egyetlen régión és megoldáson belül is.

Gyakorlatilag ez azt jelenti, hogy a megoldás több Event Hubs-t fog fenntartani, gyakran különböző régiókban és Event Hubs névterekben, majd replikálja az eseményeket közöttük. Az eseményeket olyan forrásokkal és célokkal is lehet cserélni, mint például az [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md), az [Azure IoT Hub](../iot-fundamentals/iot-introduction.md)vagy a [Apache Kafka](https://kafka.apache.org). 

A különböző régiókban található több aktív Event Hubs fenntartása lehetővé teszi az ügyfelek számára, hogy a tartalmuk beolvasztása után is kiválaszthatják és átválthatják egymás között, így a teljes rendszer rugalmasabban működik a regionális rendelkezésre állási problémákkal szemben.

Ez az "összevonás" című fejezet az összevonási mintákat ismerteti, valamint azt, hogy ezek a minták hogyan végezhetők el a kiszolgáló nélküli [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) vagy a [Azure functions][1] Runtimes használatával, ha a saját átalakítási vagy dúsítási kódját közvetlenül az esemény flow-útvonalon szeretné kijelölni. 

## <a name="federation-patterns"></a>Összevonási minták

Számos oka lehet annak, hogy miért érdemes áthelyezni az eseményeket a különböző Event Hubs vagy más források és célok között, és a jelen szakasz legfontosabb mintáit is felszámoljuk, valamint a megfelelő minta részletesebb útmutatására is hivatkozunk. 

- [Rugalmasság a regionális elérhetőségi eseményekkel szemben](#resiliency-against-regional-availability-events)
- [Késés optimalizálása](#latency-optimization)
- [Ellenőrzés, csökkentés és dúsítás](#validation-reduction-and-enrichment)
- [Integráció az analitikai szolgáltatásokkal](#integration-with-analytics-services)
- [Az esemény-adatfolyamok összevonása és normalizálása](#consolidation-and-normalization-of-event-streams)
- [Az esemény-adatfolyamok felosztása és útválasztása](#splitting-and-routing-of-event-streams)
- [Kivetítések naplózása](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>Rugalmasság a regionális elérhetőségi eseményekkel szemben 

![Régiónkénti rendelkezésre állás](media/event-hubs-federation-overview/regional-availability.jpg)

Bár a maximális rendelkezésre állás és a megbízhatóság a Event Hubs legfontosabb működési prioritása, a hálózati vagy névfeloldási problémák miatt előfordulhat, hogy a gyártó vagy a fogyasztó nem beszél a hozzá tartozó "elsődleges" esemény központjának, vagy ha egy Event hub valóban átmenetileg nem válaszol, vagy hibákat ad vissza. 

Ezek a feltételek nem "katasztrofálisak", így a regionális telepítést teljes egészében fel kell vennie, mint a vész- [helyreállítási](event-hubs-geo-dr.md) helyzetekben, de előfordulhat, hogy egyes alkalmazások üzleti forgatókönyvét már nem több, mint néhány perc vagy akár másodperc is befolyásolja. 

Az ilyen forgatókönyvek kezeléséhez két alapvető minta létezik:

- A [replikálási][4] minta egy elsődleges Event hub tartalmának egy másodlagos esemény központba történő replikálását jelenti, amelynek során a rendszer általában az alkalmazás által használt elsődleges esemény központját használja mind a termelő, mind a fogyasztó eseményekhez, a másodlagos megoldás pedig tartalékként szolgál arra az esetre, ha az elsődleges esemény központja elérhetetlenné válik. Mivel a replikáció egyirányú, az elsődlegesről a másodlagosra, a termelők és a fogyasztók egy nem elérhető elsődlegestől a másodlagosnak való átállása miatt a régi elsődlegestől már nem kap új eseményt, és ezért többé nem lesz aktuális.
  A tiszta replikáció ezért csak egyirányú feladatátvételi helyzetekben alkalmas. A feladatátvétel elvégzése után a régi elsődlegest elhagyták, és egy új másodlagos eseményt kell létrehozni egy másik régióban.
- Az [egyesítési][5] minta kiterjeszti a replikálási mintát úgy, hogy a két vagy több Event Hubs tartalmának folyamatos egyesítését végzi. A rendszer minden olyan eseményt, amely eredetileg a séma egyik Event Hubs lett létrehozva, replikálódik a másik Event Hubs. Ahogy az események replikálódnak, a rendszer megjegyzésekkel láthatja el azokat, amelyeket a replikációs cél replikálási folyamata ezt követően figyelmen kívül hagy. Az egyesítési minta használatának eredménye két vagy több Event Hubs, amely végül konzisztens módon fogja tartalmazni ugyanezeket az eseményeket. 
  
Mindkét esetben a Event Hubs tartalma nem lesz azonos. Az egyik gyártótól származó és ugyanazon partíciós kulcs szerint csoportosított események ugyanabban a relatív sorrendben jelennek meg, mint az eredetileg elküldve, de az események abszolút sorrendje eltérő lehet. Ez különösen igaz olyan helyzetekben, amikor a forrás és a cél partícióinak száma Event Hubs eltérő, ami az itt leírt több kibővített minta esetében szükséges. Egy [osztó vagy útválasztó](#splitting-and-routing-of-event-streams) egy sokkal nagyobb Event hub egy szeletét szerezheti be több száz partícióval és tölcsérrel egy kisebb Event hub-ba, amely csupán néhány partícióval rendelkezik, és alkalmasabb az alkészletek korlátozott feldolgozási erőforrásokkal való kezelésére. Ezzel szemben a [Konszolidáció](#consolidation-and-normalization-of-event-streams) több kisebb Event Hubs adatait is tölcsérbe helyezheti egyetlen, nagyobb méretű Event hubhoz több partícióval, hogy megbirkózzanak az összevont adatátviteli és feldolgozási igényekkel.

Az események együttes megtartásának feltétele a partíciós kulcs, nem az eredeti partíció-azonosító. A relatív sorrendtel kapcsolatos további szempontokat és a feladatátvétel az egyik esemény központból a következőre való áttelepítését a [replikálási][4] minta leírása tárgyalja.


Útmutató 
- [Replikációs minta][4]
- [Egyesítési minta][5]

### <a name="latency-optimization"></a>Késés optimalizálása 

![Késés optimalizálása](media/event-hubs-federation-overview/latency-optimization.jpg)  

Az esemény-adatfolyamokat a gyártók egyszer írják elő, de az események fogyasztói által tetszőleges számú alkalommal olvashatók be. Azokban az esetekben, amikor egy régión belüli esemény-adatfolyamot több fogyasztó is megoszt, és az elemzési feldolgozás során többször is el kell érni a különböző régiókban, vagy az összes olyan igény esetén, amely az egyidejű fogyasztókat is kiéhezik 

Jó példa arra, hogy mikor érdemes a replikálást előnyben részesíteni a különböző régiókból származó események fogyasztása során, különösen azokban az esetekben, amikor a régiók rendkívül távol vannak, például Európa és Ausztrália csaknem Antipodes, földrajzilag és hálózati késéssel pedig könnyedén meghaladhatja a 250 MS-ot bármely oda-vissza.
Nem lehet felgyorsítani a fény sebességét, de csökkentheti a nagy késésű körutazások számát az adatkezelés érdekében.

Útmutató 
- [Replikációs minta][4]

### <a name="validation-reduction-and-enrichment"></a>Ellenőrzés, csökkentés és dúsítás

![Ellenőrzés, csökkentés, dúsítás](media/event-hubs-federation-overview/validation-enrichment.jpg)  

Az esemény-adatfolyamokat a saját megoldáson kívüli ügyfelek is elküldheti egy Event hub-ba. Az esemény-adatfolyamok esetében előfordulhat, hogy a külsőleg benyújtott eseményeket ellenőrizni kell egy adott séma megfelelőségének ellenőrzéséhez, illetve a nem megfelelő események eldobásához. 

Abban az esetben, ha az ügyfelek rendkívül nagy sávszélességet igényelnek, mivel ez a helyzet sok "eszközök internetes hálózata" forgatókönyv esetén a mért sávszélességgel, vagy ha az eseményeket eredetileg nem az IP-hálózatokra, korlátozott csomagméret miatt küldi el, akkor előfordulhat, hogy az eseményeknek olyan további kontextus hozzáadására van szükségük, amely további környezetet biztosít a alsóbb rétegbeli eseményekkel való használatra.

Más esetekben – különösen, ha a streamek konszolidálva vannak – a részletek kihagyása esetén előfordulhat, hogy az eseményre vonatkozó adatokat összetettebb vagy puszta méretre kell csökkenteni.

Ezen műveletek bármelyike a replikálás, a konszolidáció vagy az egyesítési folyamatok részeként jelenhet meg. 

Útmutató 
- [Szerkesztő minta][6]

### <a name="integration-with-analytics-services"></a>Integráció az analitikai szolgáltatásokkal

![Integráció az analitikai szolgáltatásokkal](media/event-hubs-federation-overview/integration.jpg)

Számos Azure-beli felhőalapú elemzési szolgáltatás, mint például a Azure Stream Analytics vagy az Azure szinapszis, az Event Hubs Azure-ból kiszolgált streamtel rendelkező vagy előre kötegelt feldolgozással működik, az Azure Event Hubs pedig lehetővé teszi az integrációt több nyílt forráskódú elemzési csomaggal, például az Apache Samza, az Apache flink, a Apache Spark és a Apache Stormokkal. 

Ha a megoldás elsődlegesen Service Bus vagy Event Grid használ, ezeket az eseményeket könnyen elérhetővé teheti az ilyen elemzési rendszerekhez, valamint az Event Hubs rögzítéssel való archiváláshoz is, ha az Event hub-ba helyezi őket. A Event Grid az [Event hub-integrációval](../event-grid/handler-event-hubs.md)natív módon is megteheti, Service Bus az [Service Bus replikációs útmutatást](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)követve.

[A Azure Stream Analytics Event Hubs közvetlenül integrálódik](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs).

Útmutató 
- [Replikációs minta][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>Az esemény-adatfolyamok összevonása és normalizálása

![Az esemény-adatfolyamok összevonása és normalizálása](media/event-hubs-federation-overview/consolidation.jpg)

A globális megoldások gyakran olyan regionális lábnyomokból állnak, amelyek nagymértékben függetlenek, mint a saját elemzési képességei, de a helyi és a globális elemzési perspektíváknak integrált perspektívára van szükségük, és ezért az azonos esemény-adatfolyamok központi összevonása szükséges a helyi perspektívában a megfelelő regionális lábnyomokban. 

A normalizálás az összevonási forgatókönyv íze, amelynek során két vagy több bejövő esemény is ugyanazokat az eseményeket hajtja végre, különböző struktúrákkal vagy különböző kódolásokkal együtt, és a leggyakrabban használt eseményeket a használatuk előtt átkódolják vagy átalakítják. 

A normalizálás olyan titkosítási munkákat is tartalmazhat, mint például a végpontok közötti titkosított adattartalom visszafejtése, valamint a különböző kulcsokkal és algoritmusokkal történő újratitkosítása az alárendelt fogyasztói közönség számára. 

Útmutató 
- [Egyesítési minta][5]
- [Szerkesztő minta][6]

### <a name="splitting-and-routing-of-event-streams"></a>Az esemény-adatfolyamok felosztása és útválasztása

![Az esemény-adatfolyamok felosztása és útválasztása](media/event-hubs-federation-overview/splitting.jpg)

Az Azure Event Hubs időnként használatban van a "közzététel – előfizetés" stílusú forgatókönyvekben, ahol a betöltött események bejövő özöne messze meghaladja a Azure Service Bus vagy Azure Event Grid kapacitását, és mindkettő natív közzétételi-előfizetési szűrési és terjesztési képességekkel rendelkezik, és előnyben részesített ehhez a mintához. 

Míg a "közzétételi-előfizetés" funkció az előfizetők számára a kívánt események kiválasztására ad lehetőséget, a felosztási minta a termelői térképi eseményeit egy előre meghatározott terjesztési modell és a kijelölt felhasználók számára, kizárólag a "saját" partíciójuk alapján kéri le. Ha az Event hub pufferbe helyezi a teljes forgalmat, egy adott partíció tartalma, amely az eredeti átviteli sebesség töredékét jelképezi, akkor a megbízható, tranzakciós, versengő fogyasztói felhasználás érdekében a várólistára replikálható.

Számos szituációban, ahol a Event Hubs elsődlegesen a régión belüli alkalmazásokon belüli áthelyezésére használják, bizonyos esetekben az eseményeket csak egyetlen partícióból lehet kijelölni, máshol is elérhetővé kell tenni őket. Ez a forgatókönyv hasonló a felosztási forgatókönyvhöz, de olyan méretezhető útválasztót is használhat, amely az Event hub-ban érkező összes üzenetet figyelembe veszi, és csak néhányat vesz igénybe az útválasztáshoz, és megkülönböztetheti az útválasztási célokat az események metaadatainak vagy a tartalom alapján. 

Útmutató
- [Útválasztási minta][7]

### <a name="log-projections"></a>Kivetítések naplózása 

![Napló kivetítése](media/event-hubs-federation-overview/log-projection.jpg)

Bizonyos esetekben érdemes lehet egy esemény ALStream-alstreaméhez tartozó legújabb értéket is elérni, és a partíciós kulcs gyakran megkülönbözteti őket. A Apache Kafka ez gyakran a "log Compacting" engedélyezésével érhető el egy témakörben, amely elveti az összes egyedi kulccsal jelölt legújabb eseményt. A log tömörítési módszer három összetett hátránya: 

- A tömörítéshez a napló folyamatos újraszervezése szükséges, ami túlságosan költséges művelet egy olyan közvetítő számára, amely csak Hozzáfűzéses számítási feladatokhoz van optimalizálva. 
- A tömörítés romboló, és nem teszi lehetővé az azonos adatfolyamok tömörített és nem tömörített perspektíváját.
- A tömörített adatfolyamok továbbra is szekvenciális hozzáférési modellel rendelkeznek, ami azt jelenti, hogy a napló kívánt értékének megkereséséhez a legrosszabb esetben a teljes naplót kell beolvasni, amely általában az itt bemutatott pontos minta megvalósítására szolgáló optimalizálásokhoz vezet: a napló tartalmának egy adatbázisba vagy gyorsítótárba való kivetítése. 

Végső soron a tömörített napló egy kulcs-érték tároló, amely a lehető legrosszabb megvalósítási lehetőség az ilyen tárolók számára. Sokkal hatékonyabb a keresések és a lekérdezések esetében, hogy a napló állandó leképezését hozza létre és használja a megfelelő kulcs-érték tárolóra vagy valamilyen más adatbázisra. 

Mivel az események nem változtathatók meg, és a sorrendet mindig egy naplóban őrzik meg, a kulcs-érték tárolóba való bejelentkezések minden vetülete mindig azonos lesz az események azonos tartományában, ami azt jelenti, hogy a folyamatosan frissített leképezések mindig mérvadó nézetet biztosítanak, és soha nem jó ok arra, hogy a napló tartalmából újraépítse azt. 

Útmutató
- [Napló kivetítése][8]

## <a name="replication-application-technologies"></a>Replikációs alkalmazások technológiái

A fenti minták megvalósításához méretezhető és megbízható végrehajtási környezet szükséges a konfigurálni és futtatni kívánt replikációs feladatokhoz. Az Azure-ban az ilyen feladatokhoz legmegfelelőbb futtatókörnyezeti környezetek állapot nélküli tevékenységek [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) az állapot-nyilvántartó replikációs feladatokhoz, és [Azure functions](../azure-functions/functions-overview.md) az állapot nélküli replikálási feladatokhoz.

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Állapot-nyilvántartó replikációs alkalmazások Azure Stream Analytics

A legjobb megvalósítási lehetőség olyan állapot-replikációs alkalmazások esetében, amelyeknek meg kell fontolniuk az események közötti kapcsolatokat, összetett eseményeket kell létrehoznia, eseményeket kell bővíteni, vagy csökkentenie kell az eseményeket, létre kell hoznia az adatok [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) összesítéseit

A Azure Stream Analyticsban olyan [feladatokat hoz létre](../stream-analytics/stream-analytics-quick-create-portal.md) , amelyek integrálják a [bemeneteket](../stream-analytics/stream-analytics-add-inputs.md) és [kimeneteket](../stream-analytics/stream-analytics-define-outputs.md) , és integrálják az adatokat a bemeneteken keresztül a kimeneteken elérhetővé tett eredményeket eredményező [lekérdezésekkel](/stream-analytics-query/stream-analytics-query-language-reference) .

A lekérdezések az SQL- [lekérdezés nyelvén](/stream-analytics-query/stream-analytics-query-language-reference) alapulnak, és az adatátviteli adatokat egyszerűen szűrheti, rendezheti, összesítheti és csatlakoztathatja egy adott időszakban. Ezt az SQL nyelvet a [JavaScript](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) és a [C# felhasználó által definiált függvények (UDF)](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md)használatával is kiterjesztheti. Az összesítési műveletek egyszerű nyelvi szerkezeteken és/vagy konfigurációkon keresztül történő végrehajtásával egyszerűen beállíthatja az események rendezésének beállításait és időtartamát a Windowsban.

Minden feladathoz tartozik egy vagy több kimenet az átalakított adatokhoz, és szabályozható, hogy mi történik az elemzett információkra adott válaszként. Megteheti például a következőt:

- Adatokat küldhet a szolgáltatásoknak, például a Azure Functionsnak, Service Bus témákat vagy várólistákat a kommunikáció vagy az egyéni munkafolyamatok alsóbb rétegbeli elindításához.
- Adatküldés egy Power BI Irányítópultra a valós idejű irányítópultok számára.
- Az adatok tárolása más Azure Storage-szolgáltatásokban (például Azure Data Lake, Azure szinapszis Analytics stb.) a Batch-elemzések elvégzéséhez vagy a gépi tanulási modellek betanításához nagyon nagy, indexelt készleteken alapuló korábbi adatok alapján.
- Az adatbázisokban ([SQL Database](../stream-analytics/sql-database-output.md), [Cosmos db](../stream-analytics/azure-cosmos-db-output.md) ) tárolja a kivetítéseket (más néven "jelentős nézetek").

### <a name="stateless-replication-applications-in-azure-functions"></a>Állapot nélküli replikációs alkalmazások Azure Functions

Az állapot nélküli replikálási feladatokhoz, ahol az eseményeket a hasznos adattartalmak figyelembevétele nélkül kívánja továbbítani, vagy ha nem szeretné, hogy az események (a relatív sorrendtől függetlenül) kapcsolatainak figyelembe vétele nélkül is feldolgozza őket, használhatja a Azure Functions, amely hatalmas rugalmasságot biztosít.

Azure Functions az [azure Event Hubs](../azure-functions/functions-bindings-event-hubs.md), az [Azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), a [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md)és az [Azure Queue Storage](../azure-functions/functions-bindings-storage-queue.md)számára előre összeállított, skálázható eseményindítókkal és kimeneti kötésekkel rendelkezik, valamint egyéni bővítményeket [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)és [Apache Kafkaekhez](https://github.com/azure/azure-functions-kafka-extension). A legtöbb eseményindító dinamikusan alkalmazkodik az adatátviteli igényekhez azáltal, hogy a dokumentált metrikák alapján a párhuzamosan végrehajtás alatt álló példányok számát megnöveli. 

A naplózási kivetítések létrehozásához Azure Functions támogatja [Cosmos db](../azure-functions/functions-bindings-cosmosdb-v2-output.md) és az [Azure Table Storage](../azure-functions/functions-bindings-storage-table-output.md)kimeneti kötéseit.

Azure Functions futtatható az [Azure felügyelt identitása](../active-directory/managed-identities-azure-resources/overview.md) alatt, és ezzel a szolgáltatással a hitelesítő adatok konfigurációs értékeit a szigorúan hozzáférés-vezérlésű tárolóban tárolhatja [Azure Key Vaulton](../key-vault/general/overview.md)belül.

Azure Functions továbbá lehetővé teszi, hogy a replikációs feladatok közvetlenül integrálva legyenek az Azure-beli virtuális hálózatokkal és [szolgáltatás-végpontokkal](../virtual-network/virtual-network-service-endpoints-overview.md) az összes Azure Messaging-szolgáltatáshoz, és a [Azure monitor](../azure-monitor/overview.md)könnyen integrálhatók.

A Azure Functions fogyasztási csomaggal az előre elkészített eseményindítók akár nulla is lehetnek, míg a replikáláshoz nem állnak rendelkezésre üzenetek, ami azt jelenti, hogy a konfiguráció méretezése a biztonsági mentésre készen áll. a használati terv használatának hátránya, hogy a replikálási feladatok "felébrednek" késése jelentősen meghaladja azokat a üzemeltetési terveket, amelyeken az infrastruktúra fut.  

Ezzel szemben a leggyakoribb replikációs motorok az üzenetküldéshez és az események elvégzéséhez, például a Apache Kafka [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) megköveteli egy üzemeltetési környezet megadását és a replikációs motor méretezését. Ez magában foglalja a biztonsági és hálózati funkciók konfigurálását és integrálását, valamint a figyelési adatok áramlását, és így továbbra sem lehet egyéni replikációs feladatokat beszúrni a folyamatba. 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>Választás a Azure Functions és a Azure Stream Analytics között

A Azure Stream Analytics (ASA) a legjobb megoldás, ha a replikálás során fel kell dolgoznia az események hasznos adatait. Az ASA az eseményeket egyenként másolhatja, vagy olyan összesítéseket hozhat létre, amelyek a továbbítás előtt lerövidítik az esemény-adatfolyamok információit. Az Azure Blob Storage-ban vagy Azure SQL Databaseban tárolt [hivatkozási adatforrások kiegészítéseként könnyen](../stream-analytics/stream-analytics-use-reference-data.md) kihasználható, anélkül, hogy ezeket az adatstreambe kellene importálnia.

Az ASA használatával könnyedén hozhat létre állandó és jelentős méretű nézeteket a streamek számára a Hyper-Scale-adatbázisokban. Ez a nagy teljesítményű megközelítés az Apache Kafka és a Kafka-streamek illékony, ügyféloldali táblázatos kivetítésének "a log Compacting" modellje. 

Az ASA képes a [CSV-, JSON-és Apache Avro-formátumokban](../stream-analytics/stream-analytics-parsing-json.md) kódolt hasznos adatok feldolgozására, és bármilyen más formátumhoz csatlakoztathat [Egyéni deszerializáló](../stream-analytics/custom-deserializer.md) adatokat.

Az összes olyan replikációs feladathoz, ahol az esemény-adatfolyamokat "állapotba" kell másolni, és nem kell megérinteni a hasznos adatokat, vagy ha egy útválasztót kell megvalósítani, a titkosítási feladatokat kell végrehajtania, módosítania kell a hasznos adatok kódolását, vagy ha más esetben teljes mértékben meg kell határoznia az adatfolyam tartalmát, Azure Functions a legjobb megoldás.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben számos összevonási mintát ismertetünk, és kifejtjük Azure Functions szerepét az Azure-beli esemény-és üzenetkezelési replikálási futtatókörnyezetként.

Ezután érdemes elolvasnia, hogyan állíthat be egy replikátor-alkalmazást Azure Stream Analytics vagy Azure Functions, majd hogyan replikálhatja az esemény-folyamatokat a Event Hubs és a különböző egyéb esemény-és üzenetkezelő rendszerek között:

- [Eseményreplikációs feladatminták][10]
- [Az adatfeldolgozás Azure Stream Analytics][9]
- [Event replikátor alkalmazások Azure Functions][1]
- [Események replikálása Event Hubs között][2]
- [Események replikálása a Azure Service Busba][3]
- [Apache Kafka MirrorMaker használata Event Hubs][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md