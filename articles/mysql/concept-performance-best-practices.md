---
title: Ajánlott eljárások a teljesítményhez – Azure Database for MySQL
description: Ez a cikk a Azure Database for MySQL teljesítményének figyeléséhez és finomhangolásához ajánlott eljárásokat ismerteti.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 30176e2df850e6d2794ab9c1542bcb6a89d8f89f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880406"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Ajánlott eljárások az Azure Database for MySQL – egyetlen kiszolgáló optimális teljesítményéhez

Ismerje meg az ajánlott eljárásokat a legjobb teljesítmény érdekében, miközben a Azure Database for MySQL egyetlen kiszolgálóval dolgozik. Ahogy új képességeket adunk a platformhoz, továbbra is pontosítjuk az ebben a szakaszban részletezett ajánlott eljárásokat.

## <a name="physical-proximity"></a>Fizikai közelség

 Ügyeljen arra, hogy egy adott régióban telepítsen egy alkalmazást és egy adatbázist. A teljesítmény-teljesítményteszt futtatásának megkezdése előtt egy gyors vizsgálattal határozható meg az ügyfél és az adatbázis közötti hálózati késés egy egyszerű SELECT 1 lekérdezés használatával. 

## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

Ha Azure-beli virtuális gépet, Azure Kubernetes vagy App Services használ, gyorsított hálózatkezelést használhat az alkalmazáskiszolgáló számára. A gyorsított hálózatkezelés lehetővé teszi az egyszintű I/O-virtualizálás (SR-IOV) használatát egy virtuális gépre, nagy mértékben javítja hálózati teljesítményét. Ez a nagy teljesítményű elérési út megkerüli a gazdagépet a DataPath, csökkenti a késést, a vibrálás és a CPU-kihasználtságot, és a legszigorúbb hálózati számítási feladatokhoz használja a támogatott virtuálisgép-típusoknál.

## <a name="connection-efficiency"></a>A kapcsolatok hatékonysága

Az új kapcsolatok létrehozása mindig költséges és időigényes feladat. Amikor egy alkalmazás adatbázis-kapcsolatot kér, rangsorolja a meglévő tétlen adatbázis-kapcsolatok kiosztását ahelyett, hogy újat hozna létre.  Íme néhány lehetőség a jó kapcsolatok kezeléséhez:

- **ProxySQL**: a [ProxySQL](https://proxysql.com/) -t használja, amely beépített kapcsolatokat biztosít a terheléselosztáshoz, és az alkalmazás kódjában bekövetkezett változások miatt igény szerint több olvasási replikára is [betöltheti](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) a számítási feladatokat.

- **Heimdall-adatproxy**: Alternatív megoldásként kihasználhatja a Heimdall-adatproxyt, amely egy szállító által semleges, szabadalmaztatott proxy megoldást is biztosít. Támogatja a lekérdezések gyorsítótárazását és az írási/olvasási felosztást a replikálási késések észlelésével. Azt is megtudhatja, hogyan [gyorsíthatja fel a MySQL teljesítményét a Heimdall proxyval](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349).  

- **Állandó vagy Long-Lived kapcsolat**: Ha az alkalmazás rövid tranzakciókkal vagy lekérdezésekkel rendelkezik, és általában végrehajtási idő < 5-10 MS, akkor cserélje le a rövid kapcsolatokat állandó kapcsolatokkal. Az állandó kapcsolatokkal rendelkező rövid kapcsolatok cseréje csak kisebb módosításokat igényel a kódban, de jelentős hatással van a teljesítmény javítására számos tipikus alkalmazási helyzetben. Győződjön meg arról, hogy a tranzakció befejezése után az időtúllépés vagy a kapcsolat lezárása beállítás be van állítva.

- **Replika**: Ha replikát használ, a [ProxySQL](https://proxysql.com/) használatával kiegyenlítheti a terhelést az elsődleges kiszolgáló és az olvasható másodlagos replika-kiszolgáló között. Ismerje meg [, hogyan állíthatja be a ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847).

## <a name="data-import-configurations"></a>Adatimportálási konfigurációk

- Az Adatimportálási művelet megkezdése előtt ideiglenesen méretezheti a példányt magasabb SKU-méretre, majd lekicsinyítheti az importálás sikerességét.
- A [Azure Database Migration Service (DMS)](https://datamigration.microsoft.com/) használatával online vagy offline Migrálás esetén minimális állásidővel importálhatja adatait. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Azure Database for MySQL memória javaslatai

Az Azure Database for MySQL teljesítményének bevált gyakorlata, hogy elegendő RAM-memóriát foglaljon le, így a munkakészletek majdnem teljesen a memóriában találhatók. 

- Ellenőrizze, hogy a használt memória százalékos aránya eléri-e a [határértékeket](./concepts-pricing-tiers.md) a [MySQL-kiszolgáló metrikái](./concepts-monitoring.md)alapján. 
- Állítsa be az ilyen számú riasztást annak biztosítására, hogy mivel a kiszolgálók elérik a korlátozásokat, megteheti a kijavításához szükséges lépéseket. A meghatározott korlátok alapján ellenőrizze, hogy az adatbázis-SKU felskálázása – akár magasabb számítási méretre, akár jobb árképzési szinten – a teljesítmény drámai növekedését eredményezi. 
- Vertikális felskálázás, amíg a teljesítménybeli számok nem csökkennek jelentősen a skálázási művelet után. Az adatbázis-példány metrikáinak figyelésével kapcsolatos információkért lásd: [MySQL db-metrikák](./concepts-monitoring.md#metrics).

## <a name="next-steps"></a>További lépések

- [Ajánlott eljárás a kiszolgálói műveletek Azure Database for MySQL használatával](concept-operation-excellence-best-practices.md) <br/>
- [Ajánlott eljárás a Azure Database for MySQL monitorozásához](concept-monitoring-best-practices.md)<br/>
- [Ismerkedés a Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>