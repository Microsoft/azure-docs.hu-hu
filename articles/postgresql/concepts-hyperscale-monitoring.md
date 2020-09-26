---
title: Monitorozás és Finomhangolás – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ez a cikk a Azure Database for PostgreSQL-nagy kapacitású (Citus) figyelési és hangolási funkcióit ismerteti
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9a6d8843acf27a53e261d9c7180906b15d05cc33
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314810"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL figyelése és finomhangolása – nagy kapacitású (Citus)

A kiszolgálók figyelési adatai segítenek a számítási feladatok megoldásában és optimalizálásában. A nagy kapacitású (Citus) különböző figyelési lehetőségeket biztosít, amelyek betekintést nyújtanak a kiszolgálói csoportok csomópontjainak viselkedésére.

## <a name="metrics"></a>Mérőszámok

A nagy kapacitású (Citus) metrikákat biztosít a kiszolgálócsoport egyes csomópontjaihoz. A metrikák betekintést nyújtanak a támogatási erőforrások viselkedésére. Minden metrika egy egyperces gyakorisággal van kibocsátva, és akár 30 napig is eltarthat.

A metrikák diagramjainak megtekintése mellett beállíthatja a riasztásokat is. Részletes útmutatást a [riasztások beállítása](howto-hyperscale-alert-on-metric.md)című témakörben talál.  Az egyéb feladatok közé tartozik az automatizált műveletek beállítása, a speciális elemzések futtatása és az archiválási előzmények. További információt az [Azure mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)című témakörben talál.

### <a name="list-of-metrics"></a>Metrikák listája

Ezek a metrikák a nagy kapacitású-(Citus-) csomópontokhoz érhetők el:

|Metrika|Metrika megjelenítendő neve|Egység|Description|
|---|---|---|---|
|active_connections|Aktív kapcsolatok|Darabszám|A kiszolgálóval létesített aktív kapcsolatok száma.|
|cpu_percent|CPU-százalék|Százalék|A használatban lévő CPU százalékos aránya.|
|IOPS|IOPS|Darabszám|Lásd a [IOPS-definíció](../virtual-machines/premium-storage-performance.md#iops) és a [nagy kapacitású (Citus) átviteli sebességét.](concepts-hyperscale-configuration-options.md)|
|memory_percent|Memória százaléka|Százalék|A használatban lévő memória százalékos aránya.|
|network_bytes_ingress|Bejövő hálózat|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|network_bytes_egress|Kimenő hálózat|Bájt|A hálózat aktív kapcsolatokon keresztül.|
|storage_percent|Tárolási százalék|Százalék|A kiszolgáló maximális száma által felhasznált tárterület százalékos aránya.|
|storage_used|Felhasznált tárterület|Bájt|A használatban lévő tárterület mennyisége. A szolgáltatás által használt tárterület magában foglalhatja az adatbázisfájlok, a tranzakciós naplók és a kiszolgáló naplófájljait is.|

Az Azure nem biztosít összesített mérőszámokat a fürt egészére vonatkozóan, de a több csomópont metrikái ugyanazon a gráfon helyezhetők el.

## <a name="next-steps"></a>Következő lépések

- A riasztások metrikai létrehozásával kapcsolatos útmutatást a riasztások [beállítása](howto-hyperscale-alert-on-metric.md) című témakörben tekintheti meg.
