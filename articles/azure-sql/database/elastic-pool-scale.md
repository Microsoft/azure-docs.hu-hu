---
title: Rugalmas készletek erőforrásainak méretezése
description: Ez az oldal a Azure SQL Database rugalmas készletei erőforrásainak méretezését ismerteti.
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
ms.openlocfilehash: 3d935332854816ae62dea8e30f08bee2b92a4eab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107302981"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Rugalmas készlet erőforrásainak méretezése Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk bemutatja, hogyan méretezhetők a rugalmas készletekhez és a készletezett adatbázisokhoz rendelkezésre álló számítási és tárolási erőforrások Azure SQL Databaseban.

## <a name="change-compute-resources-vcores-or-dtus"></a>Számítási erőforrások módosítása (virtuális mag vagy DTU)

A virtuális mag vagy a Edtu számának kezdeti kiosztása után a rugalmas készletet dinamikusan felfelé vagy lefelé méretezheti a tényleges tapasztalatok alapján a következő használatával:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure Portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)
* [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)
* [REST API](/rest/api/sql/elasticpools/update)


### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási réteg módosításának vagy a számítási méret átméretezésének a következményei

Egy rugalmas készlet szolgáltatási rétegének vagy számítási méretének módosítása hasonló mintát követ, mint az önálló adatbázisok esetében, és főleg a következő lépéseket hajtja végre:

1. Új számítási példány létrehozása a rugalmas készlethez  

    A rendszer létrehoz egy új számítási példányt a rugalmas készlethez a kért szolgáltatási szintű és számítási mérettel. A szolgáltatási szintek és a számítási méretek bizonyos kombinációinak változásakor az egyes adatbázisok replikáját az új számítási példányban kell létrehozni, amely az adatok másolását és a teljes késést is jelentősen befolyásolhatja. Függetlenül attól, hogy az adatbázisok online állapotban maradnak ebben a lépésben, és a kapcsolatok továbbra is az eredeti számítási példány adatbázisaira lesznek irányítva.

2. Az új számítási példánnyal létesített kapcsolatok útválasztásának váltása

    A rendszer elveti az eredeti számítási példány adatbázisainak meglévő kapcsolatait. Minden új kapcsolat létrejön az új számítási példány adatbázisaiban. A szolgáltatási szintek és a számítási méret változásainak bizonyos kombinációi esetén az adatbázisfájlok le lesznek választva, és a kapcsoló során újra vannak csatolva.  Függetlenül attól, hogy a kapcsoló egy rövid szolgáltatási megszakítást eredményezhet, ha az adatbázisok nem érhetők el általában kevesebb mint 30 másodpercig, és gyakran csak néhány másodpercig. Ha a kapcsolatok eldobásakor hosszú ideig futó tranzakciók futnak, a lépés időtartama a megszakított tranzakciók helyreállításához hosszabb időt vehet igénybe. A [gyorsított adatbázis-helyreállítás](../accelerated-database-recovery.md) csökkentheti a hosszan futó tranzakciók megszakításának hatását.

> [!IMPORTANT]
> A munkafolyamat egyik lépése sem vesz fel adatvesztést.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási réteg módosításának késése vagy a számítási méret átméretezése

A szolgáltatási réteg módosításának becsült késése, az önálló adatbázis vagy a rugalmas készlet számítási méretének skálázása, a rugalmas készleten belüli vagy kívüli adatbázis áthelyezése, illetve a rugalmas készletek közötti adatbázis áthelyezése a következőképpen történik:

|Szolgáltatási szint|Alapszintű önálló adatbázis,</br>Standard (S0-S1)|Alapszintű rugalmas készlet,</br>Standard (S2-S12), </br>Önálló adatbázis vagy rugalmas készlet általános célú|Prémium vagy üzletileg kritikus önálló adatbázis vagy rugalmas készlet|Rugalmas skálázás
|:---|:---|:---|:---|:---|
|**Alapszintű önálló adatbázis, </br> Standard (S0-S1)**|&bull;&nbsp;Állandó időbeli késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 5 perc|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|
|**Alapszintű rugalmas készlet, </br> Standard (S2-S12), </br> általános célú önálló adatbázis vagy rugalmas készlet**|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;&nbsp;Önálló adatbázisok esetén a felhasznált területtől független állandó késleltetési idő</br>&bull;&nbsp;Az önálló adatbázisok esetében általában kevesebb, mint 5 perc</br>&bull;&nbsp;Rugalmas készletek esetén az adatbázisok számával arányos|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|
|**Prémium vagy üzletileg kritikus önálló adatbázis vagy rugalmas készlet**|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|&bull;Az &nbsp; Adatmásolás miatt használt adatbázis-területtel arányos késés</br>&bull;&nbsp;Általában kevesebb, mint 1 perc/GB felhasznált lemezterület|
|**Rugalmas skálázás**|N.A.|N.A.|N.A.|&bull;&nbsp;Állandó időbeli késés a felhasznált területtől függetlenül</br>&bull;&nbsp;Általában kevesebb, mint 2 perc|

> [!NOTE]
>
> - A szolgáltatási szint vagy a rugalmas készletre vonatkozó számítási kapacitás módosítása esetén a készletben lévő összes adatbázisban használt terület összesítését kell használni a becslés kiszámításához.
> - Ha az adatbázist egy rugalmas készletre vagy-re helyezi át, csak az adatbázis által használt terület befolyásolja a késést, nem a rugalmas készlet által használt területet.
> - A standard és a általános célú rugalmas készletek esetében a rugalmas készletből vagy a rugalmas készletekből álló adatbázisok késése az adatbázis méretétől függ, ha a rugalmas készlet prémium szintű fájlmegosztást ([PFS](../../storage/files/storage-files-introduction.md)) használó tárterületet használ. Annak megállapításához, hogy egy készlet PFS-tárolót használ-e, hajtsa végre a következő lekérdezést a készletben lévő bármelyik adatbázis környezetében. Ha a AccountType oszlopban a vagy a érték `PremiumFileStorage` szerepel `PremiumFileStorage-ZRS` , a készlet PFS-tárolót használ.

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

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>A szolgáltatási réteg módosításának és a számítási méret átméretezésének további szempontjai

- A rugalmas készlet virtuális mag vagy Edtu lecsökkentésekor a készlet felhasznált területének kisebbnek kell lennie, mint a cél szolgáltatási szintje és a készlet Edtu maximálisan megengedett mérete.
- A rugalmas készletek Edtu átméretezése esetén a rendszer extra tárterületet alkalmaz, ha (1) a készlet tárterületének maximális méretét a cél készlet támogatja, és (2) a tárterület maximális mérete meghaladja a cél készletben foglalt tárterületet. Ha például egy 100 eDTU standard szintű, 100 GB-os maximális mérettel rendelkező készletet egy 50 eDTU standard készletbe foglal le, akkor a rendszer extra tárolási díjat számít fel, mivel a célként megadott készlet támogatja a 100 GB-os maximális méretet, és a benne foglalt tárolási mennyiség csak 50 GB. Így a további tárterület mérete 100 GB – 50 GB = 50 GB. Az extra tárterület díjszabását lásd: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/). Ha a felhasznált terület tényleges mennyisége kisebb, mint a foglalt tárterület, akkor ez az extra díj elkerülhető, ha az adatbázis maximális méretét a benne foglalt mennyiségre csökkenti.

### <a name="billing-during-rescaling"></a>Számlázás az átméretezés során

Az adatbázis óránkénti számlázása az adott órában alkalmazott legmagasabb szolgáltatási réteg + számítási méret alapján történik, függetlenül a használattól, illetve attól, hogy az adatbázis egy óránál kevesebb ideig volt-e aktív. Ha például létrehoz egy adatbázist, és öt perccel később törli azt, akkor a számla egy adatbázis-órára vonatkozó díjat számít fel.

## <a name="change-elastic-pool-storage-size"></a>Rugalmas készlet tárolási méretének módosítása

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

### <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

- A tárterület a maximális méretkorlát alapján állítható be:

  - A standard vagy általános célú szolgáltatási rétegekben történő tároláshoz a 10 GB-os növekmények méretének növelése vagy csökkentése
  - A prémium vagy az üzleti szempontból kritikus szolgáltatási rétegekben történő tároláshoz a méret növelése vagy csökkentése 250 GB-onként
- A rugalmas készletek tárterületét a maximális méret növelésével vagy csökkentésével lehet kiépíteni.
- A rugalmas készlet tárterületének ára a tárolási kapacitás, a szolgáltatási szintet tartalmazó tárolási egység árával megszorozva. További információ az extra tárterület díjszabásáról: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

- A rugalmas készlet eDTU díja bizonyos mennyiségű tárterületet foglal magában, többletköltség nélkül. A benne foglalt mennyiségen túli extra tárterület kiépíthető a maximális méretkorlát 250 GB-ig, 1 TB-ig, majd 256 GB-onként 1 TB-nál nagyobb mértékben. A befoglalt tárhelyek és a maximális méretkorlát esetében lásd: az erőforrások korlátai a rugalmas készletek [számára a DTU beszerzési modell](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes) vagy a [rugalmas készletek erőforrás-korlátai a virtuális mag beszerzési modell használatával](resource-limits-vcore-elastic-pools.md).
- A rugalmas készletek extra tárterületét a [Azure Portal](elastic-pool-manage.md#azure-portal), a [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), az [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)vagy a [REST API](/rest/api/sql/elasticpools/update)használatával lehet kiépíteni a maximális méret növelésével.
- A rugalmas készlet extra tárterületének díja a további tárterület mérete, a szolgáltatási szinten található extra tárolási egység árával megszorozva. További információ az extra tárterület díjszabásáról: [SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

## <a name="next-steps"></a>Következő lépések

A teljes erőforrás-korlátokkal kapcsolatban lásd: [SQL Database virtuális mag-alapú erőforrás-korlátok – rugalmas készletek](resource-limits-vcore-elastic-pools.md) és [SQL Database DTU-alapú erőforrás-korlátok – rugalmas készletek](resource-limits-dtu-elastic-pools.md).