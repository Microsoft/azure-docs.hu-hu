---
title: Az Azure SQL Data Sync |} A Microsoft Docs
description: Ez az áttekintés bemutatja az Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 08/09/2018
ms.openlocfilehash: 2afdd3f78a99d9aae5e84bc2fdf1b21cbdc150d2
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306386"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Az SQL Data Sync szolgáltatással több felhőalapú és helyszíni adatbázis közötti adatszinkronizálás

Az SQL Data Sync egy szolgáltatás, amely az Azure SQL Database, amely lehetővé teszi az adatokat több SQL-adatbázisok és az SQL Server-példányok kiválasztása kétirányúan szinkronizálja.

> [!IMPORTANT]
> Az Azure SQL Data Sync does **nem** támogatja az Azure SQL Database felügyelt példánya jelenleg.

## <a name="when-to-use-data-sync"></a>Mikor érdemes használni az adatok szinkronizálása

Adatszinkronizálás esetekben hasznos, ahol adatokat kell több Azure SQL Database-adatbázisok vagy SQL Server-adatbázisok között naprakészen kell tartani. Az alábbiakban a fő használati esetek Data Sync számára:

-   **Hibrid adatok szinkronizálása:** Az adatszinkronizálás beállíthatja, hogy a helyszíni adatbázisok és a hibrid alkalmazások az Azure SQL-adatbázisok között szinkronizált adatok. Ez a képesség is konfigurálja, hogy a ügyfelek, akik használatát fontolgatja a felhőre, és szeretné helyezni néhány alkalmazását az Azure-ban.

-   **Az elosztott alkalmazások:** Sok esetben akkor előnyös, ha különböző számítási feladatok egymástól a különböző adatbázisok között. Például ha olyan nagy méretű éles adatbázist, de szükség erre az egy jelentésben vagy elemzési számítási feladatok futtatásához, hasznos ahhoz, hogy a további számítási második adatbázis. Ez a megközelítés minimálisra csökkenti a éles számítási feladatokra gyakorolt hatást. Használhatja a Data Syncet, hogy a két adatbázis szinkronizálva.

-   **Globálisan elosztott alkalmazások:** Számos vállalat span számos régióban, és még több országban. Hálózati késés minimalizálása érdekében a legjobb, ha az adatok egy régióban Önhöz. Az adatszinkronizálás könnyedén tarthatja adatbázisok szinkronizálása a világ különböző pontjain található régiókban.

Adatok szinkronizálása nem az előnyben részesített megoldás a következő forgatókönyvekhez:

| Forgatókönyv | Néhány ajánlott megoldásokat |
|----------|----------------------------|
| Vészhelyreállítás | [Az Azure georedundáns biztonsági mentés](sql-database-automated-backups.md) |
| Olvassa el a méretezési csoport | [Csak olvasható replikákat használ a betöltése terheléselosztása csak olvasható lekérdezési számítási feladatok (előzetes verzió)](sql-database-read-scale-out.md) |
| ETL (OLTP, OLAP) | [Az Azure Data Factory](https://azure.microsoft.com/services/data-factory/) vagy [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services?view=sql-server-2017) |
| Migrálás a helyszíni SQL Serverről az Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Az SQL Data Sync áttekintése

Data Sync szinkronizálási csoport fogalma köré alapul. Szinkronizálási csoport, a szinkronizálni kívánt adatbázisok egy csoportját.

Adatok szinkronizálása egy küllős topológia használatával adatok szinkronizálása. Megadhat egy adatbázis a szinkronizálási csoport, a központi adatbázis. Az adatbázisok a többi tag adatbázisok. Szinkronizálás csak a Hub és az egyes tagok között történik.
-   A **központi adatbázis** kell lennie az Azure SQL Database.
-   A **tag adatbázisok** lehet SQL-adatbázisok, a helyszíni SQL Server-adatbázisok vagy SQL Server-példányok Azure-beli virtuális gépeken.
-   A **Sync-adatbázis** Data Sync a metaadatokat és a napló tartalmazza. A Sync-adatbázis nem lehet egy Azure SQL Database és a központi adatbázis ugyanabban a régióban található. A Sync-adatbázis létrehozása ügyfél és a vásárlói kezelésű.

> [!NOTE]
> Ha a-tag adatbázis-on helyi adatbázist használ, hogy [telepítése és konfigurálása egy helyi szinkronizálási ügynök](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Adatok szinkronizálása adatbázisok között](media/sql-database-sync-data/sync-data-overview.png)

Szinkronizálási csoport a következő tulajdonságokkal rendelkezik:

-   A **a szinkronizálási séma** ismerteti, hogy mely adatok szinkronizálása.

-   A **szinkronizálási irány** kétirányú is lehetnek, vagy csak egy irányban áramolhasson. A szinkronizálási irány lehet, hogy *tag Hub*, vagy *Hub tag*, vagy mindkettőt.

-   A **szinkronizálási időköz** azt ismerteti, hogy milyen gyakran a szinkronizálás történik.

-   A **ütközésfeloldási házirend** -szintű szabályzat, amely lehet van *Hub wins* vagy *tag wins*.

## <a name="how-does-data-sync-work"></a>Hogyan működik az adatok szinkronizálása? 

-   **Követési adatok:** Adatszinkronizálás insert, update és delete eseményindítók módosításokat követi nyomon. A módosítások a felhasználói adatbázisban egy oldali táblában vannak rögzítve. Vegye figyelembe, hogy a TÖMEGES Beszúrás nem triggereket alapértelmezés. Ha FIRE_TRIGGERS nincs megadva, nincs insert eseményindítók hajtható végre. Adja hozzá a FIRE_TRIGGERS lehetőség, hogy a Data Sync ezeket beillesztések nyomon követheti. 

-   **Adatok szinkronizálása:** Adatszinkronizálás célja egy küllős modellben. A Hub külön-külön szinkronizál minden tagja. A központi menü módosításokat a rendszer letölti a tag, és majd a tag program feltölti a hubhoz.

-   **Ütközések feloldása:** Adatszinkronizálás két lehetőséget biztosít az ütközések feloldása, *Hub wins* vagy *tag wins*.
    -   Ha *Hub wins*, a módosításokat az agyban mindig felülírja a tag változásait.
    -   Ha *tag wins*, a felülírás módosításához az agyban változásait. Ha egynél több tagja van, a végső értéke attól függ, melyik tag először szinkronizálja.

## <a name="compare-data-sync-with-transactional-replication"></a>Hasonlítsa össze a tranzakciós replikáció adatszinkronizálás

| | Adatszinkronizálás | Tranzakciós replikáció |
|---|---|---|
| Előnyök | – Aktív-aktív támogatása<br/>Kétirányú a helyszíni és az Azure SQL Database között | -Kisebb késés<br/>-Tranzakció-konzisztencia<br/>-Felhasználhatja a meglévő topológiát migrálás után |
| Hátrányai | -5 perc vagy több késés<br/>– Nincs tranzakció-konzisztencia<br/>-Nagyobb teljesítményre gyakorolt hatás | – Nem lehet közzétenni az Azure SQL Database önálló adatbázisok<br/>-Nagy karbantartás költsége |
| | | |

## <a name="get-started-with-sql-data-sync"></a>Ismerkedés az SQL Data Sync szolgáltatással

### <a name="set-up-data-sync-in-the-azure-portal"></a>Az Azure Portal Data Sync beállítása

-   [Az Azure SQL Data Sync beállítása](sql-database-get-started-sql-data-sync.md)
-   Adatok szinkronizálása az ügynök - [adatok szinkronizálása az Azure SQL Data Sync ügynök](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>A PowerShell használatával a Data Sync beállítása

-   [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)

-   [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Az adatok szinkronizálása az ajánlott eljárások áttekintése

-   [Ajánlott eljárások az Azure SQL Data Synchez](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Valami tűntek helytelen?

-   [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konzisztencia és a teljesítmény

#### <a name="eventual-consistency"></a>Végleges konzisztencia
Mivel a Data Syncet eseményindító-alapú, tranzakció-konzisztencia nem garantált. A Microsoft garantálja, hogy végül végrehajtott módosítások az összes, és nem Data Sync a adatvesztéshez vezethet.

#### <a name="performance-impact"></a>A teljesítményt
Adatok szinkronizálása által beszúrása, frissítése és törlése eseményindítók változásainak követése. Ügyféloldali táblák létrehoz a felhasználói adatbázisban a change Tracking szolgáltatáshoz. E módosítás követési tevékenységek hatással vannak az adatbázisban munkaterhelés. A szolgáltatási szintben felmérése, és szükség esetén frissítse.

Kiépítés és megszüntetés során a szinkronizálási csoport létrehozása, frissítése és törlése is befolyásolhatja az adatbázis teljesítményét. 

## <a name="sync-req-lim"></a> Követelmények és korlátozások

### <a name="general-requirements"></a>Általános követelmények

-   Minden táblának elsődleges kulccsal kell rendelkeznie. Az összes sort az elsődleges kulcs értékét ne módosítsa. Ha módosítania kell egy elsődleges kulcs értékét, törölni a sort, és hozza létre újból az új elsődleges kulcs értéke. 

-   Engedélyezni kell a pillanatkép-elkülönítést. További információ: [pillanatkép-elkülönítést az SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Általános korlátozások

-   Egy táblához nem tartozhat azonosító oszlopot, amely nem az elsődleges kulcsot.

-   Elsődleges kulcs nem rendelkezik a következő adattípusokat: sql_variant, binary, varbinary, kép, xml. 

-   Legyen óvatos elsődleges kulcsaként, az alábbi adattípusok használatakor a támogatott pontosság csak a második, mert: idő, dátum és idő, datetime2, datetimeoffset.

-   Objektumok (adatbázisok, táblákat és oszlopokat) nevét nem tartalmazza a nyomtatható karakterek pont (.), bal oldali szögletes zárójel ([), és jobb szögletes zárójel (]).

-   Az Azure Active Directory-hitelesítés nem támogatott.

-   Azonos nevű, de ettől eltérő sémájú (például dbo.customers és sales.customers) rendelkező táblák nem támogatottak.

#### <a name="unsupported-data-types"></a>Nem támogatott adattípusok

-   FileStream

-   AZ SQL ÉS CLR-BELI UDT

-   XMLSchemaCollection (XML támogatott)

-   A kurzor RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Nem támogatott oszloptípus

Adatok szinkronizálása nem tudják szinkronizálni a csak olvasható vagy rendszer által létrehozott oszlopok. Példa:

-   Számított oszlopok.

-   A rendszer által létrehozott oszlopok időbeli verziózású táblák esetében.

#### <a name="limitations-on-service-and-database-dimensions"></a>A szolgáltatás és az adatbázis-dimenziókra korlátozások

| **Méretek**                                                      | **Korlát**              | **Megkerülő megoldás**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Szinkronizálási csoportok maximális száma bármilyen adatbázis is tartozhat.       | 5                      |                             |
| Egyetlen szinkronizálási csoport végpontok maximális száma              | 30                     |                             |
| A helyszíni végpontokig egy szinkronizálási csoportban maximális számát. | 5                      | Több szinkronizálási csoport létrehozása |
| Adatbázis-, tábla-, séma, és oszlop                       | név szerint 50 karakter hosszú lehet |                             |
| A szinkronizálási csoport táblák                                          | 500                    | Több szinkronizálási csoport létrehozása |
| Szinkronizálási csoport egyik táblájában az oszlopok                              | 1000                   |                             |
| Egy tábla adatok sor mérete                                        | 24 Mb                  |                             |
| Minimális szinkronizálási időköz                                           | 5 perc              |                             |
|||
> [!NOTE]
> Lehet legfeljebb 30 végpontok egy szinkronizálási csoportban esetén csak egy szinkronizálási csoportot. Ha egynél több szinkronizálási csoport, a végpontok között az összes szinkronizálási csoportok száma nem lehet hosszabb 30. Ha egy adatbázis több szinkronizálási csoport tagja, több végpontot, nem pedig egy számít.

## <a name="faq-about-sql-data-sync"></a>Az SQL Data Sync szolgáltatással kapcsolatos gyakori kérdések

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Az SQL Data Sync szolgáltatás mennyibe?

Nem jár az SQL Data Sync szolgáltatás magát.  Azonban, továbbra is beleszámítanak adatátviteli díjak adatáthelyezéskor kicsinyítheti az SQL Database-példány. További információ: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Mely régiók támogatják a Data Sync?

Az SQL Data Sync minden régióban érhető el.

### <a name="is-a-sql-database-account-required"></a>Az SQL Database-fiók szükséges? 

Igen. Az Eseményközpont-adatbázis üzemeltetésére szolgáló SQL-adatbázis fiókkal kell rendelkeznie.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Adatszinkronizálás segítségével csak az SQL Server helyszíni adatbázis közötti szinkronizáláshoz? 
Közvetlenül nem. Szinkronizálhatja a helyszíni SQL Server-adatbázisok közötti közvetve, azonban a központi adatbázis létrehozásával az Azure-ban, és majd a helyszíni adatbázisok hozzáadása a szinkronizálási csoport.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Adatszinkronizálás segítségével különböző előfizetésekhez tartoznak az SQL Database-adatbázis közötti szinkronizáláshoz?
Igen. Szinkronizálhatja különböző előfizetésekhez tartozó erőforráscsoportok tartozó SQL-adatbázisok között.
-   Ha az előfizetés ugyanazt bérlőhöz tartozik, és Ön jogosult az összes előfizetés, konfigurálhatja a szinkronizálási csoport az Azure Portalon.
-   Ellenkező esetben rendelkezik, amelyek különböző előfizetésekhez tartoznak a szinkronizálási tagok hozzáadása a PowerShell használatával.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>Adatszinkronizálás segítségével különböző felhőkben (például az Azure nyilvános felhő- és Azure China) tartozó SQL-adatbázisok közötti szinkronizálás?
Igen. A különböző előfizetésekhez tartoznak, a szinkronizálási tagok hozzáadása a PowerShell használatával telepítette, szinkronizálhatja különböző felhőkben tartozó SQL-adatbázisok között.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Helyezhetem saját éles adatbázis egy üres adatbázist, a kezdőérték adatok adatszinkronizálás segítségével, és szinkronizálhatja őket?

Igen. A séma manuális létrehozása az új adatbázis az eredeti alkalmazott azt. Miután létrehozta a sémát, a táblák hozzáadása a szinkronizálási csoport másolja az adatokat, és legyen szinkronizálva.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Az SQL Data Sync használjam biztonsági mentése és visszaállítása az adatbázisok?

Nem ajánlott az SQL Data Sync használata az adatok biztonsági másolatának létrehozásához. Biztonsági és visszaállítása adott időpontra, mivel az SQL Data Sync szinkronizálások nem rendszerverzióval ellátott. Az SQL Data Sync továbbá nem készít biztonsági másolatot más SQL-objektumok, például a tárolt eljárások, és nem felel meg a visszaállítási művelet gyorsan.

Az egyik ajánlott biztonsági mentési módszer, lásd: [egy Azure SQL database másolása](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Szinkronizálhatja-e a Data Syncet, titkosított táblákat és oszlopokat?

-   Ha egy adatbázist használ az Always Encrypted, csak a táblákat és oszlopokat, amelyek szinkronizálhatja *nem* titkosított. A titkosított oszlopokban nem lehet szinkronizálni, mert az adatszinkronizálás nem tudja visszafejteni az adatokat.

-   Ha egy oszlop oszlopszintű titkosítást (CLE) használ, szinkronizálhatja az oszlop, mindaddig, amíg a sor mérete kisebb, mint 24 Mb maximális méretét. Adatszinkronizálás kezeli a normál bináris adat titkosítja a kulcsot (CLE) oszlopban. Az egyéb szinkronizálási tagok található adatok visszafejtése, ugyanazt a tanúsítványt kell.

### <a name="is-collation-supported-in-sql-data-sync"></a>Támogatott rendezés az SQL Data Sync?

Igen. Az SQL Data Sync támogatja a rendezést a következő esetekben:

-   Ha a kiválasztott szinkronizálási séma táblák még nem az eseményközpont vagy tag adatbázisokban, majd a szinkronizálási csoport központi telepítése során, a szolgáltatás automatikusan létrehozza a kapcsolódó táblákat és oszlopokat a rendezési beállításaitól függ a üres célhely adatbázisában kiválasztva.

-   Ha a táblák szinkronizálható, már létezik a hub és a tag-adatbázisokban, az SQL Data Sync megköveteli, hogy az elsődleges kulcsoszlopot ugyanazt a rendezést hubot és a tag adatbázis sikeres üzembe helyezéséhez a szinkronizálási csoport között. Nem vonatkoznak rendezési korlátozások oszlopokon kívül az elsődleges kulcsoszlopot.

### <a name="is-federation-supported-in-sql-data-sync"></a>Az SQL Data Sync támogatott összevonási?

Összevonási Gyökéradatbázis az SQL Data Sync szolgáltatás bármilyen korlátozás nélkül használható. Az összevont adatbázisban végpont nem lehet hozzáadni az SQL Data Sync jelenlegi verziójával.

## <a name="next-steps"></a>További lépések

### <a name="update-the-schema-of-a-synced-database"></a>A szinkronizált adatbázis sémájának frissítéséhez

Rendelkezik egy szinkronizálási csoportban egy adatbázis sémájának frissítéséhez? Sémaváltozások automatikusan nem lesznek replikálva. Az egyes megoldások a következő cikkekben talál:

-   [Az Azure SQL Data Sync sémamódosítások-replikáció automatizálása](sql-database-update-sync-schema.md)

-   [A meglévő szinkronizálási csoport szinkronizálási sémájának frissítése a PowerShell használatával](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Figyelés és hibaelhárítás

Elvárt módon működik az SQL Data Sync? Tevékenység figyelése és hibáinak elhárítása: az alábbi cikkeket:

-   [Az Azure SQL Data Sync monitorozása a Log Analytics használatával](sql-database-sync-monitor-oms.md)

-   [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Ismerkedés az Azure SQL Database szolgáltatással

További információ az SQL Database a következő cikkekben talál:

-   [Az SQL Database áttekintése](sql-database-technical-overview.md)

-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
