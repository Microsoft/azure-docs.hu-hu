---
title: Lekérdezési teljesítmény – Azure Database for MariaDB
description: Megtudhatja, hogyan használhatja a MAGYARÁZATot a Azure Database for MariaDB lekérdezési teljesítményének hibakereséséhez.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 2b7491723ffcff73e4b243fe54ef18608167d636
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664715"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>A profil lekérdezési teljesítményének ismertetése a Azure Database for MariaDB
A **magyarázat** egy praktikus eszköz a lekérdezések optimalizálásához. A magyarázó utasítás használatával információkat kaphat az SQL-utasítások végrehajtásáról. A következő kimenet egy példát mutat be a magyarázó utasítások végrehajtásához.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Ahogy az ebben a példában is látható, a *kulcs* értéke null. Ez a kimeneti érték azt jelenti, hogy a MariaDB nem talál a lekérdezésre optimalizált indexeket, és teljes táblázatos vizsgálatot végez. A lekérdezés optimalizálásához adjon hozzá egy indexet az **azonosító** oszlophoz.

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

Az új magyarázat azt mutatja, hogy a MariaDB mostantól index használatával korlátozza a sorok számát az 1 értékre, ami jelentősen lerövidíti a keresési időt.
 
## <a name="covering-index"></a>Borító indexe
A lefedési index az indexben található lekérdezés összes oszlopát tartalmazza az adattáblákból való lekérések csökkentése érdekében. Íme egy illusztráció a következő **Group By** utasításban.
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Ahogy a kimenet is látható, a MariaDB nem használ indexeket, mert nem állnak rendelkezésre megfelelő indexek. Emellett *az ideiglenes használatot is mutatja; Ha a file sort használja*, ami azt jelenti, hogy a MariaDB létrehoz egy ideiglenes táblázatot, amely kielégíti a **Group By** záradékot.
 
A **C2** -es oszlop indexének létrehozása önmagában nem tesz különbséget, és a MariaDB továbbra is létre kell hoznia egy ideiglenes táblát:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Ebben az esetben a **C1** -es és a **C2** -es kezelt **index** is létrehozható, amelynek során a rendszer a **C2**-es értéket közvetlenül az indexben adja hozzá a további adatkeresések eltávolításához.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Ahogy a fenti magyarázat mutatja, a MariaDB mostantól a kezelt indexet használja, és nem hoz létre ideiglenes táblát. 

## <a name="combined-index"></a>Kombinált index
A kombinált index több oszlopból álló értékeket tartalmaz, és az Indexelt oszlopok értékeinek összefűzésével rendezhető sorok tömbje lehet. Ez a metódus a **Group By** utasításban lehet hasznos.

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

A MariaDB olyan *rendezési* műveletet hajt végre, amely meglehetősen lassú, különösen, ha sok sort kell rendeznie. A lekérdezés optimalizálásához összevont index hozható létre mindkét sorba rendezett oszlopon.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

A magyarázat azt mutatja, hogy a MariaDB képes a kombinált index használatára, hogy elkerülje a további rendezést, mivel az index már rendezve van.
 
## <a name="conclusion"></a>Összegzés
 
A magyarázat és a különböző típusú indexek használata jelentősen növelheti a teljesítményt. A tábla indexe nem feltétlenül jelenti azt, hogy a MariaDB használni tudná a lekérdezésekhez. Az indexek használatával mindig érvényesítse a feltételezéseket a lekérdezések MAGYARÁZATával és optimalizálásával.

## <a name="next-steps"></a>Következő lépések
- Ha szeretné megkeresni a leginkább érintett kérdésekre adott társi válaszokat, vagy új kérdést/választ szeretne küldeni, látogasson el [a Microsoft Q&egy kérdés oldalra](/answers/topics/azure-database-mariadb.html) vagy [stack overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb).