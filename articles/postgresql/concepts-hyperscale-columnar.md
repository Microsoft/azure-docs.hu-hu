---
title: Oszlopos Table Storage – előzetes verzió – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Adatok tömörítése az oszlopos tárolással (előzetes verzió)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023968"
---
# <a name="columnar-table-storage-preview"></a>Oszlopos tábla tárolója (előzetes verzió)

> [!IMPORTANT]
> Az oszlopos Table Storage a nagy kapacitású (Citus) szolgáltatás jelenleg előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> Megtekintheti a [nagy kapacitású (Citus) előzetes](hyperscale-preview-features.md)verziójának új funkcióinak teljes listáját.

A Azure Database for PostgreSQL-nagy kapacitású (Citus) támogatja a csak Hozzáfűzéses oszlopos tárolást az elemzési és adattárház-számítási feladatokhoz. Ha az oszlopok (sorok helyett) contiguously vannak tárolva, az adatmennyiség egyre tömöríthető válik, és a lekérdezések gyorsabban kérhetik az oszlopok egy részhalmazát.

Ha az oszlopos tárolást szeretné használni, `USING columnar` akkor a tábla létrehozásakor határozza meg a következőt:

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

A nagy kapacitású (Citus) a Beszúrás során a "szalagok" oszlopba konvertálja a sorokat. Mindegyik sáv egy tranzakció értékű adatot vagy 150000 sort tart fenn, attól függően, hogy melyik a kisebb.  (A sáv mérete és az oszlopos tábla egyéb paraméterei a [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) függvénnyel módosíthatók.)

Az alábbi utasítás például mind az öt sort ugyanabba a szalagba helyezi, mert az összes érték egyetlen tranzakcióba van beszúrva:

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

A legjobb megoldás, ha lehetséges, nagy sávokat tesznek elérhetővé, mert a nagy kapacitású (Citus) a sávok alapján külön tömöríti az oszlopos adatok számát. A következőkkel láthatjuk az oszlopos táblázat adatait, például a tömörítési arányt, a sávok számát és a sávok átlagos sorait `VACUUM VERBOSE` :

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

A kimenet azt mutatja, hogy a nagy kapacitású (Citus) a zstd tömörítési algoritmust használta a 1.31 x adattömörítés megszerzéséhez. A tömörítési sebesség a következővel hasonlítja össze a beszúrt adatmennyiséget, mivel a memóriában található a b-ben.) az adott adatszalagon tömörített adatmennyiség mérete.

A mérés módja miatt a tömörítési sebesség a tábla sor-és oszlopos tárolójának méretétől eltérő lehet. Ennek az egyetlen módja, ha úgy találja, hogy a különbség egy sor-és oszlopos tábla létrehozása, amely ugyanazt az adathalmazt tartalmazza, és hasonlítsa össze a következőt:

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

A kis tábla esetében az oszlopos tároló ténylegesen több helyet használ, de az adatmennyiség növekedésével megnyeri a tömörítést.

## <a name="example"></a>Példa

Az oszlopos tárolás jól használható a tábla particionálásával. Példaként tekintse meg a Citus motor közösségi dokumentációját, [amely az oszlopos tárolással való archiválást](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage)tartalmazza.

## <a name="gotchas"></a>Buktatóinak elkerülése

* Az oszlopos tároló tömöríti a sávokat. A sávokat a rendszer tranzakció alapján hozza létre, így a tranzakciók egy sora egyetlen sort helyez el a saját szalagokba. Az egysoros sávok tömörítése és teljesítménye rosszabb lesz, mint a sorok táblázata. Mindig tömegesen szúrja be az oszlopos táblába.
* Ha összezavarja és columnarize egy rakás kis csíkot, a rendszer elakad.
  Az egyetlen javítás egy új oszlopos tábla létrehozása és az adatok másolása az eredetiből egy tranzakcióból:
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* Az alapvetően nem tömöríthető-alapú adatmennyiség lehet probléma, bár az oszlopos tárolók továbbra is hasznosak adott oszlopok kiválasztásakor. Nem kell betölteni a többi oszlopot a memóriába.
* Sor-és oszlopos partíciókat tartalmazó particionált táblán a frissítéseket körültekintően kell megcélozni. Csak a sorok partícióinak kiszűrése.
   * Ha a művelet egy adott sor partícióra irányul (például: `UPDATE p2 SET i = i + 1` ), akkor az sikeres lesz; ha a megadott oszlopos partícióra irányul (például `UPDATE p1 SET i = i + 1` ), az sikertelen lesz.
   * Ha a művelet a particionált táblára irányul, és olyan WHERE záradékkal rendelkezik, amely kizárja az összes oszlopos partíciót (például `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'` ), akkor az sikeres lesz.
   * Ha a művelet a particionált táblára vonatkozik, de a partíciós kulcs oszlopaiban nem végez szűrést, akkor az sikertelen lesz. Még ha vannak olyan WHERE záradékok is, amelyek csak oszlopos partíciókban egyeznek, nem elég – a partíciós kulcsot is szűrni kell.

## <a name="limitations"></a>Korlátozások

Ez a funkció továbbra is számos jelentős korlátozással rendelkezik. Lásd: [nagy kapacitású (Citus) korlátai és korlátozásai](concepts-hyperscale-limits.md#columnar-storage).

## <a name="next-steps"></a>Következő lépések

* Tekintse meg az oszlopos tárolás példáját egy Citus [Idősor oktatóanyagban](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) (külső hivatkozás).
