---
title: Rugalmas készletek erőforrásainak méretezése
description: Ez az oldal a rugalmas készletek erőforrásainak méretezését ismerteti a Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: 1125ea0c6c625ece010b2acb416ad223e37aea84
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787124"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Rugalmas készlet erőforrásainak méretezése a Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk azt ismerteti, hogyan skálázhatóak a rugalmas készletekhez és rugalmas készletbe Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Számítási erőforrások (virtuális magok vagy DTA-k) módosítása

A virtuális magok vagy eDTUS-k számának első kiválasztása után dinamikusan fel- vagy leskálásokat mérethet a tényleges tapasztalatok alapján a használatával:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure Portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)
* [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)
* [REST API](/rest/api/sql/elasticpools/update)


### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási szint módosításának vagy a számítási méret átméretezésének hatása

A rugalmas készletek szolgáltatási rétegének vagy számítási méretének módosítása hasonló mintát követ, mint az egyes adatbázisok esetében, és elsősorban a szolgáltatás végzi el a következő lépéseket:

1. Új számítási példány létrehozása a rugalmas készlethez  

    A rendszer létrehoz egy új számítási példányt a rugalmas készlethez a kért szolgáltatási szinttel és számítási mérettel. A szolgáltatási szint és a számítási méret bizonyos kombinációi esetében minden adatbázis replikáját létre kell hoznunk az új számítási példányban, amely adatok másolását is magában foglalja, és nagy mértékben befolyásolhatja a teljes késést. Ettől függetlenül az adatbázisok online állapotban maradnak ebben a lépésben, és a kapcsolatok továbbra is az eredeti számítási példány adatbázisaihoz lesznek irányítva.

2. Kapcsolatok útválasztásának váltása új számítási példányra

    Az eredeti számítási példány adatbázisához való meglévő kapcsolatokat a rendszer eldobja. Minden új kapcsolat létrejön az új számítási példány adatbázisával. A szolgáltatási szint és a számítási méret bizonyos kombinációi esetén az adatbázisfájlok le vannak különülve, és újra vannak vava a kapcsoló során.  A kapcsoló azonban a szolgáltatás rövid megszakítását eredményezheti, ha az adatbázisok általában kevesebb mint 30 másodpercig, és gyakran csak néhány másodpercig nem érhetők el. Ha hosszú ideig futó tranzakciók futnak a kapcsolatok megszakadása esetén, a megszakított tranzakciók helyreállítása hosszabb időt is igénybe vehet. [gyorsított adatbázis-helyreállítás](../accelerated-database-recovery.md) csökkentheti a hosszú ideig futó tranzakciók megszakításának hatását.

> [!IMPORTANT]
> A munkafolyamat bármely lépése során nem veszhet el adat.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási szint módosításának vagy a számítási méret átméretezésének késése

A szolgáltatási szint módosítása, az egyetlen adatbázis vagy rugalmas készlet számítási méretének skálázása, az adatbázisok rugalmas készletbe vagy rugalmas készletből való áthelyezésének, illetve az adatbázisok rugalmas készletek közötti áthelyezésének becsült késése a következőképpen paraméterezett:

|Szolgáltatási szint|Alapszintű egyetlen adatbázis,</br>Standard (S0-S1)|Alapszintű rugalmas készlet,</br>Standard (S2-S12), </br>általános célú adatbázis vagy rugalmas készlet létrehozása|Prémium vagy üzletileg kritikus adatbázis vagy rugalmas készlet|Rugalmas skálázás
|:---|:---|:---|:---|:---|
|**Alapszintű, </br> standard (S0-S1) adatbázis**|&bull;&nbsp;Állandó késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 5 perc|&bull;&nbsp;Az adatbázis adatmásolás által felhasznált területével arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc felhasznált terület GB-ban|&bull;&nbsp;Az adatbázis adatmásolás által felhasznált területével arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc felhasznált terület GB-ban|&bull;&nbsp;Az adatbázis adatmásolás által felhasznált területével arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc felhasznált terület GB-ban|
|**Alapszintű rugalmas készlet, </br> Standard (S2-S12), </br> általános célú adatbázis vagy rugalmas készlet**|&bull;&nbsp;Az adatbázis adatmásolás által felhasznált területével arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|&bull;&nbsp;Önálló adatbázisoknál az állandó késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Az egyes adatbázisoknál általában kevesebb mint 5 perc</br>&bull;&nbsp;Rugalmas készleteknél az adatbázisok számával arányosan|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|
|**Prémium vagy üzletileg kritikus adatbázis vagy rugalmas készlet**|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|&bull;Késés az adatbázis adatmásolás által felhasznált &nbsp; területével arányosan</br>&bull;&nbsp;Általában kevesebb, mint 1 perc szükséges a felhasznált terület gb-onként|
|**Rugalmas skálázás**|N.A.|N.A.|N.A.|&bull;&nbsp;Állandó késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 2 perc|

> [!NOTE]
>
> - Egy rugalmas készlet szolgáltatási rétegének módosítása vagy a számítási erőforrások átméretezése esetén a készletben lévő összes adatbázishoz felhasznált terület összegét kell használni a becslés kiszámításához.
> - Egy adatbázis rugalmas készletbe vagy készletből való áthelyezésének esetén csak az adatbázis által használt terület befolyásolja a késést, a rugalmas készlet által használt területet nem.
> - Standard és általános célú készletek esetén az adatbázisok rugalmas készletbe vagy rugalmas készletből való áthelyezésének késése arányos lesz az adatbázis méretével, ha a rugalmas készlet Prémium fájlmegosztási ([PFS)](../../storage/files/storage-files-introduction.md)tárolót használ. Annak megállapításához, hogy egy készlet PFS-tárolót használ-e, hajtsa végre a következő lekérdezést a készlet bármelyik adatbázisának környezetében. Ha az AccountType oszlop értéke vagy , a `PremiumFileStorage` `PremiumFileStorage-ZRS` készlet PFS-tárolót használ.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> A folyamatban lévő műveletek figyelése: Műveletek kezelése az [SQL REST API](/rest/api/sql/operations/list)használatával, Műveletek kezelése a [cli](/cli/azure/sql/db/op)használatával, Műveletek figyelése a T-SQL használatával és a következő két [PowerShell-parancs:](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) és [Stop-AzSqlDatabaseActivity.](/powershell/module/az.sql/stop-azsqldatabaseactivity)

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>További szempontok a szolgáltatási szint módosításakor vagy a számítási méret átméretezésekor

- Ha egy rugalmas készlet virtuális magját vagy eDTUS-ját méretre növeli, a készlet által felhasznált területnek kisebbnek kell lennie, mint a cél szolgáltatási szint és a készlet eDTUs maximálisan engedélyezett mérete.
- Ha egy rugalmas készlethez újraméretezést alkalmaz az eDTUS-khoz, további tárolási költségek lépnek fel, ha (1) a célkészlet támogatja a készlet maximális tárterületméretét, és (2) a tároló maximális mérete meghaladja a célkészlet által tartalmazott tárolási mennyiséget. Ha például egy 100 GB maximális méretű standard eDTU-készlet mérete 50 eDTU Standard-készletre csökken, további tárolási költség vonatkozik, mivel a célkészlet maximális mérete 100 GB, a benne foglalt tárterület pedig csak 50 GB. A további tárterület tehát 100 GB – 50 GB = 50 GB. A további tárterület díjszabását a [díjszabást SQL Database meg.](https://azure.microsoft.com/pricing/details/sql-database/) Ha a ténylegesen felhasznált terület kisebb, mint a benne foglalt tárterület mennyisége, akkor ez a többletköltség elkerülhető az adatbázis maximális méretének a benne foglalt mennyiségre való csökkentésével.

### <a name="billing-during-rescaling"></a>Számlázás az átméretezés során

Az adatbázis minden órában a legmagasabb szolgáltatási szintet + számítási méretet használja, amely az adott órában van alkalmazva, függetlenül a használattól, vagy attól, hogy az adatbázis egy óránál rövidebb ideje aktív volt-e. Ha például egyetlen adatbázist hoz létre, és öt perccel később törli, a számla egy adatbázisórára vonatkozó díjat tükröz.

## <a name="change-elastic-pool-storage-size"></a>Rugalmas készlet tárolóméretének módosítása

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis méretének zsugorításra a nem használt terület felszabadítása miatt. További információ: [Manage file space in Azure SQL Database](file-space-manage.md).

### <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

- A tárterület a maximális méretkorlátig kiépítható:

  - A standard vagy általános célú szolgáltatási szinteken való tárolás esetében növelje vagy csökkentse a méretet 10 GB-os növekményekben
  - A prémium vagy üzleti kritikus szolgáltatási szinteken a tárolás esetében növelje vagy csökkentse a méretet 250 GB-os növekményekben
- A rugalmas készletek tárterülete a maximális méret növelésével vagy csökkentésével létesítható.
- A rugalmas készlet tárolási ára a tárolási mennyiség és a szolgáltatási szint tárolási egységárának szorzata. A további tárterület árának részleteiért tekintse meg a SQL Database [díjszabását.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis méretének zsugorításra a nem használt terület felszabadítása miatt. További információ: [Manage file space in Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

- A rugalmas készlet eDTU-ára tartalmaz egy bizonyos tárterületet további költségek nélkül. A beépített mennyiségen túl további tárhelyet is kiépíthet a maximális méretkorlátig 250 GB és 1 TB közötti növekményekben, majd 256 GB-os, 1 TB-nál nagyobb növekményekben. A tartalmazott tárolási mennyiségről és a maximális méretkorlátról lásd: Rugalmas készletekre vonatkozó erőforráskorlátok a [DTU](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes) vásárlási modell használatával, illetve Erőforráskorlátok rugalmas készletekhez a virtuálismag-alapú [vásárlási modell használatával.](resource-limits-vcore-elastic-pools.md)
- A rugalmas készlethez további tárhelyet a maximális méret növelésével lehet kiépítni a [Azure Portal,](elastic-pool-manage.md#azure-portal) [a PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)az [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)vagy a [REST API.](/rest/api/sql/elasticpools/update)
- A rugalmas készlet extra tárterületének ára a további tárterület mennyisége szorozva a szolgáltatási szint extra tárolóegység-árával. A további tárterület árának részleteiért tekintse meg a SQL Database [díjszabását.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis méretének zsugorításra a nem használt terület felszabadítása miatt. További információ: [Manage file space in Azure SQL Database](file-space-manage.md).

## <a name="next-steps"></a>Következő lépések

Az általános erőforráskorlátokkal kapcsolatos további SQL Database [virtuálismag-alapú](resource-limits-vcore-elastic-pools.md) erőforráskorlátok – rugalmas készletek és SQL Database [DTU-alapú](resource-limits-dtu-elastic-pools.md)erőforráskorlátok – rugalmas készletek – fejezetben talál.