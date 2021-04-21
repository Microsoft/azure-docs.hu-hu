---
title: Feladatautomatizálás áttekintése rugalmas feladatokkal
description: Rugalmas feladatok használata a feladatautomatizáláshoz Transact-SQL- (T-SQL-) szkriptek egy vagy több adatbázison való futtatásához
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, contperf-fy21q3
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: conceptual
author: williamdassafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/1/2021
ms.openlocfilehash: 295889cf64d27761021dd09549a3366ea142516e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752029"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Felügyeleti feladatok automatizálása rugalmas feladatokkal (előzetes verzió)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Létrehozhat és ütemezhet rugalmas feladatokat, amelyek rendszeres időközönként végrehajthatók egy vagy több Azure SQL-adatbázison Transact-SQL- (T-SQL-) lekérdezések futtatásához és karbantartási feladatok végrehajtásához. 

Meghatározhat céladatbázist vagy adatbáziscsoportokat, ahol a feladatokat végre szeretné hajtani, és ütemezést is megadhat a feladatok futtatásához.
Egy feladat kezeli a céladatbázisba irányuló bejelentkezéseket. Emellett Transact-SQL-szkripteket is meghatározhat, fenntarthat és megőrizhet, amelyeket adatbázisok egy csoportján futtat.

Minden feladat naplózza a végrehajtás állapotát, és ha bármilyen hiba történik, automatikusan újrapróbálkozik a művelettel.

## <a name="when-to-use-elastic-jobs"></a>Mikor kell rugalmas feladatokat használni?

Több forgatókönyv is használhatja a rugalmas feladatautomatizálást:

- Automatizálhatja a felügyeleti feladatokat, és ütemezhet minden hétköznap, munkaidő után stb. futtatásokat.
  - Üzembe helyezhet sémamódosításokat, kezelheti a hitelesítő adatokat, teljesítményadatokat vagy bérlői (ügyfél-) telemetriát gyűjthet.
  - Frissítse a referenciaadatokat (az összes adatbázisra vonatkozó információkat), és töltse be az adatokat az Azure Blob Storage-ból.
- Úgy konfigurálhatja a feladatokat, hogy a rendszer egy adott adatbázis-gyűjteményen rendszeres időközönként hajtsa őket végre, például csúcsidőn kívül.
  - A lekérdezési adatokat az adatbázis-készletekből folyamatosan egy központi táblába gyűjtheti. A teljesítménylekérdezések folyamatosan végrehajthatók, illetve konfigurálhatók úgy, hogy további végrehajtandó feladatokat indítsanak el.
- Adatokat gyűjthet jelentéskészítéshez
  - Adatbázis-gyűjteményből származó adatok összesítése egyetlen céltáblában.
  - Olyan hosszabban futó adatfeldolgozási lekérdezéseket hajthat végre nagy adatbáziskészleteken, amilyen például az ügyfél-telemetria gyűjtése. A rendszer az eredményeket egyetlen céltáblában gyűjti össze a további elemzéshez.
- Adatmozgások 

### <a name="automation-on-other-platforms"></a>Automatizálás más platformokon

Vegye figyelembe a következő feladatütemezési technológiákat a különböző platformokon:

- **A rugalmas feladatok** olyan feladatütemezési szolgáltatások, amelyek egyéni feladatokat hajtnak végre a Azure SQL Database.
- **Az SQL Agent-feladatokat** az SQL Agent szolgáltatás hajtja végre, amely továbbra is a SQL Server feladatautomatizálására használatos, és a felügyelt példányok Azure SQL is tartalmazza. Az SQL Agent-feladatok nem érhetők el a Azure SQL Database.

A rugalmas feladatok [célként Azure SQL adatbázisokat,](sql-database-paas-overview.md) [](elastic-pool-overview.md)Azure SQL Database rugalmas készleteket és Azure SQL adatbázisok [szegmenstérképen való használatával.](elastic-scale-shard-map-management.md)

A T-SQL-szkript feladatok automatizálásához a SQL Server és Azure SQL Managed Instance sql [agentet érdemes megfontolni.](job-automation-managed-instances.md) 

A T-SQL-szkript feladatok automatizálásához a [](../../synapse-analytics/data-integration/concepts-data-factory-differences.md)Azure Synapse Analytics fontolja meg az ismétlődő eseményindítókat futtató folyamatokat, amelyek a következő [Azure Data Factory.](../../synapse-analytics/data-integration/concepts-data-factory-differences.md)

Érdemes figyelembe venni az SQL Agent (amely az SQL Server-ban és a SQL Managed Instance részeként érhető el) és a rugalmas adatbázis-feladat ügynök (amely A T-Azure SQL SQL-t az SQL Server és Azure SQL Managed Instance, Azure Synapse Analytics) adatbázisokon képes végrehajtani.

| |Rugalmas feladatok |SQL Agent |
|---------|---------|---------|
|**Hatókör** | A feladatügynökkel azonos Azure-felhőben Azure SQL Database és/vagy adattárházban bármilyen számú adatbázist használhat. A célok különböző kiszolgálókon, előfizetésben és/vagy régióban is lehetnek. <br><br>A célcsoportok önálló adatbázisokból vagy adattárházból, illetve egy kiszolgáló, készlet vagy szegmenstérkép összes adatbázisából (a feladat futásidejében dinamikusan számbavétele) is lehet. | Bármely önálló adatbázis, amely ugyanabban a példányban van, mint az SQL-ügynök. A többkiszolgálós adminisztrációs SQL Server Agent lehetővé teszi, hogy a fő-/célpéldányok koordinálják a feladatok végrehajtását, bár ez a szolgáltatás nem érhető el a felügyelt SQL-példányban. |
|**Támogatott API-k és eszközök** | Portál, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Rugalmas feladatcélok

**A rugalmas** feladatok lehetővé teszi egy vagy több T-SQL-szkript párhuzamos futtatását nagy számú adatbázison, ütemezés szerint vagy igény szerint.

Ütemezett feladatokat futtathat adatbázisok bármilyen kombinációján: egy vagy több önálló adatbázison, egy kiszolgálón található összes adatbázison, egy rugalmas készletben található összes adatbázison vagy szegmenstérképen, amelyek rugalmasan hozzáadhatóak vagy kizárhatnak adott adatbázisokat. A feladatok több kiszolgálón, több készleten, vagy akár különböző előfizetésekhez tartozó adatbázisokon is futtathatók. A kiszolgálókat és készleteket a rendszer futásidőben dinamikusan veszi számba, ezért a feladatok az összes olyan adatbázison futnak, amelyek a célcsoportban a végrehajtáskor megtalálhatók.

Az alábbi képen egy feladatügynök látható, amely különböző típusú célcsoportokon hajt végre feladatokat:

![Rugalmas feladat ügynökének elméleti modellje](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Rugalmas feladat összetevői

|Összetevő | Leírás (további részletek a táblázat alatt találhatók) |
|---------|---------|
|[**Rugalmas feladat ügynöke**](#elastic-job-agent) | Az az Azure-erőforrás, amelyet a feladatok futtatására és kezelésére hoz létre. |
|[**Feladat-adatbázis**](#elastic-job-database) | Egy adatbázis a Azure SQL Database, amely a feladatügynök által a feladattal kapcsolatos adatok, feladatdefiníciók stb. tárolására szolgál. |
|[**Célcsoport**](#target-group) | Azon kiszolgálók, készletek, adatbázisok és szegmenstérképek, amelyeken egy feladatot kíván futtatni. |
|[**Feladat**](#elastic-jobs-and-job-steps) | A feladat egy vagy több feladat lépésből álló munkaegység. A feladatlépések meghatározzák a futtatandó T-SQL-szkriptet, valamint a szkript végrehajtásához szükséges egyéb részleteket. |

#### <a name="elastic-job-agent"></a>Rugalmas feladatügynök

A rugalmasfeladat-ügynök a feladatok létrehozásához, futtatásához és kezeléséhez használt Azure-erőforrás. A rugalmasfeladat-ügynök egy Azure-erőforrás, amelyet a portálon hozhat létre (a [PowerShell](elastic-jobs-powershell-create.md) és a REST is támogatott).

A rugalmas **feladat ügynökének létrehozásához** egy meglévő adatbázisra van szükség a Azure SQL Database. Az ügynök ezt a meglévő Azure SQL Database [*feladat-adatbázisként konfigurálja.*](#elastic-job-database)

A rugalmasfeladat-ügynök használata ingyenes. A feladat-adatbázis számlázása ugyanolyan sebességgel történik, mint a Azure SQL Database.

#### <a name="elastic-job-database"></a>Rugalmas feladat adatbázisa

A *feladat-adatbázis* feladatok meghatározására, valamint a feladat-végrehajtások állapotának és előzményeinek nyomon követésére szolgál. A *feladat-adatbázis* az ügynök metaadatainak, naplóinak, eredményeinek, feladatdefinícióinak tárolására is használható, valamint számos hasznos tárolt eljárást és egyéb adatbázis-objektumot tartalmaz a feladatok T-SQL használatával való létrehozásához, futtatásához és kezeléséhez.

A jelenlegi előzetes verzióban a rugalmas Azure SQL Database létrehozásához egy meglévő adatbázisra (S0 vagy újabb) van szükség.

A *feladat-adatbázisnak* üresnek, S0-nek vagy magasabb szolgáltatási célkitűzésnek kell Azure SQL Database. A feladat-adatbázis  ajánlott szolgáltatási célja S1 vagy magasabb, de az optimális választás a feladat(ok) teljesítménybeli igényeitől függ: a feladat lépései, a feladatcélok száma és a feladatok futtatásának gyakorisága. 

Ha a feladat-adatbázison végzett műveletek [](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) a vártnál lassabbak, figyelje az adatbázis teljesítményét és erőforrás-kihasználtságát a feladatadatbázisban a lassúság Azure Portal vagy a DMV [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) közben. Ha egy erőforrás, például a CPU, az adat-I/O vagy a naplóírás kihasználtsága 100%-os, és a lassúság időszakával korrelál, [](service-tiers-vcore.md)fontolja meg az adatbázis magasabb szolgáltatási célokra való fokozatos skálázását (akár a [DTU-,](service-tiers-dtu.md) akár a virtuálismag-alapú modellben), amíg a feladatadatbázis teljesítménye megfelelően nem javul.

##### <a name="elastic-job-database-permissions"></a>Rugalmas feladatadatbázis engedélyei

Feladatügynök létrehozásakor a séma, a táblák és a *jobs_reader* nevű szerepkör a *feladat-adatbázisban* jön létre. A szerepkör a következő engedéllyel jön létre, és célja, hogy részletesebb hozzáférés-vezérlést biztosítson a rendszergazdák számára a feladatok monitorozásához:

|Szerepkörnév |'jobs' sémaengedélyek |'jobs_internal' sémaengedélyek |
|---------|---------|---------|
|**jobs_reader** | SELECT | Nincsenek |

> [!IMPORTANT]
> Mielőtt adatbázis-rendszergazdaként hozzáférést biztosítana a *feladat-adatbázishoz*, fontolja meg a lehetséges biztonsági következményeket. A feladatok létrehozására vagy szerkesztésére engedéllyel rendelkező rosszindulatú felhasználók létrehozhatnak vagy szerkeszthet olyan feladatokat, amelyek tárolt hitelesítő adatokkal csatlakoznak a rosszindulatú felhasználó által vezérelt adatbázishoz, ami lehetővé teheti a rosszindulatú felhasználó számára a hitelesítő adatok jelszavának megállapítását.

#### <a name="target-group"></a>Célcsoport

A *célcsoport* határozza meg az azokat az adatbázisokat, amelyeket az adott feladatlépés végre lesz hajtva. A célcsoport tetszőleges számban és kombinációban tartalmazhatja a következőket:

- **Logikai SQL-kiszolgáló** – ha meg van adva egy kiszolgáló, a feladat végrehajtásakor a kiszolgálón található összes adatbázis a csoport tagja lesz. A master adatbázis hitelesítő adatait meg kell adni ahhoz, hogy a csoportot a rendszer a feladat végrehajtása előtt számba vegye és frissítse. A logikai kiszolgálókkal kapcsolatos további információkért [lásd: Mi az a](logical-servers.md)kiszolgáló a Azure SQL Database és Azure Synapse Analytics? .
- **Rugalmas készlet** – Ha meg van adva egy rugalmas készlet, a feladat végrehajtásának időpontjában a rugalmas készletben található összes adatbázis a csoport tagja lesz. Kiszolgáló esetén a master adatbázis hitelesítő adatait meg kell adni ahhoz, hogy a csoport a feladat futtatása előtt frissíthető legyen.
- **Önálló adatbázis** – adjon meg egy vagy több egyedi adatbázist, amelyet a csoport részévé kíván tenni.
- **Szilánkleképés** – egy szegmenstérkép adatbázisai.

> [!TIP]
> A feladat végrehajtásakor a *dinamikus számbavételezés* újra kiértékeli a célcsoportokban található adatbázisokat, amelyek kiszolgálókat és készletet is tartalmaznak. A dinamikus számbavételezéssel biztosítható, hogy **a feladatok a feladat végrehajtásakor a kiszolgálón vagy a készletben létező összes adatbázison fussanak**. Az adatbázisok listájának futásidőben történő ismételt kiértékelése különösen hasznos olyan esetekben, amikor a készlet- vagy a kiszolgálótagság gyakran változik.

A készletek és az önálló adatbázisok megadhatók úgy, hogy a csoport részét képezzék vagy ki legyenek zárva a csoportból. Ez lehetővé teszi, hogy az adatbázisok tetszőleges kombinációjából hozzon létre céladatbázist. Hozzáadhat például egy kiszolgálót egy céladatbázishoz, miközben kizárja egy rugalmas készlet egyes adatbázisait (vagy egy egész rugalmas készletet).

A célcsoportok több előfizetésből és több régióból származó adatbázisokat is tartalmazhatnak. Vegye figyelembe, hogy a régiók közötti végrehajtás magasabb késéssel jár, mint a régión belüli.

Az alábbi példákban az látható, hogyan történik a különböző célcsoport-meghatározások dinamikus számba vétele a feladat végrehajtásának pillanatában, a feladat által futtatandó adatbázisok megadásához:

![Példák célcsoportokra](./media/job-automation-overview/targetgroup-examples1.png)

Az **1. példában** olyan célcsoport szerepel, amely egyedi adatbázisok listáját tartalmazza. Ha egy feladatlépést e célcsoport használatával hajt végre, akkor a szóban forgó műveletet a rendszer az összes adatbázison elvégzi.<br>
**A 2.** példában egy célcsoport látható, amely célként egy kiszolgálót tartalmaz. Ha egy feladatlépést e célcsoport használatával hajt végre, akkor a kiszolgáló dinamikus számba vételével a rendszer képes a kiszolgálón aktuálisan megtalálható adatbázisok listájának összeállítására. A szóban forgó műveletet a rendszer az összes adatbázison elvégzi.<br>
A **3. példában** a *2. példában* lévőhöz hasonló célcsoport szerepel, amelyből viszont egy adott adatbázis ki van zárva. A szóban forgó műveletet a rendszer *nem* végzi el a kizárt adatbázison.<br>
A **4. példában** olyan célcsoport szerepel, amely célhelyként egy rugalmas készletet tartalmaz. A *2. példához* hasonlóan a feladat futtatása során a készlet dinamikus számba vételével a rendszer képes a készletben lévő adatbázisok listájának összeállítására.
<br><br>

![További példák célcsoportra](./media/job-automation-overview/targetgroup-examples2.png)

**Az 5.** és **a 6.** példa olyan speciális forgatókönyveket mutat be, amelyekben a kiszolgálók, rugalmas készletek és adatbázisok kombinálhatók be- és kizárási szabályokkal.<br>
A **7. példa** azt mutatja be, hogy a feladat futtatása során a szilánkleképezésben szereplő szilánkok is kiértékelhetők.

> [!NOTE]
> Maga a feladat-adatbázis lehet a feladat célja. Ebben a forgatókönyvben a feladat-adatbázist a rendszer úgy kezeli, mint bármely más céladatbázist. A feladatfelhasználót létre kell hoznunk, és megfelelő engedélyeket kell biztosítani a feladat-adatbázisban, és a feladatfelhasználóhoz tartozó adatbázishoz tartozó hitelesítő adatoknak is léteznie kell a feladat-adatbázisban, mint bármely más céladatbázisban.

#### <a name="elastic-jobs-and-job-steps"></a>Rugalmas feladatok és feladat lépései

A *feladatok* ütemezetten vagy egyszeri feladatként végrehajtható munkaegységek. Egy feladat egy vagy több *feladatlépésből* áll.

Minden feladatlépés meghatároz egy végrehajtandó T-SQL-szkriptet, egy vagy több célcsoportot, amelyen a T-SQL-szkript futtatható és a feladatügynök számára a céladatbázishoz történő csatlakozáshoz szükséges hitelesítő adatokat. Minden feladatlépés testreszabható időtúllépési és újrapróbálkozási szabályzattal és választhatóan megadható kimeneti paraméterekkel rendelkezik.

#### <a name="job-output"></a>Feladat kimenete

A feladatok lépéseinek eredménye részletesen rögzítve lesz az egyes adatbázisokon, a szkript kimenete pedig a megadott táblában. Megadhat egy adatbázist a feladat által visszaadott adatok tárolásához.

#### <a name="job-history"></a>Feladatelőzmények

Tekintse meg a rugalmas feladatok végrehajtási előzményeit a *feladatadatbázisban* a következő [tábla lekérdezésével: jobs.job_executions.](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status) A rendszertisztítási feladat törli a 45 napnál régebbi végrehajtási előzményeket. A 45 napnál rövidebb előzmények eltávolításához hívja meg a sp_purge_jobhistory **tárolt** eljárást a *feladat-adatbázisban.*

#### <a name="job-status"></a>Feladat állapota

A rugalmas feladat-végrehajtásokat a *feladatadatbázisban* a következő tábla [lekérdezése jobs.job_executions.](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status) 

### <a name="agent-performance-capacity-and-limitations"></a>Az ügynök teljesítménye, kapacitása és korlátai

A rugalmas feladatok minimális számítási erőforrást használnak, amíg a hosszan futó feladatok befejezésére várakoznak.

Az adatbázisok célcsoportjának méretétől és az adott feladat kívánt végrehajtási idejétől (az egyidejű feldolgozók számától) függően az ügynök különböző mértékű számítási kapacitást és teljesítményt igényel a *feladat-adatbázis* részéről (több cél és több feladat esetén nagyobb számítási kapacitás szükséges).

A korlát jelenleg 100 egyidejű feladat.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>A feladatok a céladatbázis teljesítményét csökkentő hatásának megakadályozása

Ha szeretné elkerülni, hogy az erőforrások túlterheltek legyenek egy rugalmas SQL-készlet adatbázisain történő feladatvégrehajtás közben, akkor a feladatok esetén konfigurálhatja, hogy azok egyszerre legfeljebb hány adatbázison futhatnak.

## <a name="next-steps"></a>Következő lépések

- [Rugalmas feladatok létrehozása és kezelése](elastic-jobs-overview.md)
- [Rugalmas feladatok létrehozása és kezelése a PowerShell használatával](elastic-jobs-powershell-create.md)
- [Rugalmas feladatok létrehozása és kezelése a Transact-SQL (T-SQL) használatával](elastic-jobs-tsql-create-manage.md)
