---
title: Globálisan elérhető szolgáltatások tervezése
description: Ismerkedjen meg az alkalmazások kialakításával a Azure SQL Database használó, magasan elérhető szolgáltatásokhoz.
keywords: Felhőbeli vész-helyreállítási, vész-helyreállítási megoldások, alkalmazásadatok biztonsági mentése, Geo-replikáció, üzletmenet-folytonossági tervezés
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: be632ba06edc858e7eadcd6e57a4f7769f69f2cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91321679"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Globálisan elérhető szolgáltatások tervezése Azure SQL Database használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Cloud Services Azure SQL Database használatával történő létrehozásakor és üzembe helyezésekor [aktív geo-replikálási](active-geo-replication-overview.md) vagy [automatikus feladatátvételi csoportok](auto-failover-group-overview.md) segítségével biztosítható a regionális kimaradások és a katasztrofális hibák rugalmassága. Ugyanez a funkció lehetővé teszi, hogy globálisan elosztott alkalmazásokat hozzon létre, amelyek helyi hozzáférésre vannak optimalizálva az adatokhoz. Ez a cikk a gyakori alkalmazási mintákat ismerteti, beleértve az egyes lehetőségek előnyeit és kompromisszumait.

> [!NOTE]
> Ha prémium szintű vagy üzletileg kritikus-adatbázisokat és rugalmas készleteket használ, rugalmasan végezheti el a regionális kimaradásokat a zónák redundáns üzembe helyezési konfigurációjához való átalakításával. Lásd: [zóna – redundáns adatbázisok](high-availability-sla.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>1. forgatókönyv: két Azure-régió használata az üzletmenet folytonossága érdekében minimális állásidővel

Ebben az esetben az alkalmazások a következő jellemzőkkel rendelkeznek:

* Az alkalmazás egy Azure-régióban aktív
* Minden adatbázis-munkamenethez olvasási és írási hozzáférés (RW) szükséges az adateléréshez
* A késés és a forgalmi díj csökkentése érdekében a webes és az adatszinteket is közös elhelyezésű kell
* Alapesetben az állásidő magasabb üzleti kockázat ezen alkalmazások esetében, mint az adatvesztés

Ebben az esetben az alkalmazás-telepítési topológia a regionális katasztrófák kezelésére van optimalizálva, ha az összes alkalmazás-összetevőnek együtt kell átadnia a feladatátvételt. Az alábbi ábra ezt a topológiát mutatja be. Földrajzi redundancia esetén az alkalmazás erőforrásai az A és B régióba vannak telepítve. A B régió erőforrásai azonban nem lesznek felhasználva, amíg az A régió meghibásodik. Az adatbázis-kapcsolat, a replikálás és a feladatátvétel kezeléséhez egy feladatátvételi csoport van konfigurálva a két régió között. A webszolgáltatást mindkét régióban úgy kell konfigurálni, hogy az írási-olvasási figyelő **&lt; feladatátvétel-csoport-neve &gt; . database.Windows.net** (1) használatával hozzáférjen az adatbázishoz. Az Azure Traffic Manager az [elsődleges útválasztási módszer](../../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2) használatára van beállítva.  

> [!NOTE]
> Az [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) csak illusztrációs célokat szolgál az egész cikkben. Bármely terheléselosztási megoldást használhat, amely támogatja a prioritásos útválasztási módszert.

A következő ábra a leállás előtt mutatja ezt a konfigurációt:

![1. forgatókönyv Konfiguráció a leállás előtt.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Az elsődleges régió meghibásodása után SQL Database észleli, hogy az elsődleges adatbázis nem érhető el, és az automatikus feladatátvételi házirend (1) paraméterei alapján elindítja a feladatátvételt a másodlagos régióba. Az alkalmazás SLA-ja alapján beállíthat egy türelmi időszakot, amely a leállás és a feladatátvétel észlelése közötti időt vezérli. Lehetséges, hogy az Azure Traffic Manager kezdeményezi a végpont feladatátvételét, mielőtt a feladatátvételi csoport elindítja az adatbázis feladatátvételét. Ebben az esetben a webalkalmazás nem tud azonnal újracsatlakozni az adatbázishoz. Az újracsatlakoztatások azonban automatikusan sikeresek lesznek, amint az adatbázis feladatátvétele befejeződik. Ha a meghibásodott régiót visszaállítja és újra online állapotban van, a régi elsődleges automatikusan újrakapcsolódik új másodlagosként. Az alábbi ábra szemlélteti a konfigurációt a feladatátvétel után.

> [!NOTE]
> A feladatátvételt követően véglegesített összes tranzakció elvész az Újracsatlakozás során. A feladatátvétel befejezése után a B régióban lévő alkalmazás újra tud csatlakozni, és újraindítja a felhasználói kérések feldolgozását. A webalkalmazás és az elsődleges adatbázis jelenleg a B régióban található, és továbbra is közös helyen található.

![1. forgatókönyv Konfiguráció a feladatátvétel után](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Ha áramkimaradás történik a B régióban, az elsődleges és a másodlagos adatbázis közötti replikálási folyamat fel lesz függesztve, de a kettő közötti kapcsolat sértetlen marad (1). Traffic Manager észleli, hogy a B régióhoz való kapcsolat megszakadt, és a végponti webalkalmazást (2) csökkentett teljesítményű jelöli meg. Ebben az esetben az alkalmazás teljesítménye nem befolyásolja ezt az esetet, de az adatbázis elérhetővé válik, és így nagyobb az adatvesztés kockázata, ha az A régióban az A-régió meghibásodik.

> [!NOTE]
> A vész-helyreállítás érdekében javasoljuk, hogy az alkalmazás központi telepítésének konfigurációját két régióra korlátozzák. Ennek az az oka, hogy az Azure-beli földrajzi területek többsége csak két régióval rendelkezik. Ez a konfiguráció nem biztosítja az alkalmazás egyidejű katasztrofális meghibásodását mindkét régióban. Egy ilyen hiba valószínűtlen eseménye esetén a [geo-visszaállítási művelet](disaster-recovery-guidance.md#recover-using-geo-restore)használatával helyreállíthatja az adatbázisokat egy harmadik régióban.
>

 A leállás csökkentése után a másodlagos adatbázis automatikusan újraszinkronizálja az elsődlegest. A szinkronizálás során az elsődleges teljesítmény is hatással lehet. Az adott hatás a feladatátvétel óta az új elsődleges adatmennyiségtől függ. 

> [!NOTE]
> A leállás csökkentése után Traffic Manager megkezdi a kapcsolatok útválasztását az A régióban található alkalmazáshoz magasabb prioritású végpontként. Ha egy ideig kívánja megőrizni az elsődlegest a B régióban, akkor ennek megfelelően módosítania kell a prioritási táblázatot a forgalom Manager-profilban. 
>
 
 A következő ábra a másodlagos régió kimaradását szemlélteti:

![1. forgatókönyv Konfiguráció áramkimaradás után a másodlagos régióban.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

A tervezési minta fő **előnyei** a következők:

* Ugyanazt a webalkalmazást a régióra jellemző konfiguráció nélkül helyezi üzembe mindkét régióban, és nem igényel további logikát a feladatátvétel kezeléséhez.
* Az alkalmazás teljesítményét nem érinti a feladatátvétel, mert a webalkalmazás és az adatbázis mindig közös helyen található.

A fő **kompromisszum** az, hogy a B régióban lévő alkalmazás-erőforrások a legtöbbször kihasználva vannak.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>2. forgatókönyv: az üzleti folytonosságot biztosító Azure-régiók a maximális adatmegőrzéssel

Ez a lehetőség a következő jellemzőkkel rendelkező alkalmazásokhoz ideális:

* Minden adatvesztés magas üzleti kockázat. Az adatbázis-feladatátvétel csak akkor használható végső megoldásként, ha a leállás oka végzetes hiba.
* Az alkalmazás támogatja a csak olvasási és írási és olvasási üzemmódot, és egy adott időszakra vonatkozóan csak olvasható módban működhet.

Ebben a mintában az alkalmazás írásvédett módra vált, amikor az írási-olvasási kapcsolatok megkezdik az időtúllépési hibák megkezdését. A webalkalmazás mindkét régióban üzembe van helyezve, és kapcsolattal rendelkezik az írható-olvasható figyelő végponttal, valamint az írásvédett figyelő-végponthoz (1) való eltérő kapcsolattal. A Traffic Manager profilnak [prioritás-útválasztást](../../traffic-manager/traffic-manager-configure-priority-routing-method.md)kell használnia. A végponti [figyelést](../../traffic-manager/traffic-manager-monitoring.md) engedélyezni kell az alkalmazás végpontja számára az egyes régiókban (2).

A következő ábra a leállás előtt mutatja be ezt a konfigurációt:

![2. forgatókönyv Konfiguráció a leállás előtt.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Ha Traffic Manager csatlakozási hibát észlel az A régióhoz, akkor a a B régióban lévő alkalmazás-példányra automatikusan átváltja a felhasználói forgalmat. Ezzel a mintával fontos, hogy a türelmi időszakot adatvesztéssel állítsa be elég magas értékre, például 24 órára. Gondoskodik arról, hogy az adatvesztés ne legyen megtiltva, ha a leállás az adott időn belül enyhítve van. Ha a B régióban lévő webalkalmazás aktiválva van, akkor az írási és olvasási műveletek sikertelenek lesznek. Ezen a ponton csak olvasható módra kell váltania (1). Ebben a módban a rendszer automatikusan átirányítja a kéréseket a másodlagos adatbázishoz. Ha a kimaradást végzetes hiba okozta, valószínűleg nem lehet enyhíteni a türelmi időszakon belül. Amikor lejár a feladatátvételi csoport, elindítja a feladatátvételt. Ezután az írható-olvasható figyelő elérhetővé válik, és a kapcsolata leáll (2). A következő ábra a helyreállítási folyamat két fázisát szemlélteti.

> [!NOTE]
> Ha az elsődleges régióban lévő kimaradás a türelmi időszakon belül enyhítve van, Traffic Manager észleli a kapcsolat visszaállítását az elsődleges régióban, és a felhasználói forgalmat visszakapcsolja az A régióban lévő alkalmazás-példányra. Az alkalmazás példánya olvasási és írási módban működik, az a régióban lévő elsődleges adatbázis használatával, az előző ábrán látható módon.

![2. forgatókönyv Vész-helyreállítási szakaszok.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Ha egy leállás történik a B régióban, Traffic Manager észleli a B régióban a végpont Web-App-2 hibáját, és a "csökkentett teljesítményű" (1) állapotot jelzi. Addig is a feladatátvételi csoport a írásvédett figyelőt a (2) régióra vált. Ez a leállás nem befolyásolja a végfelhasználói élményt, de az elsődleges adatbázis ki van téve a leállás során. A következő ábra a másodlagos régió hibáját szemlélteti:

![2. forgatókönyv A másodlagos régió kimaradása.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

A leállás csökkentése után a rendszer azonnal szinkronizálja a másodlagos adatbázist az elsődleges és a csak olvasási figyelővel a B régió másodlagos adatbázisába. Az elsődleges szinkronizálási teljesítmény során a szinkronizálni kívánt adatmennyiségtől függően némileg befolyásolható a művelet.

Ez a kialakítási minta számos **előnnyel jár**:

* Az átmeneti kimaradások során elkerüli az adatvesztést.
* Az állásidő csak attól függ, hogy Traffic Manager észleli-e a csatlakozási hibát, amely konfigurálható.

A **kompromisszum** azt eredményezi, hogy az alkalmazásnak képesnek kell lennie csak olvasható módban való működésre.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>3. forgatókönyv: az alkalmazás áthelyezése egy másik földrajzi helyre adatvesztés nélkül, és közel nulla állásidővel

Ebben az esetben az alkalmazás a következő jellemzőkkel rendelkezik:

* A végfelhasználók az alkalmazást különböző földrajzi helyekről érik el
* Az alkalmazás olyan írásvédett munkaterheléseket tartalmaz, amelyek nem függnek a legújabb frissítésekkel való teljes szinkronizálástól
* Az adatíráshoz való hozzáférést a felhasználók többsége számára azonos földrajzi helyen kell támogatni
* Az olvasási késés kritikus fontosságú a végfelhasználói élményhez

A követelmények teljesítése érdekében biztosítania kell, hogy a felhasználói eszköz **mindig** a csak olvasási műveletekhez, például az adatok böngészéséhez, az elemzésekhez és az adatokhoz tartozó, azonos földrajzi területen üzembe helyezett alkalmazáshoz kapcsolódjon. A OLTP-műveletek feldolgozása a **legtöbb esetben** azonos földrajzi helyen történik. Például a nap folyamán a OLTP műveletei ugyanabban a földrajzban lesznek feldolgozva, de a munkaidőn kívül más földrajzi helyeken is feldolgozhatók. Ha a végfelhasználói tevékenység többnyire a munkaidő alatt történik, akkor a legtöbb felhasználó számára az optimális teljesítményt garantálhatja a legtöbb esetben. A következő ábra ezt a topológiát mutatja be.

Az alkalmazás erőforrásait minden olyan földrajzi helyen telepíteni kell, ahol jelentős használati igény van. Ha például az alkalmazás aktívan használatban van a Egyesült Államokban, az Európai Unió és Dél-Kelet-Ázsia az alkalmazást az összes ilyen földrajzi területhez telepíteni kell. Az elsődleges adatbázisnak dinamikusan kell váltania az egyik földrajzi helyről a munkaidő végén lévő következőre. Ezt a metódust "a nap követése" néven nevezzük. A OLTP munkaterhelés mindig az adatbázishoz csatlakozik az írási-olvasási figyelő **&lt; feladatátvétel-csoport-neve &gt; . database.Windows.net** (1) használatával. A csak olvasási feladat közvetlenül a helyi adatbázishoz csatlakozik a Databases Server Endpoint **&lt; Server – name &gt; . database.Windows.net** (2) adatbázis használatával. Traffic Manager a [teljesítmény-útválasztási módszerrel](../../traffic-manager/traffic-manager-configure-performance-routing-method.md)van konfigurálva. Biztosítja, hogy a végfelhasználó eszköze a legközelebbi régióban található webszolgáltatáshoz kapcsolódjon. A Traffic Manager minden webszolgáltatás-végpontnál (3) engedélyezve kell lennie a végpontok figyelésével.

> [!NOTE]
> A feladatátvételi csoport konfigurációja határozza meg, hogy melyik régiót használja a feladatátvételhez. Mivel az új elsődleges hely eltérő földrajzi helyen található, a feladatátvétel a OLTP és a csak olvasási feladatokhoz is több késést eredményez, amíg az érintett régió újra online állapotba nem kerül.

![3. forgatókönyv Konfiguráció elsődlegesként az USA keleti régiójában.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

A nap végén, például a helyi idő szerint, az aktív adatbázisokat a következő régióra kell váltani (Észak-Európa). Ez a feladat teljes mértékben automatizálható [Azure Logic apps](../../logic-apps/logic-apps-overview.md)használatával. A feladat az alábbi lépéseket foglalja magában:

* A feladatátvételi csoport elsődleges kiszolgálójának átváltása Észak-Európába a rövid feladatátvétel (1) használatával
* A feladatátvételi csoport eltávolítása az USA keleti régiója és Észak-Európa között
* Hozzon létre egy új, azonos nevű feladatátvételi csoportot, de Észak-Európa és Kelet-Ázsia között (2).
* Adja hozzá az elsődlegest Észak-Európában és a másodlagos Kelet-Ázsia a feladatátvételi csoporthoz (3).

Az alábbi ábra az új konfigurációt mutatja be a tervezett feladatátvétel után:

![3. forgatókönyv Áttérés az elsődlegesről Észak-Európára.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Ha például áramkimaradás fordul elő Észak-Európában, az automatikus adatbázis-feladatátvételt a feladatátvételi csoport kezdeményezi, ami gyakorlatilag a következő régióba helyezi át az alkalmazást a Schedule (1) előtt.  Ebben az esetben az USA keleti régiója az egyetlen fennmaradó másodlagos régió, amíg Észak-Európa ismét online állapotba kerül. A fennmaradó két régió mindhárom régióban kiszolgálja az ügyfeleket a szerepkörök váltásával. A Azure Logic Apps ennek megfelelően kell módosítani. Mivel a többi régió az Európából érkező további felhasználói adatforgalmat kap, az alkalmazás teljesítményét nem csupán a további késések, hanem a végfelhasználói kapcsolatok egyre nagyobb száma is befolyásolja. Ha a leállás az észak-európai régióban van enyhítve, a másodlagos adatbázis azonnal szinkronizálva lesz a jelenlegi elsődlegesével. Az alábbi ábrán az észak-európai leállás látható:

![3. forgatókönyv Leállás Észak-Európában.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Csökkentheti azt az időpontot, amikor a végfelhasználói élmény a hosszú késéssel csökken. Ennek elvégzéséhez proaktív módon kell telepítenie egy alkalmazást, és létre kell hoznia egy másik helyi régióban (Nyugat-Európa) a másodlagos adatbázis (oka) t a kapcsolat nélküli alkalmazás példányának cseréjeként Észak-Európában. Ha az utóbbi online állapotban van, eldöntheti, hogy folytatja-e a Nyugat-európai rendszer használatát, vagy az alkalmazás másolatának eltávolítását, és térjen vissza az észak-európai rendszer használatára.

A kialakítás legfőbb **előnyei** a következők:

* A csak olvasási jogosultsággal rendelkező alkalmazás munkaterhelése mindig a szekrényekben lévő adatokhoz fér hozzá.
* Az írási-olvasási alkalmazás munkaterhelése a legközelebb eső régióban lévő adatokhoz fér hozzá az egyes földrajzi területeken lévő legmagasabb tevékenység időtartama alatt
* Mivel az alkalmazás több régióban is üzembe lett helyezve, jelentős állásidő nélkül képes túlélni az egyik régió elvesztését.

Vannak azonban bizonyos **kompromisszumok**:

* A regionális leállás azt eredményezi, hogy a földrajz továbbra is hatással lehet a hosszú késésre. Az alkalmazás az írási és olvasási feladatokat is egy másik földrajzi helyen kézbesíti.
* Az írásvédett munkaterheléseknek minden régióban egy másik végponthoz kell csatlakozniuk.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Üzletmenet-folytonosság tervezése: válasszon ki egy alkalmazást a Felhőbeli vész-helyreállításhoz

Az adott Felhőbeli vész-helyreállítási stratégia kombinálhatja vagy kiterjesztheti ezeket a kialakítási mintákat, hogy azok megfeleljenek az alkalmazás igényeinek.  Ahogy azt korábban említettük, az Ön által választott stratégia az ügyfelek számára kínált SLA-ra és az alkalmazás-telepítési topológiára épül. A döntés meghozatalához a következő táblázat összehasonlítja a helyreállítási pont célkitűzése (RPO) és a becsült helyreállítási idő (ERT) alapján megjelenő döntéseket.

| Mintázat | RPO | ERT |
|:--- |:--- |:--- |
| Aktív – passzív üzembe helyezés a vész-helyreállításhoz közös elhelyezésű adatbázis-hozzáféréssel |Olvasási és írási hozzáférés < 5 MP |Hiba észlelési ideje + DNS ÉLETTARTAMa |
| Aktív – aktív üzembe helyezés az alkalmazások terheléselosztásához |Olvasási és írási hozzáférés < 5 MP |Hiba észlelési ideje + DNS ÉLETTARTAMa |
| Aktív – passzív üzembe helyezés az adatmegőrzéshez |Írásvédett hozzáférés < 5 MP | Írásvédett hozzáférés = 0 |
||Olvasási és írási hozzáférés = nulla | Olvasási és írási hozzáférés = hiba észlelési ideje + türelmi időszak adatvesztéssel |
|||

## <a name="next-steps"></a>Következő lépések

* Az üzletmenet folytonosságának áttekintése és forgatókönyvei: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* Az aktív geo-replikációval kapcsolatos információkért lásd: [aktív földrajzi replikálás](active-geo-replication-overview.md).
* További információ az automatikus feladatátvételi csoportokról: [automatikus feladatátvételi csoportok](auto-failover-group-overview.md).
* A rugalmas készletekkel rendelkező aktív geo-replikációval kapcsolatos információkért lásd: [rugalmas készlet vész-helyreállítási stratégiái](disaster-recovery-strategies-for-applications-with-elastic-pool.md).
