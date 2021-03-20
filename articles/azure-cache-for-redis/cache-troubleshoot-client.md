---
title: Az Azure Cache for Redis ügyféloldali hibáinak elhárítása
description: Ismerje meg, hogy miként oldhatók meg az Azure cache szolgáltatással kapcsolatos gyakori ügyféloldali problémák, például a Redis-ügyfelek Redis, a forgalom Bursa, a nagy CPU, a korlátozott sávszélesség, a nagyméretű kérelmek vagy a nagy válaszok mérete.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: 122c96c95aea794fbba9cab8a9a5b867f9f34b48
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008967"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Az Azure Cache for Redis ügyféloldali hibáinak elhárítása

Ez a szakasz azokat a hibaelhárítási problémákat ismerteti, amelyek az alkalmazás által használt Redis-ügyfél feltétele miatt következnek be.

- [Memória-nyomás a Redis-ügyfélen](#memory-pressure-on-redis-client)
- [Forgalom burst](#traffic-burst)
- [Magas ügyféloldali CPU-használat](#high-client-cpu-usage)
- [Ügyféloldali sávszélesség korlátozása](#client-side-bandwidth-limitation)
- [Nagyméretű kérelem vagy válasz mérete](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Memória-nyomás a Redis-ügyfélen

Az ügyfélgépen a memória terhelése minden olyan teljesítménnyel kapcsolatos problémát eredményez, amely késleltetheti a válaszok feldolgozását a gyorsítótárból. Ha a memória terhelése eléri a memóriát, előfordulhat, hogy a rendszer lemezre küldi az adatlapot. Az _oldal meghibásodása_ miatt a rendszerleállás jelentősen lelassul.

A memória terhelésének észlelése az ügyfélen:

- Figyelje meg a memória használatát a gépen, és győződjön meg róla, hogy nem lépi túl a rendelkezésre álló memóriát.
- Figyelje az ügyfél `Page Faults/Sec` teljesítményszámláló értékét. Normál működés esetén a legtöbb rendszernek van néhány lapja. A kérések időtúllépésével párhuzamosan hirtelen növekvő számú laphibák nagy memóriaterhelést jelezhetnek.

A nagy mennyiségű memória terhelése az ügyfélen több módon is enyhíthető:

- Az ügyfélen a memóriahasználat csökkentése érdekében vegye figyelembe a memória-használati mintákat.
- Frissítse az ügyfél virtuális gépét nagyobb méretűre, több memóriával.

## <a name="traffic-burst"></a>Forgalom burst

A gyenge beállításokkal összevont adatforgalom `ThreadPool` miatt a Redis-kiszolgáló által már elküldett adatok feldolgozása késéseket eredményezhet, de az ügyfél oldalán még nem használták fel azokat.

Figyelje meg, hogyan `ThreadPool` változnak a statisztikák az idő múlásával [egy példa `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)használatával. A  `TimeoutException` StackExchange. Redis-ből származó üzeneteket a következőhöz hasonlóan használhatja a további vizsgálathoz:

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

Az előző kivételben számos érdekes probléma van:

- Figyelje meg, hogy a `IOCP` szakasz és a `WORKER` szakasz értéke nagyobb, `Busy` mint az `Min` érték. Ez a különbség azt jelenti, hogy a `ThreadPool` beállításokat módosítani kell.
- A következőt is megtekintheti: `in: 64221` . Ez az érték azt jelzi, hogy az ügyfél kernel szoftvercsatorna rétegében 64 211 bájt érkezett, de az alkalmazás nem olvasta el. Ez a különbség általában azt jelenti, hogy az alkalmazás (például a StackExchange. Redis) nem olvas be adatokat a hálózatról olyan gyorsan, ahogy a kiszolgáló elküldi Önnek.

[A `ThreadPool` Beállítások konfigurálásával](cache-management-faq.md#important-details-about-threadpool-growth) megadhatja, hogy a szál készlete gyors legyen a burst forgatókönyvek alatt.

## <a name="high-client-cpu-usage"></a>Magas ügyféloldali CPU-használat

A magas ügyféloldali CPU-használat azt jelzi, hogy a rendszer nem tud lépést tartani a kért munkával. Bár a gyorsítótár gyorsan elküldötte a választ, előfordulhat, hogy az ügyfél időben nem tud feldolgozni a választ.

Az ügyfél rendszerszintű CPU-használatának figyelése a Azure Portal vagy a számítógépen található teljesítményszámlálók használatával. Ügyeljen arra, hogy ne figyelje a *folyamat* processzorát, mert egy folyamat alacsony CPU-használattal rendelkezhet, de a rendszerszintű CPU magas lehet. Figyelje meg a CPU-használathoz tartozó, időtúllépésekkel egyező tüskéket. A magas CPU is okozhat magas `in: XXX` értéket a `TimeoutException` hibaüzenetekben a [forgalom burst](#traffic-burst) szakasza szerint.

> [!NOTE]
> A StackExchange. Redis 1.1.603 és a későbbi verzió tartalmazza a `local-cpu` metrikát a `TimeoutException` hibaüzenetekben. Győződjön meg arról, hogy a [StackExchange. Redis NuGet-csomag](https://www.nuget.org/packages/StackExchange.Redis/)legújabb verzióját használja. A kódban folyamatosan rögzített hibák teszik hatékonyabbá az időtúllépéseket, így a legújabb verzió fontos.
>

Az ügyfél magas CPU-használatának csökkentése:

- Vizsgálja meg, mi okozza a CPU-tüskéket.
- Frissítse az ügyfelet egy nagyobb méretű virtuálisgép-méretre több CPU-kapacitással.

## <a name="client-side-bandwidth-limitation"></a>Ügyféloldali sávszélesség korlátozása

Az ügyfélgépek architektúrája alapján előfordulhat, hogy korlátozásokkal rendelkeznek a rendelkezésre álló hálózati sávszélesség mennyiségétől függően. Ha az ügyfél túllépi a rendelkezésre álló sávszélességet a hálózati kapacitás túlterhelésével, akkor a rendszer nem dolgozza fel az adatokat az ügyfél oldalán, ha a kiszolgáló elküldi azt. Ez a helyzet időtúllépéseket eredményezhet.

Figyelje meg, hogyan változik a sávszélesség-használat az idő múlásával [egy példa `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)használatával. Előfordulhat, hogy ez a kód nem fut sikeresen bizonyos korlátozott engedélyekkel rendelkező környezetekben (például az Azure webhelyek szolgáltatásban).

A szolgáltatás mérséklése érdekében csökkentse a hálózati sávszélesség-használatot, vagy növelje az ügyfél virtuális gép méretét egy nagyobb hálózati kapacitással.

## <a name="large-request-or-response-size"></a>Nagyméretű kérelem vagy válasz mérete

A nagyméretű kérések/válaszok időtúllépést okozhatnak. Tegyük fel például, hogy az ügyfélen konfigurált időtúllépési érték 1 másodperc. Az alkalmazás két kulcsot kér (például "A" és "B") egy időben (ugyanazokkal a fizikai hálózati kapcsolatok használatával). A legtöbb ügyfél támogatja a "pipeline" kérést, ahol az "A" és a "B" kérelmeket egymás után küldi el a rendszer, anélkül, hogy megvárná a válaszokat. A kiszolgáló ugyanezen sorrendben küldi vissza a válaszokat. Ha az "A" válasz nagy, akkor a későbbi kérelmek esetében az időtúllépés nagy részét is megeszik.

A következő példában az "A" és a "B" kérést a rendszer gyorsan elküldi a kiszolgálónak. A kiszolgáló gyorsan megkezdi az "A" és A "B" válaszok küldését. Az adatátviteli időpontok miatt a "B" válasznak az "A" válasz mögött kell várnia, noha a kiszolgáló gyorsan reagál.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

Ezt a kérést/választ nehéz mérni. A nagyméretű kérelmek és válaszok nyomon követéséhez az ügyfél kódját is felhasználhatja.

A nagyméretű válaszok méretének felbontása változó, azonban a következők:

1. Néhány nagy érték helyett nagy mennyiségű kis értékre optimalizálhatja alkalmazását.
    - Az előnyben részesített megoldás az adatoknak a kapcsolódó kisebb értékekre való bontása.
    - Tekintse meg a post [Mi az ideális érték a Redis számára? Túl nagy a 100 KB?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) További információ a kisebb értékek ajánlott megoldásáról.
1. Növelje a virtuális gép méretét, hogy nagyobb sávszélesség-képességeket kapjon
    - Az ügyfél vagy a kiszolgáló virtuális gépe további sávszélessége csökkentheti az adatátviteli időpontokat a nagyobb válaszok érdekében.
    - Hasonlítsa össze az aktuális hálózati használatot mindkét gépen a virtuális gép jelenlegi méretének korlátaival. Több sávszélesség csak a kiszolgálón, vagy csak az ügyfélen lehet elég.
1. Növelje az alkalmazás által használt kapcsolatok objektumainak számát.
    - A különböző kapcsolati objektumokra irányuló kérelmeket ciklikus multiplexelés használatával hajthatja végre.

## <a name="additional-information"></a>További információ

- [Az Azure Cache for Redis kiszolgálóoldali hibáinak elhárítása](cache-troubleshoot-server.md)
- [Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
