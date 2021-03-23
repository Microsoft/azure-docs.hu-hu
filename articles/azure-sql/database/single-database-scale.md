---
title: Önálló adatbázis erőforrásainak skálázása
description: Ez a cikk azt ismerteti, hogyan méretezhetők a számítási és tárolási erőforrások a Azure SQL Database önálló adatbázisai számára.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 02/22/2021
ms.openlocfilehash: ce8d4bf36524e3e7e7b3b8c974aa189fa000d845
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773249"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Önálló adatbázis erőforrásainak skálázása az Azure SQL Database-ben

Ez a cikk azt ismerteti, hogyan méretezheti a számítási és a tárolási erőforrásokat a kiépített számítási szinten lévő Azure SQL Database számára. Azt is megteheti, hogy a [kiszolgáló nélküli számítási](serverless-tier-overview.md) csomag számítási automatikus skálázást és másodpercenkénti számlázást biztosít a felhasznált számítási feladatokhoz.

A virtuális mag vagy a DTU számának kezdeti kiválasztását követően a [Azure Portal](single-database-manage.md#the-azure-portal), a [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), a [PowerShell](/powershell/module/az.sql/set-azsqldatabase), az [Azure CLI](/cli/azure/sql/db#az-sql-db-update)vagy a [REST API](/rest/api/sql/databases/update)használatával dinamikusan méretezheti az önálló adatbázisokat a tényleges tapasztalatok alapján.

A következő videó bemutatja, hogyan lehet dinamikusan módosítani a szolgáltatási szintet és a számítási méretet, hogy az elérhető DTU egyetlen adatbázishoz lehessen emelni.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

## <a name="impact"></a>Hatás

A szolgáltatási réteg vagy a számítási méret módosítása főleg a következő lépéseket hajtja végre:

1. Hozzon létre egy új számítási példányt az adatbázishoz. 

    A rendszer létrehoz egy új számítási példányt a kért szolgáltatási szintjével és számítási mérettel. A szolgáltatási szintek és a számítási méret változásainak bizonyos kombinációi esetén az adatbázis replikáját az új számítási példányban kell létrehozni, amely magában foglalja az adatok másolását, és jelentősen befolyásolhatja a teljes késést. Függetlenül attól, hogy az adatbázis online állapotban marad ebben a lépésben, és a kapcsolatok továbbra is az eredeti számítási példány adatbázisára lesznek irányítva.

2. Váltás a kapcsolatok útválasztását egy új számítási példányra.

    A rendszer eldobott egy meglévő kapcsolatot az adatbázissal az eredeti számítási példányban. Minden új kapcsolat létrejön az adatbázishoz az új számítási példányban. A szolgáltatási szintek és a számítási méret változásainak bizonyos kombinációi esetén az adatbázisfájlok le lesznek választva, és a kapcsoló során újra vannak csatolva.  Függetlenül attól, hogy a kapcsoló egy rövid szolgáltatási megszakítást eredményezhet, ha az adatbázis nem érhető el, és általában kevesebb, mint 30 másodpercig, és gyakran csak néhány másodpercig tart. Ha a kapcsolatok eldobásakor hosszan futó tranzakciók futnak, a lépés időtartama hosszabb időt vehet igénybe a megszakított tranzakciók helyreállítása érdekében. A [gyorsított adatbázis-helyreállítás](../accelerated-database-recovery.md) csökkentheti a hosszan futó tranzakciók megszakításának hatását.

> [!IMPORTANT]
> A munkafolyamat egyik lépése sem vesz fel adatvesztést. Győződjön meg arról, hogy implementált néhány [újrapróbálkozási logikát](troubleshoot-common-connectivity-issues.md) a Azure SQL Database használó alkalmazásokban és összetevőkben, miközben a szolgáltatási szintet módosítják.

## <a name="latency"></a>Késés

A szolgáltatási réteg módosításának becsült késése, az önálló adatbázis vagy a rugalmas készlet számítási méretének skálázása, a rugalmas készleten belüli vagy kívüli adatbázis áthelyezése, illetve a rugalmas készletek közötti adatbázis áthelyezése a következőképpen történik:

|Szolgáltatási szint|Alapszintű önálló adatbázis,</br>Standard (S0-S1)|Alapszintű rugalmas készlet,</br>Standard (S2-S12), </br>Önálló adatbázis vagy rugalmas készlet általános célú|Prémium vagy üzletileg kritikus önálló adatbázis vagy rugalmas készlet|Rugalmas skálázás
|:---|:---|:---|:---|:---|
|**Alapszintű önálló adatbázis, </br> Standard (S0-S1)**|&bull;&nbsp;Állandó időbeli késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 5 perc|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|
|**Alapszintű rugalmas készlet, </br> Standard (S2-S12), </br> általános célú önálló adatbázis vagy rugalmas készlet**|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;&nbsp;Önálló adatbázisok esetén a felhasznált területtől független állandó késleltetési idő</br>&bull;&nbsp;Az önálló adatbázisok esetében általában kevesebb, mint 5 perc</br>&bull;&nbsp;Rugalmas készletek esetén az adatbázisok számával arányos|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|
|**Prémium vagy üzletileg kritikus önálló adatbázis vagy rugalmas készlet**|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|
|**Rugalmas skálázás**|N.A.|N.A.|N.A.|&bull;&nbsp;Állandó időbeli késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 2 perc|

> [!NOTE]
> Emellett a standard (S2-S12) és a általános célú-adatbázisok esetében a rugalmas készletből vagy a rugalmas készletekből származó adatbázisok áthelyezésének késése az adatbázis méretétől függ, ha az adatbázis a prémium szintű fájlmegosztás ([PFS](../../storage/files/storage-files-introduction.md)) tárolót használja.
>
> Annak megállapításához, hogy egy adatbázis PFS-tárolót használ-e, hajtsa végre a következő lekérdezést az adatbázis környezetében. Ha a AccountType oszlop értéke `PremiumFileStorage` vagy `PremiumFileStorage-ZRS` , az adatbázis PFS-tárolót használ.
 
```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> A folyamatban lévő műveletek figyeléséhez tekintse meg a következő témakört: [műveletek kezelése az SQL REST API használatával](/rest/api/sql/operations/list), műveletek [kezelése a CLI](/cli/azure/sql/db/op)használatával, a műveletek [FIGYELÉSe a T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) használatával és a következő két PowerShell-paranccsal: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) és [stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Módosítások megszakítása

A szolgáltatási szintek változási vagy számítási átméretezési művelete megszakítható.

### <a name="the-azure-portal"></a>Azure Portal

Az adatbázis-Áttekintés panelen navigáljon az **értesítésekhez** , és kattintson a csempére, amely egy folyamatban lévő műveletet jelez:

![Folyamatban lévő művelet](./media/single-database-scale/ongoing-operations.png)

Ezután kattintson a **művelet megszakítása** feliratú gombra.

![Folyamatban lévő művelet megszakítása](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

A PowerShell-parancssorból állítsa be a `$resourceGroupName` , a `$serverName` és a parancsot, `$databaseName` majd futtassa a következő parancsot:

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

- Ha magasabb szolgáltatási szintre vagy számítási méretre frissít, az adatbázis maximális mérete nem növekszik, ha explicit módon nagyobb méretet (MaxSize) ad meg.
- Egy adatbázis visszalépéséhez az adatbázis felhasznált területének kisebbnek kell lennie, mint a cél szolgáltatási csomag maximálisan megengedett mérete és a számítási méret.
- Ha a **prémium** szintről a **standard** szintre vált, a rendszer extra tárterületet számít fel, ha mindkettő (1) az adatbázis maximális mérete támogatott a célként megadott számítási méretnél, és (2) a maximális méret meghaladja a cél számítási méretének belefoglalt tárolási mennyiségét. Ha például egy 500 GB-os maximális mérettel rendelkező P1-adatbázis az S3-as verzióra van lefoglalva, akkor a rendszer extra tárterületet biztosít, mivel az S3 támogatja a maximális 1 TB-os méretet, és a benne foglalt tárolási mennyiség csak 250 GB. Így a további tárterület mérete 500 GB – 250 GB = 250 GB. Az extra tárterület díjszabását lásd: [Azure SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/). Ha a felhasznált terület tényleges mennyisége kisebb, mint a foglalt tárterület, akkor ez az extra díj elkerülhető, ha az adatbázis maximális méretét a benne foglalt mennyiségre csökkenti.
- Ha a [geo-replikálást](active-geo-replication-configure-portal.md) engedélyező adatbázist frissít, frissítse a másodlagos adatbázisokat a kívánt szolgáltatási rétegre és számítási méretre, mielőtt frissítené az elsődleges adatbázist (általános útmutató a legjobb teljesítményhez). Egy másik kiadásra való frissítéskor követelmény, hogy a másodlagos adatbázis frissítve legyen először.
- Ha a [geo-replikálást](active-geo-replication-configure-portal.md) engedélyező adatbázis visszaminősítése engedélyezve van, az elsődleges adatbázisokat a kívánt szolgáltatási rétegre és számítási méretre kell visszaminősíteni a másodlagos adatbázis visszaminősítése előtt (általános útmutató a legjobb teljesítményhez). Egy másik kiadásra való visszalépéskor követelmény, hogy először az elsődleges adatbázist kell visszaértékelni.
- A visszaállítási szolgáltatásajánlatok eltérőek a különböző szolgáltatási szintek esetében. Ha az **alapszintű szintre vált** , alacsonyabb biztonsági mentési megőrzési idő van. Lásd: [Azure SQL Database biztonsági mentések](automated-backups-overview.md).
- Az adatbázis új tulajdonságai nem lesznek alkalmazva, amíg a módosítások be nem fejeződik.
- Ha adatmásolásra van szükség az adatbázis skálázásához (lásd a [késést](#latency)) a szolgáltatási réteg módosításakor, a skálázási művelettel párhuzamos magas erőforrás-kihasználtság hosszú skálázási időt eredményezhet. A [gyorsított adatbázis-helyreállítással (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts)a hosszú ideig futó tranzakciók visszaállítása nem jelentős késési forrás, de a magas párhuzamos erőforrás-használat kevesebb számítási, tárolási és hálózati sávszélesség-erőforrást hagy a méretezéshez, különösen a kisebb számítási méretekhez.

## <a name="billing"></a>Számlázás

A számlázás minden órában megtörténik, amely az adott órában alkalmazott legmagasabb szolgáltatási réteg + számítási méretet használja, függetlenül a használattól és attól, hogy az adatbázis egy óránál kevesebb ideig volt-e aktív. Ha például létrehoz egy adatbázist, és öt perccel később törli azt, akkor a számla egy adatbázis-órára vonatkozó díjat számít fel.

## <a name="change-storage-size"></a>Tárterület méretének módosítása

### <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

- A tárterület az adattároló maximális mérete 1 GB-os növekmények használatával állítható be. A minimálisan konfigurálható adattárolás 1 GB. Tekintse meg az erőforrás-korlátozási dokumentációs lapokat az [önálló adatbázisokhoz](resource-limits-vcore-single-databases.md) és a [rugalmas készletekhez](resource-limits-vcore-elastic-pools.md) az adattárolók maximális méretére az egyes szolgáltatási célkitűzéseknél
- Az önálló adatbázisok adattárolása a maximális méret növelésével vagy csökkentésével végezhető el a [Azure Portal](https://portal.azure.com), a [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), a [PowerShell](/powershell/module/az.sql/set-azsqldatabase), az [Azure CLI](/cli/azure/sql/db#az-sql-db-update)vagy a [REST API](/rest/api/sql/databases/update)használatával. Ha a maximális méret értéke bájtban van megadva, akkor az 1 GB-nál többnek kell lennie (1073741824 bájt).
- Az adatbázisok adatfájljaiban tárolható adatmennyiséget a beállított adattároló maximális mérete korlátozza. A tárterületen kívül a Azure SQL Database automatikusan 30%-kal több tárterületet foglal le a tranzakciónaplóhoz való használathoz.
- A Azure SQL Database automatikusan 32 GB-ot foglal le virtuális mag az `tempdb` adatbázishoz. `tempdb` a minden szolgáltatási szinten a helyi SSD-tárolóban található.
- Egy önálló adatbázis vagy egy rugalmas készlet tárterületének díja az adattárolási és a tranzakciónapló-tárolók összege, a szolgáltatási szint tárolási egységének díjszabása. A díját a díj `tempdb` tartalmazza. A tárolási díjszabással kapcsolatos részletekért tekintse meg a [Azure SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

- Az önálló adatbázisok DTU ára bizonyos mennyiségű tárterületet foglal magában, többletköltség nélkül. A benne foglalt mennyiségen túli extra tárterület kiépíthető a maximális méretkorlát 250 GB-ig, 1 TB-ig, majd 256 GB-onként 1 TB-nál nagyobb mértékben. A foglalt tárolási és a maximális méretkorlát esetében lásd [: önálló adatbázis: a tárolási méretek és a számítási méretek](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Az önálló adatbázisok extra tárterületének növeléséhez a Azure Portal, a [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), a [PowerShell](/powershell/module/az.sql/set-azsqldatabase), az [Azure CLI](/cli/azure/sql/db#az-sql-db-update)vagy a [REST API](/rest/api/sql/databases/update)használatával növelheti a maximális méretet.
- Az önálló adatbázisok extra tárterületének díja a szolgáltatás rétegének extra tárolási egységével megszorzott extra tárterület. További információ az extra tárterület díjszabásáról: [Azure SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

### <a name="geo-replicated-database"></a>Földrajzilag replikált adatbázis

A replikált másodlagos adatbázis adatbázis-méretének módosításához módosítsa az elsődleges adatbázis méretét. Ezt a változást ezután a rendszer replikálja és implementálja a másodlagos adatbázisban is.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11 és P15 megkötések, ha a maximális méret nagyobb, mint 1 TB

A prémium szinten több mint 1 TB tárterület jelenleg minden régióban elérhető, kivéve a következőket: Kelet-Kína, Észak-Kína, Közép-Németország és Németország északkeleti régiója. Ezekben a régiókban a prémium szintű Storage Max 1 TB-ra van korlátozva. Az alábbi szempontok és korlátozások az 1 TB-nál nagyobb maximális mérettel rendelkező P11 és P15 adatbázisokra vonatkoznak:

- Ha egy P11 vagy P15-adatbázis maximális mérete 1 TB-nál nagyobb értékre lett beállítva, akkor csak visszaállítható vagy átmásolható egy P11 vagy P15-adatbázisba.  Ezt követően az adatbázis átméretezhető egy másik számítási méretre, amennyiben az átméretezési művelet idején lefoglalt terület mennyisége nem haladja meg az új számítási méret maximális korlátját.
- Aktív földrajzi replikálási forgatókönyvek esetén:
  - Geo-replikációs kapcsolat beállítása: Ha az elsődleges adatbázis P11 vagy P15, a másodlagos (IES) P11 vagy P15 is kell lennie. Az alacsonyabb számítási méret elutasítása formátumú másodlagos zónák, mivel nem képesek 1 TB-nál nagyobb mértékben támogatni.
  - Az elsődleges adatbázis frissítése földrajzi replikálási kapcsolatban: az elsődleges adatbázison az 1 TB-nál nagyobb maximális méret megváltozása a másodlagos adatbázison ugyanezt a változást indítja el. Mindkét frissítésnek sikeresnek kell lennie ahhoz, hogy az elsődleges módosítás érvénybe lépjen. Az 1 TB-nál nagyobb területi korlátozások érvényesek. Ha a másodlagos olyan régióban található, amely nem támogatja az 1 TB-ot, az elsődleges nem frissül.
- Az import/export szolgáltatás használata az 1 TB-nál nagyobb P11-vagy P15-adatbázisok betöltéséhez nem támogatott. A SqlPackage.exe használatával [importálhat](database-import.md) és [exportálhat](database-export.md) adatfájlokat.

## <a name="next-steps"></a>Következő lépések

A teljes erőforrás-korlátokkal kapcsolatban lásd: [Azure SQL Database virtuális mag-alapú erőforrás-korlátok – önálló adatbázisok](resource-limits-vcore-single-databases.md) és [Azure SQL Database DTU-alapú erőforrás-korlátok – önálló adatbázisok](resource-limits-dtu-single-databases.md).
