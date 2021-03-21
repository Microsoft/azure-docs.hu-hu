---
title: Azure Cloud Services-Application Insights | Microsoft Docs
description: Webes és feldolgozói szerepkörök hatékony figyelése az Application Insightsszal
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 09/05/2018
ms.openlocfilehash: 264d6d4b0b397a29b5dc1db4bb299297c1e30584
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419235"
---
# <a name="application-insights-for-azure-cloud-services"></a>Azure Cloud Services-Application Insights
A [Application Insights][start] képes figyelni az [Azure Cloud Service-alkalmazásokat](https://azure.microsoft.com/services/cloud-services/) a rendelkezésre állásra, a teljesítményre, a hibákra és a használatra, ha Application Insights SDK-ból származó adatokat egyesít a cloud servicesből származó [Azure Diagnostics](../agents/diagnostics-extension-overview.md) adatokkal. A széles körben elérhető módon működő alkalmazások teljesítményével és hatékonyságával kapcsolatos visszajelzések birtokában tájékozott döntéseket hozhat a fejlesztés irányát illetően az egyes fejlesztési fázisokban.

![Áttekintő irányítópult](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Előfeltételek
A Kezdés előtt a következőkre lesz szüksége:

* Egy [Azure](https://azure.com) -előfizetés. Jelentkezzen be a Microsoft-fiók Windows, Xbox Live vagy más Microsoft Cloud Services szolgáltatással. 
* Microsoft Azure eszközök 2,9 vagy újabb.
* Fejlesztői elemzési eszközök 7,10 vagy újabb verzió.

## <a name="get-started-quickly"></a>Gyors kezdés
A felhőszolgáltatás Application Insightsszal való figyelésének leggyorsabb és legegyszerűbb módja, ha kiválasztja ezt a lehetőséget, amikor közzéteszi a szolgáltatást az Azure-ban.

![Példa diagnosztikai beállítások lapra](./media/cloudservices/azure-cloud-application-insights.png)

Ez a beállítás lehetővé teszi az alkalmazás futását, és megadja az összes olyan telemetria, amely a kérések, kivételek és függőségek figyeléséhez szükséges a webes szerepkörben. Emellett a feldolgozói szerepkörökből származó teljesítményszámlálókat is figyeli. Az alkalmazás által létrehozott diagnosztikai nyomkövetéseket is elküldjük Application Insights.

Ha erre a lehetőségre van szüksége, készen áll. 

A következő lépések az [alkalmazás mérőszámait tekintik át](../essentials/metrics-charts.md), és az [adatokat az elemzéssel kérdezik](../logs/log-query-overview.md)le. 

Ha figyelni szeretné a teljesítményt a böngészőben, érdemes lehet a [rendelkezésre állási teszteket](./monitor-web-app-availability.md) is beállítania, és [kódokat hozzáadni a weblapokhoz](./javascript.md).

A következő fejezetek a következő további lehetőségeket tárgyalják:

* Különböző összetevőktől származó adatok elküldése, valamint konfigurációk kiépítése külön erőforrásokhoz.
* Egyéni telemetriát adhat hozzá az alkalmazásból.

## <a name="sample-app-instrumented-with-application-insights"></a>Példa Application Insights
Ebben a [példában](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)a Application Insights egy felhőalapú szolgáltatáshoz kerül, amely az Azure-ban üzemeltetett két feldolgozói szerepkörrel rendelkezik. 

A következő szakaszban megtudhatja, hogyan igazíthatja saját Cloud Service-projektjét ugyanúgy.

## <a name="plan-resources-and-resource-groups"></a>Erőforrások és erőforráscsoportok tervezése
Az alkalmazás telemetria tárolása, elemzése és megjelenítése Application Insights típusú Azure-erőforrásban történik. 

Mindegyik erőforrás egy erőforráscsoportba tartozik. Az erőforráscsoportok a költségek kezelésére, a csoporttagok hozzáférésének biztosítására, valamint a frissítések egyetlen koordinált tranzakcióban történő központi telepítésére szolgálnak. [Írhat például egy parancsfájlt](../../azure-resource-manager/templates/deploy-powershell.md) egy Azure Cloud Service üzembe helyezéséhez és annak Application Insights figyelési erőforrásaihoz egy művelettel.

### <a name="resources-for-components"></a>Az összetevők erőforrásai

Azt javasoljuk, hogy [vegyen fel egy dimenzió tulajdonságot minden olyan telemetria-elemhez](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) , amely azonosítja a forrás szerepkörét. Ebben a megközelítésben a mérőszám-diagramok, például a kivételek, általában a különböző szerepkörökből származó számlálások összesítését mutatják, de a diagramot szükség szerint a szerepkör-azonosító alapján is szegmentálhatja. A kereséseket ugyanezen dimenzió alapján is szűrheti. Ez a alternatíva megkönnyíti az összes adat megtekintését, de a szerepkörök közötti zavart is okozhat.

A böngészőtelemetria általában ugyanabban az erőforrásban jelenik meg, mint a kiszolgálóoldali webes szerepköre.

Helyezze el a Application Insights erőforrásokat egy erőforráscsoport különböző összetevőihez. Ezzel a módszerrel egyszerűen kezelheti őket. 

### <a name="separate-development-test-and-production"></a>A fejlesztési, tesztelési és éles környezetek elkülönítése
Ha már egy következő funkcióhoz fejleszt egyéni eseményeket, miközben a korábbi verzió még éles üzemben működik, érdemes lehet a fejlesztési telemetriát egy külön Application Insights-erőforrásba küldeni. Ellenkező esetben nehéz lehet megkeresni a teszt telemetria az élő helyről érkező összes forgalom között.

Ezen helyzet elkerülése érdekében hozzon létre külön erőforrásokat az egyes Build-konfigurációkhoz, illetve a "Stamp" (fejlesztési, tesztelési, éles stb.) rendszerhez. Helyezze az egyes buildkonfigurációk erőforrásait külön erőforráscsoportokba. 

A telemetria a megfelelő erőforrásokhoz való elküldéséhez beállíthatja a Application Insights SDK-t úgy, hogy az a létrehozási konfigurációtól függően egy másik kialakítási kulcsot is felvesz. 

Megtudhatja, hogyan lehet [dinamikusan beállítani a kialakítási kulcsot](./separate-resources.md#dynamic-ikey) különböző fázisokban. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Application Insights-erőforrás létrehozása mindegyik szerepkörhöz

Ha úgy döntött, hogy külön erőforrást hoz létre minden egyes szerepkörhöz, és esetleg egy külön készletet az egyes Build-konfigurációkhoz, akkor a legegyszerűbb, ha mindegyiket a Application Insights portálon hozza létre. Ha sok erőforrást hoz létre, [automatizálhatja a folyamatot](./powershell.md).

1. A [Azure Portal][portal]válassza az **új**  >  **fejlesztői szolgáltatások**  >  **Application Insights** lehetőséget.  

    ![Application Insights ablaktábla](./media/cloudservices/01-new.png)

1. Az **alkalmazás típusa** legördülő listában válassza a **ASP.net webalkalmazás** lehetőséget.

Minden erőforrást egy rendszerállapot-kulcs azonosít. Később szükség lehet erre a kulcsra, ha manuálisan szeretné konfigurálni vagy ellenőrizni az SDK konfigurációját.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Azure Diagnostics beállítása az egyes szerepkörökhöz
Ezzel a beállítással figyelheti az alkalmazást az Application Insightsszal. A webes szerepkörök esetében ez a beállítás a teljesítmény figyelését, a riasztásokat, a diagnosztika és a használat elemzését teszi lehetővé. Más szerepkörök esetében olyan Azure Diagnostics kereshet és figyel, mint például az újraindítás, a teljesítményszámlálók és a System. Diagnostics. Trace hívása. 

1. A Visual Studio megoldáskezelő a **\<YourCloudService>**  >  **szerepkörök** területen nyissa meg az egyes szerepkörök tulajdonságait.

1. A **konfiguráció** területen jelölje be a **diagnosztikai adatküldés Application Insightsba** jelölőnégyzetet, majd válassza ki a korábban létrehozott Application Insights-erőforrást.

Ha úgy döntött, hogy külön Application Insights-erőforrást használ mindegyik buildkonfigurációhoz, előbb válassza ki a konfigurációt.

![Az Application Insights konfigurálása](./media/cloudservices/configure-azure-diagnostics.png)

Ennek hatására be kell szúrni a Application Insights rendszerállapot-kulcsokat a ServiceConfiguration nevű fájlba *. \* cscfg*. Itt látható a [mintakód](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Ha szeretné, hogy a Application Insights eljuttatott diagnosztikai információk szintje eltérő legyen, ezt a [ *. cscfg* fájlok közvetlen szerkesztésével](../agents/diagnostics-extension-to-application-insights.md)teheti meg.

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Az SDK telepítése az egyes projektekben
Ezzel a beállítással egyéni üzleti telemetria adhat hozzá bármely szerepkörhöz. A beállítás részletesebb elemzést nyújt az alkalmazás használatáról és végrehajtásáról.

A Visual Studióban konfigurálja külön az Application Insights SDK-t az egyes felhőalkalmazás-projektekhez.

1. A **webes szerepkörök** konfigurálásához kattintson a jobb gombbal a projektre, majd válassza a **Application Insights konfigurálása** vagy **> Application Insights telemetria hozzáadása** lehetőséget.

1. **Feldolgozói szerepkörök** konfigurálása: 

    a. Kattintson a jobb gombbal a projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.

    b. Adja hozzá az [Application Insights a Windows-kiszolgálókon](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) modult.

1. Az SDK konfigurálása az Application Insights erőforrásba való adatküldéshez:

    a. A megfelelő indítási függvényben állítsa be a kialakítási kulcsot a *. cscfg* fájl konfigurációs beállításától:
 
    ```csharp
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
        var telemetryClient = new TelemetryClient(configuration);
    ```
   
    b. Ismételje meg a "Step a" műveletet az alkalmazás egyes szerepköreinél. Lásd az alábbi példákat:
   
    * [Webes szerepkör](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Feldolgozói szerepkör](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Weblapok esetén](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Állítsa be, hogy a rendszer mindig a kimeneti könyvtárba másolja a *ApplicationInsights.config* fájlt.

   A *. config* fájlban lévő üzenet arra kéri, hogy ott helyezze el a kialakítási kulcsot. A Cloud apps esetében azonban jobb, ha a *. cscfg* fájlból állítja be. Ez a megközelítés biztosítja, hogy a szerepkör helyesen legyen azonosítva a portálon.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Állapotmonitor beállítása teljes SQL-lekérdezések gyűjtésére (opcionális)

Erre a lépésre csak akkor van szükség, ha a .NET-keretrendszerben teljes SQL-lekérdezéseket szeretne rögzíteni. 

1. A `\*.csdef` fájlban az [indítási feladat](../../cloud-services/cloud-services-startup-tasks.md) hozzáadása a következőhöz hasonló szerepkörökhöz: 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. Töltse le [InstallAgent.bat](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) és [InstallAgent.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1), majd helyezze azokat az `AppInsightsAgent` összes szerepkör-projekt mappájába. Ügyeljen rá, hogy a Visual Studio-fájl tulajdonságain keresztül másolja őket a kimeneti könyvtárba, vagy hozzon létre parancsfájlokat.

3. A feldolgozói szerepkörökben adja hozzá a környezeti változókat: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Az alkalmazás futtatása és közzététele

1. Futtassa az alkalmazást, és jelentkezzen be az Azure-ba. 

1. Nyissa meg a létrehozott Application Insights erőforrásokat.

   Az egyes adatpontok a [keresésben][diagnostic]jelennek meg, és az összesített adatok megjelennek a [metrika Explorerben](../essentials/metrics-charts.md).

1. Vegyen fel további telemetria (lásd a következő részeket), majd tegye közzé az alkalmazást az élő diagnosztika és a használati visszajelzések beszerzéséhez. 

Ha nincs ilyen érték, tegye a következőket:

1. Az egyes események megtekintéséhez nyissa meg a [Keresés][diagnostic] csempét.
1. Az alkalmazásban nyissa meg a különböző lapokat, hogy egy bizonyos telemetria hozzon létre.
1. Várjon néhány másodpercet, majd kattintson a **frissítés** gombra.  

További információk: [Hibaelhárítás][qna].

## <a name="view-azure-diagnostics-events"></a>Azure Diagnostics események megtekintése
A [Azure Diagnostics](../agents/diagnostics-extension-overview.md) információk a Application Insights a következő helyszíneken találhatók:

* A teljesítményszámlálók egyéni mérőszámokként jelennek meg. 
* A Windows eseménynaplók nyomkövetésekként és egyéni eseményekként jelennek meg.
* Az alkalmazásnaplók, ETW-naplók és egyéb diagnosztikai infrastruktúra-naplók nyomkövetésként jelennek meg.

A teljesítményszámlálók és az események számának megtekintéséhez nyissa meg [Metrikaböngésző](../essentials/metrics-charts.md) és adja hozzá a következő diagramot:

![Azure Diagnosticsi az adatgyűjtést](./media/cloudservices/23-wad.png)

A Azure Diagnostics által eljuttatott nyomkövetési naplók közötti kereséshez használjon [keresési](./diagnostic-search.md) vagy [elemzési lekérdezést](../logs/log-analytics-tutorial.md). Tegyük fel például, hogy van egy kezeletlen kivétel, amely egy szerepkör összeomlását és újrahasznosítását okozta. Ezek az információk a Windows eseménynaplójában, az Alkalmazás csatornában jelennek meg. A keresés használatával megtekintheti a Windows Eseménynapló hibáját, és lekérheti a kivétel teljes verem-nyomkövetését. Ennek segítségével megtalálhatja a probléma alapvető okát.

![Azure Diagnostics keresés](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>További telemetria
A következő részekben megismerheti, hogyan szerezhet be további telemetria az alkalmazás különböző szempontjaiból.

## <a name="track-requests-from-worker-roles"></a>Feldolgozói szerepkörök kéréseinek nyomon követése
A webes szerepkörökben a kérések modulja automatikusan gyűjti a HTTP-kérésekkel kapcsolatos adatokat. Az alapértelmezett gyűjtési viselkedés felülbírálásával kapcsolatos példákért tekintse meg a [minta MVCWebRole](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

A feldolgozói szerepkörökhöz intézett hívások teljesítményének rögzítése a HTTP-kérésekkel megegyező módon történő nyomkövetéssel hajtható végre. Az Application Insightsban a Kérés telemetriatípus a megnevezett kiszolgálóoldali műveletek egységeit méri, amelyeknek mérhető az idejük, és külön-külön sikerülhetnek vagy meghiúsulhatnak. Habár az SDK automatikusan rögzíti a HTTP-kéréseket, beillesztheti a saját kódját, hogy nyomon követhesse a feldolgozói szerepkörökre irányuló kéréseket.

Tekintse meg a kérelmeket a következő két minta feldolgozói szerepkörrel: 
* [WorkerRoleA](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Kivételek
A különböző webalkalmazások típusaitól származó nem kezelt kivételek gyűjtésével kapcsolatos további információkért lásd: [a kivételek figyelése Application Insightsban](./asp-net-exceptions.md).

A minta webes szerepkör MVC5 és Web API 2 vezérlőkkel rendelkezik. A két vezérlőtől származó nem kezelt kivételeket a rendszer a következő kezelőkkel rögzíti:

* [AiHandleErrorAttribute](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) az MVC5-vezérlőkhöz [, ahogy az ebben a példában is látható](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) beállítása a webes API 2-vezérlőkhöz az [ebben a példában látható módon](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

A feldolgozói szerepkörök esetében kétféleképpen követheti a kivételeket:

* Használja a TrackException (pl.).
* Ha hozzáadta a Application Insights Trace Listener NuGet-csomagot, a System. Diagnostics. Trace használatával naplózhatja a kivételeket, [ahogy az ebben a példában is látható](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Teljesítményszámlálók
A rendszer alapértelmezés szerint az alábbi számlálókat gyűjti:

* \Process(?? APP_WIN32_PROC??) \% Processzoridő
* \Memory\Available Bytes
* \.NET CLR-kivételek (??APP_CLR_PROC??)\# az összes kivétel közül másodpercenként
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Webes szerepkörök esetében a rendszer az alábbi számlálókat is gyűjti:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

További egyéni vagy más Windows-teljesítményszámlálókat is megadhat a *ApplicationInsights.config* szerkesztésével [, ahogy az ebben a példában is látható](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Teljesítményszámlálók](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Feldolgozói szerepkörök korrelált telemetria
A hatékony diagnosztikai élmény érdekében megtekintheti, hogy mi vezetett egy sikertelen vagy nagy késésű kéréshez. A webes szerepkörök esetében az SDK automatikusan beállítja a kapcsolódó telemetria közötti korrelációt. 

Ha ezt a nézetet szeretné elérni a feldolgozói szerepkörökhöz, használhat egy egyéni telemetria-inicializálást az összes telemetria közös Operation.Id-környezeti attribútumának beállításához. Ezzel egy pillantással megtekintheti, hogy a késés vagy a hiba problémáját egy függőség vagy a kód okozta-e. 

Ezt a következőképpen teheti meg:

* Állítsa be a correlationId egy CallContext [, az ebben a példában látható módon](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). Ebben az esetben a kérelem AZONOSÍTÓját használjuk correlationId.
* Vegyen fel egy egyéni TelemetryInitializer-megvalósítást, hogy a Operation.Id a korábban beállított correlationId állítsa be. Példaként tekintse meg a következőt: [ItemCorrelationTelemetryInitializer](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Adja hozzá az egyéni telemetriainicializálót. Ezt megteheti a *ApplicationInsights.config* fájlban vagy kódban [, ahogy az ebben a példában is látható](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Ügyfél-telemetria
A böngészőalapú telemetria beszerzéséhez, például a lapok megtekintésének száma, az oldal betöltési ideje vagy a parancsfájlok kivételei, valamint az egyéni telemetria írása az oldal parancsfájljaiba: [a JavaScript SDK hozzáadása a weblapokhoz][client].

## <a name="availability-tests"></a>Rendelkezésre állási tesztek
Győződjön meg arról, hogy az alkalmazás élőben és rugalmasan működik, és [Beállítja a webes teszteket][availability].

## <a name="display-everything-together"></a>Az összes elem együttes megjelenítése
A rendszer átfogó képére a kulcsfontosságú figyelési diagramok is megjeleníthetők egyetlen [irányítópulton](./overview-dashboard.md). Például hozzáadhatja az egyes szerepkörök kérés- és hibaszámait. 

Ha a rendszer más Azure-szolgáltatásokat (például Stream Analytics) használ, vegye fel a figyelési diagramokat is. 

Ha rendelkezik ügyfél-mobilalkalmazással, használja az [App Centert](../app/mobile-center-quickstart.md). [Analytics](../logs/log-query-overview.md)-lekérdezések létrehozásával megjelenítheti az események számát, és rögzítheti őket az irányítópulton.

## <a name="example"></a>Példa
[Ez a példa](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) egy olyan szolgáltatást figyel, amely egy webes és két feldolgozói szerepkörrel rendelkezik.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Kivétel "a metódus nem található" az Azure Cloud Servicesben való futtatáskor
A .NET 4.6-os verziójára készítette el az alkalmazást? A .NET 4,6 nem támogatott automatikusan az Azure Cloud Services szerepköreiben. Az alkalmazás futtatása előtt [telepítse a .net 4,6-es verzióját az egyes szerepkörökre](../../cloud-services/cloud-services-dotnet-install-dotnet.md) .

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Következő lépések
* [Azure Diagnostics-diagnosztikák Application Insightsba való küldésének konfigurálása](../agents/diagnostics-extension-to-application-insights.md)
* [Application Insights erőforrások automatikus létrehozása](./powershell.md)
* [Azure Diagnostics automatizálása](./powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ./api-custom-events-metrics.md
[availability]: ./monitor-web-app-availability.md
[azure]: ./app-insights-overview.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[netlogs]: ./asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md