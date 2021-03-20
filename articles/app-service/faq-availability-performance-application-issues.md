---
title: Az alkalmazások teljesítményével kapcsolatos gyakori kérdések
description: Válaszok a rendelkezésre állással, teljesítménnyel és alkalmazásokkal kapcsolatos problémákkal kapcsolatos gyakori kérdésekre Azure App Serviceban.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: dfaeee6a6e2a9728d7e63fb5681c487fbbd6139e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88958999"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Az Azure-Web Apps alkalmazások teljesítményével kapcsolatos gyakori kérdések

> [!NOTE]
> Az alábbi irányelvek némelyike csak Windows vagy Linux App Services esetén működik. A Linux App Services például alapértelmezés szerint 64 bites módban fut.
>

Ez a cikk válaszokat tartalmaz a [Azure App Service Web Apps funkciójának](https://azure.microsoft.com/services/app-service/web/)alkalmazás-teljesítménnyel kapcsolatos problémáira vonatkozó gyakori kérdésekre (GYIK).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Miért lassú az alkalmazásom?

Több tényező is hozzájárulhat a lassú alkalmazások teljesítményéhez. A részletes hibaelhárítási lépéseket lásd: [lassú webalkalmazások teljesítményének hibaelhárítása](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Hogyan a nagy CPU-fogyasztási forgatókönyvet?

Bizonyos nagy CPU-fogyasztási helyzetekben előfordulhat, hogy az alkalmazásnak több számítási erőforrásra van szüksége. Ebben az esetben érdemes lehet magasabb szolgáltatási szintet méretezni, hogy az alkalmazás megkapja az összes szükséges erőforrást. Más időpontokban a nagy CPU-felhasználás oka lehet rossz hurok vagy kódolási gyakorlat. Betekintést nyerhet a megnövelt CPU-fogyasztás indítására. Ez egy kétrészes folyamat. Először hozzon létre egy folyamat-memóriaképet, majd elemezze a folyamat memóriaképét. További információ: [a memóriakép rögzítése és elemzése a nagy CPU-fogyasztáshoz Web Apps](/archive/blogs/asiatech/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Hogyan a nagy memória-felhasználású forgatókönyvek hibakeresését?

Néhány nagy mennyiségű memóriát használó alkalmazás esetében előfordulhat, hogy alkalmazása valóban több számítási erőforrást igényel. Ebben az esetben érdemes lehet magasabb szolgáltatási szintet méretezni, hogy az alkalmazás megkapja az összes szükséges erőforrást. Máskor a kódban szereplő hiba okozhatja a memória szivárgását. A kódolási gyakorlat A memóriahasználat növelését is növelheti. Betekintést nyerhet a nagy memória-használat aktiválására. Ez egy kétrészes folyamat. Először hozzon létre egy folyamat-memóriaképet, majd elemezze a folyamat memóriaképét. Az Azure site Extension Gallery összeomlás-diagnosztizálása hatékonyan hajthatja végre ezeket a lépéseket. További információ: a [memóriakép rögzítése és elemzése a Web Apps időszakos nagy memóriához](/archive/blogs/asiatech/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Hogyan automatizálja App Service webalkalmazásokat a PowerShell használatával?

A PowerShell-parancsmagok használatával felügyelheti és karbantarthatja App Service webalkalmazásait. A blogbejegyzésben a [PowerShell használatával automatizálható webalkalmazások automatizálása Azure app Service](/archive/blogs/puneetgupta/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way)a Azure Resource Manager-alapú PowerShell-parancsmagok használata a gyakori feladatok automatizálásához. A blogbejegyzés a webalkalmazások különböző felügyeleti feladataihoz is tartalmaz mintakód-kódot. Az összes App Service Web Apps-parancsmag leírását és szintaxisát lásd: [az. websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Hogyan megtekintheti a webalkalmazásom eseménynaplóit?

A webalkalmazás eseménynaplóinak megtekintése:

1. Jelentkezzen be a **kudu webhelyére** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. A menüben válassza a **Debug konzol**  >  **cmd** elemet.
3. Válassza a **naplófájlok** mappát.
4. Az eseménynaplók megtekintéséhez válassza a **eventlog.xml** melletti ceruza ikont.
5. A naplók letöltéséhez futtassa a PowerShell-parancsmagot `Save-AzureWebSiteLog -Name webappname` .

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Hogyan a webalkalmazás felhasználói módú memóriaképének rögzítése?

A webalkalmazás felhasználói módú memóriaképének rögzítése:

1. Jelentkezzen be a **kudu webhelyére** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Válassza a **Process Explorer** menüt.
3. Kattintson a jobb gombbal a **w3wp.exe** folyamatra vagy a webjobs folyamatra.
4. Válassza a memóriakép **letöltése**  >  **teljes memóriakép** lehetőséget.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Hogyan megtekintheti a webalkalmazás folyamat-szintű adatait?

A webalkalmazáshoz tartozó folyamat-szintű információk megtekintésére két lehetőség áll rendelkezésre:

*   Az Azure Portalon:
    1. Nyissa meg a webalkalmazáshoz tartozó **Process Explorert** .
    2. A részletek megtekintéséhez válassza ki a **w3wp.exe** folyamatot.
*   A kudu-konzolon:
    1. Jelentkezzen be a **kudu webhelyére** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
    2. Válassza a **Process Explorer** menüt.
    3. A **w3wp.exe** folyamatnál válassza a **Tulajdonságok** lehetőséget.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Amikor megkeresem az alkalmazást, "hiba 403 – ez a webalkalmazás leállt" hibaüzenet jelenik meg. Hogyan oldhatom meg ezt?

A következő három feltétel okozhatja ezt a hibát:

* A webalkalmazás elérte a számlázási korlátot, és a webhely le van tiltva.
* A webalkalmazás le lett állítva a portálon.
* A webalkalmazás elérte az adott erőforrás-kvótára vonatkozó korlátot, amely az ingyenes vagy a megosztott méretezési szolgáltatási csomagra is érvényes lehet.

Ha szeretné megtekinteni, hogy mi okozza a hibát, és hogyan oldja meg a problémát, kövesse a [Web Apps: "hiba 403 – ez a webalkalmazás leállt"](/archive/blogs/waws/azure-web-apps-error-403-this-web-app-is-stopped)című témakör lépéseit.

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Hol tudhatok meg többet a különböző App Service-csomagok kvótái és korlátairól?

További információ a kvótákkal és a korlátozásokkal kapcsolatban: [app Service korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Hogyan csökkentse az első kérelem válaszideje az üresjárati idő után?

Alapértelmezés szerint a webalkalmazások el lesznek távolítva, ha egy meghatározott időtartam alatt tétlenek. Így a rendszeren erőforrásokat takaríthat meg. A hátránya, hogy a webalkalmazás eltávolítását követően az első kérelemre adott válasz már nem érhető el, így a webalkalmazás betöltődik, és megkezdheti a válaszok kiszolgálását. Az alapszintű és a standard szintű szolgáltatási csomagokban a **mindig** bekapcsolva beállítás megadásával megtarthatja az alkalmazás folyamatos betöltését. Ez a várakozási idő elteltével nem töltődik be az alkalmazás üresjárata után. Az **Always On** beállítás módosítása:

1. A Azure Portal nyissa meg a webalkalmazást.
2. **Konfiguráció** kiválasztása
3. Válassza az **általános beállítások** lehetőséget.
4. Az **Always On** beállításnál válassza **a be** lehetőséget.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Hogyan bekapcsolni a sikertelen kérelmek nyomkövetését?

A sikertelen kérelmek nyomkövetésének bekapcsolása:

1. A Azure Portal nyissa meg a webalkalmazást.
3. Válassza **a minden beállítás**  >  **diagnosztikai naplók** lehetőséget.
4. A **Sikertelen kérelmek nyomkövetéséhez** válassza **a be** lehetőséget.
5. Kattintson a **Mentés** gombra.
6. A Web App (webalkalmazás) panelen válassza az **eszközök** lehetőséget.
7. Válassza a **Visual Studio online** lehetőséget.
8. Ha a beállítás nincs **bekapcsolva**, válassza **a be** lehetőséget.
9. Válassza az **Indítás** lehetőséget.
10. Válassza a **Web.config** lehetőséget.
11. A System. webserverben adja hozzá ezt a konfigurációt (egy adott URL-cím rögzítéséhez):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. A lassú teljesítménnyel kapcsolatos problémák elhárítása érdekében adja hozzá ezt a konfigurációt (ha a rögzítési kérelem 30 másodpercnél hosszabb időt vesz igénybe):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. A sikertelen kérelmek nyomkövetésének letöltéséhez nyissa meg a webhelyet a [portálon](https://portal.azure.com).
15. Válassza az **eszközök**  >  **kudu**  >  **Go** lehetőséget.
18. A menüben válassza a **Debug konzol**  >  **cmd** elemet.
19. Jelölje ki a **naplófájlok** mappát, majd válassza ki azt a mappát, amelynek a neve a **W3SVC** karakterlánccal kezdődik.
20. Az XML-fájl megtekintéséhez válassza a ceruza ikont.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>"A (z)" munkavégző folyamat újrahasznosítása a "memória százalékos korlátja miatt" üzenet jelenik meg. Hogyan a probléma megoldásához?

Egy 32 bites folyamat számára rendelkezésre álló memória maximális mennyisége (akár 64 bites operációs rendszeren is) 2 GB. Alapértelmezés szerint a munkavégző folyamat 32 bites értékre van állítva App Service (az örökölt webalkalmazásokkal való kompatibilitás érdekében).

Érdemes lehet a 64 bites folyamatokra váltani, hogy kihasználhassa a webes feldolgozói szerepkörben elérhető további memóriát. Ez elindítja a webalkalmazás újraindítását, ezért az ütemterv szerint kell ütemezni.

Azt is vegye figyelembe, hogy egy 64 bites környezethez alapszintű vagy standard szolgáltatási csomag szükséges. Az ingyenes és a közös csomagok mindig 32 bites környezetben futnak.

További információkért lásd: [webalkalmazások konfigurálása app Serviceban](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Miért van a kérésem időtúllépése 230 másodperc után?

A Azure Load Balancer négy perc alapértelmezett üresjárati időtúllépési beállítással rendelkezik. Ez általában egy webes kérelem ésszerű válaszideje. Ha a webalkalmazásban háttér-feldolgozásra van szükség, javasoljuk, hogy használjon Azure WebJobs. Az Azure-webalkalmazás meghívhatja a webjobs-feladatokat, és értesítést kaphat a háttérben történő feldolgozás befejezésekor. Több módszert is választhat a webjobs-feladatok, például a várólisták és az eseményindítók használatához.

A webjobs a háttérben történő feldolgozásra szolgál. Annyi háttérbeli feldolgozást végezhet, amennyit csak szeretne egy Webjobs. A webjobs-feladatokkal kapcsolatos további információkért lásd: [háttérben futó feladatok futtatása webjobs](webjobs-create.md)-feladatokkal.

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core App Service futtatott alkalmazások néha nem válaszolnak. Hogyan kijavítani ezt a problémát?

A korábbi [vércse-verziók](https://github.com/aspnet/KestrelHttpServer/issues/1182) ismert problémái miatt előfordulhat, hogy egy ASP.net Core 1,0 alkalmazás, amely app Serviceben fut, időnként leállítja a válaszadást. Ezt az üzenetet is láthatja: "a megadott CGI-alkalmazás hibát észlelt, és a kiszolgáló megszakította a folyamatot."

Ez a probléma a vércse 1.0.2-es verziójában van kijavítva. Ez a verzió a ASP.NET Core 1.0.3 frissítés részét képezi. A probléma megoldásához győződjön meg arról, hogy az alkalmazás függőségeit a vércse 1.0.2 használatára frissíti. Azt is megteheti, hogy a blogbejegyzésben ismertetett két megkerülő megoldás egyikét használja [app Service web apps 1,0 ASP.net Core lassú](/archive/blogs/waws/asp-net-core-slow-perf-issues-on-azure-websites)teljesítményű problémák megoldásához.


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Nem találom a naplófájlokat a saját webalkalmazásom fájljának struktúrájában. Hogyan találhatom meg őket?

Ha a App Service helyi gyorsítótár szolgáltatását használja, a rendszer a App Service-példányhoz tartozó LogFiles és adatmappák mappastruktúrát fogja érinteni. Helyi gyorsítótár használata esetén az almappák a Storage-naplófájlokban és az adatmappákban jönnek létre. Az almappákban az "egyedi azonosító" + időbélyegző szerepel. Minden almappa olyan virtuálisgép-példánynak felel meg, amelyben a webalkalmazás fut vagy fut.

Annak megállapításához, hogy a helyi gyorsítótárat használja-e, ellenőrizze az App Service **Alkalmazásbeállítások** lapot. Ha a helyi gyorsítótár használatban van, az alkalmazás beállítása a következőre `WEBSITE_LOCAL_CACHE_OPTION` van beállítva: `Always` .

Ha nem helyi gyorsítótárat használ, és ezt a problémát tapasztalja, küldjön egy támogatási kérést.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>"Kísérlet történt egy szoftvercsatorna elérésére a hozzáférési engedélyeik által tiltott módon." üzenet jelenik meg. Hogyan oldhatom meg ezt?

Ez a hiba általában akkor fordul elő, ha a virtuálisgép-példány kimenő TCP-kapcsolatai kimerültek. App Service a korlátozásokat az egyes virtuálisgép-példányok számára elérhető kimenő kapcsolatok maximális száma miatt kényszeríti a rendszer. További információ: a [virtuális gépek közötti numerikus korlátok](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Ez a hiba akkor is előfordulhat, ha egy helyi IP-címről próbál hozzáférni az alkalmazásból. További információ: [helyi címekre vonatkozó kérelmek](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

A webalkalmazás kimenő kapcsolataival kapcsolatos további információkért tekintse meg az [Azure-webhelyekhez való kimenő kapcsolatokról](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/)szóló blogbejegyzést.

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Hogyan a Visual studiót a App Service webalkalmazásom távoli hibakereséséhez?

A webalkalmazások Visual Studióval való hibakeresését bemutató részletes útmutató: [a app Service webalkalmazás távoli hibakeresése](/archive/blogs/benjaminperkins/remote-debug-your-azure-app-service-web-app).