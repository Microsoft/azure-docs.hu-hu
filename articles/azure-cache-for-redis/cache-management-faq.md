---
title: Azure cache a Redis-kezeléshez – gyakori kérdések
description: Az Azure cache Redis való kezelésével kapcsolatos gyakori kérdésekre adott válaszok
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537660"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Azure cache a Redis-kezeléshez – gyakori kérdések
Ez a cikk az Azure cache Redis való felügyeletével kapcsolatos gyakori kérdésekre ad választ.

## <a name="common-questions-and-answers"></a>Gyakori kérdések és válaszok
Ez a szakasz a következő gyakori kérdéseket ismerteti:

* [Mikor engedélyezzem a nem TLS/SSL portot a Redis-hez való csatlakozáshoz?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [Mik a bevált eljárások az éles környezetben?](#what-are-some-production-best-practices)
* [Milyen szempontokat kell figyelembe venni a gyakori Redis parancsok használatakor?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Fontos információk a szálkészlet munkaszála belépett növekedéséről](#important-details-about-threadpool-growth)
* [A StackExchange. Redis használata esetén engedélyezze a kiszolgáló GC-t, hogy nagyobb átviteli sebességet kapjon az ügyfélen.](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Teljesítménnyel kapcsolatos szempontok a kapcsolatokhoz](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Mikor engedélyezzem a nem TLS/SSL portot a Redis-hez való csatlakozáshoz?
A Redis-kiszolgáló nem támogatja natív módon a TLS-t, de az Azure cache for Redis nem. Ha az Azure cache-hez csatlakozik a Redis-hez, és az ügyfél támogatja a TLS-t, például a StackExchange. Redis, akkor a TLS protokollt kell használnia.

>[!NOTE]
>A nem TLS port alapértelmezés szerint le van tiltva az új Azure cache Redis-példányokhoz. Ha az ügyfél nem támogatja a TLS-t, akkor a nem TLS portot a [gyorsítótár konfigurálása az Azure cache-ben Redis](cache-configure.md) [című cikkben](cache-configure.md#access-ports) található utasításokat követve engedélyeznie kell a nem TLS-port használatát.
>
>

A Redis-eszközök `redis-cli` , például a TLS-port használata nem működnek, de használhatnak olyan segédprogramot is, `stunnel` amely az eszközök TLS-porthoz való biztonságos csatlakoztatását a [bejelentési ASP.NET munkamenet-szolgáltatója a Redis előzetes kiadásához](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/) című blogbejegyzésben.

A Redis-eszközök letöltésével kapcsolatos utasításokért tekintse meg a [Hogyan futtathatom a Redis parancsokat?](cache-development-faq.md#how-can-i-run-redis-commands) című szakaszt.

### <a name="what-are-some-production-best-practices"></a>Mik a bevált eljárások az éles környezetben?
* [StackExchange. Redis – ajánlott eljárások](#stackexchangeredis-best-practices)
* [Konfigurálás és fogalmak](#configuration-and-concepts)
* [Teljesítménytesztelés](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange. Redis – ajánlott eljárások
* Állítsa `AbortConnect` false (hamis) értékre, majd engedélyezze a ConnectionMultiplexer automatikus újrakapcsolódását. [Részletekért lásd itt](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* A ConnectionMultiplexer újrafelhasználása – ne hozzon létre újat minden kérelemhez. Az `Lazy<ConnectionMultiplexer>` [itt látható](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) minta használata javasolt.
* A Redis kisebb értékekkel működik a legjobban, ezért érdemes lehet nagyobb mennyiségű kulcsot feldarabolni több kulcsra. Ebben a [Redis-vitában](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)az 100 kb nagy méretű. Olvassa el [ezt a cikket](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) egy olyan problémával kapcsolatban, amelyet nagy értékek okozhatnak.
* Az időtúllépések elkerüléséhez konfigurálja a [szálkészlet munkaszála belépett beállításait](#important-details-about-threadpool-growth) .
* Használjon legalább 5 másodperces alapértelmezett connectTimeout. Ez az intervallum elegendő időt biztosít a StackExchange. Redis számára a kapcsolat újbóli létrehozásához a hálózati rendszerindítási hiba esetén.
* Vegye figyelembe a futtatott különböző műveletekhez kapcsolódó teljesítménnyel kapcsolatos költségeket. Például a `KEYS` parancs egy O (n) művelet, ezért el kell kerülni. A [Redis.IO hely](https://redis.io/commands/) részletesen ismerteti az általa támogatott műveletek időbeli összetettségét. Az egyes parancsokra kattintva megtekintheti az egyes műveletek bonyolultságát.

#### <a name="configuration-and-concepts"></a>Konfigurálás és fogalmak
* Használjon standard vagy prémium szintű üzemi rendszereket. Az Alapszint egyetlen csomópontot tartalmaz adatreplikáció nélkül, és SLA sem tartozik hozzá. Ezen kívül használjon legalább C1 szintű gyorsítótárat. A C0 cache-gyorsítótárak jellemzően egyszerű fejlesztési/tesztelési helyzetekben használatosak.
* Ne feledje, hogy a Redis egy **memóriában** tárolt adattároló. Olvassa el [ezt a cikket](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , hogy tisztában legyenek az adatvesztést okozó forgatókönyvekkel.
* Fejlessze a rendszerét úgy, hogy a [javítás és a feladatátvétel miatt](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)képes legyen kezelni a kapcsolódási naplókat.

#### <a name="performance-testing"></a>Teljesítménytesztelés
* Kezdje a használatával `redis-benchmark.exe` , hogy a saját Perf-tesztek megírása előtt a lehető legnagyobb átviteli sebességre legyen szükség. Mivel a nem `redis-benchmark` támogatja a TLS-t, a teszt futtatása előtt [engedélyeznie kell a nem TLS portot a Azure Portalon](cache-configure.md#access-ports) . Példák: [Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* A teszteléshez használt ügyfél virtuális gépnek ugyanabban a régióban kell lennie, mint a Redis-példányhoz tartozó Azure cache-nek.
* Azt javasoljuk, hogy a Dv2 virtuálisgép-sorozatokat az ügyfélhez használja, mivel azok jobb hardvert használnak, és a legjobb eredményt kell biztosítani.
* Győződjön meg arról, hogy a kiválasztott ügyfél virtuális gépe legalább annyi számítási és sávszélességi képességgel rendelkezik, mint a tesztelt gyorsítótár.
* Engedélyezze a VRSS az ügyfélszámítógépen, ha Windows rendszeren van. [Részletekért lásd itt](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).
* A prémium szintű Redis-példányok jobb hálózati késést és átviteli sebességet biztosítanak, mivel a processzor és a hálózat esetében is jobb hardveren futnak.

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Milyen szempontokat kell figyelembe venni a gyakori Redis parancsok használatakor?

* Ne használjon olyan Redis-parancsokat, amelyek hosszú ideig tartanak a végrehajtás során, kivéve, ha teljes mértékben tisztában van a parancsok hatásával. Ne futtassa például a [Keys](https://redis.io/commands/keys) parancsot éles környezetben. A kulcsok számától függően hosszú időt is igénybe vehet. A Redis egy egyszálas kiszolgáló, és egyszerre dolgozza fel a parancsokat. Ha a kulcsok után más parancsokat is kiállítottak, a rendszer addig nem dolgozza fel őket, amíg a Redis feldolgozza a KEYS parancsot. A [Redis.IO hely](https://redis.io/commands/) részletesen ismerteti az általa támogatott műveletek időbeli összetettségét. Az egyes parancsokra kattintva megtekintheti az egyes műveletek bonyolultságát.
* Kulcsok mérete – érdemes kis kulcs/értékek vagy nagy kulcs/értékek használata? Ez a forgatókönyvtől függ. Ha a forgatókönyv nagyobb kulcsokat igényel, módosíthatja a ConnectionTimeout, majd újrapróbálkozhat az értékekkel, és módosíthatja az újrapróbálkozási logikát. A Redis-kiszolgáló szemszögéből a kisebb értékek jobb teljesítményt nyújtanak.
* Ezek a megfontolások nem azt jelentik, hogy nem tárolhat nagyobb értékeket a Redis; a következő szempontokat kell figyelembe vennie: A késések magasabbak lesznek. Ha olyan adatkészlettel rendelkezik, amely nagyobb és kisebb, akkor több ConnectionMultiplexer-példányt is használhat, amelyek mindegyike eltérő időkorláttal és újrapróbálkozási értékekkel van konfigurálva, ahogy az előző, a [StackExchange. Redis konfigurációs beállítások do](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) szakaszban leírtak szerint.

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?
* [Engedélyezze a gyorsítótár-diagnosztikát,](cache-how-to-monitor.md#enable-cache-diagnostics) hogy [megfigyelhesse](cache-how-to-monitor.md) a gyorsítótár állapotát. Megtekintheti a metrikákat a Azure Portalban, és az Ön által választott eszközökkel [letöltheti és áttekintheti](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) őket.
* redis-benchmark.exe használatával betöltheti a Redis-kiszolgáló tesztelését.
* Győződjön meg arról, hogy a terhelés-tesztelési ügyfél és a Redis Azure-gyorsítótára ugyanabban a régióban található.
* Használja redis-cli.exe és figyelje a gyorsítótárat az INFO parancs használatával.
* Ha a terhelés nagy mennyiségű memóriát okoz, nagyobb gyorsítótár-méretre kell méreteznie.
* A Redis-eszközök letöltésével kapcsolatos utasításokért tekintse meg a [Hogyan futtathatom a Redis parancsokat?](cache-development-faq.md#how-can-i-run-redis-commands) című szakaszt.

Az alábbi parancsok példaként szolgálnak redis-benchmark.exe használatára. A pontos eredmények érdekében futtassa ezeket a parancsokat egy olyan virtuális gépről, amely ugyanabban a régióban található, mint a gyorsítótár.

* Folyamaton keresztüli beállított kérelmek tesztelése 1k hasznos adattartalommal

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* A folyamatok tesztelése egy 1k hasznos adattartalommal.

>[!NOTE]
> Futtassa először a fent látható SET tesztet a gyorsítótár feltöltéséhez.
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>Fontos információk a szálkészlet munkaszála belépett növekedéséről
A CLR-beli szálkészlet munkaszála belépett két típusú szálat tartalmaz: "Worker" és "I/O-befejezési port" (olvasóhoz) szálak.

* A munkaszálakat olyan dolgokhoz használják, mint például a `Task.Run(…)` , vagy a `ThreadPool.QueueUserWorkItem(…)` metódusok feldolgozása. Ezeket a szálakat a CLR különböző összetevői is használják, ha a munkafolyamatnak egy háttérben futó szálon kell történnie.
* A olvasóhoz szálakat akkor használja a rendszer, amikor aszinkron IO történik, például a hálózatról való olvasáskor.

A szál készlete új munkavégző szálakat vagy I/O-befejezési szálakat biztosít igény szerint (szabályozás nélkül), amíg el nem éri a "minimális" beállítást az egyes típusú szálak esetében. Alapértelmezés szerint a szálak minimális száma a rendszeren lévő processzorok számára van beállítva.

Ha a meglévő (foglalt) szálak száma eléri a "minimális" számú szálat, a szálkészlet munkaszála belépett fogja szabályozni, hogy az új szálakat 500 ezredmásodpercen belül egy szálhoz adja. Ha a rendszer általában egy olvasóhoz-szálat igénylő munkafolyamatot kap, akkor az gyorsan feldolgozza a munkát. Ha azonban a munka felszakadása nagyobb, mint a beállított "minimális" beállítás, akkor némi késés fog történni a munka bizonyos részének feldolgozásakor, mivel a szálkészlet munkaszála belépett megvárja a két dolog egyikét.

* Egy meglévő szál szabadon feldolgozhatja a munkát.
* Egy meglévő szál 500 MS-ra nem lesz szabad, ezért létrejön egy új szál.

Alapvetően azt jelenti, hogy ha a foglalt szálak száma nagyobb, mint a minimális szál, valószínűleg 500 – MS késleltetést fizet, mielőtt az alkalmazás feldolgozza a hálózati forgalmat. Azt is fontos megjegyezni, hogy ha egy meglévő szál 15 másodpercnél hosszabb ideig marad üresjáratban (attól függően, hogy mit emlékszik), a rendszer törli, és a növekedés és a zsugorodás ezen ciklusa is megismételhető.

Ha egy példa a StackExchange. Redis (build 1.0.450 vagy újabb) hibaüzenetet jelenít meg, látni fogja, hogy most kinyomtatja a szálkészlet munkaszála belépett statisztikáit (lásd a olvasóhoz és a feldolgozó adatait alább).

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

Az előző példában látható, hogy a olvasóhoz szál esetében hat foglalt szál van, és a rendszer úgy van konfigurálva, hogy négy minimális szálat engedélyezzen. Ebben az esetben az ügyfél valószínűleg 2 500 – MS késéssel fog rendelkezni, mivel 6 > 4.

Vegye figyelembe, hogy a StackExchange. Redis az időtúllépéseket is elérheti, ha a olvasóhoz vagy a munkavégző szálak növekedését szabályozzák.

#### <a name="recommendation"></a>Ajánlás

Ezeknek az információknak az alapján határozottan azt javasoljuk, hogy az ügyfelek az alapértelmezett értéknél nagyobb értékre állítsa be a olvasóhoz és a feldolgozói szálak minimális konfigurációs értékét. Az értékhez nem biztosítunk egy-egy méretre kiterjedő útmutatást, mivel az egyik alkalmazás megfelelő értéke valószínűleg túl magas vagy alacsony lesz egy másik alkalmazás esetében. Ez a beállítás hatással lehet a bonyolult alkalmazások más részeinek teljesítményére is, így minden ügyfélnek az adott igényeknek megfelelően kell finomítani ezt a beállítást. A megfelelő kiindulási hely 200 vagy 300, majd szükség szerint tesztelhető és csípés.

A beállítás konfigurálása:

* Azt javasoljuk, hogy programozottan módosítsa ezt a beállítást a [szálkészlet munkaszála belépett. SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) metódussal a alkalmazásban `global.asax.cs` . Például:

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > A metódus által megadott érték globális beállítás, amely hatással van a teljes alkalmazástartomány. Ha például egy 4 magos géppel rendelkezik, és a *minWorkerThreads* és a *minIoThreads* processzort 50-ra szeretné beállítani, akkor a **szálkészlet munkaszála belépett. SetMinThreads (200, 200)** értéket kell használnia.

* A szálak minimális beállítása a [ *minIoThreads* vagy a *minWorkerThreads* konfigurációs beállítással](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100)) is megadható a alkalmazásban `<processModel>` `Machine.config` , általában a következő helyen: `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **A szálak minimális számának beállítása így általában nem ajánlott, mert ez egy rendszerszintű beállítás.**

  > [!NOTE]
  > A konfigurációs elemben megadott érték egy *alapszintű* beállítás. Ha például egy 4 magos géppel rendelkezik, és szeretné, hogy a *minIoThreads* -beállítás értéke 200, akkor a következőt fogja használni: `<processModel minIoThreads="50"/>` .
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>A StackExchange. Redis használata esetén engedélyezze a kiszolgáló GC-t, hogy nagyobb átviteli sebességet kapjon az ügyfélen.
A kiszolgáló GC szolgáltatásának engedélyezése optimalizálja az ügyfelet, és jobb teljesítményt és átviteli sebességet biztosít a StackExchange. Redis használatakor. A kiszolgáló GC-vel és annak engedélyezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

* [A kiszolgáló GC engedélyezése](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [A Garbage Collection alapjai](/dotnet/standard/garbage-collection/fundamentals)
* [A szemetet gyűjtése és teljesítménye](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>Teljesítménnyel kapcsolatos szempontok a kapcsolatokhoz

Az egyes díjszabási szintek eltérő korlátokkal rendelkeznek az ügyfélkapcsolatok, a memória és a sávszélesség tekintetében. Míg a gyorsítótárak minden mérete *egy adott* számú kapcsolaton keresztül engedélyezett, a Redis-kapcsolatokhoz kapcsolódó minden egyes kapcsolathoz hozzá kell rendelni a terhelést. Ilyen például a CPU és a memóriahasználat a TLS/SSL titkosítás miatt. Egy adott gyorsítótár méretének maximális korlátja egy enyhén betöltött gyorsítótár. Ha a terhelés a kapcsolatok terhelése *és* az ügyfél műveleteinek terhelése meghaladja a rendszer kapacitását, akkor a gyorsítótár a kapacitással kapcsolatos problémákat is tapasztalhatja, még akkor is, ha nem lépte túl a gyorsítótár jelenlegi méretére vonatkozó korlátot.

Az egyes rétegek különböző kapcsolatainak korlátaival kapcsolatos további információkért lásd az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/). További információ a kapcsolatokról és az egyéb alapértelmezett konfigurációkról: az [alapértelmezett Redis-kiszolgáló konfigurációja](cache-configure.md#default-redis-server-configuration).

## <a name="next-steps"></a>Következő lépések

További információ [Az Azure cache Redis-ről – gyakori kérdések](cache-faq.md).