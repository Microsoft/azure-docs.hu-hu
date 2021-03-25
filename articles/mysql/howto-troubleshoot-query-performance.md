---
title: Lekérdezési teljesítmény – Azure Database for MySQL
description: Megtudhatja, hogyan használhatja a MAGYARÁZATot a Azure Database for MySQL lekérdezési teljesítményének hibakereséséhez.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 8a7a0f8754b0ed7981d0ec888552efc65f451ef9
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109811"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>A profil lekérdezési teljesítményének ismertetése a Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

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

Ahogy az ebben a példában is látható, a *kulcs* értéke null. Ez azt jelenti, hogy a MySQL nem talál a lekérdezésre optimalizált indexeket, és teljes táblázatos vizsgálatot végez. A lekérdezés optimalizálásához adjon hozzá egy indexet az **azonosító** oszlophoz.

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

Az új magyarázat azt mutatja, hogy a MySQL mostantól egy indexet használ a sorok számának 1 értékre való korlátozásához, ami jelentősen lerövidíti a keresési időt.
 
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

Ahogy a kimenet is látható, a MySQL nem használ indexeket, mert nem állnak rendelkezésre megfelelő indexek. Emellett *az ideiglenes használatot is mutatja; A file sort használatával* a MySQL egy ideiglenes táblázatot hoz létre, amely kielégíti a **Group By** záradékot.
 
A **C2** oszlophoz tartozó index létrehozása önmagában nem tesz különbséget, és a MySQL-nek továbbra is létre kell hoznia egy ideiglenes táblát:

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

A fenti magyarázat szerint a MySQL mostantól a kezelt indexet használja, és nem hoz létre ideiglenes táblát. 

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

A MySQL olyan *rendezési* műveletet hajt végre, amely meglehetősen lassú, különösen, ha sok sort kell rendeznie. A lekérdezés optimalizálásához összevont index hozható létre mindkét sorba rendezett oszlopon.

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

A magyarázat azt mutatja, hogy a MySQL képes a kombinált index használatára, hogy elkerülje a további rendezést, mivel az index már rendezve van.
 
## <a name="conclusion"></a>Összegzés
 
A magyarázat és a különböző típusú indexek használata jelentősen növelheti a teljesítményt. A táblázat indexe nem feltétlenül jelenti azt, hogy a MySQL használni tudná a lekérdezésekhez. Az indexek használatával mindig érvényesítse a feltételezéseket a lekérdezések MAGYARÁZATával és optimalizálásával.


## <a name="next-steps"></a>Következő lépések
- Ha szeretné megkeresni a leginkább érintett kérdésekre adott társi válaszokat, vagy új kérdést/választ szeretne küldeni, látogasson el [a Microsoft Q&egy kérdés oldalra](/answers/topics/azure-database-mysql.html) vagy [stack overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
