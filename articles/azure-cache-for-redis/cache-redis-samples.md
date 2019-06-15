---
title: Az Azure Cache Redis-minták |} A Microsoft Docs
description: Útmutató az Azure Cache for Redis használatához
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: 73c771ab18d1cc2944298818c1cab90eb2f277ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829803"
---
# <a name="azure-cache-for-redis-samples"></a>Azure Cache for Redis-minták
Ez a témakör az Azure Cache Redis-minták kiterjedő forgatókönyvek, például egy cache olvasása és írása adat- és a gyorsítótárból való kapcsolódással és az Azure Cache gyorsítótárral az ASP.NET a Redis-szolgáltatók listája. A minták néhány letölthető projektek, és néhány tartalmaz részletes útmutatást és kódrészletek tartalmazza, de ne csatolja egy letölthető projektet.

## <a name="hello-world-samples"></a>Hello world minta
Ebben a szakaszban a minták bemutatják a Redis-példány az Azure Cache szolgáltatáshoz való csatlakozáskor és a gyorsítótárba, számos nyelvet használja az adatok írásakor vagy olvasásakor alapjait, és Redis ügyfelek.

A [Helló, világ](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta bemutatja, hogyan hajthat végre különböző gyorsítótár-műveletek használata a [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET ügyfél.

Ez a példa bemutatja, hogyan lehet:

* Használja a különböző kapcsolati lehetőségek
* Olvasási és írási objektumokat, és a gyorsítótárból a szinkron és aszinkron műveletek használatával
* Visszatérési értékek megadott kulcsok a Redis MGET/MSET parancsok használatával
* A Redis tranzakciós műveletek végrehajtása
* A Redis használata sorolja fel, és rendezett készleteket
* Store JsonConvert objektumszerializáló használata .NET-objektumokká
* Használhatja a Redis csoportokat címkézés megvalósítása
* A Redis-fürt használata

További információkért lásd: a [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) dokumentációja a Githubon, és további használati forgatókönyvek esetén tekintse meg a [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) egységteszteket.

[Az Azure Cache használata pythonnal redis](cache-python-get-started.md) bemutatja, hogyan kezdheti el az Azure Cache a redis használata Python és a [redis-py](https://github.com/andymccurdy/redis-py) ügyfél.

[.NET-objektumok használata a gyorsítótárban](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) adjuk meg a .NET-objektumokká szerializálásához így írja őket, és olvassa el őket egy Azure-gyorsítótárból a Redis-példányt. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Használja az Azure Cache redis egy horizontális felskálázási csatlakozópanel meghibásodása ASP.NET SignalR
A [használata Azure Cache redis egy horizontális felskálázási csatlakozópanel meghibásodása, az ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) a minta azt ismerteti, hogyan használhatja az Azure Cache redis egy SignalR-csatlakozópanel meghibásodása. Csatlakozópanel meghibásodása kapcsolatos további információkért lásd: [SignalR horizontális Felskálázás a Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Az Azure Cache Redis ügyfél lekérdezés minta
Ez a minta bemutatja a összehasonlítja teljesítménye között gyorsítótár származó adatok egységes elérésével és az adatok elérése az adatmegőrzés storage-ból. Ez a példa két projektet tartalmaz.

* [Hogyan Azure Cache redis a jobb teljesítmény érdekében az adatokat bemutató](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Az adatbázis és a gyorsítótár szeretne a bemutatóra kezdőérték](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Az ASP.NET munkamenet-állapot és a kimeneti gyorsítótárazás
A [használata Azure Cache redis ASP.NET SessionState és OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) minta azt ismerteti, hogyan, hogy az Azure Cache redis használni az ASP.NET munkamenet- és kimeneti gyorsítótár használatával a SessionState és OutputCache szolgáltatók redis .

## <a name="manage-azure-cache-for-redis-with-maml"></a>Az Azure Cache Redis-MAML kezelése
A [Azure Cache kezelése az Azure kezelési Kódtáraival redis](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) minta bemutatja, hogyan lehet a Azure kezelési kódtárak kezelése – (létrehozása / frissítése / törlése) a gyorsítótárat. 

## <a name="custom-monitoring-sample"></a>Egyéni figyelés minta
A [Access Azure Cache Redis figyelési adatok](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) a minta azt ismerteti, hogyan férhet hozzá monitorozási adatok az Azure Cache redis az Azure Portalon kívül.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>A PHP és a Redis használatával írt Twitter-stílusú klón
A [Retwis](https://github.com/SyntaxC4-MSFT/retwis) minta a Redis "Hello World". Legyen a Redis és a PHP használatával írt minimális Twitter-stílusú közösségi hálózati klón használatával a [Predis](https://github.com/nrk/predis) ügyfél. A forráskód nagyon egyszerű, és egyszerre megjeleníthető különböző Redis datové struktury célja.

## <a name="bandwidth-monitor"></a>A sávszélesség-figyelő
A [sávszélesség figyelő](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) minta lehetővé teszi, hogy az ügyfélen használt sávszélesség figyelése. A sávszélesség mérését, futtassa a mintát a gyorsítótár ügyfélszámítógépen, a gyorsítótár-hívások, és figyelje meg a sávszélesség a sávszélesség-figyelő minta által jelentett.

