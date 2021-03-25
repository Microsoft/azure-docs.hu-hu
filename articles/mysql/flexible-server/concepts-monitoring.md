---
title: Monitorozás – Azure Database for MySQL – rugalmas kiszolgáló
description: Ez a cikk a Azure Database for MySQL rugalmas kiszolgáló figyelési és riasztási mérőszámait ismerteti, beleértve a CPU-t, a tárolást és a kapcsolatok statisztikáit.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 660e87e0c09d1bf052b20f60276a30e572ab825b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110474"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>Beépített metrikákkal rendelkező rugalmas kiszolgálók figyelése Azure Database for MySQL

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Azure Database for MySQL rugalmas kiszolgáló Azure Monitor használatával figyeli a kiszolgálók figyelését. A metrikák olyan numerikus értékek, amelyek egy adott időpontban ismertetik a kiszolgáló erőforrásainak bizonyos aspektusait. A kiszolgáló erőforrásainak figyelése segíti a számítási feladatok hibakeresését és optimalizálását azáltal, hogy lehetővé teszi, hogy a legtöbbet figyelje. A megfelelő mérőszámok figyelésével megőrizheti a kiszolgáló és az alkalmazások teljesítményét, megbízhatóságát és rendelkezésre állását.

Ebben a cikkben megismerheti a rugalmas kiszolgáló különböző mérőszámait, amelyek betekintést nyújtanak a kiszolgáló működésére.

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

Azure Database for MySQL rugalmas kiszolgáló különböző mérőszámokat biztosít a számítási feladatok végrehajtásához és az adatok alapján, megtudhatja, milyen hatással van a kiszolgálóra és az alkalmazásra. A rugalmas kiszolgálókon például figyelheti a **GAZDAGÉP CPU-százalékát**, az **aktív kapcsolatokat**, az **i/o százalékot** és a **gazdagép memória százalékát** , hogy azonosítsa a teljesítményre gyakorolt hatást. Előfordulhat, hogy optimalizálnia kell a munkaterhelést, méreteznie kell függőlegesen a számítási rétegek módosításával, vagy horizontálisan kell méreteznie az olvasási replika használatával.

Minden Azure-metrika egyperces gyakorisággal rendelkezik, és minden metrika 30 napos előzményt biztosít. A mérőszámokra vonatkozó riasztásokat is beállíthat. Részletes útmutatást a [riasztások beállítása](./how-to-alert-on-metric.md)című témakörben talál. Az egyéb feladatok közé tartozik az automatizált műveletek beállítása, a speciális elemzések végrehajtása és az archiválási előzmények. További információt az [Azure mérőszámok áttekintése](../../azure-monitor/data-platform.md)című témakörben talál.

### <a name="list-of-metrics"></a>Metrikák listája
Ezek a metrikák a Azure Database for MySQL számára érhetők el:

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Leírás|
|---|---|---|---|
|Gazda CPU-százaléka|cpu_percent|Százalék|A kiszolgálón a CPU-kihasználtság százalékos aránya, beleértve a processzor kihasználtságát és az Azure MySQL folyamatait is|
|Gazdagép hálózata |network_bytes_ingress|Bájt|Bejövő hálózati forgalom a kiszolgálón, beleértve az ügyfél-adatbázis és az Azure MySQL-funkciók (például replikálás, monitorozás, naplók stb.) forgalmát.|
|Gazdagép kimenő hálózati forgalom|network_bytes_egress|Bájt|A kiszolgáló kimenő hálózati forgalma, beleértve az ügyfél-adatbázis és az Azure MySQL-szolgáltatások (például replikálás, monitorozás, naplók stb.) forgalmát.|
|Replikációs késés|replication_lag|Másodperc|Az utolsó visszajátszott tranzakció óta eltelt idő. Ez a metrika csak replika kiszolgálók esetében érhető el.|
|Aktív kapcsolatok|active_connection|Darabszám|A kiszolgálóval létesített aktív kapcsolatok száma.|
|Felhasznált biztonsági mentési tár|backup_storage_used|Bájt|A felhasznált biztonsági mentési tár mennyisége.|
|IO-százalék|io_consumption_percent|Százalék|A használatban lévő IO százalékos aránya.|
|Gazdagép memória százaléka|memory_percent|Százalék|A kiszolgálón használt memória százalékos aránya, beleértve az ügyfél és az Azure MySQL folyamatainak kihasználtságát is|
|Tárolási korlát|storage_limit|Bájt|A kiszolgáló maximális tárterülete.|
|Tárolási százalék|storage_percent|Százalék|A kiszolgáló maximális száma által felhasznált tárterület százalékos aránya.|
|Felhasznált tárterület|storage_used|Bájt|A használatban lévő tárterület mennyisége. A szolgáltatás által használt tárterület magában foglalhatja az adatbázisfájlok, a tranzakciós naplók és a kiszolgáló naplófájljait is.|
|Összes kapcsolat|total_connections|Darabszám|A kiszolgálóval létesített kapcsolatok teljes száma|
|Megszakított kapcsolatok|aborted_connections|Darabszám|A MySQL-hez való kapcsolódás sikertelen próbálkozások száma, például hibás hitelesítő adatok miatt sikertelen volt a kapcsolat.|
|Lekérdezések|lekérdezések|Darabszám|A lekérdezések másodpercenkénti száma|

## <a name="next-steps"></a>Következő lépések
- A riasztások metrikai létrehozásával kapcsolatos útmutatást a riasztások [beállítása](./how-to-alert-on-metric.md) című témakörben tekintheti meg.
- További információ a [IOPS méretezéséről](./concepts/../concepts-compute-storage.md#iops) a teljesítmény növelése érdekében.