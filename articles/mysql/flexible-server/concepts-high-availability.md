---
title: A zóna redundáns, magas rendelkezésre állásának áttekintése Azure Database for MySQL rugalmas kiszolgálóval
description: Ismerje meg a zóna redundáns magas rendelkezésre állásának fogalmait Azure Database for MySQL rugalmas kiszolgálóval
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: f01a0869f7786ee6197835610456f4bb1cbd6b03
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097117"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Magas rendelkezésre állási fogalmak Azure Database for MySQL rugalmas kiszolgálóban (előzetes verzió)

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Azure Database for MySQL rugalmas kiszolgáló (előzetes verzió) lehetővé teszi a magas rendelkezésre állás konfigurálását automatikus feladatátvételsel a **zóna redundáns** magas rendelkezésre állási lehetőségével. Amikor zónaredundáns konfigurációban van üzembe helyezve, a rugalmas kiszolgáló automatikusan kiépít és felügyel egy készenléti replikát egy másik rendelkezésreállási zónában. A tárolási szintű replikáció használatával a rendszer **szinkron módon replikálja** az adatátvitelt a másodlagos zónában lévő készenléti kiszolgálóra, hogy a feladatátvétel után zéró adatvesztést engedélyezzen. A feladatátvétel teljesen átlátszó az ügyfélalkalmazás számára, és nincs szükség felhasználói műveletekre. A készenléti kiszolgáló semmilyen olvasási vagy írási művelethez nem érhető el, de a gyors feladatátvétel engedélyezéséhez passzív készenléti állapotban van. A feladatátvételi idők jellemzően 60-120 másodperctől eltérnek.

A zóna redundáns magas rendelkezésre állási konfigurációja lehetővé teszi az automatikus feladatátvételt a tervezett események, például a felhasználó által kezdeményezett méretezési műveletek, valamint a nem tervezett események, például a mögöttes hardver-és szoftver-hibák, a hálózati hibák és a rendelkezésre állási zónák meghibásodása esetén.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="a zóna redundáns magas rendelkezésre állásának megtekintése":::

## <a name="zone-redundancy-architecture"></a>Zóna redundancia-architektúrája

Az elsődleges kiszolgáló üzembe helyezése a régióban és egy adott rendelkezésre állási zónában történik. Ha a magas rendelkezésre állást választja, az elsődleges kiszolgálóval megegyező konfigurációval rendelkező készenléti replika kiszolgáló automatikusan települ, beleértve a számítási szintet, a számítási méretet, a tárterület méretét és a hálózati konfigurációt. A rendszer szinkron módon replikálja a naplózási adatkészletet a készenléti replikába, hogy az adatvesztés ne legyen a meghibásodási helyzetben. Az automatikus biztonsági másolatok, a pillanatképek és a naplók biztonsági mentése is az elsődleges adatbázis-kiszolgálóról történik. 

A HA állapotát folyamatosan figyelik és jelentik az Áttekintés oldalon.

A különböző replikációs állapotok az alábbiakban láthatók:

| **Állapot** | **Leírás** |
| :----- | :------ |
| <b>Nem betartható | A zóna redundáns, HA nincs engedélyezve |
| <b>CreatingStandby | Új készenléti állapot létrehozásának folyamata |
| <b>ReplicatingData | A készenlét létrehozása után a rendszer felveszi az elsődleges kiszolgálóval. |
| <b>FailingOver | Az adatbázis-kiszolgáló abban a folyamatban van, hogy feladatátvételt hajt végre az elsődlegesről a készenléti állapotra. |
| <b>Kifogástalan | A zóna redundáns, HA állandó állapotban van, és kifogástalan állapotú. |
| <b>RemovingStandby | A felhasználói műveletek alapján a készenléti replika törlési folyamatban van.| 

## <a name="advantages"></a>Előnyök

Íme néhány előny a zóna-redundancia-szolgáltatás használatához: 

-   A készenléti replikát a rendszer egy pontos virtuálisgép-konfigurációban telepíti, mint például a virtuális mag, a Storage, a hálózati beállítások (VNET, tűzfal) stb.
-   A magas rendelkezésre állás letiltásával lehetősége van a készenléti replika eltávolítására.
-   Az automatikus biztonsági mentések pillanatkép-alapúak, amelyeket az elsődleges adatbázis-kiszolgálóról végeznek el, és amelyeket egy zóna redundáns tárolóban tárolnak.
-   Feladatátvétel esetén Azure Database for MySQL a rugalmas kiszolgáló automatikusan feladatátvételt hajt végre a készenléti replikára, ha a magas rendelkezésre állás engedélyezve van. A magas rendelkezésre állás beállítása figyeli az elsődleges kiszolgálót, és ismét online állapotba helyezi.
-   Az ügyfelek mindig az elsődleges adatbázis-kiszolgálóhoz csatlakoznak.
-   Ha van adatbázis-összeomlási vagy csomópont-meghibásodás, az újraindítást a rendszer először ugyanazon a csomóponton kísérli meg. Ha ez nem sikerül, az automatikus feladatátvétel aktiválódik.
-   A kiszolgáló újraindításának lehetősége a statikus kiszolgálói paraméterek módosításainak kiválasztásához.

## <a name="steady-state-operations"></a>Állandó állapotú műveletek

Az alkalmazások az adatbázis-kiszolgáló neve alapján csatlakoznak az elsődleges kiszolgálóhoz. A készenléti replika információi nem érhetők el közvetlen hozzáférés esetén. A commit és az írásokat az alkalmazás csak akkor fogadja el, ha a naplófájlokat az elsődleges kiszolgáló lemezén és a készenléti replikán szinkron módon megőrzi. Ennek a további kerekítési követelménynek köszönhetően az alkalmazások emelt szintű késést várhatnak az írások és a véglegesítés során. A portálon ellenőrizheti a magas rendelkezésre állás állapotát.

## <a name="failover-process"></a>Feladatátvételi folyamat 
Az üzletmenet folytonossága érdekében feladatátvételi folyamatra van szükség a tervezett és nem tervezett eseményekhez. 

### <a name="planned-events"></a>Tervezett események

A tervezett leállási események közé tartoznak az Azure által ütemezett tevékenységek, például az időszakos szoftverfrissítések, az alverziók frissítése vagy az ügyfelek által kezdeményezett tevékenységek, például a számítási és a skálázási tárolási műveletek. Ezeket a módosításokat először a készenléti replikára alkalmazza a rendszer. Ebben az időszakban az alkalmazások továbbra is hozzáférnek az elsődleges kiszolgálóhoz. A készenléti replika frissítésekor a rendszer kiüríti az elsődleges kiszolgáló kapcsolatait, és a rendszer feladatátvételt indít el, amely aktiválja a készenléti replikát, hogy az elsődleges legyen ugyanazzal az adatbázis-kiszolgálóval a DNS-rekord frissítésekor. Az ügyfélkapcsolatok le vannak választva, és újra kell csatlakozniuk, és folytatni tudják a műveleteiket. Az új készenléti kiszolgáló a régi elsődlegesvel megegyező zónában lesz létrehozva. A teljes feladatátvételi idő várhatóan 60-120 s. 

>[!NOTE]
> A számítási skálázási művelet esetén a másodlagos másodpéldány-kiszolgálót, majd az elsődleges kiszolgálót kell méretezni. Nincs feladatátvétel.

### <a name="failover-process---unplanned-events"></a>Feladatátvételi folyamat – nem tervezett események
A nem tervezett leállások közé tartoznak azok a szoftveres hibák, amelyek vagy infrastruktúra-meghibásodások, például a számítási, hálózati, tárolási hibák vagy áramkimaradások hatással vannak az adatbázis rendelkezésre állására. Ha az adatbázis nem érhető el, a készenléti replikára történő replikáció megszakad, és a készenléti replika aktiválva lesz az elsődleges adatbázisnak. A DNS frissült, és az ügyfelek újra csatlakoznak az adatbázis-kiszolgálóhoz, és folytatják a műveleteiket. A teljes feladatátvételi idő várhatóan 60-120 s lesz. Azonban attól függően, hogy az elsődleges adatbázis-kiszolgáló milyen tevékenységgel rendelkezik a feladatátvétel során, például a nagy tranzakciókat és a helyreállítási időt, a feladatátvétel hosszabb időt vehet igénybe.

## <a name="schedule-maintenance-window"></a>Karbantartási időszak ütemezett időpontja 

A rugalmas kiszolgálók karbantartási ütemezési képességet biztosítanak, melyben egy 30 perces időszakot választhat egy olyan napon, amelyben az Azure-karbantartási munkák, például a javítások vagy az alverziók frissítései is megtörténnek. A magas rendelkezésre állással konfigurált rugalmas kiszolgálók esetében a karbantartási tevékenységeket először a készenléti replikán kell végrehajtani. 

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás 
Mivel a rugalmas kiszolgáló magas rendelkezésre állású szinkronban van konfigurálva, a készenléti kiszolgáló az elsődlegesnél naprakész lesz. Az elsődleges felhasználói hibák – például egy tábla véletlen eldobása vagy a helytelen frissítések – a készenléti állapotba kerülnek. Ezért nem használhat készenléti állapotot az ilyen logikai hibák helyreállításához. Ha ezeket a logikai hibákat szeretné helyreállítani, a hiba előtti időpontra vonatkozó visszaállítást kell végrehajtania. A rugalmas kiszolgáló időponthoz való visszaállításának lehetősége, ha a magas rendelkezésre állással konfigurált adatbázist állítja vissza, egy új adatbázis-kiszolgáló lesz visszaállítva új, felhasználó által megadott névvel rendelkező rugalmas kiszolgálóként. Ezután exportálhatja az objektumot az adatbázis-kiszolgálóról, és importálhatja az éles adatbázis-kiszolgálóra. Hasonlóképpen, ha tesztelési és fejlesztési célokra szeretné összeállítani az adatbázis-kiszolgálót, vagy bármilyen más célra szeretné visszaállítani, akkor választhatja a legújabb visszaállítási pontot vagy egy egyéni visszaállítási pontot. A visszaállítási művelet egyetlen zónában lévő rugalmas kiszolgálót fog létrehozni.

## <a name="mitigate-downtime"></a>Állásidő csökkentése 
Ha nem használja a zóna-redundancia szolgáltatást, továbbra is képesnek kell lennie az alkalmazás leállásának enyhítésére. A szolgáltatás leállásának megtervezése, például az ütemezett javítások, az alverziók frissítése vagy a felhasználó által kezdeményezett műveletek az ütemezett karbantartási időszakok részeként végezhetők el. A tervezett leállások, például az ütemezett javítások, az alverziók frissítése vagy a felhasználó által kezdeményezett műveletek, például a számítási erőforrások leállásának skálázása. Az Azure által kezdeményezett karbantartási feladatokra gyakorolt hatás enyhítése érdekében dönthet úgy, hogy a hét napján és a legkevésbé hatással van az alkalmazásra. 

A nem tervezett leállási események (például az adatbázis-összeomlás vagy a kiszolgálóhiba) során az érintett kiszolgáló újraindul ugyanazon a zónán belül. Bár ritkán fordul elő, ha a teljes zónára hatással van, visszaállíthatja az adatbázist a régión belüli másik zónában. 

## <a name="things-to-know-with-zone-redundancy"></a>A zónák redundanciával kapcsolatos tudnivalók 

Az alábbiakban néhány megfontolandó szempontot figyelembe kell venni a zóna redundancia magas rendelkezésre állásának használatakor: 

-   A magas rendelkezésre állás **csak** a rugalmas kiszolgáló létrehozási ideje alatt állítható be.
-   A magas rendelkezésre állás nem támogatott a feltört számítási szinteken.
-   Egy másik rendelkezésre állási zónához való szinkron replikálás miatt az elsődleges adatbázis-kiszolgáló emelt szintű írási és végrehajtási késést tapasztalhat.
-   A készenléti replika nem használható csak olvasható lekérdezésekhez.
-   Az elsődleges kiszolgálón a feladatátvétel időpontjában végzett tevékenységtől függően akár 60-120 másodpercig vagy hosszabb ideig is eltarthat, amíg a feladatátvétel befejeződik.
-   A statikus paraméterek kiválasztására szolgáló elsődleges adatbázis-kiszolgáló újraindítása szintén újraindítja a készenléti replikát.
-   Az olvasási replikák a zóna redundáns magas rendelkezésre állású kiszolgálókhoz való konfigurálása nem támogatott.
-   Az ügyfél által kezdeményezett felügyeleti feladatok konfigurálása nem automatizálható a felügyelt karbantartási időszak alatt.
-   A tervezett események, például a méretezési számítások és az alverziók frissítései az elsődleges és a készenléti állapotban is megtörténnek. 


## <a name="next-steps"></a>Következő lépések

-   Az [üzletmenet folytonosságának](./concepts-business-continuity.md) megismerése
-   További információ a [zónák redundáns magas rendelkezésre állásáról](./concepts-high-availability.md)
-   Tudnivalók a [biztonsági mentésről és a helyreállításról](./concepts-backup-restore.md)
