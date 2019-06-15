---
title: Az Azure-beli SQL-lehetőségek között válogat |} A Microsoft Docs
description: Ismerje meg, hogyan választhat a központi telepítési beállítások Azure SQL Database és az Azure SQL Database és SQL Server közötti Azure-beli virtuális gépeken
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server-felhő, SQL Server a felhőben, PaaS-adatbázis, felhőalapú SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: d9cd5ba0b697cbf67f943eb49d66010745d8561e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584853"
---
# <a name="choose-the-right-sql-server-option-in-azure"></a>Válassza ki a jobb oldalon az SQL Server beállítása az Azure-ban

Az Azure-ban, az SQL Server számítási feladatok egy szolgáltatott infrastruktúra (IaaS) futtatja, vagy egy üzemeltetett szolgáltatást futtató rendelkezhet ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). Belül PaaS hogy több központi telepítési beállítások és szolgáltatási szinteken belül minden egyes üzembe helyezési lehetőség. A legfontosabb kérdés, lépjen kapcsolatba a PaaS vagy IaaS közötti meghatározásakor az szeretné az adatbázis kezelése, -javítások alkalmazása, készítsen biztonsági másolatokat, vagy szeretne delegálni ezeket a műveleteket az Azure-bA?
Attól függően, a válasz a következő lehetőségek állnak rendelkezésére:

- [Azure SQL Database](sql-database-technical-overview.md): Egy teljes körűen felügyelt SQL-adatbázismotor, a legújabb stabil Enterprise Edition, SQL Server-alapú. Ez az egy relációs adatbázis--szolgáltatásként (DBaaS), amely az iparági kategóriába esik az Azure felhőalapú *Platform--szolgáltatásként (PaaS)* . Az SQL database több központi telepítési beállítások, amelyek mindegyike épül szabványos hardvereken és szoftvereken rendszer tulajdonában lévő üzemeltetett és a Microsoft által karbantartott rendelkezik. Az SQL Database beépített szolgáltatásai és funkciói széles körű konfigurációt az SQL Server használatakor igénylő használhatja (a helyszínen vagy egy Azure virtuális gépen). Az SQL Database használatalapú fizetéssel működik, emellett a teljesítményigény növekedése esetére lehetőség van vertikális vagy horizontális felskálázásra is. Az SQL Database további funkciók, amelyek nem érhetők el az SQL Server, például a beépített magas rendelkezésre állást, intelligenciát és felügyeleti rendelkezik. Az Azure SQL Database kínál az alábbi telepítési lehetőségek közül:
  
  - Mint egy [önálló adatbázis](sql-database-single-database.md) saját erőforráskészlettel felügyelt SQL Database-kiszolgálón keresztül. Önálló adatbázis hasonlít egy [tartalmazott adatbázisok](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) az SQL Server. Ez a beállítás a felhőben született alkalmazásokhoz új probléma a modern alkalmazások fejlesztése van optimalizálva.
  - Egy [rugalmas készlet](sql-database-elastic-pool.md), azaz egy SQL Database-kiszolgálón keresztül felügyelt közös erőforráskészlettel az adatbázis-gyűjtemény. Önálló adatbázisok áthelyezhetők, és a egy rugalmas készletből. Ez a beállítás a modern alkalmazásnak a több-bérlős SaaS-alkalmazás használatával új felhőben született alkalmazások fejlesztését van optimalizálva.
  - [Felügyelt példány](sql-database-managed-instance.md), amely gyűjteménye, rendszer- és felhasználói adatbázisok közös erőforráskészlettel az. Felügyelt példány hasonlít a [Microsoft SQL Server adatbázismotor] ajánlat megosztott erőforrásokat az adatbázisok és a példány hatókörébe tartozó további funkciók egy példányát. Felügyelt példány támogatja-e a helyszíni adatbázis-migrálás minimális nincs adatbázis módosítása. Ez a beállítás biztosít minden Azure SQL Database a PaaS előnyei, de csak korábban elérhető SQL virtuális gépek képességekkel bővíti. Ez magában foglalja egy natív virtuális hálózati (VNet) és közel 100 %-os kompatibilitást a helyszíni SQL Server.
- [Az SQL Server Azure virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/sql-server/) iparági kategóriába esik *infrastruktúra--szolgáltatásként (IaaS)* , és lehetővé teszi, hogy az SQL Server futtatása egy teljes körűen felügyelt virtuális gépen az Azure-felhőben. [Az SQL Server-virtuálisgépek](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) szabványos hardvereken, hogy a rendszer tulajdonában lévő üzemeltetett és a Microsoft által karbantartott is futtathatja. Az SQL Server virtuális gépen is fizetheti-,-nyissa meg az SQL Server-licence már szerepel egy SQL Server-lemezképet, vagy egyszerűen használhatja a meglévő licenchez. Állítsa le vagy folytatása a virtuális gép igény szerint is. Az SQL Server telepítve van, és a Windows Server vagy Linux rendszerű virtuális gépek (VM) az Azure-ban, más néven egy infrastruktúra-szolgáltatás (IaaS) a felhőben üzemeltetett. Migrálás jó választás a helyszíni SQL Server-adatbázisokat és alkalmazásokat adatbázis változás nélkül, egy SQL Server Azure-beli virtuális gépeken. Az összes legújabb verzióit és kiadásait az SQL Server telepítési IaaS virtuális gépen érhetők el. Az SQL Database-ből legfontosabb különbség az, hogy engedélyezik-e teljes körűen felügyelve az adatbázismotor SQL Server virtuális gépek. Amikor karbantartás vagy javítások elindul, változtassa meg a helyreállítási modelljét egyszerűre vagy Tömegesen naplózott ahhoz, hogy gyorsabban betöltődő kevésbé napló, szüneteltetése vagy indítsa el a motor, amikor szükséges, és teljes mértékben testre szabható az SQL Server adatbázismotor választhat. A további felügyelet együttműködik a hozzáadott feladata, hogy a virtuális gépek felügyeletéhez.

Ezek a beállítások közötti fő különbség a következő táblázatban láthatók:

| SQL Server virtuális gépen | Felügyelt példány az SQL Database-ben | Önálló adatbázis / rugalmas készlet, SQL Database-ben |
| --- | --- | --- |
|Az SQL Server motoron teljes hozzáféréssel rendelkezik.<br/>Akár 99,95 %-os elérhetőségét.<br/>Teljes paritásos a helyszíni SQL Server megfelelő verziójával.<br/>Rögzített, jól ismert adatbázis motor verziója.<br/>Egyszerű áttelepítés a helyszíni SQL Server.<br/>Magánhálózati IP-cím az Azure virtuális hálózaton belül.<br/>Alkalmazás vagy a gazdagépen, amelyen az SQL Server kerül szolgáltatások telepítésének lehetősége van.| A helyszíni SQL Server magas rendelkezésre állással rendelkezik.<br/>garantált 99,99 %-os elérhetőségét.<br/>Beépített biztonsági javítás, a helyreállítás.<br/>Legújabb stabil adatbázismotor verzióra.<br/>Egyszerű migrálás SQL Serverről.<br/>Magánhálózati IP-cím az Azure virtuális hálózaton belül.<br/>Beépített fejlett intelligenciát és védelmet.<br/>Online módosítsa erőforrások (CPU/storage).|A leggyakrabban használt SQL Server-funkciók érhetők el.<br/>garantált 99,99 %-os elérhetőségét.<br/>Beépített biztonsági javítás, a helyreállítás.<br/>Legújabb stabil adatbázismotor verzióra.<br/>Lehetővé teszi a szükséges erőforrások (CPU/storage) hozzárendelése az egyes adatbázisok.<br/>Beépített fejlett intelligenciát és védelmet.<br/>Online módosítsa erőforrások (CPU/storage).|
|A biztonsági mentések és a javítások kezelését kell.<br>A saját magas rendelkezésre állású megoldás megvalósításához szüksége.<br/>Van egy állásidő a resources(CPU/storage) módosítása közben|Van még néhány nem érhető el az SQL Server szolgáltatásokat minimális számát.<br/>Nem garantált pontos karbantartási idő (de szinte transzparens).<br/>Kompatibilitás az SQL Server-verzió csak az adatbázis kompatibilitási szintek segítségével lehet megvalósítani.|Előfordulhat, hogy a migrálás SQL Serverről rögzített.<br/>Néhány SQL Server-funkciók nem érhetők el.<br/>Nem garantált pontos karbantartási idő (de szinte transzparens).<br/>Kompatibilitás az SQL Server-verzió csak az adatbázis kompatibilitási szintek segítségével lehet megvalósítani.<br/>Nem lehet hozzárendelni a magánhálózati IP-cím (korlátozhatja a hozzáférést, tűzfal-szabályok használatával).|

Ismerje meg, hogyan illeszkedik a különböző telepítési lehetőségek a Microsoft data platformon, és rátalálhat az üzleti igényeinek leginkább megfelelő lehetőségre. Akár az adminisztrációs terhek csökkentését, akár a költségek csökkentését helyezi előtérbe, ez a cikk segít eldönteni, hogy melyik megközelítés képes teljesíteni az Ön számára legfontosabb üzleti igényekhez kötődő elvárásokat.

## <a name="microsofts-sql-data-platform"></a>A Microsoft SQL-adatplatform

Az Azure-platformon futó és a helyszíni SQL Server-adatbázisok közötti összehasonlítás első lépése, hogy leszögezzük: semmiről nem kell lemondania. A Microsoft adatplatformja különböző fizikai, helyszíni gépeken, magánfelhőt alkalmazó környezetekben, külső fejlesztőtől származó magánfelhő-környezetekben és nyilvános felhőkben egyaránt lehetővé teszi az SQL Server-technológia felhasználását. Az Azure-beli virtuális gépeken futó SQL Server helyszíni és felhőben futó üzemelő példányok együttes használatával segít megfelelni az egyedi és sokszínű üzleti igényeiknek, ráadásul Ön az összes környezetben ugyanazokat a kiszolgálótermékeket, fejlesztői eszközöket és ismereteket alkalmazhatja.

   ![Felhőalapú SQL Server-beállítások: SQL server IaaS vagy SaaS SQL-adatbázis a felhőben.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Ahogy az az ábrán is látható, az ajánlatokat az infrastruktúra feletti felügyelet mértéke (X-tengely) és az adatbázisszintű konszolidáció és automatizálás által elérhető költségmegtakarítás mértéke (Y-tengely) különbözteti meg egymástól.

Az alkalmazások fejlesztése során négy alapvető lehetőség áll rendelkezésre az alkalmazás SQL Server részének futtatására:

- SQL Server nem virtuális, fizikai gépeken
- SQL Server helyszíni virtuális gépeken (magánfelhő)
- SQL Server Azure virtuális gépeken (Microsoft nyilvános felhő)
- Azure SQL Database (Microsoft nyilvános felhő)

A következő szakaszban megismerkedhet a Microsoft nyilvános felhőben az SQL Server: Az Azure SQL Database és az SQL Server Azure virtuális gépeken. Ezenfelül áttekintünk néhány általános üzleti igényt, amelyek segítségével megállapíthatja, hogy melyik verzió a legjobb az Ön alkalmazása számára.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Az Azure SQL Database és az Azure virtuális gépeken futó SQL Server részletes bemutatása

Általánosságban elmondható, hogy ezeket az SQL-verziókat különböző célok végrehajtására optimalizálták:

- **Azure SQL Database**

Optimalizált kiépítéséhez és a számos adatbázis kezelése a minimális átfogó felügyeleti költségek csökkentése érdekében. Ez a megoldás csökkenti az adminisztrációra fordítandó összeget, mivel használata esetén nincs szükség virtuális gépek, operációs rendszerek vagy adatbázisszoftverek kezelésére. Nem kell foglalkoznia a frissítések, a magas rendelkezésre állás és a [biztonsági mentések](sql-database-automated-backups.md) kezelésével sem. Összefoglalva elmondható, hogy az Azure SQL Database segítségével egyetlen számítástechnikai vagy fejlesztői erőforrás jóval nagyobb számú adatbázis kezelésére válik alkalmassá. [Rugalmas készletek](sql-database-elastic-pool.md) is támogatja a SaaS több-bérlős architektúrák funkciókat, beleértve a bérlők elkülönítését és az erőforrások adatbázisok közötti megosztása a költségcsökkentés érdekében méretezhető. [Felügyelt példány](sql-database-managed-instance.md) támogatást biztosít a példány hatókörű funkciói a meglévő alkalmazások egyszerű migrálás engedélyezéséhez, valamint az erőforrások adatbázisok közötti megosztása.

- **Azure virtuális gépeken futó SQL Server**

Meglévő alkalmazások Azure-ba való migrálás vagy kiterjesztése a meglévő helyszíni alkalmazásokkal, a hibrid felhőre optimalizált. Emellett a virtuális gépen futó SQL Servert használhatja hagyományos SQL Server-alkalmazások fejlesztésére és tesztelésére is. Az Azure virtuális gépeken futó SQL Server esetében teljes körű rendszergazdai jogokkal rendelkezik egy dedikált SQL Server-példányban és egy felhőalapú virtuális gépen. Ez tökéletes választás, ha a vállalatnál adva vannak a virtuális gépek fenntartásához szükséges számítástechnikai erőforrások. E lehetőségeknek köszönhetően a legapróbb részletekig testre szabhatja a rendszert, úgy, hogy az a leginkább megfeleljen az alkalmazás teljesítmény- és rendelkezésre állási követelményeinek.

Az alábbi táblázatban az SQL Database és az Azure virtuális gépeken futó SQL Server főbb jellemzőit foglaljuk össze:

| | Az SQL Database önálló adatbázisok és rugalmas készletek | Az SQL Database felügyelt példányai |Az SQL Server-beli virtuális gépek |
| --- | --- | --- |---|
| **A következőkre alkalmas:** |Új felhőalapú alkalmazásai számára, hogy a legújabb stabil az SQL Server funkciói és fejlesztési és a marketing szűkösebb időkeret áll. | Új alkalmazások vagy a meglévő helyszíni alkalmazásokat, amelyeket szeretne a legújabb stabil SQL Server-funkciók és, amely a felhőbe legfeljebb minimális változtatásokra lesznek áttelepítve.  | Igénylő alkalmazásokhoz, gyors áttelepítés a felhőbe, a minimális módosításait vagy módosítása nélkül. Ha gyorsan kell elvégezni a fejlesztést és a tesztelést, és nem szeretne helyszíni nem üzemi SQL Server-hardvert vásárolni. |
|  | Olyan csoportok számára, amelyeknek beépített magas rendelkezésre állási, vészhelyreállítási és frissítési mechanizmusokkal rendelkező adatbázisokra van szüksége. | Ugyanaz, mint az SQL Database egyetlen vagy készletezett adatbázisok. | Csoportok, amelyek konfigurálni, részletes hangolása, testreszabása és magas rendelkezésre állást, vészhelyreállítást és javítása az SQL Server kezeléséhez. Mindezeket egyes beépített automatizált funkciók jelentősen leegyszerűsítik. |
|  | Olyan csoportok számára, amelyek nem szeretnének az alkalmazás mögötti operációs rendszer beállításával és egyéb konfigurációs feladatokkal foglalkozni. | Ugyanaz, mint az SQL Database egyetlen vagy készletezett adatbázisok. | Teljes körű rendszergazdai jogosultságokkal rendelkező testre szabott környezetre van szüksége. |
|  | Akár 100 TB-os adatbázis. | Legfeljebb 8 TB. | Akár 64 TB tárterülettel rendelkező SQL Server-példányokhoz. A példányok annyi adatbázist képesek támogatni, amennyire szükség van. |
| **Kompatibilitási** | Támogatja a legtöbb a helyszíni adatbázis-szintű képességeit. | Szinte minden támogatja a helyszíni példányszintű és az adatbázis-szintű képességek. | Támogatja az összes helyszíni képességeit. |
| **Erőforrások:** | Nem szeretne konfigurálása és felügyelete az alapul szolgáló infrastruktúra informatikai erőforrásokat fordítani, de szeretné az alkalmazási rétegre összpontosíthat. | Ugyanaz, mint az SQL Database egyetlen vagy készletezett adatbázisok. | Ha rendelkezik bizonyos informatikai erőforrásokkal a konfigurációhoz és felügyelethez. Mindezeket egyes beépített automatizált funkciók jelentősen leegyszerűsítik. |
| **Tulajdonosi költségek:** | Nincsenek hardverköltségek és alacsonyabbak a felügyeleti költségek. | Ugyanaz, mint az SQL Database egyetlen vagy készletezett adatbázisok. | Nincsenek hardverköltségek. |
| **Az üzletmenet folytonossága** |Mellett [infrastruktúra beépített hibatűrési képességeket](sql-database-high-availability.md), Azure SQL Database olyan szolgáltatásokat nyújt, mint például [automatizált biztonsági mentések](sql-database-automated-backups.md), [-időponthoz visszaállítási](sql-database-recovery-using-backups.md#point-in-time-restore), [geo-visszaállítás](sql-database-recovery-using-backups.md#geo-restore), [aktív georeplikáció](sql-database-active-geo-replication.md), és [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md) üzletmenet-folytonossági növelése érdekében. További információk: [SQL Database business continuity overview](sql-database-business-continuity.md) (Az SQL Database üzletmenet-folytonossági funkcióinak áttekintése). | Ugyanaz, mint az SQL Database egyetlen vagy készletezett adatbázisok, valamint a felhasználó által kezdeményezett, csak másolatot biztonsági mentéseket érhetők el. | Az Azure virtuális gépeken futó SQL Server lehetőséget kínál az adatbázis konkrét igényeinek megfelelő magas rendelkezésre állási és vészhelyreállítási megoldás kialakítására. Így az adott alkalmazásra optimalizálhatja a rendszert. Szükség esetén önállóan is tesztelheti a feladatátvételt. További információk: [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) (Magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó SQL Serveren). |
| **Hibrid felhő:** |A helyszíni alkalmazások képesek az Azure SQL Database-ben tárolt adatok elérésére. | [Natív virtuálishálózat-implementáció](sql-database-managed-instance-vnet-configuration.md) és a helyszíni környezet Azure Express Route vagy VPN-átjáró használatával való kapcsolódás. | Az Azure virtuális gépeken futó SQL Server esetében részben a felhőben, részben helyszínen futó alkalmazásokat is használhat. Például az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) segítségével kiterjesztheti helyszíni hálózatát és Active Directory-tartományát a felhőre. A hibrid felhőbeli megoldások további információkért lásd: [a felhő helyszíni Adatmegoldások kiterjesztése](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Előfizetőként támogatja az [SQL Server tranzakciós replikációját](https://msdn.microsoft.com/library/mt589530.aspx) az adatok replikálása céljából. | Replikáció támogatott a felügyelt példány előzetes verzióként. | Teljes körűen támogatja az [SQL Server tranzakciós replikációját](https://msdn.microsoft.com/library/mt589530.aspx), [Always On rendelkezésre állási csoportok](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services és a Naplóküldést az adatok replikálása. Emellett a hagyományos SQL Server-biztonságimásolatok is teljes körűen támogatottak. |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Az Azure SQL Database vagy az Azure virtuális gépeken futó SQL Server használatát támogató üzleti indokok

Van több, válassza ki az SQL-adatbázisok üzemeltetéséhez PaaS vagy IaaS mellett is befolyásoló tényezők:

- [Költség](#cost) -egyaránt PaaS és IaaS beállítást tartalmazza base pontosan rögzítik, az alapul szolgáló infrastruktúrát, és licencelési díjszabása. Azonban az IaaS-beállítást kell további időt és erőforrásokat az adatbázis kezeléséhez, amíg a paas is ezeket a felügyeleti funkciókat az ár tartalmazza az első támogatásán. IaaS-beállítás lehetővé teszi, hogy a leállított az erőforrások miközben nem használja őket a költségek csökkentéséhez, amíg PaaS-verzió mindig fut, kivéve, ha ha dobja el és hozza létre újból az erőforrásokat szükség esetén.
- [Felügyeleti](#administration) – PaaS beállítások csökkentheti az, hogy mennyi ideig kell az adatbázis felügyeletéhez befektetni. Azonban egyéni felügyeleti feladatokat, és hajtsa végre, vagy futtatott parancsfájlok is korlátozza. A CLR-beli például önálló vagy készletezett adatbázisok használata nem támogatott, de a felügyelt példány esetében támogatott. Emellett nincsenek központi telepítési lehetőségek PaaS támogatja az nyomkövetési jelzők.
- [Szolgáltatásiszint-szerződés](#service-level-agreement-sla) – is IaaS és PaaS biztosít magas, iparági standard szintű SLA-t. PaaS-beállítás 99,99 %-os SLA-t, garantálja, míg IaaS garantálja 99,95 %-os SLA-infrastruktúra, ami azt jelenti, hogy meg kell valósítania az adatbázisok rendelkezésre állásának biztosításához a további mechanizmusok. A szélsőséges esetben ha azt szeretné, hogy a megfelelő PaaS, magas rendelkezésre állású megoldás megvalósításához szüksége lehet további SQL-kiszolgáló létrehozása a virtuális gép, és előfordulhat, hogy a double a költségek az adatbázis AlwaysOn rendelkezésre állási csoportok konfigurálásához.
- [Az Azure-bA áthelyezéséhez szükséges idő](#market) – SQL Server Azure-beli virtuális gépen a környezetben, a pontos egyezés, ezért áttelepítés a helyszínről az Azure SQL virtuális Géphez nem más, mint az adatbázisok áthelyezését egy helyszíni kiszolgáló között. Felügyelt példány is lehetővé teszi a rendkívül egyszerű áttelepítés; Előfordulhat azonban, bizonyos módosításokat az alkalmazása a felügyelt példány történő áttelepítése előtt kell.

Ezek a tényezők az alábbi szakaszok részletesebben tárgyalja.

### <a name="cost"></a>Költségek

Az adatbázisok üzemeltetési módjának kiválasztását általában a rendelkezésre álló források határozzák meg, és ez egyformán igaz, akár szűkös költségvetésű startupról, akár egy nagyvállalat szigorú költségkeretek közé szorított osztályáról van szó. Ebben a szakaszban megismerkedhet a számlázási és licencelési alapokkal az Azure-ban két relációs adatbázis: Az SQL Database és az SQL Server Azure virtuális gépeken. Megismerheti az alkalmazás összköltségének kiszámításának módját is.

#### <a name="billing-and-licensing-basics"></a>A számlázás és a licencelés alapjai

Jelenleg **SQL Database** szolgáltatásként vásárolható meg, és többféle üzembe helyezési lehetőséget, és és az erőforrások, amelyek mindegyike számlázzuk óránként egy rögzített a szolgáltatásszint alapján díjak többféle szolgáltatáscsomagban érhető el, és a számítási méret mellett dönt. A legfrissebb információkért támogatott az aktuális szolgáltatásszintek, méretek és a storage összegeket, lásd: számítási [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

- Az önálló SQL Database-adatbázist választhat, amely a legjobban az igényeinek, az árak kezdet: 5 USD/hónap alapszintű számos szolgáltatási réteg.
- Létrehozhat [rugalmas készletek](sql-database-elastic-pool.md) megosztott erőforrások adatbázispéldányok csökkentheti a költségeket és befogadásához használat hirtelen megugró kihasználtság.
- Az SQL Database felügyelt példány a saját licencét is használhatja. A bring-your-own licenc további információkért lásd: [az Azure frissítési garancián keresztüli Licenchordozhatóság](https://azure.microsoft.com/pricing/license-mobility/) vagy [Azure Hybrid Benefit Számológép](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) megtekintéséhez hogyan **akár 40 %-osmentése**.

Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik. Módosíthatja a szolgáltatási szintek és méretek szerint változó átviteli igényekhez az alkalmazás számítási dinamikusan.

Az **SQL Database** használata esetén az adatbázis automatikus konfigurálásáért, javításáért és frissítéséért a Microsoft felel, így Önnek kevesebbet kell adminisztrációra költenie. Ezenfelül a [beépített biztonsági mentési](sql-database-automated-backups.md) funkciókkal is jelentős költségmegtakarítást érhet el, különösen, ha nagyszámú adatbázist használ.

Az **Azure virtuális gépeken futó SQL Server** segítségével használhatja a platform által biztosított bármelyik SQL Server-rendszerképet (amely tartalmaz licencet) vagy saját SQL Server-licencét. A támogatott SQL Server-verziók (2008R2, 2012, 2014, 2016) és -kiadások (Developer, Express, Web, Standard, Enterprise) mindegyike elérhető. Emellett a rendszerképek saját licenccel használható (BYOL) verziói is elérhetők. Az Azure által biztosított rendszerképek használata esetén az üzemeltetési költségek a virtuális gép méretétől, valamint a használni kívánt SQL Server-verziótól függnek. Függetlenül a virtuális gép méretétől és az SQL Server percalapú licencelési költségeit az SQL Server és a Windows vagy Linux-kiszolgálón, valamint a virtuális gép lemezeivel kapcsolatban felmerülő Azure Storage költségeket kell fizetnie. A percalapú számlázásnak köszönhetően igényeinek megfelelő ideig használhatja az SQL Servert, anélkül, hogy további SQL Server-licenceket kellene vásárolnia. Az Azure-ban tenné a saját SQL Server-licencét, ha a kiszolgáló, és csak a tárolási költségek díjkötelesek. A saját licenc használatával kapcsolatos további információkért lásd: [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) (Licenchordozás az Azure Frissítési garancia programja keretében). Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik.

#### <a name="calculating-the-total-application-cost"></a>Az alkalmazás összköltségének kiszámítása

Ha elkezd használni egy felhőalapú platform, az alkalmazás futtatásának díja tartalmazza az új fejlesztési költség- és adminisztrációra fordítandó összeget, valamint a nyilvános felhő platform szolgáltatási költségei teszik.

**Az Azure SQL Database használata esetében:**

- Legalacsonyabbra csökkentett adminisztrációs költségek
- Korlátozott szoftverfejlesztési költségek az áttelepített alkalmazások (felügyelt példányok)
- Az SQL Database szolgáltatási költségei
- Nincsenek hardverköltségek vásárlási

**Az Azure virtuális gépeken futó SQL Server esetében:**

- Nagyobb adminisztrációs költségek
- Kizárólag a migrált alkalmazások fejlesztési költségek nélkül
- Virtuális gép szolgáltatási költségei
- Nincsenek hardverköltségek vásárlási

Az árakkal kapcsolatos további információkért lásd a következő erőforrásokat:

- [Az SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/)
- [Virtuális gépek díjszabását](https://azure.microsoft.com/pricing/details/virtual-machines/) a [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) és [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Adminisztráció

Számos vállalat számára a felhőszolgáltatásokra történő átállást az összetett adminisztrációs feladatok kiszervezése ösztönzi, és nem csupán a költségszempontok. Az IaaS és PaaS, a Microsoft felügyeli az alapul szolgáló infrastruktúra, és automatikusan biztosít a vész-helyreállítási adatok, konfigurálja és frissíti az adatbázisszoftvert, kezeli a terheléselosztást és does a transzparens feladatátvételt, ha van egy Kiszolgálóhiba adatközponton belül.

- A **Azure SQL Database**, továbbra is az adatbázis, de az adatbázismotor, az operációs rendszer vagy a hardveres már nincs szüksége. Ön felelős például a következő területekért: adatbázisok és bejelentkezési adatok, indexek és lekérdezések hangolása, naplózás és biztonság. Emellett egy másik adatközpontba magas rendelkezésre állás konfigurálása szükséges minimális konfiguráció és felügyelet.
- Az **Azure virtuális gépeken futó SQL Server** használata esetén teljes mértékben Ön végezheti el az operációs rendszer és az SQL Server-példány konfigurálását. A virtuális gépen Ön dönti el, hogy mikor frissíti az operációs rendszert és az adatbázisszoftvert, illetve, hogy telepít-e kiegészítő programokat, például víruskereső vagy biztonsági mentési eszközöket. Egyes automatizált szolgáltatások jelentősen leegyszerűsítik a frissítést, a biztonsági mentések készítését és a magas rendelkezésre állást. Ezenfelül meghatározhatja a virtuális gép méretét, a lemezek számát, valamint a tárhely konfigurációját is. Az Azure-ban igény szerint módosítható a virtuális gépek mérete. További információk: [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/windows/sizes.md) (Virtuális gépek és felhőszolgáltatások mérete az Azure-ban).

### <a name="service-level-agreement-sla"></a>Szolgáltatói szerződés (SLA)

Számos számítástechnikai osztály számára elsődleges prioritást jelent a szolgáltatói szerződésben (SLA) vállalt üzemidő biztosítása. Ebben a részben áttekintjük, hogy milyen  SLA vonatkozik az egyes adatbázis-üzemeltetési megoldásokra.

A **SQL Database**, a Microsoft biztosít garantált 99,99 %. További információk: [SLA a következőhöz: SQL-adatbázis](https://azure.microsoft.com/support/legal/sla/sql-database/).

Az **Azure virtuális gépeken futó SQL Server** esetében a Microsoft 99,95%-os SLA-elérhetőséget garantál, amely kizárólag a virtuális gépre vonatkozik. Ez az SLA nem érvényes a virtuális gépen futó folyamatokra (például az SQL Serverre), ezenfelül legalább két, rendelkezésre állási csoportba rendezett virtuálisgép-példány futtatása is szükséges hozzá. A legfrissebb információkért lásd: [SLA a következőhöz: Virtuális gépek](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Az adatbázis magas rendelkezésre ÁLLÁS virtuális gépeken belül, konfigurálnia kell a támogatott magas rendelkezésre állású egyikét az SQL Server, mint például [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). A támogatott magas rendelkezésre állási lehetőségek valamelyikének használata nem biztosít további SLA-t, de lehetővé teszi az adatbázis 99,99% fölötti rendelkezésre állásának elérését.

### <a name="market"></a>Az Azure-bA áthelyezéséhez szükséges idő

**Az SQL Database önálló adatbázisok és rugalmas készletek** fejlesztői hatékonyságot és gyors-piacra új megoldások különösen fontos a megfelelő megoldás az felhőbeli környezethez tervezett alkalmazások esetén. A DBA-hoz hasonló programozási funkciói révén tökéletes választás a felhőben dolgozó tervezők és fejlesztők számára, mivel csökkenti az alkalmazás mögötti operációs rendszer és adatbázis felügyeletére fordítandó időt.

**Az SQL Database felügyelt példány** jelentősen leegyszerűsíti a meglévő alkalmazások az Azure SQL Database, lehetővé téve a migrált adatbázis alkalmazások az Azure-ban gyorsan piacra használata az áttelepítés.

**Azure virtuális gépeken futó SQL Server** tökéletes megoldás, ha meglévő vagy új alkalmazásainak nagy méretű adatbázisokhoz szükséges, vagy az SQL Server vagy Windows/Linux, az összes funkciójához hozzáférést, és el szeretné kerülni az idő és új beszerzésének költségek a helyszíni hardver. Az is jó megoldás, ha meg szeretné áttelepíteni meglévő helyszíni alkalmazásokat és adatbázisokat az Azure-ba,-van – az esetekben, ahol az Azure SQL Database felügyelt példány nem jó megoldás. Mivel nem kell a bemutató, alkalmazási és adatréteg módosítására, időt és pénzt takarít költségvetés a meglévő megoldást kell újratervezni a. Ehelyett termékeinek az Azure-ra történő áttelepítésére, illetve az Azure-platformon való működéshez esetleg szükséges teljesítményoptimalizálásra összpontosíthat. További információk: [Performance Best Practices for SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md) (Teljesítményre vonatkozó ajánlott eljárások az Azure Virtual Machines szolgáltatásban futtatott SQL Server esetében).

## <a name="next-steps"></a>További lépések

- Tekintse meg [az első Azure SQL-adatbázissal](sql-database-single-database-get-started.md) foglalkozó témakört, hogy megismerkedhessen az SQL Database használatának első lépéseivel.
- [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).
- Az Azure virtuális gépeken futó SQL Server használatának megkezdésével kapcsolatban lásd: [Provision a SQL Server virtual machine in Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) (SQL Servert futtató virtuális gép létrehozása az Azure-ban).
