---
title: Mi az Azure SQL Database szolgáltatás?
description: 'Ismerkedjen meg SQL Database: a felhőben a Microsoft RDBMS-kezelő rendszer technikai részletei és képességei.'
keywords: bevezetés az sql-be,az sql bemutatása,mi az sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: 65710ae40a971d214068f0e2686f78fb994a967e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601397"
---
# <a name="what-is-azure-sql-database"></a>Mi az Azure SQL Database?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az Azure SQL Database egy olyan adatbázismotor, amely egy teljes mértékben felügyelt, szolgáltatásként nyújtott platform (PaaS), és képes kezelni az adatbázisok felügyeletével kapcsolatos legtöbb feladatot, például a frissítéseket, a javítások telepítését, a biztonsági mentések készítését és a felhasználói beavatkozás nélküli monitorozást. Az Azure SQL Database mindig az SQL Server-adatbázismotor legfrissebb stabil verzióján és javított operációs rendszeren fut, 99,99%-os rendelkezésre állással. Az Azure SQL Database-be beépített PaaS-funkciók lehetővé teszik, hogy azon alkalmazási területre jellemző adatbázisgazdai és -optimalizálási tevékenységekre koncentráljon, amelyek kritikus fontosságúak a vállalat számára.

Az Azure SQL Database használatával magas rendelkezésre állású és nagy teljesítményű adattároló réteget hozhat létre az Azure-ban az alkalmazások és a megoldások számára. A SQL Database a különböző modern felhőalapú alkalmazások számára megfelelő választás lehet, mivel lehetővé teszi a kapcsolati és [nem kapcsolati struktúrák](../multi-model-features.md), például a diagramok, a JSON, a térbeli és az XML feldolgozását.

A Azure SQL Database az [Microsoft SQL Server adatbázismotor](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json)legújabb stabil verziójára épül. Használhatja a speciális lekérdezés-feldolgozási funkciókat, például [a nagy teljesítményű memóriában lévő technológiákat és az](../in-memory-oltp-overview.md) [intelligens lekérdezések feldolgozását](/sql/relational-databases/performance/intelligent-query-processing?toc=%2fazure%2fsql-database%2ftoc.json). Az SQL Server legújabb funkciói először az SQL Database-ben jelennek meg, és csak ezután válnak elérhetővé magában az SQL Serverben. Az SQL Server legújabb funkciói a karbantartás és frissítés terhei nélkül állnak az Ön rendelkezésére úgy, hogy már több millió adatbázison tesztelték őket. 

A SQL Database lehetővé teszi a teljesítmény egyszerű meghatározását és méretezését két különböző beszerzési modellen belül: egy [virtuális mag-alapú vásárlási modellt](service-tiers-vcore.md) és egy [DTU-alapú vásárlási modellt](service-tiers-dtu.md). Az SQL Database egy teljeskörűen felügyelt szolgáltatás, amely beépített magas rendelkezésre állással, biztonsági mentésekkel és egyéb gyakori karbantartási műveletekkel rendelkezik. A Microsoft az SQL és az operációs rendszer kódjának összes javítását és frissítését kezeli. A mögöttes infrastruktúrát Önnek nem kell kezelnie.

Ha még nem ismeri a Azure SQL Database, tekintse meg a részletes [Azure SQL-videó sorozatának](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner) *Azure SQL Database áttekintő* videóját:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

> [!TIP]
> Hogyan lehet jobbá tenni az Azure SQL-t? [Vegye fel a kérdőívet](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="deployment-models"></a>Üzembe helyezési modellek

Azure SQL Database a következő telepítési beállításokat biztosítja egy adatbázishoz:

- Az [önálló adatbázisok](single-database-overview.md) egy teljes körűen felügyelt, elkülönített adatbázist jelentenek. Ezt a lehetőséget akkor használhatja, ha olyan modern Felhőbeli alkalmazásokkal és szolgáltatásokkal rendelkezik, amelyeknek egyetlen megbízható adatforrásra van szükségük. Egy adatbázis a [SQL Server adatbázis-motorban](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json)található [tárolt adatbázishoz](/sql/relational-databases/databases/contained-databases?toc=%2fazure%2fsql-database%2ftoc.json) hasonlít.
- A [rugalmas készlet](elastic-pool-overview.md) olyan [önálló adatbázisok](single-database-overview.md) gyűjteménye, amelyek erőforrásainak, például a processzornak vagy a memóriának a közös készletét tartalmazzák. Az önálló adatbázisok egy rugalmas készletbe helyezhetők és kivehetők.

> [!IMPORTANT]
> A SQL Database és SQL Server közötti különbségek megismeréséhez, valamint a különböző Azure SQL Database lehetőségek közötti különbségekről lásd: [SQL Database szolgáltatások](features-comparison.md).

SQL Database kiszámítható teljesítményt nyújt több erőforrástípus, szolgáltatási réteg és számítási méret alapján. Dinamikus skálázhatóságot biztosít leállás nélkül, beépített intelligens optimalizálással, globális skálázhatósággal és rendelkezésre állással, valamint speciális biztonsági beállításokkal. Ezek a funkciók lehetővé teszik, hogy a virtuális gépek és az infrastruktúra kezelése helyett az alkalmazások gyors fejlesztésére és a piacra kerülési idő felgyorsítására összpontosítsanak. SQL Database jelenleg a világ 38-es adatközpontjában található, így az adatbázist az Ön közelében lévő adatközpontban futtathatja.

## <a name="scalable-performance-and-pools"></a>Méretezhető teljesítmény és készletek

Megadhatja a hozzárendelt erőforrások mennyiségét. 
- Az önálló adatbázisok esetében minden adatbázis el van különítve a többitől, és hordozható. Mindegyiknek saját garantált mennyiségű számítási, memória-és tárolási erőforrása van. Az adatbázishoz rendelt erőforrások mennyisége dedikált az adatbázishoz, és az Azure-ban más adatbázisokkal nem lesz megosztva. Az [önálló adatbázisok erőforrásainak dinamikus méretezése](single-database-scale.md) felfelé és lefelé is elvégezhető. Az önálló adatbázis beállítás különböző számítási, memória-és tárolási erőforrásokat biztosít a különböző igényekhez. Például az 1 – 80 virtuális mag, vagy 32 GB – 4 TB lehet. Az önálló adatbázisok [nagy kapacitású-szolgáltatási rétege](service-tier-hyperscale.md) lehetővé teszi, hogy a gyors biztonsági mentési és visszaállítási képességekkel bővítse a 100 TB-ot.
- A rugalmas készletek használatával a készletben lévő összes adatbázis által megosztott erőforrásokat rendelhet hozzá. Létrehozhat egy új adatbázist, vagy áthelyezheti a meglévő önálló adatbázisokat egy erőforrás-készletbe, így maximalizálhatja az erőforrások használatát és pénzt takaríthat meg. Ez a beállítás lehetővé teszi a [rugalmas készlet erőforrásainak](elastic-pool-scale.md) felfelé és lefelé történő dinamikus méretezését.

Az általános célú szolgáltatási szinten az első alkalmazását egy kis méretű, egyetlen adatbázison is létrehozhatja. Ezt követően manuálisan vagy programozott módon módosíthatja a szolgáltatási szintet az üzleti szempontból kritikus szolgáltatási rétegre, hogy megfeleljen a megoldás igényeinek. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak a szükséges erőforrásokért kell fizetnie.

A *dinamikus méretezhetőség* különbözik az automatikus *méretezéstől*. Automatikus skálázás esetén a szolgáltatás adott feltételek alapján, automatikusan méretez, míg a dinamikus méretezhetőség lehetővé teszi a manuális méretezést, amely nem jár állásidővel. Az egyetlen adatbázis-beállítás támogatja a manuális dinamikus méretezhetőséget, de nem automatikus méretezést. Ha automatikus megoldást keres, érdemes megfontolni a rugalmas készletek használatát, amely lehetővé teszi, hogy az adatbázisok osztozzanak egy készlet erőforrásain az egyes adatbázisok egyedi igényei alapján. Egy másik lehetőség a parancsfájlok használata, amelyek segítségével automatizálható egy adott adatbázis skálázhatósága. Példa: [egyetlen adatbázis figyelése és méretezése a PowerShell használatával](scripts/monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Vásárlási modellek

SQL Database a következő vásárlási modelleket kínálja:
- A [virtuális mag-alapú vásárlási modell](service-tiers-vcore.md) segítségével kiválaszthatja a virtuális mag számát, a memória mennyiségét, valamint a tárterület mennyiségét és sebességét. A virtuális mag-alapú vásárlási modell azt is lehetővé teszi, hogy a megtakarítások megszerzéséhez a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) is használja. További információ a Azure Hybrid Benefitről: a cikk későbbi, "gyakori kérdések" című szakasza.
- A [DTU-alapú vásárlási modell](service-tiers-dtu.md) a számítási, a memória-és az I/O-erőforrások keverékét kínálja három szolgáltatási szinten a nagy mennyiségű adatbázis-munkaterhelések támogatásához. Az egyes szintjein belüli számítási méretek különböző mennyiségű erőforrást biztosítanak, amelyhez további tárolási erőforrásokat adhat hozzá.
- A [kiszolgáló nélküli modell](serverless-tier-overview.md) automatikusan méretezi a számítási feladatok igénye alapján történő számítást, és a másodpercenként felhasznált számítások mennyiségére vonatkozó számlákat. A kiszolgáló nélküli számítási rétegek automatikusan szüneteltetik az adatbázisokat az inaktív időszakok során, amikor csak a tárterületet számlázzák, és automatikusan folytatják az adatbázisokat, amikor a tevékenység visszatér.

### <a name="service-tiers"></a>Szolgáltatásszintek

A Azure SQL Database három szolgáltatási szintet kínál, amelyek különböző típusú alkalmazásokhoz készültek:
- [Általános célú/standard](service-tier-general-purpose.md) szolgáltatási szint általános számítási feladatokhoz tervezve. Költségvetés-alapú, kiegyensúlyozott számítási és tárolási lehetőségeket kínál.
- [Üzletileg kritikus/prémium](service-tier-business-critical.md) szintű szolgáltatási szint a nagy tranzakciós sebességgel és a legalacsonyabb késleltetésű I/O-OLTP alkalmazásokhoz tervezve. Több elkülönített replika használatával a hibák legmagasabb rugalmasságát nyújtja.
- A [nagy kapacitású](service-tier-hyperscale.md) szolgáltatási szintje nagy OLTP-adatbázishoz lett tervezve, és lehetővé teszi a tárolási és a számítási kapacitások méretezését.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Rugalmas készletek az erőforrások kihasználtságának maximalizálásához

Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. A kiszámíthatatlan használati szokások miatt a költségek és az üzleti modell is nehezen kezelhető. A [rugalmas készletek](elastic-pool-overview.md) megoldást jelentenek erre a problémára. A teljesítményadatokat egy készlethez kell hozzárendelni, és nem egy önálló adatbázishoz. A készlet kollektív teljesítménybeli erőforrásaiért kell fizetnie, nem pedig egyetlen adatbázis teljesítményére.

   ![Az alapszintű, standard és prémium kiadásokban rugalmas készleteket bemutató ábra](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

Rugalmas készletekkel nem kell összpontosítania az adatbázis teljesítményének felfelé és lefelé állítására, mivel az erőforrások igénye ingadozik. A rugalmas készletbe helyezett adatbázisok szükség szerint használják fel a teljesítmény-erőforrásokat. A készletezett adatbázisok felhasználják, de nem lépik túl a készlet korlátait, így a díjszabás akkor is kiszámítható marad, ha az egyes adatbázisok használata nem.

[Hozzáadhat és eltávolíthat adatbázisokat a készlethez](elastic-pool-overview.md), az alkalmazást egy maroknyi adatbázisból több ezerre, az Ön által felügyelt Költségvetésen belül. A készlet adatbázisai számára elérhető erőforrások minimális és maximális mennyiségét is szabályozhatja, így biztosíthatja, hogy a készlet egyetlen adatbázisa se használja fel a készlet összes erőforrását, és hogy minden készletezett adatbázisnak garantált minimális erőforrása legyen. A rugalmas készleteket használó szoftveres (SaaS-) alkalmazások tervezési mintáinak megismeréséhez lásd: [tervezési minták több-bérlős SaaS-alkalmazásokhoz SQL Database](saas-tenancy-app-design-patterns.md)használatával.

A szkriptek segítik a rugalmas készletek megfigyelését és méretezését. Példa: a [PowerShell használata egy rugalmas készlet figyelésére és méretezésére Azure SQL Databaseban](scripts/monitor-and-scale-pool-powershell.md).


### <a name="blend-single-databases-with-pooled-databases"></a>Önálló adatbázisok beolvasztása a rugalmas készletbe helyezett adatbázisokba

Az önálló adatbázisokat rugalmas készletekkel vegyítheti, és módosíthatja az önálló adatbázisok és a rugalmas készletek szolgáltatási rétegeit, hogy azok alkalmazkodni tudjanak a helyzethez. A SQL Database használatával más Azure-szolgáltatásokat is összekeverheti, és megtekintheti az alkalmazás egyedi kialakítási igényeit, a meghajtó költségeit és erőforrás-hatékonyságát, és új üzleti lehetőségeket nyithat meg.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Széles körű figyelési és riasztási funkciók

A Azure SQL Database fejlett monitorozási és hibaelhárítási funkciókat biztosít, amelyekkel mélyebb elemzéseket kaphat a munkaterhelés jellemzőiről. Ezek a funkciók és eszközök a következők:
 - A SQL Server adatbázismotor legújabb verziójában elérhető beépített figyelési képességek. Lehetővé teszik a valós idejű teljesítménnyel kapcsolatos megállapításokat. 
 - Az Azure által biztosított, a nagy számú adatbázis-példány figyelésére és hibakeresésére képes,

A [query Store](/sql/relational-databases/performance/best-practice-with-the-query-store)egy beépített SQL Server figyelési funkciója, amely valós időben rögzíti a lekérdezések teljesítményét, és lehetővé teszi a lehetséges teljesítménnyel kapcsolatos problémák és a legfontosabb erőforrás-felhasználók azonosítását. Az automatikus hangolás és a javaslatok a romlott teljesítményével és hiányzó vagy duplikált indexekkel kapcsolatos tanácsokat nyújtanak. A SQL Database automatikus hangolásával manuálisan alkalmazhatja a hibákat kijavító szkripteket, vagy engedélyezheti SQL Database a javítás alkalmazását. A SQL Database tesztelheti és ellenőrizheti, hogy a javítás bizonyos előnyöket biztosít-e, és az eredménytől függően megőrzi vagy visszaállíthatja a változást. A lekérdezési tároló és az Automatikus hangolási funkciók mellett a standard [DMV és a XEvent](monitoring-with-dmvs.md) is használható a munkaterhelés teljesítményének figyelésére.

Az Azure [beépített teljesítmény-figyelési](performance-guidance.md) és [riasztási](alerts-insights-configure-portal.md) eszközöket kínál a teljesítmény-minősítéssel együtt, amely lehetővé teszi több ezer adatbázis állapotának figyelését. Ezeknek az eszközöknek a használatával gyorsan elemezheti a fel-vagy leskálázás hatásait az aktuális vagy tervezett teljesítménybeli igények alapján. Emellett a SQL Database a könnyebb monitorozás érdekében [mérőszámokat és erőforrás-naplókat is képes kibocsátani](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) . Az SQL Database beállítható az erőforrás-használatra, feldolgozókra és munkamenetekre, valamint kapcsolatokra vonatkozó adatok tárolására a következő Azure-erőforrások valamelyikén:

- **Azure Storage**: nagy mennyiségű telemetria alacsony áron való archiválásához.
- **Azure Event Hubs**: SQL Database telemetria integrálása az egyéni figyelési megoldással vagy a gyors folyamatokkal.
- **Azure monitor naplók**: beépített figyelési megoldás jelentéskészítési, riasztási és kockázatcsökkentő képességekkel.

![Az Azure monitoring architektúra ábrája](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Rendelkezésre állás

Azure SQL Database lehetővé teszi a vállalat számára, hogy a megszakítások során továbbra is működőképes maradjon. Hagyományos SQL Server-környezetben általában legalább két géppel kell helyileg beállítani. Ezek a gépek pontosan, szinkronban karbantartva, az adataik másolatával védik az egyes gépek vagy összetevők meghibásodását. Ez a környezet magas rendelkezésre állást biztosít, de nem nyújt védelmet az adatközpontot megsemmisítő természeti katasztrófák ellen.

A vész-helyreállítás azt feltételezi, hogy egy katasztrofális esemény földrajzilag honosítva van ahhoz, hogy egy másik géppel vagy géppel rendelkezzen az adatai egy példányával. A SQL Server az aszinkron módban futó always on rendelkezésre állási csoportokat használhatja a funkció beszerzéséhez. A felhasználók gyakran nem szeretnének várni, amíg a replikáció el nem kerül a tranzakció véglegesítése előtt, így lehetséges, hogy adatvesztés történik, ha nem tervezett feladatátvételt végez.

A prémium szintű és a üzletileg kritikus szolgáltatási szinten lévő adatbázisok már a rendelkezésre állási csoport szinkronizálásához [hasonlóak](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) . Az alacsonyabb szolgáltatási szinten lévő adatbázisok egy [másik, de egyenértékű mechanizmus](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability)használatával biztosítják a redundanciát a tárterületen. A beépített logika egyetlen gép meghibásodása elleni védelmet nyújt. Az aktív geo-replikációs szolgáltatás lehetővé teszi, hogy védelmet nyújtson a katasztrófák ellen, amikor az egész régió megsemmisül.

A Azure Availability Zones egy adott régión belül egy adatközpont-kialakítás KIMARADÁSÁVAL szembeni védelmet próbál meg védeni. Segít megvédeni az energia vagy a hálózat elvesztését egy épületben. SQL Database a különböző replikákat a különböző rendelkezésre állási zónákba helyezi (a különböző épületek és a hatékony).

Valójában a Microsoft által felügyelt adatközpontok globális hálózata által működtetett Azure szolgáltatói szerződése [(SLA)](https://azure.microsoft.com/support/legal/sla/) segít megőrizni az alkalmazást a 24/7-es verzióban. Az Azure platform teljes mértékben felügyeli az összes adatbázist, és nem garantálja az adatvesztést és az adatmennyiség magas százalékos arányát. Az Azure automatikusan kezeli a javításokat, a biztonsági mentéseket, a replikálást, a hibák észlelését, az alapul szolgáló hardver-, szoftver-és hálózati hibákat, hibajavítások, feladatátvételek, adatbázis-frissítések és egyéb karbantartási feladatok telepítését. A standard szintű rendelkezésre állás a számítási és tárolási rétegek elkülönítését foglalja magában. A prémium szintű rendelkezésre állást úgy érheti el, hogy a számítási és tárolási kapacitást egyetlen csomóponton integrálja a teljesítményre, majd az Always On rendelkezésre állási csoportokhoz hasonló technológiákat implementál. A Azure SQL Database magas rendelkezésre állási képességeinek teljes körű megvitatását lásd: [SQL Database rendelkezésre állás](high-availability-sla.md). 

Emellett a SQL Database beépített [üzletmenet-folytonossági és globális skálázhatósági](business-continuity-high-availability-disaster-recover-hadr-overview.md) funkciókat biztosít. Ezek a következők:

- [Automatikus biztonsági mentések](automated-backups-overview.md):

  SQL Database automatikusan elvégzi az adatbázisok teljes, különbözeti és tranzakciós naplójának biztonsági másolatát, így bármikor visszaállíthatja azokat. Az önálló adatbázisok és a készletezett adatbázisok esetében beállíthatja, hogy SQL Database az adatbázis teljes biztonsági mentését az Azure Storage-ba tárolja a hosszú távú biztonsági mentés megőrzése érdekében. Felügyelt példányok esetén a biztonsági másolatok hosszú távú megőrzéséhez is elvégezheti a csak másolással készített biztonsági mentést.

- [Időponthoz tartozó visszaállítások](recovery-using-backups.md):

  Az összes SQL Database üzembe helyezési lehetőség az összes adatbázisra vonatkozó automatikus biztonsági mentési megőrzési időszakon belül bármely időpontra támogatja a helyreállítást.
- [Aktív földrajzi replikálás](active-geo-replication-overview.md):

  Az önálló adatbázis és a készletezett adatbázisok beállításai lehetővé teszik legfeljebb négy olvasható másodlagos adatbázis konfigurálását akár azonos, akár globálisan elosztott Azure-adatközpontokban. Ha például egy SaaS-alkalmazás olyan katalógus-adatbázissal rendelkezik, amely nagy mennyiségű egyidejű írásvédett tranzakcióval rendelkezik, akkor az aktív földrajzi replikálással engedélyezheti a globális olvasási léptéket. Ezzel eltávolítja az elsődlegesen az olvasási munkaterhelések miatti szűk keresztmetszeteket. Felügyelt példányok esetén használja az automatikus feladatátvételi csoportokat.
- [Automatikus feladatátvételi csoportok](auto-failover-group-overview.md):

  Az összes SQL Database üzembe helyezési lehetőség lehetővé teszi a feladatátvételi csoportok használatát a magas rendelkezésre állás és a terheléselosztás globális méretekben történő engedélyezéséhez. Ez magába foglalja a nagy méretű adatbázisok, rugalmas készletek és felügyelt példányok transzparens földrajzi replikálását és feladatátvételét. A feladatátvételi csoportok lehetővé teszik a globálisan elosztott SaaS-alkalmazások létrehozását, minimális adminisztrációs terheléssel. Ezzel a művelettel az összes összetett figyelési, útválasztási és feladatátvételi folyamat SQL Database.
- [Zóna – redundáns adatbázisok](high-availability-sla.md):

  SQL Database lehetővé teszi a prémium vagy üzletileg kritikus adatbázisok vagy rugalmas készletek kiépítését több rendelkezésre állási zónában. Mivel ezek az adatbázisok és rugalmas készletek több redundáns replikával rendelkeznek a magas rendelkezésre állás érdekében, a replikák több rendelkezésre állási zónába való elhelyezése nagyobb rugalmasságot biztosít. Ez magában foglalja az adatközpont skálázási hibáiból az adatvesztés nélkül történő automatikus helyreállítás lehetőségét.

## <a name="built-in-intelligence"></a>Beépített intelligencia

A SQL Database használatával olyan beépített intelligenciát érhet el, amely jelentősen csökkentheti az adatbázisok futtatásának és kezelésének költségeit, és maximalizálja az alkalmazás teljesítményét és biztonságát. Akár több millió ügyfél-számítási feladatot is futtat az óra körül, SQL Database nagy mennyiségű telemetria-adatot gyűjt és dolgoz fel, ugyanakkor teljes mértékben tiszteletben tartja az ügyfelek adatvédelmét. A különböző algoritmusok folyamatosan értékelik a telemetria adatait, így a szolgáltatás megtanulhatja és módosíthatja az alkalmazását.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatikus teljesítményfigyelés és finomhangolás

Az SQL Database részletes betekintést nyújt a figyelést kívánó lekérdezésekbe. SQL Database megismerheti az adatbázis-mintákat, és lehetővé teszi az adatbázis-séma a számítási feladatokhoz való hozzáigazítását. Az SQL Database [javaslatokat tesz a teljesítmény finomhangolására](database-advisor-implement-performance-recommendations.md), és Ön eldöntheti, hogy alkalmazza-e a javasolt finomhangolási műveleteket.

Az adatbázisok folyamatos figyelése azonban nehéz és unalmas feladat, különösen akkor, ha sok adatbázist használ. [Intelligent Insights](intelligent-insights-overview.md) ezt a feladatot a SQL Database teljesítményének automatikus figyelésével végzi el. Tájékoztatja a teljesítmény romlásával kapcsolatos problémákról, azonosítja az egyes problémák kiváltó okát, és a teljesítmény javítására vonatkozó ajánlásokat biztosít, amikor lehetséges.

Előfordulhat, hogy a nagy számú adatbázis kezelése nem lehetséges hatékonyan, még a SQL Database és az Azure által biztosított összes elérhető eszközzel és jelentéssel. Az adatbázis manuális figyelése és finomhangolása helyett érdemes lehet a figyelési és hangolási műveletek némelyikének delegálását SQL Database az [automatikus hangolás](automatic-tuning-overview.md)használatával. A SQL Database automatikusan alkalmazza a javaslatokat, a teszteket és ellenőrzi az egyes hangolási műveleteket, így biztosítva a teljesítmény javítását. Így SQL Database automatikusan alkalmazkodik a számítási feladatokhoz egy ellenőrzött és biztonságos módon. Az automatikus Finomhangolás azt jelenti, hogy az adatbázis teljesítményét minden hangolási művelet előtt és után alaposan figyelni és összehasonlítani kell. Ha a teljesítmény nem javul, a hangolási művelet visszaállt.

Számos partnerünk, akik [SaaS több-bérlős alkalmazásokat](saas-tenancy-app-design-patterns.md) futtatnak SQL Databaseon, az automatikus teljesítmény-finomhangolásra támaszkodva gondoskodnak arról, hogy alkalmazásaik mindig stabil és kiszámítható teljesítményt nyújtsanak. Ez a funkció nagy mértékben csökkenti számukra egy éjszaka közepén fellépő teljesítmény-visszaesés kockázatát. Továbbá, mivel az Ügyfélkörük egy része a SQL Server is használja, a SQL Database által biztosított indexelési ajánlásokat is használják a SQL Server ügyfeleinek segítésére.

SQL Database két automatikus finomhangolási szempont [érhető el](automatic-tuning-overview.md):

- **Automatikus indexkezelés**: Azonosítja az adatbázishoz hozzáadandó és az abból eltávolítandó indexeket.
- **Automatikus terv javítása**: azonosítja a problémás terveket, és javítja az SQL-terv teljesítményével kapcsolatos problémákat.

### <a name="adaptive-query-processing"></a>Adaptív lekérdezés-feldolgozás

Használhatja az [adaptív lekérdezések feldolgozását](/sql/relational-databases/performance/intelligent-query-processing), többek között a többutasításos táblázat értékű függvények többértékű futtatását, a kötegelt üzemmód memóriájának visszajelzését és a kötegelt módú adaptív illesztéseket. Ezen adaptív lekérdezés-feldolgozási funkciók mindegyike hasonló "Learn and alkalmazkodás" technikákat alkalmaz, így segítve a korábban bevonható lekérdezés-optimalizálási problémákkal kapcsolatos teljesítménnyel kapcsolatos problémák megoldását.

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség

Az SQl Database számos [beépített biztonsági és megfelelőségi szolgáltatást](../../active-directory/identity-protection/concept-identity-protection-security-overview.md) kínálva járul hozzá, hogy az Ön alkalmazása eleget tegyen a különféle biztonsági és megfelelőségi elvárásoknak.

> [!IMPORTANT]
> A Microsoft minősített Azure SQL Database (az összes üzembe helyezési lehetőség) számos megfelelőségi szabvány alapján. További információkért tekintse meg a [Microsoft Azure adatvédelmi központot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), ahol megtalálhatja a SQL Database megfelelőségi minősítések legfrissebb listáját.

### <a name="advance-threat-protection"></a>Veszélyforrások elleni védelem

Az SQL-hez készült Azure Defender egységes csomag a fejlett SQL-biztonsági funkciókhoz. Az adatbázis biztonsági rései kezelésére szolgáló funkciókat tartalmaz, valamint észleli a rendellenes tevékenységeket, amelyek fenyegetést jelenthetnek az adatbázisra. Egyetlen helyet biztosít a képességek engedélyezéséhez és kezeléséhez.

- [Sebezhetőségi felmérés](sql-vulnerability-assessment.md):

  A szolgáltatás felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- [Veszélyforrások észlelése](threat-detection-configure.md):

  Ez a funkció olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázis eléréséhez vagy kiaknázásához. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A veszélyforrás-észlelési riasztások tartalmazzák a gyanús tevékenység részleteit, és intézkedéseket javasolnak a fenyegetés kivizsgálására és mérséklésére.

### <a name="auditing-for-compliance-and-security"></a>Naplózás a megfelelőség és biztonság szolgálatában

A [naplózás](../../azure-sql/database/auditing-overview.md) nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókban lévő naplóba írja azokat. A naplózás elősegíti a jogszabályi megfelelőség fenntartását és az adatbázison végzett tevékenység megértését, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenesség feltárását.

### <a name="data-encryption"></a>Adattitkosítás

Az SQL Database titkosítás biztosításával segíti az adatok védelmét. A mozgásban lévő adatforgalom a [Transport Layer Security](https://support.microsoft.com/kb/3135244)szolgáltatást használja. A nyugalmi állapotban lévő adatok esetében [transzparens adattitkosítást](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)használ. A használatban lévő adatkezeléshez [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)használ.

### <a name="data-discovery-and-classification"></a>Adatfelderítés és besorolás

Az [adatok észlelése és besorolása](data-discovery-and-classification-overview.md) Azure SQL Database beépített képességeket biztosít az adatbázisaiban található bizalmas adatok felderítéséhez, besorolásához, címkézéséhez és védelméhez. Betekintést nyújt az adatbázis-besorolási állapotba, és nyomon követi a bizalmas adatokhoz való hozzáférést az adatbázison belül és a határain kívül is.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés

Az SQL Database az [Azure Active Directory-integráció](authentication-aad-overview.md) által lehetővé teszi adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését. Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. A Azure Active Directory támogatja a [többtényezős hitelesítést](authentication-mfa-ssms-overview.md) az adatkezelés és az alkalmazások biztonságának növeléséhez, miközben támogatja az egyszeri bejelentkezést.

## <a name="easy-to-use-tools"></a>Egyszerűen használható eszközök

Az SQL Database egyszerűbbé és hatékonyabbá teszi az alkalmazások létrehozását és karbantartását. Az SQL Database lehetővé teszi, hogy arra koncentrálhasson, amit legjobban tud: kiváló alkalmazások készítésére. SQL Database a már meglévő eszközök és szaktudás használatával kezelheti és fejlesztheti.

|Eszköz|Leírás|
|:---|:---|
|[Az Azure Portal](https://portal.azure.com/)|Webalapú alkalmazás az összes Azure-szolgáltatás kezeléséhez.|
|[Azure Data Studio](/sql/azure-data-studio/)|Platformfüggetlen adatbázis-eszköz, amely Windows, macOS és Linux rendszeren fut.|
|[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)|Ingyenes, letölthető ügyfélalkalmazás bármely SQL-infrastruktúra kezelésére, SQL Serverról SQL Databasere.|
|[SQL Server Data Tools a Visual Studióban](/sql/ssdt/download-sql-server-data-tools-ssdt)|Ingyenes, letölthető ügyfélalkalmazás SQL Server a kapcsolódó adatbázisok, adatbázisok Azure SQL Database, integrációs szolgáltatások csomagjai, Analysis Services adatmodellek és Reporting Services-jelentések fejlesztéséhez.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Ingyenes, letölthető, nyílt forráskódú Kódszerkesztő Windows, macOS és Linux rendszerekhez. Támogatja a bővítményeket, beleértve az [MSSQL bővítményt](https://aka.ms/mssql-marketplace) Microsoft SQL Server, Azure SQL Database és az Azure Azure szinapszis Analytics lekérdezéséhez.|

A SQL Database támogatja a Python, a Java, a Node.js, a PHP, a Ruby és a .NET alkalmazások létrehozását macOS, Linux és Windows rendszeren. Az SQL Database az SQL Serverrel azonos [adatkapcsolattárakat](connect-query-content-reference-guide.md#libraries) támogat.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database gyakori kérdések

### <a name="can-i-control-when-patching-downtime-occurs"></a>Szabályozható a javítási állásidő?

Nem. A javítások hatása általában nem észlelhető, ha az alkalmazásban [újrapróbálkozási logikát alkalmaz](develop-overview.md#resiliency) . További információ: az [Azure karbantartási eseményeinek tervezése Azure SQL Databaseban](planned-maintenance.md).



## <a name="engage-with-the-sql-server-engineering-team"></a>Kapcsolatfelvétel az SQL Server műszaki csoportjával

- [DBA stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): kérdezze meg az adatbázis adminisztrációs kérdéseit.
- [Stack overflow](https://stackoverflow.com/questions/tagged/sql-server): forduljon a fejlesztési kérdésekhez.
- [A Microsoft Q&egy kérdést tartalmazó oldalt](/answers/topics/azure-synapse-analytics.html): technikai kérdéseket tehet fel.
- [Visszajelzés](https://aka.ms/sqlfeedback): hibák jelentése és funkciók kérése.
- [Reddit](https://www.reddit.com/r/SQLServer/): beszéljen SQL Server.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/sql-database/) , amely az önálló adatbázisokra és a rugalmas készletekre vonatkozó költség-összehasonlításokat és számológépeket tartalmazza.
- Az első lépésekhez tekintse meg ezeket a rövid útmutatókat:

  - [Adatbázis létrehozása az Azure Portalon](single-database-create-quickstart.md)  
  - [Adatbázis létrehozása az Azure CLI-vel](az-cli-script-samples-content-guide.md)
  - [Adatbázis létrehozása a PowerShell használatával](powershell-script-content-guide.md)

- Több Azure CLI és PowerShell-mintát talál itt:
  - [Azure CLI-minták az SQL Database-hez](az-cli-script-samples-content-guide.md)
  - [Azure PowerShell-minták az SQL Database-hez](powershell-script-content-guide.md)

- További információ a bejelentett új képességekről: [SQL Database Azure-útiterve](https://azure.microsoft.com/roadmap/?category=databases).
- Tekintse meg a [Azure SQL Database blogot](https://azure.microsoft.com/blog/topics/database), ahol SQL Server termék csapat tagjainak blogja a SQL Database híreket és szolgáltatásokat tartalmazza.