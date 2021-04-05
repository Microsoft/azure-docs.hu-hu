---
title: Kezdeti kiszolgálói csoport mérete – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Válassza ki a megfelelő kezdeti méretet a használati esethez
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026403"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Nagy kapacitású-(Citus-) kiszolgálócsoport kezdeti méretének kiválasztása

A kiszolgálócsoport mérete, a csomópontok száma és a hardver kapacitása is [könnyen módosítható](howto-hyperscale-scale-grow.md). Azonban továbbra is ki kell választania egy új kiszolgálócsoport kezdeti méretét. Íme néhány tipp az ésszerű választáshoz.

## <a name="multi-tenant-saas-use-case"></a>Több-bérlős SaaS-használati eset

Ha a nagy kapacitású (Citus) áttelepítést végez egy meglévő egycsomópontos PostgreSQL-adatbázisból, válasszon olyan fürtöt, amelyben a feldolgozói virtuális mag és a RAM mennyisége megegyezik az eredeti példány értékével. Ilyen helyzetekben 2 – 3 – 4 teljesítménybeli javulás látható, mivel a horizontális felskálázás javítja az erőforrások kihasználtságát, és lehetővé teszi a kisebb indexek használatát stb.

A virtuális mag száma valójában az egyetlen döntés. A RAM-foglalás jelenleg a virtuális mag száma alapján van meghatározva a [nagy kapacitású (Citus) konfigurációs beállítások](concepts-hyperscale-configuration-options.md) lapon leírtak szerint.
A koordinátori csomópont nem igényel annyi RAM-t, mint a feldolgozók, de nincs mód a RAM és a virtuális mag egymástól független kiválasztására.

## <a name="real-time-analytics-use-case"></a>Valós idejű elemzési használati eset

Teljes virtuális mag: Ha a munkamennyiség a RAM-ban található, akkor a nagy kapacitású (Citus) lineáris teljesítményének növelése várható a munkavégző magok számával arányosan. Az igényeinek megfelelő számú virtuális mag meghatározásához vegye figyelembe az egycsomópontos adatbázisban lévő lekérdezések aktuális késését, valamint a nagy kapacitású (Citus) szükséges késését. Ossza el az aktuális kését a kívánt késéssel, majd kerekítse az eredményt.

Feldolgozó RAM: a legjobb eset elegendő memória biztosítása lenne, amelyben a munkakészlet többsége elfér. Az alkalmazás által használt lekérdezések típusa befolyásolja a memóriakövetelményeket. Az ELEMZÉSek MAGYARÁZATának futtatásával határozhatja meg, hogy mennyi memóriát igényel. Ne feledje, hogy a virtuális mag és a RAM méretezése a [nagy kapacitású (Citus) konfigurációs beállításai](concepts-hyperscale-configuration-options.md) című cikkben leírtak szerint történik.

## <a name="next-steps"></a>Következő lépések

- [Kiszolgálócsoport skálázása](howto-hyperscale-scale-grow.md)
- További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
