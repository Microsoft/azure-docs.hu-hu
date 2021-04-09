---
title: Az Azure Service Bus-üzenetkezelés áttekintése | Microsoft Docs
description: Ez a cikk átfogó áttekintést nyújt a Azure Service Busről, amely egy teljes körűen felügyelt Enterprise Integration Message Broker.
ms.topic: overview
ms.date: 02/16/2021
ms.openlocfilehash: 897729b9748d69ad3c6de507e800dbb3a1a3619c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570472"
---
# <a name="what-is-azure-service-bus"></a>Mi az Azure Service Bus?
A Microsoft Azure Service Bus egy teljes körűen felügyelt vállalati üzenetsor az üzenetsor-kezelés és a közzététel – előfizetés témakörökben. Service Bus az alkalmazások és szolgáltatások egymástól való elválasztására szolgálnak, a következő előnyöket biztosítva:

- Terheléselosztási munka a versengő feldolgozók között
- Biztonságos Útválasztás és adatátvitel és vezérlés a szolgáltatás és az alkalmazások határai között
- Magas fokú megbízhatóságot igénylő tranzakciós munka koordinálása 

## <a name="overview"></a>Áttekintés
Az adatok különböző alkalmazások és szolgáltatások közötti átvitele az *üzenetek* segítségével történik. Az üzenet egy metaadatokkal díszített tároló, amely adatokat tartalmaz. Az adatok bármilyen típusú információval rendelkezhetnek, beleértve a közös formátumokkal kódolt strukturált adatokat is, például a következő esetekben: JSON, XML, Apache Avro, egyszerű szöveg.

Néhány gyakori üzenetküldési forgatókönyv:

* *Üzenetküldés*. Üzleti adatok (például értékesítési vagy beszerzési rendelések, naplók vagy leltározási mozgások) továbbítása.
* *Alkalmazások* leválasztása. Az alkalmazások és szolgáltatások megbízhatóságának és méretezhetőségének javítása. A gyártónak és a fogyasztónak nem kell online vagy azonnal elérhetőnek lennie. A [terhelést úgy kell megterhelni](/azure/architecture/patterns/queue-based-load-leveling) , hogy a forgalmi tüskék ne túladóztat meg a szolgáltatást. 
* *Terheléselosztás.* Lehetővé teszi több [versengő fogyasztó](/azure/architecture/patterns/competing-consumers) számára, hogy egy várólistából is beolvassák a kizárólagos tulajdonosi jogokat bizonyos üzenetekhez. 
* *Témakörök és előfizetések*. 1:*n* kapcsolatok engedélyezése a [közzétevők és az előfizetők](/azure/architecture/patterns/publisher-subscriber)között, amely lehetővé teszi, hogy az előfizetők kiválasszák az adott üzeneteket a közzétett üzenet-adatfolyamból.
* *Tranzakciók*. Lehetővé teszi, hogy több műveletet végezzen el, amelyek mindegyike egy atomi tranzakció hatókörében van. Például a következő műveletek hajthatók végre egy tranzakció hatókörében.  

    1. Üzenet beszerzése egy várólistából.
    2. Egy vagy több várólistára történő feldolgozás eredményeinek közzététele.
    3. Helyezze át a bemeneti üzenetet az eredeti várólistából. 
    
    Az eredmények az alsóbb rétegbeli fogyasztók számára csak a siker után válnak láthatóvá, beleértve a bemeneti üzenet sikeres rendezését is, amely lehetővé teszi a csak egyszer feldolgozható szemantikai műveletek elvégzését. Ez a tranzakciós modell robusztus alap a [kompenzáló tranzakciók](/azure/architecture/patterns/compensating-transaction) mintája számára a nagyobb megoldási környezetben. 
* *Üzenet-munkamenetek*. Nagy léptékű koordinációt valósíthat meg a munkafolyamatok és a többszintű, a szigorú üzenetek rendezését vagy az üzenetek késleltetését igénylő átvitelek esetében.

Ha már ismeri a más üzenet-közvetítőket, például az Apache ActiveMQ-t, Service Bus fogalmak hasonlóak ahhoz, amit tud. Mivel a Service Bus a szolgáltatásként nyújtott platform ("a"), a fő különbség az, hogy nem kell aggódnia a következő műveletekkel kapcsolatban. Az Azure gondoskodik az Ön számára. 

- Naplók elhelyezése és a lemezterület kezelése
- Biztonsági másolatok feldolgozása
- Az operációs rendszerek vagy a termékek javításának megtartása
- Problémák a hardverhiba miatt 
- Feladatátvétel a tartalék gépre

## <a name="compliance-with-standards-and-protocols"></a>Megfelelőség a szabványokkal és protokollokkal

A Service Bus elsődleges átviteli protokollja a [speciális üzenetküldési üzenetsor-kezelési protokoll (AMQP) 1,0](service-bus-amqp-overview.md), egy Open ISO/IEC szabvány. Lehetővé teszi, hogy az ügyfelek olyan alkalmazásokat írjanak, amelyek Service Bus és helyszíni közvetítők, például a ActiveMQ vagy a RabbitMQ használatával működnek. A [AMQP protokoll útmutatója](service-bus-amqp-protocol-guide.md) részletes információkat tartalmaz abban az esetben, ha ilyen absztrakciót szeretne készíteni.

Az [Service Bus Premium](service-bus-premium-messaging.md) teljes mértékben megfelel a Java/Jakarta EE [Java Message Service (JMS) 2,0](how-to-use-java-message-service-20.md) API-nak. A és a Service Bus standard a JMS 1,1 részhalmazt is támogatja a várólistákra összpontosítva. A JMS az üzenetek közvetítésének közös absztrakciója, és számos alkalmazással és keretrendszerrel integrálható, beleértve a népszerű rugós keretrendszert is. A többi közvetítőtől a Azure Service Busra való váltáshoz csak újra kell létrehoznia a várólisták és a témakörök topológiáját, és módosítania kell az ügyfél szolgáltatójának függőségeit és konfigurációját. Példaként tekintse meg a [ActiveMQ áttelepítési útmutatót](migrate-jms-activemq-to-servicebus.md).

## <a name="concepts-and-terminology"></a>Fogalmak és szakkifejezések 
Ez a szakasz a Service Bus fogalmakat és terminológiát ismerteti.

### <a name="namespaces"></a>Névterek
A névtér az összes üzenetküldési összetevő tárolója. Több várólista és témakör is lehet egyetlen névtérben, és a névterek gyakran alkalmazás-tárolóként szolgálnak. 

Egy névtér összehasonlítható más közvetítők terminológiájában lévő "kiszolgálóval", de a fogalmak nem közvetlenül egyenértékűek. A Service Bus névtér egy nagy méretű fürt saját kapacitású szelete, amely több tucat, az összes aktív virtuális gépről készült. Előfordulhat, hogy három Azure-beli [rendelkezésre állási zónát](../availability-zones/az-overview.md)is kijelöl. Így az összes rendelkezésre állási és megbízhatósági előnyt élvezheti a nagy léptékű Message Broker futtatásához. És nem kell aggódnia az alapul szolgáló bonyolultságok miatt. Service Bus "kiszolgáló nélküli" üzenetküldés.

### <a name="queues"></a>Üzenetsorok
Az üzenetek az *üzenetsorokba* érkeznek be, és onnan küldi ki őket a rendszer. A várólisták üzeneteket tárolnak, amíg a fogadó alkalmazás elérhetővé válik a fogadáshoz és a feldolgozáshoz.

![Üzenetsor](./media/service-bus-messaging-overview/about-service-bus-queue.png)

A várólistákban lévő üzenetek megrendelése és időbélyege az érkezéskor. Ha a közvetítő elfogadta ezt az üzenetet, a rendszer mindig tartósan a Triple-redundáns tárolásban, és a rendelkezésre állási zónák között elosztva, ha a névtér zóna engedélyezve van. Service Bus soha nem hagy üzeneteket a memóriában vagy a felejtő tárolóban, miután elfogadták őket az ügyfélnek.

Az üzenetek *lekéréses* módban lesznek kézbesítve, csak az üzenetek kézbesítését kérik. A más Felhőbeli várólisták foglalt lekérdezési modelljétől eltérően a lekéréses művelet hosszú életű lehet, és csak akkor fejeződik be, ha egy üzenet elérhetővé válik. 

### <a name="topics"></a>Témakörök

Az üzenetek küldéséhez és fogadásához *témaköröket* is használhat. Amíg egy üzenetsort gyakran használnak közvetlen kommunikációra, addig a témakörök hasznosak a közzétételi/előfizetési forgatókönyvekben.

![Témakör](./media/service-bus-messaging-overview/about-service-bus-topic.png)

A témakörök több, független előfizetéssel rendelkezhetnek, amelyek a témakörhöz vannak csatolva, és más módon ugyanúgy működnek, mint a fogadó oldalról származó várólisták. Egy adott témakör előfizetője az adott témakörben küldött összes üzenetről kaphat másolatot. Az előfizetések névvel ellátott entitások. Az előfizetések alapértelmezés szerint tartósak, de a rendszer úgy konfigurálható, hogy lejárjon, majd automatikusan törölhető legyen. A JMS API-n keresztül a Service Bus Premium lehetővé teszi a kapcsolatok időtartamára vonatkozó illékony előfizetések létrehozását is.

Megadhat szabályokat egy előfizetéshez. Egy előfizetési szabály rendelkezik egy *szűrővel* , amely meghatározza az üzenetnek az előfizetésbe másolandó feltételt, valamint egy opcionális *műveletet* , amely módosíthatja az üzenetek metaadatait. További információ: a [szűrők és a műveletek](topic-filters.md)témakör. Ez a funkció a következő esetekben hasznos:

- Nem szeretné, hogy egy előfizetés megkapja a témakörbe küldött összes üzenetet.
- Az előfizetésen keresztül továbbított üzeneteket további metaadatokkal szeretné megjelölni.

## <a name="advanced-features"></a>Speciális funkciók

A Service Bus olyan speciális funkciókat tartalmaz, amelyek lehetővé teszik összetettebb üzenetkezelési problémák megoldását. A következő szakaszok ismertetik ezeket a funkciókat.

### <a name="message-sessions"></a>Üzenet-munkamenetek

Ha Service Bus-ben szeretne létrehozni egy beérkező, első kimenő (FIFO) garanciát, használja a munkameneteket. Az üzenet-munkamenetek lehetővé teszik a kapcsolódó üzenetek nem kötött sorrendjének kizárólagos, rendezett kezelését. A munkamenetek nagy léptékű, magas rendelkezésre állású rendszerekben való kezelésének engedélyezéséhez a munkamenet-szolgáltatás a munkamenet-állapot tárolását is lehetővé teszi, amely lehetővé teszi, hogy a munkamenetek biztonságosan mozogjanak a kezelők között. További információ [: Message Sessions: First in, First out (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Továbbítás

Az öntovábbító funkció egy várólistát vagy előfizetést egy másik várólistára vagy témakörre, ugyanazon a névtéren belül. Ha ezt a funkciót használja, Service Bus automatikusan áthelyezi az üzeneteket egy várólistából vagy előfizetésből egy célvárólista vagy témakörbe. Minden ilyen lépés tranzakciós műveletet végez. További információ: [Service Bus entitások Kiláncolása az autoforwarding szolgáltatással](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Kézbesítetlen levelek várólistája

Az összes Service Bus várólista és témakör-előfizetéshez tartozik egy kézbesítetlen levelek várólistája (DLQ). A DLQ a következő feltételeknek megfelelő üzeneteket tárolja: 

- A címzettek nem továbbíthatók sikeresen.
- Időtúllépés.
- Ezeket a fogadó alkalmazás explicit módon kiszorul. 

A kézbesítetlen levelek várólistáján lévő üzeneteket a rendszer azt az indoklással láthatja el, hogy miért helyezték el őket. A kézbesítetlen levelek várólistája speciális végponttal rendelkezik, de más módon úgy viselkedik, mint bármely normál várólista. Egy alkalmazás vagy eszköz böngészhet a DLQ vagy a sorból. A kézbesítetlen levelek várólistáján is elvégezhető a továbbítás. További információ: [Service Bus kézbesítetlen levelek várólistáinak áttekintése](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Ütemezett kézbesítés

Üzeneteket küldhet egy várólistába vagy egy témakörbe késleltetett feldolgozás céljából, és beállíthatja, hogy mikor legyen elérhető az üzenet a felhasználáshoz. Az ütemezett üzenetek is megvonhatók. További információ: [ütemezett üzenetek](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Üzenetek halasztása

Egy üzenetsor vagy előfizetési ügyfél egy későbbi időpontra elhalaszthatja a fogadott üzenetek lekérését. Előfordulhat, hogy az üzenet el lett küldve a várt sorrendben, és az ügyfél meg szeretné várni, amíg egy másik üzenetet kap. A késleltetett üzenetek a várólistán vagy az előfizetésen belül maradnak, és a szolgáltatáshoz hozzárendelt sorozatszám alapján explicit módon újra kell aktiválni őket. További információ: [üzenet halasztása](message-deferral.md).

### <a name="batching"></a>Kötegelés

Az ügyféloldali kötegek lehetővé teszik, hogy egy üzenetsor vagy egy témakör-ügyfél összegyűjtse az üzenetek egy halmazát, és együtt továbbítsa őket. Gyakran a sávszélesség megtakarítása vagy az átviteli sebesség növelése érdekében történik. További információ: [ügyféloldali kötegelt feldolgozás](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Tranzakciók

Egy tranzakció két vagy több műveletet egyesít egy *végrehajtási hatókörbe*. Service Bus lehetővé teszi, hogy egyetlen tranzakció hatókörébe eső több üzenetkezelési entitáson belül csoportosítsa a műveleteket. Az üzenet entitás lehet üzenetsor, témakör vagy előfizetés. További információkért lásd: [Service Bus tranzakciók feldolgozásának áttekintése](service-bus-transactions.md).

### <a name="autodelete-on-idle"></a>Az autodelete inaktív állapotban
Az automatikus törlés üresjáratban beállítással megadhat egy üresjárati időközt, amely után a rendszer automatikusan törli a várólista vagy a témakör előfizetését. A minimális érték 5 perc. 

### <a name="duplicate-detection"></a>Duplikálás észlelése
A duplikált észlelési funkció lehetővé teszi, hogy a küldő újra ugyanazt az üzenetet küldje újra, és a bróker el lehessen dobni a lehetséges duplikált elemeket. További információ: [duplikált észlelés](duplicate-detection.md).

### <a name="geo-disaster-recovery"></a>Geo-vészhelyreállítás

Ha egy Azure-régió leállást tapasztal, a vész-helyreállítási funkció lehetővé teszi az adatfeldolgozást, hogy egy másik régióban vagy adatközpontban folytassa a működést. A szolgáltatás megtartja a másodlagos régióban elérhető névtér szerkezeti tükrözését, és lehetővé teszi, hogy a névtér identitása a másodlagos névtérre váltson. A már közzétett üzenetek a korábbi elsődleges névtérben maradnak, ha a rendelkezésre állási epizódot a rendszer kinyeri. További információ: [Azure Service Bus földrajzi katasztrófa utáni helyreállítás](service-bus-geo-dr.md).

### <a name="security"></a>Biztonság

Service Bus támogatja a standard [AMQP 1,0](service-bus-amqp-overview.md) , valamint a [http-vagy Rest](/rest/api/servicebus/) -protokollok és azok biztonsági létesítményei, beleértve a TRANSPORT szintű biztonságot (TLS). Az ügyfelek a [közös hozzáférésű aláírással](service-bus-sas.md) vagy [Azure Active Directory](service-bus-authentication-and-authorization.md) szerepköralapú biztonsággal való hozzáférésre is jogosultak. 

A nemkívánatos forgalom elleni védelem érdekében Service Bus olyan [biztonsági funkciókat](network-security.md) biztosít, mint például az IP-tűzfal és a virtuális hálózatokkal való integráció. 

## <a name="client-libraries"></a>Ügyfélkódtárak

Az Azure SDK-n keresztül teljes mértékben támogatott Service Bus ügyféloldali kódtárak érhetők el.

- [.NET-Azure Service Bus](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Java-kódtárak Azure Service Bus](/java/api/overview/azure/servicebus?preserve-view=true)
- [Azure Service Bus-szolgáltató a Java JMS 2,0-hez](how-to-use-java-message-service-20.md)
- [A JavaScript és az írógéppel Azure Service Bus moduljai](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus kódtárak a Pythonhoz](/python/api/overview/azure/servicebus?preserve-view=true)

[Azure Service Bus ' az elsődleges protokoll a AMQP 1,0](service-bus-amqp-overview.md) , és bármely AMQP 1,0-kompatibilis protokoll-ügyfélről használható. Számos nyílt forráskódú AMQP-ügyfél olyan mintákat tartalmaz, amelyek kifejezetten bemutatják Service Bus együttműködési képességet. Tekintse át a [AMQP 1,0 protokoll útmutatóját](service-bus-amqp-protocol-guide.md) , amelyből megtudhatja, hogyan használhatja a AMQP 1,0-ügyfeleket közvetlenül a Service Bus funkcióit.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Integráció

Service Bus teljes mértékben integrálható a Microsoft és az Azure számos szolgáltatásával, például:

* [Event Grid](service-bus-to-event-grid-integration-example.md)
* [Logic Apps](../connectors/connectors-create-api-servicebus.md)
* [Azure Functions](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>Következő lépések

A Service Bus-üzenetküldéssel való megismerkedéshez tekintse meg a következő cikkeket:

* Az Azure Messaging Services összehasonlításához tekintse meg [a szolgáltatások összehasonlítása](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)című témakört.
* Próbálja ki a [.net](service-bus-dotnet-get-started-with-queues.md), a [Java](service-bus-java-how-to-use-queues.md)vagy a [JMS](service-bus-java-how-to-use-jms-api-amqp.md)rövid útmutatóit.
* Service Bus-erőforrások kezeléséhez lásd: [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Ha többet szeretne megtudni a standard és a prémium szintekről és azok díjszabásáról, tekintse meg a [Service Bus díjszabását](https://azure.microsoft.com/pricing/details/service-bus/).
* A prémium szint teljesítményével és késésével kapcsolatos információkért lásd: [prémium szintű üzenetkezelés](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).