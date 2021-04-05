---
title: A Queue Storage-Azure Storage teljesítmény-és méretezhetőségi ellenőrzőlistája
description: A nagy teljesítményű alkalmazások fejlesztéséhez Queue Storage használható bevált eljárások ellenőrzőlistája.
author: tamram
services: storage
ms.author: tamram
ms.date: 10/10/2019
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 4040a81d5b509ddbdd355953e28721a7c9fccfb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585666"
---
<!-- docutune:casing "Timeout and Server Busy errors" -->

# <a name="performance-and-scalability-checklist-for-queue-storage"></a>A Queue Storage teljesítményére és méretezhetőségére vonatkozó ellenőrzőlista

A Microsoft számos bevált gyakorlatot fejlesztett ki a nagy teljesítményű alkalmazások Queue Storage való fejlesztéséhez. Ez az ellenőrzőlista azokat a kulcsfontosságú eljárásokat azonosítja, amelyeket a fejlesztők követhetnek a teljesítmény optimalizálása érdekében. Tartsa szem előtt ezeket a gyakorlatokat az alkalmazás tervezésekor és a folyamat során.

Az Azure Storage méretezhetőségi és teljesítménybeli célokat biztosít a kapacitáshoz, a tranzakciós sebességhez és a sávszélességhez. További információ az Azure Storage skálázhatósági céljairól: a [méretezhetőségi és teljesítményi célok a standard szintű Storage-fiókok esetében](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) , valamint [a méretezhetőség és a teljesítményre vonatkozó célok a Queue Storage számára](scalability-targets.md).

## <a name="checklist"></a>Ellenőrzőlista

Ez a cikk bevált eljárásokat szervez a teljesítményre vonatkozóan a Queue Storage alkalmazás fejlesztése során követendő ellenőrzőlista keretében.

| Kész | Kategória | Tervezési szempont |
|--|--|--|
| &nbsp; | Méretezhetőségi célok | [Megtervezheti, hogy az alkalmazás ne legyen több, mint a Storage-fiókok maximális száma?](#maximum-number-of-storage-accounts) |
| &nbsp; | Méretezhetőségi célok | [Elkerüli a kapacitást és a tranzakciós korlátokat?](#capacity-and-transaction-targets) |
| &nbsp; | Hálózatkezelés | [A szükséges teljesítmény elérése érdekében az ügyféloldali eszközök megfelelően nagy sávszélességgel és kis késéssel rendelkeznek?](#throughput) |
| &nbsp; | Hálózatkezelés | [Az ügyféloldali eszközök magas színvonalú hálózati kapcsolattal rendelkeznek?](#link-quality) |
| &nbsp; | Hálózatkezelés | [Az ügyfélalkalmazás ugyanabban a régióban található, mint a Storage-fiók?](#location) |
| &nbsp; | Közvetlen ügyfél-hozzáférés | [Közös hozzáférésű aláírásokat (SAS) és több eredetű erőforrás-megosztást (CORS) használ az Azure Storage-hoz való közvetlen hozzáférés engedélyezéséhez?](#sas-and-cors) |
| &nbsp; | .NET-konfiguráció | [A .NET Core 2,1-as vagy újabb verzióját használja az optimális teljesítmény érdekében?](#use-net-core) |
| &nbsp; | .NET-konfiguráció | [Konfigurálta az ügyfelet az egyidejű kapcsolatok megfelelő számának használatára?](#increase-default-connection-limit) |
| &nbsp; | .NET-konfiguráció | [A .NET-alkalmazások esetében megfelelő számú szál használatára konfigurálta a .NET-et?](#increase-the-minimum-number-of-threads) |
| &nbsp; | Párhuzamosság | [Gondoskodott arról, hogy a párhuzamosságok megfelelően legyenek kötve, hogy ne legyenek túlterhelve az ügyfél képességei, vagy közelítse meg a méretezhetőségi célokat?](#unbounded-parallelism) |
| &nbsp; | Eszközök | [A Microsoft által biztosított ügyféloldali kódtárak és eszközök legújabb verzióit használja?](#client-libraries-and-tools) |
| &nbsp; | Újrapróbálkozások | [Újrapróbálkozási szabályzatot használ egy exponenciális leállítási a hibák és időtúllépések szabályozásához?](#timeout-and-server-busy-errors) |
| &nbsp; | Újrapróbálkozások | [Az alkalmazás elkerüli a nem újrapróbálkozást okozó hibák újrapróbálkozását?](#non-retryable-errors) |
| &nbsp; | Konfiguráció | [Kikapcsolta a Nyéki algoritmust a kis kérelmek teljesítményének javítása érdekében?](#disable-nagles-algorithm) |
| &nbsp; | Üzenet mérete | [Az üzenetek tömörítve vannak az üzenetsor teljesítményének növelése érdekében?](#message-size) |
| &nbsp; | Tömeges beolvasás | [Több üzenetet is beolvas egyetlen Get műveletben?](#batch-retrieval) |
| &nbsp; | Lekérdezés gyakorisága | [Elég gyakran lekérdezni az alkalmazást, hogy csökkentse az alkalmazás észlelt késését?](#queue-polling-interval) |
| &nbsp; | Frissítési üzenet | [A frissítési üzenet művelettel tárolja az üzenetek feldolgozásának folyamatát, így elkerülhető, hogy a teljes üzenetet újra fel lehessen dolgozni, ha hiba történik?](#perform-an-update-message-operation) |
| &nbsp; | Architektúra | [Olyan várólistákat használ, amelyekkel a teljes alkalmazás jobban méretezhető a hosszú távú munkaterhelések kiszámításával a kritikus elérési útról és a méretezéstől függetlenül?](#application-architecture) |

## <a name="scalability-targets"></a>Méretezhetőségi célok

Ha az alkalmazás megközelíti vagy túllépi a méretezhetőségi célokat, akkor a tranzakciós késések vagy a szabályozás nagyobb mértékben merülhet fel. Amikor az Azure Storage szabályozza az alkalmazást, a szolgáltatás megkezdi a 503 ( `Server Busy` ) vagy a 500 ( `Operation Timeout` ) hibakódok visszaadását. A méretezhetőségi célok korlátain belül maradó hibák elkerülésének fontos része az alkalmazás teljesítményének növelése.

A Queue Storage skálázhatósági céljaival kapcsolatos további információkért lásd: az [Azure Storage skálázhatósági és teljesítményi céljai](./scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Storage-fiókok maximális száma

Ha egy adott előfizetés/régió kombináció számára engedélyezett maximális számú Storage-fiókot használ, több Storage-fiókot is használhat a beléptetés, a kimenő forgalom, az I/O műveletek másodpercenkénti (IOPS) vagy kapacitásának növelésére? Ebben a forgatókönyvben a Microsoft azt javasolja, hogy a Storage-fiókok megnövekedett korlátainak kihasználásával csökkentse a munkaterhelés számára szükséges tárolási fiókok számát, ha lehetséges. Vegye fel a kapcsolatot az [Azure támogatási szolgálatával](https://azure.microsoft.com/support/options/) , és kérjen nagyobb korlátokat a Storage-fiókjához. További információ: [nagyobb méretű, magasabb szintű Storage-fiókok bejelentése](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapacitás-és tranzakciós célok

Ha az alkalmazása közeledik egyetlen Storage-fiók skálázhatósági céljaihoz, vegye figyelembe az alábbi módszerek egyikét:

- Ha a várólisták skálázhatósági céljai nem elegendőek az alkalmazáshoz, akkor használjon több várólistát, és ossza meg őket egymás között.
- Gondolja át a számítási feladatokat, amelyek hatására az alkalmazás megközelíti vagy túllépi a méretezhetőségi célt. Másképpen is megtervezhető, hogy kevesebb sávszélességet vagy kapacitást vagy kevesebb tranzakciót használjon?
- Ha az alkalmazásnak meg kell haladnia a méretezhetőségi célok valamelyikét, hozzon létre több Storage-fiókot, és particionálja az alkalmazásadatok körét a több Storage-fiók között. Ha ezt a mintát használja, ügyeljen arra, hogy tervezze meg az alkalmazását, hogy a jövőben további Storage-fiókokat lehessen hozzáadni a terheléselosztáshoz. A Storage-fiókok önmagukban a tárolt adatok, a tranzakciók vagy az átvitt adatok alapján nem számítanak bele a használati díjakba.
- Ha az alkalmazás közeledik a sávszélesség-célokhoz, érdemes lehet az ügyfél oldalán lévő adatok tömörítését az adatok Azure Storage-ba való küldéséhez szükséges sávszélesség csökkentése érdekében. Az adatok tömörítése csökkentheti a sávszélességet, és javíthatja a hálózati teljesítményt, továbbá negatív hatással lehet a teljesítményre. Értékelje ki a további feldolgozási követelmények teljesítményére gyakorolt hatást az adattömörítés és a kibontás az ügyféloldali oldalon. Ne feledje, hogy a tömörített adattárolók megnehezítik a hibaelhárítást, mivel a szabványos eszközök használatával nagyobb kihívást jelenthet az adatmegtekintés.
- Ha az alkalmazás közeledik a méretezhetőségi célokhoz, akkor győződjön meg arról, hogy exponenciális leállítási használ az újrapróbálkozásokhoz. A legjobb megoldás, ha a jelen cikkben ismertetett javaslatok végrehajtásával el szeretné kerülni a méretezhetőségi célok elérését. Az újrapróbálkozások exponenciális leállítási használata azonban megakadályozza, hogy az alkalmazás gyorsan újrapróbálkozjon, ami még rosszabb szabályozást eredményezhet. További információ: [időtúllépés és kiszolgáló foglalt hibák](#timeout-and-server-busy-errors) szakasz.

## <a name="networking"></a>Hálózatkezelés

Az alkalmazás fizikai hálózati korlátai jelentős hatással lehetnek a teljesítményre. A következő szakaszok ismertetik néhány korlátozást a felhasználók számára.

### <a name="client-network-capability"></a>Ügyfél hálózati funkciója

A sávszélesség és a hálózati kapcsolat minősége fontos szerepet játszik az alkalmazás teljesítményében, az alábbi szakaszokban leírtak szerint.

#### <a name="throughput"></a>Teljesítmény

A sávszélesség miatt a probléma gyakran az ügyfél képességei. A nagyobb méretű Azure-példányok nagyobb kapacitású hálózati adapterekkel rendelkeznek, ezért érdemes nagyobb méretű virtuális gépeket használni, ha egy gépről nagyobb hálózati korlátokra van szüksége. Ha egy helyszíni alkalmazásból fér hozzá az Azure Storage-hoz, ugyanez a szabály vonatkozik rá: Ismerje meg az ügyféleszközök hálózati képességeit és a hálózati kapcsolatot az Azure Storage-beli helyhez, vagy javítsa azokat igény szerint, vagy tervezze meg, hogy az alkalmazás működjön a képességein belül.

#### <a name="link-quality"></a>Kapcsolat minősége

Ahogy bármilyen hálózati használat esetében, ne feledje, hogy a hibákat eredményező hálózati feltételek és a csomagok elvesztése lassú működést eredményez. A Wireshark vagy a Hálózatfigyelő használata segíthet a probléma diagnosztizálásában.

### <a name="location"></a>Hely

Minden elosztott környezetben, a-ügyfél közel a kiszolgálóhoz, a legjobb teljesítményt nyújtja. Ha az Azure Storage-t a legalacsonyabb késéssel szeretné elérni, az ügyfél számára a legjobb hely ugyanazon az Azure-régión belül van. Ha például van egy Azure-webalkalmazása, amely az Azure Storage szolgáltatást használja, akkor mindkettőt egyetlen régióban, például az USA nyugati régiójában vagy Délkelet-Ázsiában is megkeresheti. Az erőforrások közös elhelyezése csökkenti a késést és a költségeket, mivel az egyetlen régión belüli sávszélesség-használat ingyenes.

Ha az ügyfélalkalmazások hozzáférnek az Azure Storage-hoz, de nem az Azure-ban futnak, például a mobileszköz-alkalmazásokban vagy a helyszíni nagyvállalati szolgáltatásokban, akkor a Storage-fiók az ügyfelek közelében található régióban is csökkentheti a késést. Ha az ügyfeleket széles körben terjesztik (például néhány Észak-Amerika és néhány európai), akkor érdemes lehet régiónként egy Storage-fiókot használni. Ez a megközelítés könnyebben megvalósítható, ha az alkalmazás által tárolt adattárolók egyediek az egyes felhasználók számára, és nem igénylik az adatreplikálást a Storage-fiókok között.

## <a name="sas-and-cors"></a>SAS és CORS

Tegyük fel, hogy engedélyezni kell a kódot, például a JavaScriptet, amely egy felhasználó webböngészőjében vagy egy mobiltelefon-alkalmazásban fut az Azure Storage-ban tárolt adateléréshez. Az egyik módszer egy olyan szolgáltatásalkalmazás létrehozása, amely proxyként működik. A felhasználó eszköze hitelesíti magát a szolgáltatással, amely viszont engedélyezi az Azure Storage-erőforrásokhoz való hozzáférést. Így elkerülhető, hogy a Storage-fiók kulcsa nem biztonságos eszközökön legyen kitéve. Ez a megközelítés azonban jelentős terhelést jelent a szolgáltatásalkalmazás számára, mivel a felhasználó eszközén és az Azure Storage-ban továbbított összes adatmennyiségnek továbbítania kell a szolgáltatásalkalmazás alkalmazásán.

A megosztott hozzáférési aláírások (SAS) használatával elkerülhető, hogy a szolgáltatásalkalmazás proxyként legyen használatban az Azure Storage-ban. A SAS használatával engedélyezheti a felhasználó eszközének, hogy közvetlenül az Azure Storage-ba irányuló kéréseket hozzon igénybe korlátozott hozzáférési jogkivonat használatával. Ha például egy felhasználó egy fényképet szeretne feltölteni az alkalmazásba, a szolgáltatásalkalmazás létrehozhat egy SAS-t, és elküldheti azt a felhasználó eszközére. Az SAS-jogkivonat engedélyezheti az Azure Storage-erőforrásoknak adott időtartamra való írást, amely után az SAS-jogkivonat lejár. Az SAS-vel kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférési aláírások (SAS) használatával](../common/storage-sas-overview.md).

A webböngésző általában nem engedélyezi a JavaScript használatát egy olyan oldalon, amelyet egy adott tartomány webhelye futtat bizonyos műveletek, például írási műveletek végrehajtásához egy másik tartományba. Ez a házirend megakadályozza, hogy az egyik lapon lévő rosszindulatú parancsfájlok hozzáférjenek egy másik weblapon lévő adatokhoz. Ugyanakkor a felhőben a megoldás kiépítésekor is megadható korlátozás. A több eredetű erőforrás-megosztás (CORS) egy böngésző-szolgáltatás, amely lehetővé teszi a célként megadott tartomány számára, hogy kommunikáljon a forrás tartományból származó kérelmeket megbízható böngészővel.

Tegyük fel például, hogy egy Azure-ban futó webalkalmazás egy Azure Storage-fiókhoz kér egy erőforrást. A webalkalmazás a forrástartomány, a Storage-fiók pedig a célként megadott tartomány. A CORS bármely Azure Storage-szolgáltatáshoz beállíthatja úgy, hogy az Azure Storage által megbízhatónak tekintse azt a böngészőt, amelyet a forrástartománytól érkező kérések biztosítanak. A CORS kapcsolatos további információkért lásd: [Az Azure Storage-hoz készült több eredetű erőforrás-megosztás (CORS) támogatása](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Az SAS és a CORS is segíthet elkerülni a webalkalmazás szükségtelen terhelését.

## <a name="net-configuration"></a>.NET-konfiguráció

A .NET-keretrendszer használata esetén ez a szakasz több gyors konfigurációs beállítást tartalmaz, amelyek segítségével jelentős teljesítmény-növelést végezhet. Ha más nyelveket használ, ellenőrizze, hogy a hasonló fogalmak érvényesek-e a választott nyelven.

### <a name="use-net-core"></a>A .NET Core használata

Fejlessze Azure Storage-alkalmazásait a .NET Core 2,1-es vagy újabb verziójával, hogy kihasználhassa a teljesítménnyel kapcsolatos fejlesztéseket. Ha lehetséges, a .NET Core 3. x használata javasolt.

A .NET Core teljesítményével kapcsolatos újdonságokról a következő blogbejegyzésekben olvashat bővebben:

- [Teljesítménnyel kapcsolatos újdonságok a .NET Core 3,0-ben](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Teljesítménnyel kapcsolatos újdonságok a .NET Core 2,1-ben](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Az alapértelmezett kapcsolatok korlátjának megemelése

A .NET-ben a következő kód növeli az alapértelmezett kapcsolódási korlátot (amely általában 2 egy ügyfél-környezetben, vagy 10 a kiszolgálói környezetben) 100-re. Az értéket általában az alkalmazás által használt szálak számának körülbelül értékre kell állítani.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

A kapcsolatok megnyitása előtt állítsa be a kapcsolati korlátot.

Más programozási nyelvek esetében az adott nyelv dokumentációjában megtudhatja, hogyan állíthatja be a kapcsolódási korlátot.

További információ [: a webszolgáltatások közzétételét ismertető webszolgáltatások: egyidejű kapcsolatok](/archive/blogs/darrenj/web-services-concurrent-connections).

### <a name="increase-the-minimum-number-of-threads"></a>A szálak minimális számának megemelése

Ha a szinkron hívásokat aszinkron feladatokkal együtt használja, érdemes megnövelni a szálak számát a szál készletében:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

További információ: [`ThreadPool.SetMinThreads`](/dotnet/api/system.threading.threadpool.setminthreads) metódus.

## <a name="unbounded-parallelism"></a>Nem kötött párhuzamosság

Míg a párhuzamosság kiválóan használható a teljesítmény szempontjából, ügyeljen a nem korlátozott párhuzamosságok használatára, ami azt jelenti, hogy a szálak vagy a párhuzamos kérelmek száma nem kényszerített. Ügyeljen arra, hogy korlátozza a párhuzamos kérelmek feltöltését vagy letöltését, hogy ugyanazon a Storage-fiókban több partíciót lehessen elérni, vagy ugyanabban a partícióban több elemhez is hozzáférhessen. Ha a párhuzamosság feloldva, az alkalmazás túllépheti az ügyféleszközök képességeit vagy a tárolási fiók skálázhatósági céljait, ami hosszabb késést és szabályozást eredményez.

## <a name="client-libraries-and-tools"></a>Ügyféloldali kódtárak és eszközök

A legjobb teljesítmény érdekében mindig használja a Microsoft által biztosított legújabb ügyféloldali kódtárakat és eszközöket. Az Azure Storage ügyféloldali kódtárai különböző nyelveken érhetők el. Az Azure Storage a PowerShellt és az Azure CLI-t is támogatja. A Microsoft aktívan fejleszti ezeket az ügyfélszoftvereket és eszközöket a teljesítmény szem előtt tartásával, naprakészen tartja a legújabb verziókat, és gondoskodik arról, hogy a bevált teljesítményekkel kapcsolatos gyakorlatokat belsőleg kezeljék. További információt az [Azure Storage dokumentációjában](./reference.md)talál.

## <a name="handle-service-errors"></a>Szolgáltatás hibáinak kezelése

Az Azure Storage hibát jelez, ha a szolgáltatás nem tud feldolgozni egy kérést. Az Azure Storage által az adott forgatókönyvben visszaadott hibák megismerése hasznos lehet a teljesítmény optimalizálása érdekében.

### <a name="timeout-and-server-busy-errors"></a>Időtúllépés és a kiszolgáló foglalt hibái

Az Azure Storage szabályozhatja az alkalmazást, ha az megközelíti a méretezhetőségi korlátokat. Bizonyos esetekben előfordulhat, hogy az Azure Storage bizonyos átmeneti feltételek miatt nem tudja kezelni a kérést. A szolgáltatás mindkét esetben 503 ( `Server Busy` ) vagy 500 ( `Timeout` ) hibát adhat vissza. Ezek a hibák akkor is előfordulhatnak, ha a szolgáltatás az adatpartíciók újrakiegyensúlyozását teszi lehetővé a magasabb átviteli sebesség érdekében. Az ügyfélalkalmazás általában próbálja megismételni a műveletet, amely a hibák egyikét okozza. Ha azonban az Azure Storage szabályozza az alkalmazást, mert az meghaladja a skálázhatósági célokat, vagy ha a szolgáltatás nem tudta kiszolgálni a kérelmet valamilyen más okból, az agresszív újrapróbálkozások rosszabbul tehetik a problémát. Az exponenciális visszahívási újrapróbálkozási házirend használata ajánlott, és az ügyféloldali kódtárak ezt a viselkedést használják. Előfordulhat például, hogy az alkalmazás 2 másodperc, 4 másodperc, 10 másodperc, majd 30 másodperc múlva újra próbálkozik. Így az alkalmazása jelentősen csökkenti a szolgáltatás terhelését, és nem súlyosbítja a szabályozáshoz vezethető viselkedést.

A kapcsolódási hibákat azonnal újra lehet próbálni, mert nem a szabályozás eredménye, és a várt átmeneti állapotú.

### <a name="non-retryable-errors"></a>Nem újrapróbálkozást lehetővé tevő hibák

Az ügyféloldali kódtárak kezelik az újrapróbálkozásokat, és megismerik, hogy mely hibákat lehet újrapróbálni, és melyek nem. Ha azonban közvetlenül hívja meg az Azure Storage-REST API, néhány hiba miatt nem kell újrapróbálkoznia. Például egy 400 ( `Bad Request` ) hiba azt jelzi, hogy az ügyfélalkalmazás olyan kérelmet küld, amely nem dolgozható fel, mert nem volt a várt formában. A kérelem újraküldése minden alkalommal ugyanazt a választ eredményezi, így a rendszer nem próbálkozik újra. Ha közvetlenül hívja meg az Azure Storage-REST API, vegye figyelembe a lehetséges hibákat, valamint azt, hogy újra kell-e próbálni.

Az Azure Storage-hibakódokkal kapcsolatos további információkért lásd: [állapot-és hibakódok](/rest/api/storageservices/status-and-error-codes2).

## <a name="disable-nagles-algorithm"></a>A Nyéki algoritmus letiltása

A Nyéki algoritmusa széles körben implementálva van a TCP/IP-hálózatokon a hálózati teljesítmény javítása érdekében. Ez azonban nem minden esetben optimális (például a nagyon interaktív környezetekben). A Nyéki algoritmusa negatív hatással van az Azure Table Storage érkező kérések teljesítményére, és ha lehetséges, le kell tiltania.

## <a name="message-size"></a>Üzenet mérete

A várólista teljesítményének és méretezhetőségének csökkentése az üzenet méretének növekedésével. Csak azokat az adatokat helyezze el, amelyekre a fogadónak szüksége van egy üzenetben.

## <a name="batch-retrieval"></a>Kötegelt lekérés

Egy várólistából legfeljebb 32 üzenetet kérhet le egyetlen művelettel. A kötegelt lekérések csökkenthetik az ügyfélalkalmazás által beérkező utak számát, ami különösen hasznos olyan környezetekben, mint például a mobileszközök, nagy késéssel.

## <a name="queue-polling-interval"></a>Várólista lekérdezési időköze

A legtöbb alkalmazás egy üzenetsor által küldött üzenetek lekérdezése, amely az adott alkalmazáshoz tartozó tranzakciók egyik legnagyobb forrása lehet. Válassza ki a lekérdezési időközt bölcsen: a lekérdezés túl gyakran okozhatja az alkalmazásnak, hogy megközelítse a várólista skálázhatósági céljait. Azonban a $0,01-es (az írás időpontjában) 200 000-tranzakciókban a processzorok másodpercenkénti lekérdezése kevesebb, mint 15 cent, így a költségek nem jellemzően a lekérdezési időközt befolyásoló tényező.

A naprakész költséggel kapcsolatos információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

## <a name="perform-an-update-message-operation"></a>Frissítési üzenet műveletének végrehajtása

A frissítési üzenet művelettel növelheti a nem láthatósági időtúllépést, vagy frissítheti egy üzenet állapotadatok állapotát. Ez a megközelítés hatékonyabb lehet, mint egy olyan munkafolyamatnál, amely egy adott várólistából a következőre továbbítja a feladatokat, mivel a feladatok mindegyik lépése befejeződött. Az alkalmazás elmentheti a feladatot az üzenetbe, majd folytathatja a munkát, és nem kell az üzenetet a művelet minden egyes lépésekor a következő lépéshez átütemezni. Ne feledje, hogy az egyes frissítési üzenetek minden művelete beleszámít a méretezhetőségi célra.

## <a name="application-architecture"></a>Alkalmazásarchitektúra

A várólisták segítségével méretezhetővé teheti az alkalmazás architektúráját. Az alábbi lista néhány módszert biztosít a várólisták használatára az alkalmazás méretezhetőségének megtételéhez:

- A várólisták használatával várakozó munkaterhelések hozhatók létre az alkalmazásban felhasználható számítási és kiegyenlítő feladatok elvégzéséhez. Előfordulhat például, hogy a felhasználóktól érkező kéréseket a processzorok intenzív működéséhez, például a feltöltött képek átméretezéséhez szeretné elvégezni.
- A várólisták segítségével elválaszthatja az alkalmazás egyes részeit, így egymástól függetlenül méretezheti azokat. A webes előtér például a felhasználók által egy várólistába helyezheti a későbbi elemzést és tárolást. További feldolgozói szerepkör-példányok hozzáadásával feldolgozhatja a várólista-adatkészletek igény szerinti feldolgozását.

## <a name="next-steps"></a>Következő lépések

- [A Queue Storage méretezhetősége és teljesítménybeli céljai](scalability-targets.md)
- [A standard szintű Storage-fiókok méretezhetősége és teljesítménybeli céljai](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Állapot-és hibakódok](/rest/api/storageservices/Status-and-Error-Codes2)
