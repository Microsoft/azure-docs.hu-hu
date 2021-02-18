---
title: Log Analytics integráció a Power BI és az Excel alkalmazással
description: Eredmények küldése Log Analyticsról Power BIra
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: 80a508aeb7eff9567bea67cb84350fc2e77551da
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614048"
---
# <a name="log-analytics-integration-with-power-bi"></a>Log Analytics integráció a Power BI

Ez a cikk azt ismerteti, hogyan lehet adatcsatornákat Log Analytics a Microsoft Power BIba venni, hogy vizuálisan vonzó jelentéseket és irányítópultokat hozzon létre. 

## <a name="background"></a>Háttér 

Azure Monitor naplók egy olyan platform, amely teljes körű megoldást biztosít a naplók betöltésére. [Azure Monitor log Analytics](../platform/data-platform.md#) az a felület, amellyel lekérdezheti ezeket a naplókat. További információ a teljes Azure Monitor adatplatformról, beleértve a Log Analyticst: [Azure monitor adatplatform](../platform/data-platform.md). 

A Microsoft Power BI a Microsoft adatvizualizációs platformja. További információ az első lépésekről: [Power bi honlapja](https://powerbi.microsoft.com/). 


Általánosságban elmondható, hogy az ingyenes Power BI funkciók használatával vizuálisan vonzó jelentéseket és irányítópultokat építhet ki és hozhat létre.

A fejlettebb funkciókhoz Power BI Pro vagy prémium szintű fiók vásárlása lehet szükséges. Ezek a funkciók a következők: 
 - a munka megosztása 
 - ütemezett frissítések
 - Power BI-alkalmazások 
 - adatfolyamok és Növekményes frissítés 

További információ: további információk [a Power bi díjszabásáról és szolgáltatásairól](https://powerbi.microsoft.com/pricing/) 

## <a name="integrating-queries"></a>Lekérdezések integrálása  

Power BI az [M lekérdezési nyelvet](/powerquery-m/power-query-m-language-specification/) használja fő lekérdezési nyelvként. 

Log Analytics lekérdezések exportálhatók az M-be, és közvetlenül Power BI használhatók. A sikeres lekérdezés futtatása után válassza ki az Exportálás **Power bi (M Query)** elemet az **exportálás** gombra kattintva log Analytics felhasználói felület felső műveleti sávján.


:::image type="content" source="media/log-powerbi/export-query2.png" alt-text="Az exportálási lehetőség legördülő menüjét megjelenítő Log Analytics lekérdezés" border="true":::

A Log Analytics létrehoz egy. txt nevű fájlt, amely tartalmazza az M kódot, amely közvetlenül a Power BIben használható.

## <a name="connecting-your-logs-to-a-dataset"></a>Naplók összekapcsolása egy adatkészlettel 

A Power BI adatkészlet a jelentéskészítéshez és a vizualizációhoz kész adatforrás. Ha Log Analytics lekérdezést szeretne összekapcsolni egy adatkészlettel, másolja a Log Analyticsból exportált M-kódot egy üres lekérdezésbe Power BIban. 

További információt a [Power bi adatkészletek ismertetése](/power-bi/service-datasets-understand/)című témakörben talál. 

## <a name="collect-data-with-power-bi-dataflows"></a>Adatok gyűjtése Power BI adatfolyamok 

Power BI adatfolyamok az adatok gyűjtését és tárolását is lehetővé teszi. További információ: [Power bi adatfolyamok](/power-bi/service-dataflows-overview).

A adatfolyam olyan "Cloud ETL" típusú, amely segít az adatok gyűjtésében és előkészítésében. Az adatkészlet a "modell", amely segítséget nyújt a különböző entitások csatlakoztatásához és az igényeinek megfelelő modellezéshez.

## <a name="incremental-refresh"></a>Növekményes frissítés 

A Power BI adatkészletek és Power BI adatfolyamok is növekményes frissítési lehetőséggel rendelkeznek. Power BI adatfolyamok és Power BI adatkészletek támogatják ezt a funkciót, de Power BI Premium kell használni.  


A Növekményes frissítés kisméretű lekérdezéseket futtat, és a lekérdezés futtatásakor az összes adat újbóli betöltése helyett a kisebb mennyiségű adatot frissíti. Lehetősége van arra, hogy nagy mennyiségű adatmennyiséget mentsen el, de a lekérdezés minden egyes futtatásakor új növekményt adjon hozzá. Ez a viselkedés a már futó jelentések esetében ideális megoldás.

Power BI Növekményes frissítés az eredményhalmazban megadott *datetime* -érték létezésére támaszkodik. A Növekményes frissítés konfigurálása előtt győződjön meg arról, hogy a Log Analytics lekérdezési eredményhalmaz tartalmaz legalább egy *datetime* értéket. 

További információ a Növekményes frissítés konfigurálásáról: [Power bi adatkészletek és növekményes](/power-bi/service-premium-incremental-refresh) frissítés, valamint [Power bi adatfolyamok és Növekményes frissítés](/power-bi/service-dataflows-incremental-refresh).

## <a name="reports-and-dashboards"></a>Jelentések és irányítópultok

Miután elküldte az adatait a Power BIba, továbbra is használhatja a Power BIt jelentések és irányítópultok létrehozásához.

További információkért tekintse [meg az első Power bi modell és jelentés létrehozásával kapcsolatos útmutatót](/learn/modules/build-your-first-power-bi-report/).  

## <a name="excel-integration"></a>Excel-integráció

Egy Excel-számolótáblával való integráláshoz használhatja a Power BIban használt M-integrációt. További információkért tekintse meg az [útmutató az Excel alkalmazással való integrálását](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) és a log Analyticsból exportált M-lekérdezés beillesztését ismertető témakört.

További információ a [log Analytics és az Excel integrálása](log-excel.md) című témakörben található.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg [log Analytics lekérdezésekkel](../log-query/log-query-overview.md).
