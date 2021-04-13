---
title: Azure Storage-üzenetsorok és Service Bus-üzenetsorok összehasonlítása
description: Elemzi az Azure által kínált két típusú várólista közötti különbségeket és hasonlóságokat.
ms.topic: article
ms.date: 04/12/2021
ms.openlocfilehash: 1c3b0fda12d5e301b17a342c5d5ed11ab76c76da
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304358"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Tárolási várólisták és Service Bus várólisták – összehasonlítás és kontrasztos
Ez a cikk a Microsoft Azure: Storage Queues és Service Bus Queues által kínált két típusú várólista közötti különbségeket és hasonlóságokat elemzi. Ezen információk használatával tájékozott döntést hozhat arról, hogy melyik megoldás felel meg legjobban az igényeinek.

## <a name="introduction"></a>Bevezetés
Az Azure két típusú várólista-mechanizmust támogat: a **tárolási várólistákat** és a **Service Bus várólistákat**.

A **tárolási várólisták** az [Azure Storage](https://azure.microsoft.com/services/storage/) -infrastruktúra részét képezik. Lehetővé teszik nagy mennyiségű üzenet tárolását. A HTTP-vagy HTTPS-t használó hitelesített hívásokon keresztül érheti el az üzeneteket a világ bármely pontján. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra. További információ: [Mi az Azure Storage Queues](../storage/queues/storage-queues-introduction.md).

A **Service Bus Queues** egy szélesebb körű [Azure-üzenetkezelési](https://azure.microsoft.com/services/service-bus/) infrastruktúra része, amely támogatja a üzenetsor-kezelési, a közzétételi/előfizetési és a fejlettebb integrációs mintákat. Ezek olyan alkalmazások vagy alkalmazás-összetevők integrálására lettek kialakítva, amelyek több kommunikációs protokollt, adategyezményt, megbízhatósági tartományt vagy hálózati környezetet is kiterjedhetnek. Service Bus várólistákkal/témakörökkel/előfizetésekkel kapcsolatos további információkért tekintse meg a [Service Bus várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)című témakört.


## <a name="technology-selection-considerations"></a>A technológia kiválasztási szempontjai
A tárolási várólisták és a Service Bus várólisták némileg eltérő szolgáltatáskészlet-készlettel rendelkeznek. Az adott megoldás igényeitől függően választhat egyet vagy akár mindkettőt is.

Ha meghatározza, hogy az adott megoldás melyik üzenetsor-kezelő technológiához igazodik, a megoldás-építészeknek és a fejlesztőknek figyelembe kell venniük ezeket a javaslatokat. 

### <a name="consider-using-storage-queues"></a>Tárolási várólisták használata
Megoldás-építészként/fejlesztőként érdemes **megfontolni a tárolási várólisták használatát** a következők esetén:

* Az alkalmazásnak egy várólistán több mint 80 gigabájt üzenetet kell tárolnia.
* Az alkalmazás nyomon szeretné követni a várólistán lévő üzenetek feldolgozásának folyamatát. Ez akkor hasznos, ha az üzenetet feldolgozó dolgozó összeomlik. Egy másik feldolgozó ezt az információt használhatja annak folytatására, hogy az előző feldolgozó abbahagyta a munkát.
* A várólistákon végrehajtott összes tranzakció kiszolgálóoldali naplófájljaira van szükség.

### <a name="consider-using-service-bus-queues"></a>Érdemes Service Bus várólistákat használni
Megoldás-építészként/fejlesztőként érdemes **megfontolni Service Bus várólisták használatát** , ha:

* A megoldásnak üzeneteket kell fogadnia a várólista lekérdezése nélkül. A Service Bus használatával a Service Bus által támogatott TCP-alapú protokollok használatával hosszú lekérdezési fogadási művelettel valósítható meg.
* A megoldáshoz szükséges, hogy a várólista biztosítson egy garantált, első alkalommal kifelé irányuló (FIFO) rendezett kézbesítést.
* A megoldásnak támogatnia kell az automatikus ismétlődő észlelést.
* Azt szeretné, hogy az alkalmazás párhuzamos, hosszan futó adatfolyamként dolgozza fel az üzeneteket (az üzenetek az üzenet **munkamenet-azonosító** tulajdonságával vannak társítva). Ebben a modellben a felhasználó alkalmazás minden csomópontja verseng a streamek számára, az üzenetek helyett. Ha egy adatfolyamot egy felhasználó csomóponthoz adnak, a csomópont a tranzakciók használatával megvizsgálhatja az alkalmazás-adatfolyam állapotát.
* A megoldás tranzakciós viselkedést és atomi kezelést igényel, ha több üzenetet küld vagy fogad egy várólistából.
* Az alkalmazás a 64 KB-nál nagyobb méretű üzeneteket kezel, de valószínűleg nem fogja tudni megközelíteni az 256-KB korlátot.
* A szerepkör-alapú hozzáférési modellnek a várólistákhoz való biztosításához, valamint a küldők és a fogadók számára a különböző jogokkal/engedélyekkel kell foglalkoznia. További információért tekintse át a következő cikkeket:
    - [Hitelesítés felügyelt identitásokkal](service-bus-managed-service-identity.md)
    - [Hitelesítés egy alkalmazásból](authenticate-application.md)
* A várólista mérete nem növekszik 80 GB-nál nagyobb mértékben.
* A AMQP 1,0 szabványokon alapuló üzenetküldési protokollt kívánja használni. További információ a AMQP: [Service Bus AMQP áttekintése](service-bus-amqp-overview.md).
* A várólista-alapú pont-pont típusú kommunikációról egy közzétételi és előfizetési üzenetküldési mintára vonatkozó végleges áttelepítést képzel el. Ez a minta lehetővé teszi a további fogadók (előfizetők) integrálását. Minden fogadó megkapja a várólistára küldött néhány vagy minden üzenet független másolatát. 
* Az üzenetkezelési megoldásnak támogatnia kell a "legfeljebb egyszeri" kézbesítési garanciát anélkül, hogy létre kellene hoznia a további infrastruktúra-összetevőket.
* A megoldásnak az üzenetek kötegeit kell közzétennie és felhasználnia.

## <a name="compare-storage-queues-and-service-bus-queues"></a>Tárolási várólisták és Service Bus várólisták összehasonlítása
A következő szakaszokban szereplő táblázatok a várólista-funkciók logikai csoportosítását biztosítják. Lehetővé teszik, hogy összehasonlítsa az Azure Storage-várólistákban és a Service Bus-várólistákban elérhető képességeket.

## <a name="foundational-capabilities"></a>Alapvető képességek
Ez a szakasz összehasonlítja a tárolási várólisták és a Service Bus várólisták által biztosított alapvető üzenetsor-kezelő képességeket.

| Összehasonlítási feltételek | Tárolási üzenetsorok | Service Bus-üzenetsorok |
| --- | --- | --- |
| Megrendelés jótállása |**Nem** <br/><br>További információ: az első megjegyzés a [További információk](#additional-information) szakaszban.</br> | **Igen – elsőként elsőként ki (FIFO)**<br/><br>(üzenet- [munkamenetek](message-sessions.md)használatával) |
| Kézbesítési garancia |**Legalább egyszer** |**Legalább egyszeri** (PeekLock fogadási mód használata). Ez az alapértelmezett érték) <br/><br/>**Legfeljebb egyszeri** (ReceiveAndDelete fogadási mód használata) <br/> <br/> További információ a különböző [fogadási módokról](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Atomi működés támogatása |**Nem** |**Igen**<br/><br/> |
| Fogadások viselkedése |**Nem blokkoló**<br/><br/>(azonnal befejeződik, ha nem található új üzenet) |**Blokkolás időtúllépéssel vagy anélkül**<br/><br/>(hosszú lekérdezéseket, vagy az ["Comet Technique"](https://go.microsoft.com/fwlink/?LinkId=613759)-t kínál)<br/><br/>**Nem blokkoló**<br/><br/>(csak .NET felügyelt API-k használata) |
| Leküldéses stílusú API |**Nem** |**Igen**<br/><br/>A .NET, a Java, a JavaScript és a go SDK-k leküldéses stílusú API-t biztosítanak. |
| Fogadási mód |**Betekintés & bérletbe** |**Betekintés & zárolás**<br/><br/>**Fogadás & törlés** |
| Kizárólagos hozzáférési mód |**Bérlet-alapú** |**Zárolási alapú** |
| Bérlet/zárolás időtartama |**30 másodperc (alapértelmezett)**<br/><br/>**7 nap (maximum)** (a [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API használatával megújíthatja vagy felszabadíthatja az üzenetek bérletét). |**30 másodperc (alapértelmezett)**<br/><br/>Minden alkalommal megújíthatja az üzenet zárolását ugyanazon zárolási időtartamra, vagy használhatja az automatikus zárolás megújítása funkciót, ahol az ügyfél kezeli a zárolás megújítását. |
| Bérlet/zárolás pontossága |**Üzenet szintje**<br/><br/>Minden üzenet rendelkezhet eltérő időtúllépési értékkel, amelyet az üzenet feldolgozásakor szükség szerint frissíthet az [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) API használatával. |**Várólista szintje**<br/><br/>(minden várólistához tartozik egy zárolási pontosság, amely az összes üzenetére vonatkozik, de a zárolás megújítható az előző sorban leírtak szerint) |
| Kötegelt fogadás |**Igen**<br/><br/>(explicit módon megadhatja az üzenetek számát az üzenetek beolvasása során, legfeljebb 32 üzenet) |**Igen**<br/><br/>(a beolvasás előtti tulajdonság vagy explicit módon a tranzakciók használatával) |
| Kötegelt küldés |**Nem** |**Igen**<br/><br/>(tranzakciók vagy ügyféloldali kötegek használatával) |

### <a name="additional-information"></a>További információ
* A tárolási várólisták üzenetei általában elsők, de előfordulhat, hogy az üzenetek sorrendje nem lehetséges. Például ha egy üzenet láthatósága-időtúllépési időtartama lejár, mert egy ügyfélalkalmazás egy üzenet feldolgozásakor összeomlott. Ha a láthatósági időkorlát lejár, az üzenet ismét láthatóvá válik a várólistán egy másik feldolgozó számára, hogy a várólistára kerüljön. Ekkor előfordulhat, hogy az újonnan látható üzenet el lesz helyezve a várólistába.
* A Service Bus Queues-ben garantált FIFO-minta az üzenetkezelési munkamenetek használatát igényli. Ha az alkalmazás összeomlik, miközben a **betekintés & zárolási** módban kapott üzenetet dolgozza fel, akkor a várólista következő fogadója fogad egy üzenetküldési munkamenetet, és a sikertelen üzenet jelenik meg, miután lejár az üzenet élettartama (TTL).
* A tárolási várólisták úgy lettek kialakítva, hogy támogassák a szabványos üzenetsor-kezelő forgatókönyveket, például a következőket:
    - Az alkalmazás-összetevők leválasztása a méretezhetőség és a hibák hibatűrésének növeléséhez
    - Terhelés simítása
    - Folyamat munkafolyamatainak kiépítése.
* A Service Bus munkamenetek kontextusában az üzenetek kezelésével kapcsolatos következetlenségek elkerülhetők, ha a munkamenet-állapottal szeretné tárolni az alkalmazás állapotát a munkamenet üzenet-sorrendjének kezeléséhez képest, valamint a fogadott üzenetek rendezése és a munkamenet-állapot frissítése közötti tranzakciók használatával. Ez a fajta konzisztencia-szolgáltatás néha pontosan egyszer van megjelölve a többi gyártó termékeinek *feldolgozásával* . A tranzakciós hibák nyilvánvalóan az üzenetek újrakézbesítését okozzák, ezért a kifejezés nem pontosan megfelelő.
* A Storage-várólisták egységes és konzisztens programozási modellt biztosítanak a várólisták, táblák és Blobok között, a fejlesztők és az operatív csapatok számára egyaránt.
* Service Bus a várólisták a helyi tranzakciók támogatását biztosítják egyetlen üzenetsor kontextusában.
* A Service Bus által támogatott **fogadási és törlési** mód lehetővé teszi az üzenetkezelési műveletek számának (és a kapcsolódó költségek) csökkentését az Exchange-ben a lecsökkentett kézbesítési garancia érdekében.
* A tárolási várólisták bérleteket biztosítanak az üzenetek bérletének meghosszabbítására. Ez a funkció lehetővé teszi, hogy a munkavégző folyamatok rövid bérleteket tartsanak fenn az üzenetekben. Tehát ha egy feldolgozó összeomlik, az üzenetet gyorsan fel lehet dolgozni egy másik feldolgozó. Emellett a feldolgozók kiterjeszthetik a bérletet egy üzenetben, ha az a jelenlegi címbérleti időpontnál hosszabb ideig fel kell dolgoznia.
* A tárolási várólisták olyan láthatósági időtúllépést biztosítanak, amelyet egy üzenet enqueuing vagy dequeuing szolgáltatásával lehet beállítani. Emellett frissítheti a különböző címbérleti értékekkel rendelkező üzeneteket futásidőben, és frissítheti a különböző értékeket ugyanazon a várólistán lévő üzenetek között. Service Bus zárolási időtúllépések definiálva vannak a várólista metaadataiban. Ugyanakkor megújíthatja az üzenet zárolását az előre definiált zárolás időtartamára manuálisan, vagy használhatja az automatikus zárolás megújítása funkciót, amelyben az ügyfél kezeli a zárolás megújítását.
* Service Bus várólistán a blokkoló fogadási művelet maximális időtúllépése 24 nap. Azonban a REST-alapú időtúllépések maximális értéke 55 másodperc.
* A Service Bus által biztosított ügyféloldali kötegek lehetővé teszik, hogy egy üzenetsor-ügyfél több üzenetet is egy küldési műveletbe írjon. A kötegelt feldolgozás csak aszinkron küldési műveletekhez érhető el.
* Az olyan funkciók, mint például a tárolási várólisták 200 – TB-os felső határa (többek között a fiókok virtualizálása) és a korlátlan várólisták ideális platformot biztosítanak az SaaS-szolgáltatók számára.
* A tárolási várólisták rugalmas és teljesítményű delegált hozzáférés-vezérlési mechanizmust biztosítanak.

## <a name="advanced-capabilities"></a>Speciális képességek
Ez a szakasz a tárolási várólisták és a Service Bus várólisták speciális képességeit hasonlítja össze.

| Összehasonlítási feltételek | Tárolási üzenetsorok | Service Bus-üzenetsorok |
| --- | --- | --- |
| Ütemezett kézbesítés |**Igen** |**Igen** |
| Automatikus kézbesítetlen levelek |**Nem** |**Igen** |
| A várólista-élettartam értékének növelése |**Igen**<br/><br/>(a láthatósági időkorlát helyben történő frissítése révén) |**Igen**<br/><br/>(dedikált API-függvénnyel biztosítva) |
| Megmérgezhető üzenetek támogatása |**Igen** |**Igen** |
| Helyben történő frissítés |**Igen** |**Igen** |
| Kiszolgálóoldali tranzakciónapló |**Igen** |**Nem** |
| Tárolási metrikák |**Igen**<br/><br/>A **perc mérőszámok** valós idejű mérőszámokat biztosítanak a rendelkezésre állás, a TPS, az API-hívások száma, a hibák száma és egyebek tekintetében. Ezek valós időben vannak összesítve, és néhány percen belül jelentést kapnak arról, hogy mi történt az éles környezetben. További információ: [a Storage Analytics mérőszámok ismertetése](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Igen**<br/><br/>Az Azure Service Bus által támogatott metrikákkal kapcsolatos információkért lásd: [üzenet metrikái](service-bus-metrics-azure-monitor.md#message-metrics). |
| Állapotkezelés |**Nem** |**Igen** (aktív, letiltva, SendDisabled, ReceiveDisabled. Ezekről az állapotokról a [várólista állapota](entity-suspend.md#queue-status)című témakörben talál további információt. |
| Üzenet-továbbítás |**Nem** |**Igen** |
| Várólista-törlési függvény |**Igen** |**Nem** |
| Üzenetek csoportjai |**Nem** |**Igen**<br/><br/>(üzenetküldési munkamenetek használatával) |
| Alkalmazás állapota csoportonként |**Nem** |**Igen** |
| Duplikálás észlelése |**Nem** |**Igen**<br/><br/>(a küldő oldalon konfigurálható) |
| Tallózási üzenetek csoportjai |**Nem** |**Igen** |
| Üzenet-munkamenetek beolvasása azonosító alapján |**Nem** |**Igen** |

### <a name="additional-information"></a>További információ
* Mindkét üzenetsor-kezelő technológia lehetővé teszi, hogy egy későbbi időpontban ütemezhető legyen az üzenet kézbesítése.
* A várólista-továbbítás lehetővé teszi több ezer várólista számára, hogy az üzeneteiket egyetlen várólistára továbbítsák, amelyből a fogadó alkalmazás felhasználja az üzenetet. Ezt a mechanizmust használhatja a biztonság, a vezérlési folyamat és a tárolók elkülönítésére az egyes üzenetek közzétevői között.
* A tárolási várólisták támogatást nyújtanak az üzenetek tartalmának frissítéséhez. Ezzel a funkcióval megtarthatja az állapotadatokat és a növekményes előrehaladási frissítéseket az üzenetbe, hogy az utolsó ismert ellenőrzőpontról feldolgozható legyen, és ne a semmiből kellene kezdenie. Service Bus várólistákkal ugyanezt a forgatókönyvet is engedélyezheti az üzenet-munkamenetek használatával. További információ: [üzenetküldési munkamenet állapota](message-sessions.md#message-session-state).
* Service Bus várólisták támogatják a [Kézbesítetlen betűket](service-bus-dead-letter-queues.md). Hasznos lehet az alábbi feltételeknek megfelelő üzenetek elkülönítéséhez:
    - A fogadó alkalmazás nem tudja sikeresen feldolgozni az üzeneteket 
    - Az üzenetek a lejárt élettartam (TTL) tulajdonság miatt nem érhetik el a célját. A TTL érték azt határozza meg, hogy mennyi ideig marad az üzenet a várólistán. Service Bus esetén az üzenet egy $DeadLetterQueue nevű speciális várólistába kerül, amikor a TTL-időszak lejár.
* Ha meg szeretné keresni a "méreg" üzeneteket a tárolási várólistákban, amikor egy üzenet dequeuing üzenetet küld, az alkalmazás megvizsgálja az üzenet [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) tulajdonságát. Ha a **DequeueCount** nagyobb, mint egy megadott küszöbérték, az alkalmazás áthelyezi az üzenetet egy alkalmazás által definiált "kézbesítetlen levél" várólistára.
* A tárolási várólisták lehetővé teszik a várólistán végrehajtott összes tranzakció részletes naplójának, valamint az összesített mérőszámok beszerzését. Mindkét lehetőség hasznos lehet a hibakereséshez és annak megismeréséhez, hogy az alkalmazás hogyan használja a tárolási várólistákat. Emellett hasznosak lehetnek az alkalmazások teljesítményének finomhangolásához és a várólisták használatának költségeinek csökkentéséhez.
* A Service Bus által támogatott [üzenetküldési munkamenetek](message-sessions.md) lehetővé teszik, hogy a logikai csoporthoz tartozó üzenetek egy fogadóhoz legyenek társítva. Az üzenetek és a hozzájuk tartozó fogadók közötti kapcsolathoz hasonló affinitást hoz létre. Ezt a speciális funkciót a Service Busban is engedélyezheti, ha a munkamenet-azonosító tulajdonságot egy üzenetre állítja be. A fogadók ezt követően egy adott munkamenet-azonosítót és a megadott munkamenet-azonosítót megosztó üzeneteket fogadhatnak.
* Service Bus várólisták ismétlődés-észlelési funkciója automatikusan eltávolítja az üzenetsor vagy témakör számára küldött duplikált üzeneteket az üzenet azonosítója tulajdonság értéke alapján.

## <a name="capacity-and-quotas"></a>Kapacitás és kvóták
Ez a szakasz a tárolási várólistákat és a Service Bus várólistákat a lehetséges [kapacitás és kvóták](service-bus-quotas.md) szempontjából hasonlítja össze.

| Összehasonlítási feltételek | Tárolási üzenetsorok | Service Bus-üzenetsorok |
| --- | --- | --- |
| Várólista maximális mérete |**500 TB**<br/><br/>( [egyetlen Storage-fiók kapacitására](../storage/common/storage-introduction.md#queue-storage)korlátozódik) |**1 GB – 80 GB**<br/><br/>(az üzenetsor létrehozásával és a [particionálás engedélyezésével](service-bus-partitioning.md) van meghatározva – lásd a "További információk" szakaszt) |
| Üzenetek maximális mérete |**64 KB**<br/><br/>(48 KB **Base64** kódolás használatakor)<br/><br/>Az Azure a várólisták és a Blobok kombinálásával is támogatja a nagyméretű üzeneteket, ekkor akár 200 GB-ot is sorba helyezni egyetlen elemhez. |**256 kb** vagy **1 MB**<br/><br/>(beleértve a fejlécet és a törzset is, a fejléc maximális mérete: 64 KB).<br/><br/>A [szolgáltatási szinttől](service-bus-premium-messaging.md)függ. |
| Üzenetek maximális élettartama (TTL) |**Infinite** (API-Version 2017-07-27 vagy újabb) |**TimeSpan. max** |
| Várólisták maximális száma |**Korlátlan** |**10 000**<br/><br/>(szolgáltatási névtér esetében) |
| Egyidejű ügyfelek maximális száma |**Korlátlan** |**5 000** |

### <a name="additional-information"></a>További információ
* Service Bus kényszeríti a várólista méretének korlátozásait. Várólista létrehozásakor a várólista maximális mérete van megadva. 1 GB és 80 GB között lehet. Ha a várólista mérete eléri ezt a korlátot, a rendszer elutasítja a további bejövő üzeneteket, és a hívó kivételt kap. További információ a Service Bus kvótákkal kapcsolatban: [Service Bus kvóták](service-bus-quotas.md).
* A particionálás nem támogatott a [prémium](service-bus-premium-messaging.md)szinten. A standard szintű üzenetkezelési szinten Service Bus várólistákat és témaköröket hozhat létre 1 (alapértelmezett), 2, 3, 4 vagy 5 GB méretű méretben. Ha engedélyezve van a particionálás, a Service Bus 16 példányt (16 partíciót) hoz létre az entitásból, és mindegyiket azonos méretben adja meg. Így ha egy 5 GB méretű, 16 partíciót tartalmazó várólistát hoz létre, akkor a maximális várólista mérete (5 * 16) = 80 GB lesz.  Megtekintheti a particionált üzenetsor vagy témakör maximális méretét a [Azure Portal][Azure portal].
* A Storage Queues használata esetén, ha az üzenet tartalma nem XML-Safe, akkor **Base64** kódolású kell lennie. Ha **Base64** kódolással kódolja az üzenetet, a felhasználói adattartalom legfeljebb 48 kb lehet, 64 kb helyett.
* A Service Bus várólisták esetében az üzenetsor-sorokban tárolt összes üzenet két részből áll: egy fejlécből és egy törzsből. Az üzenet teljes mérete nem haladhatja meg a szolgáltatási szintet támogató üzenetek maximális méretét.
* Ha az ügyfelek a TCP protokollon keresztül kommunikálnak Service Bus várólistákkal, az egyService Busos üzenetsor egyidejű kapcsolatainak maximális száma a 100-ra korlátozódik. Ez a szám a küldők és a fogadók között van megosztva. Ha eléri ezt a kvótát, a további kapcsolatokra vonatkozó kérések el lesznek utasítva, és a hívási kód kivételt kap. Ez a korlát nem vonatkozik a várólistákat REST-alapú API-k használatával összekötő ügyfelekre.
* Ha több mint 10 000 várólistára van szüksége egyetlen Service Bus névtérben, vegye fel a kapcsolatot az Azure támogatási csoportjával, és növelje a növekményt. Ha az 10 000-es várólistán kívülre szeretné méretezni a Service Bus, további névtereket is létrehozhat a [Azure Portal][Azure portal]használatával.

## <a name="management-and-operations"></a>Felügyelet és műveletek
Ez a szakasz összehasonlítja a tárolási várólisták és Service Bus várólisták által biztosított felügyeleti szolgáltatásokat.

| Összehasonlítási feltételek | Tárolási üzenetsorok | Service Bus-üzenetsorok |
| --- | --- | --- |
| Felügyeleti protokoll |**REST HTTP/HTTPS-n keresztül** |**REST HTTPS-kapcsolaton keresztül** |
| Futásidejű protokoll |**REST HTTP/HTTPS-n keresztül** |**REST HTTPS-kapcsolaton keresztül**<br/><br/>**AMQP 1,0 standard (TCP TLS-sel)** |
| .NET API |**Igen**<br/><br/>(.NET Storage ügyféloldali API) |**Igen**<br/><br/>(.NET Service Bus API) |
| Natív C++ |**Igen** |**Igen** |
| Java API |**Igen** |**Igen** |
| PHP API |**Igen** |**Igen** |
| Node.js API |**Igen** |**Igen** |
| Tetszőleges metaadat-támogatás |**Igen** |**Nem** |
| Várólista elnevezési szabályai |**Legfeljebb 63 karakter hosszú lehet**<br/><br/>(A várólista nevének kisbetűnek kell lennie.) |**Legfeljebb 260 karakter hosszú lehet**<br/><br/>(A várólista elérési útja és neve nem megkülönbözteti a kis-és nagybetűket.) |
| Várólista hosszának beolvasása függvény |**Igen**<br/><br/>(Megközelítő érték, ha az üzenetek törlés nélkül lejárnak az ÉLETTARTAMon kívül.) |**Igen**<br/><br/>(Pontos, időponthoz megadott érték.) |
| Betekintés függvény |**Igen** |**Igen** |

### <a name="additional-information"></a>További információ
* A tárolási várólisták olyan tetszőleges attribútumok támogatását biztosítják, amelyek a várólista leírására alkalmazhatók név/érték párok formájában.
* A várólista-technológiák mindkét esetben lehetővé teszik az üzenetek betekintését a zárolás nélkül, ami hasznos lehet a üzenetsor-kezelő/böngésző eszköz megvalósítása során.
* A Service Bus .NET-alapú üzenetkezelési API-k teljes kétirányú TCP-kapcsolatok használatával javítják a teljesítményt a HTTP-n keresztüli REST-kapcsolathoz képest, és támogatják a AMQP 1,0 standard protokollt.
* A tárolási várólisták nevei 3-63 karakter hosszúak lehetnek, kisbetűket, számokat és kötőjeleket tartalmazhatnak. További információ: a [várólisták és a metaadatok elnevezése](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Service Bus üzenetsor neve legfeljebb 260 karakter hosszúságú lehet, és kevésbé korlátozó elnevezési szabályokkal rendelkezhet. Service Bus üzenetsor neve betűket, számokat, pontokat, kötőjeleket és aláhúzást tartalmazhat.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
Ez a szakasz a tárolási várólisták és Service Bus várólisták által támogatott hitelesítési és engedélyezési funkciókat ismerteti.

| Összehasonlítási feltételek | Tárolási üzenetsorok | Service Bus-üzenetsorok |
| --- | --- | --- |
| Hitelesítés |**Szimmetrikus kulcs** |**Szimmetrikus kulcs** |
| Biztonsági modell |Delegált hozzáférés SAS-tokeneken keresztül. |SAS |
| Identitás-szolgáltató összevonása |**Igen** |**Igen** |

### <a name="additional-information"></a>További információ
* A várólista-technológiák bármelyikére vonatkozó kérelmet hitelesíteni kell. A névtelen hozzáféréssel rendelkező nyilvános várólisták nem támogatottak. Az [sas](service-bus-sas.md)használatával ezt a forgatókönyvet csak írható sas, írásvédett sas vagy akár teljes hozzáférésű sas közzétételével kezelheti.
* A tárolási várólisták által biztosított hitelesítési séma egy szimmetrikus kulcs használatát foglalja magában. Ez a kulcs egy kivonatoló alapú üzenethitelesítő kód (HMAC), amely az SHA-256 algoritmussal lett kiszámítva, és **Base64** -karakterláncként van kódolva. További információ a megfelelő protokollról: [Az Azure Storage-szolgáltatások hitelesítése](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). A Service Bus-várólisták a szimmetrikus kulcsokat használó hasonló modellt támogatják. További információ: [közös hozzáférésű aláírások hitelesítése Service Bussal](service-bus-sas.md).

## <a name="conclusion"></a>Összegzés
A két technológia mélyebb megismerése révén tájékozott döntéseket hozhat, amelyekkel a várólista-technológiát és a-t használhatja. A tárolási várólisták vagy Service Bus-várólisták használatára vonatkozó döntés egyértelműen számos tényezőtől függ. Ezek a tényezők nagy mértékben befolyásolhatják az alkalmazás és az architektúrájuk egyedi igényeit. 

Érdemes lehet a tárolási várólistákat választani, például a következő esetekben:

- Ha az alkalmazás már a Microsoft Azure alapvető képességeit használja 
- Ha alapszintű kommunikációra és üzenetküldésre van szüksége a szolgáltatások között 
- Az 80 GB-nál nagyobb méretű várólistákra van szükség

Service Bus Queues számos olyan speciális funkciót biztosít, mint például a következők. Így érdemes lehet választani, ha hibrid alkalmazást készít, vagy ha az alkalmazása más funkciókat is igényel.

- [Munkamenetek](message-sessions.md)
- [Tranzakciók](service-bus-transactions.md)
- [Duplikálás észlelése](duplicate-detection.md)
- [Automatikus kézbesítetlen levelek](service-bus-dead-letter-queues.md)
- [Tartós közzétételi és előfizetési lehetőségek](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>Következő lépések
A következő cikkek további útmutatást és információkat nyújtanak a tárolási várólisták vagy Service Bus várólisták használatával kapcsolatban.

* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Az Queue Storage szolgáltatás használata](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Ajánlott eljárások a teljesítmény növeléséhez Service Bus felügyelt üzenetkezelés használatával](service-bus-performance-improvements.md)
* [A Azure Service Bus várólistáinak és témaköreinek bemutatása (blogbejegyzés)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [A Service Bus fejlesztői útmutatója](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Az Azure-beli üzenetsor-kezelő szolgáltatás használata](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

