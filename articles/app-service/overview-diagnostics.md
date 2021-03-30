---
title: Diagnosztika és megoldási eszköz
description: Ismerje meg, hogy miként lehet elhárítani az alkalmazással kapcsolatos problémákat Azure App Service a diagnosztika és a megoldás eszközzel a Azure Portal.
keywords: App Service, Azure app Service, diagnosztika, támogatás, webalkalmazás, hibaelhárítás, önkiszolgáló Súgó
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: d58341979b0bbe0699a5ca293b20394c43cde1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962808"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service diagnosztika áttekintése

Webalkalmazások futtatásakor elő kell készítenie az esetlegesen felmerülő problémákra, a 500-es hibáktól a felhasználóktól, hogy a webhely nem működik. A App Service Diagnostics egy intelligens és interaktív megoldás, amely segítséget nyújt az alkalmazás konfigurálásához, és nincs szükség konfigurációra. Ha az alkalmazással kapcsolatos problémákba ütközik, App Service a diagnosztika kimutatja, mi a baj, hogy a megfelelő információkhoz vezessen, hogy könnyebben és gyorsan javítsa a problémát.

Bár ez a funkció akkor hasznos, ha az alkalmazással kapcsolatos problémákat tapasztal az elmúlt 24 órában, az összes diagnosztikai gráf mindig elérhető az elemzéshez.

App Service diagnosztika nem csak a Windows rendszeren futó alkalmazás, hanem [Linux/tárolók](./overview.md#app-service-on-linux), [app Service Environment](./environment/intro.md)és [Azure functions](../azure-functions/functions-overview.md)alkalmazások esetében is működik.

## <a name="open-app-service-diagnostics"></a>App Service diagnosztika megnyitása

App Service diagnosztika eléréséhez navigáljon a App Service webalkalmazáshoz vagy App Service Environment a [Azure Portal](https://portal.azure.com). A bal oldali navigációs sávon kattintson a **problémák diagnosztizálása és megoldása** elemre.

Azure Functions esetében navigáljon a Function alkalmazáshoz, és a felső navigációs sávon kattintson a **platform szolgáltatásai** lehetőségre, és válassza a **diagnosztizálás és megoldás** az **erőforrás-kezelésről** szakaszban.

A App Service Diagnostics kezdőlapján kiválaszthatja azt a kategóriát, amely a legjobban leírja az alkalmazással kapcsolatos problémát az egyes homepage-csempék kulcsszavai segítségével. Ezen a lapon a Windows-alkalmazások **diagnosztikai eszközei** is megtalálhatók. Lásd: [diagnosztikai eszközök (csak Windows-alkalmazáshoz)](#diagnostic-tools-only-for-windows-app).

![Kezdőlap](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Ha az alkalmazás nem működik vagy lassú, a [profilkészítési nyomkövetést összegyűjtve](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) azonosíthatja a probléma kiváltó okát. A profilkészítés kis súlyú, és éles környezetekhez van tervezve.
>

## <a name="interactive-interface"></a>Interaktív felület

Miután kiválasztott egy Kezdőlap kategóriát, amely a legjobban megfelel az alkalmazás problémájának, App Service Diagnostics interaktív felülete, a Genie, végigvezeti Önt az alkalmazással kapcsolatos problémák diagnosztizálásán és megoldásán. A Genie által biztosított csempe-parancsikonok használatával megtekintheti az Önt érdeklő probléma kategóriájának teljes diagnosztikai jelentését. A csempék parancsikonjai közvetlen módot biztosítanak a diagnosztikai metrikák elérésére.

![Csempe parancsikonjai](./media/app-service-diagnostics/tile-shortcuts-2.png)

A csempére való kattintás után megtekintheti a csempén bemutatott problémával kapcsolatos témakörök listáját. Ezek a témakörök a teljes jelentésből származó jelentős információk részleteit tartalmazzák. A témakörök bármelyikére kattintva megvizsgálhatja a problémákat. Emellett a **teljes jelentés megtekintése lehetőségre kattintva megtekintheti** az összes témakört egyetlen oldalon.

![Témakörök](./media/app-service-diagnostics/application-logs-insights-3.png)

![Teljes jelentés megtekintése](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnosztikai jelentés

Miután kiválasztotta, hogy a probléma továbbra is megvizsgálható egy témakörre kattintva, megtekintheti a témakörök további részleteit, amelyek a diagramokkal és a Markdowns együtt gyakran kiegészítik a témakört. A diagnosztikai jelentés hatékony eszköz lehet az alkalmazással kapcsolatos problémák azonosításához.

![Diagnosztikai jelentés](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Állapot-kivizsgálás

Ha nem tudja, mi a probléma az alkalmazással, vagy nem tudja, hol kezdje el elhárítani a problémákat, az állapot-kivizsgálás jó kiindulópont. Az állapot-kivizsgálás elemzi az alkalmazásokat, hogy egy gyors, interaktív áttekintést nyújtson, amely kimutatja, hogy mi az egészséges, és mi a baj, és hogy hol érdemes megvizsgálni a problémát. Intelligens és interaktív kezelőfelülete útmutatást nyújt a hibaelhárítási folyamaton keresztül. Az állapot-kivizsgálás integrálva van a Windows-alkalmazások és a webalkalmazások által használt Genie-diagnosztikai jelentéssel a Linux-alkalmazásokhoz.

### <a name="health-checkup-graphs"></a>Állapot-szűrési diagramok

Az állapot-kivizsgálásnak négy különböző gráfja van.

- **kérelmek és hibák:** Egy gráf, amely az elmúlt 24 órában küldött kérések számát jeleníti meg HTTP-kiszolgálói hibákkal együtt.
- **alkalmazás teljesítménye:** Egy gráf, amely megjeleníti az elmúlt 24 órában fellépő válaszidőt a különböző percentilis csoportok esetében.
- **CPU-használat:** Egy gráf, amely az elmúlt 24 órában az egyes példányok százalékos CPU-kihasználtságát mutatja.  
- **memóriahasználat:** Egy gráf, amely az elmúlt 24 órában a teljes százalékos fizikai memóriahasználat megjelenítését mutatja.

![Állapot-kivizsgálás](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Alkalmazás-programkódokkal kapcsolatos problémák vizsgálata (csak Windows-alkalmazás esetén)

Mivel sok alkalmazási probléma kapcsolódik az alkalmazás kódjában felmerülő problémákhoz, App Service diagnosztika integrálva van [Application Insights](../azure-monitor/app/app-insights-overview.md) a kivételek és függőségi problémák kiemelésére a kiválasztott állásidővel való összefüggés érdekében. A Application Insights külön engedélyezni kell.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

A Application Insights kivételek és függőségek megtekintéséhez válassza ki a **webalkalmazást le** vagy a **webalkalmazás lassú** csempe parancsikonjait.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Hibaelhárítási lépések (csak Windows-alkalmazás esetén)

Ha az elmúlt 24 órában egy adott problémás kategóriával kapcsolatos problémát észlel, megtekintheti a teljes diagnosztikai jelentést, és App Service diagnosztika kérheti, hogy tekintse meg a további hibaelhárítási tanácsokat és a további lépéseket a további útmutatásért.

![Application Insights és hibaelhárítás és további lépések](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnosztikai eszközök (csak Windows-alkalmazásokhoz)

A diagnosztikai eszközök több speciális diagnosztikai eszközt is tartalmaznak, amelyek segítségével megvizsgálhatja az alkalmazás kódjával kapcsolatos problémákat, a lassúságot, a kapcsolatok karakterláncait és egyebeket. és proaktív eszközöket, amelyek segítségével csökkentheti a CPU-használattal, a kérelmekkel és a memóriával kapcsolatos problémákat.

### <a name="proactive-cpu-monitoring"></a>Proaktív CPU-figyelés

A proaktív CPU-figyelés lehetővé teszi, hogy egyszerű, proaktív módon végezze el a műveletet, ha az alkalmazás vagy a gyermek folyamata magas CPU-erőforrásokat használ. Megadhatja a saját CPU-küszöbértékeit, hogy a rendszer átmenetileg csökkentse a PROCESSZORok nagy hányadát, amíg a nem várt probléma valódi okot nem talál. További információ: [a CPU-problémák enyhítése, mielőtt azok történnek](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Proaktív CPU-figyelés](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Automatikus gyógyulás és proaktív automatikus javítás

Az automatikus javítás olyan kockázatcsökkentő művelet, amelyet akkor használhat, ha az alkalmazás váratlan viselkedést tapasztal. A kockázatcsökkentő műveletek elindításához a kérelmek száma, a lassú kérés, a memória korlátja és a HTTP-állapotkód alapján állíthatja be a saját szabályait. Az eszköz használatával átmenetileg csökkentheti a váratlan viselkedést, amíg meg nem találja a kiváltó okot. További információ: [az App Service Diagnostics új automatikus gyógyulási élményének bejelentése](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Automatikus javítás](./media/app-service-diagnostics/auto-healing-10.png)

A proaktív CPU-figyeléshez hasonlóan az proaktív automatikus gyógyulás egy kulcsrakész megoldás, amely csökkenti az alkalmazás váratlan viselkedését. Az proaktív automatikus javítás újraindítja az alkalmazást, ha App Service megállapítja, hogy az alkalmazás helyreállíthatatlan állapotban van. További információ: az [proaktív automatikus gyógyítás bemutatása](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigátor és változás elemzése (csak Windows-alkalmazás esetén)

A folyamatos integrációval rendelkező nagyméretű csapatokban, ahol az alkalmazás számos függőséggel rendelkezik, nehéz lehet kijelölni a nem kifogástalan működést okozó változást. A navigátor az alkalmazás topológiájának megjelenítését segíti, és az adott előfizetésben található összes erőforrást automatikusan leképezi. A navigátor segítségével megtekintheti az alkalmazás és a függőségei által végrehajtott módosítások összevont listáját, és leszűkítheti a nem kifogástalan viselkedést okozó változást. A Kezdőlap csempe- **Navigátoron** keresztül érhető el, és az első használata előtt engedélyezni kell. További információ: betekintést [nyerhet az alkalmazás függőségeibe a Navigátorban](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Navigátor alapértelmezett lapja](./media/app-service-diagnostics/navigator-default-page-11.png)

![Diff nézet](./media/app-service-diagnostics/diff-view-12.png)

Az alkalmazások változásainak elemzése a csempe parancsikonjain, az **alkalmazások változásain** és az **alkalmazások összeomlásán** keresztül érhető el a **rendelkezésre állás és a teljesítmény** terén, így egyidejűleg más metrikákkal is használható. A funkció használata előtt engedélyeznie kell azt. További információ: [az új Change Analysis Experience Bejelentése app Service diagnosztikát](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Tegye fel kérdéseit vagy visszajelzéseit a [UserVoice](https://feedback.azure.com/forums/169385-web-apps) címen a "[diag]" cím hozzáadásával.