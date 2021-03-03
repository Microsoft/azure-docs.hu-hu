---
title: Lekérdezések naplózása Azure Monitor
description: Az Azure Monitor által használt Kusto-lekérdezési nyelvre vonatkozó információk. A Azure Monitor és a Azure Monitor napló lekérdezésekben nem támogatott elemek további elemeit tartalmazza.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 7ce73fc69ac0ff88c6048aad39fd07ae802b978d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704225"
---
# <a name="log-queries-in-azure-monitor"></a>Lekérdezések naplózása Azure Monitor
Azure Monitor naplók az Azure Adatkezelőon alapulnak, és a napló lekérdezéseit ugyanazzal a Kusto lekérdezési nyelvvel (KQL) kell írni. Ez egy sokoldalú, könnyen olvasható és elsajátítható nyelv, ezért a lekérdezéseket elindíthatja néhány alapvető útmutatással.

Azok a területek, amelyekben a Azure Monitor a lekérdezéseket fogja használni, a következők:

- [Log Analytics](../logs/log-analytics-overview.md). A Azure Portal elsődleges eszköze a naplók szerkesztéséhez és az eredmények interaktív elemzéséhez. Még akkor is, ha a Azure Monitorban máshol is szeretne egy log-lekérdezést használni, a rendszer általában a végső helyükre másolja és teszteli a Log Analytics.
- [Riasztási szabályok naplózása](../alerts/alerts-overview.md). Proaktív módon azonosíthatja a munkaterületen lévő adatokkal kapcsolatos problémákat.  Minden riasztási szabály egy olyan naplózási lekérdezésen alapul, amely rendszeres időközönként automatikusan fut.  Az eredményeket a rendszer ellenőrzi, hogy létre kell-e hozni egy riasztást.
- [Munkafüzetek](../visualize/workbooks-overview.md). A Azure Portal interaktív vizuális jelentéseiben különböző vizualizációkat használó naplók eredményeinek belefoglalása.
- [Azure-irányítópultok](../visualize/tutorial-logs-dashboards.md). Bármely lekérdezés eredményét rögzítheti egy Azure-irányítópulton, amely lehetővé teszi a naplók és a metrikus adatok együttes megjelenítését, és opcionálisan megoszthatja őket más Azure-felhasználókkal.
- [Logic apps](../logs/logicapp-flow-connector.md).  Az Logic Apps használatával automatizált munkafolyamatban használhatja a naplóbeli lekérdezés eredményét.
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult). A log lekérdezés eredményét egy parancssorból vagy egy, a Get-AzOperationalInsightsSearchResults-t használó Azure Automation runbook származó PowerShell-parancsfájlban használhatja.
- [Azure monitor naplózó API](https://dev.loganalytics.io)-t. Napló adatainak beolvasása a munkaterületről bármely REST API ügyfélről.  Az API-kérelem tartalmaz egy lekérdezést, amely az Azure Monitoron fut, hogy meghatározza a lekérdezni kívánt adatgyűjtést.

## <a name="getting-started"></a>Első lépések
A KQL használatával történő napló-lekérdezések írásához a legjobb módszer az elérhető oktatóanyagok és minták kihasználása.

- [Log Analytics oktatóanyag](./log-analytics-tutorial.md) – a log Analytics funkcióinak használatáról szóló oktatóanyag, amely az Azure Portal a lekérdezések szerkesztéséhez és futtatásához használandó eszköz. Azt is lehetővé teszi, hogy egyszerű lekérdezéseket írjon anélkül, hogy közvetlenül a lekérdezési nyelvet kellene dolgoznia. Ha még nem használta Log Analytics korábban, kezdje el ide, hogy megértse az eszközt, amelyet más oktatóanyagokkal és mintákkal fog használni.
- KQL-oktatóanyag – útmutató az alapszintű KQL-fogalmak és a gyakori operátorok [megismeréséhez](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) . A legjobb megoldás az, ha a nyelvet magával a nyelvvel és a naplók struktúrájával kezdi meg. 
- [Példák a lekérdezésekre](../logs/example-queries.md) – a log Analyticsban elérhető lekérdezések leírása. A lekérdezéseket módosítás nélkül is használhatja, és mintákat is használhat a KQL megismeréséhez.
- [Lekérdezési minták](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) – a különböző fogalmakat bemutató minta lekérdezések.



## <a name="reference-documentation"></a>Dokumentáció
A [KQL dokumentációja](/azure/data-explorer/kusto/query/) , beleértve az összes parancs és operátor hivatkozását az Azure adatkezelő dokumentációjában. Még a KQL használata során is rendszeresen használhatja a hivatkozást a korábban még nem használt új parancsok és forgatókönyvek vizsgálatához.


## <a name="language-differences"></a>Nyelvi különbözőségek
Míg a Azure Monitor ugyanazt a KQL használja, mint az Azure Adatkezelő, van néhány különbség. A KQL dokumentációja megadja azokat az operátorokat, amelyeket a Azure Monitor nem támogat, vagy amelyek eltérő funkciókkal rendelkeznek. Az Azure Monitorhoz tartozó operátorok dokumentálva vannak a Azure Monitor tartalmában. A következő szakaszokban a gyors útmutató a nyelv verziói közötti különbségeket tartalmazza.

### <a name="statements-not-supported-in-azure-monitor"></a>Azure Monitor nem támogatott utasítások

* [Alias](/azure/kusto/query/aliasstatement)
* [Paraméterek lekérdezése](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>A függvények nem támogatottak Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current (), current_cursor ()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure Monitor nem támogatott operátorok

* [Fürtök közötti csatlakozás](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>A beépülő modulok Azure Monitor nem támogatottak

* [Python beépülő modul](/azure/kusto/query/pythonplugin)
* [sql_request beépülő modul](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>További operátorok a Azure Monitor
A következő operátorok bizonyos Azure Monitor szolgáltatásokat támogatnak, és nem érhetők el Azure Monitoron kívül.

* [alkalmazás ()](../logs/app-expression.md)
* [erőforrás ()](./resource-expression.md)
* [munkaterület ()](../logs/workspace-expression.md)

## <a name="next-steps"></a>Következő lépések
- [Útmutató a lekérdezések írásához](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor).
- A [Kusto lekérdezési nyelvének teljes dokumentációjának](/azure/kusto/query/)elérése.