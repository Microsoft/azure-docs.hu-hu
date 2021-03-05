---
title: Az Azure Application Insights Profiler kapcsolatos problémák elhárítása
description: Ez a cikk hibaelhárítási lépéseket és információkat nyújt a fejlesztők számára a Application Insights Profiler engedélyezéséhez és használatához.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 622a83c6d91bf2a30c2844e3279d6fd4b89d429f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213793"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Az Application Insights Profiler engedélyezésével vagy megtekintésével kapcsolatos problémák elhárítása

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Általános hibaelhárítás

### <a name="make-sure-youre-using-the-appropriate-profiler-endpoint"></a>Győződjön meg arról, hogy a megfelelő Profiler-végpontot használja

Jelenleg csak a végpontok módosítását igénylő régiók [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) és az [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide).

|Alkalmazásbeállítás    | Egyesült államokbeli kormányzati felhő | Kínai felhő |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>A profilok csak akkor lesznek feltöltve, ha a Profiler futtatása közben kérések vannak az alkalmazáshoz

Az Azure Application Insights Profiler óránként két percig gyűjt adatokat. Emellett adatokat is gyűjthet, amikor kiválasztja a **profil most** gombot a **Konfigurálás Application Insights Profiler** ablaktáblán.

> [!NOTE]
> A profilkészítési adatgyűjtés csak akkor történik meg, ha csatolható egy, a Profiler futtatása közben történt kérelemhez. 

A Profiler nyomkövetési üzeneteket és egyéni eseményeket ír a Application Insights erőforrásba. Ezen események használatával megtekintheti, hogyan fut a Profiler:

1. Keressen nyomkövetési üzeneteket és a Profiler által a Application Insights-erőforrásnak küldött egyéni eseményeket. Ezt a keresési karakterláncot használhatja a releváns adatokat megkeresni:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Az alábbi ábrán két AI-erőforrásból származó keresések jelennek meg: 
    
   * A bal oldalon az alkalmazás nem kap kéréseket, amíg a Profiler fut. Az üzenet azt mutatja be, hogy a feltöltést nem a tevékenység miatt szakították meg. 

   * A jobb oldalon a Profiler megkezdte és elküldötte az egyéni eseményeket, amikor a Profiler futtatása közben történt kérelmeket észlelt. Ha az `ServiceProfilerSample` egyéni esemény megjelenik, az azt jelenti, hogy a rendszer rögzíti a profilt, és az elérhetővé válik a **Application Insights teljesítmény** ablaktáblán.

     Ha nem jelennek meg rekordok, a Profiler nem fut. A hibaelhárításhoz tekintse meg a jelen cikk későbbi, az adott alkalmazásra vonatkozó hibaelhárítási részeit.  

     ![A Profiler telemetria keresése][profiler-search-telemetry]

### <a name="other-things-to-check"></a>További ellenőrzési dolgok
* Győződjön meg arról, hogy az alkalmazás a .NET-keretrendszer 4,6-es verzióját futtatja.
* Ha a webalkalmazás egy ASP.NET Core alkalmazás, legalább ASP.NET Core 2,0-at kell futtatnia.
* Ha a megtekinteni kívánt adatmennyiség régebbi, mint néhány hétig, próbálja meg korlátozni az időszűrőt, és próbálkozzon újra. A Nyomkövetések hét nap után törlődnek.
* Győződjön meg arról, hogy a proxyk vagy a tűzfal nem tiltotta le a hozzáférést a következőhöz: https://gateway.azureserviceprofiler.net .
* A Profiler ingyenes vagy megosztott app Service-csomagok esetén nem támogatott. Ha ezen csomagok valamelyikét használja, próbálja meg az alapszintű csomagok és a Profiler használatának megkezdését.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dupla számlálás párhuzamos szálakban

Bizonyos esetekben a verem-megjelenítőben lévő teljes időmetrika meghaladja a kérelem időtartamát.

Ez a helyzet akkor fordulhat elő, ha két vagy több párhuzamos szál van társítva egy kérelemhez. Ebben az esetben a szál teljes ideje meghaladja az eltelt időt.

Lehet, hogy egy szál várakozik a másikra. A megjelenítő megpróbálja felderíteni ezt a helyzetet, és kihagyja a nem érdekes várakozást. Ennek során a téved túl sok információt jelenít meg, nem pedig kihagyhatja, hogy mi lehet a kritikus információ.

Ha a nyomkövetésekben párhuzamos szálak láthatók, határozza meg, hogy mely szálak várnak el, hogy azonosítsa a kéréshez szükséges elérési utat.

Általában az a szál, amely gyorsan befejeződik a várakozási állapotba, egyszerűen a többi szálra vár. Koncentráljon a többi szálra, és hagyja figyelmen kívül a várakozási szálak időpontját.

### <a name="error-report-in-the-profile-viewer"></a>Hibajelentés a profil megjelenítőben
Támogatási jegy küldése a portálon. Ügyeljen arra, hogy a hibaüzenetben szerepeljen a korrelációs azonosító.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>A Azure App Service Profiler hibáinak megoldása

A Profiler megfelelő működéséhez:
* A Web App Service-csomagnak alapszintű vagy magasabb szintűnek kell lennie.
* A webalkalmazásnak Application Insights engedélyezve kell lennie.
* A webalkalmazásnak a következő alkalmazás-beállításokkal kell rendelkeznie:

    |Alkalmazásbeállítás    | Érték    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | a Application Insights-erőforrás Rendszerállapotkulcsot    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |


* A **ApplicationInsightsProfiler3** -webjobs futnia kell. A webjobs megkeresése:
   1. Nyissa meg a [kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. Az **eszközök** menüben válassza a **webjobs irányítópult** elemet.  
      Megnyílik a **webjobs** panel. 
   
      ![Képernyőfelvétel: a webjobs panel, amely a feladatok nevét, állapotát és utolsó futási idejét jeleníti meg.][profiler-webjob]   
   
   1. A webjobs részleteinek megtekintéséhez, beleértve a naplót, válassza a **ApplicationInsightsProfiler3** hivatkozást.  
     Megnyílik a **folyamatos Webjobs részletei** ablaktábla.

      ![Képernyőfelvétel: a folyamatos Webjobs részletek panelje.][profiler-webjob-log]

Ha a Profiler nem működik Önnek, letöltheti a naplót, és elküldheti a csapatnak segítségért serviceprofilerhelp@microsoft.com .

### <a name="check-the-diagnostic-services-site-extension-status-page"></a>A diagnosztikai szolgáltatások helyének bővítményének állapota lap
Ha a Profiler engedélyezte a portálon a [Application Insights ablaktáblán](profiler.md) , azt a diagnosztikai szolgáltatások helyének bővítménye engedélyezte.

> [!NOTE]
> Application Insights Profiler kód nélküli telepítése a .NET Core támogatási szabályzatot követi.
> További információ a támogatott futtatókörnyezetekről: [.net Core támogatási szabályzat](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

A bővítmény állapot lapját a következő URL-címen tekintheti meg: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> Az állapot lap hivatkozásának tartománya a felhőtől függően eltérő lesz.
Ez a tartomány a App Service kudu-felügyeleti helyével azonos lesz.

Ez az állapot lap a Profiler és a Snapshot Collector ügynökök telepítési állapotát jeleníti meg. Ha váratlan hiba történt, akkor megjelenik, és bemutatja, hogyan kell kijavítani.

A App Service kudu felügyeleti webhelyének beszerzéséhez használja az állapot lap alap URL-címét:
1. Nyissa meg a App Service alkalmazást a Azure Portalban.
2. Válassza a **speciális eszközök** lehetőséget, vagy keressen rá a **kudu**.
3. Válassza az **Indítás** lehetőséget.
4. Ha a kudu felügyeleti webhelyen van, az URL-címben **fűzze hozzá a következőt, `/DiagnosticServices` majd nyomja le az ENTER** billentyűt.
 Ez a következőképpen fog kinézni: `https://<kudu-url>/DiagnosticServices`

A következőhöz hasonló állapot lapot jelenít meg: ![ diagnosztikai szolgáltatások állapota lap](./media/diagnostic-services-site-extension/status-page.png)
    
### <a name="manual-installation"></a>Manuális telepítés

A Profiler konfigurálásakor a rendszer frissíti a webalkalmazás beállításait. Ha a környezete megköveteli, manuálisan is alkalmazhatja a frissítéseket. Lehetséges például, hogy az alkalmazás a PowerApps Web Apps-környezetében fut. Frissítések manuális alkalmazása:

1. A **webalkalmazás-vezérlő** ablaktáblán nyissa meg a **Beállítások** lapot.

1. A **.NET-keretrendszer verziójának** beállítása: **v 4.6**.

1. A **mindig** be értékre állítása **.**
1. Az Alkalmazásbeállítások létrehozása:

    |Alkalmazásbeállítás    | Érték    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | a Application Insights-erőforrás Rendszerállapotkulcsot    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |

### <a name="too-many-active-profiling-sessions"></a>Túl sok aktív profilkészítési munkamenet

A Profilert legfeljebb négy olyan Web Apps engedélyezheti, amelyek ugyanabban a szervizcsomagban futnak. Ha négynél több is van, akkor a Profiler a *Microsoft. ServiceProfiler. kivétellistájára. TooManyETWSessionException*. A megoldáshoz helyezzen át néhány webalkalmazást egy másik szolgáltatási csomagba.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Üzembe helyezési hiba: a könyvtár nem üres: \\ Kezdőlap \\ hely \\ wwwroot \\ App_Data \\ feladatok

Ha a webalkalmazást egy olyan Web Apps erőforrásra telepíti újra, amelyen a Profiler engedélyezve van, akkor a következő üzenet jelenhet meg:

*A könyvtár nem üres: \\ Kezdőlap \\ hely \\ wwwroot \\ App_Data \\ feladatok*

Ez a hiba akkor fordul elő, ha a web Deploy szkriptből vagy az Azure-folyamatokból futtatja. A megoldás a következő központi telepítési paramétereket adja hozzá a web Deploy feladathoz:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ezek a paraméterek törlik a Application Insights Profiler által használt mappát, és feloldják az újratelepítési folyamatot. Nincsenek hatással a jelenleg futó Profiler-példányra.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Hogyan határozza meg, hogy a Application Insights Profiler fut-e?

A Profiler folyamatos webjobs fut a webalkalmazásban. A webalkalmazás-erőforrást a [Azure Portalban](https://portal.azure.com)nyithatja meg. A **webjobs** ablaktáblán tekintse meg a **ApplicationInsightsProfiler** állapotát. Ha nem fut, nyissa meg a **naplókat** , hogy további információkat kapjon.

## <a name="troubleshoot-vms-and-cloud-services"></a>Virtuális gépek és Cloud Services hibáinak megoldása

>**A Profilerben szereplő hiba, amely a Cloud Services WAD-ban található.** A Cloud Services WAD (1.12.2.0) legújabb verziója az App ininsights SDK legújabb verzióival működik. A Cloud Service-gazdagépek automatikusan frissítik a WAD-t, de nem azonnal. A frissítés kényszerítéséhez újra üzembe helyezheti a szolgáltatást, vagy újraindíthatja a csomópontot.

A következő lépések végrehajtásával ellenőrizheti, hogy a Profiler megfelelően van-e konfigurálva Azure Diagnostics. 
1. Ellenőrizze, hogy a telepített Azure Diagnostics-konfiguráció tartalma várható-e. 

1. Másodszor, győződjön meg arról, hogy Azure Diagnostics átadja a megfelelő Rendszerállapotkulcsot a Profiler parancssorban. 

1. Harmadszor, a Profiler naplófájljában ellenőrizze, hogy a Profiler futott-e, de hibát adott vissza. 

A Azure Diagnostics konfigurálásához használt beállítások ellenőrzését:

1. Jelentkezzen be a virtuális gépre (VM), majd nyissa meg a naplófájlt ezen a helyen. Előfordulhat, hogy a beépülő modul verziója újabb a gépen.
    
    Virtuális gépek esetén:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Cloud Services esetén:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. A fájlban megkeresheti a **WadCfg** karakterláncot, hogy megkeresse a virtuális gép számára átadott beállításokat a Azure Diagnostics konfigurálásához. Megtekintheti, hogy helyes-e a Profiler-fogadó által használt Rendszerállapotkulcsot.

1. A Profiler elindításához használt parancssorban keresse meg a következőt:. A Profiler elindításához használt argumentumok a következő fájlban találhatók. (A meghajtó lehet c: vagy d:, és a könyvtár rejtett lehet.)

    Virtuális gépek esetén:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    Cloud Services esetén:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Ellenőrizze, hogy helyes-e a Rendszerállapotkulcsot a Profiler parancssorban. 

1. A fájl előzőconfig.jstalálható elérési út használatával keresse *meg* a Profiler-naplófájlt ( **BootstrapN. log**). Megjeleníti a Profiler által használt beállításokat jelző hibakeresési információkat. Megjeleníti a Profiler állapot-és hibaüzeneteit is.  

    Virtuális gépek esetén a fájl itt található:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Cloud Services esetén:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Ha a Profiler fut, miközben az alkalmazás fogadja a kérelmeket, a következő üzenet jelenik meg: a *rendszerállapotkulcsot által észlelt tevékenység*. 

    A nyomkövetés feltöltésekor a következő üzenet jelenik meg: a *nyomkövetés feltöltésének megkezdése*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Hálózati proxy-vagy tűzfalszabályok szerkesztése

Ha az alkalmazás proxyn vagy tűzfalon keresztül csatlakozik az internethez, előfordulhat, hogy frissítenie kell a szabályokat a Profiler szolgáltatással való kommunikációhoz.

Az Application Insights Profiler által használt IP-címek szerepelnek a Azure Monitor szolgáltatás címkéjén. További információ: szolgáltatás- [címkék dokumentációja](../../virtual-network/service-tags-overview.md).


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png