---
title: Konfigurációs beállítások – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Nagy kapacitású-(Citus-) kiszolgálócsoport beállításai, beleértve a csomópontok számítását, tárolását és régióit.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/12/2021
ms.openlocfilehash: 48537483501165d4a978afdbd05560613170d187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165611"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – nagy kapacitású (Citus) konfigurációs beállítások

## <a name="compute-and-storage"></a>Számítás és tárolás
 
A számítási és tárolási beállításokat a munkavégző csomópontok és a nagy kapacitású-(Citus-) kiszolgálócsoport koordinátor csomópontja egymástól függetlenül is kiválaszthatja.  A számítási erőforrások virtuális mag-ként vannak megadva, amely az alapul szolgáló hardver logikai PROCESSZORát jelöli. A kiépítés tárolási mérete a nagy kapacitású-(Citus-) kiszolgálócsoport koordinátora és munkavégző csomópontjai számára elérhető kapacitásra utal. A tároló tartalmazza az adatbázisfájlok, az ideiglenes fájlok, a tranzakciós naplók és a postgres-kiszolgáló naplóit.
 
| Erőforrás              | Munkavégző csomópont           | Koordinátor csomópont      |
|-----------------------|-----------------------|-----------------------|
| Számítás, virtuális mag       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória/virtuális mag, GiB | 8                     | 4                     |
| Tároló mérete, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Tárolási típus          | Általános célú (SSD) | Általános célú (SSD) |
| IOPS                  | Legfeljebb 3 IOPS/GiB      | Legfeljebb 3 IOPS/GiB      |

Az egyetlen nagy kapacitású (Citus) csomópontban található RAM teljes mennyisége a kiválasztott számú virtuális mag alapul.

| Virtuális mag | Egy feldolgozói csomópont, GiB RAM | Koordinátori csomópont, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

A kiépített tárterület teljes mennyisége határozza meg az egyes feldolgozók és a koordinátorok csomópontja számára elérhető I/O-kapacitást is.

| Tároló mérete, TiB | Maximális IOPS |
|-------------------|--------------|
| 0,5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6 148        |

A teljes nagy kapacitású (Citus) fürt esetében az összesített IOPS a következő értékekre működik:

| Munkavégző csomópontok | 0,5 TiB, összes IOPS | 1 TiB, összes IOPS | 2 TiB, összes IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12 296            |
| 3            | 4 608               | 9 216             | 18 444            |
| 4            | 6,144               | 12 288            | 24 592            |
| 5            | 7 680               | 15 360            | 30 740            |
| 6            | 9 216               | 18 432            | 36 888            |
| 7            | 10 752              | 21 504            | 43 036            |
| 8            | 12 288              | 24 576            | 49 184            |
| 9            | 13 824              | 27 648            | 55 332            |
| 10           | 15 360              | 30 720            | 61 480            |
| 11           | 16 896              | 33 792            | 67 628            |
| 12           | 18 432              | 36 864            | 73 776            |
| 13           | 19 968              | 39 936            | 79 924            |
| 14           | 21 504              | 43 008            | 86 072            |
| 15           | 23 040              | 46 080            | 92 220            |
| 16           | 24 576              | 49 152            | 98 368            |
| 17           | 26 112              | 52 224            | 104 516           |
| 18           | 27 648              | 55 296            | 110 664           |
| 19           | 29 184              | 58 368            | 116 812           |
| 20           | 30 720              | 61 440            | 122 960           |

## <a name="regions"></a>Régiók
A nagy kapacitású-(Citus-) kiszolgálócsoportok a következő Azure-régiókban érhetők el:

* Amerika
    * Közép-Kanada
    * USA középső régiója
    * USA keleti régiója
    * USA 2. keleti régiója
    * USA északi középső régiója
    * USA 2. nyugati régiója
* Ázsia és a Csendes-óceáni térség:
    * Kelet-Ausztrália
    * Kelet-Japán
    * Dél-Korea középső régiója
    * Délkelet-Ázsia
* Európa
    * Észak-Európa
    * Az Egyesült Királyság déli régiója
    * Nyugat-Európa

Előfordulhat, hogy a régiók némelyike nem aktiválódik minden Azure-előfizetésen. Ha a fenti listából szeretne egy régiót használni, és nem látja az előfizetésben, vagy ha a listán nem szereplő régiót szeretne használni, nyisson meg egy [támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-and-limitations"></a>Korlátok és korlátozások

A következő szakasz a nagy kapacitású (Citus) szolgáltatás kapacitását és működési korlátait ismerteti.

### <a name="maximum-connections"></a>Kapcsolatok maximális száma

Minden PostgreSQL-kapcsolat (akár tétlen is) legalább 10 MB memóriát használ, ezért fontos az egyidejű kapcsolatok korlátozása. A csomópontok állapotának megőrzésére a következő korlátozások vonatkoznak:

* Koordinátor csomópont
   * Kapcsolatok maximális száma: 300
   * Maximális felhasználói kapcsolatok: 297
* Munkavégző csomópont
   * Kapcsolatok maximális száma: 600
   * Maximális felhasználói kapcsolatok: 597

A fenti korlátokon túli kapcsolódási kísérletek hibával meghiúsulnak. A rendszer három kapcsolatot tart fenn a figyelési csomópontok számára, ezért három kevesebb kapcsolat áll rendelkezésre a felhasználói lekérdezéseknél, mint a kapcsolatok összege.

Az új kapcsolatok létrehozása időt vesz igénybe. Ez a legtöbb alkalmazással működik, amelyek sok rövid életű kapcsolatot igényelnek. Javasoljuk, hogy használjon egy kapcsolati Pooler, amely csökkenti az üresjárati tranzakciókat és a meglévő kapcsolatok újrafelhasználását. További információért látogasson el a [blogbejegyzésbe](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

### <a name="storage-scaling"></a>Tárolási skálázás

A koordinátor és a munkavégző csomópontok tárterülete felméretezhető (nagyobb), de nem méretezhető (csökkent).

### <a name="storage-size"></a>Tárterület mérete

A koordinátori és munkavégző csomópontok legfeljebb 2 TiB tárterületet támogatnak. A csomópont-és fürtök méretéhez tekintse meg a [fenti](#compute-and-storage) rendelkezésre álló tárolási beállításokat és a IOPS számítását.

### <a name="database-creation"></a>Adatbázis létrehozása

A Azure Portal hitelesítő adatokat biztosít ahhoz, hogy a nagy kapacitású-(Citus-) kiszolgálócsoport, az adatbázis pontosan egy adatbázishoz kapcsolódjon `citus` . Egy másik adatbázis létrehozása jelenleg nem engedélyezett, és az adatbázis létrehozása parancs hibával leáll.

## <a name="pricing"></a>Díjszabás
A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/postgresql/).
A kívánt konfiguráció költségének megtekintéséhez a [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) a kiválasztott beállítások alapján a **configure (Konfigurálás** ) lapon szereplő havi költséget jeleníti meg. Ha még nem rendelkezik Azure-előfizetéssel, az Azure díjszabási kalkulátor használatával megbecsülheti a becsült árat. Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **elemek hozzáadása**, majd az **adatbázisok** kategóriát, és válassza a **Azure Database for PostgreSQL – nagy kapacitású (Citus)** lehetőséget a beállítások testreszabásához.
 
## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan [hozhat létre nagy kapacitású-(Citus-) kiszolgálócsoport a portálon](quickstart-create-hyperscale-portal.md).
