---
title: Az Azure Cache for Redis kiszolgálóoldali hibáinak elhárítása
description: Megtudhatja, Hogyan oldhatók fel az Azure cache szolgáltatással kapcsolatos gyakori kiszolgálóoldali problémák a Redis, például a memória terhelése, a magas CPU-használat, a hosszan futó parancsok vagy a sávszélesség korlátozásai miatt.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 12d78846f5892e71388de6e6e76b868f9b14d4de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008916"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Az Azure Cache for Redis kiszolgálóoldali hibáinak elhárítása

Ez a szakasz azokat a hibaelhárítási problémákat ismerteti, amelyek az Azure cache Redis vagy az azt üzemeltető virtuális gép (ek) feltétele miatt fordulnak elő.

- [A Redis-kiszolgáló memóriaterhelése](#memory-pressure-on-redis-server)
- [Magas CPU-használat vagy-kiszolgáló terhelése](#high-cpu-usage-or-server-load)
- [Hosszú ideig futó parancsok](#long-running-commands)
- [Kiszolgálóoldali sávszélesség-korlátozás](#server-side-bandwidth-limitation)

> [!NOTE]
> Az útmutatóban ismertetett hibaelhárítási lépések többek között a Redis-parancsok futtatására és a különböző teljesítmény-mérőszámok figyelésére vonatkozó utasításokat tartalmaznak. További információkért és útmutatásért tekintse meg a [További információ](#additional-information) szakasz cikkeit.
>

## <a name="memory-pressure-on-redis-server"></a>A Redis-kiszolgáló memóriaterhelése

A kiszolgálóoldali memória-terhelés a kérelmek feldolgozását késleltető teljesítményproblémák összes típusához vezet. Ha a memória terhelése eléri a memóriát, előfordulhat, hogy a rendszer lemezre küldi az adatlapot. Az _oldal meghibásodása_ miatt a rendszerleállás jelentősen lelassul. Ennek a memória-nyomásnak több lehetséges oka van:

- A gyorsítótár megtelik a maximális kapacitás közelében lévő adattal.
- A Redis nagy memória töredezettségét látja. Ezt a töredezettséget leggyakrabban a nagyméretű objektumok tárolása okozza, mivel a Redis kis objektumokra van optimalizálva.

A Redis két statisztikát tesz elérhetővé az [info](https://redis.io/commands/info) paranccsal, amely segíthet a probléma azonosításában: "used_memory" és "used_memory_rss". Ezek a [metrikák](cache-how-to-monitor.md#view-metrics-with-azure-monitor) a portál használatával tekinthetők meg.

Számos különféle módosítást végezhet, hogy a memóriahasználat megfelelő maradjon:

- [Állítsa be a memória-házirendet](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , és állítsa be a lejárati időt a kulcsokra. Töredezettség esetén előfordulhat, hogy ez a szabályzat nem lesz elegendő.
- [Állítson be egy olyan maxmemory foglalt értéket](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , amely elég nagy a memória töredezettségének kompenzálására.
- Ossza fel a nagy méretű, gyorsítótárazott objektumokat kisebb kapcsolódó objektumokra.
- [Hozzon létre riasztásokat](cache-how-to-monitor.md#alerts) olyan metrikákhoz, mint a felhasznált memória mennyisége, így hamar értesülhet a lehetséges hatásokról.
- Nagyobb méretű gyorsítótár-méretre [méretezheti](cache-how-to-scale.md) a memóriát.

## <a name="high-cpu-usage-or-server-load"></a>Magas CPU-használat vagy-kiszolgáló terhelése

A kiszolgálók nagy terhelése vagy CPU-kihasználtsága azt jelenti, hogy a kiszolgáló nem tudja időben feldolgozni a kérelmeket. Előfordulhat, hogy a kiszolgáló nem válaszol, és nem tud lépést tartani a kérelmek díjszabásával.

A metrikák, például a CPU vagy a kiszolgáló terhelésének [figyelése](cache-how-to-monitor.md#view-metrics-with-azure-monitor) . Figyelje meg a CPU-használathoz tartozó, időtúllépésekkel egyező tüskéket.

Több módosítást is végezhet a kiszolgálók nagy terhelésének csökkentése érdekében:

- Vizsgálja meg, hogy mi okozza a CPU-tüskéket, például a [hosszú ideig futó parancsokat](#long-running-commands) , amelyek a nagy memória-nyomás miatt az alább vagy az oldalon hibásak.
- [Riasztásokat hozhat létre](cache-how-to-monitor.md#alerts) olyan mérőszámokon, mint a CPU vagy a kiszolgáló terhelése, hogy korán értesüljön a lehetséges hatásokról.
- Nagyobb méretű gyorsítótár-méretre [Méretezés](cache-how-to-scale.md) nagyobb CPU-kapacitással.

## <a name="long-running-commands"></a>Hosszú ideig futó parancsok

Egyes Redis-parancsok drágábbak, mint mások. A [Redis-parancsok dokumentációja](https://redis.io/commands) az egyes parancsok időbeli összetettségét mutatja. Mivel a Redis-parancsok feldolgozása egyszálas, a Futtatás ideje alatt álló parancs letiltja az összes többiet, amely azután következik be. Tekintse át a Redis-kiszolgálónak kiadott parancsokat, hogy megértsék a teljesítményre gyakorolt hatásukat. A [kulcsok](https://redis.io/commands/keys) parancs például gyakran használatos az O (N) művelet ismerete nélkül. A kulcsok a [vizsgálat](https://redis.io/commands/scan) használatával elkerülhetők a CPU-tüskék csökkentése érdekében.

A [slowlog parancs kimenetét](https://redis.io/commands/slowlog) parancs használatával a kiszolgálón végrehajtott költséges parancsok mérhetők.

## <a name="server-side-bandwidth-limitation"></a>Kiszolgálóoldali sávszélesség-korlátozás

A különböző gyorsítótár-méretek eltérő hálózati sávszélességgel rendelkeznek. Ha a kiszolgáló meghaladja a rendelkezésre álló sávszélességet, akkor a rendszer nem küldi el az adatforgalmat az ügyfélnek gyorsan. Az ügyfelek kérései időtúllépést okoznak, mert a kiszolgáló nem tud elég gyorsan leküldeni az adott ügyfélnek az adatmennyiséget.

A "cache Read" és a "cache Write" metrikák használatával megtekintheti, hogy mennyi kiszolgálóoldali sávszélességet használ a rendszer. Ezek a [metrikák](cache-how-to-monitor.md#view-metrics-with-azure-monitor) a portálon tekinthetők meg.

Az olyan helyzetek enyhítése, amikor a hálózati sávszélesség-használat a maximális kapacitáshoz közeledik:

- Az ügyfél hívási viselkedésének módosítása a hálózati igény csökkentése érdekében.
- [Hozzon létre riasztásokat](cache-how-to-monitor.md#alerts) olyan metrikákhoz, mint például a gyorsítótár-olvasás vagy a gyorsítótárírás, így idejekorán értesülhet a lehetséges negatív hatásokról.
- Nagyobb méretű gyorsítótár-méretre [Méretezés](cache-how-to-scale.md) nagyobb hálózati sávszélesség-kapacitással.

## <a name="additional-information"></a>További információ

- [Az Azure Cache for Redis ügyféloldali hibáinak elhárítása](cache-troubleshoot-client.md)
- [A megfelelő szint kiválasztása](cache-overview.md#choosing-the-right-tier)
- [Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Az Azure Cache for Redis monitorozása](cache-how-to-monitor.md)
- [Hogyan Futtathatok Redis-parancsokat?](cache-development-faq.md#how-can-i-run-redis-commands)
