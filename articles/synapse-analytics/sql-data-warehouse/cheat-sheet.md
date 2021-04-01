---
title: Cheat Sheet dedikált SQL-készlethez (korábban SQL DW)
description: Hivatkozásokat és ajánlott eljárásokat talál a dedikált SQL-készlet (korábban SQL DW) gyors létrehozásához az Azure szinapszis Analytics szolgáltatásban.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: cc54ae66cda6bf8ecde07d1830448ec39a15cc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120223"
---
# <a name="cheat-sheet-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytic"></a>A dedikált SQL Pool (korábban SQL DW) Azure szinapszis analitikus

Ez a Cheat-táblázat hasznos tippeket és ajánlott eljárásokat biztosít a dedikált SQL-készlet (korábban SQL DW) megoldások létrehozásához.

Az alábbi ábrán egy adattárház dedikált SQL-készlettel (korábban SQL DW) történő megtervezésének folyamata látható:

![Vázlat](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Táblák közötti lekérdezések és műveletek

Ha előre tudja, milyen elsődleges műveleteket és lekérdezéseket futtat majd az adattárházban, figyelembe veheti ezeket az adattárház architektúrájának kialakításakor. Ilyen lekérdezések és műveletek lehetnek többek között a következők:

* Egy vagy két ténytábla egyesítése dimenziótáblákkal, a kombinált tábla szűrése, majd az eredmények összefűzése egy data martba.
* Nagyobb vagy kisebb frissítések elvégzése a tényértékesítésekben.
* Csak adatok hozzáfűzése a táblákhoz.

A művelettípusok előzetes ismerete segít optimalizálni a táblák kialakítását.

## <a name="data-migration"></a>Adatok migrálása

Először töltse be az adatait [Azure Data Lake Storageba](../../data-factory/connector-azure-data-lake-store.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) vagy az Azure Blob Storageba. Ezután a [copy utasítás](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használatával töltse be az adatait az átmeneti táblákba. Használja a következő konfigurációt:

| Tervezés | Ajánlás |
|:--- |:--- |
| Disztribúció | Ciklikus időszeletelés |
| Indexelés | Halommemória |
| Particionálás | Nincsenek |
| Erőforrásosztály | largerc vagy xlargerc |

Itt további információkat tudhat meg az [adatok migrálásáról](/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice), az [adatok betöltéséről](design-elt-data-loading.md) és a [kinyerési, betöltési és átalakítási (ELT) folyamatról](design-elt-data-loading.md).

## <a name="distributed-or-replicated-tables"></a>Elosztott vagy replikált táblák

A tábla tulajdonságaitól függően a következő stratégiákat használja:

| Típus | Kiválóan alkalmas...| Ügyeljen a következő esetekben:|
|:--- |:--- |:--- |
| Replikált | * Kis dimenziós táblák egy csillag-sémában, kevesebb, mint 2 GB tárhellyel a tömörítés után (~ 5x tömörítés) |* Sok írási tranzakció van a táblában (például INSERT, upsert, DELETE, Update)<br></br>* Az adatraktár-egységek (DWU-EK) kiépítési gyakoriságának módosítása<br></br>* Csak 2-3 oszlopot használ, de a tábla sok oszlopot tartalmaz<br></br>* Egy replikált tábla indexelése |
| Ciklikus időszeletelés (alapértelmezett) | * Ideiglenes/előkészítési tábla<br></br> * Nincs nyilvánvaló csatlakozású kulcs vagy jó jelölt oszlop |* Az adatáthelyezés miatt lassú a teljesítmény |
| Kivonat | * Egyedkapcsolat táblák<br></br>* Nagyméretű dimenziós táblák |* A terjesztési kulcs nem frissíthető |

**Tippek**

* Kezdje ciklikus időszeleteléssel, de haladjon a kivonatoló terjesztési stratégia felé, hogy kihasználhassa a nagymértékben párhuzamos architektúrát.
* Ügyeljen arra, hogy a közös kivonatkulcsoknak ugyanaz legyen az adatformátuma.
* Ne terjesszen varchar formátumra.
* A gyakori csatlakozási műveletekkel rendelkező ténytáblákhoz közös kivonatkulccsal rendelkező dimenziótáblákhoz kivonatterjesztés használható.
* A *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* segítségével elemezheti az adatokban lévő eltéréseket.
* A *[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)* használatával elemezheti a lekérdezéseket a lekérdezések mögött, figyelheti a szórási időt, és megtekintheti a shuffle-műveleteket. Ez az elosztási stratégia áttekintéséhez hasznos.

További tudnivalók a [replikált táblákról](design-guidance-for-replicated-tables.md) és az [elosztott táblákról](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>A tábla indexelése

Az indexelés a táblák gyors olvasásához hasznos. Egyedi technológiákat alkalmazhat az igényei szerint:

| Típus | Kiválóan alkalmas... | Ügyeljen a következő esetekben:|
|:--- |:--- |:--- |
| Halommemória | * Átmeneti/ideiglenes tábla<br></br>* Kisméretű táblák kis keresésekkel |* A keresés megkeresi a teljes táblázatot |
| Fürtözött index | * Táblák legfeljebb 100 000 000 sorral<br></br>* Nagyméretű táblák (több mint 100 000 000 sor), amelyekben csak 1-2 oszlop használatos |* Replikált táblán használatos<br></br>* Összetett lekérdezések több JOIN és Group By Operations használatával<br></br>* Frissíti az indexelt oszlopokat: a memóriába kerül |
| Fürtözött oszlopcentrikus index (CCI) (alapértelmezett) | * Nagyméretű táblák (több mint 100 000 000 sor) | * Replikált táblán használatos<br></br>* Nagy frissítési műveleteket hajthat végre a táblán<br></br>* A tábla túlparticionálása: a Sorcsoportok nem különböző terjesztési csomópontokra és partícióra terjednek ki. |

**Tippek**

* A fürtözött indexek mellett érdemes lehet nem fürtözött indexet is hozzáadni a szűréshez gyakran használt oszlopokhoz.
* Ügyeljen arra, hogyan kezeli a memóriát a CCI-vel rendelkező táblákban. Adatok betöltésekor az a cél, hogy a felhasználó (vagy a lekérdezés) nagyméretű erőforrásosztályt használhasson. Kerülje a vágást és sok kis tömörített sorcsoport létrehozását.
* A Gen2-n a teljesítmény maximalizálása érdekében a CCI-táblák gyorsítótárazása a számítási csomópontokon helyben történik.
* CCI esetén a sorcsoportok nem megfelelő tömörítése miatt gyenge lehet a teljesítmény. Ilyen esetben állítsa össze újra vagy rendezze át a CCI-t. Tömörített sorcsoportonként legalább 100 000 sorra van szükség. Az ideális a sorcsoportonként 1 millió sor.
* A növekményes terhelési gyakoriság és méret alapján érdemes lehet automatizálni az indexek átrendezésekor vagy újraépítésekor. A tavaszi nagytakarítás mindig hasznos.
* A sorcsoportokat stratégiai szempontok szerint vágja. Mekkorák a nyitott sorcsoportok? Mennyi adatot tervez betölteni a következő napokban?

További információk az [indexekről](sql-data-warehouse-tables-index.md).

## <a name="partitioning"></a>Particionálás

Particionálhatja a táblát nagyméretű ténytábla esetén (1 milliárd sornál nagyobb). Az esetek 99 százalékában a partíciókulcsnak dátumon kell alapulnia. Kerülje a túlparticionálást fürtözött oszlopcentrikus index esetén.

ELT-t igénylő előkészítési táblák esetén hasznos lehet a particionálás. Ez megkönnyíti az adatok életciklus-felügyeletét.
Kerülje az adatok túlparticionálását, különösen fürtözött oszlopcentrikus indexeken.

További információk a [partíciókról](sql-data-warehouse-tables-partition.md).

## <a name="incremental-load"></a>Növekményes betöltés

Ha növekményesen fogja betölteni az adatokat, először győződjön meg arról, hogy nagyobb méretű erőforrásosztályokat foglalt le az adatok betöltéséhez.  Ez különösen fontos a fürtözött oszlopcentrikus indexekkel rendelkező táblákba való betöltéskor.  További részletekért tekintse meg az [erőforrás-osztályok](resource-classes-for-workload-management.md) című részt.  

Javasoljuk, hogy a ELT-folyamatok adattárházba való automatizálásához használja a következőt: Base és ADF v2.

A korábbi adatain belüli nagy mennyiségű frissítéshez érdemes lehet egy [CTAS](sql-data-warehouse-develop-ctas.md) használni, hogy az INSERT, Update és DELETE helyett táblázatba írja a megőrizni kívánt adatmennyiséget.

## <a name="maintain-statistics"></a>Statisztikák karbantartása

Fontos a statisztikák frissítése, mivel az adatokban *jelentős* változások történhetnek. A *jelentős* változások előfordulásának megállapításához tekintse meg a [statisztikák frissítése](sql-data-warehouse-tables-statistics.md#update-statistics) című témakört. A frissített statisztikák optimalizálja a lekérdezési terveket. Ha úgy gondolja, hogy túl sokáig tart az összes statisztika karbantartása, körültekintőbben válassza ki, mely oszlopok rendelkezzenek statisztikákkal.

A frissítések gyakoriságát is megadhatja. Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. A legnagyobb előnnyel az jár, ha a csatlakozások részét képező, a WHERE záradékban használt és a GROUP BY elemben megtalálható oszlopok statisztikáit készíti el.

További információk a [statisztikákról](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Erőforrásosztály

Az erőforráscsoportok használatával memóriát foglalhat le a lekérdezésekhez. Ha a lekérdezés vagy a betöltés sebességének növelése érdekében több memóriára van szüksége, magasabb erőforrásosztályokat kell lefoglalnia. A nagyobb erőforrásosztályok használata azonban hatással van a párhuzamos működésre. Ezt érdemes figyelembe venni, mielőtt az összes felhasználót nagyméretű erőforrásosztályba helyezné át.

Ha úgy látja, hogy a lekérdezések túl sokáig tartanak, ellenőrizze, hogy a felhasználók nem nagyméretű erőforrásosztályokban futnak-e. A nagyméretű erőforrás osztályok számos egyidejű helyet foglalnak le, ezért más lekérdezések várólistára helyezését okozhatják.

Végül a [DEDIKÁLT SQL-készlet (korábban SQL DW)](sql-data-warehouse-overview-what-is.md)Gen2 használatával minden erőforráscsoport 2,5-szor több memóriát kap, mint a Gen1.

További információk az [erőforrásosztályokról és a párhuzamos működésről](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Csökkentheti költségeit

Az Azure szinapszis egyik fő funkciója a [számítási erőforrások kezelése](sql-data-warehouse-manage-compute-overview.md). Ha nem használja, szüneteltetheti a dedikált SQL-készlet (korábban SQL DW) használatát, ami leállítja a számítási erőforrások számlázását. Az erőforrásokat a teljesítményigényeinek megfelelően skálázhatja. A szüneteltetést az [Azure Portalon](pause-and-resume-compute-portal.md) vagy a [PowerShell-lel](pause-and-resume-compute-powershell.md) végezheti el. A méretezéshez használja a [Azure Portal](quickstart-scale-compute-portal.md), a [PowerShell](quickstart-scale-compute-powershell.md), a [T-SQL](quickstart-scale-compute-tsql.md)vagy a [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Az Azure Functions használatával mostantól bármikor használhatja az automatikus skálázást:

[![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

## <a name="optimize-your-architecture-for-performance"></a>Teljesítményre optimalizálhatja az architektúrát

Küllős architektúra esetén az SQL Database és az Azure Analysis Services használatát javasoljuk. Ez a megoldás a munkaterhelések elkülönítését biztosítja a különböző felhasználói csoportok között, és az SQL Database és az Azure Analysis Services speciális biztonsági funkcióinak használatát teszi lehetővé. Emellett ezzel a módszerrel korlátlan párhuzamos működést biztosíthat a felhasználók számára.

Ismerkedjen meg a [tipikus architektúrákkal, amelyek kihasználják a DEDIKÁLT SQL-készlet (korábban SQL DW) előnyeit az Azure szinapszis Analytics szolgáltatásban](/archive/blogs/sqlcat/common-isv-application-patterns-using-azure-sql-data-warehouse).

Egyetlen kattintással üzembe helyezheti az SQL-adatbázisokat a dedikált SQL-készletből (korábban SQL DW):

[![Az "üzembe helyezés az Azure-ban" feliratú gombot ábrázoló kép.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json)