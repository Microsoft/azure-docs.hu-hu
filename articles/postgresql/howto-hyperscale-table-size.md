---
title: Táblázat méretének meghatározása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Elosztott táblák valódi méretének megkeresése nagy kapacitású-(Citus-) kiszolgálócsoport esetén
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937611"
---
# <a name="determine-table-and-relation-size"></a>Tábla és a kapcsolatok méretének meghatározása

A táblázat méretének szokásos módja a PostgreSQL-ben, a `pg_total_relation_size` (z) rendszerben drasztikusan a nagy kapacitású (Citus) elosztott tábláinak mérete jelenti.
Ez a függvény egy nagy kapacitású (Citus) szolgál, hogy felfedje a táblák méretét a koordinátor csomóponton.  A valóságban az elosztott táblákban lévő adatfeldolgozó csomópontok (szegmensekben) a munkavégző csomópontokon, nem pedig a koordinátoron vannak. A rendszer az elosztott tábla méretének valódi mértékét adja meg a szegmens méretek összegeként. A nagy kapacitású (Citus) segítő függvényeket biztosít az adatok lekérdezéséhez.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>Függvény</th>
<th>Válaszok</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name)</td>
<td><ul>
<li>A tábla tényleges adatainak mérete (a "<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">fő elágazás</a>").</li>
<li>A kapcsolatok lehet egy tábla vagy egy index neve.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name)</td>
<td><ul>
<li><p>citus_relation_size plusz:</p>
<blockquote>
<ul>
<li><a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">szabad terület leképezésének</a> mérete</li>
<li><a href="https://www.postgresql.org/docs/current/static/storage-vm.html">láthatósági Térkép</a> mérete</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name)</td>
<td><ul>
<li><p>citus_table_size plusz:</p>
<blockquote>
<ul>
<li>indexek mérete</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Ezek a függvények a PostgreSQL- [objektumok szabványos méretének három függvényéhez](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)hasonlóak, kivéve, ha nem tudnak csatlakozni egy csomóponthoz.

## <a name="example"></a>Példa

A következőképpen listázhatja az összes elosztott tábla méretét:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Kimenet:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Következő lépések

* További információ a [kiszolgálócsoport méretezéséről](howto-hyperscale-scale-grow.md) .
* Megkülönböztetni [a](concepts-hyperscale-nodes.md) nagy kapacitású (Citus).
