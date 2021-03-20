---
title: Autovákuum optimalizálása – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan optimalizálható az autoporszívó egy Azure Database for PostgreSQL egyetlen kiszolgálón
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012561"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Az autovákuum optimalizálása egy Azure Database for PostgreSQL egyetlen kiszolgálón

Ez a cikk bemutatja, hogyan optimalizálhatja hatékonyan az autovákuumot egy Azure Database for PostgreSQL-kiszolgálón.

## <a name="overview-of-autovacuum"></a>Az autovacuum áttekintése

A PostgreSQL a többverziós Egyidejűség-vezérlést (MVCC) használja a nagyobb adatbázis-párhuzamosságok engedélyezéséhez. Minden frissítés egy beszúrást és egy törlést eredményez, és minden törlési eredmény a sorokban törlésre van kijelölve. A Soft-Mark olyan halott rekordok azonosít, amelyet később törölni kell. Ezeknek a feladatoknak a végrehajtásához a PostgreSQL egy vákuum-feladatot futtat.

A vákuum-feladatok manuálisan vagy automatikusan is elindíthatók. Több halott rekordok létezik, amikor az adatbázis nagy mennyiségű frissítési vagy törlési műveletet tapasztal. Kevesebb halott rekordok létezik, ha az adatbázis üresjáratban van. Gyakrabban kell vákuumot használnia, ha az adatbázis terhelése nehéz, így a vákuum-feladatok *manuális* futtatása nem megfelelő.

Az automatikus porszívó konfigurálható és kihasználható a hangolás előnyeivel. A PostgreSQL által szállított alapértelmezett értékek, amelyekkel biztosítható, hogy a termék bármilyen típusú eszközön működjön. Ezek az eszközök közé tartozik a málna PiS. Az ideális konfigurációs értékek a következőktől függenek:

- Az összes rendelkezésre álló erőforrás, például az SKU és a tárterület mérete.
- Erőforrás-használat.
- Az egyes objektumok jellemzői.

## <a name="autovacuum-benefits"></a>Autovacuum előnyök

Ha nem vákuumot időről időre, a felhalmozott rekordok a következőket eredményezheti:

- Az adatközelítés, például nagyobb adatbázisok és táblák.
- Nagyobb, optimálisnál rosszabb indexek.
- Nagyobb I/O-műveletek.

## <a name="monitor-bloat-with-autovacuum-queries"></a>A duzzadás figyelése autovacuum lekérdezésekkel

A következő minta-lekérdezés célja, hogy azonosítsa az XYZ nevű táblázatban lévő elhalt és élő rekordok számát:

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>Autovákuum-konfigurációk

Az autovákuumot vezérlő konfigurációs paraméterek két fő kérdésre választanak:

- Mikor érdemes elkezdeni?
- Mennyi legyen tiszta a megkezdése után?

Íme néhány automatikus vákuum-konfigurációs paraméter, amelyet az előző kérdések alapján frissíthet, és útmutatást kaphat.

Paraméter|Leírás|Alapértelmezett érték
---|---|---
autovacuum_vacuum_threshold|Meghatározza, hogy legalább hány frissített vagy törölt rekordok van szükség ahhoz, hogy a vákuum-műveletet egyetlen táblában lehessen elindítani. Az alapértelmezett érték a 50 rekordok. Ezt a paramétert csak a PostgreSQL. conf fájlban vagy a Server parancssorban állítsa be. Az egyes táblák beállításainak felülbírálásához módosítsa a tábla tárolási paramétereit.|50
autovacuum_vacuum_scale_factor|Megadja a tábla azon méretének a töredékét, amelyet hozzá szeretne adni a autovacuum_vacuum_thresholdhoz, amikor eldönti, hogy kell-e vákuum-műveletet indítania. Az alapértelmezett érték a 0,2, amely a tábla méretének 20 százalékát képezi. Ezt a paramétert csak a PostgreSQL. conf fájlban vagy a Server parancssorban állítsa be. Az egyes táblák beállításainak felülbírálásához módosítsa a tábla tárolási paramétereit.|0,2
autovacuum_vacuum_cost_limit|Meghatározza az automatikus vákuum-műveletekben használt költségfelosztás értékét. Ha-1 van megadva, amely az alapértelmezett, a rendszer a normál vacuum_cost_limit értéket használja. Ha több feldolgozó is van, akkor az értéket a futó autovacuum-feldolgozók között arányosan osztják el. Az egyes feldolgozók korlátainak összege nem haladja meg a változó értékét. Ezt a paramétert csak a PostgreSQL. conf fájlban vagy a Server parancssorban állítsa be. Az egyes táblák beállításainak felülbírálásához módosítsa a tábla tárolási paramétereit.|-1
autovacuum_vacuum_cost_delay|Meghatározza az automatikus vákuum-műveletekben használt költséghatékonysági értéket. Ha a-1 érték van megadva, a rendszer a normál vacuum_cost_delay értéket használja. Az alapértelmezett érték 20 ezredmásodperc. Ezt a paramétert csak a PostgreSQL. conf fájlban vagy a Server parancssorban állítsa be. Az egyes táblák beállításainak felülbírálásához módosítsa a tábla tárolási paramétereit.|20 MS
autovacuum_naptime | Meghatározza az autoporszívó futtatásának minimális késleltetését az adott adatbázison. A démon minden körben megvizsgálja az adatbázist, és az adatbázisban lévő táblákhoz szükség szerint a szükséges vákuum-és elemzési parancsokat. A késleltetés mérése másodpercben történik. Ezt a paramétert csak a PostgreSQL. conf fájlban vagy a Server parancssorban állítsa be.| 15 s
autovacuum_max_workers | Meghatározza, hogy az autovákuum-indítón kívül legfeljebb hány autoporszívós folyamat fusson egyszerre. Az alapértelmezett érték három. Ezt a paramétert csak a kiszolgáló indításakor állítsa be.|3

Az egyes táblák beállításainak felülbírálásához módosítsa a tábla tárolási paramétereit.

## <a name="autovacuum-cost"></a>Autoporszívós díj

Az alábbi "költségek" a vákuumos művelet futtatásakor:

- A vákuumban futó adatlapok zárolva vannak.
- A rendszer a számítási és a memóriát használja a vákuumos feladatok futtatásakor.

Ennek eredményeképpen ne futtasson túl gyakran vagy túl ritkán a vákuum-feladatokat. A vákuum-feladatnak alkalmazkodnia kell a munkaterheléshez. Tesztelje az összes autovákuum paramétert az egyes kompromisszumok miatt.

## <a name="autovacuum-start-trigger"></a>Autovacuum indítási trigger

Az autoporszívó akkor aktiválódik, ha a halott rekordok száma meghaladja a autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Itt a reltuples állandó.

Az autoporszívóból történő karbantartásnak meg kell őriznie az adatbázis terhelését. Ellenkező esetben előfordulhat, hogy elfogy a tárterület, és általános lassulást tapasztal a lekérdezésekben. Az idő múlásával a vákuum-művelet megtisztítja a kézbesítetlen rekordok, és ezzel egyenlő arányban kell megegyeznie a halott rekordok létrehozásával.

A sok frissítéssel és törléssel rendelkező adatbázisok több halott rekordok rendelkeznek, és több helyre van szükségük. Általában a sok frissítéssel rendelkező adatbázisok és a autovacuum_vacuum_scale_factor és autovacuum_vacuum_threshold alacsony értékeinek kihasználása. Az alacsony értékek meggátolják a halott rekordok hosszú távú felhalmozódását. A kisebb adatbázisokkal rendelkező paramétereknél nagyobb értékeket is használhat, mert a porszívózási igény kevésbé sürgős. A gyakori porszívózás a számítási és a memória díja.

A 20%-os alapértelmezett méretezési tényező jól működik olyan táblákon, amelyek alacsony százalékban elhalt rekordok rendelkeznek. Nem működik jól olyan táblákon, amelyek nagy százalékban elhalt rekordok rendelkeznek. Egy 20 GB-os táblázatban például ez a skálázási tényező 4 GB-nyi elhalt rekordok-t fordít. 1 TB-os táblán a 200 GB-os elhalt rekordok.

A PostgreSQL használatával ezeket a paramétereket a tábla szintjén vagy a példány szintjén állíthatja be. Manapság a táblázat szintjén állíthatja be ezeket a paramétereket Azure Database for PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Az autoporszívó díjainak becslése

Az autoporszívó futtatása "költséges", és vannak paraméterek a vákuum-műveletek futtatókörnyezetének szabályozására. A következő paraméterek segítenek megbecsülni a vákuum futtatásának költségeit:

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

A vákuumos folyamat fizikai oldalakat olvas be, és ellenőrzi a kézbesítetlen rekordok. A shared_buffers minden lapja 1 (vacuum_cost_page_hit) értéknek tekintendő. Az összes többi oldal ára 20 (vacuum_cost_page_dirty), ha az elhalt rekordok létezik, vagy 10 (vacuum_cost_page_miss), ha nem létezik halott rekordok. A vákuum-művelet leáll, ha a folyamat túllépi a autovacuum_vacuum_cost_limit.

A korlát elérésekor a folyamat a autovacuum_vacuum_cost_delay paraméter által megadott időtartamra alvó állapotba lép, mielőtt újra megkezdené. Ha a korlát nem érhető el, az autovacuum a autovacuum_nap_time paraméter által megadott érték után indul el.

Az összefoglalás területen a autovacuum_vacuum_cost_delay és autovacuum_vacuum_cost_limit paraméterek határozzák meg, hogy mennyi idő alatt engedélyezett az adattisztítás. Vegye figyelembe, hogy az alapértelmezett értékek túl alacsonyak a legtöbb árképzési szinten. Ezeknek a paramétereknek az optimális értéke az árképzési rétegtől függ, és ennek megfelelően kell konfigurálni.

A autovacuum_max_workers paraméter határozza meg a párhuzamosan futtatható autoporszívós folyamatok maximális számát.

A PostgreSQL használatával ezeket a paramétereket a tábla szintjén vagy a példány szintjén állíthatja be. Manapság a táblázat szintjén állíthatja be ezeket a paramétereket Azure Database for PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Autovákuum optimalizálása táblázat szerint

A táblázat összes korábbi konfigurációs paraméterét beállíthatja. Bemutatunk egy példát:

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Az autoporszívó egy táblázatos szinkron folyamat. Egy tábla rekordok nagyobb hányada, annál magasabb a "Cost" az autoporszívóhoz. A nagy mennyiségű frissítéssel rendelkező táblákat és a több táblába való törlést is eloszthatja. A táblázatok felosztásával integrálással az autovákuumot, és csökkentheti a "Cost"-t az autoporszívó egyetlen táblán való elvégzéséhez. Emellett növelheti a párhuzamos autovákuumos munkavégzők számát is, hogy a dolgozók ne legyenek liberális módon ütemezve.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az autoporszívó használatáról és finomhangolásáról, tekintse meg a következő PostgreSQL-dokumentációt:

- [18. fejezet, kiszolgáló konfigurációja](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
- [24. fejezet, az adatbázis-karbantartási feladatok rutinja](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
