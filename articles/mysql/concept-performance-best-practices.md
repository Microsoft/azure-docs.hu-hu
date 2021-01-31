---
title: Ajánlott eljárások a teljesítményhez – Azure Database for MySQL
description: Ez a cikk néhány, a Azure Database for MySQL teljesítményének figyelésére és hangolására vonatkozó javaslatot ismertet.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 46c7952247babd528b230dfa0e70b0eb47878912
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217754"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Ajánlott eljárások az Azure Database for MySQL – egyetlen kiszolgáló optimális teljesítményéhez

Ismerje meg, hogyan érheti el a legjobb teljesítményt a Azure Database for MySQL egyetlen kiszolgálóval való munka során. Ahogy új képességeket adunk a platformhoz, az ebben a szakaszban ismertetett javaslatokat továbbra is finomítjuk.

## <a name="physical-proximity"></a>Fizikai közelség

 Ügyeljen arra, hogy egy adott régióban telepítsen egy alkalmazást és egy adatbázist. A teljesítmény-teljesítményteszt futtatásának megkezdése előtt egy gyors vizsgálattal határozható meg az ügyfél és az adatbázis közötti hálózati késés egy egyszerű SELECT 1 lekérdezés használatával. 

## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

Ha Azure-beli virtuális gépet, Azure Kubernetes vagy App Services használ, használjon gyorsított hálózatkezelést az alkalmazáskiszolgáló számára. A gyorsított hálózatkezelés lehetővé teszi az egyszintű I/O-virtualizálás (SR-IOV) használatát egy virtuális gépre, nagy mértékben javítja hálózati teljesítményét. Ez a nagy teljesítményű elérési út megkerüli a gazdagépet a DataPath, csökkenti a késést, a vibrálás és a CPU-kihasználtságot, és a legszigorúbb hálózati számítási feladatokhoz használja a támogatott virtuálisgép-típusoknál.

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
 
## <a name="use-innodb-buffer-pool-warmup"></a>InnoDB-puffer használata – bemelegedési

Azure Database for MySQL kiszolgáló újraindítása után a rendszer betölti a tárolóban lévő adatlapokat, mivel a rendszer lekérdezi a táblákat, ami nagyobb késést és lassabb teljesítményt eredményez a lekérdezések első végrehajtásakor. Ez nem fogadható el a késésre érzékeny munkaterhelések esetében. 

A InnoDB-puffer használatával a bemelegedési idő lerövidíti a bemelegedési időszakot azáltal, hogy az újraindítás előtt újra betölti a pufferben lévő lemezeket, és nem várta a DML-t, vagy KIVÁLASZTJA a műveleteket a megfelelő sorok eléréséhez.

A Azure Database for MySQL-kiszolgáló újraindítása után csökkentheti a bemelegedési időszakot, amely a [InnoDB puffer-kiszolgáló paramétereinek](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)konfigurálásával teljesítménybeli előnyt jelent. A InnoDB az egyes pufferek legutóbb használt lapjainak százalékos arányát menti a kiszolgáló leállításakor, és visszaállítja ezeket a lapokat a kiszolgáló indításakor.

Azt is fontos megjegyezni, hogy a jobb teljesítmény a kiszolgáló hosszú indítási ideje alapján történik. Ha ez a paraméter engedélyezve van, a kiszolgáló indítási és újraindítási ideje várhatóan a kiszolgálón kiépített IOPS függően növekszik. 

Javasoljuk, hogy tesztelje és figyelje az újraindítási időt annak biztosítására, hogy az indítási/újraindítási teljesítmény elfogadható legyen, mivel a kiszolgáló ebben az időszakban nem érhető el. Ezt a paramétert nem ajánlott a 1000-nél kevesebb kiosztott IOPS használni (vagy más szóval, ha a tároló kiosztott értéke kisebb, mint 335 GB).

Ha menteni szeretné a puffer állapotát a kiszolgáló leállításakor, állítsa a Server paramétert a következőre: `innodb_buffer_pool_dump_at_shutdown` `ON` . Hasonlóképpen állítsa be a kiszolgálói paramétert a `innodb_buffer_pool_load_at_startup` `ON` puffer-készlet állapotának visszaállításához a kiszolgáló indításakor. A kiszolgáló paraméter értékének csökkentésével és finomhangolásával szabályozhatja az indítási és újraindítási időpontra gyakorolt hatást `innodb_buffer_pool_dump_pct` . Alapértelmezés szerint ez a paraméter a következőre van beállítva: `25` .

> [!Note]
> A InnoDB puffer-készlet bemelegedési paramétereit csak az általános célú tároló kiszolgálók támogatják, legfeljebb 16 TB tárhellyel. További információ a [Azure Database for MySQL tárolási lehetőségeiről](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage).

## <a name="next-steps"></a>Következő lépések

- [Ajánlott eljárás a kiszolgálói műveletek Azure Database for MySQL használatával](concept-operation-excellence-best-practices.md) <br/>
- [Ajánlott eljárás a Azure Database for MySQL monitorozásához](concept-monitoring-best-practices.md)<br/>
- [Ismerkedés a Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>