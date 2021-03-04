---
title: Adatok megjelenítése Azure Monitorból | Microsoft Docs
description: A Azure Monitorban tárolt metrika és naplózási adatok megjelenítéséhez elérhető módszerek összegzését tartalmazza.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2021
ms.openlocfilehash: b90d628f0d24e43d7b9f2e3fa87e74d426648c6e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048573"
---
# <a name="visualizing-data-from-azure-monitor"></a>Adatok megjelenítése az Azure Monitorból
Ez a cikk az Azure Monitorban tárolt napló-és metrikai adatok megjelenítéséhez használható módszerek összegzését tartalmazza.

A vizualizációk, például a diagramok és a diagramok segítségével elemezheti a figyelési adatait a problémák részletezése és a minták azonosítása érdekében. A használt eszköztől függően lehetősége van a vizualizációk megosztására is a szervezeten belül és kívül más felhasználókkal.

## <a name="workbooks"></a>Munkafüzetek
A [munkafüzetek](./visualize/workbooks-overview.md) olyan interaktív dokumentumok, amelyek részletesen betekintést nyújtanak az adataiba, a nyomozásba és a csapaton belüli együttműködésbe. Konkrét példák, ahol a munkafüzetek hasznosak a hibaelhárítási útmutatók és az incidens utáni események.

![A diagram a munkafüzetek több oldalának képernyőképeit mutatja be, beleértve az oldal nézeteinek elemzését, a használatot és az oldalon töltött időt.](media/visualizations/workbook.png)

### <a name="advantages"></a>Előnyök
- A a metrikákat és a naplókat is támogatja.
- A támogatja azokat a paramétereket, amelyek interaktív jelentéseket tesznek lehetővé, ahol a táblázatban lévő elemek kiválasztásával dinamikusan frissülnek a társított diagramok és vizualizációk.
- Dokumentumhoz hasonló folyamat.
- Személyes vagy megosztott munkafüzetekhez tartozó lehetőség.
- Egyszerű, együttműködő-barátságos szerzői élmény.
- A sablonok támogatják a nyilvános GitHub-alapú sablon-gyűjteményt.

### <a name="limitations"></a>Korlátozások
- Nincs automatikus frissítés.
- Nincsenek olyan sűrű elrendezések, mint az irányítópultok, amelyek a munkafüzetek számára kevésbé hasznosak, mint az üvegek. További részletek a mélyebb elemzések biztosításához.


## <a name="azure-dashboards"></a>Azure-irányítópultok
Az [Azure irányítópultok](../azure-portal/azure-portal-dashboards.md) az Azure elsődleges irányítópult-technológiái. Különösen hasznosak lehetnek az Azure-infrastruktúra és-szolgáltatások egyetlen ablaktáblájának biztosításában, amely lehetővé teszi a fontos problémák gyors azonosítását.

![A képernyőképen egy példa látható egy testreszabható adatokat tartalmazó Azure-irányítópultra.](media/visualizations/dashboard.png)

Itt látható egy útmutató az irányítópultok létrehozásához.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

### <a name="advantages"></a>Előnyök
- Mélyreható integráció az Azure-ba. A vizualizációk több Azure-oldalról is rögzíthetők az irányítópultokon, például [Metrikaböngésző](essentials/metrics-charts.md), [log Analytics](logs/log-analytics-overview.md)és [Application Insights](app/app-insights-overview.md).
- A a metrikákat és a naplókat is támogatja.
- Több forrásból származó adatok egyesítése, beleértve a [Metrikaböngésző](essentials/metrics-charts.md), a [naplózási lekérdezések](logs/log-query-overview.md)és a [térképek](app/app-map.md) és a rendelkezésre állás eredményét [Application Insightsban](app/app-insights-overview.md).
- Személyes vagy megosztott irányítópultok beállítása. Integrálva van az [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../role-based-access-control/overview.md).
- Automatikus frissítés. A metrikák frissítése az időtartománytól függ, amely legalább öt percet vesz igénybe. A naplók óránként frissülnek, az adott vizualizáción található "frissítés" ikonra kattintva vagy a teljes irányítópult frissítésével igény szerint manuális frissítési lehetőséggel.
- Parametrized metrikai irányítópultok timestamp és egyéni paraméterekkel.
- Rugalmas elrendezési beállítások.
- Teljes képernyős mód.


### <a name="limitations"></a>Korlátozások
- Az adattáblákat nem támogató naplózási vizualizációk korlátozott vezérlése. Az adatsorozatok száma összesen 50-ra van korlátozva, és a további adatsorozatok egy _másik_ gyűjtő alá vannak csoportosítva.
- Nincsenek egyéni paraméterek a naplózási diagramokhoz.
- A naplózási diagramok az elmúlt 30 napban vannak korlátozva.
- A naplózási diagramokat csak megosztott irányítópultokra lehet rögzíteni.
- Nincs interaktivitás az irányítópult-adatkezeléssel.
- Korlátozott környezetfüggő részletezés.


## <a name="power-bi"></a>Power BI
[Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) különösen hasznos az üzleti központú irányítópultok és jelentések létrehozásához, valamint a hosszú távú KPI-trendek elemzését szolgáló jelentésekhez. [A naplók eredményét importálhatja](visualize/powerbi.md) egy Power bi adatkészletbe, így kihasználhatja az olyan funkciók előnyeit, mint például a különböző forrásokból származó adatok egyesítése és a jelentések megosztása a webes és a mobil eszközökön.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Előnyök
- Sokoldalú vizualizációk.
- Széleskörű interaktivitás, beleértve a nagyítást és a szűrést.
- Könnyen megosztható a szervezeten belül.
- Integráció más adatokkal több adatforrásból.
- Jobb teljesítmény az adatkockában gyorsítótárazott eredményekkel.


### <a name="limitations"></a>Korlátozások
- Támogatja a naplókat, de nem metrikákat.
- Nincs Azure-integráció. Az irányítópultok és modellek nem kezelhetők Azure Resource Manageron keresztül.
- A lekérdezési eredményeket a konfiguráláshoz Power BI modellbe kell importálni. Az eredmény méretének és frissítésének korlátozása.
- Korlátozott Adatfrissítés naponta 8 alkalommal.


## <a name="grafana"></a>Grafana
A [Grafana](https://grafana.com/) egy nyílt platform, amely kiemelkedik az operatív irányítópultokon. Ez különösen hasznos az operatív incidensek észleléséhez és elkülönítéséhez, illetve osztályozásakor. Az Azure-előfizetéshez hozzáadhat [Grafana Azure monitor adatforrási beépülő modult](visualize/grafana-plugin.md) , amely az Azure-metrikák adatait jeleníti meg.

![A képernyőképen a Grafana vizualizációk láthatók.](media/visualizations/grafana.png)

### <a name="advantages"></a>Előnyök
- Sokoldalú vizualizációk.
- Az adatforrások gazdag ökoszisztémája.
- Adatinteraktivitás, beleértve a nagyítást is.
- Támogatja a paramétereket.

### <a name="limitations"></a>Korlátozások
- Nincs Azure-integráció. Az irányítópultok és modellek nem kezelhetők Azure Resource Manageron keresztül.
- A Grafana-felhő további Grafana-infrastruktúrájának vagy további díjainak támogatásához.


## <a name="build-your-own-custom-application"></a>Saját egyéni alkalmazás létrehozása
A naplózási és metrikai adatokban lévő adatokhoz a Azure Monitor API-n keresztül férhet hozzá a REST-ügyfél használatával, amely lehetővé teszi saját egyéni webhelyek és alkalmazások létrehozását.

### <a name="advantages"></a>Előnyök
- Teljes rugalmasság a felhasználói felületen, a vizualizációban, az interaktivitásban és a funkciókban.
- Metrikák és adatok naplózása más adatforrásokkal.

### <a name="disadvantages"></a>Hátrányok
- Jelentős mérnöki tevékenység szükséges.


## <a name="azure-monitor-views"></a>Azure Monitor nézetek

> [!IMPORTANT]
> A nézetek elavultak. Tekintse meg a munkafüzetekhez kapcsolódó nézetek konvertálásával kapcsolatos útmutatót a [Azure monitor View Designer a munkafüzetek](visualize/view-designer-conversion-overview.md) számára című témakörben.

[A Azure monitor nézetei](visualize/view-designer.md)  lehetővé teszik, hogy egyéni vizualizációkat hozzon létre a naplózási adattal. Ezeket a [figyelési megoldások](insights/solutions.md) használják a gyűjtött adatok bemutatására.


![Képernyőfelvétel: a tároló-figyelési megoldás csempéje és a kiválasztáskor megnyíló Részletes Azure Monitor nézet.](media/visualizations/view.png)

### <a name="advantages"></a>Előnyök
- Rich vizualizációk a naplózási adatmegjelenítéshez.
- A nézetek exportálásával és importálásával továbbíthatja azokat más erőforráscsoportok és előfizetések számára.
- Integrálható Azure Monitor felügyeleti modellbe munkaterületekkel és figyelési megoldásokkal.
- Egyéni paraméterek [szűrői](visualize/view-designer-filters.md) .
- Interaktív, támogatja a többszintű részletezést (ez a nézet egy másik nézetre is kiterjed)

### <a name="limitations"></a>Korlátozások
- Támogatja a naplókat, de nem metrikákat.
- Nincs személyes nézet. Minden olyan felhasználó számára elérhető, aki hozzáféréssel rendelkezik a munkaterülethez.
- Nincs automatikus frissítés.
- Korlátozott elrendezési beállítások.
- Nem támogatott több munkaterület vagy Application Insights alkalmazások lekérdezése.
- A lekérdezések mérete legfeljebb 8 MB, a lekérdezés végrehajtási ideje pedig 110 másodperc.

## <a name="next-steps"></a>Következő lépések
- Tudnivalók a [Azure monitor által gyűjtött adatokról](data-platform.md).
- Ismerje meg az [Azure-irányítópultokat](../azure-portal/azure-portal-dashboards.md).
- Tudnivalók a [Metrikaböngésző](essentials/metrics-getting-started.md)
- További tudnivalók a [munkafüzetek](./visualize/workbooks-overview.md)használatáról.
- További információ a [naplózási adatainak az Power BIba való importálásáról](./visualize/powerbi.md).
- Ismerkedjen meg a [Grafana Azure monitor adatforrás beépülő modullal](./visualize/grafana-plugin.md).
- A [Azure monitor nézeteinek](visualize/view-designer.md)megismerése.

