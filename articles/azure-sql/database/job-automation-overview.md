---
title: A feladatok automatizálásának áttekintése rugalmas feladatokkal
description: Rugalmas feladatok használata a feladat-automatizáláshoz Transact-SQL (T-SQL) parancsfájlok futtatásához egy vagy több adatbázison keresztül
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
ms.openlocfilehash: 1f4bd28d2b95aeebe07fcad84d757327622d51f0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690430"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Felügyeleti feladatok automatizálása rugalmas feladatokkal (előzetes verzió)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Transact-SQL (T-SQL) lekérdezések futtatásához és a karbantartási feladatok végrehajtásához olyan rugalmas feladatokat hozhat létre és ütemezhet, amelyek egy vagy több Azure SQL-adatbázison keresztül rendszeres időközönként végrehajthatók. 

Meghatározhat céladatbázist vagy adatbáziscsoportokat, ahol a feladatokat végre szeretné hajtani, és ütemezést is megadhat a feladatok futtatásához.
Egy feladat kezeli a céladatbázisba irányuló bejelentkezéseket. Emellett Transact-SQL-szkripteket is meghatározhat, fenntarthat és megőrizhet, amelyeket adatbázisok egy csoportján futtat.

Minden feladat naplózza a végrehajtás állapotát, és ha bármilyen hiba történik, automatikusan újrapróbálkozik a művelettel.

## <a name="when-to-use-elastic-jobs"></a>Mikor használjon rugalmas feladatokat

A rugalmas feladatok automatizálását több esetben is használhatja:

- Automatizálja a felügyeleti feladatokat, és ütemezze őket a hétköznapok futtatásához, órák után stb.
  - Üzembe helyezhet sémamódosításokat, kezelheti a hitelesítő adatokat, teljesítményadatokat vagy bérlői (ügyfél-) telemetriát gyűjthet.
  - Frissítse a hivatkozási adatokat (az összes adatbázissal közös információ), töltse be az adatokat az Azure Blob Storage-ból.
- Úgy konfigurálhatja a feladatokat, hogy a rendszer egy adott adatbázis-gyűjteményen rendszeres időközönként hajtsa őket végre, például csúcsidőn kívül.
  - A lekérdezési adatokat az adatbázis-készletekből folyamatosan egy központi táblába gyűjtheti. A teljesítménylekérdezések folyamatosan végrehajthatók, illetve konfigurálhatók úgy, hogy további végrehajtandó feladatokat indítsanak el.
- Adatokat gyűjthet jelentéskészítéshez
  - Adatbázisokból származó összesített adatok egyetlen célként megadott táblába.
  - Olyan hosszabban futó adatfeldolgozási lekérdezéseket hajthat végre nagy adatbáziskészleteken, amilyen például az ügyfél-telemetria gyűjtése. A rendszer az eredményeket egyetlen céltáblában gyűjti össze a további elemzéshez.
- Adatáthelyezés 

### <a name="automation-on-other-platforms"></a>Automatizálás más platformokon

Vegye figyelembe a következő feladatütemezés-technológiákat a különböző platformokon:

- A **rugalmas feladatok** olyan feladatütemezés-szolgáltatások, amelyek a Azure SQL Database egy vagy több adatbázisában egyéni feladatokat hajtanak végre.
- Az SQL- **ügynök** feladatait az SQL Agent szolgáltatás hajtja végre, amely továbbra is használható a SQL Serverban található feladatok automatizálásához, és az Azure SQL felügyelt példányain is megtalálható. Az SQL-ügynök feladatai nem érhetők el Azure SQL Databaseban.

A rugalmas feladatok [Azure SQL Database-adatbázisokat](sql-database-paas-overview.md), [Azure SQL Database rugalmas készleteket](elastic-pool-overview.md)és Azure SQL-adatbázisokat is megcélozhat a szegmenses [térképeken](elastic-scale-shard-map-management.md).

A T-SQL-szkriptek feladatainak automatizálásához SQL Server és az Azure SQL felügyelt példányában tekintse meg az [SQL Agent](job-automation-managed-instances.md)összetevőt. 

A T-SQL parancsfájl-feladatoknak az Azure szinapszis Analyticsben való automatizálásához érdemes lehet [ismétlődő eseményindítókkal rendelkező folyamatokat](../../synapse-analytics/data-integration/concepts-data-factory-differences.md)figyelembe venni, amelyek [a Azure Data Factoryon alapulnak](../../synapse-analytics/data-integration/concepts-data-factory-differences.md).

Érdemes megfigyelni az SQL Agent (SQL Server és az SQL felügyelt példányának részeként elérhető) közötti különbségeket, valamint az adatbázis rugalmas feladattípusát (amely a T-SQL-T hajtja végre az Azure SQL-adatbázisokban vagy-adatbázisokban SQL Server és az Azure SQL felügyelt példányain, az Azure szinapszis Analyticsben).

| |Rugalmas feladatok |SQL-ügynök |
|---------|---------|---------|
|**Hatókör** | A Azure SQL Database és/vagy adattárházban lévő adatbázisok száma ugyanabban az Azure-felhőben, mint a feladatsor. A célok lehetnek különböző kiszolgálókon, előfizetéseken és/vagy régiókban. <br><br>A célcsoportok lehetnek önálló adatbázisok vagy adattárházak, vagy a kiszolgálók, készletek vagy szegmensek összes adatbázisa (a feladatok futtatókörnyezetében dinamikusan enumerálva). | Az SQL-ügynökkel megegyező példányban lévő egyes adatbázisok. A SQL Server Agent több kiszolgáló adminisztrációs funkciója lehetővé teszi a fő-és a cél példányok számára a feladatok végrehajtásának koordinálását, bár ez a funkció nem érhető el az SQL felügyelt példányain. |
|**Támogatott API-k és eszközök** | Portál, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Rugalmas feladatok célja

A **rugalmas feladatok** lehetővé teszik, hogy egy vagy több T-SQL-szkriptet párhuzamosan futtasson egy nagy számú adatbázisban, egy ütemezésben vagy igény szerint.

Az ütemezett feladatokat az adatbázisok tetszőleges kombinációja alapján futtathatja: egy vagy több egyéni adatbázis, egy kiszolgáló összes adatbázisa, egy rugalmas készletben lévő összes adatbázis, vagy egy szegmenses Térkép, a hozzáadott rugalmassággal, hogy belefoglalja vagy kizárja az adott adatbázist. A feladatok több kiszolgálón, több készleten, vagy akár különböző előfizetésekhez tartozó adatbázisokon is futtathatók. A kiszolgálókat és készleteket a rendszer futásidőben dinamikusan veszi számba, ezért a feladatok az összes olyan adatbázison futnak, amelyek a célcsoportban a végrehajtáskor megtalálhatók.

Az alábbi képen egy feladatügynök látható, amely különböző típusú célcsoportokon hajt végre feladatokat:

![Rugalmas feladat ügynökének elméleti modellje](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Rugalmas feladatokhoz tartozó összetevők

|Összetevő | Leírás (további részletek a táblázat alatt találhatók) |
|---------|---------|
|[**Rugalmas feladat ügynöke**](#elastic-job-agent) | Az az Azure-erőforrás, amelyet a feladatok futtatására és kezelésére hoz létre. |
|[**Feladat-adatbázis**](#elastic-job-database) | Egy adatbázis a Azure SQL Databaseban, amelyet a Feladatkezelő a feladatok kapcsolódó adatainak, a feladatdefiníciók stb. tárolására használ. |
|[**Célcsoport**](#target-group) | Azon kiszolgálók, készletek, adatbázisok és szegmenstérképek, amelyeken egy feladatot kíván futtatni. |
|[**Feladat**](#elastic-jobs-and-job-steps) | A feladatok olyan Munkaegységek, amelyek egy vagy több tevékenységi lépésből állnak. A feladatlépések meghatározzák a futtatandó T-SQL-szkriptet, valamint a szkript végrehajtásához szükséges egyéb részleteket. |

#### <a name="elastic-job-agent"></a>Rugalmas feladatok ügynöke

A rugalmasfeladat-ügynök a feladatok létrehozásához, futtatásához és kezeléséhez használt Azure-erőforrás. A rugalmasfeladat-ügynök egy Azure-erőforrás, amelyet a portálon hozhat létre (a [PowerShell](elastic-jobs-powershell-create.md) és a REST is támogatott).

A **rugalmas feladatok ügynökének** létrehozásához a Azure SQL Database meglévő adatbázisra van szükség. Az ügynök konfigurálja ezt a meglévő Azure SQL Database [*feladatot adatbázisként*](#elastic-job-database).

A rugalmasfeladat-ügynök használata ingyenes. A feladatok adatbázisa a Azure SQL Databaseban található összes adatbázissal megegyező sebességgel lett kiszámlázva.

#### <a name="elastic-job-database"></a>Rugalmas feladatokhoz tartozó adatbázis

A *feladat-adatbázis* feladatok meghatározására, valamint a feladat-végrehajtások állapotának és előzményeinek nyomon követésére szolgál. A *feladat-adatbázis* az ügynök metaadatait, naplóit, eredményeit, feladatait, valamint számos hasznos tárolt eljárást és egyéb, a T-SQL-T használó feladatok létrehozására, futtatására és kezelésére szolgáló adatbázis-objektumot is tartalmaz.

Az aktuális előzetes verzióhoz a rugalmas feladatok ügynökének létrehozásához Azure SQL Database (S0 vagy újabb) meglévő adatbázis szükséges.

A *feladat adatbázisának* tiszta, üres, S0 vagy magasabb szolgáltatási cél Azure SQL Databasenak kell lennie. A *feladat-adatbázis* javasolt szolgáltatási célja az S1 vagy a magasabb, de az optimális választás a feladat (ok) teljesítményének, a feladatok számának, a feladathoz tartozó célok számának és a gyakran futtatott feladatok számától függ. 

Ha a feladatra vonatkozó művelet a vártnál lassabban működik, [Figyelje](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) az adatbázis teljesítményét és az erőforrás-kihasználtságot a feladatban a Azure Portal vagy a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV használatával. Ha egy Erőforrás kihasználtsága, például a CPU, az adat IO vagy a log Write a 100%-ot közelíti meg, és a lassulási időszakokkal összefügg, érdemes lehet az adatbázis növekményes méretezését magasabb szolgáltatási célkitűzésekre (akár a [DTU-modellbe](service-tiers-dtu.md) , akár a [virtuális mag-modellbe](service-tiers-vcore.md)), amíg a feladatok adatbázisának teljesítménye nem megfelelő.

##### <a name="elastic-job-database-permissions"></a>Rugalmas feladatok adatbázisának engedélyei

Feladatügynök létrehozásakor a séma, a táblák és a *jobs_reader* nevű szerepkör a *feladat-adatbázisban* jön létre. A szerepkör a következő engedéllyel jön létre, és célja, hogy részletesebb hozzáférés-vezérlést biztosítson a rendszergazdák számára a feladatok monitorozásához:

|Szerepkörnév |'jobs' sémaengedélyek |'jobs_internal' sémaengedélyek |
|---------|---------|---------|
|**jobs_reader** | SELECT | Nincs |

> [!IMPORTANT]
> Mielőtt adatbázis-rendszergazdaként hozzáférést biztosítana a *feladat-adatbázishoz*, fontolja meg a lehetséges biztonsági következményeket. A feladatok létrehozásához vagy szerkesztéséhez engedéllyel rendelkező rosszindulatú felhasználók létrehozhatnak vagy szerkeszthetnek egy olyan feladatot, amely tárolt hitelesítő adatokat használ a rosszindulatú felhasználó vezérlője alá tartozó adatbázishoz való kapcsolódáshoz, ami lehetővé teheti a rosszindulatú felhasználó számára a hitelesítő adatok jelszavának meghatározását.

#### <a name="target-group"></a>Célcsoport

A *célcsoport* határozza meg az azokat az adatbázisokat, amelyeket az adott feladatlépés végre lesz hajtva. A célcsoport tetszőleges számban és kombinációban tartalmazhatja a következőket:

- **Logikai SQL Server** – ha meg van adva egy kiszolgáló, a rendszer a feladatok végrehajtásának időpontjában létező összes adatbázist a csoport részévé teheti. A master adatbázis hitelesítő adatait meg kell adni ahhoz, hogy a csoportot a rendszer a feladat végrehajtása előtt számba vegye és frissítse. További információ a logikai kiszolgálókról: [Mi az a kiszolgáló a Azure SQL Database és az Azure szinapszis Analytics?](logical-servers.md).
- **Rugalmas készlet** – Ha meg van adva egy rugalmas készlet, a feladat végrehajtásának időpontjában a rugalmas készletben található összes adatbázis a csoport tagja lesz. Kiszolgáló esetén a master adatbázis hitelesítő adatait meg kell adni ahhoz, hogy a csoport a feladat futtatása előtt frissíthető legyen.
- **Önálló adatbázis** – adjon meg egy vagy több egyedi adatbázist, amelyet a csoport részévé kíván tenni.
- Szegmenses **Térkép** – egy szegmenses Térkép adatbázisai.

> [!TIP]
> A feladat végrehajtásakor a *dinamikus számbavételezés* újra kiértékeli a célcsoportokban található adatbázisokat, amelyek kiszolgálókat és készletet is tartalmaznak. A dinamikus számbavételezéssel biztosítható, hogy **a feladatok a feladat végrehajtásakor a kiszolgálón vagy a készletben létező összes adatbázison fussanak**. Az adatbázisok listájának futásidőben történő ismételt kiértékelése különösen hasznos olyan esetekben, amikor a készlet- vagy a kiszolgálótagság gyakran változik.

A készletek és az önálló adatbázisok megadhatók úgy, hogy a csoport részét képezzék vagy ki legyenek zárva a csoportból. Ez lehetővé teszi, hogy az adatbázisok tetszőleges kombinációjából hozzon létre céladatbázist. Hozzáadhat például egy kiszolgálót egy céladatbázishoz, miközben kizárja egy rugalmas készlet egyes adatbázisait (vagy egy egész rugalmas készletet).

A célcsoportok több előfizetésből és több régióból származó adatbázisokat is tartalmazhatnak. Vegye figyelembe, hogy a régiók közötti végrehajtás magasabb késéssel jár, mint a régión belüli.

Az alábbi példákban az látható, hogyan történik a különböző célcsoport-meghatározások dinamikus számba vétele a feladat végrehajtásának pillanatában, a feladat által futtatandó adatbázisok megadásához:

![Példák célcsoportokra](./media/job-automation-overview/targetgroup-examples1.png)

Az **1. példában** olyan célcsoport szerepel, amely egyedi adatbázisok listáját tartalmazza. Ha egy feladatlépést e célcsoport használatával hajt végre, akkor a szóban forgó műveletet a rendszer az összes adatbázison elvégzi.<br>
A **2. példa** egy olyan célcsoportot mutat be, amely célként megadott kiszolgálót tartalmaz. Ha egy feladatlépést e célcsoport használatával hajt végre, akkor a kiszolgáló dinamikus számba vételével a rendszer képes a kiszolgálón aktuálisan megtalálható adatbázisok listájának összeállítására. A szóban forgó műveletet a rendszer az összes adatbázison elvégzi.<br>
A **3. példában** a *2. példában* lévőhöz hasonló célcsoport szerepel, amelyből viszont egy adott adatbázis ki van zárva. A szóban forgó műveletet a rendszer *nem* végzi el a kizárt adatbázison.<br>
A **4. példában** olyan célcsoport szerepel, amely célhelyként egy rugalmas készletet tartalmaz. A *2. példához* hasonlóan a feladat futtatása során a készlet dinamikus számba vételével a rendszer képes a készletben lévő adatbázisok listájának összeállítására.
<br><br>

![További példák a célcsoportokra](./media/job-automation-overview/targetgroup-examples2.png)

**5. példa** és a **6. példa** olyan speciális forgatókönyveket mutat be, amelyekben a kiszolgálók, a rugalmas készletek és az adatbázisok kombinálhatók a belefoglalási és kizárási szabályokkal.<br>
A **7. példa** azt mutatja be, hogy a feladat futtatása során a szilánkleképezésben szereplő szilánkok is kiértékelhetők.

> [!NOTE]
> A feladatok adatbázisa lehet egy adott feladatokhoz tartozó cél. Ebben az esetben a feldolgozói adatbázist ugyanúgy kezeli a rendszer, mint bármely más céladatbázis. A feladathoz tartozó felhasználónak létre kell hoznia és megfelelő engedélyeket kell biztosítania a feladathoz tartozó adatbázishoz, és az adatbázis hatókörön belüli hitelesítő adatainak is léteznie kell a feladathoz tartozó adatbázisban, ugyanúgy, mint bármely más céladatbázis esetében.

#### <a name="elastic-jobs-and-job-steps"></a>Rugalmas feladatok és feladatok lépései

A *feladatok* ütemezetten vagy egyszeri feladatként végrehajtható munkaegységek. Egy feladat egy vagy több *feladatlépésből* áll.

Minden feladatlépés meghatároz egy végrehajtandó T-SQL-szkriptet, egy vagy több célcsoportot, amelyen a T-SQL-szkript futtatható és a feladatügynök számára a céladatbázishoz történő csatlakozáshoz szükséges hitelesítő adatokat. Minden feladatlépés testreszabható időtúllépési és újrapróbálkozási szabályzattal és választhatóan megadható kimeneti paraméterekkel rendelkezik.

#### <a name="job-output"></a>Feladat kimenete

A feladatok lépéseinek eredménye részletesen rögzítve lesz az egyes adatbázisokon, a szkript kimenete pedig a megadott táblában. Megadhat egy adatbázist a feladat által visszaadott adatok tárolásához.

#### <a name="job-history"></a>Feladatelőzmények

A *feladatok adatbázisában* a rugalmas feladatok végrehajtásának előzményeit a [tábla Jobs.job_executions lekérdezésével](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status)tekintheti meg. A rendszertisztítási feladat törli a 45 napnál régebbi végrehajtási előzményeket. A 45 napnál nem régebbi előzmények törléséhez hívja meg az **sp_purge_history** tárolt eljárást a *feladat-adatbázisban*.

#### <a name="job-status"></a>Feladatok állapota

A rugalmas feladatok végrehajtásának figyelését a *feladatok adatbázisában* a [Jobs.job_executions tábla lekérdezésével](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status)végezheti el. 

### <a name="agent-performance-capacity-and-limitations"></a>Az ügynök teljesítménye, kapacitása és korlátai

A rugalmas feladatok minimális számítási erőforrást használnak, amíg a hosszan futó feladatok befejezésére várakoznak.

Az adatbázisok célcsoportjának méretétől és az adott feladat kívánt végrehajtási idejétől (az egyidejű feldolgozók számától) függően az ügynök különböző mértékű számítási kapacitást és teljesítményt igényel a *feladat-adatbázis* részéről (több cél és több feladat esetén nagyobb számítási kapacitás szükséges).

Jelenleg a korlát 100 egyidejű feladat.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>A feladatok a céladatbázis teljesítményét csökkentő hatásának megakadályozása

Ha szeretné elkerülni, hogy az erőforrások túlterheltek legyenek egy rugalmas SQL-készlet adatbázisain történő feladatvégrehajtás közben, akkor a feladatok esetén konfigurálhatja, hogy azok egyszerre legfeljebb hány adatbázison futhatnak.

## <a name="next-steps"></a>Következő lépések

- [Rugalmas feladatok létrehozása és kezelése](elastic-jobs-overview.md)
- [Rugalmas feladatok létrehozása és kezelése a PowerShell-lel](elastic-jobs-powershell-create.md)
- [Rugalmas feladatok létrehozása és kezelése a Transact-SQL (T-SQL) használatával](elastic-jobs-tsql-create-manage.md)