---
title: Magas rendelkezésre állás – Azure Database for MySQL
description: Ez a cikk a magas rendelkezésre állásról nyújt információkat Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 74d6981c0465a1960e920313c1f960f0d781692b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101092971"
---
# <a name="high-availability-in-azure-database-for-mysql"></a>Magas rendelkezésre állás a Azure Database for MySQLban
A Azure Database for MySQL szolgáltatás garantált magas szintű rendelkezésre állást biztosít a pénzügyi felelősséggel vállalt szolgáltatói szerződéssel (SLA) [99,99%-os](https://azure.microsoft.com/support/legal/sla/mysql) üzemidő mellett. Azure Database for MySQL magas rendelkezésre állást biztosít a tervezett események (például a megkezdeni skálázási számítási művelet) során, valamint olyan nem tervezett események esetén is, mint például az alapul szolgáló hardver, szoftver vagy hálózati hiba. Azure Database for MySQL gyorsan helyreállítható a legfontosabb körülmények között, így gyakorlatilag nincs alkalmazás-leállási idő a szolgáltatás használatakor.

Azure Database for MySQL alkalmas olyan kritikus fontosságú adatbázisok futtatására, amelyek nagy üzemidőt igényelnek. Az Azure-architektúrára épülő szolgáltatás magas rendelkezésre állást, redundanciát és rugalmasságot biztosít a tervezett és nem tervezett leállások miatt, anélkül, hogy további összetevőket kellene konfigurálnia. 

## <a name="components-in-azure-database-for-mysql"></a>Összetevők a Azure Database for MySQL

| **Összetevő** | **Leírás**|
| ------------ | ----------- |
| <b>MySQL adatbázis-kiszolgáló | Azure Database for MySQL biztosít biztonságot, elkülönítést, erőforrás-védelmet és gyors újraindítási képességet az adatbázis-kiszolgálók számára. Ezek a képességek megkönnyítik az olyan műveleteket, mint például a skálázás és az adatbázis-kiszolgáló helyreállítási művelete, az adatbázis tranzakciós tevékenységeitől függően 60-120 másodpercen belül. <br/> Az adatbázis-kiszolgáló adatmódosításai jellemzően egy adatbázis-tranzakció kontextusában történnek. Az adatbázis összes változását szinkronban kell rögzíteni az Azure Storage-ban (ib_log), az adatbázis-kiszolgálóhoz csatolt naplófájlok formájában. Az adatbázis- [ellenőrzőpont](https://dev.mysql.com/doc/refman/5.7/en/innodb-checkpoints.html) folyamata során a rendszer az adatbázis-kiszolgáló memóriájából származó adatlapokat is kiüríti a tárolóba. |
| <b>Távoli tárterület | Az összes MySQL fizikai adatfájlt és naplófájlt az Azure Storage tárolja, amely az adatredundancia, a rendelkezésre állás és a megbízhatóság biztosítása érdekében három példányban tárolja az adatmennyiséget a régión belül. A tárolási réteg szintén független az adatbázis-kiszolgálótól. Leválasztható egy sikertelen adatbázis-kiszolgálóról, és 60 másodpercen belül újra csatlakozik egy új adatbázis-kiszolgálóhoz. Emellett az Azure Storage folyamatosan figyeli a tárolási hibákat. Ha a rendszer blokkolja a sérülést, a rendszer automatikusan egy új tárolási példány létrehozásával rögzíti. |
| <b>Átjáró | Az átjáró adatbázis-proxyként működik, az adatbázis-kiszolgálóval létesített összes ügyfélkapcsolatot irányítja. |

## <a name="planned-downtime-mitigation"></a>Tervezett leállás-csökkentés
A Azure Database for MySQL a tervezett leállási műveletek során magas rendelkezésre állást biztosít. 

:::image type="content" source="./media/concepts-high-availability/elastic-scaling-mysql-server.png" alt-text="Rugalmas skálázás megtekintése az Azure MySQL-ben":::

Néhány tervezett karbantartási forgatókönyv:

| **Forgatókönyv** | **Leírás**|
| ------------ | ----------- |
| <b>Számítási felskálázás felfelé/lefelé | Ha a felhasználó számítási vertikális Felskálázási műveletet hajt végre, egy új adatbázis-kiszolgáló lesz kiépítve a méretezett számítási konfiguráció használatával. A régi adatbázis-kiszolgálóban az aktív ellenőrzőpontok befejeződik, az ügyfélkapcsolatok kiürülnek, a nem véglegesített tranzakciók megszakadnak, majd leállnak. A tárterület ezután le lesz választva a régi adatbázis-kiszolgálóról, és az új adatbázis-kiszolgálóhoz van csatolva. Amikor az ügyfélalkalmazás újrapróbálkozik a csatlakozással, vagy új csatlakozást próbál létrehozni, az átjáró a kapcsolódási kérelmet az új adatbázis-kiszolgálóra irányítja.|
| <b>Tárterület skálázása | A tárterület skálázása online művelet, és nem szakítja meg az adatbázis-kiszolgálót.|
| <b>Új szoftverek központi telepítése (Azure) | Új szolgáltatások bevezetése vagy hibajavítások automatikusan történnek a szolgáltatás tervezett karbantartásának részeként. További információkért tekintse meg a [dokumentációt](concepts-monitoring.md#planned-maintenance-notification), és tekintse meg a [portált](https://aka.ms/servicehealthpm)is.|
| <b>Másodlagos verziók frissítése | Azure Database for MySQL az adatbázis-kiszolgálókat az Azure által meghatározott alverzióra automatikusan kijavításra kerül. A szolgáltatás tervezett karbantartásának részeként történik. A tervezett karbantartás során az adatbázis-kiszolgáló újraindítása vagy feladatátvétele is lehetséges, ami a végfelhasználók adatbázis-kiszolgálóinak rövid elérhetőségét eredményezheti. Azure Database for MySQL-kiszolgálók tárolókban futnak, így az adatbázis-kiszolgáló újraindítása általában gyors, és általában 60-120 másodpercen belül várható. A mérnöki csapat gondosan figyeli a teljes tervezett karbantartási eseményt, beleértve az egyes kiszolgálók újraindítását is. A kiszolgáló feladatátvételi ideje függ az adatbázis helyreállításának időpontjától, ami azt eredményezheti, hogy az adatbázis továbbra is online állapotba kerül, ha a feladatátvétel időpontjában nagy tranzakciós tevékenységet végez a kiszolgálón. Ha el szeretné kerülni a hosszabb újraindítási időt, ajánlott elkerülni a hosszú ideig futó tranzakciókat (tömeges terheléseket) a tervezett karbantartási események során. További információkért tekintse meg a [dokumentációt](concepts-monitoring.md#planned-maintenance-notification), és tekintse meg a [portált](https://aka.ms/servicehealthpm)is.|


##  <a name="unplanned-downtime-mitigation"></a>Nem tervezett leállás-csökkentés

A nem tervezett leállás váratlan meghibásodások miatt fordulhat elő, beleértve a mögöttes hardverhiba, a hálózati problémák és a szoftverek hibáit. Ha az adatbázis-kiszolgáló váratlanul leáll, az új adatbázis-kiszolgáló 60-120 másodperc múlva automatikusan kiépítve lesz. A távoli tárterület automatikusan csatolva lesz az új adatbázis-kiszolgálóhoz. A MySQL-motor a helyreállítási műveletet a WAL-és adatbázisfájlok használatával hajtja végre, és megnyitja az adatbázis-kiszolgálót, amely lehetővé teszi az ügyfelek kapcsolódását. A nem véglegesített tranzakciók elvesznek, és az alkalmazásnak újra kell próbálkoznia. A nem tervezett állásidőt nem lehet elkerülni, Azure Database for MySQL csökkenti az állásidőt úgy, hogy az adatbázis-kiszolgálón és a tárolási rétegen is automatikusan végrehajtja a helyreállítási műveleteket anélkül, hogy emberi beavatkozásra lenne szükség. 


:::image type="content" source="./media/concepts-high-availability/availability-for-mysql-server.png" alt-text="Magas rendelkezésre állás megtekintése az Azure MySQL-ben":::

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Nem tervezett leállás: meghibásodási forgatókönyvek és szolgáltatás-helyreállítás
Íme néhány meghibásodási forgatókönyv, valamint a Azure Database for MySQL automatikus helyreállítása:

| **Forgatókönyv** | **Automatikus helyreállítás** |
| ---------- | ---------- |
| <B>Adatbázis-kiszolgáló meghibásodása | Ha az adatbázis-kiszolgáló valamilyen mögöttes hardverhiba miatt leáll, a rendszer elveti az aktív kapcsolatokat, és minden fedélzeti tranzakciót megszakít. A rendszer automatikusan telepíti az új adatbázis-kiszolgálót, és a távoli adattároló csatlakozik az új adatbázis-kiszolgálóhoz. Az adatbázis-helyreállítás befejezése után az ügyfelek az átjárón keresztül csatlakozhatnak az új adatbázis-kiszolgálóhoz. <br /> <br /> A MySQL-adatbázisokat használó alkalmazásokat úgy kell létrehozni, hogy azok felderítsék és újra elhagyják a kapcsolatokat és a sikertelen tranzakciókat.  Ha az alkalmazás újrapróbálkozik, az átjáró transzparens módon átirányítja a kapcsolódást az újonnan létrehozott adatbázis-kiszolgálóhoz. |
| <B>Tárolási hiba | Az alkalmazások nem érintik a tárterülettel kapcsolatos problémákat, például a lemez meghibásodását vagy a fizikai blokk sérülését. Mivel az adattárolás 3 példányban történik, az adatmásolatot a túlélő tároló kézbesíti. A rendszer automatikusan kijavítja a blokkolási hibákat. Ha a rendszer elveszi az adatmásolatot, a rendszer automatikusan létrehozza az adatgyűjtés új másolatát. |

Az alábbiakban néhány olyan meghibásodási forgatókönyvet talál, amelyek felhasználói beavatkozást igényelnek a helyreállításhoz:

| **Forgatókönyv** | **Helyreállítási terv** |
| ---------- | ---------- |
| <b> Régió meghibásodása | A régió meghibásodása ritka esemény. Ha azonban egy régió meghibásodása elleni védelemre van szüksége, egy vagy több olvasási replikát is beállíthat más régiókban a vész-helyreállításhoz (DR). (A részletekért olvassa el [a következő cikket](howto-read-replicas-portal.md) : olvasási replikák létrehozása és kezelése. Régió szintű meghibásodás esetén manuálisan is előléptetheti a másik régióban konfigurált olvasási replikát az éles adatbázis-kiszolgálóként. |
| <b> Logikai/felhasználói hibák | A felhasználói hibákból, például a véletlenül eldobott táblákból vagy a helytelenül frissített adatokból történő helyreállításhoz az adott [időponthoz tartozó helyreállítást](concepts-backup.md) (PITR) kell végrehajtania az adatok visszaállításával és helyreállításával egészen a hiba előtt.<br> <br>  Ha az adatbázis-kiszolgáló összes adatbázisa helyett csak adatbázisok vagy meghatározott táblák egy részhalmazát szeretné visszaállítani, az adatbázis-kiszolgálót visszaállíthatja egy új példányban, exportálhatja a táblázat (oka) t a [mysqldump](concepts-migrate-dump-restore.md)-on keresztül, majd a [visszaállítás](concepts-migrate-dump-restore.md#restore-your-mysql-database-using-command-line-or-mysql-workbench) használatával visszaállíthatja ezeket a táblákat az adatbázisba. |



## <a name="summary"></a>Összefoglalás

A Azure Database for MySQL gyors újraindítási képességet biztosít az adatbázis-kiszolgálók, a redundáns tárolók és a hatékony útválasztás számára az átjáróról. További adatvédelem esetén a biztonsági mentések földrajzilag replikálva konfigurálhatók, és egy vagy több olvasási replika is üzembe helyezhető más régiókban. A magas rendelkezésre állási képességekkel rendelkező Azure Database for MySQL a leggyakoribb kimaradások miatt védi az adatbázisokat, és piacvezető, pénzügyi támogatású, [99,99%-os üzemidőt](https://azure.microsoft.com/support/legal/sla/mysql)biztosít. Mindezek a rendelkezésre állási és megbízhatósági képességek lehetővé teszik az Azure számára, hogy ideális platformot biztosítson a kritikus fontosságú alkalmazások futtatásához.

## <a name="next-steps"></a>Következő lépések
- Az [Azure-régiók](../availability-zones/az-overview.md) megismerése
- Tudnivalók az [átmeneti kapcsolódási hibák kezelésére](concepts-connectivity.md)
- Ismerje meg, hogyan [replikálhatja adatait olvasási replikákkal](howto-read-replicas-portal.md)
