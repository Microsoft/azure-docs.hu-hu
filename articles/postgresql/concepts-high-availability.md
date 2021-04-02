---
title: Magas rendelkezésre állás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a magas rendelkezésre állással kapcsolatos információkat nyújt Azure Database for PostgreSQL – egyetlen kiszolgálón
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/15/2020
ms.openlocfilehash: aa9f38b2cefa60a0c3341c1317cf45fbcb735301
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92485443"
---
# <a name="high-availability-in-azure-database-for-postgresql--single-server"></a>Magas rendelkezésre állás Azure Database for PostgreSQL – egyetlen kiszolgáló
A Azure Database for PostgreSQL – az egykiszolgálós szolgáltatás garantált magas szintű rendelkezésre állást biztosít a pénzügyi felelősséggel vállalt szolgáltatói szerződéssel (SLA) [99,99%-os](https://azure.microsoft.com/support/legal/sla/postgresql) üzemidő mellett. Azure Database for PostgreSQL magas rendelkezésre állást biztosít a tervezett események (például a megkezdeni skálázási számítási művelet) során, valamint olyan nem tervezett események esetén is, mint például az alapul szolgáló hardver, szoftver vagy hálózati hiba. Azure Database for PostgreSQL gyorsan helyreállítható a legfontosabb körülmények között, így gyakorlatilag nincs alkalmazás-leállási idő a szolgáltatás használatakor.

Azure Database for PostgreSQL alkalmas olyan kritikus fontosságú adatbázisok futtatására, amelyek nagy üzemidőt igényelnek. Az Azure-architektúrára épülő szolgáltatás magas rendelkezésre állást, redundanciát és rugalmasságot biztosít a tervezett és nem tervezett leállások miatt, anélkül, hogy további összetevőket kellene konfigurálnia. 

## <a name="components-in-azure-database-for-postgresql--single-server"></a>Összetevők Azure Database for PostgreSQL – egyetlen kiszolgáló

| **Összetevő** | **Leírás**|
| ------------ | ----------- |
| <b>PostgreSQL adatbázis-kiszolgáló | Azure Database for PostgreSQL biztosít biztonságot, elkülönítést, erőforrás-védelmet és gyors újraindítási képességet az adatbázis-kiszolgálók számára. Ezek a képességek olyan műveleteket könnyítenek meg, mint például a skálázás és az adatbázis-kiszolgáló helyreállítási művelet másodpercek alatt. <br/> Az adatbázis-kiszolgáló adatmódosításai jellemzően egy adatbázis-tranzakció kontextusában történnek. Az adatbázis összes változását szinkronban kell rögzíteni az Azure Storage-ban (a WAL), amely az adatbázis-kiszolgálóhoz van csatolva. Az adatbázis- [ellenőrzőpont](https://www.postgresql.org/docs/11/sql-checkpoint.html) folyamata során a rendszer az adatbázis-kiszolgáló memóriájából származó adatlapokat is kiüríti a tárolóba. |
| <b>Távoli tárterület | A PostgreSQL fizikai adatfájljait és a WAL-fájlokat az Azure Storage tárolja, amely az adatredundancia, a rendelkezésre állás és a megbízhatóság biztosítása érdekében a régión belül három példányban tárolja az adatmennyiséget. A tárolási réteg szintén független az adatbázis-kiszolgálótól. Leválasztható egy sikertelen adatbázis-kiszolgálóról, és néhány másodpercen belül újra hozzá lett csatolva egy új adatbázis-kiszolgálóhoz. Emellett az Azure Storage folyamatosan figyeli a tárolási hibákat. Ha a rendszer blokkolja a sérülést, a rendszer automatikusan egy új tárolási példány létrehozásával rögzíti. |
| <b>Átjáró | Az átjáró adatbázis-proxyként működik, az adatbázis-kiszolgálóval létesített összes ügyfélkapcsolatot irányítja. |

## <a name="planned-downtime-mitigation"></a>Tervezett leállás-csökkentés
A Azure Database for PostgreSQL a tervezett leállási műveletek során magas rendelkezésre állást biztosít. 

:::image type="content" source="./media/concepts-high-availability/azure-postgresql-elastic-scaling.png" alt-text="Rugalmas skálázás megtekintése az Azure PostgreSQL-ben":::

1. A PostgreSQL-adatbázis-kiszolgálók vertikális fel-és leskálázása másodpercek alatt
2. Az ügyfél útválasztására szolgáló proxyként működő átjáró a megfelelő adatbázis-kiszolgálóhoz csatlakozik
3. A tárterület felskálázása leállás nélkül végezhető el. A távoli tárterület lehetővé teszi a gyors leválasztást/újbóli csatolást a feladatátvétel után.
Néhány tervezett karbantartási forgatókönyv:

| **Forgatókönyv** | **Leírás**|
| ------------ | ----------- |
| <b>Számítási felskálázás felfelé/lefelé | Ha a felhasználó számítási vertikális Felskálázási műveletet hajt végre, egy új adatbázis-kiszolgáló lesz kiépítve a méretezett számítási konfiguráció használatával. A régi adatbázis-kiszolgálóban az aktív ellenőrzőpontok befejeződik, az ügyfélkapcsolatok kiürülnek, a nem véglegesített tranzakciók megszakadnak, majd leállnak. A tárterület ezután le lesz választva a régi adatbázis-kiszolgálóról, és az új adatbázis-kiszolgálóhoz van csatolva. Amikor az ügyfélalkalmazás újrapróbálkozik a csatlakozással, vagy új csatlakozást próbál létrehozni, az átjáró a kapcsolódási kérelmet az új adatbázis-kiszolgálóra irányítja.|
| <b>Tárterület skálázása | A tárterület skálázása online művelet, és nem szakítja meg az adatbázis-kiszolgálót.|
| <b>Új szoftverek központi telepítése (Azure) | Új szolgáltatások bevezetése vagy hibajavítások automatikusan történnek a szolgáltatás tervezett karbantartásának részeként. További információkért tekintse meg a [dokumentációt](./concepts-monitoring.md#planned-maintenance-notification), és tekintse meg a [portált](https://aka.ms/servicehealthpm)is.|
| <b>Másodlagos verziók frissítése | Azure Database for PostgreSQL az adatbázis-kiszolgálókat az Azure által meghatározott alverzióra automatikusan kijavításra kerül. A szolgáltatás tervezett karbantartásának részeként történik. Ez rövid állásidőt von maga után másodpercben, és az adatbázis-kiszolgáló automatikusan újraindul az új alverzióval. További információkért tekintse meg a [dokumentációt](./concepts-monitoring.md#planned-maintenance-notification), és tekintse meg a [portált](https://aka.ms/servicehealthpm)is.|


##  <a name="unplanned-downtime-mitigation"></a>Nem tervezett leállás-csökkentés

A nem tervezett leállás váratlan meghibásodások miatt fordulhat elő, beleértve a mögöttes hardverhiba, a hálózati problémák és a szoftverek hibáit. Ha az adatbázis-kiszolgáló váratlanul leáll, a rendszer automatikusan kiépít egy új adatbázis-kiszolgálót másodpercek alatt. A távoli tárterület automatikusan csatolva lesz az új adatbázis-kiszolgálóhoz. A PostgreSQL motor a helyreállítási műveletet a WAL-és adatbázisfájlok használatával hajtja végre, és megnyitja az adatbázis-kiszolgálót, amely lehetővé teszi az ügyfelek kapcsolódását. A nem véglegesített tranzakciók elvesznek, és az alkalmazásnak újra kell próbálkoznia. A nem tervezett állásidőt nem lehet elkerülni, Azure Database for PostgreSQL csökkenti az állásidőt úgy, hogy az adatbázis-kiszolgálón és a tárolási rétegen is automatikusan végrehajtja a helyreállítási műveleteket anélkül, hogy emberi beavatkozásra lenne szükség. 


:::image type="content" source="./media/concepts-high-availability/azure-postgresql-built-in-high-availability.png" alt-text="Magas rendelkezésre állás megtekintése az Azure PostgreSQL-ben":::

1. Azure PostgreSQL-kiszolgálók gyors skálázási képességekkel.
2. Átjáró, amely proxyként funkcionál az ügyfélkapcsolatok megfelelő adatbázis-kiszolgálóhoz való továbbításához
3. Az Azure Storage három példánnyal rendelkezik a megbízhatóság, a rendelkezésre állás és a redundancia érdekében.
4. A távoli tárterület lehetővé teszi a gyors leválasztást/újbóli csatolást a kiszolgáló feladatátvétele után.
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Nem tervezett leállás: meghibásodási forgatókönyvek és szolgáltatás-helyreállítás
Íme néhány meghibásodási forgatókönyv, valamint a Azure Database for PostgreSQL automatikus helyreállítása:

| **Forgatókönyv** | **Automatikus helyreállítás** |
| ---------- | ---------- |
| <B>Adatbázis-kiszolgáló meghibásodása | Ha az adatbázis-kiszolgáló valamilyen mögöttes hardverhiba miatt leáll, a rendszer elveti az aktív kapcsolatokat, és minden fedélzeti tranzakciót megszakít. A rendszer automatikusan telepíti az új adatbázis-kiszolgálót, és a távoli adattároló csatlakozik az új adatbázis-kiszolgálóhoz. Az adatbázis-helyreállítás befejezése után az ügyfelek az átjárón keresztül csatlakozhatnak az új adatbázis-kiszolgálóhoz. <br /> <br /> A helyreállítási idő (RTO) függ a különböző tényezőktől, például a hiba időpontjában felmerülő tevékenységtől, például a nagy tranzakciótól, valamint az adatbázis-kiszolgáló indítási folyamata során elvégzendő helyreállítás mennyiségétől. <br /> <br /> A PostgreSQL-adatbázisokat használó alkalmazásokat úgy kell létrehozni, hogy felderítsék és újra elhagyják a kapcsolatokat és a sikertelen tranzakciókat.  Ha az alkalmazás újrapróbálkozik, az átjáró transzparens módon átirányítja a kapcsolódást az újonnan létrehozott adatbázis-kiszolgálóhoz. |
| <B>Tárolási hiba | Az alkalmazások nem érintik a tárterülettel kapcsolatos problémákat, például a lemez meghibásodását vagy a fizikai blokk sérülését. Mivel az adattárolás 3 példányban történik, az adatmásolatot a túlélő tároló kézbesíti. A rendszer automatikusan kijavítja a blokkolási hibákat. Ha a rendszer elveszi az adatmásolatot, a rendszer automatikusan létrehozza az adatgyűjtés új másolatát. |

Az alábbiakban néhány olyan meghibásodási forgatókönyvet talál, amelyek felhasználói beavatkozást igényelnek a helyreállításhoz:

| **Forgatókönyv** | **Helyreállítási terv** |
| ---------- | ---------- |
| <b> Régió meghibásodása | A régió meghibásodása ritka esemény. Ha azonban egy régió meghibásodása elleni védelemre van szüksége, egy vagy több olvasási replikát is beállíthat más régiókban a vész-helyreállításhoz (DR). (A részletekért olvassa el [a következő cikket](./howto-read-replicas-portal.md) : olvasási replikák létrehozása és kezelése. Régió szintű meghibásodás esetén manuálisan is előléptetheti a másik régióban konfigurált olvasási replikát az éles adatbázis-kiszolgálóként. |
| <b> Logikai/felhasználói hibák | A felhasználói hibákból, például a véletlenül eldobott táblákból vagy a helytelenül frissített adatokból történő helyreállításhoz az adott [időponthoz tartozó helyreállítást](./concepts-backup.md) (PITR) kell végrehajtania az adatok visszaállításával és helyreállításával egészen a hiba előtt.<br> <br>  Ha csak adatbázisok vagy meghatározott táblák egy részhalmazát szeretné visszaállítani az adatbázis-kiszolgáló összes adatbázisa helyett, akkor az adatbázis-kiszolgálót visszaállíthatja egy új példányban, majd exportálhatja a táblázat (oka) t [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html), majd a [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) használatával visszaállíthatja ezeket a táblákat az adatbázisba. |



## <a name="summary"></a>Összefoglalás

A Azure Database for PostgreSQL gyors újraindítási képességet biztosít az adatbázis-kiszolgálók, a redundáns tárolók és a hatékony útválasztás számára az átjáróról. További adatvédelem esetén a biztonsági mentések földrajzilag replikálva konfigurálhatók, és egy vagy több olvasási replika is üzembe helyezhető más régiókban. A magas rendelkezésre állási képességekkel rendelkező Azure Database for PostgreSQL a leggyakoribb kimaradások miatt védi az adatbázisokat, és piacvezető, pénzügyi támogatású, [99,99%-os üzemidőt](https://azure.microsoft.com/support/legal/sla/postgresql)biztosít. Mindezek a rendelkezésre állási és megbízhatósági képességek lehetővé teszik az Azure számára, hogy ideális platformot biztosítson a kritikus fontosságú alkalmazások futtatásához.

## <a name="next-steps"></a>Következő lépések
- Az [Azure-régiók](../availability-zones/az-overview.md) megismerése
- Tudnivalók az [átmeneti kapcsolódási hibák kezelésére](concepts-connectivity.md)
- Ismerje meg, hogyan [replikálhatja adatait olvasási replikákkal](howto-read-replicas-portal.md)