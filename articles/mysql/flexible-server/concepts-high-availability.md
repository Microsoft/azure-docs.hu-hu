---
title: Zónaredundáns magas rendelkezésre állás áttekintése rugalmas Azure Database for MySQL kiszolgálóval
description: Ismerje meg a zónaredundáns magas rendelkezésre állás és a rugalmas Azure Database for MySQL fogalmait
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 5b5e1491d7f76cd4cff76d0c9a1af4daa49fa483
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813001"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Magas rendelkezésre állású fogalmak rugalmas Azure Database for MySQL (előzetes verzió)

> [!IMPORTANT] 
> Azure Database for MySQL – A rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Azure Database for MySQL (előzetes verzió) lehetővé teszi a magas rendelkezésre állás konfigurálását automatikus feladatátvételsel zónaredundáns **magas** rendelkezésre állási beállítással. Amikor zónaredundáns konfigurációban van üzembe helyezve, a rugalmas kiszolgáló automatikusan kiépít és felügyel egy készenléti replikát egy másik rendelkezésreállási zónában. A tárolószintű replikációval  a rendszer szinkron módon replikálja az adatokat a másodlagos zónában található készenléti kiszolgálóra, hogy a feladatátvételt követően ne okozjon adatvesztést. A feladatátvétel teljesen transzparens az ügyfélalkalmazástól, és nem igényel felhasználói műveleteket. A készenléti kiszolgáló nem érhető el olvasási vagy írási műveletekhez, hanem passzív készenléti kiszolgáló a gyors feladatátvétel engedélyezéséhez. A feladatátvételi idők általában 60–120 másodperc között vannak.

A zónaredundáns magas rendelkezésre állású konfiguráció lehetővé teszi az automatikus feladatátvételt olyan tervezett események során, mint a felhasználó által kezdeményezett skálázható számítási műveletek, valamint a nem tervezett események, például a mögöttes hardver- és szoftverhibák, a hálózati hibák vagy akár a rendelkezésre állási zónák meghibásodása.

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="zónaredundáns magas rendelkezésre állás nézete":::

## <a name="zone-redundancy-architecture"></a>Zónaredundania architektúrája

Az elsődleges kiszolgáló a régióban és egy adott rendelkezésre állási zónában van telepítve. Ha a magas rendelkezésre állást választja, a rendszer automatikusan üzembe helyez egy készenléti replikakiszolgálót, amelynek konfigurációja megegyezik az elsődleges kiszolgáló konfigurációval, beleértve a számítási szintet, a számítási méretet, a tárterület méretét és a hálózati konfigurációt. A rendszer szinkron módon replikálja a naplóadatokat a készenléti replikába, hogy hiba esetén ne legyen adatvesztés. Az automatikus biztonsági mentések , a pillanatképek és a naplók biztonsági mentése is az elsődleges adatbázis-kiszolgálóról történik. 

A rendszer folyamatosan figyeli és jelenti a hatékony állapotát az áttekintő oldalon.

A különböző replikációs állapotok alább vannak felsorolva:

| **Állapot** | **Leírás** |
| :----- | :------ |
| <b>NotEnabled (NotEnabled) | A zónaredundáns ha még nincs engedélyezve |
| <b>CreatingStandby (Standby létrehozása) | Az új készenléti rendszer létrehozásának folyamata során |
| <b>ReplicatingData (Adatok replikálása) | A készenléti kiszolgáló létrehozása után az elsődleges kiszolgálóval fog lemaradni. |
| <b>Feladatátvétel | Az adatbázis-kiszolgáló folyamatban van az elsődleges kiszolgálóról a készenléti kiszolgálóra való feladat-adatokat átveszve. |
| <b>Kifogástalan | A zónaredundáns hatékony stabil állapotban van és kifogástalan állapotban van. |
| <b>AStandby eltávolítása | A felhasználói művelet alapján a készenléti replika törlése folyamatban van.| 

## <a name="advantages"></a>Előnyök

A zónaredundania hatékony funkció használatának néhány előnye: 

- A készenléti replika az elsődleges virtuálisgép-konfigurációnak megfelelő pontos konfigurációban van telepítve, például virtuális magokkal, tárterülettel, hálózati beállításokkal (VNET, tűzfal stb.).
- Lehetőség a készenléti replikák eltávolítására a magas rendelkezésre állás letiltásával.
- Az automatikus biztonsági mentések pillanatkép-alapúak, az elsődleges adatbázis-kiszolgálóról hajthatóak végre, és egy zónaredundáns tárolóban vannak tárolva.
- Feladatátvétel esetén a rugalmas Azure Database for MySQL automatikusan átveszi a feladatátvételt a készenléti replikára, ha a magas rendelkezésre állás engedélyezve van. A magas rendelkezésre állású beállítás figyeli az elsődleges kiszolgálót, és online állapotba hozza.
- Az ügyfelek mindig az elsődleges adatbázis-kiszolgálóhoz csatlakoznak.
- Adatbázis-összeomlás vagy csomóponthiba esetén a rendszer először az újraindítást kíséreli meg ugyanazon a csomóponton. Ha ez nem sikerül, akkor az automatikus feladatátvétel aktiválódik.
- Lehetőség van a kiszolgáló újraindítására a statikus kiszolgálóparaméterek változásainak be- és be- vagy kivezetéséhez.

## <a name="steady-state-operations"></a>Stabil állapotú műveletek

Az alkalmazások az adatbázis-kiszolgáló nevével csatlakoznak az elsődleges kiszolgálóhoz. A készenléti replika adatai közvetlen hozzáféréshez nem veszélynek vannak kitéve. A véglegesítéseket és az írásokat az alkalmazás csak akkor nyugtázza, ha a naplófájlok szinkron módon megőrzve vannak az elsődleges kiszolgáló lemezén és a készenléti replikán. Ennek a további adat oda-vissza követelménynek köszönhetően az alkalmazások emelt szintű késést számíthatnak az írások és véglegesítések esetében. A magas rendelkezésre állás állapotát a portálon követheti nyomon.

## <a name="failover-process"></a>Feladatátvételi folyamat 
Az üzletmenet folytonossága érdekében feladatátvételi folyamatra van szükség a tervezett és nem tervezett eseményekhez. 

### <a name="planned-events"></a>Tervezett események

A tervezett leállási események közé tartoznak az Azure által ütemezett tevékenységek, például az időszakos szoftverfrissítések, az alverziófrissítések, vagy az ügyfelek által kezdeményezett tevékenységek, például a számítási feladatok méretezése és a tárolási műveletek méretezése. A rendszer először alkalmazza ezeket a módosításokat a készenléti replikára. Ez idő alatt az alkalmazások továbbra is hozzáférnek az elsődleges kiszolgálóhoz. A készenléti replika frissítése után a rendszer kiüríti az elsődleges kiszolgálókapcsolatokat, és feladatátvételt indít el, amely a DNS-rekord frissítésével aktiválja a készenléti replikát, hogy elsődleges legyen ugyanazokkal az adatbázis-kiszolgálónévvel. Az ügyfélkapcsolatok megszakadnak, és újra kell csatlakozniuk, és folytathatják a működést. Egy új készenléti kiszolgáló ugyanabban a zónában van, mint a régi elsődleges kiszolgáló. A teljes feladatátvételi idő várhatóan 60–120 másodperc lesz. 

>[!NOTE]
> A számítási skálázás esetén a másodlagos replikakiszolgálót az elsődleges kiszolgáló követi. Nincs szükség feladatátvételre.

### <a name="failover-process---unplanned-events"></a>Feladatátvételi folyamat – nem tervezett események
A nem tervezett szolgáltatáskimaradások közé tartoznak a szoftverhibák, amelyek vagy infrastruktúrahibák, például számítási, hálózati, tárolási hibák vagy áramkimaradások befolyásolják az adatbázis rendelkezésre állását. Ha az adatbázis elérhetetlenné nem érhető el, a rendszer a készenléti replikára történő replikációt megszűküli, és a készenléti replikát aktiválja elsődleges adatbázisként. A RENDSZER frissíti a DNS-t, majd az ügyfelek újra csatlakoznak az adatbázis-kiszolgálóhoz, és folytatják a működést. A teljes feladatátvételi idő várhatóan 60–120 másodpercet fog kihozni. Azonban a feladatátvételkor az elsődleges adatbázis-kiszolgálón végzett tevékenységtől , például a nagy tranzakcióktól és a helyreállítási időtől függően a feladatátvétel hosszabb időt is igénybe vehet.

### <a name="forced-failover"></a>Kényszerített feladatátvétel
Azure Database for MySQL kényszerített feladatátvétel lehetővé teszi a feladatátvétel manuális kényszerítését, amellyel tesztelheti a funkciókat az alkalmazás forgatókönyvei alapján, és segít abban, hogy kimaradás esetén készen ugorjon. A kényszerített feladatátvétel úgy váltja át a készenléti kiszolgálót, hogy elsődleges kiszolgálóvá váljon egy olyan feladatátvétel aktiválásával, amely úgy aktiválja a készenléti replikát, hogy a DNS-rekord frissítésével az adatbázis-kiszolgáló nevével azonos elsődleges kiszolgálóvá váljon. A rendszer újraindítja az eredeti elsődleges kiszolgálót, és készenléti replikára vált. Az ügyfélkapcsolatok megszakadnak, és újra kell csatlakozniuk a működés folytatásához. A rendszer az aktuális számítási feladattól és az utolsó ellenőrzőponttól függően méri a teljes feladatátvételi időt. Általában 60 és 120 közötti lehet.

## <a name="schedule-maintenance-window"></a>Karbantartási időszak ütemezése 

A rugalmas kiszolgálók karbantartási ütemezési képességet kínálnak, amelyben igény szerint egy 30 perces időszak közül választhat, amelyben az Azure karbantartási szolgáltatása (például a javítás vagy az alverzió frissítése) működik. A magas rendelkezésre állású rugalmas kiszolgálók esetén ezeket a karbantartási tevékenységeket először a készenléti replikán kell elvégezni. 

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás 
Mivel a rugalmas kiszolgáló magas rendelkezésre állású, szinkron módon replikálja az adatokat, a készenléti kiszolgáló naprakész az elsődleges kiszolgálóval. Az elsődleges adatbázis felhasználói hibái – például egy tábla véletlen eldobása vagy helytelen frissítések – sikeresen replikálódnak a készenléti kiszolgálóra. Ezért nem használhat készenléti üzemmódot az ilyen logikai hibákból való helyreállításhoz. A logikai hibákból való helyreállításhoz a hiba beeste előtti időponthoz kell állítania az időponthoz való visszaállítást. A rugalmas kiszolgáló időponthoz állásra vonatkozó visszaállítási képességének használatával a magas rendelkezésre állású adatbázis visszaállításakor egy új adatbázis-kiszolgáló lesz visszaállítva új, felhasználó által megadott névvel rendelkező rugalmas kiszolgálóként. Ezután exportálhatja az objektumot az adatbázis-kiszolgálóról, és importálhatja az éles adatbázis-kiszolgálóra. Hasonlóképpen, ha az adatbázis-kiszolgálót tesztelési és fejlesztési célokra szeretné klónozni, vagy bármilyen más célra szeretné visszaállítani, választhatja a legújabb visszaállítási pontot vagy egy egyéni visszaállítási pontot. A visszaállítási művelet egy egyzónás, rugalmas kiszolgálót hoz létre.

## <a name="mitigate-downtime"></a>Az állásidő csökkentése 
Ha nem használja a zónaredundania hatékony funkcióját, továbbra is képesnek kell lennie csökkenteni az alkalmazás állásideját. A szolgáltatás leállásának megtervezése, például ütemezett javítások, alverziófrissítések vagy a felhasználó által kezdeményezett műveletek az ütemezett karbantartási időszak részeként hajthatóak végre. A szolgáltatás tervezett állásideje, például ütemezett javítások, alverziófrissítések vagy a felhasználó által kezdeményezett műveletek, például a skálázható számítási feladatok állásidővel is számolnak. Az Azure által kezdeményezett karbantartási feladatok alkalmazásra gyakorolt hatásának csökkentése érdekében a hét napja és ideje szerint ütemezheti azokat, ami a legkevésbé befolyásolja az alkalmazást. 

Nem tervezett leállási események, például adatbázis-összeomlás vagy kiszolgálóhiba esetén az érintett kiszolgáló ugyanabban a zónában indul újra. Bár ritka, ha a teljes zónát befolyásolja, visszaállíthatja az adatbázist a régió egy másik zónájában. 

## <a name="things-to-know-with-zone-redundancy"></a>A zónaredundania miatt tudni kell 

A zónaredundania magas rendelkezésre állásának használata során a következő szempontokat kell figyelembe vennie: 

- A magas rendelkezésre állás csak **rugalmas** kiszolgáló létrehozási ideje alatt beállítható.
- A magas rendelkezésre állás nem támogatott az eltörhető számítási szinteken.
- A másik rendelkezésre állási zónába történő szinkron replikáció miatt az elsődleges adatbázis-kiszolgáló emelt szintű írási és véglegesítési késést tapasztalhat.
- A készenléti replika nem használható csak olvasható lekérdezésekhez.
- A feladatátvételkor az elsődleges kiszolgálón végzett tevékenységtől függően a feladatátvétel akár 60–120 másodpercet is igénybe vehet.
- Az elsődleges adatbázis-kiszolgáló újraindítása a statikus paraméterváltozások behozása érdekében szintén újraindítja a készenléti replikát.
- Az olvasási replikák zónaredundáns magas rendelkezésre állású kiszolgálókhoz való konfigurálása nem támogatott.
- Az ügyfél által kezdeményezett felügyeleti feladatok konfigurálása nem automatizálható a felügyelt karbantartási időszakban.
- A tervezett események, például a számítási méretezés és az alverziófrissítések egyszerre történnek az elsődleges és készenléti környezetben is. 


## <a name="next-steps"></a>Következő lépések

- További tudnivalók az [üzletmenet folytonosságával kapcsolatban](./concepts-business-continuity.md)
- Tudnivalók a [zónaredundáns magas rendelkezésre állásról](./concepts-high-availability.md)
- Tudnivalók a biztonsági [mentésről és a helyreállításról](./concepts-backup-restore.md)
