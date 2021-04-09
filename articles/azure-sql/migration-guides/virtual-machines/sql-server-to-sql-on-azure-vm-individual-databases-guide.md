---
title: 'Azure-beli virtuális gépeken SQL Server SQL Server: áttelepítési útmutató'
description: Ez az útmutató bemutatja, hogyan telepítheti át az egyes SQL Server adatbázisait az Azure-beli virtuális gépeken SQL Server.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 7ef942404158768f4249b5bcd0640632b6475f63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023707"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>Áttelepítési útmutató: az Azure-beli virtuális gépeken SQL Server SQL Server 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Ez az áttelepítési útmutató bemutatja, hogyan **derítheti fel**, **értékelheti** és **telepítheti át** a felhasználói adatbázisokat a SQL Serverról SQL Server Azure-beli Virtual Machines (VM)-példányra a biztonsági mentési és visszaállítási, valamint a naplózási szolgáltatással az [adatbázis Migration Assistant (DMA)](/sql/dma/dma-overview) használatával történő értékeléshez. 

A helyszíni vagy a szolgáltatásban futó SQL Server áttelepíthetők:

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon-hoz kapcsolódó adatbázis-szolgáltatás (AWS RDS) 
- Számítási motor (Google Cloud Platform-GCP)

További információ a további áttelepítési stratégiákkal kapcsolatban: [SQL Server VM áttelepítésének áttekintése](sql-server-to-sql-on-azure-vm-migration-overview.md). Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://docs.microsoft.com/data-migration). 

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Áttelepítési folyamat folyamatábrája":::

## <a name="prerequisites"></a>Előfeltételek

Az Azure-beli virtuális gépeken SQL Server áttelepítéshez a következők szükségesek: 

- [Adatbázis Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
- Egy [Azure Migrate projekt](../../../migrate/create-manage-projects.md).
- Egy előkészített cél [SQL Server az Azure-beli virtuális gépen](../../virtual-machines/windows/create-sql-vm-portal.md) , amely a forrás-SQL Server megegyező vagy újabb verziójú.
- [Kapcsolódás az Azure és a](/azure/architecture/reference-architectures/hybrid-networking)helyszíni környezet között.
- [Megfelelő áttelepítési stratégia kiválasztása](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>A migrálás előtt

Az áttelepítés megkezdése előtt Fedezze fel az SQL-környezet topológiáját, és mérje fel a kívánt áttelepítés megvalósíthatóságát. 

### <a name="discover"></a>Felderítés

Azure Migrate a helyszíni számítógépek áttelepítésének megfelelőségét méri, teljesítmény-alapú méretezést hajt végre, és a helyszínen történő futtatáshoz biztosít költségbecslést. Az áttelepítés megtervezéséhez használja a Azure Migrate a [meglévő adatforrások azonosításához és a](../../../migrate/concepts-assessment-calculation.md) SQL Server példányok által használt funkciók részleteit. Ez a folyamat a szervezet összes SQL Server-példányának azonosítására szolgál a használatban lévő verzió és szolgáltatások alapján. 

> [!IMPORTANT]
> Az Azure-beli virtuális gépek SQL Server-példányhoz való kiválasztásakor vegye figyelembe az Azure-beli virtuális gépeken futó [SQL Server teljesítmény-irányelveit](../../virtual-machines/windows/performance-guidelines-best-practices.md).

További felderítési eszközök: az adatáttelepítési forgatókönyvekhez elérhető [szolgáltatások és eszközök](../../../dms/dms-tools-matrix.md#business-justification-phase) .


### <a name="assess"></a>Kiértékelés

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Miután felderítette az összes adatforrást, a [Data Migration Assistant (DMA)](/sql/dma/dma-overview) segítségével mérje fel a helyszíni SQL Server példányokat, amelyek áttelepíthetők az Azure-beli virtuális gépen SQL Server példányára, hogy megértse a forrás-és a cél példányok közötti hézagokat. 


> [!NOTE]
> Ha _nem_ frissíti a SQL Server verzióját, hagyja ki ezt a lépést, és lépjen az [áttelepítés](#migrate) szakaszra. 


#### <a name="assess-user-databases"></a>Felhasználói adatbázisok felmérése 

A Data Migration Assistant (DMA) segíti az áttelepítést egy modern adatplatformon azáltal, hogy észleli a kompatibilitási problémákat, amelyek hatással lehetnek az adatbázis-funkciókra a SQL Server új verziójában. A DMA a célzott környezet teljesítményének és megbízhatóságának növelését javasolja, továbbá lehetővé teszi a séma, az adatok és a bejelentkezési objektumok áthelyezését a forráskiszolgálóról a célkiszolgálóra.

További információért tekintse meg az [értékelést](/sql/dma/dma-migrateonpremsql) . 


> [!IMPORTANT]
>Az értékelés típusa alapján a forrás SQL Serverhoz szükséges engedélyek eltérőek lehetnek. 
   > - A **szolgáltatás paritásos** tanácsadója esetében a forrás SQL Server adatbázishoz való kapcsolódáshoz megadott hitelesítő adatoknak a *sysadmin (rendszergazda* ) kiszolgálói szerepkör tagjának kell lenniük.
   > - A kompatibilitási problémák tanácsadója számára a megadott hitelesítő adatoknak legalább `CONNECT SQL` `VIEW SERVER STATE` és `VIEW ANY DEFINITION` engedélyekkel kell rendelkezniük.
   > - A DMA a felmérés futtatása előtt kiemeli a kiválasztott tanácsadóhoz szükséges engedélyeket.


#### <a name="assess-applications"></a>Alkalmazások értékelése

Az alkalmazási réteg általában a felhasználói adatbázisokhoz fér hozzá az adatmegőrzéshez és az adatmódosításhoz.  A DMA kétféleképpen tudja értékelni az alkalmazás adatelérési rétegét: 

- Rögzített [kiterjesztett események](/sql/relational-databases/extended-events/extended-events) vagy a felhasználói adatbázisok [SQL Server Profiler nyomkövetésének ](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) használata. A [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-capture-trace) használatával olyan nyomkövetési naplót is létrehozhat, amely a/B teszteléshez is használható.

- Az [adathozzáférés áttelepítési eszközkészletének (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) (DAMT) használata, amely a KÓDBAN található SQL-lekérdezések felderítését és értékelését biztosítja, és az alkalmazás forráskódját az egyik adatbázis-platformról egy másikra telepíti át. Ez az eszköz számos népszerű fájltípust támogat, többek között a C#, a Java, az XML és a panasz szöveget. A DAMT-felmérés végrehajtásával kapcsolatos útmutatóért tekintse meg a [DMAT blog használata](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) című témakört.

A DMA használatával a rögzített nyomkövetési fájlokat vagy DAMT fájlokat [importálhatja](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) a felhasználói adatbázisok értékelése során. 


#### <a name="scale-assessments"></a>Skálázási felmérések

Ha több olyan kiszolgálóval rendelkezik, amelyek DMA-értékelést igényelnek, akkor a [parancssori felületen](/sql/dma/dma-commandline)automatizálhatja a folyamatot. Az illesztőfelület használatával előre felkészítheti az értékelési parancsokat a hatókörben lévő összes SQL Server-példányra az áttelepítéshez. 

A nagyméretű ingatlanok, Data Migration Assistant (DMA) felmérések [összevonásához mostantól összevonható a Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-dma"></a>Újrabontási adatbázisok DMA-val

A DMA értékelésének eredményei alapján lehetséges, hogy a felhasználói adatbázis (ok) elvégzése és az áttelepítés után megfelelően működik. A DMA részletesen ismerteti az érintett objektumokat, valamint az egyes problémák megoldásához szükséges erőforrásokat. Javasoljuk, hogy az éles áttelepítés előtt az összes módosítást és a viselkedés változásait megoldják.

Az elavult funkciók esetében dönthet úgy, hogy a felhasználói adatbázis (oka) t az eredeti [kompatibilitási](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) módban futtatja, ha el kívánja kerülni ezeket a módosításokat, és felgyorsítja a Migrálás folyamatát. Ez azonban megakadályozza, [hogy az adatbázis-kompatibilitás frissítése](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) az elavult elemek feloldása előtt megtörténjen.

Erősen ajánlott, hogy az [áttelepítés után](#post-migration)a rendszer az összes DMA-javítást parancsfájlokkal és a cél SQL Server adatbázisára alkalmazza.

> [!CAUTION]
> Nem minden SQL Server verziója támogatja az összes kompatibilitási módot. Győződjön meg arról, hogy a [cél SQL Server verziója](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) támogatja a kiválasztott adatbázis-kompatibilitást. Például a SQL Server 2019 nem támogatja a 90-os szintű kompatibilitást (amely SQL Server 2005). Ezeknek az adatbázisoknak legalább a 100-es kompatibilitási szintre kell frissíteniük.
>

## <a name="migrate"></a>Migrate

Az áttelepítés előtti lépések elvégzése után készen áll a felhasználói adatbázisok és összetevők áttelepítésére. Telepítse át az adatbázisait az előnyben részesített [áttelepítési módszer](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)használatával.  

A következő lépésekkel végezheti el az áttelepítést a biztonsági mentés és a visszaállítás segítségével, vagy egy minimális állásidő-áttelepítést a biztonsági mentéssel és visszaállítással együtt a log Shipping használatával. 

### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

A következő lépésekkel végezheti el a normál áttelepítést a biztonsági mentés és a visszaállítás használatával: 

1. A követelmények alapján állítsa be az Azure-beli virtuális gépen található cél SQL Serverhoz való kapcsolódást. Lásd: [kapcsolódás SQL Server virtuális géphez az Azure-ban (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Az áttelepítésre szánt adatbázisokat használó alkalmazások szüneteltetése/leállítása. 
1. Győződjön meg arról, hogy a felhasználói adatbázis (ok) inaktív az [egyfelhasználós módban](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
1. Teljes adatbázis biztonsági mentése egy helyszíni helyre.
1. Másolja a helyszíni biztonságimásolat-fájl (oka) t a virtuális gépre a távoli asztal, az [Azure adatkezelő](/azure/data-explorer/data-explorer-overview)vagy a [AZCopy parancssori segédprogram](../../../storage/common/storage-use-azcopy-v10.md) (> 2 TB-os biztonsági másolatok ajánlott) használatával.
1. A teljes adatbázis biztonsági másolatának visszaállítása az Azure-beli virtuális gépen lévő SQL Serverra.

### <a name="log-shipping--minimize-downtime"></a>Napló szállítása (az állásidő csökkentése)

A következő lépésekkel végezheti el a minimális állásidő-áttelepítést a biztonsági mentés, visszaállítás és naplózás használatával: 

1. A követelmények alapján állítsa be a kapcsolatot az Azure-beli virtuális gépen a TARGET SQL Serverhoz. Lásd: [kapcsolódás SQL Server virtuális géphez az Azure-ban (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Győződjön meg arról, hogy az áttelepítendő helyszíni felhasználói adatbázis (ok) teljes vagy tömegesen naplózott helyreállítási modellben van.
1. Az adatbázis teljes biztonsági mentését elvégezheti egy helyszíni helyre, és módosíthatja a meglévő teljes adatbázis-biztonsági mentések feladatait, hogy [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) kulcsszót használjanak a naplózási lánc megőrzése érdekében.
1. Másolja a helyszíni biztonságimásolat-fájl (oka) t a virtuális gépre a távoli asztal, az [Azure adatkezelő](/azure/data-explorer/data-explorer-overview)vagy a [AZCopy parancssori segédprogram](../../../storage/common/storage-use-azcopy-v10.md) (>1 TB biztonsági másolatok ajánlott) használatával.
1. Az Azure-beli virtuális gépen lévő SQL Server teljes adatbázis-biztonsági mentése (i) visszaállítása.
1. Állítsa be a [naplózást](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) a helyszíni adatbázis és a cél-SQL Server között az Azure-beli virtuális gépen. Győződjön meg arról, hogy az adatbázis (ok) újrainicializálása nem történt meg, mert ez már befejeződött az előző lépésekben.
1. A rendszer **átvágja** a célkiszolgálóra. 
   1. Alkalmazások szüneteltetése/leállítása az áttelepítendő adatbázisok használatával. 
   1. Győződjön meg arról, hogy a felhasználói adatbázis (ok) inaktív az [egyfelhasználós módban](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
   1. Ha elkészült, végezze el a helyszíni adatbázis (ok) naplózási szolgáltatás által [vezérelt feladatátvételét](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) a SQL Server Azure-beli virtuális gépen való megcélzásához.



### <a name="migrating-objects-outside-user-databases"></a>A felhasználói adatbázis (ok) n kívüli objektumok áttelepítése

Előfordulhat, hogy további SQL Server objektumokat kell megadnia a felhasználói adatbázisok zökkenőmentes működéséhez az áttelepítés után. 

A következő táblázat egy lista-összetevőket és ajánlott áttelepítési módszereket tartalmaz, amelyeket a felhasználói adatbázisok áttelepítése előtt vagy után is el lehet végezni: 


| **Szolgáltatás** | **Összetevő** | **Áttelepítési módszer (ek)** |
| --- | --- | --- |
| **Adatbázisok** | Modellezés  | Parancsfájl SQL Server Management Studio |
|| TempDB | Tervezze meg a TempDB áthelyezését az [Azure VM ideiglenes lemezére (SSD](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)) a legjobb teljesítmény érdekében. Ügyeljen arra, hogy olyan virtuálisgép-méretet válasszon, amely elegendő helyi SSD-vel rendelkezik a TempDB való alkalmazkodáshoz. |
|| FileStream rendelkező felhasználói adatbázisok |  Az áttelepítéshez használja a [biztonsági mentési és visszaállítási](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) metódusokat. A DMA nem támogatja az FileStream-t használó adatbázisokat. |
| **Biztonság** | SQL Server és Windows-bejelentkezések | A [felhasználói bejelentkezések áttelepíthetők](/sql/dma/dma-migrateserverlogins)a DMA használatával. |
|| Szerepkörök SQL Server | Parancsfájl SQL Server Management Studio |
|| Kriptográfiai szolgáltatók | Ajánlott [Azure Key Vault szolgáltatás használatára konvertálni](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Ez az eljárás az [SQL VM erőforrás-szolgáltatót](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)használja. |
| **Kiszolgálói objektumok** | Biztonságimásolat-eszközök | Cserélje le az adatbázis biztonsági mentését [Azure Backup szolgáltatás](../../../backup/backup-sql-server-database-azure-vms.md) használatával, vagy írjon biztonsági mentést az [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) -ba (SQL Server 2012 SP1 CU2 +). Ez az eljárás az [SQL VM erőforrás-szolgáltatót](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)használja.|
|| Társított kiszolgálók | Parancsfájl SQL Server Management Studiosal. |
|| Kiszolgáló-eseményindítók | Parancsfájl SQL Server Management Studiosal. |
| **Replikáció** | Helyi kiadványok | Parancsfájl SQL Server Management Studiosal. |
|| Helyi előfizetők | Parancsfájl SQL Server Management Studiosal. |
| **Polybase** | Polybase | Parancsfájl SQL Server Management Studiosal. |
| **Felügyelet** | Database Mail | Parancsfájl SQL Server Management Studiosal. |
| **SQL Server Agent** | Feladatok | Parancsfájl SQL Server Management Studiosal. |
|| Riasztások | Parancsfájl SQL Server Management Studiosal. |
|| Operátorok | Parancsfájl SQL Server Management Studiosal. |
|| Legközelebbi | Parancsfájl SQL Server Management Studiosal. |
| **Operációs rendszer** | Fájlok, fájlmegosztás | Jegyezze fel az SQL-kiszolgálók által használt további fájlokat vagy fájlmegosztást, és replikálja az Azure VM-tárolót. |


## <a name="post-migration"></a>A migrálás után

Miután sikeresen elvégezte az áttelepítési szakaszt, ugorjon át az áttelepítés utáni feladatok sorozatán, hogy a lehető legzökkenőmentesebb és hatékony működést biztosítsa.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek megvalósítása bizonyos esetekben szükségessé teheti az alkalmazások módosítását.

A felhasználói adatbázis (ok) ra Migration Assistant ajánlott javítások alkalmazása bármely adatbázisra. Javasoljuk, hogy a konzisztencia biztosításához és az automatizálás engedélyezéséhez parancsfájlokat lehessen használni.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységek elvégzését tartalmazza:

1. **Ellenőrzési tesztek fejlesztése.**  SQL-lekérdezések használata az adatbázisok áttelepítésének teszteléséhez. Hozzon létre érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.
2. **Tesztkörnyezet beállítása.**  A tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.
3. **Ellenőrzési tesztek futtatása.**  Futtassa az ellenőrző teszteket a forráson és a célon, majd elemezze az eredményeket.
4. **Teljesítménytesztek futtatása.**  Futtasson teljesítménytesztet a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

> [!TIP]
> A [Database Experimentation Assistant (DEA)](/sql/dea/database-experimentation-assistant-overview) segítségével segítheti a cél SQL Server teljesítmény kiértékelését.


### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatpontossággal és a teljességgel kapcsolatos problémák egyeztetéséhez, valamint a számítási feladatok lehetséges teljesítménybeli problémáinak kezeléséhez.

További információt ezekről a problémákról és az azok enyhítésére szolgáló konkrét lépésekről a következő forrásokban talál:

- [Áttelepítés utáni érvényesítés és optimalizálási útmutató.](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [A teljesítmény finomhangolása az Azure SQL Virtual Machinesban](../../virtual-machines/windows/performance-guidelines-best-practices.md).
- [Azure Cost-optimalizálási központ](https://azure.microsoft.com/overview/cost-optimization/).

## <a name="next-steps"></a>Következő lépések

- A SQL Server alkalmazandó szolgáltatások rendelkezésre állásának vizsgálatához tekintse meg az [Azure globális infrastruktúra-központot](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

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