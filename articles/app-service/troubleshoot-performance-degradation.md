---
title: A teljesítmény romlásának hibája
description: Ismerje meg, hogyan lehet elhárítani a lassú alkalmazások teljesítményével kapcsolatos problémákat a Azure App Serviceban, beleértve az alkalmazások viselkedésének figyelését, az adatok gyűjtését és a probléma enyhítését.
tags: top-support-issue
keywords: webalkalmazás teljesítménye, lassú alkalmazás, alkalmazás lassú
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: e048e4c17092c71a5adaf29f342546b9258837c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100586241"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>A lassú alkalmazások teljesítményével kapcsolatos hibák elhárítása Azure App Service
Ez a cikk segítséget nyújt a lassú alkalmazások teljesítményével kapcsolatos hibák elhárításában [Azure app Service](./overview.md).

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban és a stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Azt is megteheti, hogy Azure-támogatási incidenst is beküld. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és kattintson a **támogatás kérése** lehetőségre.

## <a name="symptom"></a>Hibajelenség
Amikor megkeresi az alkalmazást, az oldalak lassan és időnként időtúllépéssel töltődnek be.

## <a name="cause"></a>Ok
Ezt a problémát gyakran az alkalmazási szintű problémák okozzák, például:

* a hálózati kérelmek hosszú időt vesznek igénybe
* az alkalmazás kódja vagy az adatbázis lekérdezése nem hatékony
* nagy memóriát/CPU-t használó alkalmazás
* az alkalmazás összeomlik egy kivétel miatt

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A hibaelhárítás három különálló feladatra osztható, szekvenciális sorrendben:

1. [Az alkalmazások viselkedésének megfigyelése és figyelése](#observe)
2. [Adatok gyűjtése](#collect)
3. [A probléma enyhítése](#mitigate)

[App Service](overview.md) az egyes lépésekben különböző lehetőségeket biztosít.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. az alkalmazások viselkedésének megfigyelése és figyelése
#### <a name="track-service-health"></a>Szolgáltatás állapotának nyomon követése
Microsoft Azure minden alkalommal nyilvánosságra kerül, amikor a szolgáltatás megszakad vagy a teljesítmény romlása. A szolgáltatás állapotát a [Azure Portal](https://portal.azure.com/)követheti nyomon. További információ: a [szolgáltatás állapotának nyomon követése](../service-health/service-notifications.md).

#### <a name="monitor-your-app"></a>Az alkalmazás figyelése
Ezzel a beállítással megtudhatja, hogy az alkalmazás problémákba ütközik-e. Az alkalmazás paneljén kattintson a **kérelmek és hibák** csempére. A **metrika** panel megjeleníti az összes felvehető mérőszámot.

Előfordulhat, hogy az alkalmazáshoz figyelni kívánt metrikák némelyike

* Memória átlagos munkakészlete
* Átlagos válaszidő
* Processzoridő
* Memória munkakészlete
* Kérelmek

![alkalmazás teljesítményének figyelése](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

További információkért lásd:

* [Alkalmazások figyelése Azure App Service](web-sites-monitor.md)
* [Riasztási értesítések fogadása](../azure-monitor/alerts/alerts-overview.md)

#### <a name="monitor-web-endpoint-status"></a>Webes végpont állapotának figyelése
Ha az alkalmazást a **standard** szintű díjszabásban futtatja, app Service két végpont figyelését teszi lehetővé három földrajzi helyről.

A végpontok figyelése olyan földrajzilag elosztott helyekről konfigurálja a webes teszteket, amelyek a válaszadási időt és a webes URL-címek üzemidőét tesztelik. A teszt végrehajt egy HTTP GET műveletet a webes URL-címen, hogy meghatározza a válaszidő és az üzemidőt az egyes helyekről. Minden konfigurált hely 5 percenként futtat egy tesztet.

A üzemidőt a HTTP-válasz kódok használatával figyeli a rendszer, a válaszidő mérése pedig ezredmásodpercben történik. A figyelési teszt sikertelen, ha a HTTP-válasz kódja nagyobb vagy egyenlő, mint 400, vagy ha a válasz 30 másodpercnél hosszabb időt vesz igénybe. Egy végpont akkor tekinthető elérhetőnek, ha a figyelési tesztek az összes megadott helyről sikeresek voltak.

A beállításához lásd: [alkalmazások figyelése Azure app Serviceban](web-sites-monitor.md).

Lásd még: az [Azure-webhelyek és a végpontok figyelése – Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) a végpontok figyelésével kapcsolatos videókhoz.

#### <a name="application-performance-monitoring-using-extensions"></a>Alkalmazások teljesítményének monitorozása bővítmények használatával
Az alkalmazások teljesítményét a *hely bővítményének* használatával is nyomon követheti.

Az egyes App Service alkalmazások egy bővíthető felügyeleti végpontot biztosítanak, amely lehetővé teszi, hogy a helyi bővítményként telepített eszközök hatékony készletét használja. A bővítmények a következők: 

- Forráskód-szerkesztők, például az [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- A csatlakoztatott erőforrások, például egy alkalmazáshoz csatlakoztatott MySQL-adatbázis felügyeleti eszközei.

Az [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) egy teljesítmény-figyelési hely kiterjesztése, amely szintén elérhető. Application Insights használatához újra kell építenie a kódot egy SDK-val. Olyan bővítményt is telepíthet, amely hozzáférést biztosít a további információkhoz. Az SDK lehetővé teszi, hogy kódot írjon az alkalmazás használatának és teljesítményének figyelésére részletesebben. További információ: [a teljesítmény figyelése a webalkalmazásokban](../azure-monitor/app/app-insights-overview.md).

<a name="collect"></a>

### <a name="2-collect-data"></a>2. adatgyűjtés
A App Service diagnosztikai funkciókat biztosít a webkiszolgálóról és a webalkalmazásból származó adatok naplózásához. Az információk a webkiszolgáló-diagnosztika és az Application Diagnostics szolgáltatásban vannak elkülönítve.

#### <a name="enable-web-server-diagnostics"></a>Webkiszolgáló-diagnosztika engedélyezése
Engedélyezheti vagy letilthatja a következő típusú naplókat:

* **Részletes hiba naplózása** – a hibát jelző HTTP-állapotkódok részletes információi (400 vagy újabb állapotkód). Ez olyan információkat tartalmazhat, amelyek segíthetnek meghatározni, hogy a kiszolgáló miért adta vissza a hibakódot.
* **Sikertelen kérelmek nyomkövetése** – részletes információk a sikertelen kérésekről, beleértve a kérés feldolgozásához és az egyes összetevőkben használt IIS-összetevők nyomkövetését. Ez akkor lehet hasznos, ha az alkalmazás teljesítményének javítására vagy egy adott HTTP-hiba okozta elkülönítésére tesz kísérletet.
* **Webkiszolgáló-naplózás** – a W3C bővített naplófájl formátumával http-tranzakciókra vonatkozó információk. Ez hasznos lehet az alkalmazás általános metrikáinak, például a kezelt kérelmek számának vagy egy adott IP-címről érkező kérések meghatározásakor.

#### <a name="enable-application-diagnostics"></a>Application Diagnostics engedélyezése
Számos lehetőség áll rendelkezésre az alkalmazások teljesítményadatait App Serviceból való összegyűjtésére, az alkalmazás a Visual studióból való beolvasására, vagy az alkalmazás kódjának módosítására további információk és Nyomkövetések naplózása érdekében. A beállításokat az alkalmazáshoz és a figyelési eszközöktől megfigyelt hozzáférések alapján választhatja ki.

##### <a name="use-application-insights-profiler"></a>Application Insights Profiler használata
Engedélyezheti a Application Insights Profiler a részletes teljesítmény-nyomkövetés rögzítésének megkezdéséhez. A nyomkövetéseket akár öt nappal ezelőtt is elérheti, ha a múltban történt problémák vizsgálatára van szükség. Ezt a beállítást akkor válassza, ha az alkalmazás Application Insights erőforrását Azure Portalon szeretné elérni.

Application Insights Profiler statisztikai adatokat biztosít minden olyan webes hívás és nyomkövetés esetében, amely azt jelzi, hogy a kód mely sorában okozta a lassú válaszokat. Előfordulhat, hogy a App Service alkalmazás lassú, mert bizonyos kódok nem a megfelelő módon íródnak. Ilyenek például a szekvenciális kód, amely párhuzamos és nem kívánt adatbázis-zárolási tartalommal futtatható. Ha eltávolítja ezeket a szűk keresztmetszeteket a kódban, az megnöveli az alkalmazás teljesítményét, de nehéz felderíteni, hogy nem lettek kidolgozva a Nyomkövetések és a naplók. A Application Insights Profiler által összegyűjtött nyomkövetési funkció segítségével azonosíthatja a kód azon sorait, amelyek lelassítják az alkalmazást, és elhárítják ezt a kihívást App Service alkalmazások esetében.

 További információ: a [Azure app Service élő alkalmazásainak profilkészítése a Application Insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Távoli profilkészítés használata
A Azure App Service a webalkalmazások, az API-alkalmazások, a mobil háttérrendszer és a webjobs távolról is létrehozhatók. Akkor válassza ezt a lehetőséget, ha rendelkezik hozzáféréssel az alkalmazás-erőforráshoz, és tudja, hogyan reprodukálja a problémát, vagy ha ismeri a probléma pontos időintervallumát.

A távoli profilkészítés akkor hasznos, ha a folyamat CPU-használata magas, és a folyamat a vártnál lassabban fut, vagy ha a HTTP-kérések késése a Normálnál magasabb, a folyamat távolról is feldolgozható, és lekérheti a CPU mintavételezési hívását, hogy elemezze a folyamat tevékenységeit és a kód gyors elérési útját.

További információ: [távoli profilkészítés támogatása Azure app Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Diagnosztikai Nyomkövetések manuális beállítása
Ha rendelkezik hozzáféréssel a webalkalmazás forráskódhoz, az Application Diagnostics lehetővé teszi a webalkalmazások által létrehozott információk rögzítését. A ASP.NET-alkalmazások az `System.Diagnostics.Trace` osztály használatával naplózzák az adatokat az Application Diagnostics-naplóba. Azonban módosítania kell a kódot, és újra kell telepítenie az alkalmazást. Ez a módszer akkor javasolt, ha az alkalmazás tesztelési környezetben fut.

Az alkalmazás naplózáshoz való konfigurálásának részletes ismertetését lásd: a [diagnosztikai naplózás engedélyezése a Azure app Service alkalmazásokhoz](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>A diagnosztikai eszköz használata
A App Service egy intelligens és interaktív élményt nyújt, amely segít a szükséges konfigurációval kapcsolatos hibák megoldásában. Ha az alkalmazással kapcsolatos problémákba ütközik, a diagnosztikai eszköz kimutatja, hogy mi a baj, hogy a probléma megoldásához könnyebben és gyorsan javítsa a megfelelő információkat.

App Service diagnosztika eléréséhez nyissa meg a App Service alkalmazást vagy App Service Environment a [Azure Portalban](https://portal.azure.com). A bal oldali navigációs sávon kattintson a **problémák diagnosztizálása és megoldása** elemre.

#### <a name="use-the-kudu-debug-console"></a>A kudu hibakeresési konzoljának használata
A App Service egy hibakeresési konzolt tartalmaz, amellyel hibakeresést végezhet, megvizsgálhatja, feltöltheti és feltölthet fájlokat, valamint JSON-végpontokat a környezettel kapcsolatos információk beszerzéséhez. Ezt a konzolt az alkalmazás *kudu-konzoljának* vagy *SCM-irányítópultjának* nevezzük.

Ehhez az irányítópulthoz az **https:// &lt;>. SCM.azurewebsites.net/** hivatkozást kell megnyitnia.

A kudu által biztosított néhány dolog:

* az alkalmazás környezeti beállításai
* napló Stream
* diagnosztikai memóriakép
* hibakeresési konzol, amelyen PowerShell-parancsmagokat és alapszintű DOS-parancsokat futtathat.

A kudu egy másik hasznos funkciója, hogy ha az alkalmazás első alkalommal kivételeket vált ki, akkor a kudu és a SysInternals eszköz Procdump használatával hozhat létre memóriaképeket. Ezek a memóriaképek a folyamat pillanatképei, és gyakran segítenek az alkalmazással kapcsolatos bonyolultabb problémák megoldásában.

A kudu-ben elérhető szolgáltatásokkal kapcsolatos további információkért lásd: [Azure DevOps-eszközök](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. a probléma enyhítése
#### <a name="scale-the-app"></a>Az alkalmazás méretezése
Azure App Service a teljesítmény és az átviteli sebesség növelése érdekében módosíthatja azt a méretezést, amelyen az alkalmazást futtatja. Az alkalmazások horizontális felskálázása két kapcsolódó művelettel jár: a App Service terv magasabb árképzési szintre való módosításával, valamint bizonyos beállítások konfigurálásával, miután átváltotta a magasabb díjszabási szintet.

További információ a skálázásról: [alkalmazások méretezése Azure app Serviceban](manage-scale-up.md).

Emellett dönthet úgy is, hogy az alkalmazást egynél több példányon futtatja. A horizontális felskálázás nem csupán nagyobb feldolgozási képességet biztosít, ugyanakkor bizonyos mennyiségű hibatűrést is biztosít. Ha a folyamat egy példányon leáll, a többi példány továbbra is kéri a kérelmek kiszolgálását.

Beállíthatja, hogy a méretezés manuális vagy automatikus legyen.

#### <a name="use-autoheal"></a>Az autoheal használata
Az automatikus gyógyulás a választott beállítások alapján újrahasznosítja az alkalmazás munkavégző folyamatát (például a konfiguráció módosításait, a kérelmeket, a memória alapú korlátokat vagy a kérelem végrehajtásához szükséges időt). A legtöbb esetben a folyamat újrahasznosítása a leggyorsabb módszer a probléma megoldására. Bár bármikor újraindíthatja az alkalmazást közvetlenül a Azure Portal belül, az automatikus javítás automatikusan elvégzi Önt. Mindössze néhány eseményindítót kell felvennie az alkalmazás legfelső web.config. Ezek a beállítások ugyanúgy működnek, még akkor is, ha az alkalmazás nem .NET-alkalmazás.

További információ: az [Azure webhelyek automatikus](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)javítása.

#### <a name="restart-the-app"></a>Az alkalmazás újraindítása
Az újraindítás gyakran a legegyszerűbb módszer az egyszeri problémák elhárítására. A [Azure Portal](https://portal.azure.com/)az alkalmazás paneljén lehetősége van az alkalmazás leállítására vagy újraindítására.

 ![az alkalmazás újraindítása a teljesítménnyel kapcsolatos problémák megoldásához](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Az alkalmazást Azure PowerShell használatával is kezelheti. További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](../azure-resource-manager/management/manage-resources-powershell.md).