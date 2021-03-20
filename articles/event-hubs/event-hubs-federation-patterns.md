---
title: Esemény-replikációs feladat mintái – Azure Event Hubs | Microsoft Docs
description: Ez a cikk részletes útmutatást nyújt az adott esemény-replikációs feladat-mintázatok megvalósításához
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 438964c228f060dede93abf582c9504b698db8b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934611"
---
# <a name="event-replication-tasks-patterns"></a>Esemény-replikációs feladatok mintái

Az [összevonás áttekintése](event-hubs-federation-overview.md) és a [replikátor függvények áttekintése](event-hubs-federation-replicator-functions.md) ismerteti a replikációs feladatok indoklását és alapvető elemeit, és azt javasoljuk, hogy a jelen cikk folytatása előtt Ismerkedjen meg velük.

Ebben a cikkben részletesen ismertetjük a megvalósítási útmutatót az Áttekintés szakaszban Kiemelt minták közül.

## <a name="replication"></a>Replikálás

A replikációs minta az eseményeket egy adott esemény központból a következőre másolja, vagy egy Event hub-ból egy másik célhelyre, például egy Service Bus-várólistára. Az események továbbítása az esemény hasznos adatainak módosítása nélkül történik.

Ennek a mintának a megvalósítását a [Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) és az [esemény replikációja Event Hubs és Service Bus minták között](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) , valamint a [Apache Kafka MirrorMaker használata Event Hubs](event-hubs-kafka-mirror-maker-tutorial.md) oktatóanyagban az adatok Apache Kafka-átvitelszervezőből Event Hubsba való replikálásának konkrét esetére.

### <a name="streams-and-order-preservation"></a>Adatfolyamok és megrendelés megőrzése

A Azure Functions vagy Azure Stream Analytics használatával történő replikáció nem célja, hogy biztosítsa a forrás-esemény hub pontos 1:1-klónjának létrehozását egy cél Event hub-ba, de az olyan események relatív sorrendjének megőrzésére összpontosít, amelyeken az alkalmazás igényli. Az alkalmazás úgy kommunikál, hogy a kapcsolódó eseményeket ugyanazzal a partíciós kulccsal csoportosítja, és a Event Hubs egyazon partíciós [kulccsal rendelkező üzeneteket rendezi egymás után ugyanabban a partícióban](event-hubs-features.md#partitions).

> [!IMPORTANT] 
> Az "eltolás" információ minden egyes Event hub esetében egyedi, és az azonos eseményekhez tartozó eltolások az Event hub-példányok között eltérőek lesznek. Ha egy pozíciót szeretne megkeresni egy másolt adatfolyamban, használja az időalapú eltolásokat, és tekintse át a [propagált szolgáltatáshoz hozzárendelt metaadatokat](#service-assigned-metadata).
>
> Az időalapú eltolások egy adott időpontban indítja el a fogadót:
> - *EventPosition. FromStart ()* – az összes megőrzött adat olvasása újra.
> - *EventPosition. FromEnd ()* – az összes új adatok olvasása a kapcsolatok időpontjában.
> - *EventPosition. FromEnqueuedTime (datetime)* – az összes adat egy adott dátumtól és időponttól kezdődően.
>
> A EventProcessor állítsa be a pozíciót a EventProcessorOptions InitialOffsetProvider. A többi fogadó API-val a pozíció a konstruktoron keresztül halad át. 


A Azure Functions alapuló útmutatóban használt [,](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) előre elkészített Replication functions-segítők biztosítják, hogy a forrás-partícióról beolvasott esemény-adatfolyamok bekerülnek a cél Event hubhoz egy kötegként az eredeti adatfolyamban és ugyanazzal a partíciós kulccsal.

Ha a forrás és a cél esemény hub partícióinak száma azonos, a célhelyen lévő összes adatfolyam ugyanahhoz a partícióhoz lesz hozzárendelve, mint a forrásban.
Ha a partíciók száma eltér, ami az alábbi táblázatban leírt további mintákra is kiterjed, a leképezés eltérő lesz, de a streamek mindig együtt és sorrendben vannak tárolva.

A különböző streamekhez vagy független eseményekhez tartozó események relatív sorrendje a megcélzott partícióban található partíciós kulcs nélkül mindig különbözhet a forrás partíciótól.

### <a name="service-assigned-metadata"></a>Szolgáltatáshoz rendelt metaadatok

A forrás-esemény központból beszerzett esemény szolgáltatáshoz hozzárendelt metaadatait, az eredeti sorba helyezni időt, a sorszámot és az eltolást a rendszer új, szolgáltatáshoz rendelt értékekre cseréli a cél Event hub-ban, de a [segítő függvények](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication), a mintákban található replikációs feladatok megőrzik az eredeti értékeket a felhasználó tulajdonságaiban: `repl-enqueue-time` (ISO8601 karakterlánc), `repl-sequence` , `repl-offset` .

Ezek a tulajdonságok karakterlánc típusúak, és tartalmazzák a megfelelő eredeti tulajdonságok sztringesített értékét. Ha az eseményt többször továbbítják, az azonnali forrás szolgáltatáshoz hozzárendelt metaadatait a rendszer hozzáfűzi a már meglévő tulajdonságokhoz, és a pontosvesszővel elválasztott értékeket tartalmaz.

### <a name="failover"></a>Feladatátvétel

Ha a replikálást vész-helyreállítási célokra használja, a Event Hubs szolgáltatásban vagy a hálózati megszakításokkal szembeni védelem érdekében az ilyen meghibásodási forgatókönyvek esetében a feladatátvételt az egyik esemény központból a következőre kell elvégeznie, amely a másodlagos végpont használatára utasítja a gyártókat és/vagy a fogyasztókat.

Az összes feladatátvételi forgatókönyv esetében feltételezhető, hogy a névterek szükséges elemei szerkezetileg megegyeznek, ami azt jelenti, hogy a Event Hubs és a fogyasztói csoportok azonos névvel rendelkeznek, és a közös hozzáférésű aláírási szabályok és/vagy szerepköralapú hozzáférés-vezérlési szabályok ugyanúgy vannak beállítva. A másodlagos névterek létrehozásához és frissítéséhez kövesse a [névterek áthelyezésére](move-across-regions.md) és a karbantartási lépés kihagyása című témakör útmutatását.

Ha szeretné kényszeríteni a termelőket és a fogyasztókat a váltásra, meg kell adnia, hogy mely névtér használható a kereséshez egy könnyen elérhető és frissíthető helyen. Ha a termelők vagy a fogyasztók gyakori vagy állandó hibákba ütköznek, akkor a helyükön kell megjelenniük, és módosítaniuk kell a konfigurációt. A konfiguráció megosztása számos módon megosztható, de a következő két lehetőség van: DNS-és fájlmegosztás.

#### <a name="dns-based-failover-configuration"></a>DNS-alapú feladatátvételi konfiguráció

Az egyik jelölt módszer a DNS SRV-rekordok információinak megtartása egy Ön által felügyelt DNS-ben, és a megfelelő Event hub-végpontokra mutat. 

> [!IMPORTANT] 
> Ne feledje, hogy a Event Hubs nem teszi lehetővé, hogy a végpontok közvetlenül CNAME rekordokkal legyenek felhasználva, ami azt jelenti, hogy a DNS-t rugalmas keresési mechanizmusként fogja használni a végponti címekhez, és nem közvetlenül oldja fel az IP-cím adatait.

Tegyük fel, hogy Ön a tartomány tulajdonosa, `example.com` és az alkalmazása esetében egy zóna `test.example.com` . Két alternatív Event Hubs esetén két további beágyazott zónát, valamint egy SRV-rekordot fog létrehozni.

Az SRV-rekordok a Common Convention (két végponttal ellátott) előtagot követve, a (z) és a (z) 5671-as porton, az egyik az 443-es porton található AMQP-en, a másik pedig a `_azure_eventhubs._amqp` zónához tartozó névtér Event Hubs végpontján mutatnak.

| Zóna                   | SRV-rekord                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

Az alkalmazás zónájában létre fog hozni egy olyan CNAME-bejegyzést, amely az elsődleges esemény hubhoz tartozó alárendelt zónára mutat:

| CNAME-rekord                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

Ha olyan DNS-ügyfelet használ, amely lehetővé teszi a CNAME és SRV rekordok explicit lekérdezését (a Java és a .NET beépített ügyfelei csak a nevek egyszerű feloldását teszik lehetővé IP-címekre), akkor a kívánt végpontot fel lehet oldani. Ha például a [DnsClient.net](https://dnsclient.michaco.net/), a lookup függvény a következő:

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

A függvény a zóna 5671-as portján regisztrált cél gazdagép nevét adja vissza, amely jelenleg a CNAME néven van alias.

A feladatátvétel végrehajtásához szerkesztenie kell a CNAME rekordot, és azt a másodlagos zónára kell mutatnia.

A DNS használatának előnye, és különösen [Azure DNS](../dns/dns-overview.md), hogy az Azure DNS információ globálisan replikálódik, ezért az egyrégiós kimaradások elleni ellenálló képesség.

Ez az eljárás hasonlít a [Event Hubs geo-Dr](event-hubs-geo-dr.md) működéséhez, de teljes mértékben a saját irányítása alá tartozik, és aktív/aktív forgatókönyvekkel is működik.

#### <a name="file-share-based-failover-configuration"></a>Fájlmegosztás alapú feladatátvételi konfiguráció

A DNS a végponti adatok megosztására való használatának legegyszerűbb alternatívája az elsődleges végpont neve egy egyszerű szövegfájlba, és a fájl kiszolgálása egy olyan infrastruktúrából, amely robusztus az kimaradások ellen, és továbbra is lehetővé teszi a frissítések használatát.

Ha már futtatta a globális rendelkezésre állással és a tartalom-replikálással rendelkező, magasan elérhető webhelyes infrastruktúrát, adjon hozzá egy ilyen fájlt, és tegye közzé újra a fájlt, ha kapcsolóra van szükség.

> [!CAUTION]
> Ebben a példában csak a végpont nevét kell közzétennie, nem pedig egy teljes, a titkokat is tartalmazó karakterláncot.

#### <a name="extra-considerations-for-failing-over-consumers"></a>További szempontok a felhasználók feladatátvételéhez

Az Event hub-felhasználók esetében a feladatátvételi stratégia további szempontjai az esemény-feldolgozó igényeitől függenek.

Ha van olyan katasztrófa, amely a rendszer újraépítési folyamatát igényli, beleértve az adatbázisokat, a biztonsági mentési adatokból, és az adatbázisokat közvetlenül vagy közbenső feldolgozás útján, az Event hub-ban tárolt események alapján, akkor vissza kell állítania a biztonsági mentést, majd újra kell indítani az eseményeket a rendszerbe az adatbázis biztonsági másolatának létrehozásakor, nem pedig az eredeti rendszer megsemmisítésének pillanatától kezdve

Ha a hiba csak a rendszer egy szeletét érinti, vagy valójában csak egyetlen Event hub, amely nem érhető el, valószínűleg folytatni kívánja az események feldolgozását, ha a feldolgozás megszakadt.

Ha a megfelelő Azure SDK-val kapcsolatos forgatókönyvet szeretné megvalósítani, [hozzon létre egy új ellenőrzőpont-tárolót](event-processor-balance-partition-load.md#checkpointing) , és adjon meg egy kezdeti partíciós pozíciót annak az _időbélyegzőnek_ a alapján, amelynek a feldolgozását folytatni kívánja.

Ha továbbra is hozzáfér az Event hub ellenőrzőpont-tárolójához, akkor a fentiekben ismertetett [propagált metaadatok](#service-assigned-metadata) segítenek kihagyni azokat az eseményeket, amelyeket már kezeltek, és pontosan onnan folytatják, hogy hol hagyták el.

## <a name="merge"></a>Egyesítés

Az egyesítési minta egy vagy több olyan replikációs feladattal rendelkezik, amely egy adott célt mutat, valószínűleg párhuzamosan a normál termelőkkel is, és az eseményeket ugyanarra a célra küldi.

A következő pattok variációi:

- Két vagy több replikációs függvény egyidejűleg szerzi be a különböző forrásokból származó eseményeket, és elküldi őket ugyanarra a célra.
- Egy újabb replikálási függvény, amely egy forrásból szerzi be az eseményeket, és a célt közvetlenül a gyártók is használják.
- Az előző minta, de két vagy több Event Hubs között tükrözött, így a Event Hubs azonos streameket tartalmaznak, függetlenül attól, hogy hol állították elő az eseményeket.

Az első két minta variációja triviális, és nem különbözik az egyszerű replikálási feladatoktól.

Az utolsó forgatókönyvhöz szükséges, hogy a már replikált események ne legyenek replikálva. A technikát a [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) -minta mutatja be és ismerteti.

## <a name="editor"></a>Szerkesztő

A szerkesztő minta a [replikálási](#replication) mintára épül, de a továbbított üzenetek a továbbítás előtt módosulnak. 

Ilyen módosítások például a következők:

- **_Átkódolás_** – ha az esemény tartalma (más néven "szövegtörzs" vagy "hasznos adat") az _Apache Avro_ -formátum vagy valamilyen saját szerializálási formátum használatával kódolt forrásból érkezik, azonban a cél tulajdonosának a célja, hogy a tartalom _JSON_ -kódolású legyen, egy átkódoló replikációs feladat először deszerializálja az _Apache Avro_ adattartalmát a memóriában lévő Object gráfba, majd szerializálja a gráfot a továbbított esemény _JSON_ -formátumára. A transcoding a **tartalom tömörítési** és kibontási feladatait is tartalmazza.
- **_Átalakítás_** – a strukturált adatmennyiséget tartalmazó események esetében szükség lehet az ilyen adatmennyiségek átformálására, hogy az alsóbb rétegbeli fogyasztók könnyebben használhatóvá váljon. Ez magában foglalhat olyan munkát, mint a beágyazott struktúrák összeolvasztása, a felesleges adatelemek metszése vagy a hasznos adatok átalakítása, hogy pontosan illeszkedjenek az adott sémához.
- **_Kötegelt feldolgozás_** – előfordulhat, hogy az események kötegekben (több, egyetlen átvitelben lévő eseményekben) érkeznek a forrásokból, de egy célhoz kell továbbítani őket, vagy fordítva. Egy feladat ezért több eseményt is továbbíthat egyetlen bemeneti esemény alapján, vagy összesítheti azokat az eseményeket, amelyek ezután együtt kerülnek átvitelre.
- **_Ellenőrzés_** – előfordulhat, hogy a külső forrásokból származó események adatait gyakran ellenőrizni kell, hogy azok megfelelnek-e a szabályoknak, mielőtt azok továbbítva lesznek. A szabályok sémák vagy kódok használatával adhatók meg. Azok az események, amelyek nem felelnek meg a megfelelőségnek, eldobásra kerülhetnek, a naplókban feljegyzett problémával, vagy egy speciális célhelyre továbbítva a további kezelés érdekében.
- A **_dúsítás_** – egyes forrásokból származó esemény-adatok esetében a további kontextus megkövetelése szükséges ahhoz, hogy a megcélzott rendszerekben használható legyen. Ez magában foglalhatja a hivatkozási adatok megkeresését és az adatok beágyazását az eseményekkel, illetve a replikálási feladat által ismert forrással kapcsolatos információk hozzáadását, de az eseményekben nem szerepelnek.
- **_Szűrés_** – előfordulhat, hogy egyes forrásokból érkező eseményeket bizonyos szabályok alapján vissza kell tartani a célhelyről. A szűrő ellenőrzi az eseményt egy szabályhoz, és eldobja az eseményt, ha az esemény nem felel meg a szabálynak. A duplikált események kiszűrése bizonyos feltételek betartásával és a későbbi események azonos értékekkel való eldobásával a szűrés egy formája.
- **_Titkosítás_** – előfordulhat, hogy a replikációs feladatnak vissza kell fejtenie a forrásból érkező tartalmat, és/vagy titkosítania kell a tartalmat a célhelyre, és/vagy a tartalom és a metaadatok integritását ellenőrizni kell az eseményen végrehajtott aláíráshoz képest, vagy csatolnia kell az aláírást.
- **_Igazolás_** – a replikálási feladat a digitális aláírással potenciálisan védett metaadatokat is csatolhat egy olyan eseményhez, amely igazolja, hogy az eseményt egy adott csatornán vagy adott időpontban fogadták.
- **_Láncolás_** – egy replikációs feladat aláírásokat alkalmazhat az események adatfolyamára úgy, hogy az adatfolyam integritása védett, és a rendszer hiányzó eseményeket észlel.

Az átalakítási, a kötegelt és a dúsítási minták általában [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) feladatokkal valósíthatók meg legjobban. 

Ezek a minták a Azure Functions használatával valósíthatók meg, a [Event Hubs trigger](../azure-functions/functions-bindings-event-hubs-trigger.md) használatával az események beszerzéséhez és az [Event hub kimeneti kötéséhez](../azure-functions/functions-bindings-event-hubs-output.md) a kézbesítéshez.

## <a name="routing"></a>Útválasztás

Az útválasztási minta a [replikálási](#replication) mintára épül, de ahelyett, hogy egy forrást és egy célt használ, a replikálási feladat több célként is szerepel a C# nyelven:

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

Az útválasztási függvény figyelembe veszi az üzenet metaadatait és/vagy az üzenet tartalmát, majd kiválaszthatja az egyik elérhető célhelyet a küldéshez.

Azure Stream Analytics a több kimenet definiálásával, majd kimenetek lekérdezésének végrehajtásával is megvalósítható.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Napló kivetítése

A napló-kivetítési minta az esemény-adatfolyamot egy indexelt adatbázisra, a rekordokat pedig az adatbázisban naplózza. Az eseményeket általában ugyanahhoz a gyűjteményhez vagy táblához adja hozzá a rendszer, az Event hub partíciós kulcsa pedig annak az elsődleges kulcsnak a része lesz, amely a rekord egyedivé tételét keresi.

A napló kivetítése létrehozhat egy idősoros történészet az esemény adatairól vagy egy tömörített nézetből, amely minden partíciós kulcs esetében csak a legújabb eseményt őrzi meg. A céladatbázis alakja végső soron Ön és az alkalmazás igényeinek megfelelően működik. Ezt a mintát "esemény-beszerzésnek" is nevezzük.

> [!TIP]
> Azure Stream Analytics a [Azure SQL Databaseba](../stream-analytics/sql-database-output.md) és a [Azure Cosmos DBba](../stream-analytics/azure-cosmos-db-output.md) könnyedén hozhat létre naplózási előrejelzéseket, és így érdemes ezt a beállítást használnia.

A következő Azure-függvény az Event hub egy Azure CosmosDB-gyűjteménybe tömörített tartalmát feltervezi.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

- [Event replikátor alkalmazások Azure Functions][1]
- [Események replikálása Event Hubs között][2]
- [Események replikálása a Azure Service Busba][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
