---
title: Monitorozás és mérőszámok – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL rugalmas kiszolgáló figyelési és mérőszám-funkcióit ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 7d1d7f9759fc8e2826da6232981eb3a389576c07
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935029"
---
# <a name="monitor-metrics-azure-database-for-postgresql---flexible-server"></a>Metrikák figyelése Azure Database for PostgreSQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

A kiszolgálók figyelési adatai segítenek a számítási feladatok megoldásában és optimalizálásában. Azure Database for PostgreSQL különböző figyelési lehetőségeket biztosít a kiszolgáló működésének áttekintéséhez.

## <a name="metrics"></a>Mérőszámok
Azure Database for PostgreSQL különböző mérőszámokat biztosít, amelyek betekintést nyújtanak a PostgreSQL-kiszolgálót támogató erőforrások viselkedésére. Minden metrika egyperces gyakorisággal van kibocsátva, és akár [93 napig](../../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics)is eltarthat. A mérőszámokra vonatkozó riasztásokat is beállíthat. A további lehetőségek közé tartozik az automatizált műveletek beállítása, a speciális elemzések végrehajtása és az archiválási előzmények. További információt az [Azure mérőszámok áttekintése](../../azure-monitor/platform/data-platform-metrics.md)című témakörben talál.

### <a name="list-of-metrics"></a>Metrikák listája
A következő mérőszámok érhetők el a PostgreSQL rugalmas kiszolgálóhoz:


|Metrika|Metrika megjelenítendő neve|Egység|Leírás|
|---|---|---|---|
| backup_storage_used | Felhasznált biztonsági mentési tár | Bájt | A felhasznált biztonsági mentési tár mennyisége. Ez a metrika a teljes adatbázis biztonsági mentése, a különbözeti biztonsági másolatok és a naplózott biztonsági mentések által felhasznált tárterület összegét jelöli, amelyet a kiszolgáló számára beállított biztonsági másolatok megőrzési időtartama alapján tartanak fenn. A biztonsági mentések gyakorisága a szolgáltatás által felügyelt. A földrajzilag redundáns tároláshoz a biztonsági mentési tárterület a helyileg redundáns tárolásnál kétszer szerepel. |
| connections_failed | Sikertelen kapcsolatok | Darabszám | Sikertelen kapcsolatok. |
| connections_succeeded | Sikeres kapcsolatok | Darabszám | Sikeres kapcsolatok. |
| cpu_credits_consumed | Felhasznált CPU-kreditek | Darabszám | A rugalmas kiszolgáló által használt kreditek száma. A feltört szintre alkalmazható. |
| cpu_credits_remaining | Fennmaradó CPU-kreditek | Darabszám | A feltörésre rendelkezésre álló kreditek száma. A feltört szintre alkalmazható. |
| cpu_percent | CPU-százalék | Százalék | A használatban lévő CPU százalékos aránya. | 
| disk_queue_depth | Lemez várólistájának mélysége | Darabszám | Az adatlemezre vonatkozó függőben lévő I/O-műveletek száma. |
| IOPS | IOPS | Darabszám | A lemez másodpercenkénti I/O-műveleteinek száma. |
| maximum_used_transactionIDs | Maximálisan használt tranzakciós azonosítók | Darabszám | A tranzakciós azonosító maximális száma használatban. |
| memory_percent | Memória százaléka | Százalék | A használatban lévő memória százalékos aránya. |
| network_bytes_egress | Kimenő hálózat | Bájt | A kimenő hálózati forgalom mennyisége. |
| network_bytes_ingress | Bejövő hálózat | Bájt | A bejövő hálózati forgalom mennyisége. |
| read_iops | IOPS olvasása | Darabszám | Az adatlemez I/O-olvasási műveleteinek másodpercenkénti száma |
| read_throughput | Olvasási sebesség | Bájt | Lemezről olvasott bájtok másodpercenkénti száma |
| storage_free | Szabad tárterület | Bájt | A rendelkezésre álló tárterület mennyisége. |
| storage_percent | Tárolási százalék | Százalék | A felhasznált tárterület százalékos aránya. A szolgáltatás által használt tárterület magában foglalhatja az adatbázisfájlok, a tranzakciós naplók és a kiszolgáló naplófájljait is.|
| storage_used | Felhasznált tárterület | Bájt | A felhasznált tárterület százalékos aránya. A szolgáltatás által használt tárterület magában foglalhatja az adatbázisfájlok, a tranzakciós naplók és a kiszolgáló naplófájljait is. |
| txlogs_storage_used | A tranzakciós napló tárterülete használatban van | Bájt | A tranzakciónaplók által felhasznált tárterület mennyisége. | 
| write_throughput | Írási sebesség | Bájt | A lemezre írt bájtok másodpercenkénti száma. |
| write_iops | IOPS írása | Darabszám | Az adatlemez I/O-írási műveleteinek másodpercenkénti száma. |

## <a name="server-logs"></a>Kiszolgálói naplók
Azure Database for PostgreSQL lehetővé teszi a postgres standard szintű naplók konfigurálását és elérését. Ha többet szeretne megtudni a naplókról, látogasson el a [naplózási fogalmakat ismertető dokumentumra](concepts-logging.md).
