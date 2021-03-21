---
title: A zóna redundáns, magas rendelkezésre állásának áttekintése Azure Database for PostgreSQL rugalmas kiszolgálóval (előzetes verzió)
description: Ismerje meg a zóna redundáns magas rendelkezésre állásának fogalmait Azure Database for PostgreSQL rugalmas kiszolgálóval
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: c0d9b6042ae695caa73d926653f237b756bf4971
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94366723"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Magas rendelkezésre állási fogalmak Azure Database for PostgreSQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Azure Database for PostgreSQL – a rugalmas kiszolgáló magas rendelkezésre állású konfigurációt biztosít, amely automatikus feladatátvételi funkcióval rendelkezik a **zóna redundáns** kiszolgálójának üzembe helyezésével. Amikor zónaredundáns konfigurációban van üzembe helyezve, a rugalmas kiszolgáló automatikusan kiépít és felügyel egy készenléti replikát egy másik rendelkezésreállási zónában. A PostgreSQL streaming Replication használatával a rendszer **szinkron** módban replikálja az adatfájlokat a készenléti replika-kiszolgálóra. 

A zóna redundáns konfigurációja lehetővé teszi az automatikus feladatátvételi képességet, amely nulla adatvesztést biztosít a tervezett események, például a felhasználó által kezdeményezett méretezési számítási művelet, valamint a nem tervezett események (például a mögöttes hardver-és szoftver-hibák, a hálózati hibák és a rendelkezésre állási zónák) során. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="zóna redundáns magas rendelkezésre állása"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Zóna redundáns magas rendelkezésre állású architektúrája

Kiválaszthatja a régiót és rendelkezésreállási zónát, amelyben az elsődleges adatbázis kiszolgálóját üzembe szeretné helyezni. A rendszer kiépít egy készenléti replikát egy másik rendelkezésreállási zónában az elsődleges kiszolgáló konfigurációjával, beleértve a számítási szintet, a számítási méretet, a tárolóméretet és a hálózati konfigurációt. A tranzakciós naplók szinkron módban vannak replikálva a készenléti replikára a PostgreSQL streaming Replication használatával. Az automatikus biztonsági mentések rendszeres időközönként az elsődleges adatbázis-kiszolgálóról történik, míg a tranzakciós naplókat a rendszer folyamatosan archiválja a tartalék replikából a biztonsági mentési tárolóba. 

A magas rendelkezésre állási konfiguráció állapotát folyamatosan figyelik és jelentik a portálon. A zóna redundáns magas rendelkezésre állási állapota az alábbi listában látható:

| **Állapot** | **Leírás** |
| ------- | ------ |
| <b> Inicializálása | Új készenléti kiszolgáló létrehozásának folyamata |
| <b> Az adatreplikálás | A készenlét létrehozása után a rendszer felveszi az elsődlegest. |
| <b> Kifogástalan | A replikáció állandó állapotban van és kifogástalan állapotú. |
| <b> Feladatátvétel | Az adatbázis-kiszolgáló abban a folyamatban van, hogy feladatátvételt hajt végre. |
| <b> Készenlét eltávolítása | A készenléti kiszolgáló törlésének folyamata. | 
| <b> Nincs engedélyezve | A zóna redundáns magas rendelkezésre állása nincs engedélyezve.  |

## <a name="steady-state-operations"></a>Állandó állapotú műveletek

A PostgreSQL-ügyfélalkalmazások az adatbázis-kiszolgáló neve alapján csatlakoznak az elsődleges kiszolgálóhoz. Az alkalmazás olvasásait a rendszer közvetlenül az elsődleges kiszolgálóról kézbesíti, míg a commit és az írásokat csak akkor erősíti meg az alkalmazás, ha az adatokat az elsődleges kiszolgálón és a készenléti replikán is megőrzi. Ennek a további kerekítési követelménynek köszönhetően az alkalmazások emelt szintű késést várhatnak az írások és a véglegesítés során. A portálon ellenőrizheti a magas rendelkezésre állás állapotát.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="zóna redundáns magas rendelkezésre állása – állandósult állapot"::: 

1. Az ügyfelek a rugalmas kiszolgálóhoz csatlakoznak, és írási műveleteket hajtanak végre.
2. A módosításokat a rendszer a készenléti helyre replikálja.
3. Az elsődleges visszaigazolás fogadása.
4. Az írások és a véglegesítés el van ismerve.

## <a name="failover-process---planned-downtimes"></a>Feladatátvételi folyamat – tervezett állásidő

A tervezett állásidő események közé tartoznak az Azure ütemezett rendszeres szoftverfrissítések és az alverziók frissítései. Ha magas rendelkezésre állást konfigurál, a rendszer először a készenléti replikára alkalmazza ezeket a műveleteket, amíg az alkalmazások továbbra is hozzáférnek az elsődleges kiszolgálóhoz. A készenléti replika frissítésekor a rendszer kiüríti az elsődleges kiszolgáló kapcsolatait, és elindít egy feladatátvételt, amely aktiválja a készenléti replikát, hogy az elsődleges legyen ugyanazzal az adatbázis-kiszolgáló nevével. Az ügyfélalkalmazások újra kell csatlakozniuk ugyanazzal az adatbázis-kiszolgáló nevével az új elsődleges kiszolgálóhoz, és folytatni tudják a műveleteiket. Az új készenléti kiszolgáló a régi elsődlegesvel megegyező zónában lesz létrehozva. 

Más, felhasználó által kezdeményezett műveletek, például a méretezési számítások vagy a méretezési tárolók esetében a módosításokat a rendszer a készenléti állapotban alkalmazza, majd az elsődlegest. A kapcsolatok jelenleg nem felelnek meg a készenléti állapotnak, ezért a rendszer leállást okoz, miközben a műveletet az elsődleges kiszolgálón hajtja végre.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>A tervezett állásidő csökkentése a felügyelt karbantartási időszakmal

 Az Azure által kezdeményezett karbantartási tevékenységeket úgy ütemezhet, hogy egy 30 perces időszakot választ egy olyan napon, amelyben az adatbázisokra vonatkozó tevékenységek várhatóan alacsonyak lesznek. Az Azure-karbantartási feladatok, például a javítások vagy az alverziók frissítése az adott ablakban történnek.  A magas rendelkezésre állással konfigurált rugalmas kiszolgálók esetében a karbantartási tevékenységeket először a készenléti replikán hajtják végre, majd aktiválják. Az alkalmazások ezután újra csatlakozni tudnak az új elsődleges kiszolgálóhoz, és folytatják a műveleteiket, miközben új készenléti állapot van kiépítve.

## <a name="failover-process---unplanned-downtimes"></a>Feladatátvételi folyamat – nem tervezett leállások

A nem tervezett leállások közé tartozik például a szoftveres hibák vagy az infrastruktúra-összetevők meghibásodása, ami hatással van az adatbázis elérhetőségére. Ha a figyelési rendszer észleli a kiszolgáló elérhetőségét, a készenléti replikára történő replikáció megszakad, és a készenléti replika aktiválva lesz az elsődleges adatbázis-kiszolgálóként. Az ügyfelek ugyanazzal a kapcsolati karakterlánccal csatlakozhatnak az adatbázis-kiszolgálóhoz, és folytatják a műveleteiket. A teljes feladatátvételi idő várhatóan 60 120s lesz. Azonban attól függően, hogy az elsődleges adatbázis-kiszolgáló milyen tevékenységgel rendelkezik a feladatátvétel során, például a nagy tranzakciókat és a helyreállítási időt, a feladatátvétel hosszabb időt vehet igénybe.

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="zóna redundáns magas rendelkezésre állása – feladatátvétel"::: 

1. Az elsődleges adatbázis-kiszolgáló nem érhető el, és az ügyfelek elvesztik az adatbázis-kapcsolatot. 
2. A készenléti kiszolgáló úgy van aktiválva, hogy az új elsődleges kiszolgáló legyen. Az ügyfél ugyanazzal a kapcsolati karakterlánccal csatlakozik az új elsődleges kiszolgálóhoz. Ha az ügyfélalkalmazás ugyanabban a zónában van, mint az elsődleges adatbázis-kiszolgáló, csökkenti a késést, és javítja a teljesítményt.
3. A készenléti kiszolgáló ugyanabban a zónában van, mint a régi elsődleges kiszolgáló, és a rendszer kezdeményezi a folyamatos átviteli replikálást. 
4. Az állandó állapotú replikáció megállapítása után az ügyfélalkalmazás véglegesíti és írja az írásokat, miután az adattárolást mindkét helyen megőrzi.

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás 

A magas rendelkezésre állású rugalmas kiszolgálók esetében a rendszer valós időben replikálja az adatokból a készenléti kiszolgálót, hogy az naprakész maradjon. Az elsődleges kiszolgálón lévő összes felhasználói hiba – például egy tábla véletlen eldobása vagy helytelen Adatfrissítés – a rendszer hűségesen replikálja a készenléti replikát. Ezért nem használhat készenléti állapotot az ilyen logikai hibákból való helyreállításhoz. Az ilyen hibákból való helyreállításhoz a biztonsági másolatokból történő időpontra történő visszaállítást kell végezni.  A rugalmas kiszolgáló időponthoz tartozó visszaállítási képességének használatával a hiba előtti időre állíthatja vissza a szolgáltatást. A magas rendelkezésre állással konfigurált adatbázisok esetében az új adatbázis-kiszolgáló egy, a felhasználó által megadott névvel rendelkező, egyzónás rugalmas kiszolgálóként lesz visszaállítva. Ezután exportálhatja az objektumot az adatbázis-kiszolgálóról, és importálhatja az éles adatbázis-kiszolgálóra. Hasonlóképpen, ha tesztelési és fejlesztési célokra szeretné összeállítani az adatbázis-kiszolgálót, vagy bármilyen más célra szeretné visszaállítani, az időponthoz tartozó visszaállításokat is végrehajthatja.

## <a name="zone-redundant-high-availability---features"></a>A zóna redundáns magas rendelkezésre állása – funkciók

-   A készenléti replikát a rendszer egy pontos virtuálisgép-konfigurációban telepíti, amely megegyezik az elsődleges kiszolgálóval, beleértve a virtuális mag, a tárhelyet, a hálózati beállításokat (VNET, tűzfal) stb.

-   Magas rendelkezésre állás hozzáadása egy meglévő adatbázis-kiszolgálóhoz.

-   A magas rendelkezésre állás letiltásával lehetősége van a készenléti replika eltávolítására.

-   Lehetőség az elsődleges adatbázis-kiszolgáló rendelkezésre állási zónájának kiválasztására.

-   Az elsődleges és a készenléti adatbázis-kiszolgálók leállításának, indításának és újraindításának lehetősége.

-   Az automatikus biztonsági mentés az elsődleges adatbázis-kiszolgálóról történik, és egy zónában redundáns tárolóban tárolódik.

-   Az ügyfelek mindig az elsődleges adatbázis-kiszolgálóhoz csatlakoznak.

-   A kiszolgáló újraindításának lehetősége a statikus kiszolgálói paraméterek módosításainak kiválasztásához.
  
-   Az időszakos karbantartási tevékenységek, például az alverziók frissítései az első készenléti állapotban történnek, és a szolgáltatás átadása az állásidő csökkentése érdekében történik.  

## <a name="zone-redundant-high-availability---limitations"></a>A zóna redundáns magas rendelkezésre állása – korlátozások

-   A magas rendelkezésre állás nem támogatott a feltört számítási szint esetében.
-   A magas rendelkezésre állás csak olyan régiókban támogatott, ahol több zóna is elérhető.
-   Egy másik rendelkezésre állási zónához való szinkron replikálás miatt az alkalmazások megemelt írási és végrehajtási késéssel rendelkezhetnek.

-   A készenléti replika nem használható olvasási lekérdezésekhez.

-   Az elsődleges kiszolgáló munkaterheléstől és tevékenységtől függően a feladatátvételi folyamat 120 másodpercnél hosszabb időt is igénybe vehet.

-   Az elsődleges adatbázis-kiszolgáló újraindítása szintén újraindítja a készenléti replikát. 

-   További olvasási replikák konfigurálása nem támogatott.

-   Az ügyfél által kezdeményezett felügyeleti feladatok konfigurálása nem ütemezhető a felügyelt karbantartási időszak alatt.

-   A tervezett események, például a számítások méretezése és a tároló méretezése először a készenléti replikán történik meg, majd az elsődleges kiszolgálón. A kiszolgáló nem végez feladatátvételt ezen tervezett műveletekhez. 

-  Ha a logikai dekódolás vagy a logikai replikáció egy HA konfigurált rugalmas kiszolgálóval van konfigurálva, a készenléti kiszolgáló feladatátvétele esetén a rendszer nem másolja át a logikai replikációs tárolóhelyeket a készenléti kiszolgálóra.  

## <a name="next-steps"></a>Következő lépések

-   Az [üzletmenet folytonosságának](./concepts-business-continuity.md) megismerése
-   Ismerje meg, hogyan [kezelheti a magas rendelkezésre állást](./how-to-manage-high-availability-portal.md)
-   Tudnivalók a [biztonsági mentésről és a helyreállításról](./concepts-backup-restore.md)