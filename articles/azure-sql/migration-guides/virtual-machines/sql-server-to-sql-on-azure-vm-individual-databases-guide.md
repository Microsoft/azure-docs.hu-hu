---
title: 'SQL Server SQL Server az Azure Virtual Machines: áttelepítési útmutató'
description: Ebből az útmutatóból megtudhatja, hogyan telepítheti át az egyes SQL Server adatbázisait az Azure-beli Virtual Machines SQL Server.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e7fc4bacd73cec0fdab3117ada190fb7964b4282
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550897"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-virtual-machines"></a>Áttelepítési útmutató: az Azure-ban SQL Server SQL Server Virtual Machines

[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Ebből az útmutatóból megtudhatja, hogyan *derítheti fel*, *értékelheti* és *telepítheti át* a felhasználói adatbázisait SQL Serverról SQL Server Azure-beli Virtual Machinesre, ha a biztonsági mentést és visszaállítást, valamint a naplózási szolgáltatást használja, amely az értékeléshez [Data Migration Assistant](/sql/dma/dma-overview) használ.

A helyszíni vagy a szolgáltatásban futó SQL Server áttelepíthetők:

- SQL Server virtuális gépeken (VM).
- Amazon Web Services (AWS) EC2.
- Amazon-hoz kapcsolódó adatbázis-szolgáltatás (AWS RDS).
- Számítási motor (Google Cloud Platform [GCP]).

További információ a további áttelepítési stratégiákkal kapcsolatban: [SQL Server VM áttelepítésének áttekintése](sql-server-to-sql-on-azure-vm-migration-overview.md). Más áttelepítési útmutatókért lásd: az [Azure Database áttelepítési útmutatói](https://docs.microsoft.com/data-migration).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Az áttelepítési folyamatot bemutató diagram.":::

## <a name="prerequisites"></a>Előfeltételek

Az Azure Virtual Machines SQL Server való áttelepítéséhez a következő erőforrások szükségesek:

- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- Egy [Azure Migrate projekt](../../../migrate/create-manage-projects.md).
- Egy előkészített cél [SQL Server az Azure Virtual Machines](../../virtual-machines/windows/create-sql-vm-portal.md) példányon, amely a SQL Server forrásnál megegyező vagy újabb verzió.
- [Kapcsolódás az Azure és a](/azure/architecture/reference-architectures/hybrid-networking)helyszíni környezet között.
- [Megfelelő áttelepítési stratégia kiválasztása](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>A migrálás előtt

Az áttelepítés megkezdése előtt fel kell derítenie az SQL-környezet topológiáját, és fel kell mérnie a tervezett áttelepítés megvalósíthatóságát.

### <a name="discover"></a>Felderítés

Azure Migrate a helyszíni számítógépek áttelepítésének megfelelőségét méri, teljesítmény-alapú méretezést hajt végre, és a helyszínen történő futtatáshoz biztosít költségbecslést. Az áttelepítés megtervezéséhez használja a Azure Migrate a [meglévő adatforrások azonosításához és a](../../../migrate/concepts-assessment-calculation.md) SQL Server példányok által használt funkciók részleteit. Ez a folyamat a szervezet összes SQL Server-példányának azonosítására szolgál a használatban lévő verzió és szolgáltatások alapján.

> [!IMPORTANT]
> Ha a cél Azure-beli virtuális gépet választja a SQL Server-példányhoz, vegye figyelembe az [azure Virtual Machines SQL Server teljesítmény-irányelveit](../../virtual-machines/windows/performance-guidelines-best-practices.md).

További felderítési eszközökhöz tekintse meg az adatáttelepítési forgatókönyvekhez elérhető [szolgáltatásokat és eszközöket](../../../dms/dms-tools-matrix.md#business-justification-phase) .

### <a name="assess"></a>Kiértékelés

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Miután felderítette az összes adatforrást, a [Data Migration Assistant](/sql/dma/dma-overview) használatával mérje fel a helyszíni SQL Server példányokat az Azure Virtual Machines SQL Server egy példányára, hogy megértse a forrás-és a cél példányok közötti hézagokat.

> [!NOTE]
> Ha _nem_ frissíti a SQL Server verzióját, hagyja ki ezt a lépést, és lépjen az [áttelepítés](#migrate) szakaszra.

#### <a name="assess-user-databases"></a>Felhasználói adatbázisok felmérése

A Data Migration Assistant a SQL Server új verziójában a kompatibilitási problémák észlelésével segíti a migrációt egy modern adatplatformon. Data Migration Assistant a célzott környezet teljesítményének és megbízhatóságának növelését javasolja, továbbá lehetővé teszi a séma, az adatok és a bejelentkezési objektumok áthelyezését a forráskiszolgálóról a célkiszolgálóra.

További információ: [Assessment](/sql/dma/dma-migrateonpremsql).

> [!IMPORTANT]
>Az értékelés típusa alapján a forrás SQL Serverhoz szükséges engedélyek eltérőek lehetnek:
   > - A *szolgáltatás paritási* tanácsadójának a forrás SQL Server adatbázishoz való kapcsolódáshoz megadott hitelesítő adatoknak a *sysadmin (rendszergazda* ) kiszolgálói szerepkör tagjának kell lennie.
   > - A *kompatibilitási problémák* tanácsadójának a megadott hitelesítő adatoknak legalább `CONNECT SQL` , `VIEW SERVER STATE` és `VIEW ANY DEFINITION` engedélyekkel kell rendelkezniük.
   > - Data Migration Assistant a felmérés futtatása előtt kiemeli a kiválasztott tanácsadóhoz szükséges engedélyeket.

#### <a name="assess-the-applications"></a>Az alkalmazások értékelése

Az alkalmazási réteg általában a felhasználói adatbázisokhoz fér hozzá az adatmegőrzéshez és az adatmódosításhoz. A Data Migration Assistant kétféle módon tudja értékelni az alkalmazás adatelérési rétegét:

- Rögzített [kiterjesztett események](/sql/relational-databases/extended-events/extended-events) vagy a felhasználói adatbázisok [SQL Server Profiler nyomkövetésének](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) használatával. A [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-capture-trace) használatával olyan nyomkövetési naplót is létrehozhat, amely a/B teszteléshez is használható.
- Az [adathozzáférés áttelepítési eszközkészletének (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)használatával, amely a KÓDBAN található SQL-lekérdezések felderítését és értékelését biztosítja, és az alkalmazás forráskódját az egyik adatbázis-platformról a másikra telepíti át. Ez az eszköz támogatja a népszerű fájltípusokat (például C#, Java, XML és egyszerű szöveg). Az adathozzáférés áttelepítési eszközkészlet értékelésének végrehajtásáról a [Data Migration Assistant blogbejegyzés használata](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) című témakörben talál útmutatást.

A felhasználói adatbázisok értékelése során a Data Migration Assistant használatával [importálhatja](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) a rögzített nyomkövetési fájlokat vagy az adathozzáférés áttelepítési eszközkészletének fájljait.

#### <a name="assessments-at-scale"></a>Felmérések nagy léptékben

Ha több, Data Migration Assistant értékelést igénylő kiszolgálóval rendelkezik, automatizálhatja a folyamatot a [parancssori felület](/sql/dma/dma-commandline)használatával. Az illesztőfelület használatával előre felkészítheti az értékelési parancsokat a hatókörben lévő összes SQL Server-példányra az áttelepítéshez.

A nagyméretű ingatlanok közötti összegző jelentéskészítéshez a Data Migration Assistant értékelések mostantól [összevonható Azure Migrateba](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-data-migration-assistant"></a>Újrabontási adatbázisok Data Migration Assistant

A Data Migration Assistant értékelésének eredményei alapján előfordulhat, hogy számos javaslattal rendelkezik, amelyekkel biztosíthatja, hogy a felhasználói adatbázisok a Migrálás után megfelelően működjenek. Data Migration Assistant részletezi az érintett objektumokat és erőforrásokat az egyes problémák megoldásához. Az éles áttelepítés megkezdése előtt győződjön meg arról, hogy az összes változás és a viselkedés változásai megtörténnek.

Az elavult funkciók esetében dönthet úgy, hogy az eredeti [kompatibilitási](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) módban futtatja a felhasználói adatbázisokat, ha el szeretné kerülni ezeket a módosításokat, és felgyorsítja a Migrálás folyamatát. Ez a művelet megakadályozza az [adatbázis-kompatibilitás frissítését](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) az elavult elemek feloldása előtt.

Az [áttelepítés utáni](#post-migration) fázisban az összes Data Migration Assistant parancsfájlt kell használnia, és alkalmaznia kell őket a cél SQL Server adatbázisra.

> [!CAUTION]
> Nem minden SQL Server verziója támogatja az összes kompatibilitási módot. Győződjön meg arról, hogy a [cél SQL Server verziója](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) támogatja a kiválasztott adatbázis-kompatibilitást. Például SQL Server 2019 nem támogatja a 90 szintű kompatibilitású adatbázisok (amely SQL Server 2005). Ezeknek az adatbázisoknak legalább a 100-es kompatibilitási szintre kell frissíteniük.
>

## <a name="migrate"></a>Migrate

Az áttelepítés előtti lépések elvégzése után készen áll a felhasználói adatbázisok és összetevők áttelepítésére. Telepítse át az adatbázisait az előnyben részesített [áttelepítési módszer](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)használatával.

A következő szakaszokban ismertetjük az áttelepítést a biztonsági mentéssel és visszaállítással, vagy egy minimális állásidő-áttelepítéssel a biztonsági mentéssel és visszaállítással együtt a log Shipping használatával.

### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Szabványos áttelepítés végrehajtása a biztonsági mentés és a visszaállítás használatával:

1. A követelmények alapján állítsa be a kapcsolatot az Azure Virtual Machines SQL Server. További információ: [kapcsolódás SQL Server virtuális géphez az Azure-ban (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Szüneteltetheti vagy leállíthatja az áttelepítésre szánt adatbázisokat használó alkalmazásokat.
1. Gondoskodjon arról, hogy a felhasználói adatbázisok ne legyenek inaktívak [egyetlen felhasználói módban](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
1. Teljes adatbázis biztonsági mentése egy helyszíni helyre.
1. Másolja a helyszíni biztonságimásolat-fájlokat a virtuális gépre a távoli asztal, az [Azure adatkezelő](/azure/data-explorer/data-explorer-overview)vagy a [AZCopy parancssori segédprogram](../../../storage/common/storage-use-azcopy-v10.md)használatával. (2 TB-nál nagyobb biztonsági másolatok használata javasolt.)
1. Állítsa vissza a teljes adatbázis biztonsági másolatait az Azure Virtual Machines SQL Server.

### <a name="log-shipping-minimize-downtime"></a>Napló szállítása (az állásidő csökkentése)

A biztonsági mentéssel és visszaállítással, valamint a napló szállításával minimális állásidő-áttelepítés végrehajtásához:

1. A követelmények alapján állítsa be a kapcsolatot az Azure Virtual Machines SQL Server. További információ: [kapcsolódás SQL Server virtuális géphez az Azure-ban (Resource Manager)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Győződjön meg arról, hogy az áttelepítendő helyszíni felhasználói adatbázisok teljes vagy tömegesen naplózott helyreállítási modellben vannak.
1. Végezzen teljes biztonsági mentést egy helyszíni helyre, és módosítsa a meglévő teljes biztonsági mentések feladatait, hogy a [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) kulcsszó használatával őrizze meg a naplózási láncot.
1. Másolja a helyszíni biztonságimásolat-fájlokat a virtuális gépre a távoli asztal, az [Azure adatkezelő](/azure/data-explorer/data-explorer-overview)vagy a [AZCopy parancssori segédprogram](../../../storage/common/storage-use-azcopy-v10.md)használatával. (1 TB-nál nagyobb biztonsági másolatok használata javasolt.)
1. A teljes adatbázis biztonsági másolatának visszaállítása az Azure Virtual Machines SQL Serveron.
1. Állítsa be a [naplózást](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) a helyszíni adatbázis és az Azure Virtual Machines SQL Server között. Ügyeljen arra, hogy ne inicializálja újra az adatbázisokat, mert ez a feladat már befejeződött az előző lépésekben.
1. A rendszer átvágja a célkiszolgálóra.
   1. Az alkalmazások szüneteltetése vagy leállítása az áttelepítendő adatbázisok használatával.
   1. Gondoskodjon arról, hogy a felhasználói adatbázisok ne legyenek inaktívak [egyetlen felhasználói módban](/sql/relational-databases/databases/set-a-database-to-single-user-mode).
   1. Ha elkészült, végezze el a helyszíni adatbázisok naplózása [irányított feladatátvételét](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) az Azure Virtual Machines SQL Serverához.

### <a name="migrate-objects-outside-user-databases"></a>Objektumok migrálása a felhasználói adatbázisokon kívül

Az áttelepítés után további SQL Server objektumokra lehet szükség a felhasználói adatbázisok zökkenőmentes működéséhez.

Az alábbi táblázat az összetevők és a javasolt áttelepítési módszerek listáját tartalmazza, amelyeket a felhasználói adatbázisok áttelepítése előtt vagy után is el lehet végezni.

| **Szolgáltatás** | **Összetevő** | **Áttelepítési módszerek** |
| --- | --- | --- |
| **Adatbázisok** | Modellezés | Parancsfájl SQL Server Management Studiosal. |
|| TempDB | A legjobb teljesítmény érdekében tervezze meg a tempDB áthelyezését az [Azure VM ideiglenes lemezére (SSD)](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk). Ügyeljen arra, hogy olyan virtuálisgép-méretet válasszon, amely elegendő helyi SSD-vel rendelkezik a tempDB való alkalmazkodáshoz. |
|| FileStream rendelkező felhasználói adatbázisok | Az áttelepítéshez használja a [biztonsági mentési és visszaállítási](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) metódusokat. A Data Migration Assistant nem támogatja az FileStream-t használó adatbázisokat. |
| **Biztonság** | SQL Server és Windows-bejelentkezések | A [felhasználói bejelentkezések áttelepíthetők](/sql/dma/dma-migrateserverlogins)a Data Migration Assistant használatával. |
|| Szerepkörök SQL Server | Parancsfájl SQL Server Management Studiosal. |
|| Kriptográfiai szolgáltatók | [A Azure Key Vault használatára való konvertálás](../../virtual-machines/windows/azure-key-vault-integration-configure.md)javasolt. Ez az eljárás az [SQL VM erőforrás-szolgáltatót](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)használja. |
| **Kiszolgálói objektumok** | Biztonságimásolat-eszközök | Cserélje le az adatbázis biztonsági másolatát [Azure Backup](../../../backup/backup-sql-server-database-azure-vms.md)használatával, vagy írjon biztonsági mentést az [Azure Storage](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) -ba (SQL Server 2012 SP1 CU2 +). Ez az eljárás az [SQL VM erőforrás-szolgáltatót](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md)használja.|
|| Társított kiszolgálók | Parancsfájl SQL Server Management Studiosal. |
|| Kiszolgáló-eseményindítók | Parancsfájl SQL Server Management Studiosal. |
| **Replikáció** | Helyi kiadványok | Parancsfájl SQL Server Management Studiosal. |
|| Helyi előfizetők | Parancsfájl SQL Server Management Studiosal. |
| **PolyBase** | PolyBase | Parancsfájl SQL Server Management Studiosal. |
| **Felügyelet** | Adatbázisbeli levelezés | Parancsfájl SQL Server Management Studiosal. |
| **SQL Server Agent** | Feladatok | Parancsfájl SQL Server Management Studiosal. |
|| Riasztások | Parancsfájl SQL Server Management Studiosal. |
|| Operátorok | Parancsfájl SQL Server Management Studiosal. |
|| Legközelebbi | Parancsfájl SQL Server Management Studiosal. |
| **Operációs rendszer** | Fájlok, fájlmegosztás | Jegyezze fel az SQL-kiszolgálók által használt egyéb fájlokat vagy fájlmegosztást, és replikálja az Azure Virtual Machines-célt. |

## <a name="post-migration"></a>A migrálás után

Az áttelepítési fázis sikeres befejezése után el kell végeznie az áttelepítést követő feladatok sorozatát, hogy a lehető leggördülékenyebb és hatékony működést biztosítsa.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek a feladatnak a végrehajtásához bizonyos esetekben szükség lehet az alkalmazások változásaira.

A Data Migration Assistant által ajánlott javítások alkalmazása a felhasználói adatbázisokra. Ezeket a javításokat úgy kell megadnia, hogy biztosítsa az egységességet, és lehetővé tegye az automatizálást.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységekből áll:

1. **Ellenőrzési tesztek fejlesztése**: az adatbázis áttelepítésének teszteléséhez SQL-lekérdezéseket kell használnia. Hozzon létre érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.
1. **Tesztkörnyezet beállítása**: a tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.
1. **Ellenőrzési tesztek futtatása**: ellenőrző tesztek futtatása a forráson és a célon, majd az eredmények elemzése.
1. **Teljesítménytesztek futtatása**: futtasson teljesítményteszteket a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

> [!TIP]
> A [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview) segítségével segítheti a cél SQL Server teljesítmény kiértékelését.

### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez, a teljesség ellenőrzéséhez és a számítási feladatok lehetséges teljesítménybeli problémáinak kezeléséhez.

A problémákról és azok enyhítésének lépéseiről további információt a következő témakörben talál:

- [Áttelepítés utáni érvényesítés és optimalizálási útmutató](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Teljesítmény finomhangolása Azure SQL-alapú virtuális gépeken](../../virtual-machines/windows/performance-guidelines-best-practices.md)
- [Azure Cost Optimization-központ](https://azure.microsoft.com/overview/cost-optimization/)

## <a name="next-steps"></a>Következő lépések

- Az SQL Server-ra vonatkozó szolgáltatások rendelkezésre állásának ellenőrzését az [Azure globális infrastruktúra-központjában](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)találja.
- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egy olyan mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek és speciális feladatok ellátásához nyújt segítséget, lásd: [szolgáltatások és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).
- Az Azure SQL-ről további információt a következő témakörben talál:
   - [Üzembe helyezési lehetőségek](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server az Azure Virtual Machines szolgáltatásban](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure teljes tulajdonlási költség (TCO) – kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/)

- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszeréről és bevezetési ciklusáról, tekintse meg a következőt:
   - [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   - [Ajánlott eljárások az Azure-ba való Migrálás díjszabásához és méretezéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- A licenceléssel kapcsolatos további információkért lásd:
   - [Saját licenc használata a Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [A SQL Server 2008 és SQL Server 2008 R2 ingyenes kibővített támogatása](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- További információ az adatelérési réteg/B tesztelésének végrehajtásáról: a [Database Experimentation Assistant áttekintése](/sql/dea/database-experimentation-assistant-overview).
