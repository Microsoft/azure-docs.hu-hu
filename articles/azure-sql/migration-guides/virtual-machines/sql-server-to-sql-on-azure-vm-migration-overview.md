---
title: Az Azure-beli virtuális gépen SQL Server SQL Server (áttelepítési áttekintés)
description: Ismerje meg a különböző áttelepítési stratégiákat, amikor át szeretné telepíteni a SQL Servert az Azure-beli virtuális gépeken való SQL Serverre.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 2702451aa3645b2702f8f38c37574a6601249dac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641413"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>Áttelepítési Áttekintés: az Azure-beli virtuális gépeken SQL Server SQL Server
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Ismerkedjen meg a különböző áttelepítési stratégiákkal a SQL Server áttelepítéséhez az Azure Virtual Machines (VM) SQL Server. 

A helyszíni vagy a szolgáltatásban futó SQL Server áttelepíthetők:

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon-hoz kapcsolódó adatbázis-szolgáltatás (AWS RDS) 
- Számítási motor (Google Cloud Platform-GCP)

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Áttekintés

Migrálás [SQL Serverre az Azure Virtual Machines (VM)](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) szolgáltatásban, ha a jól ismert SQL Server környezetet szeretné használni az operációs rendszer vezérlésével, és szeretné kihasználni a felhőben biztosított funkciókat, például a beépített virtuális gépek magas rendelkezésre állását, az [automatizált biztonsági mentéseket](../../virtual-machines/windows/automated-backup.md)és az [automatizált javításokat](../../virtual-machines/windows/automated-patching.md). 

Az [ingyenes biztonsági frissítések](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)beszerzésével megtakaríthatja a saját licencét a [Azure Hybrid Benefit licencelési modellel](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) , vagy kiterjesztheti a SQL Server 2008 és SQL Server 2008 R2 támogatását. 


## <a name="choose-appropriate-target"></a>A megfelelő cél kiválasztása

Az Azure Virtual Machines több különböző Azure-régióban is futtatható, és számos különféle [méretű](../../../virtual-machines/sizes.md) és [tárolási lehetőséget](../../../virtual-machines/disks-types.md)kínál. A SQL Server számítási feladathoz tartozó virtuális gépek és tárolók megfelelő méretének meghatározásakor tekintse meg az Azure-beli [SQL Server teljesítmény-irányelveit Virtual machines.](../../virtual-machines/windows/performance-guidelines-best-practices.md#vm-size-guidance) A virtuális gép méretének és tárolási követelményeinek meghatározása a számítási feladatokhoz. azt javasoljuk, hogy ezek a Performance-Based [Azure Migrate Assessment](../../../migrate/concepts-assessment-calculation.md#types-of-assessments)használatával legyenek méretezve. Ha ez nem érhető el, tekintse meg a következő cikket a saját alapkonfigurációjának létrehozásához a [teljesítményhez](https://azure.microsoft.com/services/virtual-machines/sql-server/).

A virtuális gépen SQL Server megfelelő telepítésére és konfigurálására is figyelmet kell fordítani. Javasoljuk, hogy az [Azure SQL-alapú virtuális gépek képtárat](../../virtual-machines/windows/create-sql-vm-portal.md) használja, mivel ez lehetővé teszi, hogy a megfelelő verziójú, kiadású és operációs rendszerű SQL Server VM hozzon létre. A szolgáltatás automatikusan regisztrálja az Azure-beli virtuális gépet a SQL Server [erőforrás-szolgáltatóval](../../virtual-machines/windows/create-sql-vm-portal.md) , így lehetővé teszi az automatikus biztonsági mentéseket és az automatikus javítást.

## <a name="migration-strategies"></a>Migrálási stratégiák

Két áttelepítési stratégia van a felhasználói adatbázisok áttelepítésére az Azure-beli virtuális gépeken futó SQL Server-példányra: **Migrálás**, **átemelés és áthelyezés**. 

Az üzleti tevékenységének megfelelő megközelítése általában az alábbi tényezőktől függ: 

- Az áttelepítés mérete és méretezése
- Áttelepítési sebesség
- Alkalmazás-támogatás a kód módosításához
- Módosítania kell SQL Server verziót, az operációs rendszert vagy mindkettőt.
- A meglévő termékek támogatási életciklusa
- Alkalmazás-állásidő ablaka az áttelepítés során

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="virtuális gépek áttelepítésének leállása":::

Az alábbi táblázat a két áttelepítési stratégia különbségeit ismerteti:
<br />

| **Migrálási stratégia** | **Leírás** | **A következő esetekben használja** |
| --- | --- | --- |
| **& eltolásának emelése** | A lift és a SHIFT áttelepítési stratégiával áthelyezheti a teljes fizikai vagy virtuális SQL Server a jelenlegi helyükről az Azure-beli virtuális GÉPEN lévő SQL Server egy példányára, az operációs rendszer vagy a SQL Server verziójának módosítása nélkül. A felvonó és a váltás áttelepítésének befejezéséhez lásd: [Azure Migrate](../../../migrate/migrate-services-overview.md). <br /><br /> A forráskiszolgáló online és szolgáltatási kéréseket is biztosít, miközben a forrás-és a célkiszolgáló szinkronizálja az adatokat, így szinte zökkenőmentesen áttelepíthetők. | Egyetlen és nagyon nagy léptékű Migrálás esetén is használható, akár olyan forgatókönyvekre is, mint például az adatközpont kilépése. <br /><br /> A felhasználók SQL-adatbázisaihoz vagy-alkalmazásaihoz minimálisan szükséges programkódok módosítása, ami lehetővé teszi a gyorsabb teljes áttelepítést. <br /><br />Nincs szükség további lépésekre az üzleti intelligencia szolgáltatások, például a  [SSIS](/sql/integration-services/sql-server-integration-services), az [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)és a [SSAS](/analysis-services/analysis-services-overview)áttelepítéséhez. |
|**Migrate** | Ha a cél SQL Server és/vagy az operációs rendszer verzióját szeretné frissíteni, használja az áttelepítési stratégiát. <br /> <br /> Válasszon ki egy Azure-beli virtuális gépet az Azure Marketplace-ről, vagy egy előkészített SQL Server rendszerképet, amely megfelel a forrás SQL Server verziójának. | Akkor használja, ha a SQL Server újabb verzióiban elérhető funkciókra van szükség, vagy ha a régebbi SQL Server és/vagy operációsrendszer-verziók frissítésére van szükség, amelyek már nem támogatottak.  <br /> <br /> Előfordulhat, hogy a SQL Server frissítésének támogatásához bizonyos alkalmazás-vagy felhasználói adatbázis-módosítások szükségesek. <br /><br />Az [üzleti intelligencia](#business-intelligence) szolgáltatásainak áttelepítésére további szempontokat is figyelembe kell venni, ha az áttelepítés hatálya alá esik. |


## <a name="lift-and-shift"></a>Átemelés  

Az alábbi táblázat ismerteti a **lift és a SHIFT** áttelepítési stratégiájának elérhető módszereit, hogy áttelepítse a SQL Server-adatbázist az Azure-beli virtuális gépeken SQL Serverba:
<br />

|**Metódus** | **A forrás minimális verziója** | **Cél minimális verziója** | **Forrás biztonsági mentési méretének korlátja** |  **Megjegyzések** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Azure VM-tároló korlátja](../../../index.yml) |  Meglévő SQL Server áthelyezhető egy Azure-beli virtuális gépen lévő SQL Server-példányra. Akár 35 000 virtuális gépre is méretezheti az áttelepítési feladatokat. <br /><br /> A forrás-kiszolgáló (k) a kiszolgáló adatainak szinkronizálása során online és karbantartási kérelmeket is megtarthat, minimalizálva az állásidőt. <br /><br /> **Automation & parancsfájlkezelés**: [Azure site Recovery szkriptek](../../../migrate/how-to-migrate-at-scale.md) és [példa az Azure-ra méretezett áttelepítésre és tervezésre](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Migrate  

A könnyű telepítés miatt a javasolt áttelepítési módszer a natív SQL Server [biztonsági mentés helyi mentése](/sql/t-sql/statements/backup-transact-sql) , majd a fájl másolása az Azure-ba. Ez a módszer támogatja a nagyobb adatbázisok (>1 TB) használatát a SQL Server összes verziójához, amely a 2008-es és nagyobb adatbázis-biztonsági mentéstől (>1 TB). Azonban a SQL Server 2014-től kezdődő, 1 TB-nál kisebb, és az Azure-hoz való jó kapcsolattal rendelkező adatbázisok esetében a jobb megközelítés [SQL Server biztonsági mentés az URL-címre](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . 

Ha SQL Server-adatbázisokat az Azure virtuális gépeken SQL Server egy példányára telepít át, akkor fontos, hogy olyan megközelítést válasszon, amely megfelel a átváltás, mivel ez hatással van az alkalmazás leállására szolgáló ablakra.

Az alábbi táblázat az SQL Server-adatbázis Azure-beli virtuális gépeken való SQL Serverre történő áttelepítésének összes lehetséges módszerét részletezi:
<br />

|**Metódus** | **A forrás minimális verziója** | **Cél minimális verziója** | **Forrás biztonsági mentési méretének korlátja** | **Megjegyzések** |
| --- | --- | --- | --- | --- |
| **[Biztonsági mentés fájlba](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Azure VM-tároló korlátja](../../../index.yml) |  Ez egy egyszerű és jól tesztelt módszer az adatbázisok számítógépek közötti áthelyezéséhez. Használjon tömörítést a biztonsági másolatok méretének minimalizálásához az átvitelhez. <br /><br /> **Automation & parancsfájlkezelés**: [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) és [AzCopy a blob Storage-hoz](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Biztonsági mentés az URL-címre](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 12,8 TB SQL Server 2016, ellenkező esetben 1 TB | A biztonságimásolat-fájlnak a virtuális gépre való áthelyezésének másik módja az Azure Storage használatával. Használjon tömörítést a biztonsági másolatok méretének minimalizálásához az átvitelhez. <br /><br /> **Automation & parancsfájlkezelés**:  [T-SQL vagy karbantartási terv](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Adatbázis-Migration Assistant (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Azure VM-tároló korlátja](../../../index.yml) |  A [DMA](/sql/dma/dma-overview) a helyszíni SQL Server, majd zökkenőmentesen frissíti SQL Server újabb verzióit, vagy áttelepíti az Azure-beli virtuális gépek, Azure SQL Database vagy az Azure SQL felügyelt példányának SQL Server. <br /><br /> Nem használhatók FileStream-kompatibilis felhasználói adatbázisokon.<br /><br /> A DMA emellett képes az [SQL-és Windows-bejelentkezések](/sql/dma/dma-migrateserverlogins) átadására és az [SSIS-csomagok](/sql/dma/dma-assess-ssis)értékelésére is. <br /><br /> **Automation & parancsfájlkezelés**: [parancssori felület](/sql/dma/dma-commandline) |
| **[Leválasztás és csatolás](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Azure VM-tároló korlátja](../../../index.yml) | Akkor használja ezt a módszert, ha [ezeket a fájlokat az Azure Blob Storage szolgáltatással szeretné tárolni](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) , és csatolja őket egy Azure-beli virtuális gépen lévő SQL Server egy példányához, különösen nagy méretű adatbázisokhoz, vagy ha a biztonsági mentés és a visszaállítás ideje túl hosszú. <br /><br /> **Automation & parancsfájlkezelés**:  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) és [AzCopy a blob Storage-hoz](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Napló szállítása](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (csak Windows) | SQL Server 2008 SP4 (csak Windows) | [Azure VM-tároló korlátja](../../../index.yml) | A naplózási szolgáltatás a tranzakciós naplófájlokat a helyszíni környezetből replikálja egy Azure-beli virtuális gépen lévő SQL Server egy példányára. <br /><br /> Ez minimális állásidőt biztosít a feladatátvétel során, és kevesebb konfigurációs terheléssel rendelkezik, mint az Always On rendelkezésre állási csoport beállítása. <br /><br /> **Automation & parancsfájlkezelés**: [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Elosztott rendelkezésre állási csoport](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Azure VM-tároló korlátja](../../../index.yml) |  Az [elosztott rendelkezésre állási csoport](/sql/database-engine/availability-groups/windows/distributed-availability-groups) a rendelkezésre állási csoport egy speciális típusa, amely két különálló rendelkezésre állási csoportot ölel fel. Az elosztott rendelkezésre állási csoportban részt vevő rendelkezésre állási csoportoknak nem kell ugyanazon a helyen lenniük, és a tartományok közötti támogatást is meg kell adni. <br /><br /> Ez a módszer lekicsinyíti az állásidőt, és akkor használja, ha a helyszínen konfigurált rendelkezésre állási csoport van. <br /><br /> **Automation & parancsfájlkezelés**: [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> A hálózati beállításokkal nem rendelkező nagyméretű adatátvitelek esetében tekintse meg a [nagyméretű adatátvitel korlátozott kapcsolattal](../../../storage/common/storage-solution-large-dataset-low-network.md)című témakört.
> 

### <a name="considerations"></a>Megfontolandó szempontok

Az alábbi lista az áttelepítési módszerek áttekintése során megfontolandó főbb pontokat sorolja fel:

- Az optimális adatátviteli teljesítmény érdekében az adatbázisok és fájlok áttelepíthetők az Azure-beli virtuális gépen lévő SQL Server példányára egy tömörített biztonságimásolat-fájl használatával. Nagyobb adatbázisok esetén a tömörítésen kívül a biztonsági mentés és az átvitel során nagyobb teljesítmény érdekében [Ossza szét a biztonságimásolat-fájlt kisebb fájlokra](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) . 
- Ha SQL Server 2014-es vagy újabb verzióról végez áttelepítést, érdemes lehet [titkosítani a biztonsági mentéseket](/sql/relational-databases/backup-restore/backup-encryption) az adatok védelme érdekében a hálózati átvitel során.
- Az adatbázis-áttelepítés során felmerülő állásidő minimalizálásához használja az Always On rendelkezésre állási csoport lehetőséget. 
- Ha az állásidőt a rendelkezésre állási csoport konfigurálása nélkül szeretné csökkenteni, használja a naplózási lehetőséget. 
- A hálózati beállítások nélküli áttelepítési módszerekkel, például a biztonsági mentéssel és visszaállítással, vagy az Azure-ban elérhető [lemezes adatátviteli szolgáltatásokkal](../../../storage/common/storage-solution-large-dataset-low-network.md) .
- Ha az Azure-beli virtuális gépen lévő SQL Server SQL Server verzióját is módosítani szeretné, tekintse meg a [SQL Server kiadásának módosítása](../../virtual-machines/windows/change-sql-server-edition.md)című témakört.

## <a name="business-intelligence"></a>Üzleti intelligencia 

További szempontokat is figyelembe kell venni a SQL Server üzleti intelligencia szolgáltatásoknak a felhasználói adatbázis áttelepítésének hatókörén kívüli áttelepítése során. 

Ilyen szolgáltatások többek között a következők:

- [**SQL Server Integration Services (SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>Támogatott verziók

A SQL Server adatbázisok Azure-beli virtuális gépeken való SQL Serverre való áttelepítésének előkészítésekor ügyeljen arra, hogy a támogatott SQL Server-verziókat vegye figyelembe. Az Azure-beli virtuális gépeken támogatott SQL Server-verziók listáját az Azure-beli [virtuális gépeken található SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

## <a name="migration-assets"></a>Áttelepítési eszközök 

További segítségért tekintse meg az alábbi, a valós migrációs projektekhez fejlesztett forrásokat.

|Objektum  |Leírás  |
|---------|---------|
|[Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Data%20Workload%20Assessment%20Model%20and%20Tool)| Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely segít felgyorsítani a nagyméretű ingatlanok értékelését azáltal, hogy lehetővé teszi a és automatizált és egységes célként megadott platform döntési folyamatát.|
|[Perfmon-adatgyűjtés automatizálása a Logman használatával](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Egy olyan eszköz, amely összegyűjti az adatokat, hogy megismerje az áttelepítési cél javaslatában támogatott alapkonfigurációk teljesítményét. Ez az eszköz a logman.exe használatával hozza létre a távoli SQL Serveron beállított teljesítményszámlálók létrehozására, indítására, leállítására és törlésére szolgáló parancsot.|
|[SQL Server üzembe helyezés az Azure-ban](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/SQL%20Server%20Deployment%20in%20Azure%20.pdf)|Ez az útmutató a különböző lehetőségek áttekintését segíti a SQL Server számítási feladatok Azure-ba való áthelyezéséhez, beleértve a funkciók összehasonlítását, a magas rendelkezésre állást, valamint a biztonsági mentési és tárolási szempontokat. |
|[Helyszíni SQL Server Azure-beli virtuális géphez](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/OnPremise%20SQL%20Server%20to%20Azure%20VM.pdf)|Ez a tanulmány az adatbázisok helyszíni SQL Serverról történő biztonsági mentésének és visszaállításának lépéseit ismerteti az Azure-beli virtuális gépen a minta parancsfájlokkal való SQL Server.|
|[Több-SQL-VM-VNet-ILB](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/ARM%20Templates/Multiple-SQL-VM-VNet-ILB)|Ez a tanulmány a SQL Server always on rendelkezésre állási csoport konfigurációjában található több Azure-beli virtuális gép beállításának lépéseit ismerteti.|
|[Régiónként ultra SSD támogató Azure-beli virtuális gépek](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Find%20Azure%20VMs%20supporting%20Ultra%20SSD)|Ezek a PowerShell-parancsfájlok az ultra SSD-ket támogató Azure-beli virtuális gépeket támogató régiók listájának beolvasására szolgáló programozott lehetőséget biztosítanak.|

Az adatsql mérnöki csapat fejlesztette ezeket az erőforrásokat. A csapat alapszintű alapokmánya az adatplatform-áttelepítési projektek a Microsoft Azure-beli adatplatformra való feltiltásának és felgyorsításának feloldása.

## <a name="next-steps"></a>Következő lépések

Az SQL Server-adatbázisok Azure-beli virtuális gépeken SQL Server való áttelepítésének megkezdéséhez tekintse meg az [egyes adatbázis-áttelepítési útmutatót](sql-server-to-sql-on-azure-vm-individual-databases-guide.md). 

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egy olyan mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, tekintse meg a cikk [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md) című témakört.

- További információ az Azure SQL-ről:
   - [Üzembe helyezési lehetőségek](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure-beli virtuális gépeken futó SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- További információ a licencelésről:
   - [Saját licenc használata a Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [A SQL Server 2008 és SQL Server 2008 R2 ingyenes kibővített támogatása](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).