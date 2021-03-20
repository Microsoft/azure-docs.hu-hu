---
title: Gyakori kérdések – Azure Event Hubs | Microsoft Docs
description: Ez a cikk a gyakori kérdések (GYIK) listáját tartalmazza az Azure Event Hubs és azok válaszait illetően.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: e6fd4814e771d03827e51f1cd5ee182c9e432cc5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98696108"
---
# <a name="event-hubs-frequently-asked-questions"></a>Event Hubs gyakori kérdések

## <a name="general"></a>Általános kérdések

### <a name="what-is-an-event-hubs-namespace"></a>Mi az Event Hubs névtér?
A névtér az Event hub/Kafka témakörök hatóköri tárolója. Egyedi [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)-t biztosít. A névtér olyan alkalmazás-tárolóként szolgál, amely több Event hub-vagy Kafka-témakört is képes elhelyezni. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Mikor hozzon létre egy új névteret, vagy használjon egy meglévő névteret?
A kapacitás kiosztása ([átviteli egység (TUs)](#throughput-units)) a névtér szintjén kerül kiszámlázásra. A névtér egy régióhoz is társítva van.

A következő esetekben érdemes lehet új névteret létrehozni a meglévő használata helyett: 

- Szüksége lesz egy új régióhoz társított Event hub-ra.
- Egy másik előfizetéshez társított Event hub szükséges.
- Az Event hub-nak külön kapacitás-kiosztással kell rendelkeznie (azaz a hozzáadott Event hub-hoz tartozó névtér kapacitásának nagyobbnak kell lennie a 40-as számú TU-küszöbértéknél, és nem kívánja a dedikált fürthöz jutni)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Mi a különbség az alapszintű és a standard csomag Event Hubs között?

Az Azure Event Hubs standard szintje az alapszintű szinten elérhető funkciókat is biztosítja. A standard a következő funkciókat tartalmazza:

* Hosszú esemény megőrzése
* További felügyelt kapcsolatok, amelyek több mint a megadott számon felüli díjat tartalmaznak
* Több, mint egyetlen [fogyasztói csoport](event-hubs-features.md#consumer-groups)
* [Rögzítés](event-hubs-capture-overview.md)
* [Kafka-integráció](event-hubs-for-kafka-ecosystem-overview.md)

A díjszabással kapcsolatos további információkért, beleértve a dedikált Event Hubs is, tekintse meg a [Event Hubs díjszabási részleteit](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Hol érhető el az Azure Event Hubs?

Az Azure Event Hubs az összes támogatott Azure-régióban elérhető. A listában keresse fel az [Azure-régiók](https://azure.microsoft.com/regions/) lapot.  

### <a name="can-i-use-a-single-advanced-message-queuing-protocol-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Használhatok egyetlen Advanced Message Queueing Protocol (AMQP)-kapcsolattal több Event hub-ból való küldésre és fogadásra?

Igen, ha az összes esemény-hubok ugyanabban a névtérben vannak.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Mi a maximális megőrzési időszak az eseményekhez?

A standard szintű Event Hubs jelenleg legfeljebb hét napos megőrzési időtartamot támogat. Az Event hubok nem végleges adattárként szolgálnak. A 24 óránál hosszabb megőrzési időtartamok olyan forgatókönyvekhez készültek, amelyekben az esemény-adatfolyamok ugyanabba a rendszerbe való visszajátszására alkalmasak. Például egy új gépi tanulási modell betanításához vagy ellenőrzéséhez a meglévő adattípusokon. Ha az üzenetek megőrzése hét nap elteltével is szükséges, a [Event Hubs rögzítésének](event-hubs-capture-overview.md) engedélyezése az Event hub-on az Event hub adatait az Ön által választott Storage-fiókba vagy Azure Data Lake-szolgáltatási fiókba kéri le. Ha engedélyezi a rögzítést, a megvásárolt átviteli egységek alapján kell fizetnie.

A rögzített adatok megőrzési időtartamát beállíthatja a Storage-fiókban. Az Azure Storage **életciklus-kezelési** funkciója az általános célú v2-és blob Storage-fiókok gazdag, szabályon alapuló szabályzatát kínálja. A szabályzat segítségével átválthatja az adatait a megfelelő hozzáférési rétegekbe, vagy lejárhat az adatéletciklus végén. További információ: [Az Azure Blob Storage életciklusának kezelése](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Hogyan figyeli a Event Hubs?
Event Hubs teljes mérőszámokat bocsát ki, amelyek a [Azure monitor](../azure-monitor/overview.md)erőforrásainak állapotát biztosítják. Emellett lehetővé teszik a Event Hubs szolgáltatás általános állapotának felmérését, nem csak a névtér szintjén, hanem az entitás szintjén is. Ismerje meg, hogy az [Azure Event Hubs](event-hubs-metrics-azure-monitor.md)milyen monitorozási lehetőségeket kínál.

### <a name="where-does-azure-event-hubs-store-data"></a><a name="in-region-data-residency"></a>Hol tárolja az Azure Event Hubs az adattárolást?
Az Azure Event Hubs standard és dedikált szintjei a kiválasztott régiókban tárolják a metaadatokat és az adatokat. Ha a Geo-vész-helyreállítás egy Azure Event Hubs-névtérre van beállítva, a metaadatokat a rendszer átmásolja a kiválasztott másodlagos régióba. Ezért ez a szolgáltatás automatikusan megfelel a régió adattárolási követelményeinek, beleértve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net/)megadott követelményeket is.

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

## <a name="apache-kafka-integration"></a>Apache Kafka integráció

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>A meglévő Kafka-alkalmazást a Event Hubs Hogyan integrálni?
A Event Hubs egy Kafka-végpontot biztosít, amelyet a meglévő Apache Kafka-alapú alkalmazásai használhatnak. A konfigurációs változás minden, ami szükséges a Péteri Kafka-élményhez. Alternatívát biztosít a saját Kafka-fürt futtatásához. Event Hubs támogatja a Apache Kafka 1,0-es és újabb verziójú ügyfélprogramokat, és együttműködik a meglévő Kafka-alkalmazásokkal, eszközökkel és keretrendszerekkel. További információ: [Event Hubs for Kafka](https://github.com/Azure/azure-event-hubs-for-kafka)-tárház.

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Milyen konfigurációs módosításokat kell végrehajtani a meglévő alkalmazáshoz, hogy Event Hubs beszéljen?
Az Event hub-hoz való kapcsolódáshoz frissítenie kell a Kafka-ügyfél konfigurációit. Event Hubs névtér létrehozásával és a [kapcsolatok karakterláncának](event-hubs-get-connection-string.md)beszerzésével végezhető el. Módosítsa a bootstrap. Servert úgy, hogy a Event Hubs teljes tartománynevet és a portot 9093-re mutassa. Frissítse a sasl.jaas.configt, hogy a Kafka-ügyfelet a Event Hubs végpontra irányítsa (amely az Ön által beszerzett kapcsolódási karakterlánc), a megfelelő hitelesítéssel az alábbi ábrán látható módon:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

Példa:

```properties
bootstrap.servers=dummynamespace.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXX";
```
Megjegyzés: Ha sasl.jaas.config nem támogatott konfiguráció a keretrendszerben, keresse meg azokat a konfigurációkat, amelyek segítségével beállítja a SASL felhasználónevét és jelszavát, és használja őket. Adja meg a felhasználónevet $ConnectionString és a jelszót a Event Hubs-kapcsolódási karakterlánchoz.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Mi a Event Hubs üzenet/esemény mérete?
Az Event Hubs számára engedélyezett maximális üzenet mérete 1 MB.

## <a name="throughput-units"></a>Átviteli egységek

### <a name="what-are-event-hubs-throughput-units"></a>Mik azok a Event Hubs átviteli egységek?
A Event Hubs átviteli sebessége a Mega bájtokban lévő adatok mennyiségét, illetve az olyan 1 KB-os események számát határozza meg, amelyek beáramlnak és leküldenek Event Hubs keresztül. Ezt az átviteli sebességet az átviteli egységek (TUs) mérik. A Event Hubs szolgáltatás használatának megkezdése előtt vásárolja meg az TUs-t. Explicit módon kiválaszthatja Event Hubs TUs-t a portálon vagy Event Hubs Resource Manager-sablonok használatával. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Az átviteli egységek a névtér összes esemény-hubhoz érvényesek?
Igen, az átviteli egységek (TUs) a Event Hubs névtér összes esemény-hubhoz érvényesek. Ez azt jelenti, hogy a rendszer a névtér szintjén vásárolja meg az TUs-t, és az adott névtér alá tartozó Event hubok között meg van osztva. Mindegyik TU a következő képességekhez jogosítja fel a névteret:

- Akár 1 MB/s a bejövő események másodpercenkénti száma (esemény központba elküldve), de legfeljebb 1000 bejövő esemény, felügyeleti művelet vagy vezérlési API-hívás másodpercenként.
- Akár 2 MB/s a kimenő események másodpercenkénti száma (az Event hub által felhasznált események), de legfeljebb 4096 kimenő esemény.
- Akár 84 GB-nyi esemény-tárterület (elegendő az alapértelmezett 24 órás megőrzési időszakhoz).

### <a name="how-are-throughput-units-billed"></a>Hogyan történik az átviteli egységek számlázása?
Az átviteli egységek (TUs) számlázása óránként történik. A számlázás az adott órában kiválasztott egységek maximális száma alapján történik. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Hogyan optimalizálható a használat az átviteli egységeken?
Akár alacsony átviteli egység (TU) is elindítható, és bekapcsolhatja az [automatikus](event-hubs-auto-inflate.md)kikapcsolást. Az automatikus kibővítés funkció lehetővé teszi, hogy növelje a forgalom/hasznos adattartalom növekedését. A felső korlátot is megadhatja az TUs számának.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Hogyan működik a Event Hubs a működésének automatikus kivonása?
Az automatikus kiemelés funkció lehetővé teszi az átviteli egységek (TUs) vertikális felskálázását. Ez azt jelenti, hogy az alacsony állapotú és az automatikus kiemelések megvásárlásával megvásárolhatja az alapszintű állapotot, mivel a bejövő forgalom növekszik. Költséghatékony megoldást kínál, és teljes körűen szabályozhatja a felügyelni kívánt TUs mennyiségét. Ez **a funkció egy** vertikálisan felhasználható funkció, és a frissítéssel teljes mértékben szabályozható az TUs-szám skálázása. 

Előfordulhat, hogy alacsony átviteli egységekkel (TUs) szeretne kezdeni, például 2 TUs. Ha azt jósolja, hogy a forgalom 15 TUs-ra nőhet, kapcsolja be az automatikus feltöltés funkciót a névtérben, és állítsa be a maximális korlátot 15 TUs-re. A forgalom növekedésével mostantól automatikusan növelheti az TUs-t.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Van-e díj társítva az automatikus feltöltés funkció bekapcsolásakor?
Ehhez a szolgáltatáshoz **nem tartozik díj** . 

### <a name="how-are-throughput-limits-enforced"></a>Hogyan kényszeríti az átviteli korlátokat?
Ha a névtérben lévő összes esemény-hubhoz kiterjedő teljes beáramlási sebesség vagy **a teljes** belépési arány meghaladja az összesített átviteli egységre vonatkozó kedvezményeket, a küldők szabályozva lesznek, és a bejövő forgalomra vonatkozó kvóta túllépése miatti hibákat jeleznek.

Ha a teljes **kimenő** forgalom vagy az esemény teljes kilépési aránya a névtérben lévő összes esemény hubok esetében meghaladja az összesített átviteli egységre vonatkozó kedvezményeket, a rendszer szabályozza a fogadókat, de nem generál szabályozási hibát. 

A bejövő és a kimenő forgalomra vonatkozó kvóták külön vannak kikényszerítve, így egyetlen feladó sem csökkentheti az események felhasználását, és nem akadályozhatja meg, hogy a fogadó eseményt küldjön az Event hub-ba.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-reservedselected"></a>Korlátozva van a fenntartott/kiválasztható átviteli egységek száma?

Ha alapszintű vagy standard szintű névteret hoz létre a Azure Portalban, a névtérhez legfeljebb 20 TUs-t választhat. Ahhoz, hogy **pontosan** 40 TUs-re növelje, küldjön egy  [támogatási kérést](../azure-portal/supportability/how-to-create-azure-support-request.md).  

1. Az **Event Bus-névtér** lapon válassza az **új támogatási kérelem** lehetőséget a bal oldali menüben. 
1. Az **új támogatási kérelem** oldalon hajtsa végre az alábbi lépéseket:
    1. **Összefoglalva**, néhány Szóval írja le a problémát. 
    1. A **probléma típusa** beállításnál válassza a **kvóta** lehetőséget. 
    1. A **probléma altípusa** beállításnál válassza **az átviteli egység növelésének vagy csökkentésének kérelmét**. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-throughput-units.png" alt-text="Support request lap":::

Az 40-as túllépés Event Hubs a dedikált Event Hubs-fürtök nevű erőforrás/kapacitás alapú modellt kínálja. A dedikált fürtöket kapacitási egységekben (ke) értékesítjük. További információ: [dedikált Event Hubs – áttekintés](event-hubs-dedicated-overview.md).

## <a name="dedicated-clusters"></a>Dedikált fürtök

### <a name="what-are-event-hubs-dedicated-clusters"></a>Mik azok a dedikált Event Hubs-fürtök?
Dedikált Event Hubs-fürtök egybérlős üzemelő példányokat biztosítanak a legigényesebb követelményeknek megfelelő ügyfelek számára. Ez az ajánlat olyan kapacitás-alapú fürtöt hoz létre, amely nem az átviteli egységekhez van kötve. Ez azt jelenti, hogy a fürtöt használhatja a fürt CPU-és memóriahasználat által diktált adatok betöltésére és továbbítására. További információ: dedikált Event Hubs- [fürtök](event-hubs-dedicated-overview.md).

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Hogyan hozzon létre egy dedikált Event Hubs-fürtöt?
A Event Hubs dedikált fürt beállításával kapcsolatos részletes utasításokért tekintse meg a rövid útmutató [: dedikált Event Hubs-fürt létrehozása Azure Portal használatával](event-hubs-dedicated-cluster-create-portal.md)című témakört. 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="partitions"></a>Partíciók

### <a name="how-many-partitions-do-i-need"></a>Hány partícióra van szükségem?
A partíciók száma a létrehozáskor van megadva, és 1 és 32 között kell lennie. A partíciók száma nem változtatható meg az összes szinten, kivéve a [dedikált szintet](event-hubs-dedicated-overview.md), ezért a partíciók számának beállításakor figyelembe kell venni a hosszú távú méretezést. A partíció egy adatrendezési mechanizmus, és a felhasználó alkalmazásokban szükséges alárendeltségi párhuzamossághoz köthető. Az egyes eseményközpontokban található partíciók számának kiválasztása közvetlenül kapcsolódik az egyidejű olvasók várt számához. További információ a partíciókon: [partíciók](event-hubs-features.md#partitions).

A létrehozás időpontjában érdemes lehet beállítani a lehető legmagasabb értéket, amely a 32. Ne feledje, hogy több partíció használata esetén a rendszer több partícióba küldi az eseményeket anélkül, hogy megtartja a sorrendet, kivéve, ha a küldőket úgy konfigurálja, hogy csak egyetlen, a 32-es partíción kívülről küldje el a fennmaradó 31 partíciót. Az előző esetben az összes 32-partíción el kell olvasnia az eseményeket. Az utóbbi esetben az Event Processor Host-on kívül az extra konfigurációtól eltekintve nincs nyilvánvaló további díj.

Event Hubs úgy lett kialakítva, hogy a felhasználói csoportok számára egyetlen partíciós olvasót engedélyezzen. A legtöbb felhasználási esetben a négy partíció alapértelmezett beállítása elegendő. Ha az esemény feldolgozását szeretné méretezni, érdemes lehet további partíciókat hozzáadnia. Nincs konkrét átviteli korlát a partíción, azonban a névtérben lévő összesített átviteli sebességet az átviteli egységek száma korlátozza. A névtérben lévő átviteli egységek számának növelésével további partíciók is lehetnek, amelyek lehetővé teszik az egyidejű olvasók számára a maximális átviteli sebesség elérését.

Ha azonban van olyan modellje, amelyben az alkalmazás affinitással rendelkezik egy adott partícióhoz, akkor a partíciók számának növelésével nem élvezheti Önt. További információ: [rendelkezésre állás és konzisztencia](event-hubs-availability-and-consistency.md).

### <a name="increase-partitions"></a>Partíciók bővítése
Egy támogatási kérelem elküldésével megadhatja, hogy a partíciók száma a 40 (pontos) értékre legyen növelve. 

1. Az **Event Bus-névtér** lapon válassza az **új támogatási kérelem** lehetőséget a bal oldali menüben. 
1. Az **új támogatási kérelem** oldalon hajtsa végre az alábbi lépéseket:
    1. **Összefoglalva**, néhány Szóval írja le a problémát. 
    1. A **probléma típusa** beállításnál válassza a **kvóta** lehetőséget. 
    1. A **probléma altípusa** beállításnál válassza **a partíció módosítására vonatkozó kérés** lehetőséget. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-increase-partitions.png" alt-text="Partíciók számának növekedése":::

A partíciók száma pontosan 40-ra növelhető. Ebben az esetben az adatmennyiséget is növelni kell 40-ra. Ha később úgy dönt, hogy a TU-korlátot a <= 20 értékre csökkenti, a maximális partíciós korlát 32-ra is csökken. 

A partíciók csökkenése nem érinti a meglévő Event hubokat, mert a partíciók az Event hub szintjén vannak alkalmazva, és a hub létrehozása után nem változtathatók meg. 

## <a name="pricing"></a>Díjszabás

### <a name="where-can-i-find-more-pricing-information"></a>Hol találhatok további díjszabási információkat?

A Event Hubs díjszabásával kapcsolatos részletes információkért tekintse meg a [Event Hubs díjszabását](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Van olyan díj, amely több mint 24 óránál Event Hubs eseményt tart fenn?

A standard szintű Event Hubs 24 óránál hosszabb üzenetek megőrzési időtartamát teszi lehetővé legfeljebb hét napig. Ha a tárolt események teljes száma meghaladja a tárolási kapacitást a kiválasztott átviteli egységek (84 GB/s átviteli egység) számára, akkor a juttatást meghaladó méretet a közzétett Azure Blob Storage-díjszabás alapján számítjuk fel. Az egyes átviteli egységekben a tárolási támogatás kiterjed a 24 órás megőrzési időtartamra (az alapértelmezett értékre), még akkor is, ha az átviteli egység a maximális bejövő forgalomhoz képest fel van használva.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Hogyan számítja ki és számítja ki a Event Hubs tárolási méretet?

Az összes tárolt esemény teljes mérete, beleértve az esemény-fejlécek belső terhelését vagy az összes Event hub lemezes tárolási struktúráját, a nap folyamán méri. A nap végén a rendszer kiszámítja a maximális tárolási méretet. A napi tárterületet a nap folyamán kiválasztott átviteli egységek minimális száma alapján számítjuk ki (minden átviteli egység 84 GB-os kedvezményt biztosít). Ha a teljes méret meghaladja a napi kiszámított tárterületet, a felesleges tárterületet az Azure Blob Storage díjszabása (a **helyileg redundáns tárolási** arány) alapján számlázjuk.

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Hogyan számítják ki Event Hubs beáramlási események kiszámításának módját?

Az Event hub-nak küldött összes esemény számlázandó üzenetnek számít. A *bejövő események* olyan adategységként vannak definiálva, amely kisebb vagy egyenlő, mint 64 kb. A 64 KB-nál kisebb vagy azzal egyenlő események csak egy számlázandó eseménynek tekintendők. Ha az esemény meghaladja a 64 KB-ot, a számlázható események száma az esemény méretétől függően számítja ki a 64 KB-os többszörösét. Az Event hub-nak küldött 8 KB-os esemény például egy eseménynek számít, az Event hub-nak küldött 96 KB-os üzenet pedig két eseménynek számít.

Az Event hub által felhasznált események, valamint a felügyeleti műveletek és a vezérlési hívások, például az ellenőrzőpontok, nem számlázható beáramlási eseménynek számítanak, de az átviteli egységre is felmerülhetnek.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>A felügyelt kapcsolatok díjai a Event Hubsra vonatkoznak?

A kapcsolati díjak csak akkor érvényesek, ha az AMQP protokollt használják. Az események HTTP-n keresztüli küldéséhez nem tartoznak kapcsolódási díjak, a küldő rendszerek és eszközök számától függetlenül. Ha azt tervezi, hogy AMQP használ (például a hatékonyabb esemény-adatfolyam eléréséhez vagy a kétirányú kommunikáció engedélyezéséhez a IoT-parancsok és-vezérlési forgatókönyvek esetében), tekintse meg [Event Hubs a díjszabási információkkal](https://azure.microsoft.com/pricing/details/event-hubs/) foglalkozó oldalt, amelyből megtudhatja, hogy hány kapcsolat szerepel az egyes szolgáltatási rétegekben.

### <a name="how-is-event-hubs-capture-billed"></a>Hogyan történik a Event Hubs rögzítésének számlázása?

A rögzítés akkor engedélyezett, ha a névtérben található bármely Event hub engedélyezve van a rögzítési lehetőséggel. Event Hubs a rögzítés óradíjat igényel a megvásárolt átviteli egységenként. Az átviteli egységek számának növelése vagy csökkenése esetén Event Hubs rögzítési számlázás a teljes óránkénti növekmények alapján mutatja be ezeket a módosításokat. További információ a Event Hubs rögzítésének számlázásáról: [Event Hubs díjszabási információk](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Fizetnem kell a Event Hubs rögzítéshez kiválasztott Storage-fiókhoz?

A rögzítés a megadott Storage-fiókot használja, ha az engedélyezve van az Event hub-ban. Ahogy a Storage-fiókja, a konfiguráció minden változása az Azure-előfizetésre lesz kiszámlázva.

## <a name="quotas"></a>Kvóták

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Van-e kvóta társítva a Event Hubshoz?

Az összes Event Hubs kvóta listáját itt tekintheti meg: [kvóták](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Miért nem lehet névteret létrehozni egy másik előfizetésből való törlés után? 
Ha töröl egy névteret egy előfizetésből, várjon 4 órát, mielőtt újra létrehozza azt ugyanazzal a névvel egy másik előfizetésben. Ellenkező esetben a következő hibaüzenet jelenhet meg: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Melyek a Event Hubs által generált kivételek és a javasolt műveletek?

A lehetséges Event Hubs kivételek listáját lásd: [kivételek áttekintése](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Event Hubs a [diagnosztikai naplók](event-hubs-diagnostic-logs.md) két típusát támogatja – a rögzítési hibák naplóit és az operatív naplókat – mindkettő a JSON-ban szerepel, és a Azure Portaln keresztül kapcsolható be.

### <a name="support-and-sla"></a>Támogatás és SLA

A Event Hubs technikai támogatása a [Microsoft Q&](/answers/topics/azure-service-bus.html)keresztül érhető el Azure Service Bus. A számlázási és előfizetés-kezelési támogatást díjmentesen biztosítjuk.

Ha többet szeretne megtudni az SLA-ról, tekintse meg a [szolgáltatói szerződéseket](https://azure.microsoft.com/support/legal/sla/) ismertető oldalt.

## <a name="azure-stack-hub"></a>Azure Stack Hub

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>Hogyan célozható meg az Azure Storage SDK egy adott verziója az Azure Blob Storage ellenőrzőpont-tárolóként való használatakor?
Ha Azure Stack hub-on futtatja ezt a kódot, futásidejű hibákat tapasztal, kivéve, ha egy adott tárolási API-verziót céloz meg. Ennek oka az, hogy az Event Hubs SDK az Azure-ban elérhető legújabb elérhető Azure Storage API-t használja, amely esetleg nem érhető el az Azure Stack hub platformon. Az Azure Stack hub a Storage blob SDK egy másik verzióját is támogatja, mint az Azure-ban általában elérhető. Ha az Azure blog Storage-t ellenőrzőpont-tárolóként használja, tekintse [meg a támogatott Azure Storage API-verziót az Azure stack hub](/azure-stack/user/azure-stack-acs-differences?#api-version) -hoz, amely a programkódban található verzióra van kialakítva. 

Ha például Azure Stack hub 2005-es verzióján fut, a Storage szolgáltatás legmagasabb rendelkezésre álló verziója a 2019-02-02-es verzió. Alapértelmezés szerint az Event Hubs SDK ügyféloldali kódtár az Azure legmagasabb rendelkezésre állású verzióját használja (2019-07-07 az SDK kiadásának időpontjában). Ebben az esetben a szakasz következő lépésein kívül kódot is hozzá kell adnia a Storage szolgáltatás API 2019-02-02-es verziójának célzásához. Az adott tárolási API-verzió célzására vonatkozó példát a C#, a Java, a Python és a JavaScript/írógéppel következő példákban talál.  

A következő példákban megtudhatja, hogyan célozhat meg egy adott tárolási API-verziót a kódból a GitHubon: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python – [szinkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [aszinkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) és [írógéppel](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>Következő lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs áttekintése](./event-hubs-about.md)
* [Event hub létrehozása](event-hubs-create.md)
* [Event Hubs automatikus kiemelés](event-hubs-auto-inflate.md)
