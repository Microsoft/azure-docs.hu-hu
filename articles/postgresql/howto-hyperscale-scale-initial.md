---
title: Kezdeti kiszolgálói csoport mérete – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Válassza ki a megfelelő kezdeti méretet a használati esethez
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8b87cfc8276d13ccc7e12a4901489ea0b1e770a5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012506"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Nagy kapacitású-(Citus-) kiszolgálócsoport kezdeti méretének kiválasztása

A kiszolgálócsoport mérete, a csomópontok száma és a hardver kapacitása [könnyen módosítható](howto-hyperscale-scale-grow.md). Azonban továbbra is ki kell választania egy új kiszolgálócsoport kezdeti méretét. Íme néhány tipp az ésszerű választáshoz.

## <a name="use-cases"></a>Használati esetek

A nagy kapacitású (Citus) gyakori használata a következő módokon történik.

### <a name="multi-tenant-saas"></a>Több-bérlős SaaS

Ha a nagy kapacitású (Citus) áttelepítést végez egy meglévő egycsomópontos PostgreSQL-adatbázisból, válasszon olyan fürtöt, amelyben a feldolgozói virtuális mag és a RAM mennyisége megegyezik az eredeti példány értékével. Ilyen helyzetekben 2 – 3 – 4 teljesítménybeli javulás látható, mivel a horizontális felskálázás javítja az erőforrások kihasználtságát, és lehetővé teszi a kisebb indexek használatát stb.

A virtuális mag száma valójában az egyetlen döntés. A RAM-foglalás jelenleg a virtuális mag száma alapján van meghatározva a [nagy kapacitású (Citus) konfigurációs beállítások](concepts-hyperscale-configuration-options.md) lapon leírtak szerint.
A koordinátori csomópont nem igényel annyi RAM-t, mint a feldolgozók, de nincs mód a RAM és a virtuális mag egymástól független kiválasztására.

### <a name="real-time-analytics"></a>Valós idejű elemzések

Teljes virtuális mag: Ha a munkamennyiség a RAM-ban található, akkor a nagy kapacitású (Citus) lineáris teljesítményének növelése várható a munkavégző magok számával arányosan. Az igényeinek megfelelő számú virtuális mag meghatározásához vegye figyelembe az egycsomópontos adatbázisban lévő lekérdezések aktuális késését, valamint a nagy kapacitású (Citus) szükséges késését. Ossza el az aktuális kését a kívánt késéssel, majd kerekítse az eredményt.

Munkavégző RAM: a legjobb esetben elegendő memóriát biztosítanak, amely a legtöbb munkakészletnek a memóriában elfér. Az alkalmazás által használt lekérdezések típusa befolyásolja a memóriakövetelményeket. Az ELEMZÉSek MAGYARÁZATának futtatásával határozhatja meg, hogy mennyi memóriát igényel. Ne feledje, hogy a virtuális mag és a RAM méretezése a [nagy kapacitású (Citus) konfigurációs beállításai](concepts-hyperscale-configuration-options.md) című cikkben leírtak szerint történik.

## <a name="choosing-a-hyperscale-citus-tier"></a>Nagy kapacitású-(Citus-) rétegek kiválasztása

> [!IMPORTANT]
> A nagy kapacitású (Citus) alapszintű csomag jelenleg előzetes verzióban érhető el.  Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> Megtekintheti a [nagy kapacitású (Citus) előzetes](hyperscale-preview-features.md)verziójának új funkcióinak teljes listáját.

A fenti részekben megtudhatja, hány virtuális mag és mennyi RAM szükséges az egyes használati esetekhez. Ezek az igények a két nagy kapacitású (Citus) szintjein keresztül is megadhatók: az alapszintű csomag és a standard szint között.

Az alapszintű csomag egyetlen adatbázis-csomópontot használ a feldolgozás végrehajtásához, míg a standard szint több csomópontot is engedélyez. A rétegek egyébként azonosak, és ugyanazokat a funkciókat kínálja. Bizonyos esetekben az egyetlen csomópont virtuális mag és lemezterülete elegendő, és más esetekben több csomópont együttműködésére is szükség van.

A rétegek összehasonlításával kapcsolatban tekintse meg az [alapszintű fogalmakat](concepts-hyperscale-tiers.md) ismertető oldalt.

## <a name="next-steps"></a>Következő lépések

- [Kiszolgálócsoport skálázása](howto-hyperscale-scale-grow.md)
- További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
