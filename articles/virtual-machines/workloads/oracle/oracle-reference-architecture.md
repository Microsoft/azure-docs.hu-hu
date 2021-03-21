---
title: Az Azure-beli Oracle-adatbázisok referenciái architektúrái | Microsoft Docs
description: A Oracle Database Enterprise Edition-adatbázisok Microsoft Azure Virtual Machines-on való futtatására szolgáló architektúrák.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.openlocfilehash: 6bce6f011086d9855c4da2739addbb34e661e2d6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507483"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Az Azure-beli Oracle Database Enterprise Edition hivatkozási architektúrái

Ez az útmutató részletesen ismerteti a magasan elérhető Oracle-adatbázisok üzembe helyezését az Azure-ban. Az útmutató emellett a vész-helyreállítási megfontolásokra is kiesik. Ezek az architektúrák az ügyfelek központi telepítései alapján lettek létrehozva. Ez az útmutató csak Oracle Database Enterprise Editionre vonatkozik.

Ha szeretne többet megtudni az Oracle-adatbázis teljesítményének maximalizálásáról, tekintse meg a [Oracle db Architect](oracle-design.md)című témakört.

## <a name="assumptions"></a>Feltételezések

- Megértette az Azure különböző fogalmait, például a [rendelkezésre állási zónákat](../../../availability-zones/az-overview.md)
- Oracle Database Enterprise Edition 12c vagy újabb verziót futtat
- A jelen cikkben ismertetett megoldások használatakor tudomásul veszi és elfogadja a licencelési szempontokat

## <a name="high-availability-for-oracle-databases"></a>Magas rendelkezésre állás Oracle-adatbázisokhoz

A magas rendelkezésre állás elérése a felhőben fontos része minden szervezet tervezésének és kialakításának. A Microsoft Azure [rendelkezésre állási zónákat](../../../availability-zones/az-overview.md) és rendelkezésre állási csoportokat kínál (amelyek olyan régiókban használhatók, ahol a rendelkezésre állási zónák nem érhetők el). További információ a [virtuális gépek rendelkezésre állásának kezeléséről](../../availability.md) a Felhőbeli tervezéshez.

A Felhőbeli natív eszközök és ajánlatok mellett az Oracle olyan megoldásokat kínál a magas rendelkezésre álláshoz, mint például az [Oracle-adatvédelem](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7), az [FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [, a](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)horizontális skálázás és az Azure-ban beállítható [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) . Ez az útmutató az egyes megoldások hivatkozási architektúráit ismerteti.

Végül, ha a felhőhöz telepít át vagy hoz létre alkalmazásokat, fontos, hogy az alkalmazás kódját a Felhőbeli natív mintázatok, például az [újrapróbálkozási](/azure/architecture/patterns/retry) minta és az [áramkör-megszakító minta](/azure/architecture/patterns/circuit-breaker)hozzáadásával lehessen felvenni. A [Felhőbeli tervezési minták útmutatójában](/azure/architecture/patterns/) meghatározott további minták segíthetnek az alkalmazás rugalmasabb működésében.

### <a name="oracle-rac-in-the-cloud"></a>Oracle-RAC a felhőben

Az Oracle Real Application Cluster (RAC) egy olyan megoldás, amelyet az Oracle az ügyfelek számára nagy teljesítményű adatforgalom eléréséhez biztosít, mivel számos példány fér hozzá egy adatbázis-tárolóhoz (közös – minden architektúra minta). Habár az Oracle RAC a helyszíni magas rendelkezésre álláshoz is használható, az Oracle-RAC önmagában nem használható magas rendelkezésre álláshoz a felhőben, mivel csak a példányok szintjének meghibásodása elleni védelmet nyújt, és nem az állvány-vagy adatközpont-szintű hibák ellen. Ezért az Oracle a magas rendelkezésre állás érdekében azt javasolja, hogy az Oracle-adatvédelmet az adatbázissal (akár egy példány vagy RAC) használja. Az ügyfeleknek általában magas SLA-ra van szükségük a kritikus fontosságú alkalmazások futtatásához. Az Oracle-RAC jelenleg nem hitelesített vagy nem támogatott az Azure-on. Az Azure azonban olyan funkciókat kínál, mint például az Azure, Availability Zones és tervezett karbantartási időszakokat, amelyek segítenek a példány-szintű hibák elleni védelemben. Ezen kívül az ügyfelek olyan technológiákat is használhatnak, mint például az Oracle-adatvédelem, az Oracle-GoldenGate és az Oracle-skálázás a nagy teljesítmény és a rugalmasság érdekében azáltal, hogy az adatbázisait a rack-szintű, valamint az adatközponti szintű és a földrajzilag fellépő hibák védik.

Ha az Oracle-adatbázisok több [rendelkezésre állási zónában](../../../availability-zones/az-overview.md) futnak Oracle-adatgárda vagy GoldenGate használatával, az ügyfeleknek 99,99%-os üzemidőt biztosító SLA-t is igénybe vehetik. Azokon az Azure-régiókban, ahol a rendelkezésre állási zónák még nincsenek jelen, az ügyfelek használhatják a [rendelkezésre állási csoportokat](../../availability-set-overview.md) , és 99,95%-os üzemidőt biztosítanak.

>Megjegyzés: a rendelkezésre állási cél sokkal nagyobb, mint a Microsoft által biztosított üzemidő.

## <a name="disaster-recovery-for-oracle-databases"></a>Vész-helyreállítás Oracle-adatbázisokhoz

Ha a kritikus fontosságú alkalmazásokat a felhőben üzemelteti, fontos megtervezni a magas rendelkezésre állást és a katasztrófa utáni helyreállítást.

Oracle Database Enterprise Edition az Oracle-adatőr hasznos szolgáltatás a vész-helyreállításhoz. Beállíthat egy készenléti adatbázis-példányt egy [párosított Azure-régióban](../../../best-practices-availability-paired-regions.md) , és beállíthatja az adatkezelő feladatátvételét a vész-helyreállításhoz. A nulla adatvesztés érdekében javasoljuk, hogy az aktív adatvédelem mellett telepítsen egy Oracle-adatvédelmet futtató távoli szinkronizálási példányt. 

Ha az alkalmazás engedélyezi a késést (alapos tesztelésre van szükség), érdemes lehet beállítani az adatvédelemre vonatkozó távoli szinkronizálási példányt egy másik rendelkezésre állási zónában, mint az Oracle elsődleges adatbázisa. A **maximális rendelkezésre állási** mód használatával állíthatja be az ismételt fájlok szinkron átvitelét a távoli szinkronizálási példányra. Ezeket a fájlokat a rendszer aszinkron módon továbbítja a készenléti adatbázisba. 

Ha az alkalmazás a **maximális rendelkezésre állási** módban (szinkron) nem teszi lehetővé a távoli szinkronizálási példány beállítását egy másik rendelkezésre állási zónában, akkor az elsődleges adatbázissal megegyező rendelkezésre állási zónában állíthatja be a távoli szinkronizálási példányt. A rendelkezésre álláshoz érdemes lehet több távoli szinkronizálási példányt beállítani az elsődleges adatbázishoz közel, és legalább egy példányt a készenléti adatbázishoz (ha a szerepkör átalakul). Tudjon meg többet [az Oracle-](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)adatvédelemről.

Az Oracle Standard Edition-adatbázisok használatakor olyan ISV-megoldások érhetők el, mint például a DBVisit készenléti szolgáltatás, amely lehetővé teszi a magas rendelkezésre állás és a vész-helyreállítás beállítását.

## <a name="reference-architectures"></a>Referenciaarchitektúrák

### <a name="oracle-data-guard"></a>Oracle Data Guard

Az Oracle-adatkezelő biztosítja a magas rendelkezésre állást, az adatvédelmet és a vész-helyreállítást a vállalati adatvédelemben. Az adatkezelő a készenléti adatbázisokat az elsődleges adatbázis tranzakciós szempontból konzisztens példányaiként tárolja. Az elsődleges és a másodlagos adatbázis közötti távolságtól, valamint a késéshez tartozó alkalmazási toleranciatől függően beállíthatja a szinkron vagy aszinkron replikálást. Ezt követően, ha az elsődleges adatbázis egy tervezett vagy nem tervezett leállás miatt nem érhető el, akkor az adatvédelem minimalizálja a készenléti adatbázist az elsődleges szerepkörre, és minimalizálja az állásidőt. 

Az Oracle-adatvédelem használatakor a másodlagos adatbázist is megnyithatja csak olvasási célokra. Ezt a konfigurációt aktív adatvédelemnek nevezzük. Oracle Database 12c egy adatvédelmes távoli szinkronizálási példány nevű funkciót vezetett be. Ez a példány lehetővé teszi az Oracle-adatbázis nulla adatvesztési konfigurációjának beállítását anélkül, hogy a teljesítményt meg kellene sérteni.

> [!NOTE]
> Az aktív adatvédelemhez további licencelés szükséges. Ez a licenc a távoli szinkronizálás funkció használatához is szükséges. Lépjen kapcsolatba az Oracle-képviselőjével, és beszéljen a licencelési következményeiről.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle-adatvédelem a FSFO
Az Oracle-adatvédelem Fast-Start Feladatátvételsel (FSFO) további rugalmasságot biztosíthat a közvetítő külön gépen való beállításával. Az adatőr-közvetítő és a másodlagos adatbázis egyaránt futtatja a megfigyelőt, és megfigyelheti az elsődleges adatbázist az állásidőhöz. Ez lehetővé teszi, hogy a redundancia az adatkezelő megfigyelője telepítőben is elérhető legyen. 

A Oracle Database 12,2-es vagy újabb verziójának használatával több megfigyelőt is beállíthat egyetlen Oracle-adatőr-átvitelszervező-konfigurációval. Ez a beállítás további rendelkezésre állást biztosít, abban az esetben, ha az egyik megfigyelő és a másodlagos adatbázis leállását tapasztalja. Az adatőr-közvetítő könnyű, és viszonylag kis virtuális gépen is üzemeltethető. Ha többet szeretne megtudni a adatőr-közvetítőről és annak előnyeiről, tekintse meg a jelen témakör [Oracle-dokumentációját](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) .

A következő ábra egy ajánlott architektúra az Oracle-adatvédelem az Azure-ban rendelkezésre állási zónák használatával történő használatához. Ez az architektúra lehetővé teszi, hogy a virtuális gép 99,99%-os ÜZEMIDŐt biztosítson.

![Az Azure-beli Oracle-adatvédelmet és rendelkezésre állási zónákat használó ajánlott architektúrát bemutató ábra.](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

Az előző ábrán az ügyfélrendszer az Oracle-háttérrel rendelkező egyéni alkalmazást a weben keresztül éri el. A webes felület egy terheléselosztó-ben van konfigurálva. A webes frontend kezdeményezi a megfelelő alkalmazáskiszolgáló számára a munka kezelését. Az alkalmazáskiszolgáló lekérdezi az elsődleges Oracle-adatbázist. Az Oracle-adatbázis egy hiperszálas [memória-optimalizált virtuális géppel](../../sizes-memory.md) lett konfigurálva, amelynek [korlátozott alapszintű vCPU](../../../virtual-machines/constrained-vcpu.md) a licencelési költségek mentése és a teljesítmény maximalizálása. A teljesítmény és a magas rendelkezésre állás érdekében több prémium vagy Ultra lemez (Managed Disks) használatos.

Az Oracle-adatbázisok több rendelkezésre állási zónába kerülnek a magas rendelkezésre állás érdekében. Minden zóna egy vagy több független energiaellátással, hűtéssel és hálózatkezeléssel ellátott adatközpontból áll. A rugalmasság biztosítása érdekében a rendszer legalább három különálló zónát állít be az összes engedélyezett régióban. A rendelkezésre állási zónák a régión belüli fizikai elkülönítése védi az adatközpont hibáiból származó adatok védelmét. Emellett két FSFO megfigyelő is be van állítva két rendelkezésre állási zónában, hogy az adatbázis a másodlagos állapotba kerüljön és feladatátvételt hajtson végre, ha áramkimaradás történik. 

Az előző architektúrában látható zónánál további megfigyelőket és/vagy készenléti adatbázisokat állíthat be egy másik rendelkezésre állási zónában (ebben az esetben az 1.). Végül az Oracle-adatbázisok figyelése az Oracle Enterprise Manager (OEM) általi üzemidő és teljesítmény érdekében történik. Az OEM Emellett különböző teljesítmény-és használati jelentéseket is létrehozhat.

Azokon a régiókban, ahol a rendelkezésre állási zónák nem támogatottak, a rendelkezésre állási csoportok használatával a Oracle Databaset kiválóan elérhető módon helyezheti üzembe. A rendelkezésre állási csoportok lehetővé teszik, hogy a virtuális gépek 99,95%-os üzemidőt érjenek el. A következő ábra a használat hivatkozási architektúrája:

![Oracle Database a rendelkezésre állási csoportok használatával a adatőr-közvetítővel – FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Az Oracle Enterprise Manager virtuális gép nem helyezhető el rendelkezésre állási csoportba, mert az OEM-nek csak egyetlen példánya van üzembe helyezve.
> * Az ultra-lemezek jelenleg nem támogatottak a rendelkezésre állási csoport konfigurációjában.

#### <a name="oracle-data-guard-far-sync"></a>Oracle-adatvédelem – távoli szinkronizálás

Az Oracle-adatvédelem távoli szinkronizálása nulla adatvesztést biztosít az Oracle-adatbázisok számára. Ez a funkció lehetővé teszi az adatvesztés elleni védelmet abban az esetben, ha az adatbázis-gép meghibásodik. Az Oracle-alapú adatvédelem távoli szinkronizálását külön virtuális gépre kell telepíteni. A far Sync egy egyszerűsített Oracle-példány, amely csak egy ellenőrző fájllal, egy jelszavas fájllal, a SPFile és a készenléti naplókkal rendelkezik. Nincsenek adatfájlok vagy Rego naplófájlok. 

A nulla adatvesztés elleni védelem esetében az elsődleges adatbázis és a távoli szinkronizálási példány közötti szinkron kommunikációra van szükség. A rendszer a távoli szinkronizálási példányt az elsődlegesről szinkron módon fogadja, és aszinkron módon továbbítja azonnal az összes készenléti adatbázisba. Ez a beállítás csökkenti az elsődleges adatbázis terhelését is, mivel az összes készenléti adatbázis helyett csak az újbóli frissítést kell elküldeni a távoli szinkronizálási példányra. Ha egy távoli szinkronizálási példány meghiúsul, az adatkezelő automatikusan aszinkron átvitelt használ a másodlagos adatbázisnak az elsődleges adatbázisból a közel nulla adatvesztés elleni védelem fenntartásához. A rugalmasság érdekében az ügyfelek több szinkronizálási példányt is telepíthetnek minden egyes adatbázis-példányon (elsődleges és formátumú másodlagos zónák).

Az alábbi ábra egy magas rendelkezésre állású architektúra, amely az Oracle-adatvédelem távoli szinkronizálását használja:

![Oracle-adatbázis rendelkezésre állási zónák használatával a & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

Az előző architektúrában egy távoli szinkronizálási példány van üzembe helyezve ugyanabban a rendelkezésre állási zónában, mint az adatbázis-példány, amely csökkenti a kettő közötti késést. Azokban az esetekben, amikor az alkalmazás késésre érzékeny, érdemes lehet üzembe helyezni az adatbázist és a távoli szinkronizálási példányt vagy példányokat egy [közelségi elhelyezési csoportban](../../../virtual-machines/linux/proximity-placement-groups.md).

A következő ábra egy olyan architektúra, amely az Oracle-adatkezelő FSFO és a magas rendelkezésre állást és a vész-helyreállítást is lehetővé téve:

![A rendelkezésre állási zónák használata a vész-helyreállításhoz az adatvédelmet és a & Broker-FSFO Oracle Database](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

A GoldenGate lehetővé teszi, hogy a tranzakciós szinten lévő adatcsere és-manipuláció több, heterogén platformon legyen a vállalaton belül. Tranzakciós integritással és minimális terheléssel helyezi el a véglegesített tranzakciókat a meglévő infrastruktúrán. A moduláris architektúrája rugalmasságot biztosít a kiválasztott adatrekordok, tranzakciós változások és DDL (adatdefiníciós nyelv) változásainak különböző topológiák közötti kinyeréséhez és replikálásához.

Az Oracle GoldenGate kétirányú replikálás biztosításával lehetővé teszi az adatbázis magas rendelkezésre állású konfigurálását. Ez lehetővé teszi, hogy beállítson egy **több főkiszolgálós** vagy **aktív-aktív konfigurációt**. A következő ábra egy ajánlott architektúra az Azure-beli GoldenGate Active-Active Setup-hoz. A következő architektúrában az Oracle-adatbázis egy hiperszálas [memória-optimalizált virtuális géppel](../../sizes-memory.md) lett konfigurálva, amely [korlátozott alapszintű vCPU](../../../virtual-machines/constrained-vcpu.md) rendelkezik a licencelési költségek mentéséhez és a teljesítmény maximalizálásához. A teljesítmény és a rendelkezésre állás érdekében több prémium vagy Ultra lemez (Managed Disks) használatos.

![Oracle Database a rendelkezésre állási zónák használatával a adatőr-közvetítővel – FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Hasonló architektúra állítható be a rendelkezésre állási csoportok használatával olyan régiókban, ahol a rendelkezésre állási zónák jelenleg nem érhetők el.

Az Oracle GoldenGate olyan folyamatokkal rendelkezik, mint a kinyerés, a szivattyú és a replikálás, amelyek segítségével aszinkron módon replikálhatja adatait az egyik Oracle Database-kiszolgálóról a másikra. Ezek a folyamatok lehetővé teszik a kétirányú replikáció beállítását az adatbázis magas rendelkezésre állásának biztosítása érdekében, ha rendelkezésre áll a rendelkezésre állási zónák szintjének leállása. Az előző ábrán a kinyerési folyamat ugyanazon a kiszolgálón fut, mint az Oracle-adatbázis, míg az adatszivattyú és a replikált folyamatok egy különálló kiszolgálón futnak ugyanazon a rendelkezésre állási zónában. A replikálási folyamat az adatoknak a másik rendelkezésre állási zónában lévő adatbázisból való fogadására szolgál, és a rendelkezésre állási zónában véglegesíti az adatokkal az Oracle Database-be. Hasonlóképpen, az adatszivattyú folyamata elküldi a kinyerési folyamat által kinyert adatokat a másik rendelkezésre állási zónában lévő replikálási folyamatnak. 

Míg az előző architektúra ábrán az adatszivattyú és a replikálási folyamat egy külön kiszolgálón konfigurálva van, az összes Oracle GoldenGate-folyamat ugyanazon a kiszolgálón állítható be, a kiszolgáló kapacitása és kihasználtsága alapján. A kiszolgáló használati mintájának megismeréséhez mindig tekintse meg a AWR-jelentést és az Azure mérőszámait.

Ha az Oracle GoldenGate kétirányú replikációját különböző rendelkezésre állási zónákban vagy különböző régiókban állítja be, fontos, hogy a különböző összetevők közötti késés az alkalmazás számára elfogadható legyen. A rendelkezésre állási zónák és régiók közötti késés változhat, és több tényezőtől függ. Javasoljuk, hogy a különböző rendelkezésre állási zónákban és/vagy régiókban az alkalmazási rétegek és az adatbázis szintje között ellenőrizze, hogy az megfelel-e az alkalmazás teljesítményére vonatkozó követelményeknek.

Az alkalmazás szintje a saját alhálózatában állítható be, és az adatbázis szintje elkülöníthető a saját alhálózatában is. Ha lehetséges, érdemes lehet az [Azure Application Gateway](../../../application-gateway/overview.md) használni az alkalmazás-kiszolgálók közötti adatforgalom elosztására. Az Azure Application Gateway egy robusztus webes forgalmi terheléselosztó. Lehetővé teszi a cookie-alapú munkamenet-affinitást, amely a felhasználói munkamenetet ugyanazon a kiszolgálón tartja, így minimalizálva az adatbázis ütközéseit. A Application Gateway alternatívái [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) és az [Azure Traffic Manager](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Oracle-skálázás

A horizontális skálázás egy Oracle 12,2-es verzióban bevezetett adatmodell-minta. Lehetővé teszi, hogy horizontálisan particionálja és méretezze az adatait a független adatbázisok között. Ez egy megosztás nélküli architektúra, ahol az egyes adatbázisok egy dedikált virtuális gépen futnak, ami lehetővé teszi a magas olvasási és írási sebességet a rugalmasság és a megnövekedett rendelkezésre állás mellett. Ez a minta kiküszöböli az egyes meghibásodási pontokat, elkülöníti a hibákat, és leállás nélkül teszi lehetővé a működés közbeni frissítését. Egy szegmens vagy adatközpont-szintű meghibásodás leállása nem befolyásolja az egyéb adatközpontokban lévő többi szegmens teljesítményét vagy rendelkezésre állását. 

A horizontális skálázás olyan nagy átviteli sebességű OLTP-alkalmazások számára megfelelő, amelyek nem tudnak leállást biztosítani. Minden olyan sor, amely ugyanazzal a horizontális Felskálázási kulccsal rendelkezik, mindig ugyanazon a szegmensen legyenek garantálva, így a nagy konzisztenciát biztosító teljesítmény növekszik. A horizontális felskálázást használó alkalmazásoknak jól definiált adatmodell-és adatterjesztési stratégiát kell tartalmazniuk (konzisztens kivonatoló, Range, List vagy Composite), amely elsősorban az adatokat egy horizontális Felskálázási kulccsal (például *Vevőkód* vagy *accountNum*) használja. A horizontális skálázás azt is lehetővé teszi, hogy az egyes adatkészleteket közelebb kerüljön a végfelhasználóhoz, így segítve a teljesítmény-és megfelelőségi követelmények teljesítését.

Javasoljuk, hogy a magas rendelkezésre állás és a vész-helyreállítás érdekében replikálja a szegmenseket. Ez a beállítás Oracle-technológiákkal, például Oracle-adatgárda vagy Oracle-GoldenGate használatával végezhető el. A replikálási egység lehet egy szegmens, egy szilánk vagy egy szegmensek csoportja. A szegmensben lévő adatbázisok rendelkezésre állását nem érinti egy vagy több szegmens kiesése vagy lassulása. A magas rendelkezésre állás érdekében a készenléti szegmensek ugyanabban a rendelkezésre állási zónában helyezhetők el, ahol az elsődleges szegmensek vannak elhelyezve. A vész-helyreállításhoz a készenléti szegmensek egy másik régióban is megtalálhatók. Ezen régiók forgalmának kiszolgálásához több régióban is üzembe helyezhet szegmenseket. További információk a többhelyes adatbázis magas rendelkezésre állásának és replikálásának konfigurálásáról az Oracle horizontális Felskálázási [dokumentációjában](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)olvashatók.

Az Oracle horizontális felskálázása elsősorban a következő összetevőkből áll. Ezekről az összetevőkről további információt az Oracle horizontális Felskálázási [dokumentációjában](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)találhat:

- **Szilánk Catalog** – speciális célú Oracle-adatbázis, amely az összes szegmens adatbázis konfigurációs adatának állandó tárolója. A rendszer az összes konfigurációs módosítást, például a szegmensek hozzáadását vagy eltávolítását, az adatleképezést, valamint a DDLs-ket a szegmens-adatbázisban kezdeményezi. A szegmens katalógusa tartalmazza az SDB-ben lévő összes duplikált tábla fő példányát is. 

  A szegmensek katalógusa az anyagilag kiosztott nézetek használatával automatikusan replikálja a duplikált táblák módosításait az összes szegmensben. A szegmens-katalógus adatbázisa olyan lekérdezési koordinátorként is funkcionál, amely a horizontális Felskálázási kulcsot nem megadó több szegmensű lekérdezés és lekérdezés feldolgozására szolgál. 
  
  Az Oracle-adatvédelmet a rendelkezésre állási zónák vagy rendelkezésre állási csoportok használata a szegmens katalógus magas rendelkezésre állása esetén ajánlott eljárás. A szegmens-katalógus rendelkezésre állása nem befolyásolja a szilánkokra osztott adatbázis rendelkezésre állását. A szegmensek katalógusában a leállás csak a karbantartási műveleteket és a többszegmenses lekérdezéseket érinti az adatvédelem feladatátvételének rövid időszakában. Az online tranzakciókat továbbra is az SDB irányítja és hajtja végre, és nem érinti a katalógus kimaradását.

- Szegmensek **igazgatói** – az egyes régiókban vagy rendelkezésre állási zónákban üzembe helyezhető, a szegmensekben található egyszerűsített szolgáltatások. A szegmensek az Oracle horizontális felskálázása keretében üzembe helyezett globális Service managerek. A magas rendelkezésre állás érdekében ajánlott legalább egy szegmens-igazgatót üzembe helyezni minden rendelkezésre állási zónában, amelyben a szegmensek találhatók. 

  Amikor először csatlakozik az adatbázishoz, az útválasztási adatokat egy szegmens igazgató állítja be, és gyorsítótárazza a további kérésekhez, és megkerüli a szegmens igazgatót. Ha a munkamenet egy szegmenssel lett létrehozva, a rendszer az összes SQL-lekérdezést és a DML-t az adott szegmens hatókörében támogatja és hajtja végre. Ez az Útválasztás gyors, és minden olyan OLTP-munkaterheléshez használatos, amely a szegmensen belüli tranzakciókat hajtja végre. Ajánlott közvetlen útválasztást használni minden olyan OLTP számítási feladathoz, amely a legmagasabb szintű teljesítményt és rendelkezésre állást igényli. Az útválasztási gyorsítótár automatikusan frissül, ha egy szegmens elérhetetlenné válik, vagy megváltozik a horizontális Felskálázási topológia. 
  
  A nagy teljesítményű, Adatfüggő útválasztáshoz az Oracle azt javasolja, hogy használjon-e egy kapcsolódási készletet a többrétegű adatbázisban lévő adatokhoz való hozzáféréshez. Az Oracle-kapcsolatok készletei, a nyelvspecifikus kódtárak és az illesztőprogramok támogatják az Oracle horizontális felskálázását. További részletekért tekintse meg az Oracle horizontális Felskálázási [dokumentációját](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) .

- **Globális szolgáltatás** – a globális szolgáltatás hasonló a normál adatbázis-szolgáltatáshoz. Az adatbázis-szolgáltatás összes tulajdonságán kívül a globális szolgáltatás a horizontálisan használt adatbázisokra, például a régiók közötti affinitásra, valamint a szegmens és a replikálási késések tűréshatárára is jellemző tulajdonságokkal rendelkezik. Csak egy globális szolgáltatást kell létrehozni, hogy az adatok beolvasása/írása egy szilánkokra osztott adatbázisba történjen. Ha aktív adatvédelmet használ, és a szegmensek írásvédett replikáit állítja be, létrehozhat egy másik gGobal szolgáltatást a csak olvasható számítási feladatokhoz. Az ügyfél ezeket a globális szolgáltatásokat használhatja az adatbázishoz való kapcsolódáshoz.

- Szegmens **adatbázisok** – a szegmens adatbázisok az Oracle-adatbázisok. Minden adatbázis replikálása az Oracle-adatőr használatával történik egy olyan közvetítői konfigurációban, amelyen engedélyezve van a Fast-Start feladatátvétel (FSFO). Az egyes szegmenseken nem kell beállítania az adatvédelem feladatátvételét és replikálását. Ezt a rendszer automatikusan konfigurálja és telepíti a megosztott adatbázis létrehozásakor. Ha egy adott szegmens meghibásodása meghiúsul, az Oracle-megosztás automatikusan feladatátvételt hajt végre az elsődlegesről a készenléti állapotba.

Az Oracle-alapú többszintű adatbázisok üzembe helyezéséhez és kezeléséhez két interfész használható: Oracle Enterprise Manager Cloud Control GUI és/vagy a `GDSCTL` parancssori segédprogram. A Felhőbeli vezérlés használatával akár a rendelkezésre állás és a teljesítmény különböző szegmenseit is figyelheti. A `GDSCTL DEPLOY` parancs automatikusan létrehozza a szegmenseket és a hozzájuk tartozó figyelőket. Emellett a parancs automatikusan telepíti a rendszerszintű magas rendelkezésre állású, a rendszergazda által megadott replikációs konfigurációt.

Az adatbázisok többféleképpen is elvégezhető:

* Rendszer által felügyelt horizontális felskálázás – automatikusan osztja szét a szegmenseket a particionálással
* Felhasználó által definiált horizontális skálázás – lehetővé teszi az adat hozzárendelésének megadását a szegmensekhez, ami jól működik, ha szabályozási vagy adathonosítási követelmények vannak megadva.)
* Összetett horizontális felskálázás – A rendszer által felügyelt és felhasználó által definiált horizontális skálázás különböző _shardspaces_
* Table alpartíciók – hasonló egy normál particionált táblához.

További információ az Oracle dokumentációjának különböző horizontális Felskálázási [módszereiről](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) .

Míg a szilánkokra osztott adatbázisok úgy tűnhet, mint egyetlen adatbázis az alkalmazásokhoz és a fejlesztőknek, ha a nem szilánkos adatbázisból egy többrétegű adatbázisba migrálnak 

A duplikált táblák az összes szegmensen tárolódnak, míg a szilánkokra osztott táblázatok különböző szegmensekben vannak elosztva. Javasoljuk, hogy duplikálja a kis-és dimenziós táblákat, és ossza meg a tények táblázatait. Az adatai betölthetők a szilánkokra osztott adatbázisba a szegmens-katalógusban központi koordinátorként vagy az egyes szegmenseken futó adatszivattyú használatával. További információ az adatáttelepítés az Oracle-dokumentációban található, többhelyes [adatbázisba való áttelepítéséről](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) .

#### <a name="oracle-sharding-with-data-guard"></a>Oracle-horizontális skálázás adatvédelemmel

Az Oracle-adatőr a rendszer által felügyelt, felhasználó által definiált és összetett horizontális Felskálázási módszerekkel végezhető.

Az alábbi ábra egy Oracle-horizontális Felskálázási útmutató, amely az egyes szegmensek magas rendelkezésre állására szolgáló Oracle-adatvédelemmel rendelkezik. Az architektúra diagram egy összetett horizontális Felskálázási _módszert_ mutat be. Az architektúra-diagram valószínűleg különbözik az adattárolással, a terheléselosztással, a magas rendelkezésre állással, a vész-helyreállítással és más módszerekkel, amelyek eltérő módon használhatók. Az Oracle horizontális felskálázása lehetővé teszi ezen követelmények teljesítését, valamint vízszintesen és hatékonyan méretezheti ezeket a lehetőségeket. Hasonló architektúra is üzembe helyezhető az Oracle GoldenGate használatával.

![A rendelkezésre állási zónák és a FSFO adatvédelme Oracle Database](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

A rendszer által felügyelt horizontális skálázás a legegyszerűbben konfigurálható és kezelhető, a felhasználó által definiált horizontális skálázási vagy összetett horizontális felskálázás olyan helyzetekben, ahol az adatok és az alkalmazások földrajzilag elosztottak, vagy olyan helyzetekben, amikor az egyes szegmensek replikálásának szabályozására van szükség. 

Az előző architektúrában az összetett horizontális felhasználható az adatelosztásra és az alkalmazás szintjeinek horizontális felskálázására. Az összetett skálázás a rendszer által felügyelt és a felhasználó által definiált horizontális skálázás kombinációja, és így mindkét módszer előnyeit biztosítja. Az előző forgatókönyvben az adatok először több shardspaces vannak szétosztva régiónként. Ezt követően az adatokat a shardspace több szegmensének konzisztens kivonatával particionálja. Mindegyik shardspace több shardgroups tartalmaz. Az egyes shardgroup több szegmensből állnak, és ebben az esetben a replikáció "egység". Minden shardgroup tartalmazza a shardspace összes értékét. Az a1 és a B1 Shardgroups elsődleges Shardgroups, míg a Shardgroups a2 és B2 is készenléti állapotban van. Dönthet úgy is, hogy az egyes szegmensek a replikáció egységei, nem pedig shardgroup.

Az előző architektúrában a magas rendelkezésre állás érdekében minden rendelkezésre állási zónában a GSM/szegmens igazgató üzembe helyezése történik. Javasoljuk, hogy adatközpont/régió szerint telepítsen legalább egy GSM/szilánk-Director-kezelőt. Emellett az alkalmazáskiszolgáló egy példánya is telepítve van minden rendelkezésre állási zónában, amely egy shardgroup tartalmaz. Ez a beállítás lehetővé teszi, hogy az alkalmazás a késést az alkalmazáskiszolgáló és az adatbázis/shardgroup között alacsony legyen. Ha egy adatbázis meghibásodik, a készenléti adatbázissal azonos zónában lévő alkalmazáskiszolgáló a kérelmeket az adatbázis-szerepkör átállása után képes kezelni. Az Azure Application Gateway és a szegmens igazgatója a kérések és válaszok késésének nyomon követését és ennek megfelelően továbbítja a kérelmeket.

Alkalmazási szempontból az ügyfélrendszer egy kérést küld az Azure Application Gateway (vagy más terheléselosztási technológiákra az Azure-ban), amely átirányítja a kérést az ügyfélhez legközelebb eső régióba. Az Azure Application Gateway támogatja a Sticky-munkameneteket is, így az azonos ügyféltől érkező kéréseket ugyanahhoz az alkalmazáskiszolgáló irányítja. Az alkalmazáskiszolgáló a kapcsolat-készletezést használja az adatelérési illesztőprogramokban. Ez a funkció olyan illesztőprogramokban érhető el, mint például a JDBC, a ODP.NET, a OCI stb. Az illesztőprogramok felhasználhatják a kérelem részeként megadott horizontális Felskálázási kulcsokat. Az [Oracle Universal csatlakozókábel (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) a JDBC-ügyfelek számára engedélyezheti a nem Oracle-alkalmazások ügyfeleit, például az Apache Tomcat és az IIS szolgáltatást az Oracle-alapú horizontális felskálázáshoz. 

A kezdeti kérelem során az alkalmazáskiszolgáló csatlakozik a saját régiójában található szegmens igazgatóhoz, hogy útválasztási információkat kapjon a szegmenshez, amelyre a kérést át kell irányítani. Az átadott horizontális Felskálázási kulcs alapján a Director a megfelelő szegmensre irányítja az alkalmazáskiszolgáló szolgáltatást. Az alkalmazáskiszolgáló egy térképet felépítve gyorsítótárazza ezeket az információkat, és a későbbi kérelmek esetében megkerüli a szegmens igazgatót, és a kérelmeket közvetlenül a szegmensbe irányítja.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle-skálázás a GoldenGate

Az alábbi ábra egy Oracle-alapú, az egyes szegmensek magas rendelkezésre állására szolgáló Oracle-GoldenGate mutató hivatkozási architektúra. Az előző architektúrának megfelelően ez az architektúra csak egy Azure-régióban (több rendelkezésre állási zónában) található magas rendelkezésre állást ábrázolja. Az Oracle GoldenGate használatával egy többrégiós magas rendelkezésre állású, horizontálisan felhasználható adatbázis is üzembe helyezhető (az előző példához hasonlóan).

![A rendelkezésre állási zónák használatával Oracle Database a GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

Az előző viszonyítási architektúra a _rendszer által felügyelt_ horizontális Felskálázási módszert használja az adatszegmenshez. Mivel az Oracle GoldenGate replikációja egy adathalmaz szintjén történik, az egyik szegmensbe replikált adatmennyiség fele replikálható egy másik szegmensbe. A másik fél replikálható egy másik szegmensbe. 

Az adatok replikálásának módja a replikálási tényezőtől függ. A 2. replikációs faktorral a shardgroup három szegmensében két különböző adathalmaz található. Hasonlóképpen, ha a shardgroup 3 és három szegmens replikációs tényezője van, a rendszer az egyes szegmensekben lévő összes adattal replikálja a shardgroup minden más szegmensére. A shardgroup egyes szegmensei eltérő replikációs tényezővel rendelkezhetnek. Ez a telepítő segítséget nyújt a magas rendelkezésre állás és a vész-helyreállítási terv hatékony definiálásához a shardgroup és több shardgroups között.

Az előző architektúrában a shardgroup a és A B shardgroup is ugyanazokat az adategységeket tartalmazzák, de különböző rendelkezésre állási zónákban találhatók. Ha a (z) és A (z) "B" shardgroup mind a (z), mind a (z) shardgroup ugyanaz, mint 3, akkor a többrétegű tábla minden egyes sora/részlete 6 alkalommal replikálódik a Ha a shardgroup egy 3-as replikációs tényezővel rendelkezik, és a B shardgroup replikációs tényezője 2, az egyes sorok/adattömbök 5 alkalommal lesznek replikálva a két shardgroups között.

Ez a beállítás megakadályozza az adatvesztést, ha egy példány szintű vagy rendelkezésre állási zóna szintű hiba történik. Az alkalmazás rétege képes az egyes szegmensek olvasására és írására. Az ütközések csökkentése érdekében az Oracle horizontálisan kijelöl egy "fő adathalmazt" a kivonatoló értékek minden tartománya számára. Ez a funkció biztosítja, hogy az adott adathalmazra vonatkozó kéréseket a rendszer a megfelelő adathalmazra irányítsa. Emellett az Oracle GoldenGate automatikus ütközések észlelését és feloldását is lehetővé teszi az esetlegesen felmerülő ütközések kezelésére. További információk és korlátozások a GoldenGate Oracle-skálázással való megvalósításával kapcsolatban: Oracle dokumentációja az [Oracle GoldenGate és a szilánkokra osztott adatbázis](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)használatával.

Az előző architektúrában a magas rendelkezésre állás érdekében minden rendelkezésre állási zónában a GSM/szegmens igazgató üzembe helyezése történik. Javasoljuk, hogy adatközpont vagy régió szerint telepítsen legalább egy GSM/szilánk-Director-kezelőt. Emellett az alkalmazáskiszolgáló egy példánya is telepítve van minden rendelkezésre állási zónában, amely egy shardgroup tartalmaz. Ez a beállítás lehetővé teszi, hogy az alkalmazás a késést az alkalmazáskiszolgáló és az adatbázis/shardgroup között alacsony legyen. Ha egy adatbázis meghibásodik, a készenléti adatbázissal azonos zónában lévő alkalmazáskiszolgáló a kérelmeket az adatbázis-szerepkör átállása után képes kezelni. Az Azure Application Gateway és a szegmens igazgatója a kérések és válaszok késésének nyomon követését és ennek megfelelően továbbítja a kérelmeket.

Alkalmazási szempontból az ügyfélrendszer egy kérést küld az Azure Application Gateway (vagy más terheléselosztási technológiákra az Azure-ban), amely átirányítja a kérést az ügyfélhez legközelebb eső régióba. Az Azure Application Gateway támogatja a Sticky-munkameneteket is, így az azonos ügyféltől érkező kéréseket ugyanahhoz az alkalmazáskiszolgáló irányítja. Az alkalmazáskiszolgáló a kapcsolat-készletezést használja az adatelérési illesztőprogramokban. Ez a funkció olyan illesztőprogramokban érhető el, mint például a JDBC, a ODP.NET, a OCI stb. Az illesztőprogramok felhasználhatják a kérelem részeként megadott horizontális Felskálázási kulcsokat. Az [Oracle Universal csatlakozókábel (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) a JDBC-ügyfelek számára engedélyezheti a nem Oracle-alkalmazások ügyfeleit, például az Apache Tomcat és az IIS szolgáltatást az Oracle-alapú horizontális felskálázáshoz. 

A kezdeti kérelem során az alkalmazáskiszolgáló csatlakozik a saját régiójában található szegmens igazgatóhoz, hogy útválasztási információkat kapjon a szegmenshez, amelyre a kérést át kell irányítani. Az átadott horizontális Felskálázási kulcs alapján a Director a megfelelő szegmensre irányítja az alkalmazáskiszolgáló szolgáltatást. Az alkalmazáskiszolgáló egy térképet felépítve gyorsítótárazza ezeket az információkat, és a későbbi kérelmek esetében megkerüli a szegmens igazgatót, és a kérelmeket közvetlenül a szegmensbe irányítja.

## <a name="patching-and-maintenance"></a>Javítás és karbantartás

Az Oracle számítási feladatainak az Azure-ba történő üzembe helyezése során a Microsoft gondoskodik a gazdagép operációsrendszer-szintű javításáról. A tervezett karbantartást az ügyfélnek előre kell tájékoztatni az ügyfelek számára. Két különböző Availability Zones kiszolgáló egyidejű kijavítása nem történik meg egyszerre. A virtuális gépek karbantartásával és javításával kapcsolatos további információkért lásd: [virtuális gépek rendelkezésre állásának kezelése](../../availability.md) . 

A virtuális gépi operációs rendszer javítása [Azure Automation Update Management](../../../automation/update-management/overview.md)használatával automatizálható. Az Oracle-adatbázis javítása és karbantartása automatizálható és ütemezhető az [Azure-folyamatok](/azure/devops/pipelines/get-started/what-is-azure-pipelines) vagy a [Azure Automation Update Management](../../../automation/update-management/overview.md) használatával az állásidő csökkentése érdekében. Tekintse meg a [folyamatos szállítást és a kék/zöld üzembe helyezést](/azure/devops/learn/what-is-continuous-delivery) , hogy megtudja, hogyan használható az Oracle-adatbázisok kontextusában.

## <a name="architecture-and-design-considerations"></a>Architektúra és kialakítási szempontok

- Vegye fontolóra a hiperszálas [memória-optimalizált virtuális gép](../../sizes-memory.md) [korlátozott alapszintű Oracle Database vCPU](../../../virtual-machines/constrained-vcpu.md) való használatát a licencelési költségek megtakarítása és a teljesítmény maximalizálása érdekében. A teljesítmény és a rendelkezésre állás érdekében több prémium vagy Ultra lemez (Managed Disks) használatát használhatja.
- A felügyelt lemezek használatakor a lemez/eszköz neve változhat az újraindítások során. Javasoljuk, hogy a név helyett az eszköz UUID-t használja, hogy a csatlakoztatások megmaradjanak az újraindítások között. További információt [itt](/previous-versions/azure/virtual-machines/linux/configure-raid#add-the-new-file-system-to-etcfstab) találhat.
- A rendelkezésre állási zónák használatával magas rendelkezésre állást érhet el a régióban.
- Érdemes lehet Ultra Disks (ha elérhető) vagy prémium szintű lemezeket használni az Oracle-adatbázishoz.
- Érdemes lehet készenléti Oracle-adatbázist beállítani egy másik Azure-régióban az Oracle-adatgárda használatával.
- Érdemes lehet [Proximity elhelyezési csoportokat](../../co-location.md#proximity-placement-groups) használni az alkalmazás és az adatbázis szintjei közötti késés csökkentése érdekében.
- Az [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) beállítása a felügyelethez, a figyeléshez és a naplózáshoz.
- Érdemes lehet az Oracle automatikus tárolási felügyelet (ASM) használatát használni az adatbázis egyszerűsített tárterület-kezeléséhez.
- Az [Azure-folyamatokkal](/azure/devops/pipelines/get-started/what-is-azure-pipelines) az adatbázis javításait és frissítéseit leállás nélkül kezelheti.
- A Felhőbeli natív mintázatok, például az [újrapróbálkozási minta](/azure/architecture/patterns/retry), az [áramkör-megszakító mintázata](/azure/architecture/patterns/circuit-breaker)és a [Felhőbeli tervezési minták útmutatójában](/azure/architecture/patterns/) definiált egyéb minták hozzáadásával az alkalmazáshoz tartozó kód finomhangolása is rugalmasabb lehet.

## <a name="next-steps"></a>Következő lépések

Tekintse át a forgatókönyvre vonatkozó alábbi Oracle-hivatkozási cikkeket.

- [Az Oracle-adatvédelem bemutatása](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Az Oracle-adatőr-átvitelszervező fogalmai](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Az Oracle-GoldenGate konfigurálása Active-Active magas rendelkezésre álláshoz](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Az Oracle horizontális felskálázásának áttekintése](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active-adatvédelem – távoli szinkronizálás – nulla adatvesztés bármilyen távolságban](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
