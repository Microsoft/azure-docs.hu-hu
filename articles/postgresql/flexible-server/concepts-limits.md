---
title: Korlátok – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL rugalmas kiszolgáló korlátozásait ismerteti, például a kapcsolatok számát és a tárolási motor beállításait.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 0221022c342735744d59f956d6047b4abf23b5cf
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516515"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Korlátok Azure Database for PostgreSQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Az alábbi szakaszok az adatbázis-szolgáltatás kapacitását és működési korlátait ismertetik. Ha szeretne többet megtudni az erőforrásról (számítási, memória-, tárolási) szintjeiről, tekintse meg a [számítási és tárolási](concepts-compute-storage.md) cikkeket.

## <a name="maximum-connections"></a>Kapcsolatok maximális száma

Alább láthatók a kapcsolatok maximális száma az árképzési szinten és a virtuális mag. Az Azure-rendszernek három kapcsolattal kell rendelkeznie a Azure Database for PostgreSQL rugalmas kiszolgáló figyeléséhez.

| SKU neve             | Virtuális mag | Memória mérete | Kapcsolatok maximális száma | Felhasználói kapcsolatok maximális száma |
|----------------------|--------|-------------|-----------------|----------------------|
| **Burstable**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4. GiB       | 100             | 97                   |
| **Általános célú**  |        |             |                 |                      |
| D2s_v3               | 2      | 8 GiB       | 214             | 211                  |
| D4s_v3               | 4      | 16 GiB      | 429             | 426                  |
| D8s_v3               | 8      | 32 GiB      | 859             | 856                  |
| D16s_v3              | 16     | 64 GiB      | 1718            | 1715                 |
| D32s_v3              | 32     | 128 GiB     | 3437            | 3434                 |
| D48s_v3              | 48     | 192 GiB     | 5000            | 4997                 |
| D64s_v3              | 64     | 256 GiB     | 5000            | 4997                 |
| **Memória optimalizálva** |        |             |                 |                      |
| E2s_v3               | 2      | 16 GiB      | 1718            | 1715                 |
| E4s_v3               | 4      | 32 GiB      | 3437            | 3434                 |
| E8s_v3               | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3              | 16     | 128 GiB     | 5000            | 4997                 |
| E32s_v3              | 32     | 256 GiB     | 5000            | 4997                 |
| E48s_v3              | 48     | 384 GiB     | 5000            | 4997                 |
| E64s_v3              | 64     | 432 GiB     | 5000            | 4997                 |

Ha a kapcsolatok túllépik a korlátot, a következő hibaüzenet jelenhet meg:
> VÉGZETES: Sajnos túl sok ügyfél már

> [!IMPORTANT]
> A legjobb megoldás érdekében javasoljuk, hogy használjon olyan kapcsolati Pooler, mint a PgBouncer, hogy hatékonyan kezelhesse a kapcsolatokat.

A PostgreSQL-kapcsolatok, akár tétlenek is, körülbelül 10 MB memóriát foglalnak magukban. Emellett az új kapcsolatok létrehozása időt vesz igénybe. A legtöbb alkalmazás sok rövid életű kapcsolatot igényel, amely ezt a helyzetet összeképezi. Ennek eredményeképpen kevesebb erőforrás érhető el a tényleges munkaterheléshez, ami csökkenti a teljesítményt. A kapcsolati készletezéssel csökkentheti az üresjárati kapcsolatokat, és újból felhasználhatja a meglévő kapcsolatokat. További információért látogasson el a [blogbejegyzésbe](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funkcionális korlátozások

### <a name="scale-operations"></a>Skálázási műveletek

- A kiszolgáló tárterületének skálázásához a kiszolgáló újraindítása szükséges.
- A kiszolgáló tárterülete csak 2x-es növekményekben méretezhető, a részletekért lásd a [számítási és tárolási](concepts-compute-storage.md) adatokat.
- A kiszolgáló tárolási méretének csökkentése jelenleg nem támogatott.

### <a name="server-version-upgrades"></a>Kiszolgáló verziófrissítése

- A fő adatbázismotor-verziók közötti automatikus áttelepítés jelenleg nem támogatott. Ha a következő főverzióra szeretne frissíteni, hozzon létre egy [memóriaképet, és állítsa vissza](../howto-migrate-using-dump-and-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

### <a name="storage"></a>Tárolás

- A konfigurálást követően a tárterület mérete nem csökkenthető.
- Jelenleg a Storage automatikus növekedés funkciója nem érhető el. Figyelje a használatot, és növelje a tárterületet magasabb méretre. 
- Ha a tárterület-használat eléri a 95%-ot, vagy ha a rendelkezésre álló kapacitás kevesebb, mint 5 GiB, a kiszolgáló automatikusan **írásvédett módra** vált, így elkerülhetők a lemezek teljes helyzetével kapcsolatos hibák. 
- Javasoljuk, hogy a riasztási szabályokat állítsa be, `storage used` vagy `storage percent` ha azok túllépik az egyes küszöbértékeket, így proaktív módon végezhet műveleteket, például növelheti a tárterület méretét. Beállíthat például egy riasztást, ha a tárolási százalék meghaladja a 80%-os kihasználtságot.
  
### <a name="networking"></a>Hálózatkezelés

- A VNET-ben és a-ban való áthelyezés jelenleg nem támogatott.
- A nyilvános hozzáférésnek a VNET belüli üzembe helyezésével való kombinálása jelenleg nem támogatott.
- A tűzfalszabályok nem támogatottak a VNET, hanem hálózati biztonsági csoportokat is használhatnak.
- A nyilvános hozzáférésű adatbázis-kiszolgálók csatlakozhatnak a nyilvános internethez, például a szolgáltatáson keresztül `postgres_fdw` , és ez a hozzáférés nem korlátozható. A VNET-alapú kiszolgálók korlátozott kimenő hozzáférést biztosíthatnak a hálózati biztonsági csoportokkal.

### <a name="high-availability-ha"></a>Magas rendelkezésre állás (HA)

- Zone-Redundant HA jelenleg nem támogatott a betört kiszolgálók esetében.
- Az adatbázis-kiszolgáló IP-címe akkor változik meg, ha a kiszolgáló átadja a HA készenléti állapotot. Győződjön meg arról, hogy a kiszolgáló IP-címe helyett a DNS-rekordot használja.
- Ha a logikai replikáció egy HA konfigurált rugalmas kiszolgálóval van konfigurálva, a készenléti kiszolgáló feladatátvétele esetén a rendszer nem másolja át a logikai replikációs tárolóhelyeket a készenléti kiszolgálóra. 
- További információ a zónáról – redundáns HA a korlátozásokat is beleértve, tekintse meg a [fogalmak – ha dokumentációs](concepts-high-availability.md) oldalt.

### <a name="availability-zones"></a>Rendelkezésreállási zónák

- A kiszolgálók egy másik rendelkezésre állási zónába történő manuális áthelyezése jelenleg nem támogatott.
- A HA készenléti kiszolgáló rendelkezésre állási zónája nem állítható be manuálisan.

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Postgres-motor,-bővítmények és-PgBouncer

- A postgres 10 és régebbi verzió nem támogatott. Ha régebbi postgres-verziókat kíván használni, javasoljuk, hogy az [egyetlen kiszolgáló](../overview-single-server.md) használata lehetőséget.
- A bővítmények támogatása jelenleg a postgres- `contrib` bővítményekre korlátozódik.
- A beépített PgBouncer-alapú Pooler jelenleg nem érhető el a VNET vagy a feltört kiszolgálókon lévő adatbázis-kiszolgálókhoz.

### <a name="stopstart-operation"></a>Leállítás/indítás művelet

- A kiszolgáló nem állítható le több mint hét napja.

### <a name="scheduled-maintenance"></a>Ütemezett karbantartás

- Ha a karbantartási időszakot kevesebb, mint öt nappal a már tervezett frissítés előtt módosítja, akkor a frissítés nem lesz hatással. A módosítások csak a következő ütemezett karbantartással lépnek életbe.

### <a name="backing-up-a-server"></a>Kiszolgáló biztonsági mentése

- A biztonsági mentéseket a rendszer felügyeli, a biztonsági másolatok manuális futtatása jelenleg nem lehetséges. Ehelyett ajánlott használni `pg_dump` .
- A biztonsági mentések mindig pillanatkép-alapú teljes biztonsági mentések (nem differenciált biztonsági másolatok), ami valószínűleg magasabb biztonsági mentési tárhely-kihasználtságot eredményez. Vegye figyelembe, hogy a tranzakciónaplók (az előre írható naplók – WAL) elkülönítik a teljes/különbözeti biztonsági másolatokat, és folyamatosan archiválják őket.

### <a name="restoring-a-server"></a>Kiszolgáló visszaállítása

- Az időponthoz tartozó visszaállítási szolgáltatás használatakor az új kiszolgáló ugyanazzal a számítási és tárolási konfigurációval jön létre, mint a kiszolgáló.
- A VNET-alapú adatbázis-kiszolgálókat a rendszer a biztonsági másolatból történő visszaállításkor visszaállítja ugyanazon a VNET.
- A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályok szabályaival. A tűzfalszabályok külön kell létrehozni az új kiszolgálón.
- A törölt kiszolgáló visszaállítása nem támogatott.
- A régiók közötti visszaállítás nem támogatott.

### <a name="other-features"></a>Egyéb jellemzők

* Az Azure AD-hitelesítés még nem támogatott. Ha Azure AD-hitelesítést kíván használni, javasoljuk, hogy az [egyetlen kiszolgáló](../overview-single-server.md) használata lehetőséget használja.
* Az olvasási replikák még nem támogatottak. Ha olvasási replikára van szüksége, javasoljuk, hogy használja az [egyetlen kiszolgáló](../overview-single-server.md) lehetőséget.
* Az erőforrások másik előfizetésbe való áthelyezése nem támogatott. 


## <a name="next-steps"></a>Következő lépések

- [A számítási és tárolási lehetőségek elérhetővé tételének](concepts-compute-storage.md) ismertetése
- További információ a [PostgreSQL-adatbázisok támogatott verzióiról](concepts-supported-versions.md)
- Tekintse át [Azure Database for PostgreSQL kiszolgáló biztonsági mentését és visszaállítását a Azure Portal használatával](how-to-restore-server-portal.md)
