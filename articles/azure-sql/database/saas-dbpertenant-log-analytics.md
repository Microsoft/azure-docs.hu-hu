---
title: Naplók Azure Monitor több-bérlős alkalmazással
description: Azure Monitor naplók beállítása és használata több-bérlős Azure SQL Database SaaS-alkalmazással
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 029e3ba799e5f239bde0ef049316dd268ebe4c8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588778"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-azure-sql-database-saas-app"></a>Azure Monitor naplók beállítása és használata több-bérlős Azure SQL Database SaaS-alkalmazással
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben az oktatóanyagban a rugalmas készletek és adatbázisok figyeléséhez [Azure monitor naplókat](../../azure-monitor/logs/log-query-overview.md) kell beállítania és használnia. Ez az oktatóanyag a [Teljesítményfigyelés és-kezelés oktatóanyagra](saas-dbpertenant-performance-monitoring.md)épül. Bemutatja, hogyan használhatók a Azure Monitor naplók a Azure Portal által biztosított figyelés és riasztások kiegészítéséhez. A Azure Monitor-naplók több ezer rugalmas készlet és több százezer adatbázis figyelését is lehetővé teszi. A Azure Monitor-naplók egyetlen figyelési megoldást biztosítanak, amely a különböző alkalmazások és Azure-szolgáltatások figyelését több Azure-előfizetésben is integrálhatja.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Monitor naplók telepítése és konfigurálása.
> * Készletek és adatbázisok figyelése Azure Monitor naplók használatával.

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip tickets SaaS-adatbázis-bérlői alkalmazás telepítve van. Ha kevesebb mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip tickets SaaS-adatbázis-bérlői alkalmazás üzembe helyezése és megismerése](./saas-dbpertenant-get-started-deploy.md)című témakört.
* Az Azure PowerShell telepítve van. További információért lásd [az Azure PowerShell használatának első lépéseit](/powershell/azure/get-started-azureps).

Tekintse meg a [Teljesítményfigyelés és-kezelés oktatóanyagot](saas-dbpertenant-performance-monitoring.md) az SaaS-forgatókönyvek és-minták megvitatására, valamint azt, hogy azok hogyan érintik a figyelési megoldás követelményeit.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Az adatbázis és a rugalmas készlet teljesítményének figyelése és kezelése Azure Monitor naplókkal

Azure SQL Database a figyelés és a riasztás a Azure Portal adatbázisaiban és készletekben érhető el. Ez a beépített figyelési és riasztási szolgáltatás kényelmes, de erőforrás-specifikus is. Ez azt jelenti, hogy kevésbé alkalmas a nagyméretű telepítések figyelésére, illetve az erőforrások és az előfizetések egységes nézetének megadására.

Nagy mennyiségű forgatókönyv esetén Azure Monitor naplókat használhat a figyeléshez és a riasztásokhoz. A Azure Monitor egy különálló Azure-szolgáltatás, amely lehetővé teszi, hogy az elemzések a különböző szolgáltatásokból származó munkaterületeken gyűjtött naplókon keresztül történjenek. Azure Monitor a naplók egy beépített lekérdezési nyelvet és adatvizualizációs eszközöket biztosítanak, amelyek lehetővé teszik az operatív adatelemzést. Az SQL Analytics megoldás számos előre definiált rugalmas készletet és adatbázis-figyelési és riasztási nézetet és lekérdezést biztosít. A Azure Monitor-naplók egyéni nézet-tervezőt is biztosítanak.

Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük. Log Analytics munkaterületek és elemzési megoldások a Azure Portalban nyílnak meg. A Azure Portal az újabb hozzáférési pont, de előfordulhat, hogy az Operations Management Suite-portál mögött bizonyos területeken.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Teljesítmény-diagnosztikai adatok létrehozása a munkaterhelésnek a bérlők számára történő szimulálása révén 

1. A PowerShell ISE-ben nyissa meg a *.. \\ WingtipTicketsSaaS-MultiTenantDb – fő \\ tanulási modulok \\ teljesítményének figyelése és kezelése \\Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa nyitva ezt a szkriptet, mert előfordulhat, hogy az oktatóanyag során több betöltési generációs forgatókönyvet szeretne futtatni.
1. Ha még nem tette volna meg, hozzon létre egy köteget, hogy a figyelési környezet érdekesebb legyen. Ez a folyamat néhány percet vesz igénybe.

   a. Állítsa be a **$DemoScenario = 1**, _bérlők kötegének kiépítése_.

   b. A parancsfájl futtatásához és további 17 bérlő üzembe helyezéséhez nyomja le az F5 billentyűt.

1. Most indítsa el a Load Generatort, hogy futtasson egy szimulált terhelést az összes bérlőn.

    a. Állítsa be a **$DemoScenario = 2**, _normál intenzitású terhelés előállítása (körülbelül 30 DTU)_.

    b. A szkript futtatásához nyomja le az F5 billentyűt.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS-adatbázis-bérlői alkalmazás parancsfájljainak beolvasása

A Wingtip jegyek SaaS több-bérlős adatbázis-szkriptek és az alkalmazás forráskódja a [WingtipTicketsSaaS-DbPerTenant GitHub-](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) tárházban érhető el. A Wingtip tickets PowerShell-parancsfájlok letöltésének és feloldásának lépéseiért tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Log Analytics munkaterület és a Azure SQL Analytics megoldás telepítése és konfigurálása

Azure Monitor egy különálló szolgáltatás, amelyet konfigurálni kell. Azure Monitor naplók a napló adatait, a telemetria és a metrikákat egy Log Analytics munkaterületen gyűjtik. Az Azure-ban más erőforrásokhoz hasonlóan Log Analytics munkaterületet is létre kell hozni. A munkaterületet nem kell ugyanabban az erőforráscsoporthoz létrehozni, mint az általa figyelt alkalmazásokkal. Ezt gyakran a lehető legtöbbet teszi. A Wingtip tickets alkalmazás esetében egyetlen erőforráscsoport használatával győződjön meg arról, hogy a munkaterület törölve lett az alkalmazással.

1. A PowerShell ISE-ben nyissa meg a *.. \\ WingtipTicketsSaaS-MultiTenantDb – fő \\ tanulási modulok \\ teljesítményének figyelése és kezelése \\ log Analytics \\Demo-LogAnalytics.ps1*.
1. A szkript futtatásához nyomja le az F5 billentyűt.

Most már megnyithatja Azure Monitor naplókat a Azure Portal. Néhány percet vesz igénybe, hogy telemetria gyűjtsön a Log Analytics munkaterületen, és láthatóvá tegye azt. Minél tovább hagyja a rendszer a diagnosztikai adatgyűjtést, annál érdekesebb a tapasztalat. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>A készletek és adatbázisok figyelése Log Analytics munkaterület és az SQL Analytics megoldás használata


Ebben a gyakorlatban nyissa meg Log Analytics munkaterületet a Azure Portalban, hogy megtekintse az adatbázisokhoz és készletekhez összegyűjtött telemetria.

1. Tallózással keresse meg a [Azure Portal](https://portal.azure.com). Log Analytics munkaterület megnyitásához válassza a **minden szolgáltatás** lehetőséget. Ezután keressen rá Log Analytics.

   ![Log Analytics munkaterület megnyitása](./media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Válassza ki a _wtploganalytics- &lt; User &gt;_ nevű munkaterületet.

1. Válassza az **Áttekintés** lehetőséget a log Analytics-megoldás megnyitásához a Azure Portalban.

   ![Áttekintés](./media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Eltarthat néhány percig, mielőtt a megoldás aktív lenne. 

1. Válassza ki a **Azure SQL Analytics** csempét a megnyitásához.

    ![Áttekintő csempe](./media/saas-dbpertenant-log-analytics/overview.png)

1. A megoldásban látható nézetek a saját belső görgetősávjának alján vannak görgetve. Szükség esetén frissítse az oldalt.

1. Az összefoglalás lap megismeréséhez válassza ki a csempéket vagy az egyes adatbázisokat a lefúrási Explorer megnyitásához.

    ![Log Analytics-irányítópult](./media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Módosítsa a szűrési beállítást az időtartomány módosításához. Ebben az oktatóanyagban válassza az **elmúlt 1 óra** lehetőséget.

    ![Időszűrő](./media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Válasszon ki egy egyedi adatbázist az adott adatbázis lekérdezési felhasználásának és metrikáinak megismeréséhez.

    ![Adatbázis-elemzés](./media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. A használati metrikák megtekintéséhez görgessen a jobb oldalon található elemzés lapra.
 
     ![Adatbázis-metrikák](./media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Görgessen a bal oldalon található elemzés lapra, és válassza ki a kiszolgáló csempét az **erőforrás-információ** listában.  

    ![Erőforrás-információk listája](./media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Megnyílik egy lap, amely a kiszolgálón lévő készleteket és adatbázisokat jeleníti meg.

    ![Kiszolgáló készletekkel és adatbázisokkal](./media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Válasszon ki egy készletet. A megnyíló készlet lapon görgessen jobbra a készlet metrikáinak megtekintéséhez. 

    ![Készlet metrikái](./media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. A Log Analytics munkaterületen kattintson a **OMS portál** lehetőségre a munkaterület megnyitásához.

    ![Log Analytics-munkaterület](./media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

A Log Analytics munkaterületen további információkért tekintse meg a napló és a metrika adatait. 

Azure Monitor naplók figyelése és riasztásai a munkaterületen lévő adatlekérdezéseken alapulnak, ellentétben a Azure Portal egyes erőforrásaiban definiált riasztásokkal. A riasztások lekérdezések alapján történő létrehozásával egyetlen riasztást is megadhat, amely az összes adatbázist megkeresi, nem pedig egy adatbázis definiálásával. A lekérdezéseket csak a munkaterületen elérhető adatmennyiség korlátozza.

Ha további információt szeretne arról, hogyan használhatók Azure Monitor naplók a riasztások lekérdezéséhez és beállításához, tekintse meg a [riasztási szabályok használata Azure monitor naplókban](../../azure-monitor/alerts/alerts-metric.md)című témakört.

A munkaterületen lévő adatmennyiség alapján Azure Monitor naplókat a SQL Database díjakhoz. Ebben az oktatóanyagban egy ingyenes munkaterületet hozott létre, amely naponta 500 MB-ra van korlátozva. A korlátot a rendszer már nem adja hozzá a munkaterülethez.


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Monitor naplók telepítése és konfigurálása.
> * Készletek és adatbázisok figyelése Azure Monitor naplók használatával.

Próbálja ki a [bérlői elemzési oktatóanyagot](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>További források

* [További oktatóanyagok, amelyek a kezdeti Wingtip jegyek SaaS-adatbázis-bérlői alkalmazás üzembe helyezésére épülnek](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Monitor-naplók](../../azure-monitor/insights/azure-sql.md)