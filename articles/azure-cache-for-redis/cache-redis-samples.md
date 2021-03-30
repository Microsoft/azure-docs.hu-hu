---
title: Azure Cache for Redis-minták
description: 'Ismerje meg, hogyan használhatja az Azure cache-t a Redis az alábbi mintakód-mintákkal: csatlakozás gyorsítótárhoz, gyorsítótárban lévő információk olvasása és írása, ASP.NET Azure cache a Redis-szolgáltatók számára.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 553850173f463a05b13768eb3b9e17703bfa2886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212293"
---
# <a name="azure-cache-for-redis-samples"></a>Azure Cache for Redis-minták
Ez a témakör felsorolja az Azure cache-t a Redis-mintákhoz, beleértve a gyorsítótárhoz való csatlakozást, az adatok olvasását és a gyorsítótárból történő írását, valamint a Redis-szolgáltatók ASP.NET Azure-gyorsítótárának használatát. Néhány minta letölthető projekt, és néhány lépésről lépésre útmutatást nyújt, és tartalmazza a kódrészleteket, de nem kapcsolódik egy letölthető projekthez.

## <a name="hello-world-samples"></a>Hello World-minták
Az ebben a szakaszban szereplő minták az Azure cache Redis-példányhoz való csatlakozásának alapjait mutatják be, valamint az adatgyorsítótárba való adatolvasást és-írást számos különböző nyelven és Redis-ügyféllel.

A [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta bemutatja, hogyan hajthat végre különböző gyorsítótárazási műveleteket a [StackExchange. Redis](https://github.com/StackExchange/StackExchange.Redis) .net-ügyfél használatával.

Ez a példa a következőket mutatja be:

* Különböző kapcsolatbeállításokat használhat
* Objektumok olvasása és írása a gyorsítótárból a szinkron és aszinkron műveletek használatával
* A megadott kulcsok értékeinek visszaadásához használja a Redis MGET/MSET parancsait.
* Redis tranzakciós műveletek végrehajtása
* Redis-listák és rendezett készletek használata
* .NET-objektumok tárolása JsonConvert-szerializálók használatával
* Redis-készletek használata a címkézés megvalósításához
* Redis-fürt használata

További információ: [StackExchange. Redis](https://github.com/StackExchange/StackExchange.Redis) dokumentáció a githubon, és további használati forgatókönyvek: [StackExchange. Redis. tesztek](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) egység tesztek.

Az [Azure cache használata a Redis-hez a Python](cache-python-get-started.md) segítségével megtudhatja, hogyan kezdheti el az Azure cache használatát a Redis a Python és a [Redis-a-](https://github.com/andymccurdy/redis-py) sel ügyfélprogram használatával.

[A gyorsítótárban lévő .net-objektumok használatakor a .net-](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) objektumok szerializálásának egyik módja jelenik meg, így a Redis-példányhoz tartozó Azure cache-ből írhatók és olvashatók. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Az Azure cache használata a Redis kibővíthető Hátlapként a ASP.NET-jelzőhöz
Az [Azure cache használata a Redis-hez a ASP.net signaler minta kibővítő hátlapja](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) azt mutatja be, hogyan használható az Azure cache a Redis-hez. További információ a hátlapról: [a signaler horizontális felskálázás és a Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure cache a Redis ügyfél-lekérdezési mintához
Ez a minta azt mutatja be, hogy összehasonlítja az adatok gyorsítótárból való elérésének és az adatmegőrzési tároló adatainak elérésének a teljesítményét. Ez a minta két projekttel rendelkezik.

* [Bemutató arról, hogy az Azure cache for Redis hogyan javíthatja a teljesítményt a gyorsítótárazási adataival](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [A bemutató adatbázisának és gyorsítótárának elvetése](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET munkamenet-állapot és a kimeneti gyorsítótárazás
Az [Azure cache használata az ASP.net-SessionState és OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) -minta tárolására a Redis-ben azt mutatja be, hogy az Azure cache hogyan használható a Redis-munkamenet és a kimeneti gyorsítótár tárolására a ASP.net SessionState és OutputCache-szolgáltatói használatával.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Az Azure cache kezelése a Redis és a MAML szolgáltatással
A [Redis Azure cache kezelése az Azure Management librarys használatával](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) minta azt mutatja be, hogyan használható az Azure felügyeleti kódtárak a gyorsítótár kezelésére – (létrehozás/frissítés/törlés). 

## <a name="custom-monitoring-sample"></a>Egyéni figyelési minta
A [Redis-figyelési adatmintahoz való hozzáférés az Azure cache](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) -ben azt mutatja be, hogy hogyan férhet hozzá az Azure-gyorsítótárban az Azure-portálon kívüli Redis tartozó figyelési adataihoz.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>A PHP és a Redis használatával írt Twitter-klón
A [Retwis](https://github.com/SyntaxC4-MSFT/retwis) minta a Redis Hello World. Ez egy minimális Twitter-stílusú közösségi hálózati klón, amelyet a Redis és a PHP használatával írt a [Predis](https://github.com/nrk/predis) -ügyfél használatával. A forráskód úgy lett kialakítva, hogy nagyon egyszerű és egyszerre jelenjen meg különböző Redis-adatstruktúrák.

## <a name="bandwidth-monitor"></a>Sávszélesség-figyelő
A [sávszélesség-figyelő](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) minta lehetővé teszi az ügyfélen használt sávszélesség figyelését. A sávszélesség méréséhez futtassa a mintát a gyorsítótár-ügyfélszámítógépen, hajtson végre hívásokat a gyorsítótárba, és figyelje meg a sávszélesség-figyelő minta által jelentett sávszélességet.
