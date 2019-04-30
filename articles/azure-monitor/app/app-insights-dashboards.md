---
title: Irányítópultok és navigáció a az Azure Application Insights |} A Microsoft Docs
description: Az APM kulcsfontosságú diagramokat és lekérdezések létrehozásához.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: afbf2bc32aa737eb5f6dde41035b206d6e260252
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767565"
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Navigáció és irányítópultok az Application Insights portálon
Miután [Application Insights beállítása a projektben](../../azure-monitor/app/app-insights-overview.md), az alkalmazás teljesítményének és használatának telemetriai adatait jelenik meg a projekt Application Insights-erőforrást a [az Azure portal](https://portal.azure.com).

## <a name="find-your-telemetry"></a>A telemetria keresése
Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az alkalmazás számára létrehozott Application Insights-erőforrás.

![Kattintson a Tallózás gombra, és válassza az Application Insights, majd az alkalmazást.](./media/app-insights-dashboards/00-start.png)

Az Áttekintés panelen (oldal) az alkalmazás számára az alkalmazás fő diagnosztikai metrikák összegzését jeleníti meg, és egy átjárót a portálon a más szolgáltatásokhoz.

![Fő útvonalakat a telemetriai adatok megtekintése](./media/app-insights-dashboards/010-oview.png)

A diagramok és táblázatok bármely, és rögzítheti őket az irányítópulton. Ezzel a módszerrel összeállíthatók a legfontosabb telemetriát a különböző alkalmazások egy központi irányítópulton.

## <a name="dashboards"></a>Irányítópultok
Először is látja a bejelentkezés után a [Microsoft Azure-portálon](https://portal.azure.com) egy irányítópult szerepel. Itt összeállíthatók a legfontosabb, Önnek az Azure-erőforrások, beleértve a használati adatok gyűjtése a különböző diagramok [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Egy testreszabott irányítópulttal.](./media/app-insights-dashboards/31.png)

1. **Keresse meg az adott erőforrásokhoz** például az alkalmazás az Application insights szolgáltatásban: A bal oldali sávon használja.
2. **Térjen vissza az aktuális irányítópulton**, vagy a legutóbbi szoftverleltárnézetek kapcsoló: Használja a legördülő menü bal felső sarokban.
3. **Váltás az irányítópultok**: Használja a legördülő menüből az irányítópult címe
4. **Létrehozására, szerkesztésére és oszthat meg irányítópultokat** az irányítópult eszköztáron.
5. **Az irányítópult szerkesztése**: Vigye az egérmutatót egy csempe fölé, majd a felső sávon áthelyezheti, vagy testre szabhatja, távolítsa el.

## <a name="add-to-a-dashboard"></a>Irányítópult hozzáadása
Dolgozik egy panel vagy diagramokat, amely különösen érdekes, amikor egy másolatot, az irányítópulton is rögzíthet. Megjelenik a legközelebb van adja vissza.

![Diagram rögzítése rámutatnak, majd kattintson a "...", a fejlécben.](./media/app-insights-dashboards/33.png)

1. Diagram rögzítése az irányítópulton. A diagram a Másolás megjelenik az irányítópulton.
2. Rögzítés az irányítópulton az egész panelt – az Irányítópulton egy csempe, amely végigkattinthat jelenik meg.
3. Kattintson a bal felső sarokban az aktuális irányítópultra való visszatéréshez. Majd a legördülő menü használatával térjen vissza az aktuális nézet.

Figyelje meg, hogy diagramok csempék vannak csoportosítva: egy csempe több diagramot is tartalmazhat. A teljes csempét az irányítópulton rögzítheti.

A rendszer automatikusan frissíti a diagram, amely attól függ, a diagram időtartomány gyakorisággal:

* Időtartomány fel 1 óra: 5 percenként frissítése
* Időtartomány: 1 – 24 óra: 15 percenként frissítése
* Időtartomány 24 óra feletti: (Időtartomány) / 60.

### <a name="pin-any-query-in-analytics"></a>Analytics minden lekérdezés rögzítése
Emellett [Analytics rögzítése](../../azure-monitor/log-query/get-started-portal.md) a megosztott irányítópultra mutató diagramokat. Ez lehetővé teszi, hogy bármilyen tetszőleges lekérdezés mellett a standard metrikák diagramok. 

Eredmények a rendszer automatikusan újraszámít minden órában. Kattintson a diagramban azonnal számítsa ki újra a frissítés ikont. (Böngésző frissítés nem újraszámítása.)

## <a name="adjust-a-tile-on-the-dashboard"></a>Állítsa be a csempét az irányítópulton
Ha egy csempe az irányítópulton, beállíthatja azt.

![Annak érdekében, hogy szerkesztheti a kurzort a diagram.](./media/app-insights-dashboards/36.png)

1. Diagram hozzáadása csempéhez.
2. Állítsa be a metrika, a csoportosítási dimenzió és a egy diagram stílusa (tábla-, graph).
3. Húzza a nagyításhoz; diagram keresztül alaphelyzetbe állítja a timespan; a Visszavonás gomb Szűrő tulajdonságainak megadása, hogy a diagramok, a csempére.
4. Állítsa be a csempe neve.

Metrika explorer panelek származó csempék vannak rögzítve, mint az Áttekintés panelen rögzített csempék több szerkesztési lehetősége van.

A módosítások nem befolyásolja az eredeti csempét rögzített.

## <a name="switch-between-dashboards"></a>Az irányítópultok közötti váltás
Több irányítópult mentése, és a kettő közötti váltás. Ha rögzít egy diagramot vagy panelen, a rendszer hozzáadja azt az aktuális irányítópulton.

![Az irányítópultok közötti váltáshoz kattintson az irányítópult, és válassza ki, mentett irányítópult. Hozzon létre, és mentse egy új irányítópultot, kattintson az új gombra. Rendezni, kattintson a Szerkesztés gombra.](./media/app-insights-dashboards/32.png)

Előfordulhat például, hogy a csoportszobában, és a egy másik általános üzembe helyezés a teljes képernyős megjelenítése egyetlen irányítópulton.

Az irányítópulton csempe formájában megjelenik egy panel: kattintson rá a paneljéről. A diagram a diagram az eredeti helyére replikálja.

![Kattintson egy csempére nyissa meg a panelt, amely jelöl](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>Irányítópultok megosztása
Ha létrehozott egy irányítópultot, megoszthatja azt más felhasználókkal.

![Az irányítópult fejlécében kattintson a megosztás](./media/app-insights-dashboards/41.png)

Ismerje meg [szerepkörök és hozzáférés-vezérlés](../../azure-monitor/app/resources-roles-access-control.md).

## <a name="create-dashboards-programmatically"></a>Programozott módon létrehozhat irányítópultokat
Irányítópult létrehozása használatával automatizálható [Azure Resource Manager](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) és a egy egyszerű JSON-szerkesztőt.

## <a name="app-navigation"></a>Alkalmazás-navigáció
Az Áttekintés panelen az alkalmazással kapcsolatos további információkat az átjárót.

* **Bármely diagramra vagy a csempe** - Csempékre vagy a diagramon megjelenő információk kapcsolatos további részletek megtekintéséhez.

### <a name="overview-blade-buttons"></a>Áttekintés panelen gombok
![Áttekintés panel felső navigációs sávban](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**Metrikaböngésző** ](../../azure-monitor/app/metrics-explorer.md) – hozzon létre saját diagramokat, teljesítményét és használatát.
* [**Keresés** ](../../azure-monitor/app/diagnostic-search.md) - specifikus példányai, például kérések, kivételek, események vizsgálata vagy a nyomkövetési naplók.
* [**Analytics** ](../../azure-monitor/app/analytics.md) – hatékony telemetriai lekérdezéseket hajthat végre a.
* **Időtartomány** – állítsa be a a panelen a diagramok szerint jelenik meg.
* **Törlés** – az alkalmazás Application Insights-erőforrás törlése. Meg kell is vagy távolítsa el az Application Insights-csomagokat a kód, vagy szerkesztheti a [kialakítási kulcs](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) közvetlen telemetriai adatokat a különböző Application Insights-erőforrást az alkalmazásban.

### <a name="essentials-tab"></a>Essentials tab
* [Rendszerállapotkulcs](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) -azonosítja az alkalmazás-erőforrást.

### <a name="app-navigation-bar"></a>Alkalmazás navigációs sáv
![Bal oldali navigációs sávon](./media/app-insights-dashboards/app-left-nav-bar.png)

* **Áttekintés** – az alkalmazás áttekintése panelre való visszatéréshez.
* **Tevékenységnapló** -riasztások és az Azure felügyeleti események.
* [**Hozzáférés-vezérlés** ](../../azure-monitor/app/resources-roles-access-control.md) -hozzáférés biztosítása a csapattagok és mások.
* [**A címkék** ](../../azure-resource-manager/resource-group-using-tags.md) -címkék használata a csoport az alkalmazás másokkal.

VIZSGÁLAT

* [**Alkalmazás-hozzárendelés** ](app-map.md) – aktív térképként ábrázolja a az alkalmazás összetevőinek a függőségi adatok származnak.
* [**Intelligens detektálás** ](../../azure-monitor/app/proactive-diagnostics.md) – tekintse át a legutóbbi teljesítményével kapcsolatos riasztások.
* [**Élő Stream** ](../../azure-monitor/app/live-stream.md) – A rögzített közel azonnali mérőszámokat, akkor hasznos, ha egy új buildet üzembe helyezéséhez, vagy a hibakeresés.
* [**Rendelkezésre állás / webes teszteket** ](../../azure-monitor/app/monitor-web-app-availability.md) -rendszeres kérelmeket küldjön a webes alkalmazás körül a world.*
* [**Hibák, a teljesítmény** ](../../azure-monitor/app/web-monitor-performance.md) -kivételek, hibaarányok és a válaszidők a kérelmek az alkalmazás és az alkalmazás kéréseit [függőségek](../../azure-monitor/app/asp-net-dependencies.md).
* [**Teljesítmény** ](../../azure-monitor/app/web-monitor-performance.md) -válaszidőt, a válaszidők függőségi.
* [Kiszolgálók](../../azure-monitor/app/web-monitor-performance.md) -teljesítményszámlálók. Ha elérhető, [telepítse az Állapotfigyelőt](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Böngésző** -nézet és az AJAX teljesítménye lapon. Ha elérhető, [alakítsa ki a weblapok](../../azure-monitor/app/javascript.md).
* **Használati** -lapon nézet, felhasználók és munkamenetek száma. Ha elérhető, [alakítsa ki a weblapok](../../azure-monitor/app/javascript.md).

KONFIGURÁLÁS

* **Első lépések** – beágyazott oktatóanyag.
* **Tulajdonságok** -kialakítási kulcsot, az előfizetés és az erőforrás-azonosító.
* [Riasztások](../../azure-monitor/app/alerts.md) -riasztási konfigurációja.
* [A folyamatos exportálás](../../azure-monitor/app/export-telemetry.md) -konfigurálása az Azure storage-telemetria exportálása.
* [Teljesítménytesztelés](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests) – állítsa be a webhely szintetikus terhelését.
* [Kvóta és díjszabás](../../azure-monitor/app/pricing.md) és [betöltési mintavételt](../../azure-monitor/app/sampling.md).
* **API-hozzáférés** -létrehozása [kiadási jegyzetek](annotations.md) és az adatok hozzáférés API-hoz.
* [**Munkaelemek** ](../../azure-monitor/app/diagnostic-search.md#create-work-item) -követési rendszer, így hozhat létre hibák telemetriai vizsgálata során munkahelyi csatlakozás.

BEÁLLÍTÁSOK

* [**Zárolja** ](../../azure-resource-manager/resource-group-lock-resources.md) – zárolhatja az Azure-erőforrások
* [**Automation-szkript** ](../../azure-monitor/app/powershell.md) -Exportálás egy az Azure-erőforrás definícióját, hogy ezzel sablonként új erőforrásokat hozhatnak létre.


## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>További lépések

|  |  |
| --- | --- |
| [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md)<br/>Szegmentálására és szűrésére is metrikák |![Keresés példa](./media/app-insights-dashboards/64.png) |
| [Diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md)<br/>Keresse meg és események, kapcsolódó események vizsgálata és létrehozása a hibák |![Keresés példa](./media/app-insights-dashboards/61.png) |
| [Elemzés](../../azure-monitor/app/analytics.md)<br/>Hatékony lekérdezési nyelvet |![Keresés példa](./media/app-insights-dashboards/63.png) |
