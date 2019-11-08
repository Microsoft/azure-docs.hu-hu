---
title: Az Azure cache Redis-időtúllépésekkel kapcsolatos hibáinak megoldása | Microsoft Docs
description: Ismerje meg, Hogyan oldhatók fel az Azure cache szolgáltatással kapcsolatos gyakori időtúllépési problémák az Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/18/2019
ms.author: yegu
ms.openlocfilehash: 4f577e6497e853d9b75f81b5da4f7121064a9d07
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826341"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Az Azure cache Redis-időtúllépésekkel kapcsolatos hibáinak megoldása

Ez a szakasz azokat a hibaelhárítási időtúllépési problémákat ismerteti, amelyek az Azure cache-hez való csatlakozáskor történnek a Redis.

- [Redis-kiszolgáló javítása](#redis-server-patching)
- [StackExchange. Redis időtúllépési kivételek](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Az útmutatóban ismertetett hibaelhárítási lépések többek között a Redis-parancsok futtatására és a különböző teljesítmény-mérőszámok figyelésére vonatkozó utasításokat tartalmaznak. További információkért és útmutatásért tekintse meg a [További információ](#additional-information) szakasz cikkeit.
>

## <a name="redis-server-patching"></a>Redis-kiszolgáló javítása

A Redis készült Azure cache rendszeresen frissíti a kiszolgáló szoftverét az általa felügyelt szolgáltatás funkciójának részeként. Ez a [javítási](cache-failover.md) tevékenység nagyrészt a jelenet mögött zajlik. A feladatátvétel során a Redis-kiszolgáló csomópontjainak javításakor előfordulhat, hogy az ezekhez a csomópontokhoz csatlakozó Redis-ügyfelek átmeneti időtúllépéseket tapasztalnak, mivel ezek a csomópontok közötti kapcsolatok váltanak át. További információ arról, [hogy a feladatátvétel milyen hatással van az ügyfélalkalmazás alkalmazására](cache-failover.md#how-does-a-failover-affect-my-client-application) , és hogy miként javítható a javítási események kezelésének lehetősége.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange. Redis időtúllépési kivételek

A StackExchange. Redis egy `synctimeout` nevű konfigurációs beállítást használ a 1000 MS alapértelmezett értékkel rendelkező szinkron műveletekhez. Ha egy szinkron hívás nem fejeződött be ebben az időszakban, a StackExchange. Redis ügyfél időtúllépési hibát jelez az alábbi példához hasonló módon:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Ez a hibaüzenet olyan metrikákat tartalmaz, amelyek segíthetnek a probléma okának és lehetséges megoldásának kimutatása érdekében. A következő táblázat a hibaüzenetek metrikáinak részleteit tartalmazza.

| Hibaüzenet metrikája | Részletek |
| --- | --- |
| ítése |Az utolsó alkalom: 0 parancs lett kiadva |
| Mgr |A szoftvercsatorna-kezelő `socket.select`, ami azt jelenti, hogy az operációs rendszer azt kéri, hogy egy olyan szoftvercsatorna jelenjen meg, amelynek van valami teendője. Az olvasó nem olvas aktívan a hálózatról, mert nem hiszem, hogy bármi van |
| üzenetsor |Összesen 73 folyamatban lévő művelet |
| l |a folyamatban lévő műveletek közül 6 a nem küldött várólistában van, és még nem lett beírva a kimenő hálózatra. |
| QS |67 a folyamatban lévő műveletek elküldése a kiszolgálónak, de a válasz még nem érhető el. A válasz lehet `Not yet sent by the server` vagy `sent by the server but not yet processed by the client.` |
| QC |a folyamatban lévő műveletek közül 0 a válaszokat észlelte, de még nem jelölték meg befejezettként, mert a befejezési hurokra várnak. |
| WR |Aktív író van (vagyis a 6 el nem küldött kérések nincsenek figyelmen kívül hagyva) bájt/activewriters |
| A |Nincs aktív olvasó, és a rendszer nulla bájtot olvas be a hálózati adapter bájtjainak/activereaders |

A lehetséges kiváltó okok kivizsgálásához a következő lépéseket használhatja.

1. Ajánlott eljárásként ellenőrizze, hogy a következő mintát használja-e a StackExchange. Redis-ügyfél használatakor való csatlakozáshoz.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    További információ: [Kapcsolódás a gyorsítótárhoz a StackExchange. Redis használatával](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Győződjön meg arról, hogy a kiszolgáló és az ügyfélalkalmazás ugyanabban a régióban van az Azure-ban. Előfordulhat például, hogy időtúllépéseket kap, amikor a gyorsítótár az USA keleti régiójában található, de az ügyfél az USA nyugati régiójában található, és a kérés nem fejeződött be a `synctimeout` intervallumon belül, vagy ha a helyi fejlesztői gépről végez hibakeresést. 

    Erősen ajánlott, hogy a gyorsítótár és az ügyfél ugyanabban az Azure-régióban legyen. Ha olyan forgatókönyvvel rendelkezik, amely több régióra kiterjedő hívásokat is tartalmaz, akkor a `synctimeout` intervallumot az alapértelmezett 1000-MS intervallumnál nagyobb értékre kell állítania, ha a kapcsolódási karakterláncban a `synctimeout` tulajdonságot is megadja. Az alábbi példa egy, a StackExchange. Redis Azure cache által biztosított, a Redis-hez `synctimeout` készült, 2000-es ms-os adatrészletet ábrázoló karakterláncot mutat be.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Győződjön meg arról, hogy a [StackExchange. Redis NuGet-csomag](https://www.nuget.org/packages/StackExchange.Redis/)legújabb verzióját használja. A kódban folyamatosan rögzített hibák teszik hatékonyabbá az időtúllépéseket, így a legújabb verzió fontos.
1. Ha a kérések sávszélesség-korlátozásokkal vannak elfoglalva a kiszolgálón vagy az ügyfélen, a végrehajtásuk tovább tart, és időtúllépéseket okozhat. Ha szeretné megtudni, hogy az időtúllépés a kiszolgáló hálózati sávszélessége miatt van-e, tekintse meg a [kiszolgálóoldali sávszélesség korlátozását](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Ha szeretné megtudni, hogy az időtúllépés az ügyfél hálózati sávszélessége miatt van-e, tekintse meg az [ügyféloldali sávszélesség korlátozását](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Lekérdezi a PROCESSZORt a kiszolgálón vagy az ügyfélen?

   - Ellenőrizze, hogy az ügyfélen van-e kötve a CPU. A nagy CPU okozhatja, hogy a kérelem nem dolgozható fel a `synctimeout` intervallumon belül, és a kérés időtúllépést okoz. Ha nagyobb ügyfél-méretre vagy a terhelés elosztására helyezi át a problémát, a probléma szabályozása segíthet.
   - Ellenőrizze, hogy a CPU- [gyorsítótár teljesítményének metrikájának](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)figyelésével bekapcsolta-e a processzort a kiszolgálón. A Redis-ben érkező kérések a CPU-kötés miatt időtúllépést okozhatnak a kérelmekben. Ennek a feltételnek a megoldásához a terhelést több szegmensre is terjesztheti egy prémium szintű gyorsítótárban, vagy frissíthet nagyobb méretre vagy díjszabási szintre. További információ: [kiszolgálóoldali sávszélesség-korlátozás](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Vannak-e olyan parancsok, amelyek hosszú időt vesznek igénybe a kiszolgálón történő feldolgozás során? A hosszú ideig tartó, a Redis-kiszolgálón történő feldolgozást elvégező parancsok időtúllépést okozhatnak. A hosszan futó parancsokkal kapcsolatos további információkért lásd: [hosszan futó parancsok](cache-troubleshoot-server.md#long-running-commands). A Redis-példányhoz a Redis-CLI-ügyféllel vagy a [Redis-konzollal](cache-configure.md#redis-console)csatlakozhat az Azure cache-hez. Ezután futtassa a [slowlog parancs kimenetét](https://redis.io/commands/slowlog) parancsot, és ellenőrizze, hogy vannak-e a vártnál lassabb kérelmek. A Redis-kiszolgáló és a StackExchange. Redis sok kisebb kérelemre van optimalizálva, és nem kevesebb nagy kérést. Az adatai kisebb adattömbökbe való felosztása itt javíthatja a dolgokat.

    A gyorsítótár SSL-végpontjának a Redis-CLI és a stunnel használatával történő csatlakoztatásával kapcsolatos információkért tekintse meg a [ASP.NET munkamenet-szolgáltatójának bejelentése a Redis előzetes kiadásához](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)című blogbejegyzésben.
1. A magas Redis-kiszolgáló terhelése időtúllépéseket eredményezhet. A kiszolgáló terhelését a `Redis Server Load` [gyorsítótár teljesítményének metrikájának](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)figyelésével figyelheti. A kiszolgálói terhelés 100 (maximális érték) azt jelzi, hogy a Redis-kiszolgáló foglalt, üresjárati idő nélkül, feldolgozási kérések. Ha szeretné megtekinteni, hogy bizonyos kérelmek elvesznek-e az összes kiszolgálói képességgel, futtassa a Slowlog parancs kimenetét parancsot az előző bekezdésben leírtak szerint. További információ: magas CPU-használat/-kiszolgáló terhelése.
1. Történt-e olyan esemény az ügyféloldali oldalon, amely a hálózati visszavertség okozta volna? Gyakori események: az ügyfél-példányok számának felfelé vagy lefelé skálázása, az ügyfél új verziójának üzembe helyezése vagy az automatikus méretezés engedélyezve van. A tesztelés során azt találtuk, hogy az automatikus méretezés vagy a felfelé/lefelé méretezés a kimenő hálózati kapcsolat elvesztését okozhatja néhány másodpercig. Az StackExchange. Redis kód ellenáll az ilyen eseményeknek, és újracsatlakozik. Az újrakapcsolódás során a várólistán lévő összes kérelem időtúllépést okozhat.
1. Volt egy nagy kérés, amely korábban több kisebb kérést adott a gyorsítótárnak, amely túllépte az időkorlátot? A hibaüzenetben `qs` paraméterrel megtudhatja, hány kérést küldött az ügyféltől a kiszolgálónak, de nem feldolgozott választ. Ez az érték továbbra is növekszik, mert a StackExchange. Redis egyetlen TCP-kapcsolatban használ, és egyszerre csak egy választ tud olvasni. Annak ellenére, hogy az első művelet időtúllépést okozott, nem állítja le a kiszolgálóról vagy a kiszolgálóra küldött több adatmennyiséget. A rendszer letiltja a további kérelmeket, amíg a nagyméretű kérelem be nem fejeződik, és időtúllépést okozhat. Az egyik megoldás az időtúllépések valószínűségének csökkentése azáltal, hogy a gyorsítótár elég nagy méretű a számítási feladatokhoz és a nagyméretű értékek kisebb adattömbökbe való felosztásához. Egy másik lehetséges megoldás a `ConnectionMultiplexer` objektumok készletének használata az ügyfélen, és az új kérés küldésekor válassza ki a legkevésbé betöltött `ConnectionMultiplexer`. A több kapcsolati objektumba való betöltésnek meg kell akadályoznia, hogy a többi kérelem is időtúllépést okozzon.
1. Ha `RedisSessionStateProvider`használ, győződjön meg róla, hogy az újrapróbálkozások időtúllépését helyesen állította be. `retryTimeoutInMilliseconds` nagyobbnak kell lennie, mint `operationTimeoutInMilliseconds`, ellenkező esetben nem történik újrapróbálkozás. A következő példában `retryTimeoutInMilliseconds` a 3000 értékre van állítva. További információ: [ASP.NET munkamenet-szolgáltató az Azure cache for Redis](cache-aspnet-session-state-provider.md) , valamint [a munkamenet-állapot szolgáltatójának és a kimeneti gyorsítótár-szolgáltató konfigurációs paramétereinek használata](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. A Redis-kiszolgálóhoz tartozó Azure gyorsítótárban található memóriahasználat ellenőrzése `Used Memory RSS` és `Used Memory`[figyelésével](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) . Ha egy kizárási házirend van érvényben, a Redis megkezdi a kulcsok kizárását, amikor `Used_Memory` eléri a gyorsítótár méretét. Ideális esetben `Used Memory RSS` csak valamivel nagyobbnak kell lennie, mint `Used memory`. A nagy különbség azt jelenti, hogy a memória töredezettsége (belső vagy külső). Ha `Used Memory RSS` kisebb, mint `Used Memory`, akkor a gyorsítótár memóriájának egy részét az operációs rendszer felcserélte. Ha ez a csere történik, bizonyos jelentős késések várhatók. Mivel a Redis nem szabályozhatja, hogy a foglalások hogyan legyenek leképezve a memória oldalaira, a magas `Used Memory RSS` gyakran a memória használatának egy csúcsát eredményezi. Ha a Redis-kiszolgáló memóriát szabadít fel, a lefoglaló veszi át a memóriát, de előfordulhat, hogy nem adja vissza a memóriát a rendszernek. Az operációs rendszer által jelentett `Used Memory` érték és a memória-felhasználás között eltérés tapasztalható. Lehetséges, hogy a memóriát a Redis használta és bocsátotta ki, de nem adta vissza a rendszernek. A memóriával kapcsolatos problémák enyhítése érdekében hajtsa végre a következő lépéseket:

   - Frissítse a gyorsítótárat nagyobb méretűre, hogy a rendszer ne fusson a memóriára vonatkozó korlátozásokkal.
   - Állítsa be a kulcs lejárati idejét úgy, hogy a régebbi értékeket proaktív módon kizárja a rendszer.
   - Figyelje a `used_memory_rss` gyorsítótár metrikáját. Ha ez az érték közelíti a gyorsítótár méretét, valószínűleg elkezdi a teljesítménnyel kapcsolatos problémákat látni. Ha prémium szintű gyorsítótárat használ, vagy nagyobb gyorsítótár-méretre kíván frissíteni, Ossza szét az adatszegmenseket több szegmens között.

   További információ: [a Redis-kiszolgáló memória-nyomása](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>További információ

- [Az Azure cache hibaelhárítása Redis ügyféloldali problémák esetén](cache-troubleshoot-client.md)
- [Az Azure cache hibaelhárítása a Redis-kiszolgálókkal kapcsolatos problémák esetén](cache-troubleshoot-server.md)
- [Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Az Azure cache figyelése a Redis](cache-how-to-monitor.md)
