---
title: Az Azure Application Insights Profiler beállítások panel használata | Microsoft Docs
description: 'Lásd: a Profiler állapota és a profilkészítési munkamenetek elindítása'
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9d9cc377ead0c297e8334d34255bd2c7c7cd39fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86499409"
---
# <a name="configure-application-insights-profiler"></a>Application Insights Profiler konfigurálása

## <a name="updated-profiler-agent"></a>Frissített Profiler-ügynök
Az trigger funkciói csak a Profiler-ügynök 2,6-es vagy újabb verziójával működnek. Ha Azure App Service futtat, akkor az ügynök automatikusan frissül. Megtekintheti, hogy az ügynök melyik verzióját futtatja, ha a webhely kudu URL-címét használja, és hozzáfűzi a \DiagnosticServices a végéhez, a következőhöz hasonló módon:  `https://yourwebsite.scm.azurewebsites.net/diagnosticservices` . A Application Insights Profiler Webjobs 2,6-es vagy újabb verziójúnak kell lennie. A webalkalmazás újraindításával kényszerítheti a frissítést. 

Ha a Profilert egy virtuális gépen vagy egy felhőalapú szolgáltatáson futtatja, akkor a Windows Azure Diagnostics (WAD) bővítmény 16.0.4 vagy újabb verzióját kell telepítenie. A WAD verziójának ellenőrzéséhez jelentkezzen be a virtuális gépre, és tekintse meg a következő könyvtárat: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. A könyvtár neve a telepített WAD verziója. Az Azure-beli virtuálisgép-ügynök automatikusan frissíti a WAD-t, ha új verzió érhető el.

## <a name="profiler-settings-page"></a>Profiler-beállítások lap

Az Azure Application Insights Profiler beállítások panel megnyitásához lépjen a Application Insights teljesítmény ablaktáblára, majd kattintson a **Profiler konfigurálása** gombra.

![Hivatkozás a Profiler-beállítások oldal megnyitására][configure-profiler-entry]

Ekkor megnyílik egy oldal, amely így néz ki:

![Profiler-beállítások lap][configure-profiler-page]

A **Application Insights Profiler konfigurálása** lapon a következő funkciók szerepelnek:

| Szolgáltatás | Leírás |
|-|-|
Profil most | A profilkészítési munkamenetek elindítása minden olyan alkalmazáshoz, amely a Application Insights ezen példányához van csatolva.
Triggerek | Lehetővé teszi olyan eseményindítók konfigurálását, amelyek a Profiler futtatását okozzák. 
Közelmúltbeli profilkészítési munkamenetek | A korábbi profilkészítési munkamenetekkel kapcsolatos információkat jeleníti meg.

## <a name="profile-now"></a>Profil most
Ez a beállítás lehetővé teszi a profilkészítési munkamenet igény szerinti elindítását. Ha erre a hivatkozásra kattint, az összes Profiler-ügynök, amely adatokat küld erre a Application Insights-példányra, megkezdi a profil rögzítését. 5 – 10 perc elteltével a profil-munkamenet az alábbi listában fog megjelenni.

Ahhoz, hogy egy felhasználó manuálisan aktiválja a Profiler-munkamenetet, a Application Insights összetevő szerepköréhez legalább "írási" hozzáférésre van szükségük. A legtöbb esetben automatikusan megkapja ezt a hozzáférést, és nincs szükség további munkára. Ha problémák merülnek fel, a hozzáadandó előfizetési hatókör szerepkör a "Application Insights Component közreműködő" szerepkör lenne. [További információ a szerepköralapú hozzáférés-vezérlésről az Azure monitoring szolgáltatással](./resources-roles-access-control.md).

## <a name="trigger-settings"></a>Trigger beállításai
![Trigger beállításai menü][trigger-settings-flyout]

A menüsávon az eseményindítók gombra kattintva megnyílik az eseményindító-beállítások mező. Beállíthatja a triggert a profilkészítés elindításához, amikor a CPU vagy a memória kihasználtsági aránya eléri a beállított szintet.

| Beállítás | Leírás |
|-|-|
Be/ki gomb | Bekapcsolva: a Profiler elindítható ez a trigger; Kikapcsolva: a Profiler nem indul el ezzel az aktiválással.
Memória küszöbértéke | Ha a memória ezen százaléka használatban van, a Profiler elindul.
Időtartam | Megadja, hogy a rendszer mikor futtassa a Profilert az aktiváláskor.
Hűtő | Azt adja meg, hogy a Profiler mennyi ideig várjon, mielőtt a rendszer elindítja a memóriát vagy a CPU-használatot.

## <a name="recent-profiling-sessions"></a>Közelmúltbeli profilkészítési munkamenetek
Az oldal ezen szakasza a közelmúltbeli profilkészítési munkamenetekkel kapcsolatos információkat jeleníti meg. A profilkészítési munkamenet azt az időtartamot jelenti, amikor a Profiler-ügynök profilt vett az alkalmazást üzemeltető gépek egyikén. A profilokat egy adott munkamenetből is megnyithatja, ha rákattint az egyik sorra. Az egyes munkamenetek esetében a következőt mutatjuk be:

| Beállítás | Leírás |
|-|-|
Aktiválta: | A munkamenet elindítása a trigger, a profil és az alapértelmezett mintavételezés használatával. 
Alkalmazásnév | A létrehozott alkalmazás neve.
Gépi példány | Annak a gépnek a neve, amelyen a Profiler-ügynök futott.
Időbélyeg | A profil rögzítésének ideje.
Farkas | Az egyes kérelmekhez csatolt Nyomkövetések száma.
CPU % | A Profiler futása közben használt CPU százaléka.
Memória | A Profiler futása közben használt memória százalékos aránya.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a> Webes teljesítmény-tesztek használata az alkalmazáshoz való adatforgalom létrehozásához

A Profilert manuálisan is aktiválhatja egyetlen kattintással. Tegyük fel, hogy webes teljesítmény-tesztet futtat. A nyomkövetési szolgáltatással megismerheti, hogy a webalkalmazás hogyan fut a betöltés alatt. A nyomon követés a Nyomkövetések rögzítésekor döntő fontosságú, mert tudja, hogy mikor fog futni a terhelési teszt. Előfordulhat azonban, hogy a véletlenszerű Mintavételezési időköz kimarad.

A következő részekben bemutatjuk, hogyan működik ez a forgatókönyv:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>1. lépés: adatforgalom létrehozása a webalkalmazáshoz a webes teljesítménytesztek elindításával

Ha a webalkalmazás már rendelkezik bejövő forgalommal, vagy ha csak manuálisan szeretné előállítani a forgalmat, ugorja át ezt a szakaszt, és folytassa a 2. lépéssel.

1. A Application Insights portálon válassza a   >  **teljesítmény-tesztelés** konfigurálása elemet. 

1. Új teljesítményteszt indításához kattintson az **új** gombra.

   ![új Teljesítményteszt létrehozása][create-performance-test]

1. Az **új teljesítményteszt** panelen konfigurálja a teszt cél URL-címét. Fogadja el az összes alapértelmezett beállítást, majd válassza a **teszt futtatása** lehetőséget a terhelési teszt futtatásának megkezdéséhez.

    ![Terhelési teszt konfigurálása][configure-performance-test]

    Az új tesztet először a rendszer várólistára helyezi, majd a *folyamatban* állapotot követi.

    ![A terhelési teszt elküldve és várólistán van][load-test-queued]

    ![A terhelési teszt folyamatban van][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>2. lépés: a Profiler igény szerinti munkamenetének elindítása

1. Ha a terhelési teszt fut, indítsa el a Profilert, hogy rögzítse a nyomkövetéseket a webalkalmazásban a terhelés fogadása közben.

1. Nyissa meg a **Profiler konfigurálása** panelt.


### <a name="step-3-view-traces"></a>3. lépés: Nyomkövetések megtekintése

Miután a Profiler befejezte a futtatást, kövesse az értesítésen megjelenő utasításokat a teljesítmény panelre való ugráshoz és a Nyomkövetések megtekintéséhez.

## <a name="next-steps"></a>Következő lépések
[A Profiler engedélyezése és a Nyomkövetések megtekintése](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
