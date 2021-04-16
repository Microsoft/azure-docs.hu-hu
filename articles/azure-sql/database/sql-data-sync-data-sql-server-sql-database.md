---
title: Mi az SQL-adatszinkronizálás az Azure-hoz?
description: Ez az áttekintés SQL-adatszinkronizálás Azure-hoz, amely lehetővé teszi az adatok szinkronizálását több felhőalapú és helyszíni adatbázis között.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: 695409740348e78ae51b263b44d9ed1cbadc1054
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531926"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Mi az SQL-adatszinkronizálás az Azure-hoz?

SQL-adatszinkronizálás szolgáltatás az Azure SQL Database, amely lehetővé teszi a kiválasztott adatok kétirányú szinkronizálását több adatbázis között a helyszínen és a felhőben is. 

> [!IMPORTANT]
> Azure SQL-adatszinkronizálás jelenleg nem támogatja Azure SQL Managed Instance-t.


## <a name="overview"></a>Áttekintés 

adatszinkronizálás a szinkronizálási csoport fogalmán alapul. A szinkronizálási csoport a szinkronizálni kívánt adatbázisok csoportja.

adatszinkronizálás küllős topológiát használ az adatok szinkronizálásához. A szinkronizálási csoportban az egyik adatbázist kell meghatároznia központi adatbázisként. A többi adatbázis tagadatbázis. A szinkronizálás csak a központ és az egyes tagok között történik.

- A **központi adatbázisnak** egy Azure SQL Database.
- A **tagadatbázisok** adatbázisai a Azure SQL Database vagy példányai SQL Server.
- A **Metaadat-szinkronizálási** adatbázis tartalmazza a metaadatokat és a adatszinkronizálás. A metaadat-szinkronizálási adatbázisnak egy olyan Azure SQL Database kell lennie, amely ugyanabban a régióban található, mint a központi adatbázis. A metaadat-szinkronizálási adatbázis az ügyfél által létrehozott és az ügyfél tulajdonában van. Régiónként és előfizetésenként csak egy szinkronizálási metaadat-adatbázissal rendelkezik. A szinkronizálási metaadatok adatbázisa nem törölhető vagy nevezhető át, amíg szinkronizálási csoportok vagy szinkronizálási ügynökök léteznek. A Microsoft azt javasolja, hogy hozzon létre egy új, üres adatbázist, amely a Metaadatok szinkronizálása adatbázisként használható. adatszinkronizálás létrehoz táblákat ebben az adatbázisban, és futtat egy gyakori számítási feladatot.

> [!NOTE]
> Ha helyszíni adatbázist használ tagadatbázisként, telepítenie és konfigurálnia kell egy helyi [szinkronizálási ügynököt.](sql-data-sync-sql-server-configure.md#add-on-prem)

![Adatok szinkronizálása adatbázisok között](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

A szinkronizálási csoport a következő tulajdonságokkal rendelkezik:

- A **szinkronizálási séma** azt írja le, hogy mely adatok szinkronizálása folyamatban van.
- A **szinkronizálási irány** lehet kétirányú, vagy csak egy irányban haladhat. Ez azt jelenti, hogy a szinkronizálás iránya lehet *Hub to Member*(Központ és tag) vagy Member to Hub (Tag a *központnak)* vagy mindkettő.
- A **szinkronizálási időköz** azt írja le, hogy milyen gyakran történik szinkronizálás.
- Az **ütközésfeloldási szabályzat** egy csoportszintű szabályzat, amely a *Központ* nyer vagy a *Tag nyer.*

## <a name="when-to-use"></a>A következő esetekben használja

adatszinkronizálás olyan esetekben hasznos, amikor az adatokat több adatbázisban kell frissíteni a Azure SQL Database vagy SQL Server. A következő esetekben használhatók a adatszinkronizálás:

- **Hibrid adatszinkronizálás:** A adatszinkronizálás a hibrid alkalmazások engedélyezéséhez szinkronizálhatja az adatokat a SQL Server és Azure SQL Database között. Ez a képesség olyan ügyfelek számára vonzó lehet, akik a felhőbe való áthelyezést fontolgatják, és az Azure-ba szeretnék tenni az alkalmazásuk egy részeüket.
- **Elosztott alkalmazások:** Sok esetben előnyös elkülönítani a különböző számítási feladatokat a különböző adatbázisok között. Ha például egy nagy méretű éles adatbázissal is, de jelentéskészítési vagy elemzési számítási feladatot is futtatnia kell az adatokon, hasznos lehet egy második adatbázist is futtatni ehhez a további számítási feladathoz. Ez a megközelítés minimálisra csökkenti az éles számítási feladatok teljesítményre gyakorolt hatását. A két adatbázis adatszinkronizálás is használhatja.
- **Globálisan elosztott alkalmazások:** Számos vállalkozás több régióra, sőt több országra/régióra is ki van ásva. A hálózati késés minimalizálása érdekében a legjobb, ha az adatokat egy Ön közelében található régióban található. A adatszinkronizálás egyszerűen szinkronizálhatja az adatbázisokat a világ különböző régióiban.

adatszinkronizálás a következő esetekben nem ez az előnyben részesített megoldás:

| Eset | Néhány ajánlott megoldás |
|----------|----------------------------|
| Vészhelyreállítás | [Azure georedundáns biztonsági másolatok](automated-backups-overview.md) |
| Olvasási skálázás | [Csak olvasható replikák használata a csak olvasható lekérdezési számítási feladatok terhelésének elosztásához (előzetes verzió)](read-scale-out.md) |
| ETL (OLTP–OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) vagy [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migrálás SQL Server-Azure SQL Database. A SQL-adatszinkronizálás azonban az áttelepítés befejezése után is használható, hogy a forrás és a cél szinkronban legyen.  | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Működés

- **Adatváltozások nyomon követése:** adatszinkronizálás beszúrási, frissítési és törlési eseményindítók használatával követi nyomon a módosításokat. A módosítások egy oldaltáblában vannak rögzítve a felhasználói adatbázisban. Vegye figyelembe BULK INSERT hogy a rendszer alapértelmezés szerint nem aktiválja az eseményindítókat. Ha FIRE_TRIGGERS nincs megadva, a rendszer nem hajt végre beszúrási eseményindítókat. Adja hozzá FIRE_TRIGGERS lehetőséget, hogy adatszinkronizálás követni tudja ezeket a beszúrásokat. 
- **Adatok szinkronizálása:** adatszinkronizálás küllős modellben van kialakítva. A központ egyenként szinkronizál az egyes tagokkal. A rendszer letölti a hub módosításait a taghoz, majd feltölti a tag módosításait a hubra.
- **Ütközések feloldása:** adatszinkronizálás két lehetőséget biztosít az ütközésfeloldáshoz: a *Hub wins* vagy *a Member wins* lehetőséget.
  - Ha a *Hub wins (Központ nyer)* lehetőséget választja, a hub módosításai mindig felülírják a tag módosításait.
  - Ha a Tag *nyer* lehetőséget választja, a tag módosításai felülírják a központban történt módosításokat. Ha egynél több tag van, a végső érték attól függ, hogy melyik tag szinkronizál először.

## <a name="compare-with-transactional-replication"></a>Összehasonlítás a tranzakciós replikációval

| | Adatszinkronizálás | Tranzakciós replikáció |
|---|---|---|
| **Előnyök** | - Aktív-aktív támogatás<br/>- Kétirányú a helyszíni hálózat és a Azure SQL Database | – Kisebb késés<br/>- Tranzakciós konzisztencia<br/>– Meglévő topológia újbóli újrafelhasználása a migrálás után <br/>-Azure SQL Managed Instance támogatás |
| **Hátrányok** | – Nincs tranzakciós konzisztencia<br/>- Nagyobb teljesítményre gyakorolt hatás | – Nem lehet közzétenni a Azure SQL Database <br/>- Magas karbantartási költségek |

## <a name="private-link-for-data-sync-preview"></a>Privát hivatkozás adatszinkronizálás (előzetes verzió)
Az új privát kapcsolat (előzetes verzió) funkció lehetővé teszi egy szolgáltatás által felügyelt privát végpont választását, hogy biztonságos kapcsolatot létesítsen a szinkronizálási szolgáltatás és a tag-/hub-adatbázisok között az adatszinkronizálási folyamat során. A szolgáltatás által felügyelt privát végpont egy magánhálózati IP-cím egy adott virtuális hálózaton és alhálózaton belül. A adatszinkronizálás szolgáltatás által felügyelt privát végpontot a Microsoft hozta létre, és kizárólag a adatszinkronizálás használja egy adott szinkronizálási művelethez. A privát kapcsolat beállítása előtt olvassa el a [funkció általános](sql-data-sync-data-sql-server-sql-database.md#general-requirements) követelményeit. 

![Privát kapcsolat adatszinkronizálás](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> Manuálisan kell jóváhagynia a szolgáltatás  által felügyelt privát végpontot a szinkronizálási csoport üzembe helyezése Azure Portal Privát végponti kapcsolatok lapján vagy a PowerShell használatával.

## <a name="get-started"></a>Bevezetés 

### <a name="set-up-data-sync-in-the-azure-portal"></a>A adatszinkronizálás beállítása a Azure Portal

- [Az Azure SQL Data Sync beállítása](sql-data-sync-sql-server-configure.md)
- Data Sync Agent – [Data Sync Agent a Azure SQL-adatszinkronizálás](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>A powershell adatszinkronizálás beállítása

- [A PowerShell használata több adatbázis szinkronizálásához a Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [A PowerShell használata egy adatbázis szinkronizálásához a Azure SQL Database-példányban és a SQL Server-példányban](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="set-up-data-sync-with-rest-api"></a>Az adatszinkronizálás beállítása REST API
- [A REST API több adatbázis közötti szinkronizáláshoz használja a Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases-rest-api.md)

### <a name="review-the-best-practices-for-data-sync"></a>Tekintse át az ajánlott adatszinkronizálás

- [Ajánlott eljárások az Azure SQL Data Synchez](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Valami nem sikerült

- [Az Azure SQL Data Synckel hibaelhárítása](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Konzisztencia és teljesítmény

### <a name="eventual-consistency"></a>Végleges konzisztencia

Mivel adatszinkronizálás eseményindító-alapú, a tranzakciós konzisztencia nem garantált. A Microsoft garantálja, hogy minden módosításra sor fog jönni, adatszinkronizálás nem okoz adatvesztést.

### <a name="performance-impact"></a>Teljesítményre gyakorolt hatás

adatszinkronizálás beszúrási, frissítési és törlési eseményindítókat használ a módosítások nyomon követéséhez. Oldaltáblákat hoz létre a felhasználói adatbázisban a változáskövetéshez. Ezek a változáskövetési tevékenységek hatással vannak az adatbázis számítási feladataira. Mérje fel a szolgáltatási szintet, és szükség esetén frissítsen.

A kiépítés és megszüntetés a szinkronizálási csoport létrehozása, frissítése és törlése során szintén hatással lehet az adatbázis teljesítményére.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Követelmények és korlátozások

### <a name="general-requirements"></a>Általános követelmények

- Minden táblának egy elsődleges kulccsal kell lennie. Ne módosítsa az elsődleges kulcs értékét egyetlen sorban sem. Ha módosítania kell egy elsődleges kulcs értékét, törölje a sort, és hozza létre újra az új elsődleges kulcs értékével.

> [!IMPORTANT]
> Egy meglévő elsődleges kulcs értékének módosítása a következő hibás viselkedést eredményezi:
> - A központ és a tag közötti adat akkor is elveszhet, ha a szinkronizálás nem jelent problémát.
> - A szinkronizálás meghiúsulhat, mert a nyomkövetési táblában az elsődleges kulcs módosítása miatt nem létező sor található a forrásból.

- A pillanatkép-elkülönítést a szinkronizálási tagok és a központ számára egyaránt engedélyezni kell. További információ: [Pillanatkép-elkülönítés az SQL Serveren](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- Ahhoz, hogy privát kapcsolatot használ adatszinkronizálás-val, a tag- és a központi adatbázist is az Azure-ban kell üzemeltetni (azonos vagy eltérő régiókban), ugyanabban a felhőtípusban (például nyilvános felhőben vagy mindkét kormányzati felhőben). A privát kapcsolat használatának érdekében a Microsoft.Network erőforrás-szolgáltatóknak regisztrálva kell lennie a központot és tagkiszolgálókat szolgáltató előfizetések esetében. Végül manuálisan jóvá kell hagynia a privát hivatkozást a adatszinkronizálás a szinkronizálási konfiguráció során, a szolgáltatás "Privát végponti kapcsolatai" szakaszában vagy a PowerShellen Azure Portal keresztül. A privát hivatkozás jóváhagyásának további részleteiért lásd: [Beállítás SQL-adatszinkronizálás.](./sql-data-sync-sql-server-configure.md) A szolgáltatás által felügyelt privát végpont jóváhagyása után a szinkronizálási szolgáltatás és a tag-/hub-adatbázisok közötti kommunikáció a privát kapcsolaton keresztül történik. A meglévő szinkronizálási csoportok frissíthetők a funkció engedélyezése érdekében.

### <a name="general-limitations"></a>Általános korlátozások

- Egy tábla nem lehet olyan identitásoszlop, amely nem az elsődleges kulcs.
- Az adatszinkronizáláshoz a táblának fürtözött indexszel kell lennie.
- Az elsődleges kulcs nem lehet a következő adattípusokkal: sql_variant, binary, varbinary, image, xml.
- Legyen óvatos, ha a következő adattípusokat használja elsődleges kulcsként, mert a támogatott pontosság csak a másodikra van va: time, datetime, datetime2, datetimeoffset.
- Az objektumok neve (adatbázisok, táblák és oszlopok) nem tartalmazhatja a nyomtatható karakterek pontját (.), a bal oldali szögletes zárójelet ([) vagy a jobb oldali szögletes zárójelet (]).
- A tábla neve nem tartalmazhat nyomtatható karaktereket: ! " # $ % ' ( ) * + - szóköz
- Azure Active Directory hitelesítés nem támogatott.
- Ha vannak azonos nevű táblák, de eltérő sémával (például dbo.customers és sales.customers), csak az egyik tábla hozzáadható a szinkronizáláshoz.
- Az User-Defined adattípusokat tartalmazó oszlopok nem támogatottak
- A kiszolgálók különböző előfizetések közötti mozgatás nem támogatott. 
- Ha két elsődleges kulcs csak abban az esetben különbözik (például Foo és foo), adatszinkronizálás nem támogatja ezt a forgatókönyvet.

#### <a name="unsupported-data-types"></a>Nem támogatott adattípusok

- FileStream (Fájlfolyam)
- SQL/CLR UDT
- XMLSchemaCollection (XML támogatott)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Nem támogatott oszloptípusok

adatszinkronizálás csak olvasható vagy a rendszer által létrehozott oszlopokat nem lehet szinkronizálni. Például:

- Számított oszlopok.
- Rendszer által létrehozott oszlopok historikus táblákhoz.

#### <a name="limitations-on-service-and-database-dimensions"></a>A szolgáltatás- és adatbázis-dimenziók korlátozásai

| **Méretek**                                                  | **Korlát**              | **Áthidaló megoldás**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Azon szinkronizálási csoportok maximális száma, amelyekhez egy adatbázis tartozhat.       | 5                      |                             |
| Végpontok maximális száma egy szinkronizálási csoportban              | 30                     |                             |
| A helyszíni végpontok maximális száma egy szinkronizálási csoportban. | 5                      | Több szinkronizálási csoport létrehozása |
| Adatbázis-, tábla-, séma- és oszlopnevek                       | Névnként 50 karakter |                             |
| Szinkronizálási csoport táblái                                          | 500                    | Több szinkronizálási csoport létrehozása |
| Egy szinkronizálási csoportban lévő tábla oszlopai                              | 1000                   |                             |
| Adatsor mérete egy táblán                                        | 24 Mb                  |                             |

> [!NOTE]
> Egy szinkronizálási csoportban legfeljebb 30 végpont lehet, ha csak egy szinkronizálási csoport van. Ha egynél több szinkronizálási csoport van, az összes szinkronizálási csoport végpontjainak teljes száma nem haladhatja meg a 30-ast. Ha egy adatbázis több szinkronizálási csoportba is tartozik, akkor több végpontnak számít, nem pedig egynek.

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

> [!NOTE]
> Ha privát kapcsolatot használ, ezek a hálózati követelmények nem érvényesek. 

A szinkronizálási csoport létrehozása után a adatszinkronizálás szolgáltatásnak csatlakoznia kell a központi adatbázishoz. A szinkronizálási csoport létrehozása során a Azure SQL-kiszolgáló beállításaiban a következő konfigurációnak kell `Firewalls and virtual networks` lennie:

 * *A nyilvános hálózati hozzáférés megtagadása* beállításnak ki kell *kapcsolva.*
 * *A kiszolgáló elérésének* engedélyezése az Azure-szolgáltatások és -erőforrások számára beállításnak *Igen,* vagy a szolgáltatás által használt IP-adatszinkronizálás kell [létrehoznia.](network-access-controls-overview.md#data-sync)

A szinkronizálási csoport létrehozása és kiépítése után letilthatja ezeket a beállításokat. A szinkronizálási ügynök közvetlenül csatlakozik a központi adatbázishoz, és a [](private-endpoint-overview.md) kiszolgáló tűzfal [IP-szabályaival](firewall-configure.md) vagy privát végpontjaival engedélyezheti az ügynök számára a központi kiszolgálóhoz való hozzáférést.

> [!NOTE]
> Ha módosítja a szinkronizálási csoport sémabeállítását, engedélyeznie kell, hogy az adatszinkronizálás szolgáltatás ismét hozzáférjen a kiszolgálóhoz, hogy a központi adatbázis újra kiépítődhet.

## <a name="faq-about-sql-data-sync"></a>Gyakori kérdések a SQL-adatszinkronizálás

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Mennyibe kerül a SQL-adatszinkronizálás szolgáltatás költsége?

A szolgáltatásért nem kell SQL-adatszinkronizálás díjat. Azonban továbbra is gyűjti az adatátviteli díjakat az adatpéldányba vagy a SQL Database való áthelyezésért. További információ: díjszabás [SQL Database.](https://azure.microsoft.com/pricing/details/sql-database/)

### <a name="what-regions-support-data-sync"></a>Milyen régiók támogatják a adatszinkronizálás

SQL-adatszinkronizálás minden régióban elérhető.

### <a name="is-a-sql-database-account-required"></a>Szükség van SQL Database fiókra

Igen. A központi adatbázis SQL Database fiókkal kell lennie.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Használhatom a adatszinkronizálás csak a SQL Server közötti szinkronizáláshoz?

Közvetlenül nem. Közvetett módon azonban szinkronizálhat SQL Server adatbázisok között, ha létrehoz egy központi adatbázist az Azure-ban, majd hozzáadja a helyszíni adatbázisokat a szinkronizálási csoporthoz.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Használhatom a adatszinkronizálás a különböző előfizetések SQL Database adatbázisai közötti szinkronizáláshoz?

Igen. Szinkronizálhatja a különböző előfizetések tulajdonában lévő erőforráscsoportokhoz tartozó adatbázisokat.

- Ha az előfizetések ugyanathoz a bérlőhöz tartoznak, és Ön az összes előfizetéshez rendelkezik engedéllyel, konfigurálhatja a szinkronizálási csoportot a Azure Portal.
- Ellenkező esetben a PowerShell használatával kell hozzáadnia a különböző előfizetések szinkronizálási tagjait.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Használhatom a adatszinkronizálás SQL Database különböző felhőkhöz (például az Azure nyilvános felhőhöz és a felhőhöz) tartozó Azure China 21Vianet) közötti szinkronizáláshoz?

Igen. A különböző felhőkhöz tartozó adatbázisok között szinkronizálhat. A különböző előfizetések szinkronizálási tagjainak hozzáadásához a PowerShellt kell használnia.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Használhatom a adatszinkronizálás az éles adatbázisból származó adatok üres adatbázisba való átszinkronizálásához?

Igen. Hozza létre manuálisan a sémát az új adatbázisban az eredeti szkript használatával. A séma létrehozása után adja hozzá a táblákat egy szinkronizálási csoporthoz az adatok másolása és szinkronizálása érdekében.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Érdemes a SQL-adatszinkronizálás az adatbázisok biztonsági mentését és visszaállítását?

Nem ajánlott a SQL-adatszinkronizálás az adatok biztonsági másolatának létrehozásához. Egy adott időpontra nem lehet biztonsági mentést és visszaállítást, mert a SQL-adatszinkronizálás szinkronizálások nem verziószámosak. Emellett a SQL-adatszinkronizálás nem biztonsági mentést ad más SQL-objektumokról, például a tárolt eljárásokról, és nem képes gyorsan a visszaállítási művelet megfelelőjére.

Az egyik ajánlott biztonsági mentési technikát lásd: [Adatbázis másolása a Azure SQL Database.](database-copy.md)

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Képes adatszinkronizálás táblák és oszlopok szinkronizálására

- Ha egy adatbázis Always Encrypted, csak a nem titkosított táblákat és oszlopokat *szinkronizálhatja.* A titkosított oszlopok nem szinkronizálhatóak, mert adatszinkronizálás nem tudja visszafejteni az adatokat.
- Ha egy oszlop Column-Level titkosítást (CLE) használ, szinkronizálhatja az oszlopot, ha a sor mérete kisebb, mint a maximális 24 Mb-os méret. adatszinkronizálás kulcs (CLE) által titkosított oszlopot normál bináris adatokként kezeli. Más szinkronizálási tagok adatainak visszafejtéséhez azonos tanúsítvánnyal kell rendelkezik.

### <a name="is-collation-supported-in-sql-data-sync"></a>A rendezés támogatott a SQL-adatszinkronizálás

Igen. SQL-adatszinkronizálás következő esetekben támogatja a rendezést:

- Ha a kiválasztott szinkronizálási sématáblák még nincsenek a központi vagy tagadatbázisban, akkor a szinkronizálási csoport üzembe helyezésekor a szolgáltatás automatikusan létrehozza a megfelelő táblákat és oszlopokat az üres céladatbázisban kiválasztott rendezésbeállításokkal.
- Ha a szinkronizálni szükséges táblák már léteznek a központi és a tagadatbázisban is, a SQL-adatszinkronizálás a szinkronizálási csoport sikeres üzembe helyezéséhez az elsődleges kulcs oszlopainak azonos rendezést kell tartalmaznak a központi és a tagadatbázisok között. Az elsődleges kulcsoszlopon kívül más oszlopokra nem vonatkoznak rendezéskorlátozások.

### <a name="is-federation-supported-in-sql-data-sync"></a>Az összevonás támogatott a SQL-adatszinkronizálás

Az összevonási gyökéradatbázis korlátozás nélkül használható a SQL-adatszinkronizálás szolgáltatásban. Az összevont adatbázis végpontját nem használhatja az adatbázis aktuális SQL-adatszinkronizálás.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Használhatom a adatszinkronizálás a Dynamics 365-ból exportált adatok saját adatbázis használata (BYOD) funkcióval történő szinkronizálásához?

A Dynamics 365 saját adatbázis használata funkcióval a rendszergazdák adatentitásokat exportálnak az alkalmazásból a saját SQL Microsoft Azure ba. adatszinkronizálás adatok más adatbázisokkal való szinkronizálására is használható, ha az adatok növekményes leküldéses exportálással vannak exportálva **(a** teljes leküldés nem támogatott), és az eseményindítók engedélyezése a céladatbázisban igenre van **állítva.** 

## <a name="next-steps"></a>Következő lépések

### <a name="update-the-schema-of-a-synced-database"></a>Szinkronizált adatbázis sémája frissítése

Frissítenie kell egy szinkronizálási csoportban egy adatbázis sémáját? A sémaváltozások nem replikálódnak automatikusan. Néhány megoldásért tekintse meg a következő cikkeket:

- [Sémaváltozások replikációjának automatizálása az Azure SQL-adatszinkronizálás ban](./sql-data-sync-update-sync-schema.md)
- [PowerShell használata meglévő szinkronizálási csoport szinkronizálási sémájának frissítéséhez](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Figyelés és hibaelhárítás

A SQL-adatszinkronizálás a várt módon működik? A tevékenységek figyelése és a hibák elhárítása érdekében tekintse meg a következő cikkeket:

- [Az SQL-adatszinkronizálás monitor Azure Monitor naplók segítségével](./monitor-tune-overview.md)
- [Az Azure SQL Data Synckel hibaelhárítása](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>További információ a Azure SQL Database

További információt a Azure SQL Database a következő cikkekben talál:

- [Az SQL Database áttekintése](sql-database-paas-overview.md)
- [Az adatbázis életciklusának felügyelete](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
