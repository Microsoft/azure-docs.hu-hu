---
title: Mi az az Azure SQL?
description: 'Megismeri a szolgáltatás-Azure SQL különböző lehetőségeit: Azure SQL Database, Azure SQL Managed Instance és SQL Server Azure-beli virtuális gépeken.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server, felhőalapú SQL Server, PaaS-adatbázis, felhőalapú felhőalapú SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 394b3390386c60e2a64f52dd944dfcdb0d33951e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727023"
---
# <a name="what-is-azure-sql"></a>Mi az az Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL egy felügyelt, biztonságos és intelligens termékekből áll, amelyek az Azure SQL Server adatbázismotorját használják.

- **Azure SQL Database:** Modern felhőalkalmazások támogatása egy intelligens, felügyelt adatbázis-szolgáltatáson, amely kiszolgáló nélküli számítást is tartalmaz. 
- **Azure SQL Managed Instance:** Meglévő SQL Server-alkalmazásait egy intelligens, teljes körűen felügyelt szolgáltatáspéldánysal modernizálhatja, amely csaknem 100%-os funkcióparitást biztosít az SQL Server adatbázismotorral. A legjobb választás a legtöbb felhőbe való migráláshoz.
- **SQL Server** Azure-beli virtuális gépeken: Az SQL Server számítási feladatait könnyedén átemlheti, és 100%-os SQL Server kompatibilitást és operációsrendszer-szintű hozzáférést tart fenn. 
 
Azure SQL a jól ismert SQL Server motorra épül, így az alkalmazásokat könnyedén át lehet milaniálni, és továbbra is használhatja a már ismert eszközöket, nyelveket és erőforrásokat. Készségeit és tapasztalatát át kell ruházni a felhőbe, így még többet tud tenni a már eddig is tapasztaltak alapján. 

Megtudhatja, hogyan illeszkednek az egyes termékek a Microsoft Azure SQL adatplatformba, hogy azok megfelelnek az Ön üzleti igényeinek. Akár a költségmegtakarítást, akár a minimális adminisztrációt rangsoroljuk, ez a cikk segít eldönteni, hogy melyik megközelítés legyen az Ön számára legfontosabb üzleti követelményekhez leginkább megfelelő.

Ha még csak most Azure SQL, tekintse meg  a Részletes videósorozat Azure SQL Azure SQL [videóját:](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Hogyan te tejük Azure SQL a jobbat? [Vegye fel a felmérést.](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456)

## <a name="overview"></a>Áttekintés

Napjaink adatvezérelt világában a digitális átalakulás egyre inkább azon múlik, hogy képesek vagyunk-e nagy mennyiségű adatot kezelni és kihasználni a benne rejlő lehetőségeket. Napjaink adat tulajdonai azonban egyre összetettebbek, és az adatok a helyszínen, a felhőben vagy a hálózat peremén vannak üzemeltetve. Az intelligens és magával ragadó alkalmazásokat fejlesztő fejlesztők olyan korlátozások korlátozhatják magukat, amelyek végső soron hatással lehetnek a felhasználói élményre. Az inkompatibilis platformokból, a nem megfelelő adatbiztonságból, az elégtelen erőforrásokból és az ár-teljesítmény akadályaiból eredő korlátozások bonyolultságot eredményeznek, ami akadályozhatja az alkalmazások modernizálását és fejlesztését. 

Az Azure-platformon futó és a helyszíni SQL Server-adatbázisok közötti összehasonlítás első lépése, hogy leszögezzük: semmiről nem kell lemondania. A Microsoft adatplatformja SQL Server technológiát, és elérhetővé teszi a fizikai helyszíni gépeken, magánfelhő-környezetekben, külső fél által üzemeltetett magánfelhő-környezetekben és a nyilvános felhőben. 


### <a name="fully-managed-and-always-up-to-date"></a>Teljes körűen felügyelt és mindig naprakész 

Több időt kell töltenie az innovációval, és kevesebb időt kell töltenie az adatbázisok javításával, frissítésével és biztonsági mentéseivel. Az Azure az egyetlen olyan felhő, amely örökzöld SQL-t alkalmaz, amely automatikusan alkalmazza a legújabb frissítéseket és javításokat, hogy az adatbázisok mindig naprakészek, és kiküszöbölhető legyen a támogatás végéhez szükséges probléma. Még az olyan összetett feladatok is automatizálva vannak, mint a teljesítmény-finomhangolás, a magas rendelkezésre állás, a vészhelyreállítás és a biztonsági mentések, így az alkalmazásokra összpontosíthat.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Adatok védelme beépített intelligens biztonsággal 

Az Azure folyamatosan figyeli a fenyegetéseket az adatokban. A Azure SQL a következőt tudja:

- A potenciális fenyegetések valós idejű szervizelése intelligens, fejlett [fenyegetésészleléssel](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) és proaktív sebezhetőségi felmérési riasztásokkal. 
- Iparágvezető, többrétegű védelmet kap [](https://azure.microsoft.com/overview/security/) beépített biztonsági vezérlőkkel, például T-SQL-sel, hitelesítéssel, hálózatkezeléssel és kulcskezeléssel. 
- Használja ki bármely felhőalapú [adatbázis-szolgáltatás](https://azure.microsoft.com/overview/trusted-cloud/compliance/) legátfogóbb megfelelőségi lefedettségét. 


### <a name="business-motivations"></a>Üzleti motivációk

A különböző adatajánlatok közötti választást számos tényező befolyásolhatja:

- [Költség:](#cost)A PaaS és az IaaS lehetőség is tartalmaz alapárat, amely a mögöttes infrastruktúrát és a licencelést fedezi. Az IaaS-lehetőséggel azonban további időt és erőforrásokat kell fektetni az adatbázis kezeléséhez, míg a PaaS-ban ezeket a felügyeleti funkciókat az ár tartalmazza. Az IaaS lehetővé teszi, hogy leállítsa az erőforrásokat, miközben nem használja őket a költségek csökkentésére, míg a PaaS mindig fut, kivéve, ha szükség esetén elejti és újra létrehozza az erőforrásokat.
- [Felügyelet:](#administration)A PaaS-lehetőségek csökkentik azt az időt, amelybe be kell fektetni az adatbázis felügyeletét. Ugyanakkor korlátozza az egyéni felügyeleti feladatok és a futtatható parancsfájlok körét is. A CLR például nem támogatott a SQL Database, de a clr-példányok SQL Managed Instance. Emellett a PaaS egyik üzembe helyezési lehetőség sem támogatja a nyomkövetési jelzők használatát.
- [Szolgáltatásiszint-szerződés:](#service-level-agreement-sla)Az IaaS és a PaaS is magas, iparági szabványnak megfelelő SLA-t biztosít. A PaaS-lehetőség 99,99%-os SLA-t garantál, míg az IaaS 99,95%-os SLA-t garantál az infrastruktúrához, ami azt jelenti, hogy további mechanizmusokat kell megvalósítania az adatbázisok rendelkezésre állásának biztosításához. 99,99%-os SLA-t kaphat egy további SQL virtuális gép létrehozásával és az SQL Server Always On rendelkezésre állási csoport magas rendelkezésre állási megoldásának megvalósításával. 
- [Az Azure-ba](#market)való áttérés ideje: SQL Server Azure-beli virtuális gépen a migrálás pontosan megegyezik a környezetével, így a helyszíni virtuális gépről az Azure-beli virtuális gépre való migrálás nem más, mint az adatbázisok egyik helyszíni kiszolgálóról a másikra való áthelyezése. SQL Managed Instance egyszerű migrálást is lehetővé tesz; előfordulhat azonban, hogy néhány módosítást a migrálás előtt kell alkalmaznia. 


## <a name="service-comparison"></a>Szolgáltatások összehasonlítása

   ![Felhőalapú SQL Server lehetőségek: SQL Server IaaS- vagy SaaS-SQL Database a felhőben.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Ahogy az az ábrán is látható, az egyes szolgáltatásajánlatokat az infrastruktúrával kapcsolatos adminisztrációs szint és a költséghatékonyság mértéke jellemezheti.

Az Azure-ban a számítási SQL Server üzemeltetett szolgáltatásként[(PaaS)](https://azure.microsoft.com/overview/what-is-paas/)vagy üzemeltetett infrastruktúraként[(IaaS) futhat.](https://azure.microsoft.com/overview/what-is-iaas/) A PaaS-ban több termékcsomag és szolgáltatási szint is elérhető. A PaaS vagy az IaaS közötti döntéshez szükséges legfontosabb kérdés az, hogy szeretné-e kezelni az adatbázist, javításokat alkalmazni és biztonsági másolatokat készíteni, vagy delegálni szeretné ezeket a műveleteket az Azure-ba?

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md) az Azure-ban üzemeltetett relációsadatbázis-szolgáltatás (DBaaS), amely a szolgáltatásként üzemeltetett platform *(PaaS)* iparági kategóriájába tartozik. 
- A legjobb választás olyan modern felhőalapú alkalmazásokhoz, amelyek a legújabb stabil SQL Server funkciókat szeretnék használni, és a fejlesztés és a marketing terén időkorlátokkal kell őket használni. 
- Teljes körűen felügyelt SQL Server adatbázismotor, amely a legújabb stabil Enterprise kiadás adatbázismotor SQL Server. SQL Database két telepítési lehetőség áll rendelkezésre, amelyek a Microsoft tulajdonában lévő, üzemeltetett és karbantartott szabványos hardverre és szoftverekre épülnek. 

A SQL Server olyan beépített funkciókat és funkciókat használhat, amelyek részletes konfigurációt igényelnek (akár a helyszínen, akár egy Azure-beli virtuális gépen). Az SQL Database használatalapú fizetéssel működik, emellett a teljesítményigény növekedése esetére lehetőség van vertikális vagy horizontális felskálázásra is. SQL Database olyan további funkciókkal is rendelkezik, amelyek a SQL Server nem érhetők el, például a beépített magas rendelkezésre állás, intelligencia és felügyelet.


Azure SQL Database üzembe helyezési lehetőségek a következők:
  - Egyetlen [*adatbázisként,*](database/single-database-overview.md) amely saját erőforráskészletét kezeli egy logikai [SQL-kiszolgálón keresztül.](database/logical-servers.md) Az önálló adatbázisok hasonlóak [a](/sql/relational-databases/databases/contained-databases) SQL Server. Ez a lehetőség új, felhőben született alkalmazások modern alkalmazásfejlesztésére van optimalizálva. [Rendelkezésre állnak a](database/service-tier-hyperscale.md) [skálázási és kiszolgáló nélküli](database/serverless-tier-overview.md) lehetőségek.
  - Egy [*rugalmas készlet,*](database/elastic-pool-overview.md)amely egy logikai SQL-kiszolgálón keresztül felügyelt, megosztott erőforráskészletet futtató adatbázisok [gyűjteménye.](database/logical-servers.md) Az egyszeres adatbázisok rugalmas készletbe és rugalmas készletből is áthelyezk. Ez a lehetőség a több-bérlős SaaS-alkalmazásmintát használó új, felhőben született alkalmazások modern alkalmazásfejlesztésére van optimalizálva. A rugalmas készletek költséghatékony megoldást biztosítanak több, változó használati mintával rendelkező adatbázis teljesítményének kezeléséhez.

### <a name="azure-sql-managed-instance"></a>Felügyelt Azure SQL-példány

[Azure SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) a szolgáltatásként használt platform *(PaaS)* iparági kategóriájába tartozik, és a legjobb választás a legtöbb felhőbe történő migráláshoz. SQL Managed Instance olyan rendszer- és felhasználói adatbázisok gyűjteménye, amelyek az átemlhető erőforrások közös készletével vannak használatban.  
- Olyan új vagy meglévő helyszíni alkalmazásokhoz ajánlott, amelyek a legújabb stabil SQL Server-funkciókat szeretnék használni, és minimális módosításokkal migrálják őket a felhőbe. A SQL Managed Instance egy példánya hasonló a Microsoft SQL Server [](/sql/database-engine/sql-server-database-engine-overview) adatbázismotor egy példányához, amely megosztott erőforrásokat kínál az adatbázisokhoz és további példányhatókörű funkciókhoz. 
- SQL Managed Instance támogatja az adatbázisok migrálását a helyszínről minimális adatbázis-módosítás nélkül. Ez a lehetőség a virtuális gépek összes PaaS Azure SQL Database előnyét biztosítja, de olyan képességeket ad hozzá, amelyek korábban csak a SQL Server voltak elérhetők. Ez magában foglalja a natív virtuális hálózatot, és közel 100%-os kompatibilitást biztosít a helyszíni SQL Server. Az sql SQL Managed Instance példányok teljes körű hozzáférést SQL Server és funkciókompatibilitást biztosítanak az SQL-kiszolgálók Azure-ba való áttelepítéséhez.

### <a name="sql-server-on-azure-vm"></a>Azure-beli virtuális gépeken futtatott SQL Server

[SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) Azure-beli virtuális gépen az infrastruktúra(IaaS) iparági kategóriába tartozik, és lehetővé teszi a SQL Server futtatását egy teljes körűen felügyelt virtuális gépen (VM) az *Azure-ban.* 
- SQL Server a felhőben telepített és üzemeltetett virtuális gépek Az Azure-on futó Windows Server vagy Linux rendszerű virtuális gépek, más néven szolgáltatott infrastruktúra (IaaS) futnak. Az SQL virtuális gépek jó választásnak hatók a helyszíni adatbázisok és SQL Server adatbázisok és alkalmazások adatbázis-módosítás nélküli áttelepítésére. Az IaaS virtuális gépeken SQL Server verziók és kiadások mind elérhetők a telepítéshez. 
- Az operációsrendszer-szintű hozzáférést igénylő migrálások és alkalmazások esetén a legjobb. Az Azure-beli SQL-alapú virtuális gépek átváltásra készek az olyan meglévő alkalmazásokhoz, amelyek gyors migrálást igényelnek a felhőbe, minimális módosításokkal vagy módosítás nélkül. Az SQL virtuális gépek teljes körű felügyeleti vezérlést nyújtanak a SQL Server és az alapul szolgáló operációs rendszer felett az Azure-ba való migráláshoz. 
- A legfontosabb különbség a SQL Database és SQL Managed Instance, hogy SQL Server Azure Virtual Machines az adatbázismotor teljes körű vezérlését teszi lehetővé. Kiválaszthatja, hogy mikor kezdje meg a karbantartást/javítást, módosítsa a helyreállítási modellt egyszerű vagy tömegesen naplózottra, szükség esetén szüneteltetheti vagy elindíthatja a szolgáltatást, és teljes mértékben testreszabhatja a SQL Server adatbázismotort. Ezzel a további vezérléssel további felelősség jár a virtuális gép kezelésével.
- Ha gyorsan kell elvégezni a fejlesztést és a tesztelést, és nem szeretne helyszíni nem üzemi SQL Server-hardvert vásárolni. Az SQL virtuális gépek a Microsoft tulajdonában álló, üzemeltetett és karbantartott szabványos hardveren is futnak. SQL virtuális gépek használata esetén használat alapján fizethet egy SQL Server-rendszerképben már szereplő SQL Server-licencért, vagy egyszerűen használhat egy meglévő licencet. Szükség szerint le is állíthatja vagy folytathatja a virtuális gépet. 
- A meglévő alkalmazások Azure-ba való áttelepítésére vagy a meglévő helyszíni alkalmazások felhőbe való kiterjesztésére optimalizálva hibrid környezetben. Emellett a virtuális gépen futó SQL Servert használhatja hagyományos SQL Server-alkalmazások fejlesztésére és tesztelésére is. Az SQL-alapú virtuális gépekkel teljes körű rendszergazdai jogosultságokkal rendelkezik egy dedikált SQL Server-példányhoz és egy felhőalapú virtuális géphez. Ez tökéletes választás, ha a vállalatnál adva vannak a virtuális gépek fenntartásához szükséges számítástechnikai erőforrások. E lehetőségeknek köszönhetően a legapróbb részletekig testre szabhatja a rendszert, úgy, hogy az a leginkább megfeleljen az alkalmazás teljesítmény- és rendelkezésre állási követelményeinek.


### <a name="comparison-table"></a>Összehasonlító táblázat

A további különbségeket az alábbi táblázat sorolja fel, de a SQL Database és a SQL Managed Instance is úgy van optimalizálva, hogy az általános felügyeleti költségeket a lehető legalacsonyabbra csökkentse számos adatbázis kiépítése és *kezelése érdekében.* A folyamatos adminisztrációs költségek csökkennek, mivel nem kell virtuális gépeket, operációs rendszereket vagy adatbázisszoftvereket kezelnie. Nem kell foglalkoznia a frissítések, a magas rendelkezésre állás és a [biztonsági mentések](database/automated-backups-overview.md) kezelésével sem. 

Általánosságban elmondható, SQL Database és SQL Managed Instance jelentősen növelhetik az egyetlen itatikus vagy fejlesztési erőforrás által kezelt adatbázisok számát. [A rugalmas készletek](database/elastic-pool-overview.md) támogatják a több-bérlős SaaS-alkalmazásarchitektúrákat is, beleértve a bérlők elkülönítését és az erőforrások adatbázisok közötti megosztásával a költségek csökkentésére való skálázás képességét. [SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) támogatja a példányra irányuló funkciókat, lehetővé téve a meglévő alkalmazások egyszerű migrálását, valamint az erőforrások adatbázisok közötti megosztását. Míg SQL Server [](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) Azure-beli virtuális gépeken az adatbázis-kezelők a legjobban hasonlítanak a már ismert helyszíni környezethez. 


| Azure SQL Database | Felügyelt Azure SQL-példány | Azure-beli virtuális gépeken futtatott SQL Server |
| :--- | :--- | :--- |
|Támogatja a legtöbb helyszíni adatbázisszintű képességet. A leggyakrabban használt SQL Server funkciók érhetők el.<br/>99,995%-os rendelkezésre állás garantálva.<br/>Beépített biztonsági mentések, javítások, helyreállítás.<br/>Az adatbázismotor legújabb stabil verziója.<br/>Lehetőség a szükséges erőforrások (CPU/tárterület) egyedi adatbázisokhoz való hozzárendelésére.<br/>Beépített fejlett intelligencia és biztonság.<br/>Erőforrások online módosítása (CPU/tárterület).| Szinte minden helyszíni példány- és adatbázisszintű képességet támogat. Nagy kompatibilitás a SQL Server.<br/>99,99%-os rendelkezésre állás garantálva.<br/>Beépített biztonsági mentések, javítások, helyreállítás.<br/>Az adatbázismotor legújabb stabil verziója.<br/>Egyszerű migrálás a SQL Server.<br/>Magánhálózati IP-cím az Azure Virtual Network.<br/>Beépített fejlett intelligencia és biztonság.<br/>Erőforrások online módosítása (CPU/tárterület).| Teljes mértékben Ön irányíthatja a SQL Server motort. Az összes helyszíni képességet támogatja.<br/>Akár 99,99%-os rendelkezésre állás.<br/>Teljes paritás a helyszíni alkalmazás megfelelő SQL Server.<br/>Rögzített, jól ismert adatbázismotor-verzió.<br/>Egyszerű migrálás a SQL Server.<br/>Magánhálózati IP-cím az Azure-Virtual Network.<br/>Telepíthet alkalmazásokat vagy szolgáltatásokat azon a gazdagépen, SQL Server a gazdagépen.|
|A SQL Server migrálás kihívást jelenthet.<br/>Egyes SQL Server funkciók nem érhetők el.<br/>Nincs garantáltan pontos karbantartási idő (de majdnem átlátszó).<br/>A SQL Server verzióval való kompatibilitás csak adatbázis-kompatibilitási szintek használatával érhető el.<br/>Magánhálózati IP-címek támogatása a [Azure Private Link.](database/private-endpoint-overview.md)|Továbbra is van néhány minimális számú SQL Server, amelyek nem érhetők el.<br/>Nincs garantáltan pontos karbantartási idő (de majdnem átlátszó).<br/>A SQL Server verzióval való kompatibilitás csak adatbázis-kompatibilitási szintek használatával érhető el.|Önnek kell kezelnie a biztonsági másolatokat és a javításokat.<br>Saját megoldásokat kell High-Availability implementálja.<br/>Az erőforrások (CPU/tárterület) módosítása során állásidő áll le|
| Legfeljebb 100 TB-os adatbázisok. | Legfeljebb 8 TB. | SQL Server legfeljebb 256 TB tárhellyel rendelkezik. A példányok annyi adatbázist képesek támogatni, amennyire szükség van. |
| A helyszíni alkalmazások hozzáférhetnek a helyszíni Azure SQL Database. | [Natív virtuális hálózat implementációja](managed-instance/vnet-existing-add-subnet.md) és kapcsolódás a helyszíni környezethez az Azure Express Route vagy a VPN Gateway. | Az SQL-alapú virtuális gépekkel részben a felhőben, részben a helyszínen futó alkalmazásokat futtathat. Például az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) segítségével kiterjesztheti helyszíni hálózatát és Active Directory-tartományát a felhőre. További információ a hibrid felhőalapú megoldásokról: Helyszíni adatmegoldások kiterjesztése [a felhőbe.](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud) |


## <a name="cost"></a>Költségek

Akár egy olyan startupról van szó, amely pénzre van cserélve, vagy egy szoros költségvetési korlátok között működő cég csapata, gyakran a korlátozott támogatás az elsődleges hajtórugó az adatbázisok működtetésének eldöntésében. Ebben a szakaszban megismeri az Azure számlázási és licencelési alapjait, amelyek a Azure SQL-családhoz kapcsolódnak.  Megismerheti az alkalmazás összköltségének kiszámításának módját is.

### <a name="billing-and-licensing-basics"></a>A számlázás és a licencelés alapjai

Jelenleg az **SQL Database** és a **SQL Managed Instance** is elérhető szolgáltatásként, és számos különböző szolgáltatási szinten és különböző erőforrás-árakon érhetők el, amelyek számlázása óradíj alapján történik a választott szolgáltatási szint és számítási méret alapján. Az aktuálisan támogatott szolgáltatási szintekkel, számítási méretekkel és tárolási mennyiségekkel kapcsolatos legfrissebb információkért lásd: [DTU-alapú](database/service-tiers-dtu.md) vásárlási modell SQL Database-hez és [virtuálismag-alapú](database/service-tiers-vcore.md)vásárlási modellhez mind a SQL Database, mind a SQL Managed Instance.

- Az SQL Database esetében az alapszintű csomag esetében havi 5 USD/hónaptól kezdve az igényeinek megfelelő szolgáltatási szintet [](database/elastic-pool-overview.md) választhat, és rugalmas készleteket hozhat létre az erőforrások adatbázisok közötti megosztásához a költségek csökkentése és a használati csúcsok kielégítése érdekében.
- A SQL Managed Instance a saját licencét is használhatja. A saját licencelésről további információt az [Azure-beli licenchordozhatóság Frissítési Garancia keretében](https://azure.microsoft.com/pricing/license-mobility/) vagy [](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) az Azure Hybrid Benefit-kalkulátor használatával 40%-os megtakarítást **engedélyező oldalon.**

Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik. A szolgáltatási szinteket és a számítási méreteket dinamikusan módosíthatja az alkalmazás különböző átviteli sebességi igényeinek megfelelően.

A **SQL Database** és **SQL Managed Instance** az Azure automatikusan konfigurálja, javítja és frissíti az adatbázisszoftvert, ami csökkenti az adminisztrációs költségeket. Ezenfelül a [beépített biztonsági mentési](database/automated-backups-overview.md) funkciókkal is jelentős költségmegtakarítást érhet el, különösen, ha nagyszámú adatbázist használ.

Az **Azure-beli** virtuális gépeken az SQL-t használva a platform által biztosított SQL Server-rendszerképeket (amelyek licencet is tartalmaznak) vagy saját SQL Server használhat. Az összes támogatott SQL Server-verzió (2008R2, 2012, 2014, 2016, 2017, 2019) és kiadás (Developer, Express, Web, Standard, Enterprise) elérhető. Emellett a rendszerképek saját licenccel használható (BYOL) verziói is elérhetők. Az Azure által biztosított rendszerképek használata esetén az üzemeltetési költségek a virtuális gép méretétől, valamint a használni kívánt SQL Server-verziótól függnek. A virtuális gép méretétől és SQL Server kiadásától függetlenül percenkénti licencelési költségeket kell fizetnie SQL Server és a Windows- vagy Linux-kiszolgálóért, valamint a virtuálisgép-lemezek Azure Storage-költségeit. A percalapú számlázásnak köszönhetően igényeinek megfelelő ideig használhatja az SQL Servert, anélkül, hogy további SQL Server-licenceket kellene vásárolnia. Ha saját licencét használja SQL Server Azure-ba, csak a kiszolgálói és tárolási költségekért kell fizetnie. A saját licenc használatával kapcsolatos további információkért lásd: [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) (Licenchordozás az Azure Frissítési garancia programja keretében). Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik.

#### <a name="calculating-the-total-application-cost"></a>Az alkalmazás összköltségének kiszámítása

Amikor elkezdi használni a felhőplatformot, az alkalmazás futtatásának költsége magában foglalja az új fejlesztési és a folyamatos adminisztrációs költségeket, valamint a nyilvános felhőplatform szolgáltatási költségeit.

Az árakkal kapcsolatos további információkért lásd a következő erőforrásokat:

- [SQL Database & SQL Managed Instance díjszabás](https://azure.microsoft.com/pricing/details/sql-database/)
- [Virtuális gépek díjszabása az](https://azure.microsoft.com/pricing/details/virtual-machines/) [SQL-hez és](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) a [Windowshoz](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Felügyelet

Számos vállalat számára a felhőszolgáltatásokra történő átállást az összetett adminisztrációs feladatok kiszervezése ösztönzi, és nem csupán a költségszempontok. Az IaaS és a PaaS segítségével az Azure felügyeli a mögöttes infrastruktúrát, és automatikusan replikál minden adatot a vészhelyreállítás érdekében, konfigurálja és frissíti az adatbázisszoftvert, kezeli a terheléselosztást, és transzparens feladatátvételt tesz lehetővé, ha kiszolgálóhiba történik egy adatközponton belül.

- A **SQL Database** és **SQL Managed Instance** továbbra is felügyelheti az adatbázist, de többé nem kell felügyelni az adatbázismotort, az operációs rendszert vagy a hardvert. Ön felelős például a következő területekért: adatbázisok és bejelentkezési adatok, indexek és lekérdezések hangolása, naplózás és biztonság. Emellett a magas rendelkezésre állás másik adatközpontba való konfigurálása minimális konfigurációt és felügyeletet igényel.
- Az **Azure-beli virtuális gépen** található SQL-sel teljes körűen szabályozhatja az operációs rendszert és a SQL Server konfigurációját. A virtuális gépeken Ön döntheti el, hogy mikor frissíti az operációs rendszert és az adatbázisszoftvert, és mikor telepít további szoftvereket, például víruskereső szoftvereket. Egyes automatizált szolgáltatások jelentősen leegyszerűsítik a frissítést, a biztonsági mentések készítését és a magas rendelkezésre állást. Ezenfelül meghatározhatja a virtuális gép méretét, a lemezek számát, valamint a tárhely konfigurációját is. Az Azure-ban igény szerint módosítható a virtuális gépek mérete. További információk: [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/sizes.md) (Virtuális gépek és felhőszolgáltatások mérete az Azure-ban).

## <a name="service-level-agreement-sla"></a>Szolgáltatói szerződés (SLA)

Számos it-részleg számára a szolgáltatásiszint-szerződések (SLA-k) időben való teljesítésének elsődleges prioritása. Ebben a részben áttekintjük, hogy milyen  SLA vonatkozik az egyes adatbázis-üzemeltetési megoldásokra.

A Microsoft **Azure SQL Database** és **Azure SQL Managed Instance** is 99,99%-os rendelkezésre állási SLA-t biztosít. A legfrissebb információkért lásd: [Szolgáltatásiszint-szerződés.](https://azure.microsoft.com/support/legal/sla/sql-database/)

Az **Azure-beli virtuális** gépen lévő SQL esetén a Microsoft 99,95%-os SLA-t biztosít, amely csak a virtuális gépre vonatkozik. Ez az SLA nem érvényes a virtuális gépen futó folyamatokra (például az SQL Serverre), ezenfelül legalább két, rendelkezésre állási csoportba rendezett virtuálisgép-példány futtatása is szükséges hozzá. A legfrissebb információkért lásd: [SLA a következőhöz: Virtuális gépek](https://azure.microsoft.com/support/legal/sla/virtual-machines/). A virtuális gépeken belüli adatbázisok magas rendelkezésre állása (HA) esetében konfigurálnia kell az egyik támogatott magas rendelkezésre állási lehetőséget a SQL Server-ban, például az Always On rendelkezésre állási [csoportokat.](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) A támogatott magas rendelkezésre állási lehetőségek valamelyikének használata nem biztosít további SLA-t, de lehetővé teszi az adatbázis 99,99% fölötti rendelkezésre állásának elérését.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Az Azure-ba való áthelyezés ideje

**Azure SQL Database** a megfelelő megoldás a felhőben tervezett alkalmazásokhoz, ha kritikus fontosságú a fejlesztői hatékonyság és az új megoldások gyors piacra hozása. A DBA-hoz hasonló programozási funkciói révén tökéletes választás a felhőben dolgozó tervezők és fejlesztők számára, mivel csökkenti az alkalmazás mögötti operációs rendszer és adatbázis felügyeletére fordítandó időt.

**Azure SQL Managed Instance** jelentősen leegyszerűsíti a meglévő alkalmazások Azure-ba való migrálását, így a migrált adatbázis-alkalmazásokat gyorsan piacra lehet hozni az Azure-ban.

**Az** Azure-beli virtuális gépen az SQL tökéletes megoldás, ha meglévő vagy új alkalmazásai nagy méretű adatbázisokat igényelnek, vagy hozzáférést igényelnek az SQL Server vagy a Windows/Linux összes szolgáltatásához, és szeretné elkerülni az új helyszíni hardver beszerzésének idejét és költségeket. Akkor is jól használható, ha meglévő helyszíni alkalmazásokat és adatbázisokat szeretne az Azure-ba miolni a jelenlegi útjára – olyan esetekben, amikor SQL Database vagy SQL Managed Instance nem megfelelő. Mivel nem kell módosítania a megjelenítési, alkalmazás- és adatrétegeket, időt és költségvetést takaríthat meg a meglévő megoldás újraépítéséhez. Ehelyett termékeinek az Azure-ra történő áttelepítésére, illetve az Azure-platformon való működéshez esetleg szükséges teljesítményoptimalizálásra összpontosíthat. További információk: [Performance Best Practices for SQL Server on Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md) (Teljesítményre vonatkozó ajánlott eljárások az Azure Virtual Machines szolgáltatásban futtatott SQL Server esetében).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Következő lépések

- Tekintse meg [az első Azure SQL-adatbázissal](database/single-database-create-quickstart.md) foglalkozó témakört, hogy megismerkedhessen az SQL Database használatának első lépéseivel.
- Az [első Azure SQL Managed Instance](managed-instance/instance-create-quickstart.md) a SQL Managed Instance. 
- [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).
- Az Azure virtuális gépeken futó SQL Server használatának megkezdésével kapcsolatban lásd: [Provision a SQL Server virtual machine in Azure](virtual-machines/windows/create-sql-vm-portal.md) (SQL Servert futtató virtuális gép létrehozása az Azure-ban).
- [Azonosítsa a SQL Database adatbázishoz SQL Managed Instance](/sql/dma/dma-sku-recommend-sql-db/)megfelelő termékváltozatot vagy termékváltozatot.
