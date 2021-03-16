---
title: Adathiány hibaelhárítása – Application Insights .NET-hez
description: Nem látja az Azure Application Insightsban tárolt adatmegjelenítést? Próbálja ki itt.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/21/2020
ms.openlocfilehash: 40fbe4d08676d7cc56478d3740424fccaa7addc0
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562195"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>A .NET/.NET Core rendszerhez nem szükséges adatApplication Insightsek hibaelhárítása

## <a name="some-of-my-telemetry-is-missing"></a>Néhány telemetria hiányzik
*A Application Insightsban csak az alkalmazás által generált események töredékét látom.*

* Ha folyamatosan ugyanazt a frakciót látja, valószínűleg alkalmazkodó [mintavételezés](./sampling.md)miatt. Ennek megerősítéséhez nyissa meg a keresést (az Áttekintés panelen), és tekintse meg a kérelem vagy más esemény egy példányát. A tulajdonságok szakasz alján kattintson a "..." elemre. a tulajdonságok teljes részletességének beolvasása. Ha a kérések száma > 1, akkor a mintavételezés művelet folyamatban van.
* Ellenkező esetben előfordulhat, hogy a díjszabási csomagra vonatkozó [adatforgalmi korlátot](./pricing.md#limits-summary) futtat. Ezeket a korlátokat percenként alkalmazza a rendszer.

*Véletlenszerűen tapasztalok adatvesztést.*

* Ellenőrizze, hogy tapasztal-e adatvesztést a [telemetria Channel](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost) szolgáltatásban

* A telemetria Channel [GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) -tárházban előforduló ismert problémák keresése

*Adatvesztést tapasztalok a konzol alkalmazásban vagy a webalkalmazásban, ha az alkalmazás hamarosan leáll.*

* Az SDK-csatorna tárolja a telemetria a pufferben, és azokat kötegekben küldi el. Ha az alkalmazás leáll, előfordulhat, hogy explicit módon meg kell hívnia a [Flush ()](api-custom-events-metrics.md#flushing-data)-t. A viselkedés az `Flush()` aktuálisan használt [csatornától](telemetry-channels.md#built-in-telemetry-channels) függ.

## <a name="no-data-from-my-server"></a>Nincsenek adatok a saját kiszolgálóról
*Telepítettem az alkalmazást a webkiszolgálón, és most nem látok semmilyen telemetria. A fejlesztői gépen rendben működött.*

* Valószínűleg tűzfallal kapcsolatos probléma. [Adja meg az adatküldés Application Insights a tűzfal kivételeit](./ip-addresses.md).
* Előfordulhat, hogy az IIS-kiszolgáló néhány előfeltételt tartalmaz: a .NET-bővíthetőség 4,5 és a ASP.NET 4,5.

*[Telepítettem Állapotmonitor](./monitor-performance-live-website-now.md) a webkiszolgálón a meglévő alkalmazások figyeléséhez. Semmilyen eredményt nem látok.*

* Lásd: [hibaelhárítás Állapotmonitor](./monitor-performance-live-website-now.md#troubleshoot).

> [!IMPORTANT]
> Az új Azure-régiókban a rendszerállapot-kulcsok helyett a kapcsolatok sztringjét **kell** használnia. A [kapcsolódási karakterlánc](./sdk-connection-string.md?tabs=net) azonosítja azt az erőforrást, amelyhez hozzá szeretné rendelni a telemetria-adatait. Azt is lehetővé teszi, hogy módosítsa az erőforrás által a telemetria célként használt végpontokat. A kapcsolódási karakterláncot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához vagy egy környezeti változóhoz.


## <a name="filenotfoundexception-could-not-load-file-or-assembly-microsoftaspnet-telemetrycorrelation"></a>FileNotFoundException: nem tölthető be a (z) "Microsoft. AspNet TelemetryCorrelation" fájl vagy szerelvény.

További információ erről a hibáról: [GitHub-probléma 1610] ( https://github.com/microsoft/ApplicationInsights-dotnet/issues/1610) .

Ha a (2,4)-nál régebbi SDK-ból frissít, meg kell győződnie arról, hogy a következő módosításokat alkalmazza a `web.config` és `ApplicationInsights.config` :

1. Egy helyett két http-modul. A-ben `web.config` két http-modulnak kell lennie. A sorrend fontos bizonyos helyzetekben:

    ``` xml
    <system.webServer>
      <modules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="integratedMode,managedHandler" />
          <add name="ApplicationInsightsHttpModule" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
      </modules>
    </system.webServer>
    ```

2. A (z) `ApplicationInsights.config` mellett a `RequestTrackingTelemetryModule` következő telemetria-modult kell használnia:

    ``` xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web"/>
    </TelemetryModules>
    ```

***A frissítés nem megfelelő lehet, ha váratlan kivételeket vagy telemetria nem gyűjt.***


## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Nincs "Application Insights hozzáadása" lehetőség a Visual Studióban
*Ha a jobb gombbal egy meglévő projektre kattintok Megoldáskezelő, nem látok semmilyen Application Insights lehetőséget.*

* Az eszközök nem támogatják az összes típusú .NET-projektet. A web-és WCF-projektek támogatottak. Más projekttípus, például asztali vagy szolgáltatási alkalmazások esetén [manuálisan is hozzáadhat egy Application INSIGHTS SDK-t a projekthez](./windows-desktop.md).
* Győződjön meg arról, hogy a [Visual Studio 2013 Update 3 vagy újabb verzió](/visualstudio/releasenotes/vs2013-update3-rtm-vs)van. A fejlesztői elemzési eszközökkel előre telepítve van, amelyek biztosítják a Application Insights SDK-t.
* Válassza az **eszközök**, **bővítmények és frissítések** lehetőséget, és győződjön meg arról, hogy a **fejlesztői elemzési eszközök** telepítve és engedélyezve vannak. Ha igen, kattintson a **frissítések** lehetőségre, és ellenőrizze, hogy van-e elérhető frissítés.
* Nyissa meg az új projekt párbeszédpanelt, és válassza a ASP.NET webalkalmazás lehetőséget. Ha megjelenik a Application Insights lehetőség, akkor a rendszer telepíti az eszközöket. Ha nem, próbálja meg eltávolítani, majd telepítse újra a fejlesztői elemzési eszközöket.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Nem sikerült hozzáadni a Application Insights
*Amikor megpróbálok Application Insights hozzáadni egy meglévő projekthez, hibaüzenet jelenik meg.*

Valószínű okok:

* Nem sikerült kommunikálni az Application Insights-portálon; vagy
* Probléma merült fel az Azure-fiókkal kapcsolatban;
* Csak [olvasási jogosultsággal rendelkezik ahhoz az előfizetéshez vagy csoporthoz, amelyhez az új erőforrást próbálta létrehozni](./resources-roles-access-control.md).

Javítsa ki

* Győződjön meg arról, hogy a megfelelő Azure-fiókhoz megadott bejelentkezési hitelesítő adatokat adott meg.
* Győződjön meg arról, hogy a böngészőben van hozzáférése a [Azure Portalhoz](https://portal.azure.com). Nyissa meg a beállításokat, és ellenőrizze, hogy van-e korlátozás.
* [Application Insights hozzáadása a meglévő projekthez](./asp-net.md): a Megoldáskezelőban kattintson a jobb gombbal a projektre, és válassza a "Hozzáadás Application Insights" lehetőséget.

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Hibaüzenet jelenik meg: "a rendszerállapot-kulcs nem lehet üres"
Úgy tűnik, hiba történt a Application Insights telepítése közben, vagy talán egy naplózási adaptert.

Megoldáskezelő kattintson a jobb gombbal a projektre, és válassza a **Application Insights > a Application Insights konfigurálása** lehetőséget. Megjelenik egy párbeszédpanel, amely meghívja Önt, hogy bejelentkezzen az Azure-ba, vagy hozzon létre egy Application Insights-erőforrást, vagy használja újra a meglévőt.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a> "Hiányzó NuGet-csomag (ok)" a saját Build-kiszolgálón
*Minden rendben van, ha hibakeresést végezek a fejlesztői gépen, de NuGet hibaüzenetet kapok a Build-kiszolgálón.*

Tekintse meg a [NuGet-csomag visszaállítása](https://docs.nuget.org/Consume/Package-Restore) és az [automatikus csomag visszaállítása](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)című témakört.

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Hiányzó menüparancs a Application Insights megnyitásához a Visual studióból
*Ha a jobb gombbal a projekt Megoldáskezelőra kattintok, nem látok Application Insights parancsokat, vagy nem látok nyitott Application Insights parancsot.*

Valószínű okok:

* Ha a Application Insights erőforrást manuálisan hozta létre, vagy ha a projekt olyan típusú, amelyet a Application Insights eszközök nem támogatnak.
* A fejlesztői elemzési eszközök le vannak tiltva a Visual Studióban.
* A Visual Studio régebbi, mint a 2013-es frissítés.

Javítsa ki

* Győződjön meg arról, hogy a Visual Studio verziója 2013 3. vagy újabb verziójú.
* Válassza az **eszközök**, **bővítmények és frissítések** lehetőséget, és győződjön meg arról, hogy a **fejlesztői elemzési eszközök** telepítve és engedélyezve vannak. Ha igen, kattintson a **frissítések** lehetőségre, és ellenőrizze, hogy van-e elérhető frissítés.
* Kattintson a jobb gombbal a projektre Megoldáskezelő. Ha a parancsot **Application Insights > konfigurálja Application Insights**, akkor a projektnek a Application Insights szolgáltatásban lévő erőforráshoz való összekapcsolásához használja.

Ellenkező esetben a projekt típusát nem támogatja közvetlenül a fejlesztői elemzési eszközök. A telemetria megtekintéséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com), válassza ki a Application Insights a bal oldali navigációs sávon, és válassza ki az alkalmazást.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Hozzáférés megtagadva" a Visual Studióban Application Insights megnyitásakor
*A "nyílt Application Insights" menüparancs a Azure Portalra mutat, de "hozzáférés megtagadva" hibaüzenet jelenik meg.*

Az alapértelmezett böngészőben utoljára használt Microsoft-bejelentkezés nem fér hozzá [az alkalmazáshoz Application Insights hozzáadásakor létrehozott erőforráshoz](./asp-net.md). Két valószínű oka van:

* Egynél több Microsoft-fiók van – talán egy munkahelyi és egy személyes Microsoft-fiók? Az alapértelmezett böngészőben utoljára használt bejelentkezés egy másik fiókhoz tartozik, mint az, amely hozzáféréssel rendelkezik a [projekthez Application Insights hozzáadásához](./asp-net.md).
  * Javítás: a böngészőablak jobb felső sarkában kattintson a nevére, és jelentkezzen ki. Ezután jelentkezzen be a hozzáféréssel rendelkező fiókkal. Ezután a bal oldali navigációs sávon kattintson a Application Insights elemre, és válassza ki az alkalmazást.
* Valaki másnak adta hozzá Application Insights a projekthez, és elfelejtette, hogy hozzáférést biztosítson [ahhoz az erőforráscsoporthoz](./resources-roles-access-control.md) , amelyben létrehozták.
  * Javítás: ha szervezeti fiókot használ, akkor hozzáadhatja Önt a csapathoz; vagy egyéni hozzáférést biztosíthat az erőforráscsoporthoz.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Az eszköz nem található" Application Insights megnyitása a Visual studióból
*A "nyitott Application Insights" menüparancs a Azure Portalra mutat, de "az eszköz nem található" hibaüzenet jelenik meg.*

Valószínű okok:

* Az alkalmazás Application Insights erőforrása törölve lett; vagy
* A rendszerállapot-kulcsot a program közvetlenül a projektfájl frissítése nélkül állította be vagy módosította ApplicationInsights.config.

ApplicationInsights.config a kialakítási kulcs a telemetria elküldésekor. A Project fájl egyik sora határozza meg, hogy melyik erőforrást kell megnyitni, amikor a parancsot a Visual Studióban használja.

Javítsa ki

* A Megoldáskezelő kattintson a jobb gombbal a projektre, és válassza a Application Insights lehetőséget, majd konfigurálja a Application Insights. A párbeszédpanelen dönthet úgy, hogy telemetria küld egy meglévő erőforrásnak, vagy újat hoz létre. Vagy
* Nyissa meg közvetlenül az erőforrást. Jelentkezzen be [a Azure Portalba](https://portal.azure.com), kattintson a bal oldali navigációs sávon a Application Insights elemre, majd válassza ki az alkalmazást.

## <a name="where-do-i-find-my-telemetry"></a>Hol találom meg a telemetria?
*Bejelentkezett a [Microsoft Azure Portalba](https://portal.azure.com), és megtekintem az Azure Home irányítópultot. Hol találom a Application Insightsi adataim?*

* A bal oldali navigációs sávon kattintson a Application Insights, majd az alkalmazás neve elemre. Ha nem rendelkezik projekttel, a [webes projektben Application Insights kell hozzáadnia vagy konfigurálnia](./asp-net.md).  
  Itt láthat néhány összefoglaló diagramot. Ide kattintva további részleteket tekinthet meg.
* A Visual Studióban az alkalmazás hibakeresése közben kattintson a Application Insights gombra.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a> Nincsenek kiszolgálói adatértékek (vagy nincsenek az összes adathalmaz)
*Futtattam az alkalmazást, majd megnyitottam a Application Insights szolgáltatást a Microsoft Azureban, de az összes diagramon a "Ismerje meg, hogyan kell gyűjteni..." vagy nincs konfigurálva.* Vagy *csak az oldal nézet és a felhasználói adatértékek, de nem szolgálnak.*

* Futtassa az alkalmazást hibakeresési módban a Visual Studióban (F5). Használja az alkalmazást úgy, hogy létrehozzon néhány telemetria. Ellenőrizze, hogy láthatók-e a Visual Studio kimeneti ablakában naplózott események.  
  ![Képernyőkép, amely az alkalmazás hibakeresési módban történő futtatását mutatja be a Visual Studióban.](./media/asp-net-troubleshoot-no-data/output-window.png)
* A Application Insights portálon nyissa meg a [diagnosztikai keresést](./diagnostic-search.md). Az adatgyűjtés általában itt jelenik meg.
* Kattintson a frissítés gombra. A panel rendszeresen frissíti magát, de manuálisan is elvégezheti. A frissítési időköz hosszabb a nagyobb időtartományok esetében.
* Győződjön meg arról, hogy a rendszerállapot-kulcsok egyeznek. Az alkalmazás fő paneljén, a Application Insights-portálon, az **alapvető** erőforrások legördülő menüben tekintse meg a rendszerállapot- **kulcsot**. Ezután a Visual Studióban a projektben nyissa meg ApplicationInsights.config és keresse meg a t `<instrumentationkey>` . Győződjön meg arról, hogy a két kulcs egyenlő. Ha nem:  
  * A portálon kattintson a Application Insights elemre, és keresse meg az alkalmazás-erőforrást a megfelelő kulccsal; vagy
  * A Visual Studio Megoldáskezelő kattintson a jobb gombbal a projektre, és válassza a Application Insights, majd a Konfigurálás lehetőséget. Állítsa vissza az alkalmazást, hogy telemetria küldjön a megfelelő erőforrásnak.
  * Ha nem találja a megfelelő kulcsokat, ellenőrizze, hogy a Visual Studióban ugyanazokat a bejelentkezési hitelesítő adatokat használja-e, mint a portálon.
* A [Microsoft Azure Kezdőlap irányítópulton](https://portal.azure.com)tekintse meg a Service Health térképet. Ha vannak riasztási jelzések, várjon, amíg vissza nem tért az OK gombra, majd zárjunk be és nyissa meg újra a Application Insights alkalmazás paneljét.
* Tekintse meg [az állapot blogját](https://techcommunity.microsoft.com/t5/azure-monitor-status/bg-p/AzureMonitorStatusBlog)is.
* Írt olyan kódot a [KISZOLGÁLÓOLDALI SDK-](./api-custom-events-metrics.md) hoz, amely megváltoztathatja a kialakítási kulcsot a `TelemetryClient` példányokban vagy a-ben `TelemetryContext` ? Vagy olyan [szűrőt vagy mintavételezési konfigurációt](./api-filtering-sampling.md) írt, amely túl sok szűrést eredményezhet?
* Ha ApplicationInsights.config szerkesztett, gondosan ellenőrizze a [TelemetryInitializers és a TelemetryProcessors](./api-filtering-sampling.md)konfigurációját. Egy helytelenül elnevezett típus vagy paraméter hatására az SDK nem tud adatküldést küldeni.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Nincsenek adatok a lap nézeteiben, böngészőkben, használatban
*Meglátok adatokat a kiszolgáló válaszideje és a kiszolgálói kérelmek diagramjaiban, de a lap nem töltődik be, vagy a böngésző vagy a használati panel nem tartalmazza az adatokat.*

Az adatok a weblapok parancsfájljaiból származnak. 

* Ha egy meglévő webes projekthez Application Insights adott hozzá, [akkor kézzel kell felvennie a szkripteket](./javascript.md).
* Győződjön meg arról, hogy az Internet Explorer nem jeleníti meg a webhelyet kompatibilitási módban.
* A böngésző hibakeresési funkciója (F12 bizonyos böngészőknél, majd a hálózat kiválasztása) segítségével ellenőrizheti, hogy az adatküldés folyamatban van-e `dc.services.visualstudio.com` .

## <a name="no-dependency-or-exception-data"></a>Nincs függőségi vagy kivételi érték
Lásd: [függőségi telemetria](./asp-net-dependencies.md) és [kivételek telemetria](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Nincsenek teljesítményadatok
A Teljesítményadatok (CPU, IO-sebesség stb.) a [Java-webszolgáltatásokhoz](./java-collectd.md), a [Windows asztali alkalmazásokhoz](./windows-desktop.md), [az IIS-webalkalmazásokhoz és-szolgáltatásokhoz](./monitor-performance-live-website-now.md)érhetők el, ha telepíti az állapotfigyelő szolgáltatást és az [Azure Cloud Services](./app-insights-overview.md). a beállítások, kiszolgálók menüpontban találhatja meg.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Nem (kiszolgáló) adatai, mert közzétettem az alkalmazást a kiszolgálón
* Győződjön meg arról, hogy ténylegesen másolta az összes Microsoftot. ApplicationInsights a DLL-eket a kiszolgálóra, valamint a Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* Előfordulhat, hogy [meg kell nyitnia néhány TCP-portot](./ip-addresses.md)a tűzfalon.
* Ha proxyt kell használnia a vállalati hálózatról történő küldéshez, a [defaultProxy](/previous-versions/dotnet/netframework-1.1/aa903360(v=vs.71)) beállítása Web.config
* Windows Server 2008: Ellenőrizze, hogy telepítette-e a következő frissítéseket: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://web.archive.org/web/20150129090641/http://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Felhasználtam az adatmegjelenítést, de leállt
* Megtalálta az adatpontok havi kvótáját? A kereséshez nyissa meg a beállítások/kvóta és a díjszabást. Ha igen, lehetősége van a csomag frissítésére, vagy a további kapacitás megfizetésére. Tekintse meg a [díjszabási sémát](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Nem látom az összes várt adatértéket
Ha az alkalmazás sok adatmennyiséget küld, és a ASP.NET 2.0.0-beta3 vagy újabb verziójához készült Application Insights SDK-t használja, akkor az [adaptív mintavételi](./sampling.md) funkció működhet, és csak a telemetria egy adott százalékát küldheti el.

A szolgáltatás letiltható, de ez nem ajánlott. A mintavétel úgy lett kialakítva, hogy a kapcsolódó telemetria megfelelően legyen továbbítva diagnosztikai célokra.

## <a name="client-ip-address-is-0000"></a>Az ügyfél IP-címe 0.0.0.0

Február 5 2018-án bejelentettük, hogy az ügyfél IP-címének naplózása el lett távolítva. Ez nem befolyásolja a földrajzi helyet.

> [!NOTE]
> Ha az IP-cím első 3 oktettje szükséges, a [telemetria inicializáló](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) használatával egyéni attribútumokat adhat hozzá.
> Ez nincs hatással a 2018. február 5. előtt összegyűjtött adatokra.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Helytelen földrajzi érték a felhasználói telemetria
A város, a régió és az ország dimenziói az IP-címekből származnak, és nem mindig pontosak. Ezeket az IP-címeket a rendszer az első helyen dolgozza fel, majd a 0.0.0.0 értékre módosítja.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>„A metódus nem található” kivétel az Azure Cloud Servicesben futó rendszeren
A .NET 4.6-os verziójára készítette el az alkalmazást? Az Azure Cloud Services szerepkörei nem támogatják automatikusan a 4.6-os verziót. [Telepítse a 4.6-os verziót mindegyik szerepkörön](../../cloud-services/cloud-services-dotnet-install-dotnet.md), mielőtt futtatná az alkalmazást.

## <a name="troubleshooting-logs"></a>Hibaelhárítási naplók

Az alábbi útmutatást követve rögzítheti a keretrendszer hibaelhárítási naplóit.

### <a name="net-framework"></a>.NET-keretrendszer

1. Telepítse a [Microsoft. AspNet. ApplicationInsights. HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) csomagot a NuGet webhelyről. A telepített verziónak meg kell egyeznie a jelenlegi telepített verziójával `Microsoft.ApplicationInsighs`

2. Módosítsa a applicationinsights.config fájlt, hogy az tartalmazza a következőket:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.FileDiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Az alkalmazásnak írási engedéllyel kell rendelkeznie a konfigurált helyhez

3. Újraindítási folyamat, hogy az SDK az új beállításokat is felveszi

4. Ha elkészült, állítsa át ezeket a módosításokat.

### <a name="net-core"></a>.NET Core

1. Telepítse a [Application INSIGHTS SDK NuGet csomagot ASP.net Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) csomaghoz a NuGet-ból. A telepített verziónak meg kell egyeznie a jelenlegi telepített verziójával `Microsoft.ApplicationInsights` .

   A Microsoft. ApplicationInsights. AspNetCore legújabb verziója a 2.14.0, és a Microsoft. ApplicationInsights 2.14.0 verziójára hivatkozik. Ezért a Microsoft. ApplicationInsights. AspNetCore telepítendő verziójának 2.14.0 kell lennie.

2. Módosítsa `ConfigureServices` a metódust az `Startup.cs` osztályban.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Az alkalmazásnak írási engedéllyel kell rendelkeznie a konfigurált helyhez

3. Újraindítási folyamat, hogy az SDK az új beállításokat is felveszi

4. Ha elkészült, állítsa át ezeket a módosításokat.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a> Naplók gyűjtése a Perfview eszköz
A [perfview eszköz](https://github.com/Microsoft/perfview) egy ingyenes diagnosztikai és teljesítmény-elemzési eszköz, amely a különböző forrásokból származó diagnosztikai információk összegyűjtésével és megjelenítésével segíti a CPU, a memória és az egyéb problémák elkülönítését.

Az Application Insights SDK-napló EventSource önkiszolgáló hibaelhárítási naplókat, amelyeket a Perfview eszköz képes rögzíteni.

Naplók gyűjtéséhez töltse le a Perfview eszköz, és futtassa a következő parancsot:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-LoggerProvider,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Ezeket a paramétereket igény szerint módosíthatja:
- **MaxCollectSec**. A paraméter beállításával megakadályozhatja, hogy a Perfview eszköz határozatlan ideig fusson, és hatással legyen a kiszolgáló teljesítményére.
- **OnlyProviders**. Ezt a paramétert úgy állítsa be, hogy csak a naplókat gyűjtsön az SDK-ból. A listát az adott vizsgálatok alapján testreszabhatja. 
- **NoGui**. Állítsa be ezt a paramétert a naplók a grafikus felhasználói felület nélküli összegyűjtéséhez.


További információk:
- [Teljesítmény-nyomkövetés rögzítése a perfview eszköz](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Application Insights eseményforrás](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/ETW)

## <a name="collect-logs-with-dotnet-trace"></a>Naplók összegyűjtése a DotNet-tracevel

Alternatív módszer a hibakeresési naplók gyűjtésére, amelyek különösen hasznosak lehetnek a Linux-alapú környezetekben [`dotnet-trace`](/dotnet/core/diagnostics/dotnet-trace)

```bash
dotnet-trace collect --process-id <PID> --providers Microsoft-ApplicationInsights-Core,Microsoft-ApplicationInsights-Data,Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,Microsoft-ApplicationInsights-Extensibility-DependencyCollector,Microsoft-ApplicationInsights-Extensibility-HostingStartup,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,Microsoft-ApplicationInsights-Extensibility-Web,Microsoft-ApplicationInsights-Extensibility-WindowsServer,Microsoft-ApplicationInsights-WindowsServer-Core,Microsoft-ApplicationInsights-LoggerProvider,Microsoft-ApplicationInsights-Extensibility-EventSourceListener,Microsoft-ApplicationInsights-AspNetCore
```

## <a name="how-to-remove-application-insights"></a>Application Insights eltávolítása

Megtudhatja, hogyan távolíthatja el Application Insights a Visual Studióban az eltávolítási [cikkben](./remove-application-insights.md)leírt lépéseket követve.

## <a name="still-not-working"></a>Még mindig nem működik...
* [A Microsoft Q&egy kérdés oldalt Application Insights](/answers/topics/azure-monitor.html)

