---
title: Üzenet-replikálás és régiók közötti összevonás – Azure Service Bus | Microsoft Docs
description: Ez a cikk áttekintést nyújt az esemény-replikálásról és a régiók közötti összevonásról Azure Service Bus.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: e47f633fcd9248eab6f47936aa7c45877decc1fe
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880827"
---
# <a name="message-replication-and-cross-region-federation"></a>Üzenetreplikáció és régiók közötti összevonás

A névtereken belül a Azure Service Bus támogatja a [láncolt várólisták és a témakör-előfizetések topológiájának létrehozását](service-bus-auto-forwarding.md) , amely lehetővé teszi a különböző útválasztási minták megvalósítását. Például megadhatja a partnereknek olyan dedikált várólistákat, amelyekhez küldési vagy fogadási engedélyek tartoznak, és amelyek szükség esetén átmenetileg fel lesznek függesztve, és rugalmasan csatlakoztathatók az alkalmazáshoz magánjellegű entitásokhoz. Létrehozhat összetett többfázisú útválasztási topológiákat is, vagy létrehozhat postaláda-stílusú várólistákat, amelyek lecsapolják az üzenetsor-előfizetéseket, és lehetővé teszik az előfizetői kapacitások további tárolókapacitását. 

Számos kifinomult megoldáshoz szükség van arra is, hogy az üzenetek replikálása a névtér határain keresztül történjen a fenti és egyéb minták megvalósításához. Előfordulhat, hogy az üzeneteknek több, különböző alkalmazás-bérlőhöz vagy több különböző Azure-régióhoz tartozó névterek között kell lenniük. 

A megoldás több Service Bus névteret tart fenn különböző régiókban, és replikálja az üzeneteket a várólisták és a témakörök között, és/vagy hogy az üzeneteket olyan forrásokkal és célokkal, mint például az [azure Event Hubs](../event-hubs/event-hubs-about.md), az [Azure IoT Hub](../iot-fundamentals/iot-introduction.md)vagy a [Apache Kafka](https://kafka.apache.org)fogja cserélni. 

Ezek a forgatókönyvek a cikk középpontjában állnak. 

## <a name="federation-patterns"></a>Összevonási minták

Számos lehetséges motiváció van arra vonatkozóan, hogy miért érdemes áthelyezni az üzeneteket Service Bus entitások, például várólisták vagy témakörök között, illetve Service Bus és más források és célok között. 

A [Event Hubshoz](../service-bus-messaging/service-bus-federation-overview.md)hasonló mintázatok összevetésével összehasonlítva a várólista-szerű entitások összevonása összetettebb, mivel az üzenetsor-kezelés a fogyasztóknak kizárólagos tulajdonosi jogosultságot biztosít egyetlen üzenetben, az üzenetek kézbesítésének megtartása, valamint a közvetítő a [versengő fogyasztók](/azure/architecture/patterns/competing-consumers)közötti valós eloszlás koordinálása érdekében. 

Vannak olyan gyakorlati akadályok, mint például a [Cap-tétel](https://en.wikipedia.org/wiki/CAP_theorem)korlátai, ami megnehezíti a több régióban egyszerre elérhető üzenetsor egységes nézetének megadását, amely lehetővé teszi a regionálisan elosztott, [versengő fogyasztók](/azure/architecture/patterns/competing-consumers) számára az üzenetek kizárólagos tulajdonjogának kiválasztását. Az ilyen földrajzilag elosztott üzenetsor nem csak az üzenetek teljes körű replikálását igényli, hanem minden üzenet kézbesítési állapotát is, mielőtt az üzenetek elérhetővé tehetők a felhasználók számára. Egy feltételezett, regionálisan elosztott várólista teljes konzisztenciájának célja közvetlen ütközésben van azzal a fő céllal, hogy gyakorlatilag az összes Azure Service Bus ügyfélnél az összevonási forgatókönyvek megtervezése során a maximális rendelkezésre állás és megbízhatóság. 

Az itt bemutatott mintázatok ezért a rendelkezésre állásra és a megbízhatóságra összpontosítanak, ugyanakkor az adatvesztés és az üzenetek ismétlődő kezelését is érdemes elkerülni. 

### <a name="resiliency-against-regional-availability-events"></a>Rugalmasság a regionális elérhetőségi eseményekkel szemben 

Bár a maximális rendelkezésre állás és a megbízhatóság a Service Bus legfontosabb működési prioritása, számos módon megakadályozható, hogy a gyártó vagy a fogyasztó ne beszéljen a hozzá tartozó "elsődleges" Service Bus a hálózati vagy névfeloldási problémák miatt, vagy ha Service Bus entitás valóban átmenetileg nem válaszol, vagy hibákat ad vissza. Előfordulhat, hogy a kijelölt üzenet-feldolgozó is elérhetetlenné válik.

Ezek a feltételek nem "katasztrofálisak", így a regionális telepítést teljes egészében fel kell vennie, mint a vész-helyreállítási helyzetekre, de előfordulhat, hogy egyes alkalmazások üzleti forgatókönyvét már nem több, mint néhány perc vagy akár másodpercek alatt a rendelkezésre állási események érintik. A Azure Service Bust gyakran használják hibrid felhőalapú környezetekben, valamint a hálózat peremén található ügyfelekkel, például a kiskereskedelmi üzletekben, az éttermekben, a banki ágakban, a gyártási helyeken, a logisztikai létesítményekben és a repülőtereken. A hálózati útválasztási vagy torlódási probléma hatással lehet arra, hogy bármely hely elérje a hozzárendelt Service Bus végpontot, miközben egy másik régió másodlagos végpontja elérhető lehet. Ugyanakkor előfordulhat, hogy az ezekről a helyekről érkező üzeneteket feldolgozó rendszerek továbbra is akadálymentes hozzáférést biztosítanak mind az elsődleges, mind a másodlagos Service Bus végpontokhoz. 

Az ilyen hibrid Felhőbeli és peremhálózati alkalmazások számos gyakorlati példát mutatnak, amelyek alacsony üzleti toleranciával rendelkeznek a hálózati útválasztási problémák vagy az Service Bus entitások átmeneti rendelkezésre állási problémáinak hatására. Ilyenek például a kiskereskedelmi webhelyeken történő fizetések, a repülőtéri kapuk beszállítása, valamint az éttermekben elérhető mobiltelefonos megrendelések, amelyek mindegyike azonnali, és teljes leállást biztosít, amikor a megbízható kommunikációs útvonal nem érhető el.

Ebben a kategóriában három különböző elosztott mintát tárgyalunk: "minden aktív" replikáció, "aktív-passzív" replikáció és "túlcsorduló" replikáció. 

#### <a name="all-active-replication"></a>All-Active replikáció

A "minden aktív" replikációs minta lehetővé teszi, hogy az azonos logikai témakör (vagy várólista) aktív replikája több névtérben (és régióban) legyen elérhető, és hogy minden üzenet elérhető legyen az összes replikában, függetlenül attól, hogy hol voltak a várólistán lévő. A minta általában a közzétevőhöz viszonyítva megőrzi az üzenetek sorrendjét. 

![Minden aktív minta](media/service-bus-federation-overview/mirrored-topics.jpg)

Ahogy az ábrán is látható, a minta általában Service Bus témakörökre támaszkodik. Egy témakör minden olyan névtérhez, amely részt vesz a replikálási sémában. Ezen témakörök mindegyike egy "replikációs előfizetéssel" rendelkezik bármely olyan témakörhöz, amelyre az üzenetek replikálódnak. A fenti ábrán csupán egy pár témakör található, ezért egyetlen replikációs előfizetésre van szükség a megfelelő másik témakörhöz. Egy három névterből *({N1, N2, N3})* álló forgatókönyvben a névtérbeli *N1* -es témakör két replikációs előfizetéssel rendelkezik, egyet az *N2* -ben található megfelelő témakörhöz, egyet pedig az *N3*-as megfelelő témakörhöz. 

Minden egyes replikációs előfizetéshez tartozik egy olyan szabály, amely kombinálja az SQL Filter kifejezést ( `replicated <> 1` ) és egy SQL-műveletet ( `set replicated = 1` ). A szabály szűrője biztosítja, hogy csak azok az üzenetek `replication` `1` legyenek jogosultak erre az előfizetésre, ahol az egyéni tulajdonság nincs beállítva, vagy ha az érték nem válik jogosulttá erre az előfizetésre `1` A hatás az, hogy amikor az üzenetet átmásolja a megfelelő témakörbe, már nem jogosult a replikálásra az ellenkező irányba, ezért a replikák között elkerüljük a visszaugró üzeneteket.

A megfelelő szabállyal rendelkező előfizetések egyszerűen hozzáadhatók bármely témakörhöz az Azure CLI használatával.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

A várólista modellezéséhez minden egyes témakör csak egy normál előfizetésre (a replikációs előfizetések kivételével) korlátozódik, amelyeket minden fogyasztó megoszt.

> A teljes körűen aktív replikációs modell minden, a témakörökbe küldött üzenet másolatát áthelyezi az egyes témakörökbe. Ez azt jelenti, hogy az egyes régiókban az alkalmazás kódja megjelenik és feldolgozza az összes üzenetet.
> Ez a minta olyan forgatókönyvekhez alkalmas, amelyekben az adatok több régióban vannak elosztva, vagy ha általában redundáns feldolgozásra van szükség. Ha csak egyszer kell feldolgoznia minden üzenetet, mint a normál üzenetsor esetében, az alábbi két minta egyikét kell figyelembe vennie.  

#### <a name="active-passive-replication"></a>Active-Passive replikáció

Az "aktív-passzív" replikációs minta az előző minta variációja, ahol csak az egyik témakört (az "elsődleges") aktívan használják az üzenetek küldéséhez és fogadásához, és az üzenetek átadása egy másodlagos témakörbe történik, ha az elsődleges témakör elérhetetlenné válik, vagy nem érhető el. 

![Aktív passzív minta](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

A minta és az előző minta közötti fő különbség az, hogy a replikáció egyirányú az elsődleges témakörből a másodlagos témakörbe. A másodlagos témakör soha nem lesz az elsődleges, de biztonsági mentési lehetőség, ha az elsődleges témakör átmenetileg használhatatlan. 

Ennek a mintának a használata az, hogy megpróbálja minimalizálni az ismétlődő feldolgozást. A replikálás során az `TimeToLive` üzenet tulajdonsága a replikált üzenetekben megadott időtartamra van állítva, amely azt jelzi, hogy az elsődleges hiba mikor fog feladatátvételt eredményezni. Ha például a használati eseti forgatókönyv a fogyasztót a másodlagosnál legfeljebb 1 percen belül átváltja az üzenetek elsődleges indítással való lekérése során, akkor a másodlagosnak ideális esetben az összes rendelkezésre álló üzenetet el kell érnie, hogy az elsődlegesen nem fér hozzá, de a problémák megkezdése előtt minimálisan megjelenő üzenetek száma. Ha `TimeToLive` ezt az időtartamot (2 perc) úgy állítja be, hogy a replikáció során ( `set sys.TimeToLive = '0:2:0'` a szabály műveletben), a másodlagos csak 2 percig őrizze meg az üzeneteket, és el fogja vetni a régieket. Ez azt jelenti, hogy amikor a fogadó átvált a másodlagosra, gyorsan átolvashatja a feldolgozott üzeneteket, és elvégezheti az utolsó feldolgozatlan üzenetek elvetését, majd feldolgozhatja azt az első üzenetből, amely még nem volt látható. A tényleges megőrzési időtartam függ a konkrét használati esettől, és gyorsan szeretne, és átválthat a másodlagosra az alkalmazásban. A `TimeToLive` beállítást néhány másodperc és nap között megbecsüljük. 

Míg az alkalmazás a másodlagost használja, közvetlenül is közzéteheti a másodlagos témakört, amely aztán bármely szokásos témakörként működik. A másodlagosra váltás után a fogyasztó a replikált üzenetek és az üzenetek egy részét is láthatja, amelyeket a rendszer közvetlenül a másodlagosnak tesz közzé. Az alkalmazásnak ezért először vissza kell váltania a közzétételt az elsődlegesre, és továbbra is engedélyezni kell a helyileg közzétett üzenetek kiürítését, mielőtt a fogyasztót visszaváltja a másodlagosra. Mivel a replikálás automatikusan folytatható, amint az elsődleges ismét elérhetővé válik, a fogyasztó az adott időszakban az elsődlegesnek küldött új üzeneteket is megszerezte, de valamivel nagyobb késéssel. 

> Ez a minta olyan forgatókönyvekhez alkalmas, amelyekben csak egyszer kell feldolgozni az üzeneteket. Az alkalmazásnak együtt kell működnie az elsődlegesen feldolgozott üzenetek nyomon követése miatt, mivel a rendszer duplikálja a feladatátvételi időszak időtartamát a másodlagosban, és ismét megkeresi az ismétlődéseket a visszaváltáskor.
> A megkettőzési feltételnek a legmegfelelőbb alkalmazásnak kell lennie `MessageId` . Az `EnqueuedTimeUtc` érték vízjel-kijelzőként is használható, de az alkalmazásnak lehetővé kell tennie bizonyos mennyiségű órajel-eltolódást (több másodpercet) az elsődleges és a másodlagos rendszer között bármely elosztott rendszeren.


#### <a name="spillover-replication"></a>Túlcsorduló replikáció

A "túlcsorduló" replikációs minta lehetővé teszi több Service Bus entitás aktív/aktív használatát több régióban, hogy a Service Bus Kifogástalan állapotba kerüljön, de a *fogyasztó* túlterheli a függőben lévő üzenetek számát, vagy nem érhető el. Ennek az lehet az oka, hogy a felhasználói folyamatot támogató adatbázis lassú vagy nem érhető el. Ez a minta egyszerű várólistákkal és témakör-előfizetésekkel működik.  

![Túlcsorduló replikáció](media/service-bus-federation-overview/spillover.jpg)  

Ahogy az ábrán is látható, a többtényezős replikálási minta a várólista vagy az előfizetés társított [kézbesítetlen levelek várólistáján](service-bus-dead-letter-queues.md) lévő üzeneteket replikálja egy párosított várólistára vagy témakörre egy másik névtérben. 

A meghibásodási helyzet hiányában a két névtér párhuzamosan használatos, amelyek mindegyike a teljes üzenet forgalmának egyes részhalmazait és a hozzájuk tartozó, az adott részhalmazt kezelő felhasználókat fogadja. Miután az egyik fogyasztó megkezdi a magas szintű meghibásodási arányt vagy leállást, a megfelelő üzenetek a kézbesítetlen levelek várólistáján jelennek meg, vagy túllépik a kézbesítések számát, vagy mert lejárnak. A replikációs feladatok ezután felveszik őket, majd újra sorba helyezni azokat a párosított várólistában, ahol ezt követően megjelennek a feltehetően kifogástalan fogyasztó számára. 

Ha a feldolgozásnak egy bizonyos határidőn belül kell történnie, akkor a `TimeToLive` várólistára és/vagy az üzenetekre úgy kell beállítani, hogy a feldolgozás továbbra is időben megtörténjen a másodlagos túllépéssel, például lehet, `TimeToLive` hogy a megengedett időtartam felére van állítva.

Ahogy a teljes körűen [aktív](#all-active-replication)mintában, az alkalmazás hozzáadhat egy mutatót az üzenethez, amely azt jelzi, hogy az üzenetet már egyszer replikálták-e a rendszer, hogy ne legyenek a várólisták párja

> Ez a minta olyan forgatókönyvekhez alkalmas, amelyekben a legfontosabb szempont, hogy megvédje a rendelkezésre állási problémákat a fogyasztók vagy az erőforrások számára, amelyeket a felhasználók használtak, valamint hogy átterjessze a forgalmi tüskéket az egyik párosított várólistán. Emellett védelmet nyújt az egyik névtérhez sem, ha a felhasználók mindkét várólistából beolvasják a felhasználókat, de a lejárat során kiszabott replikációs késés `TimeToLive` miatt előfordulhat, hogy az adott időtartományon belüli üzenetek a nem elérhető névtérben lévő területre mutatnak. 

### <a name="latency-optimization"></a>Késés optimalizálása 

A témakörök az információk több felhasználó számára történő terjesztésére szolgálnak. Bizonyos esetekben – különösen a széles földrajzi eloszlású felhasználók esetében – hasznos lehet az üzenetek egy témakörből való replikálása egy másodlagos névtérben a fogyasztóhoz közelebbi témakörben.

![Késés optimalizálása](media/service-bus-federation-overview/latency-optimization.jpg)  

Ha például a regionális, a kontinentális hubok közötti adatmegosztást szeretné elérni, akkor a rendszer hatékonyabban továbbítja az adatokat az hubok között, és a fogyasztóknak az adatok másolatát az adott hubokból kapják meg. 

A replikálási műveletek a kötegekben hajthatók végre, amelyeket a felhasználók gyakran szereznek be és rendeznek meg egyenként. Az 100 ms-os alaphálózati késéssel, azaz Észak-Amerika és Európa között minden üzenet a 200 MS-ot tovább dolgozza fel az üzenetek beszerzéséhez és rendezéséhez, az azonos régióban található entitáshoz képest. 

### <a name="validation-reduction-and-enrichment"></a>Ellenőrzés, csökkentés és dúsítás

Előfordulhat, hogy az üzenetek a saját megoldáson kívüli ügyfelek által Service Bus várólistába vagy témakörbe vannak beküldve.

![Ellenőrzés, csökkentés, dúsítás](media/service-bus-federation-overview/validation.jpg)  

Az ilyen üzenetek esetében szükség lehet az adott sémának való megfelelés ellenőrzésére, valamint a nem megfelelő üzenetek eldobására vagy elküldésére. Előfordulhat, hogy egyes üzeneteket a bonyolultsági arányban kell csökkenteni az adattörléssel, és előfordulhat, hogy a hivatkozásokat a hivatkozási adatkeresések alapján kell bővíteni. A műveletek a replikálási feladat egyéni funkcióival végezhetők el. 

### <a name="stream-to-queue-replication"></a>Stream – üzenetsor-replikáció

Az Azure Event Hubs ideális megoldás a bejövő események szélsőséges mennyiségének kezelésére. Azonban sem Event Hubs, sem hasonló motorok, például Apache Kafka olyan szolgáltatás által felügyelt [versengő fogyasztói](/azure/architecture/patterns/competing-consumers) modellt biztosítanak, amelyben több felhasználó is képes kezelni az azonos forrásból származó üzeneteket az ismétlődő feldolgozás kockázata nélkül, és végül a feldolgozásuk után végre kell rendezni ezeket az üzeneteket. 

![Integráció](media/service-bus-federation-overview/hub-to-queue.jpg)

Egy adatfolyam-várólista replikálásához a rendszer egy egyetlen Event hub-partíció tartalmát vagy egy teljes Event hub tartalmát egy Service Bus-várólistába továbbítja, ahonnan az üzenetek biztonságosan, tranzakciós és versengő fogyasztókkal dolgozhatók fel. Ez a replikálás azt is lehetővé teszi, hogy az üzenetek összes többi Service Bus funkcióját is használja, beleértve az útválasztást témakörökkel és munkamenet-alapú demultiplextel. 

### <a name="consolidation-and-normalization"></a>Összevonás és normalizálás 

A globális megoldások gyakran olyan regionális lábnyomokból állnak, amelyek nagy mértékben függetlenek, beleértve a saját feldolgozási képességeiket is, de a helyi és a globális perspektívák adatintegrációt igényelnek, és ezért az egyes üzeneteknek a helyi perspektívában kiértékelt központi összevonását. 

![Konszolidáció](media/service-bus-federation-overview/merge.jpg)

A normalizálás az összevonási forgatókönyv íze, amelynek során a két vagy több bejövő üzenet ugyanazt az információt használja, de különböző struktúrákkal vagy különböző kódolásokkal, és az üzeneteket át kell kódolni, vagy át kell alakítani a felhasználásuk előtt. 

A normalizálás olyan titkosítási munkákat is tartalmazhat, mint például a végpontok közötti titkosított adattartalom visszafejtése, valamint a különböző kulcsokkal és algoritmusokkal történő újratitkosítása az alárendelt fogyasztói közönség számára. 

### <a name="splitting-and-routing"></a>Felosztás és Útválasztás

Az Service Bus témaköröket és előfizetési szabályaikat gyakran használják egy adott közönségnek küldött üzenetek szűrésére, és a célközönség ezt követően beszerzett egy előfizetésből a szűrt készletet. 

![Felosztás](media/service-bus-federation-overview/split.jpg)

Olyan globális rendszerekben, ahol az üzenetek célközönsége globálisan elosztott vagy különböző alkalmazásokhoz tartozik, a replikálással az ilyen előfizetésből származó üzenetek átvihetők egy várólistára vagy témakörre egy másik névtérből, ahol azokat használják.

### <a name="replication-applications-in-azure-functions"></a>Azure Functions replikációs alkalmazásai

A fenti minták megvalósításához méretezhető és megbízható végrehajtási környezet szükséges a konfigurálni és futtatni kívánt replikációs feladatokhoz. Az Azure-ban az állapot nélküli feladatokhoz legjobban illeszkedő futásidejű környezet [Azure functions](../azure-functions/functions-overview.md). 

Azure Functions az [Azure által felügyelt identitásban](../active-directory/managed-identities-azure-resources/overview.md) futhat, így a replikálási feladatok integrálható a forrás-és a célként megadott szolgáltatások szerepköralapú hozzáférés-vezérlési szabályaival anélkül, hogy a titkokat a replikálási útvonalon kellene kezelnie. Az explicit hitelesítő adatokat igénylő replikációs források és célok esetében Azure Functions a [Azure Key Vaulton](../key-vault/general/overview.md)belül szigorúan hozzáférés-vezérlésű tárolóban tárolhatja a hitelesítő adatok konfigurációs értékeit.

Azure Functions továbbá lehetővé teszi, hogy a replikációs feladatok közvetlenül integrálva legyenek az Azure-beli virtuális hálózatokkal és [szolgáltatás-végpontokkal](../virtual-network/virtual-network-service-endpoints-overview.md) az összes Azure Messaging-szolgáltatáshoz, és könnyen integrálható legyen a [Azure monitor](../azure-monitor/overview.md).

A legjelentősebb Azure Functions az [azure Event Hubs](../azure-functions/functions-bindings-service-bus.md), az [Azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), a [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md)és az [Azure Queue Storage](../azure-functions/functions-bindings-storage-queue.md), az egyéni bővítmények a [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)és a [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)számára előre összeállított, skálázható eseményindítókkal és kimeneti kötésekkel rendelkeznek. A legtöbb eseményindító dinamikusan alkalmazkodik az adatátviteli igényekhez azáltal, hogy a dokumentált metrikák alapján a párhuzamosan végrehajtás alatt álló példányok számát megnöveli. 

A Azure Functions fogyasztási csomaggal az előre elkészített eseményindítók akár nullára is csökkenthetők, amíg nem állnak rendelkezésre üzenetek a replikáláshoz, ami azt jelenti, hogy a konfiguráció méretezése a biztonsági mentésre készen áll. A használati terv használatának hátránya, hogy a replikálási feladatok "felébrednek" késése jelentősen meghaladja azokat a üzemeltetési terveket, amelyeken az infrastruktúra fut.  

Ezzel szemben a leggyakoribb replikációs motorok az üzenetküldéshez és az események elvégzéséhez, például a Apache Kafka [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) megköveteli egy üzemeltetési környezet megadását és a replikációs motor méretezését. Ez magában foglalja a biztonsági és hálózati funkciók konfigurálását és integrálását, valamint a figyelési adatok áramlását, és így továbbra sem lehet egyéni replikációs feladatokat beszúrni a folyamatba. 

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben számos összevonási mintát ismertetünk, és kifejtjük Azure Functions szerepét az Azure-beli esemény-és üzenetkezelési replikálási futtatókörnyezetként. 

Ezután érdemes elolvasnia, hogyan állíthat be egy replikátor alkalmazást a Azure Functions, majd hogyan replikálhatja az esemény-folyamatokat Event Hubs és számos más esemény-és üzenetkezelő rendszer között:

- [Azure Functions replikációs alkalmazásai](service-bus-federation-replicator-functions.md)
- [Események replikálása Service Bus entitások között](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Események továbbítása az Azure Event Hubsba](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Események beolvasása az Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif