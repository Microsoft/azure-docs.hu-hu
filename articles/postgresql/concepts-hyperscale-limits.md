---
title: Korlátok és korlátozások – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Nagy kapacitású-(Citus-) kiszolgálócsoportok jelenlegi korlátai
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023962"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Azure Database for PostgreSQL – nagy kapacitású (Citus) korlátai és korlátozásai

A következő szakasz a nagy kapacitású (Citus) szolgáltatás kapacitását és működési korlátait ismerteti.

## <a name="maximum-connections"></a>Kapcsolatok maximális száma

Minden PostgreSQL-kapcsolat (akár tétlen is) legalább 10 MB memóriát használ, ezért fontos az egyidejű kapcsolatok korlátozása. A csomópontok állapotának megőrzésére a következő korlátozások vonatkoznak:

* Koordinátor csomópont
   * Kapcsolatok maximális száma: 300
   * Maximális felhasználói kapcsolatok: 297
* Munkavégző csomópont
   * Kapcsolatok maximális száma: 600
   * Maximális felhasználói kapcsolatok: 597

> [!NOTE]
> Az [előzetes verziójú funkciókat](hyperscale-preview-features.md) engedélyező kiszolgálócsoport esetében a koordinátorhoz való kapcsolódási korlát némileg eltér:
>
> * Koordinátori csomópontok maximális kapcsolatai
>    * 300 0-3 virtuális mag
>    * 500 4-15 virtuális mag
>    * 1000 16 + virtuális mag

A fenti korlátokon túli kapcsolódási kísérletek hibával meghiúsulnak. A rendszer három kapcsolatot tart fenn a figyelési csomópontok számára, ezért három kevesebb kapcsolat áll rendelkezésre a felhasználói lekérdezéseknél, mint a kapcsolatok összege.

### <a name="connection-pooling"></a>Kapcsolatok készletezése

Az új kapcsolatok létrehozása időt vesz igénybe. Ez a legtöbb alkalmazással működik, amelyek sok rövid életű kapcsolatot igényelnek. Javasoljuk, hogy használjon egy kapcsolati Pooler, amely csökkenti az üresjárati tranzakciókat és a meglévő kapcsolatok újrafelhasználását. További információért látogasson el a [blogbejegyzésbe](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

Saját Pooler is futtathat, vagy használhatja az Azure által felügyelt PgBouncer.

#### <a name="managed-pgbouncer-preview"></a>Felügyelt PgBouncer (előzetes verzió)

> [!IMPORTANT]
> A nagy kapacitású (Citus) felügyelt PgBouncer-kapcsolatok Pooler jelenleg előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> Megtekintheti a [nagy kapacitású (Citus) előzetes](hyperscale-preview-features.md)verziójának új funkcióinak teljes listáját.

A kapcsolati gyűjtők, például a PgBouncer lehetővé teszik, hogy az ügyfelek egyszerre csatlakozzanak a koordinátori csomóponthoz. Az alkalmazások csatlakoznak a Pooler, és a Pooler továbbítanak parancsokat a céladatbázis számára.

Amikor az ügyfelek a PgBouncer-on keresztül csatlakoznak, az adatbázisban aktívan futtatható kapcsolatok száma nem változik. Ehelyett a PgBouncer várólistára helyezi a kapcsolatokat, és futtatja őket, amikor az adatbázis készen áll.

A nagy kapacitású (Citus) mostantól a PgBouncer felügyelt példányát kínálja a kiszolgálócsoportok számára (előzetes verzióban). Akár 2 000 egyidejű ügyfélkapcsolatot is támogat.
A PgBouncer-en keresztüli kapcsolódáshoz kövesse az alábbi lépéseket:

1. Nyissa meg a Azure Portal a kiszolgálói csoport **kapcsolódási karakterláncok** lapját.
2. Engedélyezze a jelölőnégyzet **PgBouncer-kapcsolatok karakterláncait**. (A felsorolt kapcsolatok karakterláncai megváltoznak.)
3. Ügyfélalkalmazások frissítése az új karakterlánccal való kapcsolódáshoz.

## <a name="storage-scaling"></a>Tárolási skálázás

A koordinátor és a munkavégző csomópontok tárterülete felméretezhető (nagyobb), de nem méretezhető (csökkent).

## <a name="storage-size"></a>Tárterület mérete

A koordinátori és munkavégző csomópontok legfeljebb 2 TiB tárterületet támogatnak. A csomópont-és fürtök méretéhez tekintse meg a [fenti](concepts-hyperscale-configuration-options.md#compute-and-storage) rendelkezésre álló tárolási beállításokat és a IOPS számítását.

## <a name="database-creation"></a>Adatbázis létrehozása

A Azure Portal hitelesítő adatokat biztosít ahhoz, hogy a nagy kapacitású-(Citus-) kiszolgálócsoport, az adatbázis pontosan egy adatbázishoz kapcsolódjon `citus` . Egy másik adatbázis létrehozása jelenleg nem engedélyezett, és az adatbázis létrehozása parancs hibával leáll.

## <a name="columnar-storage"></a>Oszlopos tárolás

A nagy kapacitású (Citus) jelenleg a következő korlátozásokkal rendelkezik az [oszlopos táblákkal](concepts-hyperscale-columnar.md):

* A tömörítés a lemezen van, nem a memóriában
* Csak Hozzáfűzés (frissítés/törlés támogatás nélkül)
* Nincs lemezterület-visszanyerés (például a visszaállított tranzakciók továbbra is lemezterületet foglalnak magukban)
* Nem támogatottak az indexek, az indexelési vizsgálatok vagy a bitképek indexelése
* Nincs tidscans
* Egyetlen minta sem vizsgál
* Nincs pirítós-támogatás (a nagy értékű támogatott beágyazott értékek)
* Az ÜTKÖZÉSi utasítások nem támogatottak (kivéve a nem megadott cél nélküli műveleteket).
* Nem támogatott a rekordos zárolások támogatása (válassza a... A MEGOSZTÁSHOZ VÁLASSZA A... LEHETŐSÉGET. FRISSÍTÉShez)
* A szerializálható elkülönítési szint nem támogatott.
* A PostgreSQL-kiszolgáló 12 + verziójának támogatása
* Nem támogatott külső kulcsok, egyedi korlátozások vagy kizárási korlátozások
* Nem támogatott a logikai dekódolás
* A csomóponton belüli párhuzamos vizsgálatok nem támogatottak
* A következő után nem támogatott... Minden egyes sor eseményindítóhoz
* Nincsenek nem NAPLÓzott oszlopos táblák
* Nincsenek ideiglenes oszlopos táblák

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [hozhat létre nagy kapacitású-(Citus-) kiszolgálócsoport a portálon](quickstart-create-hyperscale-portal.md).
