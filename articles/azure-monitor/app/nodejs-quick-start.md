---
title: 'Gyors útmutató: Node.js figyelése Azure Monitor Application Insights'
description: Útmutatást nyújt egy Node.js webalkalmazás gyors beállításához Azure Monitor-alapú figyeléshez Application Insights
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 96a60c88997226516d451124280053d87065e5b6
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106381943"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Gyors útmutató: Node.js webalkalmazás figyelésének megkezdése az Azure-ban Application Insights

Ebben a rövid útmutatóban egy meglévő Node.js webalkalmazáshoz adja hozzá a Node.js Application Insights SDK 0,22-es verzióját.

Az Azure Application Insights segítségével egyszerűen monitorozhatja webalkalmazása rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat. Az SDK 0.20-as verziós kiadásától kezdve a gyakori külsős csomagok, például a MongoDB, a MySQL és a Redis monitorozása is lehetségessé vált.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Működő Node.js alkalmazás.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

A Application Insights bármely internetkapcsolattal rendelkező alkalmazásból gyűjthet telemetria adatokat, függetlenül attól, hogy az a helyszínen vagy a felhőben fut-e. Az adatok megjelenítéséhez hajtsa végre az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza **az erőforrás létrehozása**  >  **fejlesztői eszközök**  >  **Application Insights** elemet.

   ![Azure Application Insights-erőforrás hozzáadása](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Ha első alkalommal hoz létre egy Application Insights-erőforrást, további információt az [Application Insights erőforrás létrehozása](../app/create-new-resource.md) doc webhelyén olvashat.

   Megjelenik egy konfigurációs oldal; a beviteli mezők kitöltéséhez használja az alábbi táblázatot. 

    | Beállítások        | Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Név**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Erőforráscsoport**     | myResourceGroup      | Az új erőforráscsoport neve a AppInsights-adattároláshoz. Létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt is. |
   | **Hely** | USA keleti régiója | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

3. Válassza a **Létrehozás** lehetőséget.

## <a name="configure-appinsights-sdk"></a>A AppInsights SDK konfigurálása

1. Válassza az **Áttekintés** lehetőséget, és másolja az alkalmazás kialakítási **kulcsát**.

   ![A Application Insights rendszerállapot-kulcs megtekintése](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Adja hozzá a Node.js-hez készült Application Insights SDK-t az alkalmazáshoz. Az alkalmazás gyökérmappájából futtassa a következőket:

   ```bash
   npm install applicationinsights --save
   ```

3. Szerkessze az alkalmazás első *. js* fájlját, és adja hozzá az alábbi két sort a szkript legfelső részéhez. Ha a [Node.js Gyorsindítás alkalmazást](../../app-service/quickstart-nodejs.md)használja, akkor módosítania kell a *index.js* fájlt. Cserélje le az `<instrumentation_key>` alkalmazást az alkalmazás betekintési kulcsára. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Indítsa újra az alkalmazást.

> [!NOTE]
> 3–5 percet is igénybe vehet, mire az adatok elkezdenek megjelenni a portálon. Ha az alkalmazás egy alacsony forgalmú tesztalkalmazás, vegye figyelembe, hogy a legtöbb metrika rögzítése csak akkor történik, ha aktív kérések és műveletek vannak folyamatban.

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Most megnyithatja ismét az Application Insights **Áttekintés** lapját az Azure Portalon (ahol a kialakítási kulcsot is megjelenítette) a jelenleg futó alkalmazás részleteinek megtekintéséhez.

   ![Application Insights áttekintése menü](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Válassza ki az alkalmazás-összetevők közötti függőségi kapcsolatok vizualizációs elrendezéséhez tartozó **Application Map** elemet. Minden egyes összetevőnél megjelennek a KPI-k, például a terhelés, a teljesítmény, a hibák és a riasztások.

   ![Application Insights alkalmazás-hozzárendelés](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Válassza az **app Analytics** ikon ![ alkalmazás-hozzárendelés ikon ](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **nézetét az elemzés elemnél**.  Ez a művelet megnyitja **Application Insights Analytics szolgáltatást**, amely részletes lekérdezési nyelvet biztosít a Application Insights által összegyűjtött összes információ elemzéséhez. Esetünkben most egy lekérdezés jön létre, amely a kérések számát egy diagramon jeleníti meg. A további adatok elemzéséhez írhat saját lekérdezéseket is.

   ![Application Insights elemzési gráfok](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Lépjen vissza az **Áttekintés** lapra, és vizsgálja meg a KPI-gráfokat.  Ezen az irányítópulton az alkalmazás állapotával kapcsolatos statisztikák jelennek meg, köztük a bejövő kérések száma, az egyes kérések időtartama, valamint az esetleges hibák.

   ![Application Insights állapot áttekintése – ütemterv diagramjai](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   Ahhoz, hogy a **Lapmegtekintés betöltési ideje** diagramon megjelenjenek az **ügyféloldali telemetria** adatai, adja az alábbi szkriptet minden olyan oldalhoz, amelyet nyomon kíván követni:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. A bal oldalon válassza az **Metrikák** lehetőséget. Az erőforrás állapotának és kihasználtságának vizsgálatához használja a metrikák Explorert. Az **új diagram hozzáadása** lehetőség kiválasztásával további egyéni nézeteket hozhat létre, vagy a **Szerkesztés** gombra kattintva módosíthatja a meglévő diagramtípust, a magasságot, a színpalettát, a csoportosításokat és a metrikákat. Létrehozhat például egy olyan diagramot, amely megjeleníti a böngésző átlagos betöltési idejét, ha a mérőszámok legördülő menüből kiválasztja a "böngésző oldal betöltési ideje" lehetőséget, és az összesítést az "AVG" értékre. Ha többet szeretne megtudni az Azure Metrikaböngésző-ról, látogasson el [az azure Metrikaböngésző használatába](../essentials/metrics-getting-started.md).

   ![Application Insights kiszolgálói metrikák gráfja](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

A Node.js figyelésével kapcsolatos további információkért tekintse meg a [további AppInsights Node.js dokumentációját](../app/nodejs.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a teszteléssel, törölheti az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez kövesse az alábbi lépéseket.

> [!NOTE]
> Ha meglévő erőforráscsoportot használt, az alábbi utasítások nem fognak működni, és csak törölni kell az egyéni Application Insights erőforrást. Ne feledje, hogy bármikor törli az erőforráscsoportot az összes olyan underyling-erőforrást, amely tagja a csoportnak.

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget, majd válassza a **myResourceGroup** lehetőséget.
2. Az erőforráscsoport lapon válassza a **Törlés** lehetőséget, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Teljesítményproblémák észlelése és diagnosztizálása](../logs/log-query-overview.md)

