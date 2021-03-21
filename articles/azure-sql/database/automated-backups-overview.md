---
title: Automatikus, Geo-redundáns biztonsági másolatok
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: A Azure SQL Database és az Azure SQL felügyelt példánya néhány percenként automatikusan létrehoz egy helyi adatbázis biztonsági mentését, és az Azure olvasási hozzáférésű geo-redundáns tárterületet használja a Geo-redundancia érdekében.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, stevestein, danil
ms.date: 03/10/2021
ms.openlocfilehash: 5879c9107a0ab5a2ef150d119e8b5ac8e16ac01d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609923"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Automatikus biztonsági mentések – Azure SQL Database & SQL felügyelt példánya

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Mi az adatbázis biztonsági mentése?

Az adatbázis biztonsági mentései az üzletmenet folytonossága és a vész-helyreállítási stratégia alapvető részét képezik, mivel ezek az adatok a sérüléstől vagy törléstől védve vannak. Ezek a biztonsági másolatok lehetővé teszik az adatbázis visszaállítását egy adott időpontra a beállított megőrzési időtartamon belül. Ha az adatvédelmi szabályok megkövetelik, hogy a biztonsági másolatok hosszabb ideig is elérhetők legyenek (akár 10 évig), konfigurálhatja a [hosszú távú adatmegőrzést](long-term-retention-overview.md) mind az önálló, mind a készletezett adatbázisokhoz.

### <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

Mind a SQL Database, mind az SQL felügyelt példánya SQL Server technológiával [teljes biztonsági mentést](/sql/relational-databases/backup-restore/full-database-backups-sql-server) készít minden héten, a [különbözeti biztonsági mentéseket](/sql/relational-databases/backup-restore/differential-backups-sql-server) 12-24 óránként, a [tranzakciós napló biztonsági](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) mentését pedig 5 – 10 percenként. A tranzakciónapló biztonsági mentései gyakorisága a számítási mérettől és az adatbázis-tevékenység mennyiségétől függ.

Adatbázis visszaállításakor a szolgáltatás határozza meg, hogy a rendszer melyik teljes, különbözeti és tranzakciónapló biztonsági másolatokat szeretné visszaállítani.

### <a name="backup-storage-redundancy"></a>Biztonsági mentési tár redundancia

Alapértelmezés szerint a SQL Database és az SQL felügyelt példánya olyan földrajzi redundáns [tárolási blobokban](../../storage/common/storage-redundancy.md) tárolja az értékeket, amelyek egy [párosított régióba](../../best-practices-availability-paired-regions.md)replikálódnak. Ez segít az elsődleges régió biztonsági mentési tárhelyét érintő kimaradások elleni védelemben, és lehetővé teszi a kiszolgáló más régióba való visszaállítását vészhelyzet esetén. 

A Backup Storage-redundancia konfigurálásának lehetősége lehetővé teszi a helyileg redundáns, a zóna-redundáns vagy a földrajzilag redundáns tárolási Blobok közötti választást egy SQL felügyelt példány vagy egy SQL Database számára. Annak biztosítása érdekében, hogy az adatai ugyanabban a régióban maradnak, ahol a felügyelt példány vagy az SQL-adatbázis telepítve van, módosíthatja az alapértelmezett geo-redundáns biztonságimásolat-tárolási redundanciát, és konfigurálhatja a helyileg redundáns vagy a zóna-redundáns tárolási blobokat a biztonsági mentéshez. A tárolási redundancia-mechanizmusok több példányban tárolják az adataikat, így védve vannak a tervezett és nem tervezett eseményekről, beleértve az átmeneti hardverhiba, a hálózati vagy áramkimaradások vagy a súlyos természeti katasztrófákat. A konfigurált biztonságimásolat-tárolási redundancia az időponthoz kötött visszaállítási (PITR) és a hosszú távú biztonsági mentések (LTR-EK) esetében használt rövid távú biztonsági mentési adatmegőrzési beállításokra is vonatkozik. 

A SQL Database a biztonsági mentési tár redundancia konfigurálható az adatbázis létrehozásakor, vagy frissíthető egy meglévő adatbázis esetében is. a meglévő adatbázison végrehajtott módosítások csak a jövőbeli biztonsági másolatokra vonatkoznak. A meglévő adatbázis biztonsági mentési tárterületének frissítése után a módosítások alkalmazása akár 48 óráig is eltarthat. Vegye figyelembe, hogy a Geo-visszaállítás le van tiltva, amint egy adatbázis frissül a helyi vagy a zóna redundáns tároló használatára. 


> [!IMPORTANT]
> A biztonsági mentési tár redundancia konfigurálása a felügyelt példányok létrehozási folyamata során az erőforrás kiépítése után már nem lehetséges a tárterület-redundancia módosítása. 

> [!IMPORTANT]
> Zóna – a redundáns tárolás jelenleg csak [bizonyos régiókban](../../storage/common/storage-redundancy.md#zone-redundant-storage)érhető el. 

> [!NOTE]
> A Azure SQL Database konfigurálható biztonsági mentési tárolási redundancia jelenleg a Dél-Brazíliában elérhető nyilvános előzetes verzióban érhető el, és általánosan elérhető a Délkelet-ázsiai Azure-régióban. Ez a funkció még nem érhető el a nagy kapacitású szinten. 

### <a name="backup-usage"></a>Biztonsági mentés használata

A biztonsági másolatokat a következő célokra használhatja:

- **Meglévő adatbázis**  -  adott időpontban történő visszaállítása Azure Portal, Azure PowerShell, Azure CLI vagy REST API használatával [visszaállíthat egy meglévő adatbázist egy korábbi időpontra](recovery-using-backups.md#point-in-time-restore) a megőrzési időszakon belül. SQL Database esetén a művelet egy új adatbázist hoz létre az eredeti adatbázissal megegyező kiszolgálón, de az eredeti adatbázis felülírásának elkerüléséhez más nevet használ. A visszaállítás befejeződése után törölheti az eredeti adatbázist. Másik lehetőségként [átnevezheti](/sql/relational-databases/databases/rename-a-database) az eredeti adatbázist is, majd átnevezheti a visszaállított adatbázist az eredeti adatbázis nevére. Hasonlóképpen, a felügyelt SQL-példányok esetében ez a művelet létrehoz egy másolatot az adatbázisról ugyanazon az előfizetésben és ugyanazon a régión belül ugyanazon vagy eltérő felügyelt példányon.
- **Törölt adatbázis**  -  adott időpontban történő visszaállítása A [törölt adatbázis visszaállítása a törlés](recovery-using-backups.md#deleted-database-restore) időpontjára vagy a megőrzési időtartamon belül bármely időpontra. A törölt adatbázist csak ugyanazon a kiszolgálón vagy felügyelt példányon lehet visszaállítani, ahol az eredeti adatbázis létrejött. Adatbázis törlésekor a szolgáltatás a törlés előtt elvégzi a tranzakciós napló utolsó biztonsági mentését, hogy megakadályozza az adatvesztést.
- **Geo-visszaállítás**  -  [Adatbázis visszaállítása egy másik földrajzi régióba](recovery-using-backups.md#geo-restore). A Geo-visszaállítás lehetővé teszi, hogy egy földrajzi katasztrófa után helyreállítsa az adatbázist vagy a biztonsági mentéseket az elsődleges régióban. Egy új adatbázist hoz létre bármely meglévő kiszolgálón vagy felügyelt példányon bármely Azure-régióban.
   > [!IMPORTANT]
   > A Geo-visszaállítás csak az SQL-adatbázisok vagy a Geo-redundáns biztonsági mentési tárolóval konfigurált felügyelt példányok esetén érhető el.
- **Visszaállítás a hosszú távú biztonsági mentésből**  -  Egy adatbázis visszaállítása egy önálló adatbázis vagy készletezett adatbázis [adott hosszú távú biztonsági másolatából](long-term-retention-overview.md) , ha az adatbázis hosszú távú adatmegőrzési HÁZIRENDDEL (ltr) van konfigurálva. A LTR lehetővé teszi az adatbázis egy korábbi verziójának visszaállítását [a Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) vagy [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) segítségével, hogy megfeleljen a megfelelőségi kérésnek, vagy az alkalmazás egy régebbi verzióját futtassa. További információkért lásd: [Hosszú távú megőrzés](long-term-retention-overview.md).

A visszaállítás végrehajtásához tekintse meg [az adatbázis biztonsági másolatokból való visszaállítását](recovery-using-backups.md)ismertető témakört.

> [!NOTE]
> Az Azure Storage-ban a *replikálás* kifejezés a Blobok egyik helyről egy másikra történő másolását jelenti. Az SQL-ben az *adatbázis-replikáció* olyan különböző technológiákra utal, amelyek több másodlagos adatbázis szinkronizálására szolgálnak egy elsődleges adatbázissal.

Az alábbi példákkal kipróbálhatja a biztonsági mentési konfigurációt és a visszaállítási műveleteket:

| Művelet | Azure Portal | Azure PowerShell |
|---|---|---|
| **Biztonsági másolatok megőrzésének módosítása** | [SQL Database](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [SQL Managed Instance](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL Database](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[SQL Managed Instance](/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **A biztonsági másolatok hosszú távú megőrzésének módosítása** | [SQL Database](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>SQL felügyelt példánya – N/A  | [SQL Database](long-term-backup-retention-configure.md)<br/>[SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md)  |
| **Adatbázis visszaállítása egy adott időpontban** | [SQL Database](recovery-using-backups.md#point-in-time-restore)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md) | [SQL Database](/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL Managed Instance](/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Törölt adatbázis visszaállítása** | [SQL Database](recovery-using-backups.md)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL Database](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL Managed Instance](/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Adatbázis visszaállítása az Azure Blob Storage-ból** | SQL Database – N/A <br/>SQL felügyelt példánya – N/A  | SQL Database – N/A <br/>[SQL Managed Instance](../managed-instance/restore-sample-database-quickstart.md) |

## <a name="backup-scheduling"></a>Biztonsági mentés ütemezése

Az első teljes biztonsági mentést az új adatbázis létrehozása vagy visszaállítása után azonnal ütemezi a rendszer. Ez a biztonsági mentés általában 30 percen belül elvégezhető, de hosszabb időt is igénybe vehet, ha az adatbázis nagy méretű. A kezdeti biztonsági mentés például hosszabb időt vehet igénybe egy visszaállított adatbázison vagy egy adatbázis-másolaton, ami általában nagyobb, mint egy új adatbázis. Az első teljes biztonsági mentés után a rendszer az összes további biztonsági mentést automatikusan ütemezi és kezeli. Az adatbázis biztonsági másolatának pontos időzítését az SQL Database vagy az SQL felügyelt példány-szolgáltatás határozza meg, mivel a rendszer a teljes rendszerterhelést kiegyensúlyozza. Nem módosíthatja a biztonsági mentési feladatok ütemtervét, vagy letilthatja azokat.

> [!IMPORTANT]
> Új, visszaállított vagy másolt adatbázis esetén az időponthoz tartozó visszaállítási funkció elérhetővé válik a kezdeti teljes biztonsági mentést követő kezdeti tranzakciós napló biztonsági mentésének idejétől kezdve.

## <a name="backup-storage-consumption"></a>Biztonsági másolatok tárolásának felhasználása

A SQL Server biztonsági mentési és visszaállítási technológiával az adatbázisok egy adott időpontra való visszaállításához egy teljes biztonsági mentésből, opcionálisan egy különbözeti biztonsági másolatból és egy vagy több tranzakciónapló biztonsági másolatából álló, megszakítás nélküli biztonsági mentési láncra van szükség. A SQL Database és az SQL felügyelt példányának biztonsági mentési ütemterve minden héten egy teljes biztonsági mentést tartalmaz. Ezért ahhoz, hogy a PITR a teljes megőrzési időtartamon belül engedélyezzék, a rendszernek további teljes, különbözeti és tranzakciónapló-biztonsági másolatokat kell tárolnia a beállított megőrzési időtartamnál hosszabb ideig. 

Más szóval, a megőrzési időszak során bármely időpontra vonatkozóan olyan teljes biztonsági mentésre van szükség, amely régebbi a megőrzési időszak legrégebbi időpontjánál, valamint a különbözeti és tranzakciós naplók teljes biztonsági mentésének megszakítás nélküli lánca egészen a következő teljes biztonsági mentésig.

> [!NOTE]
> A PITR engedélyezéséhez a további biztonsági mentéseket a beállított megőrzési időtartamnál hosszabb ideig tárolja a rendszer. A biztonsági mentési tár díját az összes biztonsági mentés esetében azonos díjszabással számoljuk el. 

A PITR funkcióinak megadásához már nem szükséges biztonsági másolatok automatikusan törlődnek. Mivel a differenciált biztonsági másolatok és a naplók biztonsági mentései egy korábbi teljes biztonsági mentést igényelnek, a rendszer mindhárom biztonsági mentési típust hetente kiüríti.

Az összes adatbázishoz, beleértve a [TDE titkosított](transparent-data-encryption-tde-overview.md) adatbázisokat is, a biztonsági másolatok tömörítve vannak, és csökkentik a biztonsági mentési tárolók tömörítését Az átlagos biztonsági mentési tömörítési arány 3-4 alkalommal, azonban az adattermészettől függően jelentősen alacsonyabb vagy magasabb lehet, valamint az, hogy a rendszer az adatbázisban használja-e az adattömörítést.

A SQL Database és az SQL felügyelt példánya összesített értékként számítja ki az összes felhasznált biztonsági mentési tárterületet. Ezt az értéket óránként jelenteni kell az Azure számlázási folyamatnak, amelynek feladata az óránkénti használat összesítése az egyes hónapok végén kiszámított felhasználás kiszámításához. Az adatbázis törlése után a felhasználás a biztonsági mentések életkora szerint csökken, és törlődik. Ha minden biztonsági mentés törölve lett, és a PITR már nem lehetséges, a számlázás leáll.
   
> [!IMPORTANT]
> Az adatbázisok biztonsági másolatait a rendszer a PITR engedélyezéséhez is megőrzi, még akkor is, ha az adatbázist törölték. Az adatbázisok törlése és újbóli létrehozása közben a tárolási és a számítási költségek is megadhatók, így a biztonsági másolatok tárolási költségei megnövelhető, mivel a szolgáltatás minden törléskor megőrzi a törölt adatbázisok biztonsági másolatait. 

### <a name="monitor-consumption"></a>Használat figyelése

A virtuális mag-adatbázisok esetében a biztonsági mentési típusok (teljes, különbözeti és napló) által felhasznált tárterületet külön metrikaként kell jelenteni az adatbázis figyelési paneljén. Az alábbi ábra bemutatja, hogyan figyelheti meg a biztonsági másolatok tárolási felhasználását egy adott adatbázisra vonatkozóan. Ez a funkció jelenleg nem érhető el a felügyelt példányok számára.

![Az adatbázis biztonsági mentési felhasználásának figyelése a Azure Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>A biztonsági másolatok tárolásának finomhangolása

A biztonsági mentési tárterület-felhasználás legfeljebb az adatbázis maximális adatméretére vonatkozik. A biztonsági másolatok tárolásának túlzott mérete az egyes adatbázisok munkaterhelésével és maximális méretétől függ. A biztonsági mentési tár használatának csökkentése érdekében vegye figyelembe a következő hangolási technikák némelyikét:

- Csökkentse a [biztonsági másolatok megőrzési időtartamát](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) az igényeinek megfelelő minimálisra.
- Kerülje a nagyméretű írási műveleteket, például az index újraépítését, gyakrabban, mint amennyire szüksége van.
- Nagyméretű adatterhelési műveletek esetén érdemes lehet [fürtözött oszlopcentrikus indexeket](/sql/relational-databases/indexes/columnstore-indexes-overview) használni, és a kapcsolódó [ajánlott eljárásokat](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)követni, és/vagy csökkenteni a nem fürtözött indexek számát.
- A általános célú szolgáltatási szinten a kiépített adattárolás olcsóbb, mint a biztonsági mentési tár ára. Ha folyamatosan nagy mennyiségű biztonsági mentési tárolási költséggel rendelkezik, érdemes lehet növelni az adattárolást a biztonsági mentési tárolón való mentéshez.
- Az ideiglenes eredmények és/vagy átmeneti adatmennyiségek tárolásához az alkalmazás logikájában állandó táblák helyett használjon TempDB.
- Helyileg redundáns biztonsági mentési tárterület használata, ha lehetséges (például fejlesztési/tesztelési környezetek)

## <a name="backup-retention"></a>Biztonsági mentés megőrzése

Az új, visszaállított és másolt adatbázisok esetében a Azure SQL Database és az Azure SQL felügyelt példánya megfelelő biztonsági másolatokat tart fenn, hogy alapértelmezés szerint az elmúlt 7 napban engedélyezze a PITR. A nagy kapacitású és az alapszintű adatbázisok kivételével a 1-35 napos tartományban minden aktív adatbázis [biztonsági mentési megőrzési időszakát módosíthatja](#change-the-pitr-backup-retention-period) . A [biztonsági másolatok tárolásának felhasználásáról](#backup-storage-consumption)a PITR engedélyezéséhez tárolt biztonsági másolatok a megőrzési időtartamnál régebbiek lehetnek. Csak az Azure SQL felügyelt példányai esetében lehet beállítani a PITR biztonsági mentés megőrzési arányát, miután az 0-35 nap tartományba törölte az adatbázist. 

Ha töröl egy adatbázist, a rendszer ugyanúgy tartja a biztonsági mentéseket, mint egy online adatbázis számára a megadott megőrzési időtartammal. Törölt adatbázis biztonsági másolatának megőrzési időszaka nem módosítható.

> [!IMPORTANT]
> Ha töröl egy kiszolgálót vagy felügyelt példányt, akkor a kiszolgálón vagy a felügyelt példányon lévő összes adatbázis is törlődik, és nem állítható helyre. A törölt kiszolgálók vagy a felügyelt példányok nem állíthatók vissza. Ha azonban beállította a hosszú távú adatmegőrzést (LTR) egy adatbázishoz vagy egy felügyelt példányhoz, a hosszú távú adatmegőrzési biztonsági másolatok nem törlődnek, és az adatbázisokat egy másik kiszolgálón vagy felügyelt példányon, ugyanabban az előfizetésben, a hosszú távú adatmegőrzési biztonsági mentés időpontjában lehet visszaállítani.

Az elmúlt 1-35 napon belüli PITR biztonsági mentését időnként rövid távú biztonsági másolatok megőrzésének is nevezik. Ha a biztonsági mentéseket továbbra is meg kell őriznie, mint a 35 napos maximális rövid távú megőrzési időszak, akkor a [hosszú távú adatmegőrzést](long-term-retention-overview.md)is engedélyezheti.

### <a name="long-term-retention"></a>Hosszú távú megőrzés

Mind a SQL Database, mind az SQL felügyelt példány esetében az Azure Blob Storage-ban akár 10 évig is beállíthatja a teljes biztonsági mentés hosszú távú megőrzését (LTR). A LTR házirend konfigurálása után a teljes biztonsági mentések automatikusan egy másik Storage-tárolóba kerülnek. A különböző megfelelőségi követelmények kielégítése érdekében a heti, havi és/vagy évenkénti teljes biztonsági mentésekhez eltérő megőrzési időt választhat. A tárterület-használat a LTR biztonsági mentések kiválasztott gyakoriságával és megőrzési idejével függ. A [ltr díjszabási számológép](https://azure.microsoft.com/pricing/calculator/?service=sql-database) használatával megbecsülheti a ltr-tároló költségeit.

> [!IMPORTANT]
> Egy meglévő Azure SQL Database biztonsági mentési tárolási redundancia frissítése csak az adatbázishoz készített jövőbeli biztonsági másolatokra vonatkozik. Az adatbázishoz tartozó összes meglévő LTR-biztonsági másolat továbbra is a meglévő Storage-blobban marad, és az új biztonsági másolatok a kért tár blob-típusban lesznek tárolva. 

További információ a LTR: a [biztonsági másolatok hosszú távú megőrzése](long-term-retention-overview.md).

## <a name="backup-storage-costs"></a>Biztonsági másolatok tárolási költségei

A biztonsági mentési tár díja változó, és a vásárlási modelltől (DTU vagy virtuális mag), a biztonsági mentési tárterülettől, valamint a régiótól is függ. A biztonsági mentési tár díja GB/hó használatban van, díjszabásért lásd: [Azure SQL Database díjszabási](https://azure.microsoft.com/pricing/details/sql-database/single/) oldal és az [Azure SQL felügyelt példányának díjszabása](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) oldal.

> [!NOTE]
> Az Azure-számla csak a felesleges biztonsági mentési tárterületet fogja megjeleníteni, nem a teljes biztonsági mentési tárterületet. Ha például egy feltételezett helyzetben kiosztott 4TB rendelkezik, akkor az ingyenes biztonsági mentési tárolóhely 4TB kap. Abban az esetben, ha a biztonsági mentési tárolóhely összesen 5,8 TB-át használta, az Azure-számla csak 1.8 TB-ot fog megjelenni, mivel a rendszer csak a felesleges biztonsági mentési tárterületet számítja fel.

### <a name="dtu-model"></a>DTU-modell

A DTU-modellben az adatbázisok és a rugalmas készletek biztonsági mentési tárterületére nem számítunk fel további díjat. A biztonsági mentési tár ára az adatbázis vagy a készlet árának részét képezi.

### <a name="vcore-model"></a>Virtuálismag-alapú modell

SQL Database önálló adatbázisai esetén a biztonsági mentési tár összege az adatbázis maximális adattárolási méretének 100%-a, külön díj nélkül biztosítjuk. Rugalmas készletek és felügyelt példányok esetén a biztonsági mentési tár összege a készlet maximális adattárolásának 100 százalékával vagy a példányok maximális tárolási méretével egyenlő, külön díj nélkül biztosítva. 

Az önálló adatbázisok esetében ez az egyenlet a teljes számlázható biztonsági mentési tár használatának kiszámítására szolgál:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

A készletezett adatbázisok esetében a teljes számlázható biztonsági mentési tárterület összesített mérete a készlet szintjén történik, és a következőképpen számítjuk ki:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

Felügyelt példányok esetén a teljes számlázható biztonsági mentési tár mérete a példány szintjén összesítve történik, és a következőképpen számítható ki:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

A teljes számlázható biztonsági mentési tár (ha van ilyen) összege GB/hó lesz, a biztonsági mentési tárterület által felhasznált redundancia mértékének megfelelően. Ez a biztonsági mentési tárterület az egyes adatbázisok, rugalmas készletek és felügyelt példányok munkaterhelésével és méretétől függ. A nagy mértékben módosított adatbázisok nagyobb különbözeti és naplózott biztonsági mentéssel rendelkeznek, mivel a biztonsági másolatok mérete az adatváltozások mennyiségével arányos. Ezért az ilyen adatbázisok esetében a biztonsági mentési költségek is magasabbak lesznek.

A SQL Database és az SQL felügyelt példánya a teljes számlázható biztonsági mentési tárterületet összesített értékként számítja ki az összes biztonsági mentési fájlban. Az Azure számlázási folyamat minden órában ezt az értéket jelenti, amely összesíti ezt az óránkénti használatot, hogy minden hónap végén lekérje a biztonsági mentési tárterület felhasználását. Ha töröl egy adatbázist, a biztonsági mentési tár fogyasztása fokozatosan csökken, mert a régebbi biztonsági mentések le lesznek törölve. Mivel a differenciált biztonsági másolatok és a naplók biztonsági mentései egy korábbi teljes biztonsági mentést igényelnek, a rendszer mindhárom biztonsági mentési típust hetente kiüríti. Az összes biztonsági másolat törlése után a számlázás leáll. 

Egyszerűsített Példaként tegyük fel, hogy egy adatbázis 744 GB-nyi biztonsági mentési tárterülettel rendelkezik, és hogy ez az összeg állandó marad az egész hónapban, mert az adatbázis teljesen inaktív. Ha át szeretné alakítani ezt az összesített tárterület-használatot óránkénti használatra, ossza fel 744,0-ra (havonta 31 nap * 24 óra/nap). SQL Database jelentést küld az Azure számlázási folyamatnak, amelyet az adatbázis 1 GB PITR biztonsági mentést használt óránként, állandó sebességgel. Az Azure-számlázás összesíti ezt a felhasználást, és az egész hónapra vonatkozóan 744 GB-ot fog megjeleníteni. A költségeket a régiójában lévő összeg/GB/hó arány alapján számítjuk fel.

Most egy összetettebb példa. Tegyük fel, hogy ugyanazt az üresjárati adatbázist a hónap közepétől számított 7 nap és 14 nap között is megnöveli. Ez a növekmény a teljes biztonsági mentési tárterület 1 488 GB-ra való megkettőzését eredményezi. SQL Database 1 GB-nyi használatot jelent az 1 – 372 (a hónap első felében). A használatot 2 GB-ra, 373 – 744 (a hónap második felében) fogja jelenteni. Ez a használat a 1 116 GB/hó utolsó számlára lesz összesítve.

A tényleges biztonsági mentési számlázási forgatókönyvek összetettebbek. Mivel az adatbázis változásainak aránya függ a munkaterheléstől, és az idő múlásával változik, az egyes különbözeti és naplózott biztonsági másolatok mérete is változhat, ami miatt az óránkénti biztonsági mentés a tárterület-felhasználásra megfelelően ingadozik. Továbbá minden különbözeti biztonsági mentés az adatbázisban az utolsó teljes biztonsági mentés óta végrehajtott összes változást tartalmazza, így az összes különbözeti biztonsági mentés teljes mérete fokozatosan nő a hét folyamán, majd a teljes, differenciált és naplózott biztonsági másolatok kiesése után meredeken csökken. Ha például egy nagy mennyiségű írási tevékenység, például az index újraépítése a teljes biztonsági mentés befejezése után futott le, akkor az index újraépítésének módosításait a rendszer az Újraépítés ideje alatt, a következő különbözeti biztonsági másolatban, valamint minden, a következő teljes biztonsági mentésig vett különbözeti biztonsági másolatban fogja tartalmazni. Az utóbbi forgatókönyvben a nagyobb adatbázisok esetében a szolgáltatás optimalizálása egy teljes biztonsági mentést készít, nem pedig különbözeti biztonsági mentést, ha a különbözeti biztonsági másolat túlzottan nagyméretű lenne. Ez csökkenti az összes különbözeti biztonsági mentés méretét a következő teljes biztonsági mentésig.

Az egyes biztonsági mentési típusok (teljes, különbözeti, tranzakciós napló) teljes biztonsági mentését a használat [monitorozása](#monitor-consumption)című témakörben leírtak szerint figyelheti.

### <a name="backup-storage-redundancy"></a>Biztonsági mentési tár redundancia

A biztonsági mentési tár redundancia a következő módon befolyásolja a biztonsági mentési költségeket:
- helyileg redundáns ár = x
- zóna – redundáns ár = 1,25 x
- Geo-redundáns ár = 2x

A biztonsági mentési tár díjszabásával kapcsolatos további információkért tekintse meg [Azure SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/single/) és az [Azure SQL felügyelt példányának díjszabási oldalát](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

> [!IMPORTANT]
> Az SQL felügyelt példányának konfigurálható biztonságimásolat-tárolási redundancia az összes Azure-régióban elérhető, és jelenleg a Délkelet-ázsiai Azure-régióban érhető el, csak SQL Database. Felügyelt példány esetében csak a felügyelt példány létrehozása folyamat során adható meg. Az erőforrás kiépítése után nem módosítható a biztonságimásolat-tárolási redundancia beállítás.

### <a name="monitor-costs"></a>Költségek figyelése

A biztonsági másolatok tárolási költségeinek megismeréséhez lépjen a **Cost Management + számlázás** elemre a Azure Portalban, válassza a **Cost Management** lehetőséget, majd válassza a **Cost Analysis** elemet. Válassza ki a kívánt előfizetést **hatókörként**, majd szűrje azt az időszakot és szolgáltatást, amelyre kíváncsi.

Adjon hozzá egy szűrőt a **szolgáltatás neveként**, majd válassza az **SQL Database** elemet a legördülő listából. A **mérési alkategória** szűrővel válassza ki a szolgáltatás számlázási számlálóját. Önálló adatbázis vagy rugalmas adatbázis-készlet esetén válassza a **PITR egy-egy vagy rugalmas készletet**. Felügyelt példány esetén válassza a **mi PITR biztonsági mentési tár** lehetőséget. A **tárolási** és a **számítási** alkategóriák is hasznosak lehetnek, de nem a biztonsági másolatok tárolási költségeihez vannak társítva.

![A biztonsági mentési tár költséghatékonyságának elemzése](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > A mérőórák csak a jelenleg használatban lévő számlálók számára láthatók. Ha a számláló nem érhető el, akkor valószínű, hogy a kategória jelenleg nincs használatban. Például a felügyelt példányok számlálói nem lesznek jelen azon ügyfelek számára, akik nem rendelkeznek felügyelt példánnyal. Hasonlóképpen, a tárolási számlálók nem lesznek láthatók a tárolót nem használó erőforrások esetében. 

## <a name="encrypted-backups"></a>Titkosított biztonsági másolatok

Ha az adatbázis TDE van titkosítva, a biztonsági másolatok automatikusan titkosítva maradnak, beleértve a LTR biztonsági mentéseket is. Az Azure SQL összes új adatbázisa alapértelmezés szerint engedélyezve van a TDE. További információ a TDE-ről:  [transzparens adattitkosítás SQL Database & SQL felügyelt példányával](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Biztonsági mentés integritása

Az Azure SQL Engineering csapata folyamatosan teszteli az automatikus adatbázis-biztonsági mentések visszaállítását. (Ez a teszt jelenleg nem érhető el az SQL felügyelt példányain.) Az időponthoz tartozó visszaállításkor az adatbázisok a DBCC CHECKDB UTASÍTÁST is megkapják.

A sértetlenség-ellenőrzés során észlelt problémák miatt a mérnöki csapat riasztást küld. További információ: az [adatintegritás SQL Databaseban](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

A biztonsági másolatok integritásának biztosításához az összes adatbázis-biztonsági mentést az ELLENŐRZŐÖSSZEG beállítással végezheti el.

## <a name="compliance"></a>Megfelelőség

Ha az adatbázist egy DTU-alapú szolgáltatási rétegből virtuális mag-alapú szolgáltatási rétegre telepíti át, a rendszer megőrzi a PITR megőrzését annak érdekében, hogy az alkalmazás adathelyreállítási házirendje ne legyen veszélyeztetve. Ha az alapértelmezett megőrzés nem felel meg a megfelelőségi követelményeknek, módosíthatja a PITR megőrzési időtartamát. További információ: [a PITR biztonsági mentés megőrzési időtartamának módosítása](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>A PITR biztonsági mentés megőrzési idejének módosítása

Az alapértelmezett PITR biztonsági mentési megőrzési időszakot a Azure Portal, a PowerShell vagy a REST API használatával módosíthatja. Az alábbi példák bemutatják, hogyan módosíthatja a PITR megőrzését 28 napra.

> [!WARNING]
> Ha csökkenti az aktuális megőrzési időtartamot, akkor elveszíti az új megőrzési időtartamnál régebbi időpontokra való visszaállítás lehetőségét. Azok a biztonsági másolatok, amelyekre már nincs szükség az új megőrzési időtartamon belüli PITR biztosításához, törlődnek. Ha az aktuális megőrzési időtartamot emeli, akkor az új megőrzési időtartamon belül nem lehet azonnal visszaállítani a régebbi időpontokra. Az idő múlásával a rendszer megkezdi a biztonsági mentések megőrzését.

> [!NOTE]
> Ezek az API-k csak a PITR megőrzési időszakot érintik. Ha a LTR-t konfigurálta az adatbázishoz, az nem lesz hatással. További információ a LTR megőrzési időtartamának módosításáról: [hosszú távú adatmegőrzés](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>A PITR biztonsági mentés megőrzési időszakának módosítása a Azure Portal használatával

Ha módosítani szeretné a PITR biztonsági mentési megőrzési időtartamát az aktív adatbázisokhoz a Azure Portal használatával, lépjen a kiszolgálóra vagy a felügyelt példányra azokra az adatbázisokra, amelyek megőrzési időtartamát módosítani kívánja. A bal oldali ablaktáblán válassza a **biztonsági másolatok** lehetőséget, majd válassza az **adatmegőrzési szabályzatok** fület. Válassza ki azokat az adatbázis (oka) t, amelyeken módosítani szeretné a PITR biztonsági másolatának megőrzését. Ezután válassza a **megőrzés beállítása** a műveleti sávon lehetőséget.



#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

![PITR-megőrzés, kiszolgáló szintjének módosítása](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

![PITR-megőrzés, felügyelt példány módosítása](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>A PITR biztonsági mentés megőrzési időszakának módosítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell-AzureRM modult továbbra is támogatja a SQL Database és az SQL felügyelt példánya, de az az. SQL modul jövőbeli fejlesztése is. További információ: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul parancsaihoz tartozó argumentumok lényegében azonosak a AzureRm-modulokban szereplő parancsokkal.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Az aktív Azure SQL Database-adatbázisok PITR biztonsági mentési megőrzésének módosításához használja a következő PowerShell-példát.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

Az **egyes aktív** SQL felügyelt példány-adatbázisok PITR biztonsági mentési megőrzésének módosításához használja a következő PowerShell-példát.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

Ha módosítani szeretné a PITR biztonsági mentésének megőrzését az **összes aktív** SQL felügyelt példány adatbázisaiban, használja a következő PowerShell-példát.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

Az **egyes törölt** SQL felügyelt példány-adatbázisok PITR biztonsági mentésének módosításához használja a következő PowerShell-példát.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Ha módosítani szeretné a PITR biztonsági mentésének megőrzését az **összes törölt** SQL felügyelt példány-adatbázis esetében, használja a következő PowerShell-példát.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Nulla (0) a napok megőrzése azt jelzi, hogy a biztonsági mentés azonnal törlődik, és a rendszer már nem őrzi meg a törölt adatbázisokat.
Ha a PITR biztonsági mentésének megőrzése csökkentve lett egy törölt adatbázisra vonatkozóan, az már nem növelhető.

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>A PITR biztonsági mentés megőrzési időszakának módosítása a REST API használatával

#### <a name="sample-request"></a>Példa a kérelemre

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>A kérés törzse

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Mintaválasz

Állapotkód: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

További információ: a [biztonsági másolatok megőrzésének REST API](/rest/api/sql/backupshorttermretentionpolicies).

#### <a name="sample-request"></a>Példa a kérelemre

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>A kérés törzse

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Mintaválasz

Állapotkód: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

További információ: a [biztonsági másolatok megőrzésének REST API](/rest/api/sql/backupshorttermretentionpolicies).

## <a name="configure-backup-storage-redundancy"></a>A biztonsági mentési tár redundancia konfigurálása

> [!NOTE]
> Az SQL felügyelt példányának biztonsági mentéséhez konfigurálható tárolási redundancia csak a felügyelt példány létrehozása folyamat során adható meg. Az erőforrás kiépítése után nem módosítható a biztonságimásolat-tárolási redundancia beállítás. SQL Database a szolgáltatás nyilvános előzetes verziója jelenleg Brazília déli régiójában érhető el, és általánosan elérhető a Délkelet-ázsiai Azure-régióban. 

A felügyelt példányok biztonsági mentési tárolási redundancia csak a példány létrehozásakor állítható be. SQL Database esetén beállítható az adatbázis létrehozásakor, vagy frissíthető egy meglévő adatbázishoz. Az alapértelmezett érték a Geo-redundáns tárterület. A helyileg redundáns, a Zone-redundáns és a Geo-redundáns biztonsági mentési tár díjszabása közötti különbségeket a [felügyelt példány díjszabása lapon](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)tekintheti meg.

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>A biztonsági mentési tár redundancia konfigurálása a Azure Portal használatával

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Azure Portal a biztonsági mentési tár redundancia a **Create SQL Database** panelen konfigurálható. Ez a lehetőség a biztonsági mentési tároló-redundancia szakaszban érhető el. 
![A Create SQL Database panel megnyitása](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

A Azure Portal a biztonsági mentési tárhely-redundancia módosításának lehetősége az SQL felügyelt példányának létrehozásakor az **alapok** lapon, a **felügyelt példány konfigurálása** lehetőséggel elérhető, a **számítási és tárolási** panelen érhető el.
![A számítási és tárolási konfiguráció megnyitása – panel](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

Keresse meg a biztonsági mentési tár redundancia lehetőséget a **számítási és tárolási** panelen.
![A biztonsági mentési tár redundancia konfigurálása](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>A biztonsági mentési tár redundancia konfigurálása a PowerShell használatával

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Ha új adatbázis létrehozásakor szeretné konfigurálni a biztonságimásolat-tárolási redundanciát, megadhatja a-BackupStoageRedundancy paramétert. A lehetséges értékek a Geo, a zóna és a helyi. Alapértelmezés szerint az összes SQL-adatbázis geo-redundáns tárterületet használ a biztonsági mentésekhez. A Geo-visszaállítás le van tiltva, ha egy adatbázis helyi vagy zóna redundáns biztonsági mentési tárolóval jön létre. 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

További részletekért látogasson el a [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).

Egy meglévő adatbázis biztonsági mentési tárterület-redundancia frissítéséhez használhatja a-BackupStorageRedundancy paramétert. A lehetséges értékek a Geo, a zóna és a helyi.
Vegye figyelembe, hogy a módosítások az adatbázison való alkalmazása akár 48 óráig is eltarthat. A Geo-redundáns biztonsági mentési tárolóról a helyi vagy a zóna redundáns tárterületre való áttérés letiltja a Geo-visszaállítást. 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

A részletekért látogasson el a [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)

> [!NOTE]
> Ha a-BackupStorageRedundancy paramétert adatbázis-visszaállítással, adatbázis-másolattal vagy másodlagos műveletekkel kívánja használni, használja az Azure PowerShell-verziót az az. SQL 2.11.0. 


#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

A biztonsági mentési tár redundancia konfigurálásához a felügyelt példányok létrehozásakor megadhatja a-BackupStoageRedundancy paramétert. A lehetséges értékek a Geo, a zóna és a helyi.

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

További részletekért látogasson el a [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)webhelyre.

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>A biztonsági mentési tár redundancia kikényszerített Azure Policy használata

Ha olyan adattárolási követelményekkel rendelkezik, amelyeknek egyetlen Azure-régióban kell megőriznie az összes adatát, érdemes lehet kikényszeríteni a SQL Database vagy felügyelt példányra vonatkozó, a Azure Policy használatával redundáns vagy helyileg redundáns biztonsági mentést. A Azure Policy egy olyan szolgáltatás, amellyel olyan szabályzatokat hozhat létre, rendelhet hozzá és kezelhet, amelyek szabályokat alkalmaznak az Azure-erőforrásokra. Azure Policy segít megőrizni ezeket az erőforrásokat a vállalati szabványoknak és a szolgáltatói szerződéseknek. További információ: [Azure Policy áttekintése](../../governance/policy/overview.md). 

### <a name="built-in-backup-storage-redundancy-policies"></a>Beépített biztonságimásolat-tárolási redundancia-szabályzatok 

Az új beépített szabályzatok hozzáadását követően az előfizetés vagy az erőforráscsoport szintjén rendelhető hozzá az új adatbázis (ok) vagy példány (ok) a Geo-redundáns biztonsági mentési tárolóval való létrehozásához. 

[A GRS biztonsági mentési redundancia használatának elkerüléséhez SQL Database](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)

[Az SQL felügyelt példányainak kerülniük kell a GRS biztonsági mentési redundancia használatát](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

A SQL Database és a felügyelt példány beépített szabályzat-definícióinak teljes listája [itt](./policy-reference.md)található.

Ha szervezeti szinten szeretné kikényszeríteni az adattárolási követelményeket, ezeket a szabályzatokat hozzá lehet rendelni egy előfizetéshez. Miután ezek hozzá lettek rendelve egy előfizetési szinten, a megadott előfizetésben lévő felhasználók nem tudnak adatbázist vagy felügyelt példányt létrehozni a Geo-redundáns biztonsági mentési tárolóval Azure Portal vagy Azure PowerShell használatával. 

> [!IMPORTANT]
> Az Azure-szabályzatok nem lesznek kikényszerítve az adatbázisok T-SQL-n keresztüli létrehozásakor. Ha T-SQL-T használó adatbázis létrehozásakor szeretné kényszeríteni az adattárolást, [használja a "local" vagy a "Zone" utasítást a Create Database utasításban BACKUP_STORAGE_REDUNDANCY ismerhető](/sql/t-sql/statements/create-database-transact-sql#create-database-using-zone-redundancy-for-backups).

Megtudhatja, hogyan rendelhet hozzá házirendeket a [Azure Portal](../../governance/policy/assign-policy-portal.md) vagy [Azure PowerShell](../../governance/policy/assign-policy-powershell.md) használatával


## <a name="next-steps"></a>Következő lépések

- Az adatbázis biztonsági mentései az üzletmenet folytonossága és a vész-helyreállítási stratégia alapvető részét képezik, mivel az adatok véletlen sérüléstől vagy törléstől való védelme érdekében szükségesek. SQL Database az üzletmenet-folytonossági megoldásokkal kapcsolatos további információkért lásd: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- További információ arról, hogyan [állíthatja vissza az adatbázist egy adott időpontra a Azure Portal használatával](recovery-using-backups.md).
- További információ arról, hogyan [állíthatja vissza az adatbázist egy adott időpontra a PowerShell használatával](scripts/restore-database-powershell.md).
- Az Azure Blob Storage-ban az Azure Portal használatával történő automatikus biztonsági mentések konfigurálásával, kezelésével és visszaállításával kapcsolatos további információkért lásd: [a biztonsági másolatok hosszú távú megőrzésének kezelése a Azure Portal használatával](long-term-backup-retention-configure.md).
- További információ arról, hogyan konfigurálható, kezelhető és állítható vissza az automatikus biztonsági mentések az Azure Blob Storage-ban a PowerShell használatával: a [biztonsági másolatok hosszú távú megőrzésének kezelése a PowerShell használatával](long-term-backup-retention-configure.md).
- Az Azure SQL felügyelt példányain tárolt biztonsági másolatok tárolásával kapcsolatos tudnivalókat lásd: [a biztonsági másolatok tárolásának felhasználása a felügyelt példányon](https://aka.ms/mi-backup-explained).
- Ha szeretné megtudni, hogyan finomíthatja a biztonsági másolatok tárolásának megőrzését és költségeit az Azure SQL felügyelt példányain, tekintse [meg a biztonsági mentési tárolási költségek finomhangolása felügyelt példányon](https://aka.ms/mi-backup-tuning)című témakört
