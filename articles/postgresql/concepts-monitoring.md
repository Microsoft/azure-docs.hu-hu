---
title: Figyelés és Finomhangolás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk az Azure Database for PostgreSQL – egyetlen kiszolgáló figyelési és hangolási funkcióit ismerteti.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 8c4479d0892a8a6d5f613eff4592cca93e9fb179
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605127"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL figyelése és finomhangolása – egyetlen kiszolgáló
A kiszolgálók figyelési adatai segítenek a számítási feladatok megoldásában és optimalizálásában. Azure Database for PostgreSQL különböző figyelési lehetőségeket biztosít a kiszolgáló működésének áttekintéséhez.

## <a name="metrics"></a>Mérőszámok
Azure Database for PostgreSQL különböző mérőszámokat biztosít, amelyek betekintést nyújtanak a PostgreSQL-kiszolgálót támogató erőforrások viselkedésére. Minden metrika egyperces gyakorisággal van kibocsátva, és akár [93 napig](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics)is eltarthat. A mérőszámokra vonatkozó riasztásokat is beállíthat. Részletes útmutatást a [riasztások beállítása](howto-alert-on-metric.md)című témakörben talál. Az egyéb feladatok közé tartozik az automatizált műveletek beállítása, a speciális elemzések végrehajtása és az archiválási előzmények. További információt az [Azure mérőszámok áttekintése](../azure-monitor/data-platform.md)című témakörben talál.

### <a name="list-of-metrics"></a>Metrikák listája
Ezek a metrikák a Azure Database for PostgreSQL számára érhetők el:

|Metric|Metrika megjelenítendő neve|Unit (Egység)|Leírás|
|---|---|---|---|
|cpu_percent|CPU-százalék|Százalék|A használatban lévő CPU százalékos aránya.|
|memory_percent|Memória százaléka|Százalék|A használatban lévő memória százalékos aránya.|
|io_consumption_percent|IO-százalék|Százalék|A használatban lévő IO százalékos aránya. (Nem alkalmazható az alapszintű kiszolgálók esetében.)|
|storage_percent|Tárolási százalék|Százalék|A kiszolgáló maximális száma által felhasznált tárterület százalékos aránya.|
|storage_used|Felhasznált tárterület|Bájt|A használatban lévő tárterület mennyisége. A szolgáltatás által használt tárterület magában foglalhatja az adatbázisfájlok, a tranzakciós naplók és a kiszolgáló naplófájljait is.|
|storage_limit|Tárolási korlát|Bájt|A kiszolgáló maximális tárterülete.|
|serverlog_storage_percent|Kiszolgáló naplójának tárolási százaléka|Százalék|A kiszolgáló naplófájl-tárolási helyének maximális tárterületének százalékos értéke.|
|serverlog_storage_usage|Kiszolgáló naplójának tárolója|Bájt|A kiszolgáló által használt log-tároló mennyisége.|
|serverlog_storage_limit|Kiszolgáló naplójának tárolási korlátja|Bájt|A kiszolgáló maximális kiszolgálói naplózási tárterülete.|
|active_connections|Aktív kapcsolatok|Darabszám|A kiszolgálóval létesített aktív kapcsolatok száma.|
|connections_failed|Sikertelen kapcsolatok|Darabszám|A sikertelenül létesített kapcsolatok száma.|
|network_bytes_egress|Kimenő hálózat|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|network_bytes_ingress|Bejövő hálózat|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|backup_storage_used|Felhasznált biztonsági mentési tár|Bájt|A felhasznált biztonsági mentési tár mennyisége. Ez a metrika a teljes adatbázis biztonsági mentése, a különbözeti biztonsági másolatok és a naplózott biztonsági mentések által felhasznált tárterület összegét jelöli, amelyet a kiszolgáló számára beállított biztonsági másolatok megőrzési időtartama alapján tartanak fenn. A biztonsági mentések gyakorisága a szolgáltatás által felügyelt és a [fogalmakat ismertető cikkben](concepts-backup.md)olvasható. A földrajzilag redundáns tároláshoz a biztonsági mentési tárterület a helyileg redundáns tárolásnál kétszer szerepel.|
|pg_replica_log_delay_in_bytes|Replikák maximális késése|Bájt|Az elsődleges és a legnagyobb késleltetésű replika közötti késés bájtban. Ez a metrika csak az elsődleges kiszolgálón érhető el.|
|pg_replica_log_delay_in_seconds|Replika késése|Másodperc|Az utolsó visszajátszott tranzakció óta eltelt idő. Ez a metrika csak replika kiszolgálók esetében érhető el.|

## <a name="server-logs"></a>Kiszolgálói naplók
Engedélyezheti a naplózást a kiszolgálón. Ezeket az erőforrás-naplókat [Azure monitor naplókba](../azure-monitor/logs/log-query-overview.md), Event Hubsba és egy Storage-fiókba lehet elküldeni. Ha többet szeretne megtudni a naplózásról, látogasson el a [kiszolgálói naplók](concepts-server-logs.md) oldalra.

## <a name="query-store"></a>Lekérdezéstár
A [lekérdezési tár](concepts-query-store.md) nyomon követi a lekérdezési teljesítményt az idő múlásával, beleértve a lekérdezési futtatókörnyezet statisztikáit és a várakozási eseményeket. A szolgáltatás egy **azure_sys** nevű rendszeradatbázisban tartja fenn a lekérdezési futtatókörnyezet teljesítményadatait a query_store sémában. Az adatgyűjtést és-tárolást különböző konfigurációs gombokon keresztül szabályozhatja.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző
A [lekérdezési terheléselemző](concepts-query-performance-insight.md) a lekérdezési tárolóval együtt a Azure Portal elérhető vizualizációk biztosítására is használható. Ezek a diagramok lehetővé teszik a teljesítményre gyakorolt legfontosabb lekérdezések azonosítását. A Lekérdezési terheléselemző a Azure Database for PostgreSQL-kiszolgáló portál lapjának **támogatás + hibaelhárítás** szakaszában érhető el.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [teljesítményre vonatkozó javaslatok](concepts-performance-recommendations.md) funkció a munkaterhelés teljesítményének növelésére szolgáló lehetőségeket azonosítja. A teljesítménnyel kapcsolatos javaslatok olyan új indexek létrehozásához nyújtanak javaslatokat, amelyek képesek a számítási feladatok teljesítményének javítására. Az indexelési javaslatok előállításához a funkció figyelembe veszi a különböző adatbázis-jellemzőket, beleértve annak sémáját és a lekérdezési tároló által jelentett munkaterhelést. A teljesítményre vonatkozó javaslat bevezetését követően az ügyfeleknek tesztelni kell a teljesítményt a változások hatásának kiértékeléséhez. 

## <a name="planned-maintenance-notification"></a>Tervezett karbantartási értesítés

A [tervezett karbantartási értesítések](./concepts-planned-maintenance-notification.md) lehetővé teszik, hogy riasztásokat kapjon a közelgő tervezett karbantartásról a Azure Database for PostgreSQL-egyetlen kiszolgálóra. Ezek az értesítések a [Service Health](../service-health/overview.md) tervezett karbantartásával vannak integrálva, és lehetővé teszik az előfizetések összes ütemezett karbantartásának megtekintését egy helyen. Emellett segít az értesítések méretezésében a megfelelő célközönségek számára a különböző erőforráscsoportok esetében, mivel előfordulhat, hogy a különböző kapcsolattartók különböző erőforrásokért felelősek. A közelgő karbantartási 72 órával az esemény előtt értesítést fog kapni.

További információ az értesítések beállításáról a [tervezett karbantartási értesítések](./concepts-planned-maintenance-notification.md) dokumentumban.

## <a name="next-steps"></a>Következő lépések
- A riasztások metrikai létrehozásával kapcsolatos útmutatást a riasztások [beállítása](howto-alert-on-metric.md) című témakörben tekintheti meg.
- A metrikák Azure Portal, REST API vagy parancssori felülettel való eléréséről és exportálásáról további információt az [Azure-metrikák áttekintése](../azure-monitor/data-platform.md) című témakörben talál.
- A [kiszolgáló figyelésére vonatkozó ajánlott eljárásokért](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)olvassa el a blogot.
- További információ a [tervezett karbantartási értesítésekről](./concepts-planned-maintenance-notification.md) Azure Database for PostgreSQL-egyetlen kiszolgálón.