---
title: Konfigurációs beállítások – rugalmas skálázás (Citus) – Azure Database for PostgreSQL
description: A csomópont-rugalmas skálázás (Citus), beleértve a csomópontok számítási, tárolási és régióira vonatkozó lehetőségeket.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/07/2021
ms.openlocfilehash: 1dd0666c2946896ed324fb3986bb7946890b73de
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388703"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – rugalmas skálázás (Citus) beállításai

## <a name="compute-and-storage"></a>Számítás és tárolás
 
A számítási és tárolási beállítások egymástól függetlenül választhatók ki a feldolgozó csomópontokhoz és a koordinátor-csomóponthoz egy rugalmas skálázás (Citus)kiszolgálócsoportban.  A számítási erőforrások virtuális magokként biztosítanak, amelyek a mögöttes hardver logikai PROCESSZORát képviselik. Az üzembehelyezés tárolási mérete az adott kiszolgálócsoport koordinátora és munkavégző csomópontjai számára elérhető rugalmas skálázás (Citus) jelenti. A tároló tartalmazza az adatbázisfájlokat, az ideiglenes fájlokat, a tranzakciónaplókat és a Postgres-kiszolgáló naplóit.

### <a name="standard-tier"></a>Standard csomag
 
| Erőforrás              | Feldolgozó csomópont           | Koordinátor-csomópont      |
|-----------------------|-----------------------|-----------------------|
| Számítás, virtuális magok       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória virtuális magonként, GiB | 8                     | 4                     |
| Tárterület mérete, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tárolási típus          | Általános célú (SSD) | Általános célú (SSD) |
| IOPS                  | Legfeljebb 3 IOPS/GiB      | Legfeljebb 3 IOPS/GiB      |

Az egy csomóponton rugalmas skálázás (Citus) RAM teljes mennyisége a kiválasztott számú virtuális magon alapul.

| Virtuális magok | Egy feldolgozó csomópont, GiB RAM | Koordinátor-csomópont, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

A kiépíthető tárterület teljes mennyisége az egyes feldolgozói és koordinátor-csomópontok számára elérhető I/O-kapacitást is meghatározza.

| Tárterület mérete, TiB | Maximális IOPS |
|-------------------|--------------|
| 0,5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

A teljes rugalmas skálázás (Citus) összesített IOPS-érték a következő értékeket követi:

| Munkavégző csomópontok | 0,5 TiB, teljes IOPS | 1 TiB, teljes IOPS | 2 TiB, teljes IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6,144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

### <a name="basic-tier-preview"></a>Alapszintű csomag (előzetes verzió)

> [!IMPORTANT]
> Az rugalmas skálázás (Citus) alapszintű csomag jelenleg előzetes verzióban érhető el.  Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> Az előzetes verziójú funkciók további új funkcióinak teljes listáját [a](hyperscale-preview-features.md)rugalmas skálázás (Citus).

Az rugalmas skálázás (Citus) [alapszintű csomag](concepts-hyperscale-tiers.md) egy kiszolgálócsoport egyetlen csomóponttal.  Mivel nincs különbség a koordinátor és a feldolgozó csomópont között, kevésbé bonyolult a számítási és tárolási erőforrások kiválasztása.

| Erőforrás              | Elérhető lehetőségek     |
|-----------------------|-----------------------|
| Számítás, virtuális magok       | 2, 4, 8               |
| Memória virtuális magonként, GiB | 4                     |
| Tárterület mérete, GiB     | 128, 256, 512         |
| Tárolási típus          | Általános célú (SSD) |
| IOPS                  | Legfeljebb 3 IOPS/GiB      |

Az egy csomóponton rugalmas skálázás (Citus) RAM teljes mennyisége a kiválasztott számú virtuális magon alapul.

| Virtuális magok | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

A kiépíthető tárterület teljes mennyisége az alapszintű csomópont számára elérhető I/O-kapacitást is meghatározza.

| Tárterület mérete, GiB | Maximális IOPS |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1,536        |

## <a name="regions"></a>Régiók
rugalmas skálázás (Citus) kiszolgálócsoportok a következő Azure-régiókban érhetők el:

* Amerika: 
    * Dél-Brazília
    * Közép-Kanada
    * Az USA középső régiója
    * USA keleti régiója *
    * USA 2. keleti régiója
    * USA északi középső régiója
    * USA 2. nyugati régiója
* Ázsia és a Csendes-óceáni térség:
    * Kelet-Ausztrália
    * Kelet-Japán
    * Dél-Korea középső régiója
    * Délkelet-Ázsia
* Európa:
    * Közép-Franciaország
    * Észak-Európa
    * Az Egyesült Királyság déli régiója
    * Nyugat-Európa

( \* = támogatja az előzetes [verziójú funkciókat](hyperscale-preview-features.md))

Előfordulhat, hogy ezen régiók némelyike kezdetben nem lesz aktiválva az összes Azure-előfizetésben. Ha a fenti lista egyik régióját szeretné használni, és nem szeretné látni az előfizetésében, vagy ha olyan régiót szeretne használni, amely nem szerepel a listán, nyisson meg egy [támogatási kérést.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="pricing"></a>Díjszabás
A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását.](https://azure.microsoft.com/pricing/details/postgresql/)
A kívánt konfiguráció költségeinek megtekintése [](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) a Azure Portal lapon jeleníti meg  a havi költségeket a kiválasztott beállítások alapján. Ha nem rendelkezik Azure-előfizetéssel, az Azure díjkalkulátor használatával megbecsülheti a becsült árat. Az [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **Elemek** hozzáadása lehetőséget, **bontsa** ki az Adatbázisok kategóriát, majd válassza Azure Database for PostgreSQL **–** rugalmas skálázás (Citus) beállítások testreszabásához.
 
## <a name="next-steps"></a>Következő lépések
Megtudhatja, [hogyan hozhat létre rugalmas skálázás (Citus)-kiszolgálócsoportot a portálon.](quickstart-create-hyperscale-portal.md)
