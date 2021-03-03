---
title: Az üzletmenet folytonosságának áttekintése – Azure Database for MySQL rugalmas kiszolgáló
description: Ismerje meg az üzletmenet folytonosságával kapcsolatos fogalmakat Azure Database for MySQL rugalmas kiszolgálóval
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 4f9cc8321d5d1d19dbcb8294ad6205b01337ee72
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715054"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>Az üzletmenet folytonosságának áttekintése Azure Database for MySQL rugalmas kiszolgálóval (előzetes verzió)

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Azure Database for MySQL rugalmas kiszolgáló lehetővé teszi az olyan üzletmenet-folytonossági képességek használatát, amelyek a tervezett és nem tervezett leállás esetén védik az adatbázisokat. Az automatizált biztonsági mentések és a magas rendelkezésre állási funkciók a különböző helyreállítási időpontok és adatvesztési kockázatok különböző szintjeivel rendelkeznek. Az alkalmazásnak a hibák elleni védelemhez való megtervezése során érdemes figyelembe vennie a helyreállítási idő célkitűzését (RTO) és a helyreállítási pont célkitűzését (RPO) minden egyes alkalmazáshoz. A RTO az állásidő tűréshatára, és a RPO az adatvesztési tolerancia az adatbázis-szolgáltatás megszakadását követően.

Az alábbi táblázat a rugalmas kiszolgáló által kínált funkciókat mutatja be.

| **Szolgáltatás** | **Leírás** | **Korlátozások** |
| ---------- | ----------- | ------------ |
| **Biztonsági mentés & helyreállítás** | A rugalmas kiszolgáló automatikusan elvégzi az adatbázisfájlok napi biztonsági mentését, és folyamatosan készíti el a tranzakciónaplókat. A biztonsági mentések megtekinthetők az 1 – 35 nap közötti időszakban. Az adatbázis-kiszolgálót bármikor visszaállíthatja a biztonsági másolatok megőrzési időszakán belüli bármely időpontra. A helyreállítás ideje a visszaállítani kívánt adatok méretétől és a napló-helyreállítás elvégzéséhez szükséges időtől függ. További részletekért tekintse meg a [fogalmak – biztonsági mentés és visszaállítás](./concepts-backup-restore.md) című témakört. |A biztonsági mentési adatterületek a régión belül maradnak |
| **Helyi redundáns biztonsági mentés** | A rugalmas kiszolgálói biztonsági mentések automatikusan és biztonságosan tárolódnak egy helyi redundáns tárolóban egy adott régióban és ugyanazon rendelkezésre állási zónában. A helyileg redundáns biztonsági másolatok háromszor replikálják a kiszolgáló biztonsági mentési adatfájljait az elsődleges régió egyetlen fizikai helyén belül. A helyileg redundáns biztonsági mentési tár legalább 99,999999999%-os (11 kilenc) tartósságot biztosít az objektumok számára egy adott évben. További részletekért tekintse meg a [fogalmak – biztonsági mentés és visszaállítás](./concepts-backup-restore.md) című témakört.| Minden régióban alkalmazható |
| **Zóna redundáns magas rendelkezésre állása** | A rugalmas kiszolgáló magas rendelkezésre állású módban is üzembe helyezhető, amely az elsődleges és a készenléti kiszolgálókat két különböző rendelkezésre állási zónában helyezi üzembe a régión belül. Ez védelmet biztosít a zóna szintű hibáktól, és a tervezett és nem tervezett leállási események során csökkenti az alkalmazások leállását is. Az elsődleges kiszolgálóról származó adatok szinkron módon replikálódnak a készenléti replikára. Minden leállás esetén az adatbázis-kiszolgáló automatikusan feladatátvételt végez a készenléti replikára. További részletekért tekintse meg a [fogalmakat – magas rendelkezésre állást](./concepts-high-availability.md) . | Az általános célú és a memóriára optimalizált számítási szintek támogatottak. Csak olyan régiókban érhető el, ahol több zóna is elérhető.|
| **Prémium fájlmegosztás** | Az adatbázisfájlok tárolása egy nagyon tartós és megbízható Azure Premium-fájlmegosztás formájában történik, amely adatredundanciát biztosít, és egy rendelkezésre állási zónában tárolt replika három másolatával rendelkezik, és automatikus adathelyreállítási képességekkel rendelkezik. További részletekért tekintse meg a [Premium file shares](../../storage/files/storage-how-to-create-file-share.md) című témakört. | Egy rendelkezésre állási zónán belül tárolt adatértékek |

> [!IMPORTANT]
> Az előzetes verzió ideje alatt a RTO és a RPO SLA nem érhető el. Az ezen a lapon megadott adatok csak az Ön adatait és tervezési célját tartalmazzák.

## <a name="planned-downtime-mitigation"></a>Tervezett leállás-csökkentés

Íme néhány, az állásidőt okozó tervezett karbantartási forgatókönyv:

| **Forgatókönyv** | **Folyamat**|
| :------------ | :----------- |
| **Számítási skálázás (felhasználó)**| A számítási skálázási művelet végrehajtásakor a rendszer egy új rugalmas kiszolgálót épít ki a méretezett számítási konfiguráció használatával. A meglévő adatbázis-kiszolgálón engedélyezve van az aktív ellenőrzőpontok befejezése, az ügyfélkapcsolatok kiürítése, a nem véglegesített tranzakciók megszakítása, majd leállítása. Ezután az új kiszolgálóhoz csatolja a tárolót, és az adatbázis elindul, amely szükség esetén helyreállítást hajt végre, mielőtt elfogadná az ügyfélkapcsolatokat. |
| **Új szoftverek központi telepítése (Azure)** | Az új szolgáltatások bevezetése vagy hibajavítások automatikusan megtörténnek a szolgáltatás tervezett karbantartásának részeként, és a tevékenységek megtervezése után is ütemezhetők. További információkért lásd a [dokumentációt](https://aka.ms/servicehealthpm), és ellenőrizze a [portált](https://aka.ms/servicehealthpm) is |
| **Másodlagos verziófrissítések (Azure)** | Azure Database for MySQL az adatbázis-kiszolgálókat az Azure által meghatározott alverzióra automatikusan kijavításra kerül. A szolgáltatás tervezett karbantartásának részeként történik. Ez rövid állásidőt von maga után másodpercben, és az adatbázis-kiszolgáló automatikusan újraindul az új alverzióval. További információkért tekintse meg a [dokumentációt](../concepts-monitoring.md#planned-maintenance-notification), és ellenőrizze a [portált](https://aka.ms/servicehealthpm)is.|

Ha a rugalmas kiszolgáló a **zóna redundánsan magas rendelkezésre állásával** van konfigurálva, a rugalmas kiszolgáló először a készenléti kiszolgálón hajt végre műveleteket, majd az elsődleges kiszolgálón feladatátvétel nélkül. További részletekért tekintse meg a [fogalmakat – magas rendelkezésre állást](./concepts-high-availability.md) .

## <a name="unplanned-downtime-mitigation"></a>Nem tervezett leállás-csökkentés

A nem tervezett leállások az előre nem látható hibák miatt előfordulhatnak, beleértve a mögöttes hardveres hibákat, a hálózati problémákat és a szoftver hibáit. Ha az adatbázis-kiszolgáló váratlanul leáll, ha magas rendelkezésre állású [HA] van konfigurálva, akkor a készenléti replika aktiválva lesz. Ha nem, akkor a rendszer automatikusan kiépít egy új adatbázis-kiszolgálót. A nem tervezett állásidőt nem lehet elkerülni, a rugalmas kiszolgáló csökkenti az állásidőt a helyreállítási műveletek automatikus végrehajtásával az adatbázis-kiszolgálón és a tárolási rétegeken anélkül, hogy emberi beavatkozásra lenne szükség.

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Nem tervezett leállás: meghibásodási forgatókönyvek és szolgáltatás-helyreállítás

Íme néhány nem tervezett meghibásodási forgatókönyv és a helyreállítási folyamat:

| **Forgatókönyv** | **Helyreállítási folyamat [nem HA]** | **Helyreállítási folyamat [HA]** |
| :---------- | ---------- | ------- |
| **Adatbázis-kiszolgáló meghibásodása** | Ha az adatbázis-kiszolgáló valamilyen mögöttes hardverhiba miatt leáll, a rendszer elveti az aktív kapcsolatokat, és minden fedélzeti tranzakciót megszakít. Az Azure megkísérli az adatbázis-kiszolgáló újraindítását. Ha ez sikeres, akkor a rendszer az adatbázis helyreállítását hajtja végre. Ha az újraindítás meghiúsul, az adatbázis-kiszolgáló egy másik fizikai csomóponton kísérli meg a rendszer újraindítását.  <br /> <br /> A helyreállítási idő (RTO) függ a különböző tényezőktől, például a hiba időpontjában felmerülő tevékenységtől, például a nagy tranzakciótól, valamint az adatbázis-kiszolgáló indítási folyamata során elvégzendő helyreállítás mennyiségétől. <br /> <br /> A MySQL-adatbázisokat használó alkalmazásokat úgy kell létrehozni, hogy azok felderítsék és újra elhagyják a kapcsolatokat és a sikertelen tranzakciókat.  Az alkalmazás újrapróbálkozásakor a kapcsolatok az újonnan létrehozott adatbázis-kiszolgálóra lesznek irányítva. | Ha a rendszer az adatbázis-kiszolgáló meghibásodását észleli, a készenléti adatbázis kiszolgálója aktiválva lesz, ami csökkenti az állásidőt. További részletekért tekintse meg a [Ha Concepts oldalt](concepts-high-availability.md) . A RTO várhatóan 60-120 s, RPO = 0 |
| **Tárolási hiba** | Az alkalmazások nem érintik a tárterülettel kapcsolatos problémákat, például a lemez meghibásodását vagy a fizikai blokk sérülését. Mivel az adattárolás 3 példányban történik, az adatmásolatot a túlélő tároló kézbesíti. A rendszer automatikusan kijavítja a blokkolási hibákat. Ha a rendszer elveszi az adatmásolatot, a rendszer automatikusan létrehozza az adatgyűjtés új másolatát. | A nem helyreállítható hibák esetén a rugalmas kiszolgálót a rendszer átadja a készenléti replikának az állásidő csökkentése érdekében. További részletekért tekintse meg a [Ha Concepts oldalt](./concepts-high-availability.md) . |
| **Logikai/felhasználói hibák** | A felhasználói hibákból, például a véletlenül eldobott táblákból vagy a helytelenül frissített adatokból történő helyreállításhoz az adott [időponthoz tartozó helyreállítást](concepts-backup-restore.md) (PITR) kell végrehajtania az adatok visszaállításával és helyreállításával egészen a hiba előtt.<br> <br>  Ha csak adatbázisok vagy meghatározott táblák egy részhalmazát szeretné visszaállítani az adatbázis-kiszolgáló összes adatbázisa helyett, akkor az adatbázis-kiszolgálót visszaállíthatja egy új példányban, majd exportálhatja a táblázat (oka) t [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html), majd a [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) használatával visszaállíthatja ezeket a táblákat az adatbázisba. | Ezek a felhasználói hibák nem védik a magas rendelkezésre állást, mert az összes felhasználói művelet a készenléti állapotba is replikálódik. |
| **Rendelkezésre állási zóna hibája** | Habár ritka eseményről van szó, ha egy zóna szintű meghibásodás miatt szeretné helyreállítani a helyreállítást, akkor a biztonsági mentéssel időponthoz tartozó helyreállítást hajthat végre, és az egyéni visszaállítási pontot választva a legfrissebb adatokhoz juthat. Egy új rugalmas kiszolgáló lesz üzembe helyezve egy másik zónában. A visszaállításhoz szükséges idő az előző biztonsági mentéstől és a visszaállítani kívánt tranzakciónaplók számától függ. | A rugalmas kiszolgáló automatikus feladatátvételt hajt végre a készenléti helyen. További részletekért tekintse meg a [Ha Concepts oldalt](./concepts-high-availability.md) . |
| **Régió meghibásodása** | A régiók közötti replika és a Geo-visszaállítási funkciók még nem támogatottak az előzetes verzióban. | |

> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. Az [Azure erőforrás-zárolás](../../azure-resource-manager/management/lock-resources.md) segítségével megakadályozhatja a kiszolgáló véletlen törlését.

## <a name="next-steps"></a>Következő lépések

- További információ a [zónák redundáns magas rendelkezésre állásáról](./concepts-high-availability.md)
- Tudnivalók a [biztonsági mentésről és a helyreállításról](./concepts-backup-restore.md)