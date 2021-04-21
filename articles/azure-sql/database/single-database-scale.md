---
title: Önálló adatbázis erőforrásainak skálázása
description: Ez a cikk azt ismerteti, hogyan skálázhatóak az egyes adatbázisokhoz elérhető számítási és tárolási erőforrások a Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: ae1b3cc41d709c28ba517d672eb98cb60a837a8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779074"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Önálló adatbázis erőforrásainak skálázása az Azure SQL Database-ben

Ez a cikk azt ismerteti, hogyan skálázhatóak az egyes erőforrásokhoz elérhető számítási és tárolási erőforrások Azure SQL Database a kiépített számítási szinten. Alternatív megoldásként a [kiszolgáló nélküli számítási](serverless-tier-overview.md) szint automatikus számítást biztosít, és másodpercenként számláz a felhasznált számítási erőforrásokért.

A virtuális magok vagy DTUS-k számának első kiválasztása után dinamikusan skálázhat fel vagy le egy adatbázist a tényleges tapasztalatok alapján a következő használatával:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure Portal](single-database-manage.md#the-azure-portal)
* [PowerShell](/powershell/module/az.sql/set-azsqldatabase)
* [Azure CLI](/cli/azure/sql/db#az_sql_db_update)
* [REST API](/rest/api/sql/databases/update)


Az alábbi videó a szolgáltatási szint és a számítási méret dinamikus módosításával növeli az egy adatbázishoz elérhető DTUS-okat.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis méretének zsugorításra a nem használt terület felszabadítása miatt. További információ: [Manage file space in Azure SQL Database](file-space-manage.md).

## <a name="impact"></a>Hatás

A szolgáltatási szint vagy a számítási méret módosítása elsősorban a következő lépések elvégzésével jár:

1. Hozzon létre egy új számítási példányt az adatbázishoz. 

    Létrejön egy új számítási példány a kért szolgáltatási szinttel és számítási mérettel. A szolgáltatási szint és a számítási méret bizonyos kombinációi esetében létre kell hoznunk az adatbázis replikáját az új számítási példányban, amely adatok másolását is magában foglalja, és nagy mértékben befolyásolhatja a teljes késést. Ettől függetlenül az adatbázis online állapotban marad ebben a lépésben, és a kapcsolatok továbbra is az eredeti számítási példány adatbázisához lesznek irányítva.

2. A kapcsolatok útválasztásának váltása egy új számítási példányra.

    Az eredeti számítási példányban lévő adatbázis meglévő kapcsolatai el vannak dobva. Minden új kapcsolat létrejön az adatbázissal az új számítási példányban. A szolgáltatási szint és a számítási méret egyes kombinációi esetében az adatbázisfájlok le vannakválasztással és újrakapcsolódva a kapcsoló során.  A kapcsoló azonban a szolgáltatás rövid megszakítását eredményezheti, ha az adatbázis általában kevesebb mint 30 másodpercig, és gyakran csak néhány másodpercig nem érhető el. Ha hosszú ideig futó tranzakciók futnak a kapcsolatok megszakadása esetén, a megszakított tranzakciók helyreállítása hosszabb időt is igénybe vehet. [gyorsított adatbázis-helyreállítás](../accelerated-database-recovery.md) csökkentheti a hosszú ideig futó tranzakciók megszakításának hatását.

> [!IMPORTANT]
> A munkafolyamat bármely lépése során nem veszhet el adat. Győződjön meg arról, hogy [](troubleshoot-common-connectivity-issues.md) megvalósított valamilyen újrapróbálkozási logikát a szolgáltatást használó alkalmazásokban és összetevőkben Azure SQL Database amíg a szolgáltatásszint módosul.

## <a name="latency"></a>Késés

A szolgáltatási szint módosítása, az egyetlen adatbázis vagy rugalmas készlet számítási méretének skálázása, az adatbázisok rugalmas készletbe vagy rugalmas készletből való áthelyezésének, illetve az adatbázisok rugalmas készletek közötti áthelyezésének becsült késése a következőképpen paraméterezett:

|Szolgáltatási szint|Alapszintű egyetlen adatbázis,</br>Standard (S0-S1)|Alapszintű rugalmas készlet,</br>Standard (S2-S12), </br>általános célú adatbázis vagy rugalmas készlet létrehozása|Prémium vagy üzletileg kritikus adatbázis vagy rugalmas készlet|Rugalmas skálázás
|:---|:---|:---|:---|:---|
|**Alapszintű, </br> standard (S0-S1) adatbázis**|&bull;&nbsp;Állandó késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 5 perc|&bull;&nbsp;Az adatbázis adatmásolás által felhasznált területével arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc felhasznált terület GB-ban|&bull;&nbsp;Az adatbázis adatmásolás által felhasznált területével arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc felhasznált terület GB-ban|&bull;&nbsp;Az adatbázis adatmásolás által felhasznált területével arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc felhasznált terület GB-ban|
|**Alapszintű rugalmas készlet, </br> Standard (S2-S12), </br> általános célú adatbázis vagy rugalmas készlet**|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|&bull;&nbsp;Önálló adatbázisoknál az állandó késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Az egyes adatbázisoknál általában kevesebb mint 5 perc</br>&bull;&nbsp;Rugalmas készleteknél az adatbázisok számával arányosan|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|
|**Prémium vagy üzletileg kritikus adatbázis vagy rugalmas készlet**|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|
|**Rugalmas skálázás**|N.A.|N.A.|N.A.|&bull;&nbsp;Állandó késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 2 perc|

> [!NOTE]
> Emellett a Standard (S2-S12) és általános célú-adatbázisok esetén az adatbázisok rugalmas készletekbe vagy rugalmas készletekből való áthelyezésének késése arányos lesz az adatbázis méretével, ha az adatbázis Prémium fájlmegosztási ([PFS)](../../storage/files/storage-files-introduction.md)tárolót használ.
>
> Annak megállapításához, hogy egy adatbázis PFS-tárolót használ-e, hajtsa végre a következő lekérdezést az adatbázis környezetében. Ha az AccountType oszlop értéke vagy , az `PremiumFileStorage` `PremiumFileStorage-ZRS` adatbázis PFS-tárolót használ.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!NOTE]
> A zónaredundáns tulajdonság alapértelmezés szerint változatlan marad, amikor a üzletileg kritikus rétegre általános célú skáláz. A zónaredundania engedélyezése esetén a késés, valamint az általános célú-réteg zónaredundaniára való váltásának késése arányos lesz az adatbázis méretével.

> [!TIP]
> A folyamatban lévő műveletek figyelése: Műveletek kezelése az [SQL REST API](/rest/api/sql/operations/list)használatával, Műveletek kezelése a [parancssori](/cli/azure/sql/db/op)felülettel, Műveletek figyelése [a T-SQL használatával](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) és a következő két [PowerShell-parancs: Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) és [Stop-AzSqlDatabaseActivity.](/powershell/module/az.sql/stop-azsqldatabaseactivity)

## <a name="cancelling-changes"></a>Módosítások lemondása

A szolgáltatási szint módosítása vagy a számítási átméretezés művelete megszakítható.

### <a name="the-azure-portal"></a>Azure Portal

Az adatbázis áttekintő panelen lépjen az **Értesítések elemre,** és kattintson a csempére, jelezve, hogy folyamatban van a művelet:

![Folyamatban lévő művelet](./media/single-database-scale/ongoing-operations.png)

Ezután kattintson a Művelet megszakítása **feliratú gombra.**

![Folyamatban lévő művelet megszakítása](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

Egy PowerShell-parancssorból állítsa be a , és beállításokat, `$resourceGroupName` `$serverName` majd `$databaseName` futtassa a következő parancsot:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

- Ha magasabb szolgáltatási szintre vagy számítási méretre frissít, az adatbázis maximális mérete csak akkor növekszik, ha explicit módon nagyobb méretet (maxsize) ad meg.
- Egy adatbázis alacsonyabb szintre való átcsoportozásához az adatbázis felhasznált területének kisebbnek kell lennie a cél szolgáltatási szint és a számítási méret maximálisan megengedett méretében.
- Prémiumról Standard  szintre való  visszalépéskor további tárolási költség vonatkozik, ha a cél számítási méret támogatja az adatbázis maximális méretét (1), és (2) a maximális méret meghaladja a célként meghatározott számítási méret által tartalmazott tárolási mennyiséget. Ha például egy legfeljebb 500 GB-os P1-adatbázis S3-ra csökken, akkor további tárolási költség vonatkozik, mivel az S3 legfeljebb 1 TB méretet támogat, és a benne foglalt tárterület mérete csak 250 GB. A további tárterület tehát 500 GB – 250 GB = 250 GB. A további tárterület díjszabását a [díjszabást Azure SQL Database meg.](https://azure.microsoft.com/pricing/details/sql-database/) Ha a ténylegesen felhasznált terület kisebb, mint a benne foglalt tárterület, akkor ez a többletköltség elkerülhető az adatbázis maximális méretének a benne foglalt mennyiségre való csökkentésével.
- Ha az adatbázist engedélyezett georeplikációval frissíti, frissítse másodlagos adatbázisát a kívánt szolgáltatási szintre és számítási méretre az elsődleges adatbázis frissítése előtt (általános útmutatás a legjobb teljesítményhez). [](active-geo-replication-configure-portal.md) Ha egy másik kiadásra frissít, először a másodlagos adatbázist kell frissíteni.
- Ha olyan adatbázist [](active-geo-replication-configure-portal.md) tér vissza, amely esetében engedélyezve van a georeplikáció, a másodlagos adatbázis visszalépése előtt a kívánt szolgáltatási szintre és számítási méretre kell visszaminősülni az elsődleges adatbázisokat (általános útmutatás a legjobb teljesítményhez). Ha egy másik kiadásra való visszalépéskor először az elsődleges adatbázist kell visszaminősülni.
- A visszaállítási szolgáltatásajánlatok eltérőek a különböző szolgáltatási szintek esetében. Az Alapszintű szintre való  visszalépés esetén a biztonsági másolatok megőrzési ideje alacsonyabb lesz. Lásd: [Azure SQL Database biztonsági mentések.](automated-backups-overview.md)
- Az adatbázis új tulajdonságai csak a módosítások befejezéséig érvényesek.
- Ha az adatbázis méretezéséhez adatmásolásra van szükség (lásd: [Késés)](#latency)a szolgáltatási szint módosításakor, a méretezési művelettel egyidejűleg magas erőforrás-használat hosszabb skálázást eredményezhet. A [gyorsított adatbázis-helyreállítás (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts)használata esetén a hosszú ideig futó tranzakciók visszaállítási forrása nem jelentős a késésnek, de a nagy párhuzamos erőforrás-használat kevesebb számítási, tárolási és hálózati sávszélességű erőforrást hagy a skálázáshoz, különösen a kisebb számítási méretek esetében.

## <a name="billing"></a>Számlázás

Az adatbázis minden órában a legmagasabb szolgáltatási szintet + számítási méretet használja, amely az adott órában érvényes, függetlenül a használattól, vagy attól, hogy az adatbázis egy óránál rövidebb ideje aktív volt-e. Ha például egyetlen adatbázist hoz létre, és öt perccel később törli, a számla egy adatbázisórára vonatkozó díjat tükröz.

## <a name="change-storage-size"></a>Tárterület méretének módosítása

### <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

- A tárterület 1 GB-os növekményekkel a maximális adattárolási korlátig kiépítható. A minimális konfigurálható adattárolás 1 GB. Az egyes szolgáltatási célkitűzésekkel kapcsolatos maximális adattárolási [](resource-limits-vcore-single-databases.md) méretkorlátokat a virtuálismag-alapú vásárlási modellt használó, egymagos adatbázisok erőforráskorlátait és az egyes adatbázisok erőforráskorlátait a DTU vásárlási modellt használó, erőforráskorlátokkal kapcsolatos dokumentációs oldalakon találhatja [meg.](resource-limits-dtu-single-databases.md)
- Az egyetlen adatbázishoz való adattárolás a maximális méret növelésével vagy csökkentésével használhatja a [Azure Portal,](https://portal.azure.com) [a Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1)a [PowerShell,](/powershell/module/az.sql/set-azsqldatabase)az [Azure CLI](/cli/azure/sql/db#az_sql_db_update)vagy a [REST API.](/rest/api/sql/databases/update) Ha a maximális méret bájtban van megadva, akkor az 1 GB többszörösének kell lennie (1073741824 bájt).
- Az adatbázis adatfájlja által tárolható adatok mennyiségét a beállított maximális adattárolási méret korlátozza. Ezen a tárhelyen kívül a Azure SQL Database 30%-kal több tárterületet foglal le a tranzakciónaplóhoz.
- Azure SQL Database automatikusan lefoglal 32 GB-ot virtuális magonként az `tempdb` adatbázishoz. `tempdb` A a helyi SSD-tárolón található az összes szolgáltatási szinten.
- Az egyetlen adatbázis vagy rugalmas készlet tárolási ára az adattárolás és a tranzakciós napló tárolási összegének szorzata a szolgáltatási szint tárolási egységének árával. A költsége `tempdb` az ár része. A tárolási árral kapcsolatos részletekért tekintse meg a [Azure SQL Database díjszabását.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis méretének zsugorításra a nem használt terület felszabadítása miatt. További információ: [Manage file space in Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

- Az egy adatbázisra vonatkozó DTU-ár további költségek nélkül tartalmaz egy bizonyos tárterületet. A csomagban foglalt mennyiségen túl további tárhelyet is kiépíthet a maximális méretkorlátig 250 GB és 1 TB közötti növekményekben, majd 256 GB-os, 1 TB-nál nagyobb növekményekben. A benne foglalt tárolási mennyiségekkel és a maximális méretkorlátokkal lásd: Single database: Storage sizes and compute sizes (Egyetlen [adatbázis: Tárterület mérete és számítási méretek).](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Az egyetlen adatbázishoz külön tárterületet a maximális méret növelésével lehet kiépítni a Azure Portal, [a Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1)a [PowerShell,](/powershell/module/az.sql/set-azsqldatabase)az [Azure CLI](/cli/azure/sql/db#az_sql_db_update)vagy a [REST API.](/rest/api/sql/databases/update)
- Az egy adatbázishoz külön tárterület ára a további tárterület mennyisége és a szolgáltatási szint extra tárolásiegység-árának szorzata. A további tárterület árának részleteiért tekintse meg a Azure SQL Database [díjszabását.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis méretének zsugorításra a nem használt terület felszabadítása miatt. További információ: [Manage file space in Azure SQL Database](file-space-manage.md).

### <a name="geo-replicated-database"></a>Georeplikált adatbázis

A replikált másodlagos adatbázis adatbázisméretének módosítása az elsődleges adatbázis méretével. A rendszer ezután replikálja és megvalósítja ezt a változást a másodlagos adatbázisban is.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11 és P15 korlátozások 1 TB-osnál nagyobb maximális méret esetén

Jelenleg a prémium szinten több mint 1 TB tárhely érhető el az összes régióban, kivéve: Kelet-Kína, Észak-Kína, Közép-Németország és Északkelet-Németország. Ezekben a régiókban a prémium szintű csomag maximális tárterülete 1 TB-osra van korlátozva. Az alábbi szempontok és korlátozások vonatkoznak az 1 TB-osnál nagyobb maximális méretű P11 és P15 adatbázisokra:

- Ha egy P11- vagy P15-adatbázis maximális mérete valaha 1 TB-ot nagyobbra lett állítva, akkor csak P11- vagy P15-adatbázisba lehet visszaállítani vagy átmásolni.  Ezt követően az adatbázis más számítási méretre skálázható, feltéve, hogy az átméretezés során lefoglalt terület nem haladja meg az új számítási méret maximális méretkorlátját.
- Aktív georeplikációs forgatókönyvek esetén:
  - Georeplikációs kapcsolat beállítása: Ha az elsődleges adatbázis P11 vagy P15, akkor a másodlagos adatbázis(oka)nek is P11 vagy P15-nek kell lennie. Az alacsonyabb számítási méretet a rendszer elutasítja, mivel nem képesek 1 TB-osnál nagyobb támogatásra.
  - Az elsődleges adatbázis frissítése georeplikációs kapcsolatban: Ha egy elsődleges adatbázis maximális méretét 1 TB-on nagyobbra módosítja, ugyanaz a változás a másodlagos adatbázison is meg fog változni. Ahhoz, hogy az elsődleges verzió módosítása életbe lép, mindkét frissítésnek sikeresnek kell lennie. Az 1 TB-osnál több lehetőség régióra vonatkozó korlátozásai érvényesek. Ha a másodlagos régió egy 1 TB-osnál nem nagyobb támogatást támogató régióban van, az elsődleges régió nem lesz frissítve.
- Az Import/Export szolgáltatás használata 1 TB-osnál nagyobb P11/P15-adatbázisok betöltéséhez nem támogatott. A SqlPackage.exe [adatokat importálhat](database-import.md) [és exportálhat.](database-export.md)

## <a name="next-steps"></a>Következő lépések

Az általános erőforráskorlátokkal kapcsolatos további Azure SQL Database [virtuálismag-alapú](resource-limits-vcore-single-databases.md) erőforráskorlátok – egyetlen adatbázis és [Azure SQL Database DTU-alapú](resource-limits-dtu-single-databases.md)erőforráskorlátok – – egyetlen adatbázis.
