---
title: Ajánlott eljárások Query Store az Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk ismerteti a gyakorlati tanácsok a Query Store az Azure Database for PostgreSQL – egyetlen kiszolgáló.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 798a7a3edbf11c8421848871d26ba55b5bada0b6
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067230"
---
# <a name="best-practices-for-query-store"></a>Ajánlott eljárások a Query Store

**A következőkre vonatkozik:** Azure Database for PostgreSQL – egyetlen kiszolgáló 9.6 és 10

Ez a cikk ismerteti az ajánlott eljárások a Query Store az Azure Database for postgresql-hez.

## <a name="set-the-optimal-query-capture-mode"></a>Az optimális lekérdezési rögzítési üzemmód beállítása
Lehetővé teszik a Query Store rögzíteni az adatokat, az Ön számára fontos. 

|**pg_qs.query_capture_mode** | **Forgatókönyv**|
|---|---|
|_Az összes_  |A számítási feladatok alaposan tekintetében az összes lekérdezés és a végrehajtás gyakoriságát és más statisztikai elemzése. A számítási feladatok az új lekérdezések azonosítása. Észlelés, ha ad hoc lekérdezéseket lehetőségek a felhasználókkal vagy automatikus paraméterezés azonosítására szolgál. _Az összes_ egy nagyobb erőforrás-használat költség is tartalmaz. |
|_felső_  |A leggyakoribb lekérdezések – ezek az ügyfelek által kibocsátott figyelmet összpontosítanak.
|_Nincsenek_ |Korábban már rögzített egy lekérdezési készlet, és ez alatt az időszak, amely a vizsgálni kívánt, és szeretné, hogy más lekérdezések vezethet be a felesleges kiküszöbölése. _Nincs_ egy megfelelő tesztelési és a teljesítménymérés környezetekben. _Nincs_ kell legyen óvatos, előfordulhat, hogy kihagyná nyomon követésére, és fontos új lekérdezések optimalizálása lehetőséget. Adott múltbeli idő Windows adatok nem állíthatók vissza. |

Query Store áruházbeli várakozási statistics is tartalmaz. Egy további rögzítési mód lekérdezés, amely szabályozza a várakozási statisztika: **pgms_wait_sampling.query_capture_mode** állítható _nincs_ vagy _összes_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** felülír **pgms_wait_sampling.query_capture_mode**. Ha a pg_qs.query_capture_mode _none_, a pgms_wait_sampling.query_capture_mode beállítás nem befolyásolja. 


## <a name="keep-the-data-you-need"></a>A szükséges adatok megtartása
A **pg_qs.retention_period_in_days** paraméter adja meg a Query Store az Adatmegőrzés időtartama napokban. Régebbi lekérdezési és statisztikai adat törlődik. Alapértelmezés szerint a Query Store az adatok megőrzése 7 napig van konfigurálva. Elkerülése érdekében, hogy nem szeretné használni korábbi adatok megőrzése mellett. Növelje az értéket, ha szeretné megőrizni az adatokat hosszabb.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Állítsa be a várakozási statisztikái mintavétel gyakorisága 
A **pgms_wait_sampling.history_period** paraméter gyakoriságát határozza meg (ezredmásodpercben) várakozási esemény történik az adatgyűjtés. Minél rövidebb az időszak, annál a mintavételezés gyakran használják. További információt a rendszer lekéri, de a nagyobb erőforrás-használat költségeit, amely tartalmaz. Növelje az ebben az időszakban, ha a kiszolgáló terhelés alatt van, vagy nincs szükség a granularitási


## <a name="get-quick-insights-into-query-store"></a>Gyors elemzéseket kaphat a Query Store
Használhat [lekérdezési Terheléselemző](concepts-query-performance-insight.md) gyors elemzéseket kaphat az adatok beolvasása a Query Store az Azure Portalon. A Vizualizációk a leghosszabb futó lekérdezések tervezőfelületére, és a leghosszabb várjon az események idővel.

## <a name="next-steps"></a>További lépések
- Ismerje meg, vagy állítsa be a paraméterek használatával a [az Azure portal](howto-configure-server-parameters-using-portal.md) vagy a [Azure CLI-vel](howto-configure-server-parameters-using-cli.md).