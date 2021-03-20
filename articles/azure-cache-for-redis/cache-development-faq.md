---
title: Azure cache a Redis-fejlesztéssel kapcsolatos gyakori kérdések
description: Ismerje meg az Azure cache Redis való fejlesztését segítő gyakori kérdésekre adott válaszokat
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: bafd8a9752d2587ec52fe586e442e3bfc86d7537
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585768"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Azure cache a Redis-fejlesztéssel kapcsolatos gyakori kérdések

Ez a cikk az Azure cache Redis való fejlesztésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="common-questions-and-answers"></a>Gyakori kérdések és válaszok
Ez a szakasz a következő gyakori kérdéseket ismerteti:

* [Hogyan szerezhetem be az Azure cache-t a Redis-hez?](#how-can-i-get-started-with-azure-cache-for-redis)
* [Mi a StackExchange. Redis konfigurációs beállításai?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Milyen Azure cache-t használhatok a Redis-ügyfelek számára?](#what-azure-cache-for-redis-clients-can-i-use)
* [Létezik helyi emulátor az Azure cache Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hogyan Futtathatok Redis-parancsokat?](#how-can-i-run-redis-commands)
* [Miért nem rendelkezik az Azure cache for Redis rendelkezik MSDN Class Library-referenciával?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Használhatom az Azure cache-t a Redis PHP-munkamenet-gyorsítótárként?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Mik azok a Redis-adatbázisok?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Hogyan szerezhetem be az Azure cache-t a Redis-hez?
Az Azure cache Redis többféleképpen is elsajátíthatja.

* A [.net](cache-dotnet-how-to-use-azure-redis-cache.md), a [ASP.net](cache-web-app-howto.md), a [Java](cache-java-get-started.md), a [Node.js](cache-nodejs-get-started.md)és a [Python](cache-python-get-started.md)szolgáltatáshoz elérhető oktatóanyagok közül választhat.
* Megtekintheti, [hogyan hozhat létre High-Performance alkalmazásokat a Redis Microsoft Azure cache használatával](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Megtekintheti az ügyfél dokumentációját a projekt fejlesztői nyelvének megfelelő ügyfelek számára, hogy megtudja, hogyan használhatja a Redis. Számos Redis-ügyfél használható az Azure cache használatával a Redis. A Redis-ügyfelek listáját a következő témakörben tekintheti meg: [https://redis.io/clients](https://redis.io/clients) .

Ha még nem rendelkezik Azure-fiókkal, a következőket teheti:

* [Nyisson egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Jóváírásokat kap, amelyeket fizetős Azure-szolgáltatások kipróbálására használhat fel. Még ha a keretét el is használta, továbbra is megtarthatja a fiókot, és használhatja az ingyenes szolgáltatásokat és lehetőségeket.
* [Aktiválja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Az MSDN-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Mi a StackExchange. Redis konfigurációs beállításai?
A StackExchange. Redis számos lehetőséget kínál. Ez a szakasz néhány gyakori beállításról beszél. További információ a StackExchange. Redis beállításokról: [StackExchange. Redis konfiguráció](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Description | Ajánlás |
| --- | --- | --- |
| AbortOnConnectFail |Ha igaz értékre van állítva, a kapcsolat hálózati hiba után nem fog újracsatlakozni. |Állítsa hamis értékre, és hagyja, hogy a StackExchange. Redis automatikusan újracsatlakozik. |
| ConnectRetry |A kapcsolódási kísérletek megismétlésének száma a kezdeti csatlakozás során. |Útmutatásért tekintse meg az alábbi megjegyzéseket. |
| ConnectTimeout |A csatlakozási műveletekhez tartozó MS-időtúllépés. |Útmutatásért tekintse meg az alábbi megjegyzéseket. |

Általában az ügyfél alapértelmezett értékei elegendőek. A számítási feladatok alapján részletesen beállíthatja a beállításokat.

* **Újrapróbálkozások**
  * A ConnectRetry és a ConnectTimeout esetében az általános útmutató fail fast, majd újra próbálkozik. Ez az útmutató a számítási feladatokon alapul, és mennyi időt vesz igénybe az ügyfél számára, hogy kiadjon egy Redis-parancsot, és választ kapjon.
  * Hagyja, hogy a StackExchange. Redis automatikusan újracsatlakozik a kapcsolat állapotának ellenőrzése és az újbóli csatlakoztatása helyett. **Kerülje a ConnectionMultiplexer. IsConnected tulajdonság használatát**.
  * Bevezetés – időnként előfordulhat, hogy az újrapróbálkozást és a hógolyó újrapróbálkozását, illetve a rendszer soha nem állítja vissza. Ha bekövetkezik a bevezetést, érdemes lehet egy exponenciális leállítási újrapróbálkozási algoritmust használni a Microsoft Patterns & Practices csoport által közzétett [általános útmutató](/azure/architecture/best-practices/transient-faults) című témakörben leírtak szerint.
  
* **Időtúllépési értékek**
  * Vegye figyelembe a számítási feladatokat, és ennek megfelelően állítsa be az értékeket. Ha nagyméretű értékeket tárol, állítsa az időtúllépést magasabb értékre.
  * Állítsa `AbortOnConnectFail` hamis értékre, és hagyja, hogy a StackExchange. Redis újracsatlakozik.
  * Egyetlen ConnectionMultiplexer-példányt használjon az alkalmazáshoz. A LazyConnection használatával létrehozhat egyetlen, a kapcsolati tulajdonság által visszaadott példányt, ahogy az a [ConnectionMultiplexer osztály használatával a gyorsítótárhoz való kapcsolódással](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)is látható.
  * Állítsa a `ConnectionMultiplexer.ClientName` tulajdonságot az alkalmazás-példány egyedi nevére diagnosztikai célokra.
  * Több `ConnectionMultiplexer` példány használata az egyéni munkaterhelésekhez.
    * Ezt a modellt követheti, ha eltérő terhelést használ az alkalmazásban. Például:
    * A nagyméretű kulcsok kezeléséhez egy multiplexer is tartozhat.
    * A kis kulcsok kezeléséhez egy multiplexer is tartozhat.
    * Megadhat különböző értékeket a kapcsolati időtúllépésekhez, és újrapróbálkozhat a logikával minden egyes használt ConnectionMultiplexer.
    * Állítsa be az `ClientName` egyes multiplexerek tulajdonságát a diagnosztika segítésére.
    * Ez az útmutató az egyszerűbb késést eredményezhet `ConnectionMultiplexer` .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Milyen Azure cache-t használhatok a Redis-ügyfelek számára?
A Redis egyik nagyszerű dologa, hogy sok ügyfél támogatja számos különböző fejlesztési nyelvet. Az ügyfelek aktuális listájáért lásd: [Redis-ügyfelek](https://redis.io/clients). A különböző nyelveket és ügyfeleket bemutató oktatóanyagokat lásd: az [Azure cache használata a Redis](cache-dotnet-how-to-use-azure-redis-cache.md) és a testvér cikkeihez a tartalomjegyzékben.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Létezik helyi emulátor az Azure cache Redis?
Nincs helyi emulátor az Azure cache-hez a Redis-hez, de a redis-server.exe MSOpenTech-verzióját futtathatja a helyi számítógép [Redis parancssori eszközeiről](https://github.com/MSOpenTech/redis/releases/) , és csatlakozhat ahhoz, hogy hasonló élményt kapjon a helyi gyorsítótár-emulátorhoz, ahogy az alábbi példában is látható:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Ha kívánja, beállíthatja, hogy a [Redis. conf](https://redis.io/topics/config) fájl jobban illeszkedjen az online Azure cache [alapértelmezett gyorsítótár-beállításaihoz](cache-configure.md#default-redis-server-configuration) a Redis, ha szükséges.

### <a name="how-can-i-run-redis-commands"></a>Hogyan Futtathatok Redis-parancsokat?
A [Redis parancsokban](https://redis.io/commands#) felsorolt parancsok bármelyikét felhasználhatja, kivéve a [Redis parancsokban felsorolt parancsokat, amelyek nem támogatottak az Azure cache for Redis esetében](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Több lehetőség is van a Redis parancsok futtatására.

* Ha standard vagy prémium szintű gyorsítótárral rendelkezik, Redis-parancsokat futtathat a Redis- [konzol](cache-configure.md#redis-console)használatával. A Redis-konzol biztonságos módot biztosít a Redis parancsok futtatására a Azure Portal.
* Használhatja a Redis parancssori eszközöket is. A használatához hajtsa végre a következő lépéseket:
* Töltse le a [Redis parancssori eszközöket](https://github.com/MSOpenTech/redis/releases/).
* Kapcsolódjon a gyorsítótárhoz a használatával `redis-cli.exe` . Továbbítsa a gyorsítótár-végpontot a-h kapcsolóval és a-a kulcs használatával a következő példában látható módon:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> A Redis parancssori eszközei nem működnek a TLS-porttal, de használhatnak olyan segédprogramot, `stunnel` amely az eszközök TLS-porthoz való biztonságos csatlakoztatásához szükséges utasításokat követve a [Redis parancssori eszköz használata az Azure cache for Redis](./cache-how-to-redis-cli-tool.md) című cikkben található útmutatást.
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Miért nem rendelkezik az Azure cache for Redis rendelkezik MSDN Class Library-referenciával?
A Redis Microsoft Azure gyorsítótára a népszerű, nyílt forráskódú memóriában tárolt adattárban, a Redis-on alapul. Számos [Redis-ügyfél](https://redis.io/clients) számára elérhető számos programozási nyelvhez. Minden ügyfél saját API-val rendelkezik, amely hívásokat kezdeményez az Azure cache Redis-példányhoz a [Redis parancsok](https://redis.io/commands)használatával.

Mivel minden ügyfél eltérő, az MSDN-ben nem találhatók központi osztályok, és mindegyik ügyfél saját dokumentációt tart fenn. A dokumentáción kívül számos oktatóanyag mutatja be, hogyan kezdheti el az Azure cache használatát a Redis különböző nyelveken és gyorsítótár-ügyfeleken. Az oktatóanyagok eléréséhez tekintse meg az [Azure cache használata a Redis](cache-dotnet-how-to-use-azure-redis-cache.md) és a testvéri cikkek a tartalomjegyzékben című cikket.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Használhatom az Azure cache-t a Redis PHP-munkamenet-gyorsítótárként?
Igen, ha a Redis-hez készült Azure cache-t PHP-munkamenet-gyorsítótárként szeretné használni, adja meg a kapcsolati karakterláncot a Redis-példányhoz tartozó Azure cache-hez `session.save_path` .

> [!IMPORTANT]
> Ha az Azure cache-t PHP-Redis használja, akkor a gyorsítótárhoz való csatlakozáshoz használt biztonsági kulcsot URL-címként kell kódolnia, ahogy az az alábbi példában is látható:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Ha a kulcs nem kódolt URL-cím, a következőhöz hasonló üzenet jelenhet meg: `Failed to parse session.save_path`
>

További információ az Azure cache Redis való használatáról a PhpRedis-ügyféllel PHP-munkamenet-gyorsítótárként: [php-munkamenet kezelője](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Mik azok a Redis-adatbázisok?

A Redis-adatbázisok csak az azonos Redis-példányon belüli adatmennyiségek logikai elkülönítését jelentik. A gyorsítótár memóriája egy adott adatbázis összes adatbázisa és tényleges memória-felhasználása között van megosztva az adatbázisban tárolt kulcsoktól/értéktől. Egy C6-os gyorsítótár például 53 GB memóriával rendelkezik, a p5 pedig 120 GB. Dönthet úgy, hogy az összes 53 GB/120 GB-ot egy adatbázisba helyezi, vagy akár több adatbázis között is feloszthatja. 

> [!NOTE]
> Ha prémium szintű Azure cache-t használ a Redis-alapú fürtözéshez, csak az adatbázis 0 érhető el. Ez a korlátozás belső Redis korlátozás, és nem kifejezetten az Azure cache for Redis. További információkért lásd: [az ügyfélalkalmazás használatának módosítása a fürtözéshez?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering).
> 
> 

## <a name="next-steps"></a>Következő lépések

További információ [Az Azure cache Redis-ről – gyakori kérdések](cache-faq.md).