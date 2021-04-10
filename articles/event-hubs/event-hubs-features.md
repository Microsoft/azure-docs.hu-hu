---
title: A funkciók áttekintése – Azure Event Hubs | Microsoft Docs
description: Ez a cikk részletesen ismerteti az Azure Event Hubs funkcióit és terminológiáját.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: da59d62cb7060389ea94b3af5e6f66a4b6347d7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602621"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Az Azure Event Hubs funkciói és terminológiája

Az Azure Event Hubs egy skálázható esemény-feldolgozási szolgáltatás, amely nagy mennyiségű eseményt és adatot tölt be és dolgoz fel, alacsony késéssel és magas megbízhatósággal. Lásd: [Mi az Event Hubs?](./event-hubs-about.md) a magas szintű áttekintéshez.

Ez a cikk az [áttekintő cikkben](./event-hubs-about.md)található információkra épül, valamint a Event Hubs összetevőkkel és szolgáltatásokkal kapcsolatos technikai és megvalósítási részleteket tartalmaz.

> [!TIP]
> [A **Apache Kafka** -ügyfelek protokolljának támogatása](event-hubs-for-kafka-ecosystem-overview.md)  (>= 1,0 verzió) olyan hálózati végpontokat biztosít, amelyek lehetővé teszik, hogy az alkalmazások a Event Hubs használatára a Apache Kafkat használják bármely ügyféllel. A legtöbb meglévő Kafka-alkalmazás egyszerűen konfigurálható úgy, hogy a Kafka-fürt rendszertöltő kiszolgálója helyett egy Event hub-névtérre mutasson. 
>
>A Cost, a működési erőkifejtés és a megbízhatóság szempontjából az Azure Event Hubs nagyszerű alternatíva a saját Kafka-és Zookeeper-fürtök üzembe helyezéséhez és üzemeltetéséhez, valamint az Azure-ban nem natív, a szolgáltatásként kínált Kafka-szolgáltatásokhoz. 
>
> Amellett, hogy ugyanazokat az alapvető funkciókat kapja, mint a Apache Kafka-közvetítő, az Azure Event hub olyan funkcióit is elérheti, mint az automatikus kötegelt feldolgozás és az archiválás a [Event Hubs rögzítés](event-hubs-capture-overview.md), az automatikus skálázás és a kiegyensúlyozás, a vész-helyreállítási és a költséghatékony rendelkezésre állási zónák támogatása, a rugalmas és biztonságos hálózati integráció, valamint a többprotokollos támogatás, beleértve a tűzfal-barát AMQP-


## <a name="namespace"></a>Névtér
Az Event Hubs névtér DNS integrált hálózati végpontokat és számos hozzáférés-vezérlési és hálózati integrációs felügyeleti funkciót biztosít, például az [IP-szűrést](event-hubs-ip-filtering.md), a [virtuális hálózati szolgáltatás végpontját](event-hubs-service-endpoints.md)és a [privát hivatkozást](private-link-service.md) , valamint a több Event hub-példány (vagy témakörök, a Kafka-szóhasználatában) egyikének felügyeleti tárolója.

## <a name="event-publishers"></a>Esemény-közzétevők

Minden olyan entitás, amely adatokat küld az Event hub-nak, az *esemény-közzétevő* (azonos értelemben használt *esemény*). Az esemény-közzétevők a HTTPS vagy a AMQP 1,0 vagy a Kafka protokoll használatával tehetnek közzé eseményeket. Az esemény-közzétevők Azure Active Directory alapú engedélyezést használnak a OAuth2 által kiadott JWT-jogkivonatokkal, vagy egy Event Hub-specifikus közös hozzáférésű aláírás (SAS) jogkivonat közzétételi hozzáférését.

### <a name="publishing-an-event"></a>Esemény közzététele

A AMQP 1,0, a Kafka protokoll vagy a HTTPS használatával tehet közzé eseményt. A Event Hubs szolgáltatás a [REST API](/rest/api/eventhub/) és a [.net](event-hubs-dotnet-standard-getstarted-send.md), a [Java](event-hubs-java-get-started-send.md), a [Python](event-hubs-python-get-started-send.md), a [JavaScript](event-hubs-node-get-started-send.md)és a [Go](event-hubs-go-get-started-send.md) ügyféloldali kódtárainak használatával teszi közzé az eseményeket az Event hub-ban. Egyéb futtatókörnyezetek és platformok esetén használhatja bármelyik AMQP 1.0-ügyfelet, ilyen például az [Apache Qpid](https://qpid.apache.org/). 

Az AMQP vagy HTTPS használata a használati forgatókönyvtől függ. Az AMQP használatához ki kell alakítani egy állandó kétirányú szoftvercsatornát az TLS vagy SSL/TLS mellett. A AMQP magasabb hálózati költségekkel rendelkezik a munkamenet inicializálásakor, azonban a HTTPS minden kérelem esetében további TLS-terhelést igényel. A AMQP jelentősen nagyobb teljesítményt biztosít a gyakori közzétevők számára, és az aszinkron közzétételi kóddal való használatnál sokkal alacsonyabb késést érhet el.

Az eseményeket egyenként vagy kötegelten teheti közzé. Egyetlen kiadvány legfeljebb 1 MB méretű lehet, függetlenül attól, hogy egyetlen eseményről vagy kötegről van szó. A küszöbértéknél nagyobb közzétételi eseményeket a rendszer elutasítja. 

A Event Hubs átviteli sebesség a partíciók és az átviteli egység kiosztása (lásd alább) használatával méretezhető. Ajánlott eljárás a kiadók számára, hogy továbbra is tisztában legyenek az Event hub számára kiválasztott particionálási modellel, és csak olyan *partíciós kulcsot* adjon meg, amely a kapcsolódó események konzisztens hozzárendelésére szolgál ugyanahhoz a partícióhoz.

![Partíciós kulcsok](./media/event-hubs-features/partition_keys.png)

Event Hubs biztosítja, hogy a partíciós kulcs értékeit megosztó összes esemény együtt, az érkezési sorrendben legyen tárolva. Ha a partíciókulcsok használata közzétevői házirendekkel együtt történik, a közzétevő identitásának és a partíciókulcs értékének egyeznie kell. Különben hiba történik.

### <a name="event-retention"></a>Esemény-megőrzés

A közzétett események el lesznek távolítva az Event hub-ból egy konfigurálható, időzített adatmegőrzési házirend alapján. Íme néhány fontos pont:

- Az **alapértelmezett** érték és a lehető **legrövidebb** megőrzési időtartam **1 nap (24 óra)**.
- Event Hubs **standard** esetén a maximális megőrzési időtartam **7 nap**. 
- Event Hubs **dedikált** esetében a maximális megőrzési időtartam **90 nap**.
- Ha megváltoztatja a megőrzési időtartamot, az minden üzenetre vonatkozik, beleértve az Event hub-ban már szereplő üzeneteket is. 

Event Hubs megőrzi az összes partícióra vonatkozó beállított megőrzési idő eseményeit. Az események automatikusan törlődnek a megőrzési időtartam elérésekor. Ha egy nap megőrzési időtartamot ad meg, az esemény az elfogadás után nem lesz elérhető pontosan 24 órával. Az eseményeket nem lehet explicit módon törölni. 

Ha az eseményeket az engedélyezett megőrzési időtartamon túl kell archiválni, automatikusan az [Azure Storage-ban vagy a Azure Data Lakeban is tárolhatók a Event Hubs rögzítési funkciójának bekapcsolásával](event-hubs-capture-overview.md), és ha ilyen mély archívumokat kell keresnie vagy elemezni, [egyszerűen importálhatja őket az Azure szinapszisba](store-captured-data-data-warehouse.md) vagy más hasonló áruházakba és elemzési platformokba. 

A Event Hubs "az adatok megőrzésének időbeli korlátozásának oka az, hogy megakadályozza a nagy mennyiségű korábbi ügyféladatok betöltését egy olyan mély tárolóban, amely csak egy időbélyeg által indexelt, és csak szekvenciális hozzáférést tesz lehetővé. Az építészeti filozófia itt azt mutatja be, hogy a múltbeli adatindexelésnek és a Event Hubs vagy Kafka által nyújtott valós idejű eseményeknek jobb közvetlen hozzáférésre van szüksége. Az Event stream-motorok nem alkalmasak arra, hogy az események beszerzéséhez szükséges adattavak vagy hosszú távú archívumok szerepét játsszák. 
 

> [!NOTE]
> A Event Hubs valós idejű esemény-adatfolyam-motor, és nem az adatbázis és/vagy állandó tárolóként használható a végtelenül tartott esemény-adatfolyamok számára. 
> 
> Minél mélyebben tekinti át az esemény-adatfolyamok előzményeit, annál több kiegészítő indexre lesz szüksége az adott stream egy adott történelmi szeletének megkereséséhez. Az esemény-adattartalom és az indexelés ellenőrzése nem a Event Hubs (vagy Apache Kafka) szolgáltatási hatókörén belül van. Az adatbázisok és a speciális elemzési tárolók és motorok, például a [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md), a [Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) és az [Azure szinapszis](../synapse-analytics/overview-what-is.md) , ezért sokkal jobban illeszkednek a történelmi események tárolására.
>
> A [Event Hubs Capture](event-hubs-capture-overview.md) közvetlenül az Azure Blob Storage és a Azure Data Lake Storage használatával integrálódik, és az adott integráción keresztül lehetővé teszi, hogy az [események közvetlenül az Azure szinapszisba haladjanak](store-captured-data-data-warehouse.md).
>
> Ha az alkalmazáshoz tartozó [esemény-beszerzési](/azure/architecture/patterns/event-sourcing) mintát szeretné használni, a pillanatkép-stratégiát a Event Hubs megőrzési korlátaival igazíthatja. A nem célja, hogy a nyers eseményekből származó, az időponttól kezdődően kiépített jelentős nézeteket újraépítsen. Ha az alkalmazása egy ideig még egy ideje éles környezetben, és a kivetítés-készítőnek évek óta változási események történnek, akkor a legújabb és a folyamatos módosítások elvégzése során is érdemes lenne megsajnálni ezt a stratégiát. 


### <a name="publisher-policy"></a>Közzétevői házirend

Az Event Hubs lehetővé teszi az esemény-közzétevők részletes szabályozását a *közzétevői házirendek* révén. A közzétevői házirendek olyan futásidejű szolgáltatások, amelyek célja, hogy nagy számú független esemény-közzétevőt tegyen lehetővé. A közzétevői házirendek használatával mindegyik közzétevő a saját egyedi azonosítóját használja, amikor eseményeket tesz közzé egy eseményközpontban az alábbi mechanizmust használva:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

Nem kell előre létrehoznia a közzétevők neveit, azoknak azonban egyezniük kell az esemény közzétételekor használt SAS-tokennel a független közzétevő-azonosságok biztosítása érdekében. A közzétevői házirendek használatakor a **PartitionKey** értéke a közzétevő neve lesz. A megfelelő működéshez ezeknek az értékeknek egyezniük kell.

## <a name="capture"></a>Rögzítés

A [Event Hubs Capture](event-hubs-capture-overview.md) lehetővé teszi, hogy automatikusan rögzítse a streaming-adatEvent Hubs, és mentse azt egy blob Storage-fiók vagy egy Azure Data Lake szolgáltatásfiók számára. Engedélyezheti a rögzítést a Azure Portalból, és megadhatja a rögzítés végrehajtásához szükséges minimális méretet és időt. Event Hubs Capture használatával megadhatja a saját Azure Blob Storage-fiókját és-tárolóját, vagy Azure Data Lake szolgáltatásfiókot, amely az egyik a rögzített adattárolók tárolására szolgál. A rögzített adatértékek Apache Avro formátumban íródnak.

## <a name="partitions"></a>Partíciók
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS-tokenek

A Event Hubs *megosztott hozzáférési aláírásokat* használ, amelyek a névtér és az Event hub szintjén érhetők el. A SAS-tokent egy SAS-kulcsból hozza létre a rendszer, és egy URL SHA-kivonata egy meghatározott formátumban kódolva. A kulcs neve (házirend) és a token együttes használatával az Event Hubs képes újra létrehozni a kivonatot, és így azonosítani a küldőt. Az esemény-közzétevők SAS-tokenje általában csak egy adott Event hub **küldési** jogosultságokkal hozható létre. Ez a SAS-tokenes URL-mechanizmus az alapja a közzétevők a közzétevői házirendben bevezetett azonosításának. További információ a SAS használatával kapcsolatban: [Shared Access Signature Authentication with Service Bus](../service-bus-messaging/service-bus-sas.md) (Közös hozzáférésű jogosultságkóddal való hitelesítés a Service Bus használatával).

## <a name="event-consumers"></a>Eseményfelhasználók

Minden olyan entitás, amely az Event hub esemény-adatait olvassa be, az *esemény fogyasztója*. Minden Event Hubs-felhasználó az AMQP 1.0-munkameneten keresztül csatlakozik, amelyben az események azonnal megjelennek, amint elérhetővé válnak. Az ügyfélnek nem kell lekérdeznie az adatok rendelkezésre állását.

### <a name="consumer-groups"></a>Fogyasztói csoportok

A Event Hubs közzétételi/előfizetési mechanizmusa *felhasználói csoportokon* keresztül engedélyezhető. A felhasználói csoport a teljes eseményközpont egyik nézete (állapot, pozíció vagy eltolás). A felhasználói csoportok révén több felhasználó alkalmazás rendelkezhet az eseménystream külön nézetével, és a többitől függetlenül saját tempójában és saját eltolásával olvashatja a streamet.

A streamfeldolgozási architektúrákban mindegyik alárendelt alkalmazás megfelel egy felhasználói csoportnak. Amennyiben eseményadatokat kíván írni egy hosszú távú tárhelyre, az adott tárhelyírási alkalmazás is egy felhasználói csoport. Az összetett eseményfeldolgozást már egy másik, külön felhasználói csoport végzi. A partíciókat csak a felhasználói csoportokon keresztül érheti el. Az eseményközpontokban mindig van egy alapértelmezett felhasználói csoport, és akár 20 felhasználói csoportot is létrehozhat a standard szintű eseményközpontokban.

A felhasználónkénti csoportonként legfeljebb 5 egyidejű olvasó lehet egy partíción. **azt javasoljuk azonban, hogy a partíciók felhasználónkénti csoportjain csak egy aktív fogadó legyen**. Egyetlen partíción belül minden olvasó megkapja az összes üzenetet. Ha ugyanazon a partíción több olvasó is van, akkor ismétlődő üzeneteket dolgoz fel. Ezt a kódban kell kezelnie, ami esetleg nem triviális. Bizonyos helyzetekben azonban érvényes megközelítésnek kell lennie.

Az Azure SDK-k által kínált egyes ügyfelek intelligens fogyasztói ügynökök, amelyek automatikusan kezelik annak biztosításának részleteit, hogy az egyes partíciók egyetlen olvasóval rendelkezzenek, és hogy az Event hub összes partíciója beolvasható legyen. Ez lehetővé teszi, hogy a kód az Event hub-ból beolvasott események feldolgozására koncentráljon, így a partíciók számos részletét figyelmen kívül hagyhatják. További információ: [Kapcsolódás partícióhoz](#connect-to-a-partition).

A következő példák a fogyasztói csoport URI-egyezményét mutatják be:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

A következő ábrán az Event Hubs streamfeldolgozási architektúrája látható:

![Event Hubs architektúra](./media/event-hubs-about/event_hubs_architecture.svg)

### <a name="stream-offsets"></a>Streameltolások

Az *eltolás* egy esemény pozíciója a partíción belül. Az eltolásokat tekintheti ügyféloldali kurzorként. Az eltolás az esemény bájtalapú sorszáma. Ez az eltolás lehetővé teszi az eseményfelhasználó (olvasó) számára az eseménystream egy olyan pontjának megadását, ahol az események olvasását el szeretné kezdeni. Az eltolás megadható időbélyegzőként vagy eltolásértékként. A felhasználók felelőssége saját eltolásértékeik tárolása az Event Hubs szolgáltatáson kívül. A partíciókon belül mindegyik esemény rendelkezik eltolással.

![Partíció eltolása](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Ellenőrző pontok használata

Az *ellenőrzőpontok használatával* az olvasók megjelölhetik vagy véglegesíthetik pozíciójukat a partíciók eseménysorozatában. Az ellenőrzőpontok használata a felhasználó felelőssége, és partíciónkénti alapon történik a felhasználói csoportban. A felelősség itt azt jelenti, hogy mindegyik felhasználói csoport esetében mindegyik partícióolvasónak nyilván kell tartania aktuális pozícióját az eseménystreamben, és tájékoztathatja a szolgáltatást, amikor az adatstreamet befejezettnek tekinti.

Ha egy olvasó lecsatlakozik egy partícióról, az újracsatlakozáskor az adott felhasználói csoportban az adott partíció utolsó olvasója által elküldött ellenőrzőpontnál kezdi az olvasást. Amikor az olvasó csatlakozik, átadja az eltolást az Event hub számára, hogy megadja a helyet, ahol az olvasást el szeretné indítani. Az ellenőrzőpontok használatával az alárendelt alkalmazások így megjelölhetik az eseményeket „befejezettként”, valamint biztosítható a rugalmasság a különböző gépeken futó olvasók közötti feladatátvétel esetén. Lehetséges visszatérni a régebbi adatokhoz egy alacsonyabb értékű eltolás megadásával az ellenőrzőpontok használata során. Ezzel a mechanizmussal az ellenőrzőpontok használata rugalmasságot biztosít feladatátvétel esetén, és lehetővé teszi az eseménystream visszajátszását.

> [!IMPORTANT]
> Az eltolásokat a Event Hubs szolgáltatás kapja meg. Az események feldolgozásakor a fogyasztó feladata az ellenőrzőpont.

> [!NOTE]
> Ha Azure Blob Storaget használ az ellenőrzőpont-tárolóként olyan környezetben, amely támogatja a Storage blob SDK egy másik verzióját, mint az Azure-ban, akkor a Storage szolgáltatás API-verziójának az adott környezet által támogatott verzióra való módosításához programkódot kell használnia. Ha például [egy 2002-es Azure stack hub-os verzióban](/azure-stack/user/event-hubs-overview)futtatja az Event Hubs-t, a Storage szolgáltatás legmagasabb rendelkezésre álló verziója a 2017-11-09-es verzió. Ebben az esetben programkódot kell használnia a Storage szolgáltatás API-verziójának 2017-11-09-re való célzásához. Az adott tárolási API-verzió célzására vonatkozó példát a GitHubon található példákban talál: 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) vagy  [írógéppel](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>Általános felhasználói feladatok

Az összes Event Hubs-felhasználó egy AMQP 1,0-munkameneten keresztül kapcsolódhat, amely egy, az állam számára tudatos kétirányú kommunikációs csatornát biztosít. Mindegyik partíció rendelkezik AMQP 1.0-munkamenettel, ami megkönnyíti a partíció szerint elkülönített események átvitelét.

#### <a name="connect-to-a-partition"></a>Csatlakozás partícióhoz

A partíciókhoz való csatlakozáskor általános gyakorlat, hogy egy lízing mechanizmust használ az olvasói kapcsolatok adott partíciókhoz való koordinálására. Így előfordulhat, hogy a felhasználói csoport minden partíciója csak egyetlen aktív olvasóval rendelkezik. Az ellenőrzőpontok, a lízingek és az olvasók kezelése a Event Hubs SDK-k használatával egyszerűsíthető, amely intelligens fogyasztói ügynökként működik. Ezek a következők:

- A .NET-hez készült [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient)
- A Javához készült [EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient)
- A Python [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient)
- A JavaScript/írógéppel [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient)

#### <a name="read-events"></a>Események olvasása

Miután egy AMQP 1.0-munkamenet és -hivatkozás meg lett nyitva egy adott partícióra vonatkozóan, az Event Hubs szolgáltatás kézbesíti az eseményeket az AMQP 1.0-ügyfél számára. Ez a kézbesítési mechanizmus nagyobb átviteli teljesítményt és kisebb késést biztosít a lekérésalapú mechanizmusoknál, amilyen például a HTTP GET. Az eseményeknek az ügyfél számára való elküldésekor minden eseményadat-példány fontos metaadatokat tartalmaz, például az eltolást és sorszámot, amelyek az eseménysorozat ellenőrzőpontokkal való jelölése során használhatók.

Eseményadatok:
* Eltolás
* Sorozat száma
* Törzs
* Felhasználói tulajdonságok
* Rendszertulajdonságok

Az eltolás kezelése a felhasználó felelőssége.

## <a name="next-steps"></a>Következő lépések

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

- Bevezetés az Event Hubs használatába
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Event Hubs minták](event-hubs-samples.md)