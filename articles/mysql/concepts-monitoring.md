---
title: Figyelés – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL figyelésére és riasztására vonatkozó mérőszámokat ismerteti, beleértve a CPU-t, a tárolást és a kapcsolatok statisztikáit.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 8d6aa4de7c3b75747c6e2f103e66e39dfa288ba7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576253"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Figyelés Azure Database for MySQL
A kiszolgálók figyelési adatai segítenek a számítási feladatok megoldásában és optimalizálásában. A Azure Database for MySQL különböző mérőszámokat biztosít, amelyek betekintést nyújtanak a kiszolgáló működésére.

## <a name="metrics"></a>Mérőszámok
Minden Azure-metrika egyperces gyakorisággal rendelkezik, és minden metrika 30 napos előzményt biztosít. A mérőszámokra vonatkozó riasztásokat is beállíthat. Részletes útmutatást a [riasztások beállítása](howto-alert-on-metric.md)című témakörben talál. Az egyéb feladatok közé tartozik az automatizált műveletek beállítása, a speciális elemzések végrehajtása és az archiválási előzmények. További információt az [Azure mérőszámok áttekintése](../azure-monitor/data-platform.md)című témakörben talál.

### <a name="list-of-metrics"></a>Metrikák listája
Ezek a metrikák a Azure Database for MySQL számára érhetők el:

|Metric|Metrika megjelenítendő neve|Unit (Egység)|Description|
|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|A használatban lévő CPU százalékos aránya.|
|memory_percent|Memória százaléka|Százalék|A használatban lévő memória százalékos aránya.|
|io_consumption_percent|IO-százalék|Százalék|A használatban lévő IO százalékos aránya. (Nem alkalmazható az alapszintű kiszolgálók esetében)|
|storage_percent|Tárolási százalék|Százalék|A kiszolgáló maximális száma által felhasznált tárterület százalékos aránya.|
|storage_used|Felhasznált tárterület|Bájt|A használatban lévő tárterület mennyisége. A szolgáltatás által használt tárterület magában foglalhatja az adatbázisfájlok, a tranzakciós naplók és a kiszolgáló naplófájljait is.|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|A kiszolgáló naplófájl-tárolási helyének maximális tárterületének százalékos értéke.|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|A kiszolgáló által használt log-tároló mennyisége.|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|A kiszolgáló maximális kiszolgálói naplózási tárterülete.|
|storage_limit|Tárolási korlát|Bájt|A kiszolgáló maximális tárterülete.|
|active_connections|Aktív kapcsolatok|Darabszám|A kiszolgálóval létesített aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Darabszám|A kiszolgálóhoz való sikertelen kapcsolódások száma.|
|seconds_behind_master|Replikálás késése másodpercben|Darabszám|Azon másodpercek száma, ameddig a másodpéldány-kiszolgáló lemarad a forráskiszolgálón. (Nem alkalmazható az alapszintű kiszolgálók esetében)|
|network_bytes_egress|Kimenő hálózat|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|network_bytes_ingress|Bejövő hálózat|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|A felhasznált biztonsági mentési tár mennyisége. Ez a metrika a teljes adatbázis biztonsági mentése, a különbözeti biztonsági másolatok és a naplózott biztonsági mentések által felhasznált tárterület összegét jelöli, amelyet a kiszolgáló számára beállított biztonsági másolatok megőrzési időtartama alapján tartanak fenn. A biztonsági mentések gyakorisága a szolgáltatás által felügyelt és a [fogalmakat ismertető cikkben](concepts-backup.md)olvasható. A földrajzilag redundáns tároláshoz a biztonsági mentési tárterület a helyileg redundáns tárolásnál kétszer szerepel.|

## <a name="server-logs"></a>Kiszolgálói naplók
Engedélyezheti a lassú lekérdezést és a naplózást a kiszolgálón. Ezek a naplók Azure Monitor naplók, Event Hubs és Storage-fiók Azure diagnosztikai naplóiban is elérhetők. Ha többet szeretne megtudni a naplózásról, látogasson el a [naplók](concepts-audit-logs.md) és a [lassú lekérdezések naplóinak](concepts-server-logs.md) cikkeibe.

## <a name="query-store"></a>Lekérdezéstár
A [lekérdezési tároló](concepts-query-store.md) egy olyan szolgáltatás, amely nyomon követi a lekérdezési teljesítményt az idő múlásával, beleértve a lekérdezési futtatókörnyezet statisztikáit és a várakozási eseményeket. A szolgáltatás megtartja a lekérdezés futásidejű teljesítményére vonatkozó információkat a **MySQL** -sémában. Az adatgyűjtést és-tárolást különböző konfigurációs gombokon keresztül szabályozhatja.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző
A [lekérdezési terheléselemző](concepts-query-performance-insight.md) a lekérdezési tárolóval együtt a Azure Portal elérhető vizualizációk biztosítására is használható. Ezek a diagramok lehetővé teszik a teljesítményre gyakorolt legfontosabb lekérdezések azonosítását. A Lekérdezési terheléselemző a Azure Database for MySQL-kiszolgáló portál lapjának **intelligens teljesítmény** szakaszában érhető el.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [teljesítményre vonatkozó javaslatok](concepts-performance-recommendations.md) funkció a munkaterhelés teljesítményének növelésére szolgáló lehetőségeket azonosítja. A teljesítménnyel kapcsolatos javaslatok olyan új indexek létrehozásához nyújtanak javaslatokat, amelyek képesek a számítási feladatok teljesítményének javítására. Az indexelési javaslatok előállításához a funkció figyelembe veszi a különböző adatbázis-jellemzőket, beleértve annak sémáját és a lekérdezési tároló által jelentett munkaterhelést. A teljesítményre vonatkozó javaslat bevezetését követően az ügyfeleknek tesztelni kell a teljesítményt a változások hatásának kiértékeléséhez.

## <a name="planned-maintenance-notification"></a>Tervezett karbantartási értesítés

A [tervezett karbantartási értesítések](./concepts-planned-maintenance-notification.md) lehetővé teszik, hogy riasztásokat kapjon a Azure Database for MySQL közelgő tervezett karbantartásáról. Ezek az értesítések a [Service Health](../service-health/overview.md) tervezett karbantartásával vannak integrálva, és lehetővé teszik az előfizetések összes ütemezett karbantartásának megtekintését egy helyen. Emellett segít az értesítések méretezésében a megfelelő célközönségek számára a különböző erőforráscsoportok esetében, mivel előfordulhat, hogy a különböző kapcsolattartók különböző erőforrásokért felelősek. A közelgő karbantartási 72 órával az esemény előtt értesítést fog kapni.

További információ az értesítések beállításáról a [tervezett karbantartási értesítések](./concepts-planned-maintenance-notification.md) dokumentumban.

## <a name="next-steps"></a>Következő lépések
- A riasztások metrikai létrehozásával kapcsolatos útmutatást a riasztások [beállítása](howto-alert-on-metric.md) című témakörben tekintheti meg.
- A metrikák Azure Portal, REST API vagy parancssori felülettel való eléréséről és exportálásáról további információt az [Azure mérőszámok áttekintése](../azure-monitor/data-platform.md)című témakörben talál.
- A [kiszolgáló figyelésére vonatkozó ajánlott eljárásokért](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/)olvassa el a blogot.
- További információ a [tervezett karbantartási értesítésekről](./concepts-planned-maintenance-notification.md) Azure Database for MySQL – egyetlen kiszolgálón