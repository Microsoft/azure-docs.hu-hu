---
title: DTU erőforrás-korlátok rugalmas készletek
description: Ez az oldal néhány gyakori DTU-erőforrás-korlátot ismertet a rugalmas készletek Azure SQL Databaseban.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: reference
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: aa520b7d77a283e7dda883e99475947a8ccfa862
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625623"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>A rugalmas készletek DTU beszerzési modell használatával történő korlátozása
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk részletes erőforrás-korlátokat biztosít a Azure SQL Databaseban lévő, a DTU beszerzési modellt használó rugalmas készleten belüli adatbázisokhoz.

* Az Azure SQL Database DTU beszerzési modellre vonatkozó korlátozásait lásd: a [DTU erőforrás-korlátai – Azure SQL Database](resource-limits-dtu-single-databases.md).
* A virtuális mag erőforrás-korlátaival kapcsolatban lásd: virtuális mag-erőforrás korlátai [– Azure SQL Database](resource-limits-vcore-single-databases.md) és [virtuális mag erőforrás-korlátok – rugalmas készletek](resource-limits-vcore-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Rugalmas készlet: a tárolási méretek és a számítási méretek

Azure SQL Database rugalmas készletek esetében az alábbi táblázatok az egyes szolgáltatási szintek és számítási méretek számára elérhető erőforrásokat mutatják be. A szolgáltatási szintet, a számítási méretet és a tárterület mennyiségét a alábbiak szerint állíthatja be:

* [Azure Portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [Azure CLI](elastic-pool-manage.md#azure-cli)
* [REST API](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> A méretezéssel kapcsolatos útmutatást és szempontokat lásd: [rugalmas készlet](elastic-pool-scale.md) skálázása

A rugalmas készletekben található különálló adatbázisok erőforrás-korlátai általában ugyanazok, mint a készleteken kívüli önálló adatbázisok esetében a DTU és a szolgáltatási réteg alapján. Például az S2-adatbázisok maximális egyidejű feldolgozói 120 feldolgozók. Így a standard szintű készletben lévő adatbázisok maximálisan egyidejű feldolgozói is 120 feldolgozók, ha a készletben lévő adatbázis Max DTU 50 DTU (amely az S2-vel egyenértékű).
 
Az azonos számú DTU esetében a rugalmas készlethez megadott erőforrások túllépik a rugalmas készleten kívüli önálló adatbázisokhoz biztosított erőforrásokat is. Ez azt jelenti, hogy egy rugalmas készlet eDTU-kihasználtsága kevesebb, mint a készletben lévő adatbázisok DTU-kihasználtságának összesítése, a számítási feladatok mintája alapján. Például egy olyan szélsőséges esetben, ha egy rugalmas készletben csak egy adatbázis van, ahol az adatbázis DTU kihasználtsága 100%, a készlet eDTU kihasználtsága bizonyos számítási feladatok esetében 50% lehet. Ez akkor is előfordulhat, ha az adatbázis max. DTU a maximális támogatott értéknél marad a megadott készlet mérete alapján.

> [!NOTE]
> A tárolási erőforrások maximális száma az alábbi táblázatokban nem tartalmazza a tempdb és a log Storage-t.

### <a name="basic-elastic-pool-limits"></a>Alapszintű rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Foglalt tárterület/készlet (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximális tárterület készletenként (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| In-Memory OLTP maximális száma (GB) | N.A. | N.A. | N.A. | N.A. | N.A. | N.A. | N.A. | N.A. |
| Adatbázisok maximális száma <sup>1</sup> . készletben | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók (kérelmek) maximális száma a <sup>2</sup> . készletben | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Egyidejű munkamenetek maximális száma/készlet <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| DTU minimális száma | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| DTU maximális száma | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximális tárterület adatbázisonként (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>2</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="standard-elastic-pool-limits"></a>Standard rugalmas készletek korlátai

| eDTU-k száma készletenként | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Készletbe foglalt tárterület (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Maximális tárterület készletenként (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2048 |
| In-Memory OLTP maximális száma (GB) | N.A. | N.A. | N.A. | N.A. | N.A. | N.A. |
| Adatbázisok maximális száma ( <sup>2</sup> . készlet) | 100 | 200 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók (kérelmek) maximális száma a <sup>3</sup> . készletben | 100 | 200 | 400 | 600 | 800 | 1600 |
| Egyidejű munkamenetek maximális száma ( <sup>3</sup> ) | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| DTU minimális száma | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| DTU maximális száma | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximális tárterület adatbázisonként (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> a további kiépített tárterület miatt felmerülő további költségekkel kapcsolatos további információkért tekintse meg [SQL Database díjszabási lehetőségeit](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="standard-elastic-pool-limits-continued"></a>Standard rugalmas készletek korlátai (folytatás)

| eDTU-k száma készletenként | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Készletbe foglalt tárterület (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2500 | 3000 |
| Maximális tárterület készletenként (GB) | 2560 | 3072 | 3584 | 4096 | 4096 |
| In-Memory OLTP maximális száma (GB) | N.A. | N.A. | N.A. | N.A. | N.A. |
| Adatbázisok maximális száma ( <sup>2</sup> . készlet) | 500 | 500 | 500 | 500 | 500 |
| Egyidejű feldolgozók (kérelmek) maximális száma a <sup>3</sup> . készletben | 2400 | 3200 | 4000 | 5000 | 6000 |
| Egyidejű munkamenetek maximális száma ( <sup>3</sup> ) | 30000 | 30000 | 30000 | 30000 | 30000 |
| DTU minimális száma | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| DTU maximális száma | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maximális tárterület adatbázisonként (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> a további kiépített tárterület miatt felmerülő további költségekkel kapcsolatos további információkért tekintse meg [SQL Database díjszabási lehetőségeit](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="premium-elastic-pool-limits"></a>Prémium rugalmas készletek korlátai

| eDTU-k száma készletenként | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Készletbe foglalt tárterület (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Maximális tárterület készletenként (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| In-Memory OLTP maximális száma (GB) | 1 | 2 | 4 | 10 | 12 |
| Adatbázisok maximális száma ( <sup>2</sup> . készlet) | 50 | 100 | 100 | 100 | 100 |
| Egyidejű feldolgozók maximális száma (kérelem) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Egyidejű munkamenetek maximális száma ( <sup>3</sup> ) | 30000 | 30000 | 30000 | 30000 | 30000 |
| eDTU-k minimális száma adatbázisonként | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| eDTU-k maximális száma adatbázisonként | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Maximális tárterület adatbázisonként (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> a további kiépített tárterület miatt felmerülő további költségekkel kapcsolatos további információkért tekintse meg [SQL Database díjszabási lehetőségeit](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

### <a name="premium-elastic-pool-limits-continued"></a>Prémium rugalmas készletek korlátai (folytatás)

| eDTU-k száma készletenként | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Készletbe foglalt tárterület (GB) <sup>1</sup> | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maximális tárterület készletenként (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| In-Memory OLTP maximális száma (GB) | 16 | 20 | 24 | 28 | 32 |
| Adatbázisok maximális száma ( <sup>2</sup> . készlet) | 100 | 100 | 100 | 100 | 100 |
| Egyidejű feldolgozók (kérelmek) maximális száma a <sup>3</sup> . készletben | 3200 | 4000 | 4800 | 5600 | 6400 |
| Egyidejű munkamenetek maximális száma ( <sup>3</sup> ) | 30000 | 30000 | 30000 | 30000 | 30000 |
| DTU minimális száma | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| DTU maximális száma | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maximális tárterület adatbázisonként (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> a további kiépített tárterület miatt felmerülő további költségekkel kapcsolatos további információkért tekintse meg [SQL Database díjszabási lehetőségeit](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> további megfontolásokat az [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md) című témakörben talál.

<sup>3</sup> az egyidejű feldolgozók (kérelmek) maximális száma az egyes adatbázisokhoz: [Egyadatbázisos erőforrás-korlátok](resource-limits-vcore-single-databases.md). Ha például a rugalmas készlet Gen5 használ, és az adatbázis max. virtuális mag értéke 2, akkor az egyidejű feldolgozók maximális száma 200.  Ha az adatbázis max. virtuális mag értéke 0,5, akkor az egyidejű feldolgozók maximális száma értéke 50, mivel a Gen5-ben legfeljebb 100 egyidejű dolgozó van. Ha az adatbázis más maximális virtuális mag-beállításai kevesebb, mint 1 virtuális mag vagy kevesebb, az egyidejű feldolgozók maximális száma hasonlóan átméretezhető.

> [!IMPORTANT]
> A prémium szinten több mint 1 TB tárterület jelenleg minden régióban elérhető, kivéve a következőket: Kelet-Kína, Észak-Kína, Közép-Németország és Németország északkeleti régiója. Ezekben a régiókban a prémium szintű Storage Max 1 TB-ra van korlátozva.  További információ: [P11-P15 current korlátozások](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Ha egy rugalmas készlet minden DTU-ja használatban van, akkor a készletben található minden adatbázis ugyanannyi erőforrást kap a lekérdezések feldolgozásához. Az SQL Database szolgáltatás egyenlő erőforrás-megosztást biztosít az adatbázisok között azáltal, hogy mindegyiküknek egyenlő szeleteket ad a számítási időből. A rugalmas készlet egyenlő erőforrás-megosztása hozzáadódik az egyes adatbázisok számára máshonnan garantált erőforrások mennyiségéhez, ha a minimális DTU/adatbázis érték nem 0-ra van állítva.

> [!NOTE]
> A `tempdb` korlátokat lásd: [tempdb korlátok](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>A készletezett adatbázisok adatbázis-tulajdonságai

A következő táblázat a készletezett adatbázisok tulajdonságait ismerteti.

| Tulajdonság | Leírás |
|:--- |:--- |
| eDTU-k maximális száma adatbázisonként |A készletben található adatbázisok bármelyike által használható eDTU-k maximális száma (az elérhetőség a készletben található további adatbázisok kihasználtságától függ). Az eDTU adatbázisonkénti maximális száma nem garantálja az erőforrásokat az adatbázisok számára. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU-k adatbázisonkénti maximális számát állítsa elég magasra ahhoz, hogy az adatbázis-kihasználtsági csúcsokkal is elbírjon. Elvárható, hogy a szükségesnél valamivel nagyobb értéket adjon meg, mivel a készlet általában hullámzó használati mintákat feltételez az adatbázisokkal kapcsolatban, amelyekben az adatbázisok kihasználtsága nem egyszerre éri el a csúcsértéket. Például tegyük fel, hogy az adatbázisonkénti felhasználási csúcs 20 eDTU, és a készletben található 100 adatbázisnak egyszerre csak a 20%-a éri el a csúcsot. Ha az eDTU-k adatbázisonkénti maximális száma 20-ra van állítva, akkor észszerű a készletet ötszörösen túlméretezni, és az eDTU-k készletenkénti számát 400-ra állítani. |
| eDTU-k minimális száma adatbázisonként |A készletben található adatbázisok mindegyike számára garantált eDTU-k minimális száma. Ez a beállítás egy globális beállítás, amely a készletben található minden adatbázisra vonatkozik. Az eDTU adatbázisonkénti minimális száma lehet 0, ami egyben az alapértelmezett érték is. Ezen tulajdonság értékeként egy 0 és az adatbázisonkénti átlagosan használt eDTU-k száma közötti mennyiséget adjon meg. A készletben található adatbázisok számának és az eDTU-k adatbázisonkénti minimális számának szorzata nem lehet magasabb az eDTU-k készletenkénti számánál. Például ha egy készletben 20 adatbázis van, és az eDTU-k adatbázisonkénti minimális száma 10-re van állítva, akkor az eDTU-k készletenkénti száma legalább 200 kell, hogy legyen. |
| Tárterület maximális száma adatbázison |A felhasználó által a készletben lévő adatbázis számára beállított maximális adatbázis-méret. A készletezett adatbázisok azonban megosztják a lefoglalt készlet tárterületét. Még akkor is, ha az *adatbázis* teljes tárterülete úgy van beállítva, hogy nagyobb legyen, mint a *készlet* teljes rendelkezésre álló tárolóhelye, az összes adatbázis által ténylegesen felhasznált terület teljes mérete nem haladhatja meg a rendelkezésre álló készlet korlátját. Az adatbázisok maximális mérete az adatfájlok maximális méretére vonatkozik, de nem tartalmazza a naplófájlok által használt területet. |
|||

## <a name="next-steps"></a>Következő lépések

* Egyetlen adatbázis virtuális mag erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a virtuális mag beszerzési modell használatával](resource-limits-vcore-single-databases.md)
* Egyetlen adatbázis DTU erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a DTU beszerzési modell használatával](resource-limits-dtu-single-databases.md)
* A rugalmas készletek virtuális mag erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a virtuális mag beszerzési modell használatával](resource-limits-vcore-elastic-pools.md)
* Az Azure SQL felügyelt példányain felügyelt példányok erőforrás-korlátaival kapcsolatban lásd: [SQL felügyelt példányok erőforrás-korlátai](../managed-instance/resource-limits.md).
* Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* A logikai SQL Server erőforrás-korlátaival kapcsolatos információkért lásd: a [logikai SQL Server erőforrás-korlátainak áttekintése](resource-limits-logical-server.md) a kiszolgálók és az előfizetési szintek korlátaival kapcsolatos információkért.