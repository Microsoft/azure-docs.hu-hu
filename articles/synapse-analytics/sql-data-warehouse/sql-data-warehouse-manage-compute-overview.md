---
title: Számítási erőforrás kezelése a dedikált SQL-készlethez (korábban SQL DW)
description: Ismerje meg az Azure szinapszis Analytics szolgáltatásban a dedikált SQL-készlet (korábban SQL DW) teljesítményének kibővítő képességeit. A dedikált SQL-készlet (korábbi nevén SQL DW) felfüggesztésével csökkentheti a DWU és a költségek csökkentését.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 20087fbf4d5c37f1501df08cc294a10ddb9118e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601814"
---
# <a name="manage-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>A dedikált SQL-készlet (korábban SQL DW) számítási feladatainak kezelése az Azure szinapszis Analyticsben

Ismerje meg a számítási erőforrások dedikált SQL-készlet (korábban SQL DW) felügyeletét az Azure szinapszis Analyticsben. Alacsonyabb költségek a dedikált SQL-készlet szüneteltetésével vagy a dedikált SQL-készlet méretezésével a teljesítménybeli igények kielégítése érdekében.

## <a name="what-is-compute-management"></a>Mi a számítási felügyelet?

A dedikált SQL-készlet (korábban SQL DW) architektúrája elkülöníti a tárolást és a számítást, ami lehetővé teszi, hogy mindegyik egymástól függetlenül méretezhető legyen. Ennek eredményeképp az adattárolástól függetlenül megfelelhet a számítási méretezés teljesítményigényeinek. Emellett szüneteltetheti és folytathatja az erőforrásokat. Ennek az architektúrának a természetes következménye, hogy a számítás és a tárolás [számlázása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) külön történik. Ha egy ideig nem kell a dedikált SQL-készletet (korábban SQL DW) használni, a számítási költségeket a számítási költségek felfüggesztésével mentheti.

## <a name="scaling-compute"></a>Méretezési számítás

A dedikált SQL-készlet (korábban SQL DW) [adatraktár-egység](what-is-a-data-warehouse-unit-dwu-cdwu.md) beállítása beállításával kibővítheti vagy méretezheti a számítási kapacitást. A betöltési és lekérdezési teljesítmény lineárisan növekedik több adattárházegység hozzáadásával.

A kibővített lépésekért tekintse meg a [Azure Portal](quickstart-scale-compute-portal.md), a [PowerShell](quickstart-scale-compute-powershell.md)vagy a [T-SQL](quickstart-scale-compute-tsql.md) rövid útmutatót. Kibővíthető műveleteket is végrehajthat [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute)használatával.

A méretezési művelet végrehajtásához a dedikált SQL-készlet (korábban SQL DW) először megöli az összes bejövő lekérdezést, majd Visszagörgeti a tranzakciókat, így konzisztens állapotot biztosít. A méretezés csak a tranzakciók visszaállítása után történik. A méretezési műveletekhez a rendszer leválasztja a tárolási réteget a számítási csomópontokból, kiszámítja a számítási csomópontokat, majd újra csatolja a tárolási réteget a számítási réteghez. Minden dedikált SQL-készlet (korábbi nevén SQL DW) 60 Eloszlásként van tárolva, amelyek egyenletesen oszlanak el a számítási csomópontokon. További számítási csomópontok hozzáadásával további számítási kapacitást adhat hozzá. A számítási csomópontok számának növekedésével a számítási csomópontok száma csökken, és nagyobb számítási teljesítményt biztosít a lekérdezésekhez. Hasonlóképpen, a csökkenő adattárház-egységek csökkentik a számítási csomópontok számát, ami csökkenti a lekérdezések számítási erőforrásait.

A következő táblázat azt mutatja be, hogy a számítási csomópontok hány eloszlása változik az adatraktár-egységek változásakor.  A DW30000c 60 számítási csomópontot biztosít, és sokkal nagyobb lekérdezési teljesítményt érhet el, mint a DW100c.

| Adattárházegységek  | \# a számítási csomópontok | \# /csomópontok eloszlása |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c lehetőséget   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>Az adatraktár-egységek megfelelő méretének megállapítása

Ha szeretné megtekinteni a horizontális felskálázás teljesítményének előnyeit, különösen a nagyobb adattárház-egységek esetében, legalább 1 TB-os adatkészletet szeretne használni. A dedikált SQL-készlet (korábban SQL DW) adattárház-egységeinek legjobb számának megkereséséhez próbálja meg felfelé és lefelé méretezni a méretezést. Az adatok betöltése után futtasson néhány lekérdezést különböző számú adattárház-egységgel. Mivel a skálázás gyors, különböző teljesítményszint kipróbálására van lehetőség egy órán belül.

Javaslatok a legjobb számú adatraktár-egység megtalálásához:

- Egy dedikált SQL-készlet (korábban SQL DW) fejlesztésben való kiválasztásával kezdjen egy kisebb számú adattárház-egység kijelölésével.  Jó kiindulási pont a DW400c vagy a DW200c.
- Figyelheti az alkalmazás teljesítményét, és megfigyelheti a kiválasztott adatraktár-egységek számát a megfigyelt teljesítményhez képest.
- Tegyük fel a lineáris skálázást, és határozza meg, hogy mennyit kell fokoznia vagy csökkentenie az adatraktár-egységeket.
- Folytassa a módosításokat, amíg el nem éri az üzleti igényeknek megfelelő optimális teljesítményt.

## <a name="when-to-scale-out"></a>Mikor kell kibővíteni

Az adatraktár-egységek horizontális felskálázása a következő teljesítménybeli szempontokat befolyásolja:

- A rendszer lineárisan javítja a rendszerek teljesítményét a vizsgálatok, összesítések és CTAS utasításokhoz.
- Növeli az olvasók és az írók számát az betöltéshez.
- Az egyidejű lekérdezések és a párhuzamossági bővítőhelyek maximális száma.

Javaslatok az adatraktár-egységek kiskálázásának idejére:

- A nagy mennyiségű adat-betöltési vagy átalakítási művelet végrehajtása előtt bővítse az adatok gyorsabb elérhetővé tételét.
- A munkaidőn kívüli időszakban az egyidejű lekérdezések nagyobb számának megfelelően méretezhető.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Mi a teendő, ha a horizontális felskálázás nem javítja a teljesítményt?

Adattárház-egységek hozzáadása a párhuzamosság növeléséhez. Ha a munka egyenletesen oszlik meg a számítási csomópontok között, a további párhuzamosság javítja a lekérdezési teljesítményt. Ha a horizontális felskálázás nem változtatja meg a teljesítményt, bizonyos okok miatt előfordulhat, hogy ez megtörténik. Előfordulhat, hogy az adatai elferdítik a disztribúciókat, vagy a lekérdezések nagy mennyiségű adatáthelyezést mutatnak be. A lekérdezések teljesítményével kapcsolatos problémák kivizsgálásához tekintse meg a [teljesítménnyel kapcsolatos hibaelhárítás](sql-data-warehouse-troubleshoot.md#performance)témakört.

## <a name="pausing-and-resuming-compute"></a>Számítás szüneteltetése és folytatása

A számítási műveletek felfüggesztése miatt a tárolási réteg leválasztható a számítási csomópontokból. A számítási erőforrások kiadása a fiókból történik. Nem számítunk fel díjat, amíg a számítás szünetel. A számítás folytatása a számítási csomópontok számára újracsatlakoztatja a tárolót, és folytatja a számítási költségeket.
Ha egy dedikált SQL-készletet szüneteltet (korábban SQL DW):

- A számítási és memória-erőforrásokat a rendszer visszaadja az adatközpontban elérhető erőforrások készletének.
- Az adatraktár egységének költségei a Szüneteltetés időtartamára nulla értékűek.
- Az adattárolás nem érinti az adattárolást, és az adatai érintetlenek maradnak.
- Minden futó vagy várólistán lévő művelet meg lett szakítva.

Ha egy dedikált SQL-készletet folytat (korábban SQL DW):

- A dedikált SQL-készlet (korábbi nevén SQL DW) számítási és memória-erőforrásokat vásárol az adattárház-egységek beállításához.
- Az adatraktár-egységek számítási díjai folytatódnak.
- Az adatai elérhetővé válnak.
- Miután a dedikált SQL-készlet (korábban SQL DW) online állapotban van, újra kell indítania a munkaterhelés-lekérdezéseket.

Ha mindig elérhetővé szeretné tenni a dedikált SQL-készletet (korábban az SQL DW-t), érdemes lehet a legkisebb méretre méretezni a felfüggesztés helyett.

A szüneteltetési és folytatási lépésekért tekintse meg a [Azure Portal](pause-and-resume-compute-portal.md)vagy a [PowerShell](pause-and-resume-compute-powershell.md) rövid útmutatóit. Használhatja a [pause REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) vagy a [resume REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute)is.

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tranzakciók ürítése felfüggesztés vagy méretezés előtt

Azt javasoljuk, hogy a szüneteltető vagy a skálázási művelet megkezdése előtt engedélyezze a meglévő tranzakciók befejezését.

Ha szünetelteti vagy méretezi a dedikált SQL-készletet (korábban az SQL DW-t), a színfalak mögött a rendszer megszakítja a lekérdezéseket a Szüneteltetés vagy a skálázási kérelem elindításakor. Egy egyszerű választó lekérdezés megszakítása gyors művelet, amelynek szinte semmilyen hatása nincs az üzemelő példány felfüggesztéséhez vagy méretezéséhez szükséges időtartamra.  Azonban a tranzakciós lekérdezések, amelyek adatokat vagy az adatok szerkezetét is módosítják, nem biztos, hogy ilyen hamar le tudnak állni. **A tranzakciós lekérdezéseknek definíciójuk szerint vagy teljesen be kell fejeződniük, vagy vissza kell állítaniuk az általuk végrehajtott módosításokat.** A tranzakciós lekérdezések által elvégzett módosítások visszaállítása ugyanannyi, vagy akár hosszabb ideig is tarthat, mint a lekérdezés által elvégzett eredeti módosítás végrehajtása. Például ha megszakít egy olyan lekérdezést, amely sorokat törölt és már egy órája futott, a rendszernek egy újabb órájába telhet, hogy a törölt sorokat visszaállítsa. Ha a felfüggesztést vagy a méretezést olyankor futtatja, amikor épp tranzakciók vannak folyamatban, a felfüggesztés vagy a méretezés látszólag hosszú időt vehet igénybe, mivel e műveleteknek meg kell várniuk, amíg a visszaállítás lezajlik.

Lásd még: [tranzakciók ismertetése](sql-data-warehouse-develop-transactions.md)és [tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Számítási felügyelet automatizálása

A számítási felügyeleti műveletek automatizálását lásd: [a számítások kezelése az Azure functions](manage-compute-with-azure-functions.md)használatával.

A kibővített, szüneteltetési és folytatási műveletek végrehajtása több percet is igénybe vehet. Ha a méretezés, a felfüggesztés vagy a folytatás automatikusan történik, javasoljuk, hogy végezze el a logikát, és győződjön meg arról, hogy bizonyos műveletek befejeződtek, mielőtt továbblép egy másik művelettel. A dedikált SQL-készlet (korábbi nevén SQL DW) állapotának ellenőrzése különböző végpontokon keresztül lehetővé teszi az ilyen műveletek automatizálásának megfelelő megvalósítását.

A dedikált SQL-készlet (korábban SQL DW) állapotának vizsgálatához tekintse meg a [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) vagy a [T-SQL](quickstart-scale-compute-tsql.md#check-dedicated-sql-pool-formerly-sql-dw-state) rövid útmutatóját. Azt is megteheti, hogy a dedikált SQL-készlet (korábbi nevén SQL DW) állapotot is megtekintheti egy [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).

## <a name="permissions"></a>Engedélyek

A dedikált SQL-készlet (korábbi nevén SQL DW) méretezéséhez az [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)utasításban ismertetett engedélyek szükségesek.  A Szüneteltetés és folytatás megköveteli az [SQL-adatbázis közreműködői](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor) engedélyét, különösen a Microsoft. SQL/kiszolgálók/adatbázisok/művelet esetében.

## <a name="next-steps"></a>Következő lépések

A számítási erőforrások kezelésével kapcsolatos [további](manage-compute-with-azure-functions.md) tudnivalókat lásd a különböző számítási erőforrások lefoglalása az egyes lekérdezésekhez című témakör útmutatását. További információ: erőforrás- [osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md).
