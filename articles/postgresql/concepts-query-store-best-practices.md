---
title: Az ajánlott eljárások lekérdezése Azure Database for PostgreSQL – egyetlen kiszolgálón
description: Ez a cikk a Azure Database for PostgreSQL egyetlen kiszolgálón található lekérdezési tár ajánlott eljárásait ismerteti.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: dd39b7ecd51902f5035b4cd17d59dea964d0c962
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91708832"
---
# <a name="best-practices-for-query-store"></a>Ajánlott eljárások a lekérdezési tárolóhoz

**A következőkre vonatkozik:** Azure Database for PostgreSQL – egykiszolgálós verzió: 9,6, 10, 11

Ez a cikk a Query Store Azure Database for PostgreSQL-ban való használatának ajánlott eljárásait ismerteti.

## <a name="set-the-optimal-query-capture-mode"></a>Az optimális lekérdezés-rögzítési mód beállítása
Lehetővé teszi a lekérdezési tároló számára az Ön számára fontos információk rögzítését. 

|**pg_qs pg_qs.query_capture_mode** | **Forgatókönyv**|
|---|---|
|_Mind_  |Alaposan elemezheti a munkaterhelést az összes lekérdezés és a végrehajtási gyakoriságuk és egyéb statisztikáik tekintetében. Azonosítsa a számítási feladatok új lekérdezéseit. Annak észlelése, hogy az alkalmi lekérdezések a felhasználók vagy az automatikus paraméterezés lehetőségeinek azonosítására szolgálnak-e. Az _összes_ erőforrás-felhasználás megnövelt erőforrás-használati díjat tartalmaz. |
|_Top_  |A legfontosabb lekérdezésekre koncentrálhat – ezek az ügyfelek által kiállítottak.
|_Nincs_ |Már rögzített egy lekérdezési készletet és egy időablakot, amelyet meg szeretne vizsgálni, és el szeretné távolítani a más lekérdezések által esetlegesen bevezethető zavaró tényezőket. A _nincs_ megfelelő tesztelési és pad-jelölési környezetekhez. _Nincs_ szükség körültekintően, mivel előfordulhat, hogy a fontos új lekérdezések nyomon követésére és optimalizálására lehetősége van. A korábbi időpontokban nem állíthatók helyre adatok. |

A lekérdezési tároló a várakozási statisztikák tárolására is tartalmaz egy tárolót. Van egy további, a várakozási statisztikát szabályozó rögzítési mód lekérdezése: a **pgms_wait_sampling. query_capture_mode** lehet a _none_ vagy _az All_ értékre állítva. 

> [!NOTE] 
> a **pg_qs. query_capture_mode** felülírja a **pgms_wait_sampling. query_capture_mode**. Ha a pg_qs. query_capture_mode értéke _nincs, a_ pgms_wait_sampling. query_capture_mode beállításnak nincs hatása. 


## <a name="keep-the-data-you-need"></a>Tartsa meg a szükséges adatgyűjtést
A **pg_qs. retention_period_in_days** paraméter a lekérdezési tároló adatmegőrzési időszakát napokban adja meg. A rendszer törli a régebbi lekérdezési és statisztikai adatokat. Alapértelmezés szerint a Query Store úgy van konfigurálva, hogy 7 napig őrizze meg az adattárolást. Kerülje a nem használt korábbi adatszolgáltatások megőrzését. Ha továbbra is meg kell őriznie az adatmennyiséget, növelje az értéket.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>A várakozási statisztika mintavételezési gyakoriságának beállítása 
A **pgms_wait_sampling. history_period** paraméter azt határozza meg, hogy milyen gyakran (ezredmásodpercben) várakozik az események. Minél rövidebb az időszak, annál gyakoribb a mintavételezés. A rendszer további információkat kér le, de az erőforrás-felhasználás nagyobb költségeket is tartalmaz. Növelje ezt az időszakot, ha a kiszolgáló terhelés alatt van, vagy nincs szüksége a részletességre


## <a name="get-quick-insights-into-query-store"></a>Gyors betekintést kaphat a lekérdezési tárolóba
A Azure Portal [lekérdezési terheléselemző](concepts-query-performance-insight.md) használatával gyorsan betekintést nyerhet a lekérdezési tárolóban található adatelemzésbe. A vizualizációk felveszik a leghosszabb ideig futó lekérdezéseket és a leghosszabb várakozási eseményeket.

## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan kérhet le vagy állíthat be paramétereket a [Azure Portal](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI](howto-configure-server-parameters-using-cli.md)használatával.