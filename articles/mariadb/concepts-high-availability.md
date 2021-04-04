---
title: Magas rendelkezésre állás – Azure Database for MariaDB
description: Ez a cikk a magas rendelkezésre állásról nyújt információkat Azure Database for MariaDB
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 4dcb1ac7ce4b468374993c11578bce553f766a42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664333"
---
# <a name="high-availability-in-azure-database-for-mariadb"></a>Magas rendelkezésre állás a Azure Database for MariaDBban
A Azure Database for MariaDB szolgáltatás garantált magas szintű rendelkezésre állást biztosít a pénzügyi felelősséggel vállalt szolgáltatói szerződéssel (SLA) [99,99%-os](https://azure.microsoft.com/support/legal/sla/MariaDB) üzemidő mellett. Azure Database for MariaDB magas rendelkezésre állást biztosít a tervezett események (például a megkezdeni skálázási számítási művelet) során, valamint olyan nem tervezett események esetén is, mint például az alapul szolgáló hardver, szoftver vagy hálózati hiba. Azure Database for MariaDB gyorsan helyreállítható a legfontosabb körülmények között, így gyakorlatilag nincs alkalmazás-leállási idő a szolgáltatás használatakor.

Azure Database for MariaDB alkalmas olyan kritikus fontosságú adatbázisok futtatására, amelyek nagy üzemidőt igényelnek. Az Azure-architektúrára épülő szolgáltatás magas rendelkezésre állást, redundanciát és rugalmasságot biztosít a tervezett és nem tervezett leállások miatt, anélkül, hogy további összetevőket kellene konfigurálnia. 

## <a name="components-in-azure-database-for-mariadb"></a>Összetevők a Azure Database for MariaDB

| **Összetevő** | **Leírás**|
| ------------ | ----------- |
| <b>MariaDB adatbázis-kiszolgáló | Azure Database for MariaDB biztosít biztonságot, elkülönítést, erőforrás-védelmet és gyors újraindítási képességet az adatbázis-kiszolgálók számára. Ezek a képességek olyan műveleteket könnyítenek meg, mint például a skálázás és az adatbázis-kiszolgáló helyreállítási művelet másodpercek alatt. <br/> Az adatbázis-kiszolgáló adatmódosításai jellemzően egy adatbázis-tranzakció kontextusában történnek. Az adatbázis összes változását szinkronban kell rögzíteni az Azure Storage-ban (ib_log), az adatbázis-kiszolgálóhoz csatolt naplófájlok formájában. Az adatbázis- [ellenőrzőpont](https://mariadb.com/kb/innodb-redo-log/#checkpoints) folyamata során a rendszer az adatbázis-kiszolgáló memóriájából származó adatlapokat is kiüríti a tárolóba. |
| <b>Távoli tárterület | Az összes MariaDB fizikai adatfájlt és naplófájlt az Azure Storage tárolja, amely a régión belüli három adatmásolat tárolására szolgál az adatredundancia, a rendelkezésre állás és a megbízhatóság biztosítása érdekében. A tárolási réteg szintén független az adatbázis-kiszolgálótól. Leválasztható egy sikertelen adatbázis-kiszolgálóról, és néhány másodpercen belül újra hozzá lett csatolva egy új adatbázis-kiszolgálóhoz. Emellett az Azure Storage folyamatosan figyeli a tárolási hibákat. Ha a rendszer blokkolja a sérülést, a rendszer automatikusan egy új tárolási példány létrehozásával rögzíti. |
| <b>Átjáró | Az átjáró adatbázis-proxyként működik, az adatbázis-kiszolgálóval létesített összes ügyfélkapcsolatot irányítja. |

## <a name="planned-downtime-mitigation"></a>Tervezett leállás-csökkentés
A Azure Database for MariaDB a tervezett leállási műveletek során magas rendelkezésre állást biztosít. 

![Rugalmas skálázás megtekintése az Azure MariaDB](./media/concepts-high-availability/elastic-scaling-mariadb-server.png)

Néhány tervezett karbantartási forgatókönyv:

| **Forgatókönyv** | **Leírás**|
| ------------ | ----------- |
| <b>Számítási felskálázás felfelé/lefelé | Ha a felhasználó számítási vertikális Felskálázási műveletet hajt végre, egy új adatbázis-kiszolgáló lesz kiépítve a méretezett számítási konfiguráció használatával. A régi adatbázis-kiszolgálóban az aktív ellenőrzőpontok befejeződik, az ügyfélkapcsolatok kiürülnek, a nem véglegesített tranzakciók megszakadnak, majd leállnak. A tárterület ezután le lesz választva a régi adatbázis-kiszolgálóról, és az új adatbázis-kiszolgálóhoz van csatolva. Amikor az ügyfélalkalmazás újrapróbálkozik a csatlakozással, vagy új csatlakozást próbál létrehozni, az átjáró a kapcsolódási kérelmet az új adatbázis-kiszolgálóra irányítja.|
| <b>Tárterület skálázása | A tárterület skálázása online művelet, és nem szakítja meg az adatbázis-kiszolgálót.|
| <b>Új szoftverek központi telepítése (Azure) | Új szolgáltatások bevezetése vagy hibajavítások automatikusan történnek a szolgáltatás tervezett karbantartásának részeként. További információkért tekintse meg a [dokumentációt](concepts-monitoring.md#planned-maintenance-notification), és tekintse meg a [portált](https://aka.ms/servicehealthpm)is.|
| <b>Másodlagos verziók frissítése | Azure Database for MariaDB az adatbázis-kiszolgálókat az Azure által meghatározott alverzióra automatikusan kijavításra kerül. A szolgáltatás tervezett karbantartásának részeként történik. Ez rövid állásidőt von maga után másodpercben, és az adatbázis-kiszolgáló automatikusan újraindul az új alverzióval. További információkért tekintse meg a [dokumentációt](concepts-monitoring.md#planned-maintenance-notification), és tekintse meg a [portált](https://aka.ms/servicehealthpm)is.|


##  <a name="unplanned-downtime-mitigation"></a>Nem tervezett leállás-csökkentés

A nem tervezett leállás váratlan meghibásodások miatt fordulhat elő, beleértve a mögöttes hardverhiba, a hálózati problémák és a szoftverek hibáit. Ha az adatbázis-kiszolgáló váratlanul leáll, a rendszer automatikusan kiépít egy új adatbázis-kiszolgálót másodpercek alatt. A távoli tárterület automatikusan csatolva lesz az új adatbázis-kiszolgálóhoz. A MariaDB motor a helyreállítási műveletet a WAL-és adatbázisfájlok használatával hajtja végre, és megnyitja az adatbázis-kiszolgálót, amely lehetővé teszi az ügyfelek kapcsolódását. A nem véglegesített tranzakciók elvesznek, és az alkalmazásnak újra kell próbálkoznia. A nem tervezett állásidőt nem lehet elkerülni, Azure Database for MariaDB csökkenti az állásidőt úgy, hogy az adatbázis-kiszolgálón és a tárolási rétegen is automatikusan végrehajtja a helyreállítási műveleteket anélkül, hogy emberi beavatkozásra lenne szükség. 


![Magas rendelkezésre állás megtekintése az Azure MariaDB](./media/concepts-high-availability/availability-mariadb-server.png)

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Nem tervezett leállás: meghibásodási forgatókönyvek és szolgáltatás-helyreállítás
Íme néhány meghibásodási forgatókönyv, valamint a Azure Database for MariaDB automatikus helyreállítása:

| **Forgatókönyv** | **Automatikus helyreállítás** |
| ---------- | ---------- |
| <B>Adatbázis-kiszolgáló meghibásodása | Ha az adatbázis-kiszolgáló valamilyen mögöttes hardverhiba miatt leáll, a rendszer elveti az aktív kapcsolatokat, és minden fedélzeti tranzakciót megszakít. A rendszer automatikusan telepíti az új adatbázis-kiszolgálót, és a távoli adattároló csatlakozik az új adatbázis-kiszolgálóhoz. Az adatbázis-helyreállítás befejezése után az ügyfelek az átjárón keresztül csatlakozhatnak az új adatbázis-kiszolgálóhoz. <br /> <br /> A MariaDB-adatbázisokat használó alkalmazásokat úgy kell létrehozni, hogy felderítsék és újra elhagyják a kapcsolatokat és a sikertelen tranzakciókat.  Ha az alkalmazás újrapróbálkozik, az átjáró transzparens módon átirányítja a kapcsolódást az újonnan létrehozott adatbázis-kiszolgálóhoz. |
| <B>Tárolási hiba | Az alkalmazások nem érintik a tárterülettel kapcsolatos problémákat, például a lemez meghibásodását vagy a fizikai blokk sérülését. Mivel az adattárolás 3 példányban történik, az adatmásolatot a túlélő tároló kézbesíti. A rendszer automatikusan kijavítja a blokkolási hibákat. Ha a rendszer elveszi az adatmásolatot, a rendszer automatikusan létrehozza az adatgyűjtés új másolatát. |

Az alábbiakban néhány olyan meghibásodási forgatókönyvet talál, amelyek felhasználói beavatkozást igényelnek a helyreállításhoz:

| **Forgatókönyv** | **Helyreállítási terv** |
| ---------- | ---------- |
| <b> Régió meghibásodása | A régió meghibásodása ritka esemény. Ha azonban egy régió meghibásodása elleni védelemre van szüksége, egy vagy több olvasási replikát is beállíthat más régiókban a vész-helyreállításhoz (DR). (A részletekért olvassa el [a következő cikket](howto-read-replicas-portal.md) : olvasási replikák létrehozása és kezelése. Régió szintű meghibásodás esetén manuálisan is előléptetheti a másik régióban konfigurált olvasási replikát az éles adatbázis-kiszolgálóként. |
| <b> Logikai/felhasználói hibák | A felhasználói hibákból, például a véletlenül eldobott táblákból vagy a helytelenül frissített adatokból történő helyreállításhoz az adott [időponthoz tartozó helyreállítást](concepts-backup.md) (PITR) kell végrehajtania az adatok visszaállításával és helyreállításával egészen a hiba előtt.<br> <br>  Ha az adatbázis-kiszolgáló összes adatbázisa helyett csak adatbázisok vagy meghatározott táblák egy részhalmazát szeretné visszaállítani, az adatbázis-kiszolgálót visszaállíthatja egy új példányban, exportálhatja a táblázat (oka) t a [mysqldump](howto-migrate-dump-restore.md)-on keresztül, majd a [visszaállítás](howto-migrate-dump-restore.md#restore-your-mariadb-database) használatával visszaállíthatja ezeket a táblákat az adatbázisba. |



## <a name="summary"></a>Összefoglalás

A Azure Database for MariaDB gyors újraindítási képességet biztosít az adatbázis-kiszolgálók, a redundáns tárolók és a hatékony útválasztás számára az átjáróról. További adatvédelem esetén a biztonsági mentések földrajzilag replikálva konfigurálhatók, és egy vagy több olvasási replika is üzembe helyezhető más régiókban. A magas rendelkezésre állási képességekkel rendelkező Azure Database for MariaDB a leggyakoribb kimaradások miatt védi az adatbázisokat, és piacvezető, pénzügyi támogatású, [99,99%-os üzemidőt](https://azure.microsoft.com/support/legal/sla/MariaDB)biztosít. Mindezek a rendelkezésre állási és megbízhatósági képességek lehetővé teszik az Azure számára, hogy ideális platformot biztosítson a kritikus fontosságú alkalmazások futtatásához.

## <a name="next-steps"></a>Következő lépések
- Az [Azure-régiók](../availability-zones/az-overview.md) megismerése
- Tudnivalók az [átmeneti kapcsolódási hibák kezelésére](concepts-connectivity.md)
- Ismerje meg, hogyan [replikálhatja adatait olvasási replikákkal](howto-read-replicas-portal.md)