---
title: Ajánlott eljárások monitorozása – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL monitorozásának ajánlott eljárásait ismerteti.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96354988"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Ajánlott eljárások a Azure Database for MySQL figyeléséhez – egyetlen kiszolgáló

Ismerje meg az adatbázis-műveletek monitorozásához használható ajánlott eljárásokat, és győződjön meg arról, hogy a teljesítmény nem sérül, mivel az adatok mérete növekszik. Ahogy új képességeket adunk a platformhoz, továbbra is pontosítjuk az ebben a szakaszban részletezett ajánlott eljárásokat.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Az aktuális figyelési eszközkészlet elrendezése

A Azure Database for MySQL olyan eszközöket és metódusokat biztosít, amelyekkel könnyen figyelheti a használatot, hozzáadhat vagy eltávolíthat erőforrásokat (például CPU-t, memóriát vagy I/O-t), elháríthatja a lehetséges problémákat, és javíthatja az adatbázisok teljesítményét. A [teljesítmény-mérőszámokat rendszeresen figyelheti](concepts-monitoring.md#metrics) a különböző időtartományok átlagos, maximális és minimális értékeinek megtekintéséhez.

[Riasztásokat állíthat be](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) egy metrikai küszöbértékhez, így tájékoztathatja, ha a kiszolgáló elérte ezeket a korlátokat, és megfelelő műveleteket hajt végre.  

Figyelje az adatbázis-kiszolgálót, és győződjön meg arról, hogy az adatbázishoz rendelt erőforrások kezelhetik az alkalmazás munkaterhelését. Ha az adatbázis eléri az erőforrás-korlátozásokat, vegye figyelembe a következőket:
    * A legfontosabb erőforrás-fogyasztási lekérdezések azonosítása és optimalizálása. 
    * További erőforrások hozzáadása a szolgáltatási szintek frissítésével.

### <a name="cpu-utilization"></a>Processzorhasználat
A CPU-használat figyelése, és ha az adatbázis kimeríti a CPU-erőforrásokat. Ha a CPU-használat 90% vagy több, mint amennyire a számítási kapacitást növelni szeretné, növelje a virtuális mag számát, vagy a méretezést a következő árképzési szinten.  Győződjön meg arról, hogy az adatátviteli sebesség vagy a párhuzamosság a várt módon történik a CPU skálázása során. 

### <a name="memory"></a>Memória 
Az adatbázis-kiszolgáló számára elérhető memória mennyisége arányos a [virtuális mag számával](concepts-pricing-tiers.md). Győződjön meg arról, hogy a memória elég a munkaterheléshez. Az alkalmazás terhelésének tesztelésével ellenőrizheti, hogy elegendő-e a memória az olvasási és írási műveletekhez. Ha az adatbázis-memória felhasználása gyakran növekszik egy meghatározott küszöbértéken túl, ez azt jelzi, hogy a példányt a virtuális mag vagy magasabb teljesítményszint növelésével kell frissítenie. A [lekérdezési tároló](concepts-query-store.md)használata, a [lekérdezési teljesítményre vonatkozó javaslatok](concepts-performance-recommendations.md) a leghosszabb időtartamú lekérdezések azonosítására szolgálnak. Ismerkedjen meg az optimalizálási lehetőségekkel. 

### <a name="storage"></a>Tárolás 
A MySQL-kiszolgáló számára kiosztott [tárolási mennyiség](howto-create-manage-server-portal.md#scale-compute-and-storage) határozza meg a kiszolgáló IOPs. A szolgáltatás által használt tárterület magában foglalja az adatbázisfájlok, a tranzakciónaplók, a kiszolgálói naplók és a biztonsági mentési Pillanatképek használatát. Győződjön meg arról, hogy a felhasznált lemezterület mérete nem haladja meg a teljes kiépített lemezterület 85%-át. Ebben az esetben törölnie vagy archiválnia kell az adatbázis-kiszolgáló adatait, hogy szabadítson fel helyet. 

### <a name="network-traffic"></a>Hálózati forgalom 

**Hálózati fogadási átviteli sebesség, hálózati átviteli sebesség** – a MySQL-példány felé irányuló és onnan érkező hálózati forgalom sebessége másodpercenként megabájtban. Ki kell értékelnie a kiszolgáló átviteli sebességének követelményét, és folyamatosan figyelnie kell a forgalmat, ha az átviteli sebesség a vártnál kisebb. 

### <a name="database-connections"></a>Adatbázis-kapcsolatok 
**Adatbázis-kapcsolatok** – a Azure Database for MySQLhoz kapcsolódó ügyféloldali munkamenetek számát össze kell hangolni a [kiválasztott SKU-méret csatlakozási korlátaival](concepts-server-parameters.md#max_connections) . 


## <a name="next-steps"></a>Következő lépések

- [Az Azure Database for MySQL teljesítményének bevált gyakorlata](concept-performance-best-practices.md)
- [Ajánlott eljárás a kiszolgálói műveletek Azure Database for MySQL használatával](concept-operation-excellence-best-practices.md)
