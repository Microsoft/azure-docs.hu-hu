---
title: Mi az Azure-SQL-adatszinkronizálás?
description: Ez az áttekintés bemutatja SQL-adatszinkronizálás az Azure-hoz, amely lehetővé teszi az adatszinkronizálást több felhőalapú és helyszíni adatbázis között.
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
ms.openlocfilehash: c38e4681c76fb0dd52d77c7dc1438b87a9571a80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562059"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Mi az Azure-SQL-adatszinkronizálás?

A SQL-adatszinkronizálás Azure SQL Databasere épülő szolgáltatás, amely lehetővé teszi, hogy a helyileg és a felhőben egyaránt több adatbázison keresztül szinkronizálja az adatokat. 

> [!IMPORTANT]
> Az Azure SQL-adatszinkronizálás jelenleg nem támogatja az Azure SQL felügyelt példányát.


## <a name="overview"></a>Áttekintés 

Az adatszinkronizálás a szinkronizálási csoport fogalmán alapul. A szinkronizálási csoport a szinkronizálni kívánt adatbázisok egy csoportja.

Az adatszinkronizálás sugarasan és küllős topológiával szinkronizálja az adatokat. A szinkronizálási csoportban lévő adatbázisok egyikét a hub-adatbázisként kell meghatároznia. A többi adatbázis a tagok adatbázisai. A szinkronizálás csak a hub és az egyes tagok között történik.

- A **hub-adatbázisnak** Azure SQL Databasenak kell lennie.
- A **tagsági adatbázisok** lehetnek Azure SQL Database vagy SQL Server példányaiban található adatbázisok.
- A **szinkronizálási metaadatok adatbázisa** tartalmazza a metaadatokat és a naplót az adatok szinkronizálásához. A szinkronizálási metaadat-adatbázisnak egy olyan Azure SQL Database kell lennie, amely ugyanabban a régióban található, mint a hub-adatbázis. A szinkronizálási metaadat-adatbázis az ügyfél által létrehozott és az ügyfél tulajdonában van. Régiónként és előfizetésen belül csak egy szinkronizálási metaadat-adatbázis lehet. A szinkronizálási metaadat-adatbázis nem törölhető vagy nem nevezhető át, amíg a szinkronizálási csoportok vagy a szinkronizálási ügynökök léteznek. A Microsoft azt javasolja, hogy hozzon létre egy új, üres adatbázist a szinkronizálási metaadat-adatbázisként való használatra. Az adatok szinkronizálása táblákat hoz létre ebben az adatbázisban, és gyakori számítási feladatokat futtat.

> [!NOTE]
> Ha helyszíni adatbázist használ tagként adatbázisként, [telepítenie és konfigurálnia kell egy helyi szinkronizáló ügynököt](sql-data-sync-sql-server-configure.md#add-on-prem).

![Adatok szinkronizálása adatbázisok között](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

A szinkronizálási csoport a következő tulajdonságokkal rendelkezik:

- A **szinkronizálási séma** leírja, hogy mely adatokat szinkronizálja a rendszer.
- A **szinkronizálás iránya** lehet kétirányú, vagy csak egy irányba lehet flow. Vagyis a szinkronizálás iránya lehet a *tag*, a tag vagy a két *csomópont* is.
- A szinkronizálási **időköz** leírja, hogy milyen gyakran történjen a szinkronizálás.
- Az **ütközés-feloldási házirend** egy csoport szintű házirend, amely lehet *hub WINS* vagy *tag WINS*.

## <a name="when-to-use"></a>A következő esetekben használja

Az adatszinkronizálás olyan esetekben hasznos, amikor a Azure SQL Database vagy SQL Server több adatbázisában frissíteni kell az információkat. Az adatszinkronizálás fő felhasználási esetei:

- **Hibrid adatszinkronizálás:** Az adatszinkronizálással megtarthatja a szinkronizált adatokat az adatbázisok között SQL Server és Azure SQL Database a hibrid alkalmazások engedélyezéséhez. Ez a képesség olyan ügyfelek számára lehet fellebbezni, akik a felhőbe való áttérést fontolgatják, és az Azure-ban szeretnék üzembe helyezni az alkalmazásaikat.
- **Elosztott alkalmazások:** Sok esetben előnyös a különböző számítási feladatok különböző adatbázisokban való elkülönítése. Ha például nagy üzemi adatbázissal rendelkezik, de az adatokon jelentéskészítési vagy elemzési feladatokat is futtatnia kell, akkor hasznos, ha egy második adatbázissal rendelkezik ehhez a további számítási feladathoz. Ez a megközelítés csökkentheti a teljesítményre gyakorolt hatást az éles munkaterhelésen. Az adatszinkronizálás használatával megtarthatja a két adatbázis szinkronizálását.
- **Globálisan elosztott alkalmazások:** Számos vállalat számos régiót és akár több országot/régiót is kiterjedhet. A hálózati késés csökkentése érdekében a legjobb, ha az adatai egy régióhoz közel vannak. Az adatszinkronizálással könnyedén megtarthatja az adatbázisokat a világ különböző régióiban szinkronizálva.

Az adatszinkronizálás nem az előnyben részesített megoldás a következő esetekben:

| Eset | Néhány ajánlott megoldás |
|----------|----------------------------|
| Vészhelyreállítás | [Azure geo-redundáns biztonsági mentések](automated-backups-overview.md) |
| Olvasási skála | [Csak olvasható replikák használata az írásvédett lekérdezési feladatok terheléselosztásához (előzetes verzió)](read-scale-out.md) |
| ETL (OLTP – OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) vagy [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Áttelepítés SQL Serverról Azure SQL Databasera. SQL-adatszinkronizálás azonban az áttelepítés befejeződése után is használható, hogy a forrás és a cél szinkronban legyen tárolva.  | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Működés

- **Az adatváltozások nyomon követése:** Az adatszinkronizálás nyomon követi a módosításokat INSERT, Update és DELETE eseményindítók használatával. A módosításokat a rendszer a felhasználói adatbázis egyik oldalsó táblájába rögzíti. Vegye figyelembe, hogy az BULK INSERT alapértelmezés szerint nem indít el tüzet. Ha FIRE_TRIGGERS nincs megadva, a beszúrási eseményindítók nem lesznek végrehajtva. Adja hozzá a FIRE_TRIGGERS lehetőséget, hogy az adatszinkronizálás nyomon tudja követni ezeket a lapkákat. 
- **Az adatszinkronizálás:** Az adatszinkronizálást egy sugaras modellben tervezték. A központ egyenként szinkronizálja az egyes tagokat. A rendszer letölti a központból a tag felé irányuló módosításokat, majd feltölti a tagot a központba.
- **Ütközések feloldása:** Az adatszinkronizálás két lehetőséget kínál az ütközés feloldására, a *hub WINS* vagy a *tag WINS* használatára.
  - Ha a *hub WINS* lehetőséget választja, a hub módosításai mindig felülírják a tag módosításait.
  - Ha a *tag WINS* lehetőséget választja, a tag módosításai felülírják a központban lévő módosításokat. Ha több tag is van, akkor a végső érték attól függ, hogy melyik tag szinkronizálja először.

## <a name="compare-with-transactional-replication"></a>Összehasonlítás tranzakciós replikációval

| | Adatszinkronizálás | Tranzakciós replikáció |
|---|---|---|
| **Előnyök** | – Aktív-aktív támogatás<br/>– A helyszíni és a Azure SQL Database közötti kétirányú irányítás | – Alacsonyabb késés<br/>– Tranzakciós konzisztencia<br/>-Meglévő topológia újrafelhasználása az áttelepítés után <br/>– Az Azure SQL felügyelt példányának támogatása |
| **Hátrányok** | – Nincs tranzakciós konzisztencia<br/>– Nagyobb teljesítményre gyakorolt hatás | -Nem lehet közzétenni Azure SQL Database <br/>– Magas karbantartási díj |

## <a name="private-link-for-data-sync-preview"></a>Privát hivatkozás az adatszinkronizáláshoz (előzetes verzió)
Az új privát hivatkozás (előzetes verzió) funkció lehetővé teszi, hogy kiválassza a szolgáltatás által felügyelt privát végpontot, hogy biztonságos kapcsolatot hozzon létre a szinkronizálási szolgáltatás és a tag/központ adatbázisai között az adatszinkronizálási folyamat során. A szolgáltatás által felügyelt magánhálózati végpontok egy magánhálózati IP-cím egy adott virtuális hálózaton és alhálózaton belül. Az adatszinkronizáláson belül a szolgáltatás által felügyelt magánhálózati végpontot a Microsoft hozza létre, és kizárólag az adatszinkronizálási szolgáltatás használja egy adott szinkronizálási művelethez. A privát hivatkozás beállítása előtt olvassa el a szolgáltatás [általános követelményeit](sql-data-sync-data-sql-server-sql-database.md#general-requirements) . 

![Privát hivatkozás adatszinkronizáláshoz](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> Manuálisan jóvá kell hagynia a szolgáltatás által felügyelt magánhálózati végpontot a Azure Portal **privát végpontok kapcsolatainak** lapján a szinkronizálási csoport telepítése vagy a PowerShell használatával.

## <a name="get-started"></a>Bevezetés 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Adatszinkronizálás beállítása a Azure Portal

- [Az Azure SQL Data Sync beállítása](sql-data-sync-sql-server-configure.md)
- Adatszinkronizálási ügynök – [Az Azure SQL-adatszinkronizálás adatszinkronizálási ügynöke](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Az adatszinkronizálás beállítása a PowerShell-lel

- [Több adatbázis közötti szinkronizálás a Azure SQL Database-ben a PowerShell használatával](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [A PowerShell használata a Azure SQL Database adatbázisa és egy SQL Server példányban található adatbázisok közötti szinkronizáláshoz](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="set-up-data-sync-with-rest-api"></a>Adatszinkronizálás beállítása REST API
- [REST API használata több adatbázis közötti szinkronizáláshoz Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases-rest-api.md)

### <a name="review-the-best-practices-for-data-sync"></a>Az adatszinkronizálás ajánlott eljárásainak áttekintése

- [Ajánlott eljárások az Azure SQL Data Synchez](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Valami nem stimmel

- [Az Azure SQL Data Synckel hibaelhárítása](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Konzisztencia és teljesítmény

### <a name="eventual-consistency"></a>Végleges konzisztencia

Mivel az adatszinkronizálás trigger-alapú, a tranzakciós konzisztencia nem garantált. A Microsoft garantálja, hogy az összes módosítást végül megtörténik, és az adatszinkronizálás nem okozza az adatvesztést.

### <a name="performance-impact"></a>Teljesítményre gyakorolt hatás

Az adatszinkronizálás beszúrási, frissítési és törlési eseményindítókat használ a módosítások nyomon követéséhez. A változások nyomon követéséhez létrehozza a felhasználói adatbázis oldalsó táblázatait. Ezek a változás-követési tevékenységek hatással vannak az adatbázis számítási feladataira. Szükség esetén mérje fel a szolgáltatási szintet és a frissítést.

A szinkronizálási csoport létrehozása, frissítése és törlése során a kiépítés és a megszüntetés is hatással lehet az adatbázis teljesítményére.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Követelmények és korlátozások

### <a name="general-requirements"></a>Általános követelmények

- Minden táblának rendelkeznie kell egy elsődleges kulccsal. Egyetlen sorban ne módosítsa az elsődleges kulcs értékét. Ha módosítania kell egy elsődleges kulcs értékét, törölje a sort, majd hozza létre újra az új elsődleges kulcs értékével.

> [!IMPORTANT]
> Egy meglévő elsődleges kulcs értékének módosítása a következő hibás viselkedést eredményezi:
> - A központ és a tag közötti adatvesztés akkor is elvész, ha a szinkronizálás nem jelent problémát.
> - A szinkronizálás sikertelen lehet, mert a követési tábla nem létező sort tartalmaz a forrástól az elsődleges kulcs módosítása miatt.

- A pillanatkép-elkülönítést engedélyezni kell a szinkronizálási tagok és a központ esetében is. További információ: [Pillanatkép-elkülönítés az SQL Serveren](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- Ahhoz, hogy az adatszinkronizáláshoz privát hivatkozást lehessen használni, a tag és a hub adatbázisait is az Azure-ban (azonos vagy eltérő régiókban) kell tárolni, ugyanabban a felhőben (például a nyilvános felhőben vagy mindkettőben a kormányzati felhőben). Emellett a privát hivatkozás használatához a Microsoft. Network erőforrás-szolgáltatókat regisztrálni kell a hub-és tagkiszolgáló-kiszolgálókat üzemeltető előfizetésekhez. Végül manuálisan jóvá kell hagynia az adatszinkronizáláshoz tartozó privát hivatkozást a szinkronizálási konfigurációban a Azure Portal privát végponti kapcsolatok szakaszában, vagy a PowerShell használatával. A privát hivatkozás jóváhagyásáról további információt a [SQL-adatszinkronizálás beállítása](./sql-data-sync-sql-server-configure.md)című témakörben talál. Miután jóváhagyta a szolgáltatás által felügyelt privát végpontot, a szinkronizálási szolgáltatás és a tag/hub adatbázisok közötti kommunikáció a privát kapcsolaton keresztül történik. A meglévő szinkronizálási csoportok frissíthetik, hogy ez a funkció engedélyezve legyen.

### <a name="general-limitations"></a>Általános korlátozások

- Egy táblának nem lehet olyan azonosító oszlopa, amely nem az elsődleges kulcs.
- Az adatszinkronizálás használatához egy táblának fürtözött indexszel kell rendelkeznie.
- Az elsődleges kulcs nem rendelkezhet a következő adattípusokkal: sql_variant, Binary, varbinary, képet, XML.
- Legyen óvatos, ha a következő adattípusokat használja elsődleges kulcsként, mert a támogatott pontosság csak a második: Time, datetime, datetime2, DateTimeOffset.
- Az objektumok (adatbázisok, táblák és oszlopok) nevei nem tartalmazhatják a nyomtatható karaktereket (.), a bal oldali szögletes zárójelet ([) vagy a jobb oldali szögletes zárójelet (]).
- A tábla neve nem tartalmazhat nyomtatható karaktereket:! "# $% ' () * +-szóköz
- Azure Active Directory hitelesítés nem támogatott.
- Ha vannak olyan táblák, amelyeknek ugyanaz a neve, de a különböző sémák (például dbo. Customers és Sales. Customers), csak az egyik tábla lehet a szinkronizálásban.
- User-Defined adattípusú oszlopok nem támogatottak
- A kiszolgálók különböző előfizetések közötti áthelyezése nem támogatott. 

#### <a name="unsupported-data-types"></a>Nem támogatott adattípusok

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML támogatott)
- Kurzor, RowVersion, időbélyeg, Hierarchyid

#### <a name="unsupported-column-types"></a>Nem támogatott oszlopok típusai

Az adatszinkronizálás nem tudja szinkronizálni a csak olvasható vagy a rendszer által létrehozott oszlopokat. Például:

- Számított oszlopok.
- Rendszer által generált oszlopok az ideiglenes táblákhoz.

#### <a name="limitations-on-service-and-database-dimensions"></a>A szolgáltatás-és adatbázis-méretek korlátozásai

| **Méretek**                                                  | **Korlátot**              | **Áthidaló megoldás**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| A szinkronizált csoportok maximális száma, amelyhez bármely adatbázis tartozhat.       | 5                      |                             |
| Végpontok maximális száma egyetlen szinkronizálási csoportban              | 30                     |                             |
| A helyszíni végpontok maximális száma egyetlen szinkronizálási csoportban. | 5                      | Több szinkronizálási csoport létrehozása |
| Adatbázis, tábla, séma és oszlopnevek                       | 50 karakter/név |                             |
| Szinkronizálási csoport táblái                                          | 500                    | Több szinkronizálási csoport létrehozása |
| Egy szinkronizálási csoportba tartozó tábla oszlopai                              | 1000                   |                             |
| Tábla adatsorainak mérete                                        | 24 MB                  |                             |

> [!NOTE]
> Egyetlen szinkronizálási csoportban akár 30 végpont is lehet, ha csak egy szinkronizálási csoport van. Ha több szinkronizálási csoport is van, akkor az összes szinkronizálási csoporton belüli végpontok száma nem haladhatja meg a 30-at. Ha egy adatbázis több szinkronizálási csoporthoz tartozik, a rendszer több végpontnak számít, nem egy.

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

> [!NOTE]
> Ha privát hivatkozást használ, ezek a hálózati követelmények nem érvényesek. 

A szinkronizálási csoport létrehozásakor az adatszinkronizálási szolgáltatásnak csatlakoznia kell a hub-adatbázishoz. A szinkronizálási csoport létrehozásakor az Azure SQL Server-kiszolgálónak a következő konfigurációval kell rendelkeznie a `Firewalls and virtual networks` beállításaiban:

 * A *nyilvános hálózati hozzáférés megtagadását* be *kell állítani.*
 * Az *Azure-szolgáltatások és-erőforrások elérésének engedélyezése ehhez a kiszolgálóhoz* az *Igen* értékre kell állítani, vagy az [adatszinkronizálási szolgáltatás által használt IP-címekhez](network-access-controls-overview.md#data-sync)létre kell hoznia IP-szabályokat.

A szinkronizálási csoport létrehozása és üzembe helyezése után le is tilthatja ezeket a beállításokat. A Szinkronizáló ügynök közvetlenül csatlakozik a hub-adatbázishoz, és a kiszolgáló [tűzfal IP-szabályainak](firewall-configure.md) vagy [privát végpontjai](private-endpoint-overview.md) segítségével engedélyezheti az ügynök számára a központi kiszolgáló elérését.

> [!NOTE]
> Ha megváltoztatja a szinkronizálási csoport séma-beállításait, akkor engedélyeznie kell, hogy az adatszinkronizálási szolgáltatás újra hozzáférhessen a kiszolgálóhoz, hogy a központi adatbázis újra kiépíthető legyen.

## <a name="faq-about-sql-data-sync"></a>Gyakori kérdések SQL-adatszinkronizálás

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Mennyibe kerül a SQL-adatszinkronizálás szolgáltatás díja

Magáért a SQL-adatszinkronizálás szolgáltatásért nem számítunk fel díjat. Azonban továbbra is gyűjthet adatátviteli díjakat a SQL Database-példányon belüli és kívüli adatáthelyezésért. További információ: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Mely régiók támogatják az adatszinkronizálást

SQL-adatszinkronizálás minden régióban elérhető.

### <a name="is-a-sql-database-account-required"></a>SQL Database fiók szükséges

Igen. A hub-adatbázis üzemeltetéséhez SQL Database-fiókkal kell rendelkeznie.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Használhatok adatszinkronizálást SQL Server adatbázisok közötti szinkronizálásra

Nem közvetlenül. A SQL Server adatbázisok közötti szinkronizálás közvetetten, azonban egy központi adatbázis létrehozása az Azure-ban, majd a helyszíni adatbázisok hozzáadása a szinkronizálási csoporthoz.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Használhatok adatszinkronizálást a különböző előfizetésekhez tartozó SQL Database adatbázisok közötti szinkronizáláshoz

Igen. A különböző előfizetések tulajdonában lévő erőforráscsoportokba tartozó adatbázisok között lehet szinkronizálni.

- Ha az előfizetések ugyanahhoz a bérlőhöz tartoznak, és Önnek van engedélye az összes előfizetéshez, a szinkronizálási csoportot a Azure Portal is konfigurálhatja.
- Ellenkező esetben a PowerShell használatával kell felvennie a különböző előfizetésekhez tartozó szinkronizálási tagokat.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Használhatok adatszinkronizálást a különböző felhőkhöz (például az Azure Public Cloud és az Azure China 21Vianet) tartozó SQL Database adatbázisok közötti szinkronizáláshoz.

Igen. A különböző felhőkhöz tartozó adatbázisok között lehet szinkronizálni. A különböző előfizetésekhez tartozó szinkronizálási tagok hozzáadásához a PowerShellt kell használnia.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Használhatom az adatszinkronizálást az éles adatbázisból származó adatok egy üres adatbázisba való kivetéséhez, majd szinkronizálni őket

Igen. Hozza létre manuálisan a sémát az új adatbázisban az eredetiből származó parancsfájlok futtatásával. A séma létrehozása után vegye fel a táblákat egy szinkronizálási csoportba az adatmásoláshoz és a szinkronizálás megtartásához.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Az adatbázisok biztonsági mentése és visszaállítása SQL-adatszinkronizálás használatával

Nem javasoljuk, hogy a SQL-adatszinkronizálás használatával készítsen biztonsági másolatot az adatairól. Nem lehet biztonsági mentést készíteni és visszaállítani egy adott időpontra, mert SQL-adatszinkronizálás szinkronizálások nincsenek verziója. Emellett SQL-adatszinkronizálás nem készít biztonsági mentést más SQL-objektumokról, például tárolt eljárásokról, és nem hajtja végre gyorsan a visszaállítási művelet megfelelőjét.

Egy ajánlott biztonsági mentési módszer: [adatbázis másolása Azure SQL Databaseban](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Az adatszinkronizálás titkosított táblákat és oszlopokat képes szinkronizálni

- Ha egy adatbázis Always Encrypted használ, csak azokat a táblákat és oszlopokat szinkronizálhatja *, amelyek nincsenek titkosítva* . A titkosított oszlopok nem szinkronizálhatók, mert az adatszinkronizálás nem tudja visszafejteni az adattitkosítást.
- Ha egy oszlop Column-Level titkosítást (CLE) használ, szinkronizálhatja az oszlopot, ha a sor mérete nem éri el a maximálisan megengedett 24 MB-ot. Az adatszinkronizálási szolgáltatás a kulcs (CLE) által titkosított oszlopot normál bináris adatként kezeli. A többi szinkronizálási tag adatvisszafejtéséhez ugyanazzal a tanúsítvánnyal kell rendelkeznie.

### <a name="is-collation-supported-in-sql-data-sync"></a>SQL-adatszinkronizálás támogatja a rendezést.

Igen. A SQL-adatszinkronizálás a következő helyzetekben támogatja a rendezést:

- Ha a kiválasztott szinkronizálási séma táblái még nem szerepelnek a központban vagy a tagok adatbázisaiban, akkor a szinkronizálási csoport telepítésekor a szolgáltatás automatikusan létrehozza a megfelelő táblákat és oszlopokat az üres célhely-adatbázisokban kiválasztott rendezési beállításokkal.
- Ha a szinkronizálandó táblák már szerepelnek a központ és a tag adatbázisaiban is, SQL-adatszinkronizálás megköveteli, hogy az elsődleges kulcs oszlopai azonos rendezéssel rendelkezzenek a központi és a tagok adatbázisai között a szinkronizálási csoport sikeres telepítéséhez. Az elsődleges kulcs oszlopaitól eltérő oszlopok esetében nincsenek rendezési korlátozások.

### <a name="is-federation-supported-in-sql-data-sync"></a>Támogatott-e az összevonás SQL-adatszinkronizálás

Az összevonási gyökér adatbázisa korlátozás nélkül használható a SQL-adatszinkronizálás szolgáltatásban. Az összevont adatbázis-végpont nem adható hozzá a SQL-adatszinkronizálás aktuális verziójához.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Használhatom az adatszinkronizálást a Dynamics 365-ből exportált adatok szinkronizálására a saját adatbázis-(BYOD-) szolgáltatás használata révén?

A Dynamics 365 saját adatbázis-funkciója lehetővé teszi, hogy a rendszergazdák a saját Microsoft Azure SQL Database-be exportálják az alkalmazásból az adatentitásokat. Az adatszinkronizálás használatával más adatbázisokba is szinkronizálhatja ezeket az adatfájlokat, ha az adatexportálás **növekményes leküldéses** használatával történik (a teljes leküldéses funkció nem támogatott), és az **Eseményindítók engedélyezése a célként megadott adatbázisban** **Igen** értékre van állítva.

## <a name="next-steps"></a>Következő lépések

### <a name="update-the-schema-of-a-synced-database"></a>Szinkronizált adatbázis sémájának frissítése

Frissítenie kell egy adatbázis sémáját egy szinkronizálási csoportban? A séma módosításai nem replikálódnak automatikusan. Néhány megoldásért tekintse meg a következő cikkeket:

- [A séma változásainak SQL-adatszinkronizálás az Azure-ban való replikálásának automatizálása](./sql-data-sync-update-sync-schema.md)
- [PowerShell használata meglévő szinkronizálási csoport szinkronizálási sémájának frissítéséhez](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Figyelés és hibaelhárítás

A SQL-adatszinkronizálás a várt módon működik? A tevékenységek figyeléséhez és a hibák elhárításához tekintse meg a következő cikkeket:

- [SQL-adatszinkronizálás figyelése Azure Monitor naplókkal](./monitor-tune-overview.md)
- [Az Azure SQL Data Synckel hibaelhárítása](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>További információ a Azure SQL Database

A Azure SQL Databaseról további információt a következő cikkekben talál:

- [Az SQL Database áttekintése](sql-database-paas-overview.md)
- [Az adatbázis életciklusának felügyelete](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
