---
title: Az üzletmenet folytonosságának áttekintése Azure Database for PostgreSQL-rugalmas kiszolgálóval
description: Ismerje meg a Azure Database for PostgreSQL rugalmas kiszolgálóval folytatott Üzletmenet-folytonosság fogalmait
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 341d3c6f0a2d84c2b7b725959db9f7e5dd80d3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92542964"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---flexible-server"></a>Az üzletmenet folytonosságának áttekintése Azure Database for PostgreSQL-rugalmas kiszolgálóval

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Az **üzletmenet folytonossága** Azure Database for PostgreSQL rugalmas kiszolgálókon olyan mechanizmusokra, szabályzatokra és eljárásokra utal, amelyek lehetővé teszik a vállalat számára, hogy a fennakadások miatt továbbra is működőképes legyen, különösen a számítástechnikai infrastruktúrában. A legtöbb esetben a rugalmas kiszolgáló kezeli a zavaró eseményeket, amelyek a Felhőbeli környezetben történnek, és az alkalmazások és az üzleti folyamatok továbbra is futnak. Vannak azonban olyan események, amelyek nem kezelhetők automatikusan, például:

- A felhasználó véletlenül töröl vagy frissít egy sort egy táblában.
- A földrengés áramkimaradást okoz, és ideiglenesen letiltja egy adatközpontot vagy egy rendelkezésre állási zónát.
- Hiba vagy biztonsági probléma elhárításához szükséges adatbázis-javítás.

A rugalmas kiszolgáló olyan szolgáltatásokat biztosít, amelyek az adatvédelmet és a kritikus fontosságú adatbázisok leállását csökkentik a tervezett és nem tervezett leállási események esetén. Az olyan Azure-infrastruktúrára épül, amely már robusztus rugalmasságot és rendelkezésre állást biztosít, a rugalmas kiszolgáló olyan üzletmenet-folytonossági funkciókkal rendelkezik, amelyek további hibatűrést, a helyreállítási idő követelményeit és az adatvesztések csökkentését teszik lehetővé. Az alkalmazások megtervezése során figyelembe kell vennie az állásidő tűréshatárát – ez a helyreállítási időre vonatkozó célkitűzés (RTO) és az adatvesztési kitettség –, amely a helyreállítási pont célkitűzése (RPO). Az üzleti szempontból kritikus fontosságú adatbázis esetében például a tesztelési adatbázishoz képest sokkal szigorúbb üzemidőre van szükség.  

> [!IMPORTANT]
> Az előzetes verzióban nem érhető el a (z)% szolgáltatói szerződés (SLA). 

Az alábbi táblázat a rugalmas kiszolgáló által kínált funkciókat mutatja be.


| **Szolgáltatás** | **Leírás** | **Megfontolások** |
| ---------- | ----------- | ------------ |
| **Automatikus biztonsági mentések** | A rugalmas kiszolgáló automatikusan elvégzi az adatbázisfájlok napi biztonsági mentését, és folyamatosan készíti el a tranzakciónaplókat. A biztonsági mentések 7 napig, akár 35 napig is megmaradnak. Az adatbázis-kiszolgálót bármikor visszaállíthatja a biztonsági másolatok megőrzési időszakán belüli bármely időpontra. A RTO a visszaállítani kívánt adatok méretétől és a naplók helyreállításának idejétől függ. Akár 12 óráig is eltarthat. További részletekért lásd: [fogalmak – biztonsági mentés és visszaállítás](./concepts-backup-restore.md). |A biztonsági mentési adatterületek a régión belül maradnak. |
| **Zóna redundáns magas rendelkezésre állása** | A rugalmas kiszolgáló a zóna redundáns magas rendelkezésre állású (HA) konfigurációjában helyezhető üzembe, amelyben az elsődleges és a készenléti kiszolgálók két különböző rendelkezésre állási zónában vannak üzembe helyezve egy adott régióban. Ez a HA-konfiguráció védelmet nyújt az adatbázisai számára a zóna szintű hibáktól, és a tervezett és nem tervezett leállási események során csökkenti az alkalmazások leállását is. Az elsődleges kiszolgálóról származó adatok szinkron módban vannak replikálva a készenléti replikába. Az elsődleges kiszolgáló megszakítása esetén a kiszolgáló automatikusan feladatátvételt végez a készenléti replikára. A legtöbb esetben a RTO a várt érték 60 – 120s. A RPO várhatóan nulla (Nincs adatvesztés). További információ: [fogalmak – magas rendelkezésre állás](./concepts-high-availability.md). | Az általános célú és a memóriára optimalizált számítási szintek támogatottak. Csak olyan régiókban érhető el, ahol több zóna is elérhető. |
| **Prémium szintű Managed Disks** | Az adatbázisfájlok tárolása egy nagyon tartós és megbízható, prémium szintű felügyelt tárolóban történik. Ez adatredundanciát biztosít a rendelkezésre állási zónában tárolt replikák három másolatával az automatikus adathelyreállítási funkciókkal. További információ: [Managed Disks – dokumentáció](../../virtual-machines/managed-disks-overview.md). | Egy rendelkezésre állási zónán belül tárolt adatértékek. |
| **Zóna redundáns biztonsági mentése** | A rugalmas kiszolgáló biztonsági mentését a rendszer automatikusan és biztonságosan tárolja egy adott régióban található redundáns tárolási zónában. A zóna szintjének meghibásodása esetén, amikor a kiszolgálót kiépítik, és ha a kiszolgáló nem a zóna redundancia beállítással van konfigurálva, akkor továbbra is visszaállíthatja az adatbázist egy másik zóna legújabb visszaállítási pontjának használatával. További információ: [fogalmak – biztonsági mentés és visszaállítás](./concepts-backup-restore.md).| Csak olyan régiókban alkalmazható, ahol több zóna is elérhető.|


## <a name="planned-downtime-events"></a>Tervezett leállás eseményei
Az alábbiakban néhány tervezett karbantartási forgatókönyvet talál. Ezek az események általában akár néhány percet is igénybe vehetnek, az adatvesztés nélkül.

| **Forgatókönyv** | **Folyamat**|
| ------------------- | ----------- | 
| <b>Számítási skálázás (felhasználó által kezdeményezett)| A számítási skálázási művelet során az aktív ellenőrzőpontok befejeződik, az ügyfélkapcsolatok kiürítése, a nem véglegesített tranzakciók megszakítása, a tároló leválasztása, majd leállítása. Egy új, azonos adatbázis-kiszolgáló nevű rugalmas kiszolgáló van kiépítve a méretezett számítási konfigurációval. Ezután az új kiszolgálóhoz csatolja a tárolót, és az adatbázis elindul, amely szükség esetén helyreállítást hajt végre, mielőtt elfogadná az ügyfélkapcsolatokat. |
| <b>Tárterület felskálázása (felhasználó által kezdeményezett) | A skálázási tárolási művelet elindítását követően az aktív ellenőrzőpontok befejeződik, és az ügyfélkapcsolatok ki vannak ürítve, a nem véglegesített tranzakciók megszakadnak, majd leállnak. A tároló méretezése a kívánt méretre, majd az új kiszolgálóhoz csatolva. Ha az ügyfélkapcsolatok elfogadása előtt szükség van A helyreállításra. Vegye figyelembe, hogy a tárolási méret leskálázása nem támogatott. |
| <b>Új szoftverek központi telepítése (Azure által kezdeményezett) | Az új szolgáltatások bevezetése vagy hibajavítások automatikusan megtörténnek a szolgáltatás tervezett karbantartásának részeként, és a tevékenységek megtervezése után is ütemezhetők. További információkért keresse fel a [portált](https://aka.ms/servicehealthpm). | 
| <b>Másodlagos verziófrissítések (Azure által kezdeményezett) | Azure Database for PostgreSQL az adatbázis-kiszolgálókat az Azure által meghatározott alverzióra automatikusan kijavításra kerül. A szolgáltatás tervezett karbantartásának részeként történik. Az adatbázis-kiszolgáló automatikusan újraindul az új alverzióval. További információ: [dokumentáció](../concepts-monitoring.md#planned-maintenance-notification). Megtekintheti a [portált](https://aka.ms/servicehealthpm)is.| 

 Ha a rugalmas kiszolgáló **magas rendelkezésre állású zónával** van konfigurálva, a rugalmas kiszolgáló először a készenléti kiszolgálón hajtja végre a skálázást és a karbantartási műveleteket. További információ: [fogalmak – magas rendelkezésre állás](./concepts-high-availability.md).

##  <a name="unplanned-downtime-mitigation"></a>Nem tervezett leállás-csökkentés

A nem tervezett leállások olyan váratlan megszakítások miatt fordulhatnak elő, mint például a mögöttes hardverhiba, a hálózati problémák és a szoftverek hibái. Ha a magas rendelkezésre állású adatbázis-kiszolgáló váratlanul leáll, a rendszer aktiválja a készenléti replikát, és az ügyfelek folytatják a műveleteiket. Ha nincs magas rendelkezésre állású (ha) konfigurálva, akkor ha az újraindítási kísérlet meghiúsul, akkor a rendszer automatikusan kiépít egy új adatbázis-kiszolgálót. A nem tervezett állásidőt nem lehet elkerülni, a rugalmas kiszolgáló segít csökkenteni az állásidőt a helyreállítási műveletek automatikus elvégzésével anélkül, hogy emberi beavatkozásra lenne szükség. 
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Nem tervezett leállás: meghibásodási forgatókönyvek és szolgáltatás-helyreállítás
Az alábbiakban néhány nem tervezett meghibásodási forgatókönyv és a helyreállítási folyamat látható. 

| **Forgatókönyv** | **Helyreállítási folyamat [nem HA]** | **Helyreállítási folyamat [HA]** |
| ---------- | ---------- | ------- |
| <B>Adatbázis-kiszolgáló meghibásodása | Ha az adatbázis-kiszolgáló nem érhető el, az Azure megkísérli az adatbázis-kiszolgáló újraindítását. Ha ez nem sikerül, az adatbázis-kiszolgáló egy másik fizikai csomóponton indul újra.  <br /> <br /> A helyreállítási idő (RTO) függ a különböző tényezőktől, például a meghibásodás időpontjában végzett tevékenységtől, például a nagy tranzakciótól, valamint az adatbázis-kiszolgáló indítási folyamata során végrehajtandó helyreállítás mennyiségétől. <br /> <br /> A PostgreSQL-adatbázisokat használó alkalmazásokat úgy kell létrehozni, hogy felderítsék és újra elhagyják a kapcsolatokat és a sikertelen tranzakciókat. | Ha az adatbázis-kiszolgáló meghibásodása észlelhető, a kiszolgáló feladatátvételt végez a készenléti kiszolgálóval, így csökkentve az állásidőt. További információ: [Ha fogalmak oldal](./concepts-high-availability.md). A RTO várhatóan 60 120s, és nulla adatvesztéssel jár. |
| <B>Tárolási hiba | Az alkalmazások nem érintik a tárterülettel kapcsolatos problémákat, például a lemez meghibásodását vagy a fizikai blokk sérülését. Mivel az adatmennyiség három másolatban tárolódik, az adatmásolatot a túlélő tároló tárolja. A rendszer automatikusan kijavítja a sérült adatblokkot, és automatikusan létrehozza az adatgyűjtés új másolatát. | A ritka és a nem helyreállítható hibák, például a teljes tárterület nem érhető el, a rugalmas kiszolgáló feladatátvétele a tartalék replikára történik, hogy csökkentse az állásidőt. További információ: [Ha fogalmak oldal](./concepts-high-availability.md). |
| <b> Logikai/felhasználói hibák | A felhasználói hibáktól, például a véletlenül eldobott tábláktól vagy a helytelenül frissített adatoktól való helyreállításhoz [időponthoz tartozó helyreállítást](../concepts-backup.md) (PITR) kell végrehajtania. A visszaállítási művelet végrehajtása közben adja meg az egyéni visszaállítási pontot, amely a hiba előtti idő.<br> <br>  Ha csak adatbázisok vagy meghatározott táblák egy részhalmazát szeretné visszaállítani az adatbázis-kiszolgáló összes adatbázisa helyett, akkor az adatbázis-kiszolgálót visszaállíthatja egy új példányban, majd exportálhatja a táblázat (oka) t [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html), majd a [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) használatával visszaállíthatja ezeket a táblákat az adatbázisba. | Ezek a felhasználói hibák nem védik a magas rendelkezésre állást, mivel a rendszer szinkron módon replikálja az összes módosítást a készenléti replikára. Az ilyen hibák helyreállításához az időponthoz tartozó visszaállítást kell végrehajtani. |
| <b> Rendelkezésre állási zóna hibája | A zóna szintű hibákból való helyreállításhoz a biztonsági mentés használatával időponthoz való visszaállítást hajthat végre, és kiválaszthat egy egyéni visszaállítási pontot a legújabb adatok visszaállításához. Egy új rugalmas kiszolgáló egy másik, nem érintett zónában lesz telepítve. A visszaállításhoz szükséges idő az előző biztonsági mentéstől és a helyreállítható tranzakciónaplók mennyiségétől függ. | A rugalmas kiszolgálót a rendszer automatikusan átadja a készenléti kiszolgálónak a 120s-n belül, nulla adatvesztéssel. További információ: [Ha fogalmak oldal](./concepts-high-availability.md). | 
| <b> Régió meghibásodása | A többrégiós olvasási replika és a biztonsági mentési funkciók geo-visszaállítása még nem támogatott az előzetes verzióban. | |


> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. Az [Azure erőforrás-zárolás](../../azure-resource-manager/management/lock-resources.md) segítségével megakadályozhatja a kiszolgáló véletlen törlését.


## <a name="next-steps"></a>Következő lépések

-   További információ a [zónák redundáns magas rendelkezésre állásáról](./concepts-high-availability.md)
-   Tudnivalók a [biztonsági mentésről és a helyreállításról](./concepts-backup-restore.md)