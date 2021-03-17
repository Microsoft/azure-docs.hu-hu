---
title: Mi az az Azure SQL?
description: 'Ismerje meg az Azure SQL-termékcsalád szolgáltatásainak különböző lehetőségeit: Azure SQL Database, az Azure SQL felügyelt példányát, valamint az Azure-beli virtuális gépen SQL Server.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server felhő, SQL Server a felhőben, a Pásti-adatbázis, a Cloud SQL Server, a DBaaS, a IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 6961c0ffc0d5b9c6559d20b00bfb5510f7247adb
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103600981"
---
# <a name="what-is-azure-sql"></a>Mi az az Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Az Azure SQL olyan felügyelt, biztonságos és intelligens termékekből álló család, amelyek az Azure-felhőben a SQL Server adatbázismotor használatát használják.

- **Azure SQL Database**: támogatja a modern felhőalapú alkalmazásokat egy intelligens, felügyelt adatbázis-szolgáltatásban, amely kiszolgáló nélküli számítást is tartalmaz. 
- **Felügyelt Azure SQL-példány**: a meglévő SQL Server-alkalmazások nagy mértékben modernizálása egy intelligens, teljes körűen felügyelt példányként, a SQL Server adatbázismotor csaknem 100%-os szolgáltatási paritásával. A legalkalmasabb a felhőbe való Migrálás során.
- **SQL Server Azure-beli virtuális gépeken**: a SQL Server számítási feladatokat könnyedén, a 100%-os SQL Server kompatibilitás és az operációs rendszer szintű hozzáférés fenntartásával. 
 
Az Azure SQL a jól ismert SQL Server motorra épül, így könnyedén áttelepítheti az alkalmazásokat, és tovább használhatja azokat az eszközöket, nyelveket és erőforrásokat, amelyeket már ismer. Tudását és tapasztalatait a felhőbe helyezheti át, így még többet is megtudhat a már meglévő képességeiről. 

Ismerje meg, hogy az egyes termékek hogyan illeszkednek a Microsoft Azure SQL-adatplatformhoz, hogy megfeleljenek az üzleti igényeinek megfelelő lehetőségnek. Akár a költségmegtakarítást, akár a minimális adminisztrációt rangsorolja, ez a cikk segít eldönteni, hogy melyik megközelítés nyújt segítséget a leginkább fontos üzleti követelmények teljesítéséhez.

Ha még nem ismeri az Azure SQL-t, tekintse meg a *Mi az* az Azure SQL-videó a részletes [Azure SQL-videó sorozat](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Hogyan lehet jobbá tenni az Azure SQL-t? [Vegye fel a kérdőívet](https://aka.ms/AzureSQLSurvey).

## <a name="overview"></a>Áttekintés

A mai adatvezérelt világban a digitális átalakítás egyre nagyobb mértékben függ a nagy mennyiségű adatok kezelésének, valamint a hozzá tartozó potenciál kihasználásának a lehetőségeitől. Napjainkban azonban egyre összetettebbek a helyszíni, a felhőben vagy a hálózat peremén tárolt adatközpontok. A fejlesztők, akik intelligens és önmagára épülő alkalmazásokat fejlesztenek, korlátozni tudják azokat a korlátozásokat, amelyek végső hatással lehetnek a felhasználói élményre. A nem kompatibilis platformoktól, a nem megfelelő adatbiztonságtól, a nem elegendő erőforrástól és az árak teljesítményével kapcsolatos korlátozások olyan összetettséget teremtenek, amely gátolhatja az alkalmazások korszerűsítését és fejlesztését. 

Az Azure-platformon futó és a helyszíni SQL Server-adatbázisok közötti összehasonlítás első lépése, hogy leszögezzük: semmiről nem kell lemondania. A Microsoft adatplatformja SQL Server technológiát használ, és elérhetővé teszi a helyszíni gépek, a privát felhőalapú környezetek, a külső felek által üzemeltetett privát felhőalapú környezetek és a nyilvános felhő között. 


### <a name="fully-managed-and-always-up-to-date"></a>Teljes körűen felügyelt és mindig naprakész 

Több időt kell fordítania az innovációra, és kevesebb időt kell kijavítani, frissíteni és biztonsági mentést készíteni az adatbázisokról. Az Azure az egyetlen felhő és az örökzöld SQL, amely automatikusan alkalmazza a legújabb frissítéseket és javításokat, hogy az adatbázisok mindig naprakészek legyenek, így megszűnik a támogatási szóváltás. Az olyan összetett feladatok, mint például a teljesítmény finomhangolása, a magas rendelkezésre állás, a vész-helyreállítás és a biztonsági másolatok automatizáltak, így az alkalmazásokra koncentrálhat.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Az adatai védelme beépített intelligens biztonsággal 

Az Azure folyamatosan figyeli az adatait a fenyegetések ellen. Az Azure SQL-szel a következőket teheti:

- A potenciális fenyegetések valós idejű szervizelése az intelligens komplex [veszélyforrások észlelésével](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) és a biztonsági rések felmérésével kapcsolatos riasztásokkal. 
- A [beépített biztonsági vezérlőkkel](https://azure.microsoft.com/overview/security/) , többek között a T-SQL, a hitelesítés, a Hálózatkezelés és a kulcskezelő szolgáltatással piacvezető, többrétegű védelmet szerezhet. 
- Használja ki a felhőalapú adatbázis-szolgáltatás legátfogóbb [megfelelőségi](https://azure.microsoft.com/overview/trusted-cloud/compliance/) lefedettségét. 


### <a name="business-motivations"></a>Üzleti motivációk

Több tényező is befolyásolhatja a különböző adatajánlatok közötti választást:

- [Költség](#cost): a Pásti és a IaaS beállítás tartalmazza a mögöttes infrastruktúrát és a licencelést is magában foglaló alapdíjat. A IaaS beállítással azonban további időt és erőforrásokat kell megadnia az adatbázis kezeléséhez, míg a Pástiban a jelen adminisztrációs funkciók bekerülnek a díjszabásba. A IaaS lehetővé teszi az erőforrások leállítását, miközben Ön nem használja őket a díjak csökkentése érdekében, míg a Pásti mindig fut, hacsak nem dobja el és nem hozza létre újra az erőforrásokat, amikor szükség van rájuk.
- [Felügyelet](#administration): a Pásti-beállítások csökkentik az adatbázis felügyeletéhez szükséges időt. Emellett korlátozza az egyéni adminisztrációs feladatok és a végrehajtható vagy futtatható parancsfájlok tartományát is. Például a CLR nem támogatott a SQL Database, de az SQL felügyelt példányainak egy példánya támogatja. Emellett a Pásti-beli üzembe helyezési lehetőségek nem támogatják a nyomkövetési jelzők használatát.
- [Szolgáltatói szerződés](#service-level-agreement-sla): a IaaS és a Péter is magas, iparági szabványoknak megfelelő SLA-t biztosít. A Péter opció garantálja a 99,99%-os SLA-t, míg a IaaS garantálja a 99,95%-os SLA-t az infrastruktúrához, ami azt jelenti, hogy további mechanizmusokra van szükség az adatbázisok rendelkezésre állásának A 99,99%-os SLA-t egy további SQL virtuális gép létrehozásával, valamint a SQL Server always on rendelkezésre állási csoport magas rendelkezésre állási megoldásának megvalósításával érheti el. 
- Az Azure-ba való [áttérés ideje](#market): SQL Server az Azure-beli virtuális gépen a környezet pontos egyezése, így a helyszínről az Azure-ba történő Migrálás nem különbözik az adatbázisok egyik helyszíni kiszolgálóról egy másikba való áthelyezésének. Az SQL felügyelt példánya egyszerű áttelepítést is lehetővé tesz; Előfordulhat azonban, hogy az áttelepítés előtt bizonyos módosításokat kell alkalmaznia. 


## <a name="service-comparison"></a>Szolgáltatások összehasonlítása

   ![Cloud SQL Server options: SQL Server IaaS vagy SaaS SQL Database a felhőben.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Ahogy az ábrán is látható, az egyes szolgáltatási ajánlatokat az infrastruktúra szintjével és a költséghatékonyság mértékével lehet jellemezni.

Az Azure-ban üzemeltetett szolgáltatásként ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)[)](https://azure.microsoft.com/overview/what-is-paas/)futtathatja SQL Server munkaterheléseit. A Pástin belül több termékre vonatkozó lehetőség és szolgáltatási szint is rendelkezésre áll az egyes beállításokon belül. Az a kérdés, hogy meg kell-e kérdezni a Pásti vagy a IaaS közötti döntéskor, hogy szeretné-e felügyelni az adatbázist, a javítások alkalmazásával és a biztonsági mentéseket, vagy szeretné delegálni ezeket a műveleteket az Azure-ba?

### <a name="azure-sql-database"></a>Azure SQL Database

A [Azure SQL Database](database/sql-database-paas-overview.md) egy Azure-ban üzemeltetett, az Azure-ban üzemeltetett, DBaaS-szolgáltatás, amely a *platform-szolgáltatás (Péter)* iparági kategóriába tartozik. 
- A legjobb olyan modern felhőalapú alkalmazásokhoz, amelyek a legújabb stabil SQL Server funkciókat szeretnék használni, és időbeli korlátozásokkal rendelkeznek a fejlesztés és a marketing terén. 
- Teljes körűen felügyelt SQL Server adatbázismotor, amely a SQL Server legújabb stabil Enterprise kiadásán alapul. A SQL Database két üzembe helyezési lehetőséggel rendelkezik, amelyek a Microsoft által birtokolt, üzemeltetett és karbantartott szabványos hardverekre és szoftverekre épülnek. 

A SQL Server használatával olyan beépített funkciókat és funkciókat használhat, amelyek kiterjedt konfigurációt igényelnek (akár helyszíni, akár egy Azure-beli virtuális gépen). Az SQL Database használatalapú fizetéssel működik, emellett a teljesítményigény növekedése esetére lehetőség van vertikális vagy horizontális felskálázásra is. SQL Database olyan további funkciókat is tartalmaz, amelyek nem érhetők el SQL Serverban, például beépített magas rendelkezésre állás, intelligencia és felügyelet.


Azure SQL Database a következő üzembe helyezési lehetőségeket kínálja:
  - [*Önálló adatbázis*](database/single-database-overview.md) , amely egy [logikai SQL Serveren](database/logical-servers.md)keresztül felügyelt erőforrás-készlettel rendelkezik. Az önálló adatbázisok a SQL Serverban [lévő tárolt adatbázisokhoz](/sql/relational-databases/databases/contained-databases) hasonlítanak. Ez a beállítás az új felhőalapú alkalmazások modern alkalmazás-fejlesztésére van optimalizálva. [Nagy kapacitású](database/service-tier-hyperscale.md) és [kiszolgáló](database/serverless-tier-overview.md) nélküli beállítások érhetők el.
  - [*Rugalmas készlet*](database/elastic-pool-overview.md), amely egy [logikai SQL Serveren](database/logical-servers.md)keresztül felügyelt erőforrások megosztott készletével rendelkező adatbázisok gyűjteménye. Az önálló adatbázisok egy rugalmas készletbe helyezhetők és kivehetők. Ez a beállítás a több-bérlős SaaS-alkalmazás mintáját használó új felhőalapú alkalmazások modern alkalmazás-fejlesztésére van optimalizálva. A rugalmas készletek költséghatékony megoldást biztosítanak több, változó használati mintákkal rendelkező adatbázis teljesítményének kezelésére.

### <a name="azure-sql-managed-instance"></a>Felügyelt Azure SQL-példány

Az [Azure SQL felügyelt példánya](managed-instance/sql-managed-instance-paas-overview.md) a platform- *szolgáltatás (*"a") iparági kategóriába tartozik, és a leginkább a felhőbe való Migrálás terén ajánlott. A felügyelt SQL-példányok olyan rendszer-és felhasználói adatbázisok gyűjteményei, amelyek egy közös erőforrás-készlettel rendelkeznek.  
- A legjobb új alkalmazásokhoz vagy meglévő helyszíni alkalmazásokhoz, amelyek a legújabb stabil SQL Server szolgáltatásokat szeretnék használni, és amelyeket a felhőbe migrálnak, minimális változtatásokkal. A felügyelt SQL-példányok egy példánya hasonló a [Microsoft SQL Server adatbázismotor](/sql/database-engine/sql-server-database-engine-overview) egy példányához, amely megosztott erőforrásokat kínál az adatbázisokhoz és a további példányokra vonatkozó hatókörű funkciókhoz. 
- Az SQL felügyelt példánya a helyszíni adatbázis-Migrálás minimális és adatbázis-változás nélküli áttelepítését is támogatja. Ez a beállítás biztosítja az összes, a Azure SQL Database által kiváltott összes előnyét, de olyan képességeket ad, amelyek korábban csak SQL Server virtuális gépeken érhetők el. Ez magában foglalja a natív virtuális hálózatot, és közel 100%-os kompatibilitást biztosít a helyszíni SQL Serverokkal. Az SQL felügyelt példány példányai teljes körű SQL Server hozzáférést biztosítanak az SQL-kiszolgálók Azure-ba való áttelepítéséhez.

### <a name="sql-server-on-azure-vm"></a>Azure virtuális gépen futó SQL Server

Az Azure-beli virtuális gépen [SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) az *infrastruktúra-szolgáltatás (IaaS)* kategóriába tartozik, és lehetővé teszi, hogy a SQL Servert egy teljes körűen felügyelt virtuális gépen (VM) belül futtasson az Azure-ban. 
- A legmegfelelőbb az operációs rendszer szintű hozzáférést igénylő áttelepítésekhez és alkalmazásokhoz. Az Azure-beli SQL-alapú virtuális gépek olyan meglévő alkalmazások esetében állnak készen, amelyeken a felhőbe való gyors áttelepítésre van szükség, minimális változtatásokkal vagy módosításokkal. Az SQL-alapú virtuális gépek teljes körű felügyeletet biztosítanak a SQL Server példányon és a mögöttes operációs rendszeren az Azure-ba való áttelepítéshez 
- Ha gyorsan kell elvégezni a fejlesztést és a tesztelést, és nem szeretne helyszíni nem üzemi SQL Server-hardvert vásárolni. Az SQL-alapú virtuális gépek a Microsoft által birtokolt, üzemeltetett és karbantartott szabványos hardvereken is futnak. SQL-alapú virtuális gépek használata esetén a SQL Server-licenccel elszámolható egy SQL Server-lemezképben, vagy egyszerűen használhat meglévő licencet. Igény szerint leállíthatja vagy folytathatja a virtuális gépet. 
- A felhőben telepített és üzemeltetett SQL Server az Azure-ban futó Windows Server-vagy Linux-alapú virtuális gépeken, más néven infrastruktúra-szolgáltatásként (IaaS) futnak. Az SQL Virtual Machines kiváló lehetőség a helyszíni SQL Server adatbázisok és alkalmazások áttelepítésére adatbázis-módosítás nélkül. Az SQL Server összes újabb verziója és kiadása elérhető a IaaS virtuális gépeken való telepítéshez. 

    Az SQL Database és az SQL felügyelt példányai közül a legjelentősebb különbség az, hogy az Azure-Virtual Machines SQL Server az adatbázismotor teljes körű vezérlését teszi lehetővé. Kiválaszthatja, hogy mikor induljon el a karbantartás/javítás, a helyreállítási modellt egyszerűen vagy tömegesen naplózva, szüneteltetheti vagy indíthatja el, ha szükséges, valamint teljes mértékben testre is szabhatja a SQL Server adatbázis-motort. Ezzel a további szabályozással a virtuális gép felügyelete is felmerül.
- Meglévő alkalmazások Azure-ba való áttelepítésére, vagy a meglévő helyszíni alkalmazások felhőbe való kiterjesztésére optimalizált hibrid üzembe helyezés esetén. Emellett a virtuális gépen futó SQL Servert használhatja hagyományos SQL Server-alkalmazások fejlesztésére és tesztelésére is. Az SQL Virtual Machines esetében teljes körű rendszergazdai jogosultságokkal rendelkezik egy dedikált SQL Server-példányon és egy felhőalapú virtuális gépen. Ez tökéletes választás, ha a vállalatnál adva vannak a virtuális gépek fenntartásához szükséges számítástechnikai erőforrások. E lehetőségeknek köszönhetően a legapróbb részletekig testre szabhatja a rendszert, úgy, hogy az a leginkább megfeleljen az alkalmazás teljesítmény- és rendelkezésre állási követelményeinek.

A következő táblázatban további különbségek vannak felsorolva, de a *SQL Database és az SQL felügyelt példánya is optimalizálva van, hogy csökkentse a teljes körű felügyeleti költségeket a sok adatbázis kiépítés és felügyelet számára.* A folyamatos adminisztrációs költségek csökkennek, mivel nem kell virtuális gépeket, operációs rendszert vagy adatbázis-szoftvert kezelni. Nem kell foglalkoznia a frissítések, a magas rendelkezésre állás és a [biztonsági mentések](database/automated-backups-overview.md) kezelésével sem. 

Általánosságban elmondható, hogy SQL Database és az SQL felügyelt példánya jelentősen növelheti az egyetlen informatikai vagy fejlesztési erőforrás által kezelt adatbázisok számát. A [rugalmas készletek](database/elastic-pool-overview.md) támogatják az SaaS több-bérlős alkalmazások architektúráját is, beleértve a bérlők elkülönítését, valamint a költségek csökkentésének lehetőségét az erőforrások adatbázisok közötti megosztásával. A [felügyelt SQL-példányok](managed-instance/sql-managed-instance-paas-overview.md) támogatják a meglévő alkalmazások egyszerű áttelepítését, valamint az erőforrások adatbázisok közötti megosztását lehetővé tevő, példányokra kiterjedő funkciókat.

### <a name="comparison-table"></a>Összehasonlító táblázat

| Azure SQL Database | Felügyelt Azure SQL-példány | Azure virtuális gépen futó SQL Server |
| :--- | :--- | :--- |
|A a legtöbb helyszíni adatbázis-szintű funkciót támogatja. A leggyakrabban használt SQL Server funkciók érhetők el.<br/>99,995%-os rendelkezésre állás garantált.<br/>Beépített biztonsági másolatok, javítás, helyreállítás.<br/>A stabil adatbázismotor legújabb verziója.<br/>A szükséges erőforrások (CPU/tárterület) egyedi adatbázisokhoz való hozzárendelésének lehetősége.<br/>Beépített speciális intelligenciát és biztonságot.<br/>Erőforrások online változása (CPU/Storage).| Szinte minden helyszíni példány-és adatbázis-szintű funkciót támogat. Nagy kompatibilitás a SQL Serversal.<br/>99,99%-os rendelkezésre állás garantált.<br/>Beépített biztonsági másolatok, javítás, helyreállítás.<br/>A stabil adatbázismotor legújabb verziója.<br/>SQL Server egyszerű Migrálás.<br/>Magánhálózati IP-cím az Azure Virtual Networkon belül.<br/>Beépített speciális intelligenciát és biztonságot.<br/>Erőforrások online változása (CPU/Storage).| A SQL Server motor teljes körű vezérléssel rendelkezik. Támogatja az összes helyszíni funkciót.<br/>Akár 99,99%-os rendelkezésre állást.<br/>Teljes paritás a helyszíni SQL Server megfelelő verziójával.<br/>Javított, jól ismert adatbázismotor-verzió.<br/>SQL Server egyszerű Migrálás.<br/>Magánhálózati IP-cím az Azure Virtual Networkon belül.<br/>Lehetősége van arra, hogy az alkalmazást vagy szolgáltatásokat olyan gazdagépen telepítse, ahol a SQL Server elhelyezve.|
|A SQL Server áttelepítése kihívást jelenthet.<br/>Néhány SQL Server szolgáltatás nem érhető el.<br/>Nincs garantált pontos karbantartási idő (de majdnem transzparens).<br/>A SQL Server verziójával való kompatibilitás csak az adatbázis-kompatibilitási szintek használatával érhető el.<br/>Magánhálózati IP-címek támogatása az [Azure privát hivatkozásával](database/private-endpoint-overview.md).|Még mindig van néhány minimális számú SQL Server szolgáltatás, amely nem érhető el.<br/>Nincs garantált pontos karbantartási idő (de majdnem transzparens).<br/>A SQL Server verziójával való kompatibilitás csak az adatbázis-kompatibilitási szintek használatával érhető el.|Kezelnie kell a biztonsági mentéseket és a javításokat.<br>Saját High-Availability megoldást kell megvalósítani.<br/>Az erőforrások (processzor/tároló) módosítása közben állásidő van|
| Legfeljebb 100 TB-os adatbázisok. | Akár 8 TB-ig. | SQL Server példányok száma akár 256 TB tárhellyel. A példányok annyi adatbázist képesek támogatni, amennyire szükség van. |
| A helyszíni alkalmazások hozzáférhetnek Azure SQL Databasehoz. | [Natív virtuális hálózat megvalósítása](managed-instance/vnet-existing-add-subnet.md) és a helyszíni környezethez való kapcsolódás az Azure Express Route vagy a VPN Gateway használatával. | Az SQL Virtual Machines szolgáltatásban a felhőben és részben helyileg futó alkalmazások is lehetnek. Például az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) segítségével kiterjesztheti helyszíni hálózatát és Active Directory-tartományát a felhőre. A hibrid felhőalapú megoldásokkal kapcsolatos további információkért lásd: helyszíni [Adatmegoldások kiterjesztése a felhőre](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Költségek

Legyen szó akár egy olyan indításról, amely készpénzre van kialakítva, vagy egy vállalat, amely szűk költségvetési megkötések keretében működik, a korlátozott finanszírozás gyakran az elsődleges illesztőprogram az adatbázisok üzemeltetésének eldöntése során. Ebben a szakaszban megismerheti az Azure SQL Family of Services szolgáltatáshoz kapcsolódó számlázási és licencelési alapismereteket.  Megismerheti az alkalmazás összköltségének kiszámításának módját is.

### <a name="billing-and-licensing-basics"></a>A számlázás és a licencelés alapjai

Jelenleg a **SQL Database** és az **SQL felügyelt példány** is szolgáltatásként van értékesítve, és számos különböző lehetőséggel, valamint számos szolgáltatási szinten érhető el, az erőforrások eltérő áraival, amelyek mindegyikét óránként, a szolgáltatási szinten és a kiválasztott számítási méret alapján számoljuk el. A jelenleg támogatott szolgáltatási rétegekkel, számítási méretekkel és tárolási mennyiségekkel kapcsolatos legfrissebb információkért tekintse meg a [SQL Database és az SQL felügyelt példányának SQL Database és virtuális mag-alapú vásárlási modell](database/service-tiers-vcore.md) [DTU-alapú vásárlási modelljét](database/service-tiers-dtu.md) .

- A SQL Database segítségével kiválaszthatja az igényeinek megfelelő szolgáltatási szintet, amely az alapszintű csomag 5 $/hó kezdődően számos díjszabással rendelkezik, és [rugalmas készleteket](database/elastic-pool-overview.md) hozhat létre, amelyekkel az erőforrások között megoszthatja az erőforrásokat a költségek csökkentése és a használati tüskék kielégítése érdekében.
- Az SQL felügyelt példányával saját licenc is létrehozható. A saját licenceléssel kapcsolatos további információkért lásd: [licenchordozhatóság frissítési garancia keretében az Azure](https://azure.microsoft.com/pricing/license-mobility/) -ban, vagy a [Azure Hybrid Benefit Calculator](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) használatával megtudhatja, hogyan **takaríthat meg akár 40%-ot**.

Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik. A szolgáltatási szintek és a számítási méretek dinamikusan állíthatók be úgy, hogy megfeleljenek az alkalmazás eltérő átviteli igényeinek.

Az **SQL Database** és az **SQL felügyelt példánya** esetében az adatbázis szoftverét az Azure automatikusan konfigurálja, javította és frissíti, ami csökkenti az adminisztrációs költségeket. Ezenfelül a [beépített biztonsági mentési](database/automated-backups-overview.md) funkciókkal is jelentős költségmegtakarítást érhet el, különösen, ha nagyszámú adatbázist használ.

Az **SQL Azure-beli virtuális gépeken** bármilyen platformon elérhető SQL Server rendszerképet használhat (amely tartalmaz licencet), vagy a SQL Server licencét is. Az összes támogatott SQL Server verzió (2008R2, 2012, 2014, 2016, 2017, 2019) és kiadás (fejlesztői, expressz, web, standard, Enterprise) elérhető. Emellett a rendszerképek saját licenccel használható (BYOL) verziói is elérhetők. Az Azure által biztosított rendszerképek használata esetén az üzemeltetési költségek a virtuális gép méretétől, valamint a használni kívánt SQL Server-verziótól függnek. A virtuálisgép-mérettől vagy a SQL Server kiadástól függetlenül a SQL Server és a Windows-vagy Linux-kiszolgáló, valamint a virtuálisgép-lemezek Azure Storage-díjait is igénybe véve kell fizetnie. A percalapú számlázásnak köszönhetően igényeinek megfelelő ideig használhatja az SQL Servert, anélkül, hogy további SQL Server-licenceket kellene vásárolnia. Ha a saját SQL Server licencét az Azure-ba viszi, csak a kiszolgáló-és tárolási költségekért kell fizetnie. A saját licenc használatával kapcsolatos további információkért lásd: [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) (Licenchordozás az Azure Frissítési garancia programja keretében). Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik.

#### <a name="calculating-the-total-application-cost"></a>Az alkalmazás összköltségének kiszámítása

A felhőalapú platform használatának megkezdése után az alkalmazás futtatásának költsége magában foglalja az új fejlesztési és folyamatos adminisztrációs költségek költségeit, valamint a nyilvános Cloud platform szolgáltatási költségeit.

Az árakkal kapcsolatos további információkért lásd a következő erőforrásokat:

- [SQL Database & SQL felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/)
- Az [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) és a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) rendszerhez készült [virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Felügyelet

Számos vállalat számára a felhőszolgáltatásokra történő átállást az összetett adminisztrációs feladatok kiszervezése ösztönzi, és nem csupán a költségszempontok. A IaaS és a Pásti használatával az Azure felügyeli a mögöttes infrastruktúrát, és automatikusan replikálja az összes adatát a vész-helyreállítás biztosítására, az adatbázis-szoftverek konfigurálására és frissítésére, kezeli a terheléselosztást, és átlátható feladatátvételt hajt végre, ha a kiszolgáló meghibásodása egy adatközponton belül történik.

- A **SQL Database** és az **SQL felügyelt példányával** továbbra is felügyelheti az adatbázist, de többé nem kell felügyelni az adatbázismotor, az operációs rendszer vagy a hardver kezelését. Ön felelős például a következő területekért: adatbázisok és bejelentkezési adatok, indexek és lekérdezések hangolása, naplózás és biztonság. Emellett a magas rendelkezésre állás konfigurálása egy másik adatközponthoz minimális konfigurációt és felügyeletet igényel.
- Az **SQL Azure-beli virtuális gépen** teljes körű vezérléssel rendelkezik az operációs rendszer és a SQL Server példány konfigurálása során. A virtuális gép segítségével eldöntheti, hogy mikor kell frissítenie/frissíteni az operációs rendszert és az adatbázis-szoftvert, és hogy mikor kell további szoftvereket, például víruskeresőt telepítenie. Egyes automatizált szolgáltatások jelentősen leegyszerűsítik a frissítést, a biztonsági mentések készítését és a magas rendelkezésre állást. Ezenfelül meghatározhatja a virtuális gép méretét, a lemezek számát, valamint a tárhely konfigurációját is. Az Azure-ban igény szerint módosítható a virtuális gépek mérete. További információk: [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/sizes.md) (Virtuális gépek és felhőszolgáltatások mérete az Azure-ban).

## <a name="service-level-agreement-sla"></a>Szolgáltatói szerződés (SLA)

Számos informatikai részleg esetében a szolgáltatói szerződéssel (SLA) kapcsolatos határidők kiemelt prioritást jelentenek. Ebben a részben áttekintjük, hogy milyen  SLA vonatkozik az egyes adatbázis-üzemeltetési megoldásokra.

Mind a **Azure SQL Database** , mind az **Azure SQL felügyelt példánya** esetében a Microsoft 99,99%-os rendelkezésre állási SLA-t biztosít. A legfrissebb információkért lásd: [szolgáltatási szintű szerződés](https://azure.microsoft.com/support/legal/sla/sql-database/).

Az **SQL Azure**-beli virtuális gépen a Microsoft 99,95%-os rendelkezésre állási SLA-t biztosít, amely csak a virtuális gépet fedi le. Ez az SLA nem érvényes a virtuális gépen futó folyamatokra (például az SQL Serverre), ezenfelül legalább két, rendelkezésre állási csoportba rendezett virtuálisgép-példány futtatása is szükséges hozzá. A legfrissebb információkért lásd: [SLA a következőhöz: Virtuális gépek](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Az adatbázisok magas rendelkezésre állása (HA) a virtuális gépeken belül a támogatott magas rendelkezésre állási lehetőségek egyikét kell konfigurálnia SQL Serverban, például [Always On rendelkezésre állási csoportok](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). A támogatott magas rendelkezésre állási lehetőségek valamelyikének használata nem biztosít további SLA-t, de lehetővé teszi az adatbázis 99,99% fölötti rendelkezésre állásának elérését.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Az Azure-ba való áttérés ideje

A **Azure SQL Database** a megfelelő megoldás a felhőben tervezett alkalmazások számára, ha a fejlesztő és a gyors piacra kerülési idő az új megoldások esetében kritikus fontosságú. A DBA-hoz hasonló programozási funkciói révén tökéletes választás a felhőben dolgozó tervezők és fejlesztők számára, mivel csökkenti az alkalmazás mögötti operációs rendszer és adatbázis felügyeletére fordítandó időt.

Az **Azure SQL felügyelt példánya** nagymértékben leegyszerűsíti a meglévő alkalmazások Azure-ba való áttelepítését, így gyorsan áttelepítheti az áttelepített adatbázis-alkalmazásokat az Azure-ba.

Az SQL az Azure-beli **virtuális gépen** tökéletes megoldás, ha meglévő vagy új alkalmazásai nagyméretű adatbázisokat igényelnek, vagy SQL Server vagy Windows/Linux összes szolgáltatásához hozzáférnek, és el szeretné kerülni az új helyszíni hardver beszerzésének idejét és költségét. Ez akkor is megfelelő, ha a meglévő helyszíni alkalmazásokat és adatbázisokat át szeretné telepíteni az Azure-ba olyan esetekben, amikor a SQL Database vagy az SQL felügyelt példánya nem megfelelő. Mivel nem kell módosítania a bemutatót, az alkalmazást és az adatréteget, időt és költségvetést takaríthat meg a meglévő megoldás újratervezése során. Ehelyett termékeinek az Azure-ra történő áttelepítésére, illetve az Azure-platformon való működéshez esetleg szükséges teljesítményoptimalizálásra összpontosíthat. További információk: [Performance Best Practices for SQL Server on Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md) (Teljesítményre vonatkozó ajánlott eljárások az Azure Virtual Machines szolgáltatásban futtatott SQL Server esetében).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Következő lépések

- Tekintse meg [az első Azure SQL-adatbázissal](database/single-database-create-quickstart.md) foglalkozó témakört, hogy megismerkedhessen az SQL Database használatának első lépéseivel.
- Az SQL felügyelt példányának megismeréséhez tekintse [meg az első Azure SQL-beli felügyelt példányát](managed-instance/instance-create-quickstart.md) . 
- [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).
- Az Azure virtuális gépeken futó SQL Server használatának megkezdésével kapcsolatban lásd: [Provision a SQL Server virtual machine in Azure](virtual-machines/windows/create-sql-vm-portal.md) (SQL Servert futtató virtuális gép létrehozása az Azure-ban).
- [Azonosítsa a helyszíni adatbázis megfelelő SQL Database vagy SQL felügyelt példányának SKU-](/sql/dma/dma-sku-recommend-sql-db/)t.