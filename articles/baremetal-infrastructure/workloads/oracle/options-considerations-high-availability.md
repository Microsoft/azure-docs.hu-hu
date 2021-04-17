---
title: Beállítások vagy Oracle BareMetal Infrastruktúra-kiszolgálók
description: Ismerje meg az Oracle BareMetal Infrastructure-kiszolgálókra vonatkozó lehetőségeket és szempontokat.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: e8a2dece11884e3a659f14b30bce51e7f0244924
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590257"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Oracle BareMetal Infrastructure-kiszolgálók beállításai

Ebben a cikkben olyan lehetőségeket és javaslatokat tekintünk át, amelyek a legmagasabb szintű védelmet és teljesítményt biztosítják az Oracle bareMetal Infrastructure-kiszolgálókon való futtatásához. 

## <a name="data-guard-protection-modes"></a>Data Guard védelmi módok

A Data Guard kizárólag az Oracle Real Applications Cluster (RAC), a logikai replikáció (például a GoldenGate) és a tárolóalapú replikáció révén biztosít védelmet, amely nem érhető el. 

| Védelem módja | Description |
| --- | --- |
| **Maximális teljesítmény** | Az alapértelmezett védelmi mód. Ez biztosítja a legmagasabb szintű védelmet az elsődleges adatbázis teljesítményének befolyásolása nélkül. Az adatok akkor minősülnek lekötöttnek, amikor az elsődleges adatbázis újra megírta az adatfolyamot. Ezt követően a rendszer aszinkron módon replikálja a készenléti adatbázisba. A készenléti adatbázis általában másodpercek alatt megkapja azt, de erre a hatásra nem vállal garanciát. Ez a mód általában megfelel az üzleti követelményeknek (a késések figyelése mellett) anélkül, hogy alacsony késésű hálózati kapcsolatra lenne szükség az elsődleges és a készenléti helyek között.<br /><br />Ez biztosítja a legjobb működési adatmegőrzést; azonban nem garantálja az adatvesztést.   |
| **Maximális rendelkezésre állás** | A legmagasabb szintű védelmet biztosítja az elsődleges adatbázis rendelkezésre állásának befolyásolása nélkül. Az adatok csak akkor minősülnek az elsődleges adatbázisba lekötöttnek, ha legalább egy készenléti adatbázisba is le vannak kötelezettségetve. Ha az elsődleges adatbázis nem tudja legalább egy készenléti adatbázisba írni az újraalkotott módosításokat, akkor nem lesz elérhető, hanem a Maximális teljesítmény módra vált vissza. <br /><br />Lehetővé teszi a szolgáltatás folytatását, ha a készenléti hely nem érhető el. Ha csak egy hely működik, akkor az adatoknak csak egy példánya lesz megőrizve, amíg a második hely online állapotba nem áll, és a szinkronizálás újra létre nem áll. |
| **Maximális védelem** | A maximális rendelkezésre álláshoz hasonló védelmi szintet biztosít. Az elsődleges adatbázis leáll a hozzáadott funkcióval, ha nem tudja legalább egy készenléti adatbázisba írni az újraírási módosításokat. Ez biztosítja, hogy az adatvesztés ne következhet be, azonban a rendelkezésre állás a sebezhetőség rovására. |

>[!IMPORTANT]
>Ha nulla helyreállításipont-célkitűzésre (RPO) van szüksége, javasoljuk a Maximális rendelkezésre állás konfigurációt. Ezután az RPO több hiba esetén is garantálható. Például még az elsődleges adatbázisból való hálózati kimaradás esetén is, amelyet az elsődleges adatbázis egy későbbi elvesztése követ, miközben a hálózatkimaradás még érvényben van.

### <a name="data-guard-deployment-patterns"></a>Data Guard üzembe helyezési minták

Az Oracle lehetővé teszi, hogy több célhelyet konfiguráljon az újra történő generációhoz, így több készenléti adatbázist is engedélyezhet. A leggyakoribb konfiguráció az alábbi ábrán látható, amely egyetlen készenléti adatbázis egy másik régióban.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Ábra az Oracle alapértelmezett Data Guard üzemelő példányáról.":::

A Data Guard a Maximális teljesítmény módban van konfigurálva az alapértelmezett üzembe helyezéshez. Ez a konfiguráció közel valós idejű adatreplikációt biztosít aszinkron adatátvitelen keresztül. A készenléti adatbázisnak nem kell RAC üzemelő példányon belül futnia, de azt javasoljuk, hogy megfeleljen az elsődleges hely teljesítményigényeinek.

Az alábbi ábrán láthatóhoz hasonló üzembe helyezést javasoljuk a szigorú üzemidőt vagy nulla RPO-t igénylő környezetekhez. A Maximális rendelkezésre állás konfigurációja egy helyi készenléti adatbázisból áll, amely szinkron módban alkalmazza az újrajátszinkron üzemmódot, valamint egy másik készenléti adatbázist, amely egy távoli régióban fut.

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="A Data Guard maximális rendelkezésre állását bemutató ábra.":::

Helyi készenléti adatbázist akkor hozhat létre, ha az alkalmazás teljesítménye az adatbázis és az alkalmazáskiszolgálók külön régiókban való futtatásával fog futni. Ebben a konfigurációban helyi készenléti adatbázist kell használni, ha az elsődleges fürtön tervezett vagy nem tervezett karbantartásra van szükség. Ezeket az adatbázisokat szinkron replikációval is futtathatja, mivel ugyanabban a régióban vannak, így nem vesznek el közöttük az adatok.

### <a name="data-guard-configuration-considerations"></a>A Data Guard konfigurálási szempontjai

A Data Guard Brokert érdemes megvalósítani, mivel ez leegyszerűsíti a Data Guard-konfigurációk végrehajtását, és biztosítja az ajánlott eljárások betartását. Teljesítményfigyelési funkciókat biztosít, és nagy mértékben leegyszerűsíti a váltást, a feladatátvételt és a reinstantiation eljárásokat.

A Data Guard lehetővé teszi egy megfigyelő folyamat futtatását, amely egy Data Guard-konfigurációban az összes adatbázist figyeli az adatbázis rendelkezésre állásának megállapításához. Ha egy elsődleges adatbázis meghibásodik, a Data Guard megfigyelője automatikusan elindíthatja a feladatátvételt egy készenléti adatbázisba a konfigurációban. A Data Guard Megfigyelő több megfigyelővel is megvalósítható a fizikai helyek száma alapján (legfeljebb három). 

Ennek a megfigyelőnek az alkalmazásréteget támogató infrastruktúrán kell lennie. Az elsődleges megfigyelőnek mindig léteznie kell a fizikai helyen, ahol az elsődleges adatbázis nem található. A Data Guard megfigyelő által aktivált feladatátvételi műveletek automatizálása során körültekintően kell eljárni. Először győződjön meg arról, hogy az alkalmazásokat úgy tervezték és tesztelték, hogy elfogadható szolgáltatást nyújtson, amikor az adatbázis egy másik helyen fut.

Ha az alkalmazás csak helyileg tud működni, a másodlagos hely feladatátvételének manuálisnak kell lennie. A magas rendelkezésre állási szinteket (99,99%-os vagy 99,999%-os üzemidőt) igénylő környezetek esetében az előző ábrán látható módon helyi és távoli készenléti adatbázist is érdemes használni. Ezekben az esetekben a FastStartFailoverTarget paraméter csak a helyi készenléti adatbázisra lesz beállítva.

Minden olyan alkalmazáshoz, amely támogatja a helyközi alkalmazás-/adatbázis-hozzáférést, a FastStartFailoverTarget a Data Guard konfigurációban található összes készenléti adatbázisra van beállítva.

### <a name="active-data-guard"></a>Active Data Guard

Az Oracle Active Data Guard (ADG) a Data Guard alapszintű képességeinek egy olyan része, amely a Oracle Database Enterprise Edition. A szolgáltatás a következő funkciókat biztosítja, amelyek az Oracle Exadata üzemelő példányán lesznek használva:

- Egyedi sérülésészlelés és automatikus javítás.
- Gyors feladatátvétel az éles környezet szinkronizált replikája számára – manuális vagy automatikus.
- Éles számítási feladat kiszervezése szinkronizált, csak olvasható készenléti kiszolgálóra.
- Adatbázis működés közbeni frissítései és készenlétben. Az első javítás fizikai készenléti állapot használatával.
- A növekményes biztonsági mentések készenléti állapotba való kiszervezése.
- Nincs adatvesztéssel szembeni adat-helyreállítási védelem bármilyen távolságon a teljesítmény befolyásolása nélkül.

A dokumentumban elérhető tanulmány áttekintést nyújt a fenti [https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf) funkciókról, ahogyan az az alábbi ábrán látható.

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Az Oracle Active Data Guard funkcióinak áttekintését bemutató ábra.":::

## <a name="backup-recommendations"></a>Biztonsági mentésre vonatkozó javaslatok

Mindenképpen biztonsági mentése legyen az adatbázisokról. A visszaállítási és helyreállítási szolgáltatásokkal visszaállíthatja az adatbázist ugyanannak vagy egy másik rendszernek, vagy visszaállíthatja az adatbázisfájlokat.

Fontos létrehozni egy biztonsági mentési helyreállítási stratégiát, amely megvédi a Oracle Database-adatbázisokat az adatvesztéstől. Az ilyen veszteség a lemez fizikai problémája miatt is okozhatja, amely az adatbázis futtatásához szükséges lemezfájl olvasásának vagy írásának meghibásodását okozza. A felhasználói hibák adatvesztést is okozhatnak. A biztonsági mentési funkció lehetővé teszi az időponthoz időben való visszaállítást (PITR) az adatbázis visszaállítását, a rendszerváltozás-szám **(SCN) helyreállítását és a legutóbbi helyreállítást.** Biztonsági mentési szabályzatot a böngészőablakban Felhasználói felület parancssori felületen hozhat létre.

A következő biztonsági mentési lehetőségek érhetők el:

- Biztonságimentés NFS-tárolókötetre (Fast Recovery Area-FRA- /u98).
- A Azure NetApp Files SnapCenter – pillanatkép használatával.

Megfontolni kell a következő folyamatot:

- Manuális vagy automatikus biztonsági mentések.
- Az automatikus biztonsági mentések NFS-tárolókötetre (például /u98) vannak írva.
- A biztonsági mentések 12:00 és 6:00 között futnak az adatbázis-rendszer időzónájában.
- Jelenlegi megőrzési időtartamok: 7, 15, 30, 45 és 60 nap.

- Adatbázis helyreállítása objektumtárban tárolt biztonsági másolatból:
  - Az utolsó ismert jó állapotra a lehető legkisebb adatvesztéssel.
  - Megadott időbélyeg használata.
  - A megadott SCN használatával.
  - BackupReport – _a biztonsági mentési jelentés scN-ét használja a megadott SCN helyett._

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="Az ügyfél adatbázisának biztonsági mentése az FRA-re (/u98) és a nem FRA-re (/u95) való biztonsági adatokat ábrázoló diagram.":::

### <a name="backup-policy"></a>Biztonsági mentési szabályzat

A biztonsági mentési szabályzat határozza meg a biztonsági mentés részleteit. Biztonsági mentési szabályzat létrehozásakor meg kell határoznia az adatbázis biztonsági mentésének célját (NFS-hely), és meg kell határoznia a helyreállítási ablakot.

Alapértelmezés szerint a BASIC tömörítési algoritmust használja a rendszer. Alacsony, KÖZEPES vagy MAGAS tömörítési algoritmusok használata esetén a lemez- vagy NFS-alapú biztonsági mentési házirendek esetén licencre van szükség.

### <a name="backup-levels"></a>Biztonsági mentési szintek

Adja meg a biztonsági mentés szintjét a biztonsági mentéskor.

- 0. szint – Teljes
- 1. szint – Növekményes
- Hosszú távú/ Archievelog – a biztonsági másolatok megőrzési szabályzata kivételével használjon nem FRA-helyet (például /u95).

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan állítható helyre az Oracle-adatbázis hiba esetén:

> [!div class="nextstepaction"]
> [Oracle-adatbázis helyreállítása az Azure BareMetal-infrastruktúrán](oracle-high-availability-recovery.md)
