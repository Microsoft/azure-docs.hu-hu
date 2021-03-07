---
title: A Application Insights üzembe helyezésének megtervezése – egy vagy több erőforrás?
description: A különböző erőforrásokhoz való közvetlen telemetria fejlesztési, tesztelési és üzemi bélyegzők.
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 9a60981e692a45dd3630073300b206289cfd2a30
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424665"
---
# <a name="how-many-application-insights-resources-should-i-deploy"></a>Hány Application Insights erőforrást kell üzembe helyezni

Ha egy webalkalmazás következő verzióját fejleszti, nem szeretné összekeverni a [Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetria az új verzióról és a már kiadott verzióról. A félreértések elkerülése érdekében küldje el a telemetria különböző fejlesztési szakaszokból, hogy elkülönítse Application Insights erőforrásait, külön rendszerállapot-kulccsal (erőforráskulcsot). Annak érdekében, hogy könnyebb legyen módosítani a kialakítási kulcsot az egyik fázisról a másikra való váltáskor, hasznos lehet a rendszerállapotkulcsot beállítása kódban a konfigurációs fájl helyett.

(Ha a rendszer egy Azure Cloud Service-szolgáltatás, akkor [egy másik módszer a különálló erőforráskulcsot beállítására](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Az erőforrások és a rendszerállapot-kulcsok ismertetése

A webalkalmazás Application Insights figyelésének beállításakor egy Application Insights *erőforrást* hoz létre Microsoft Azure. Ezt az erőforrást a Azure Portalban megnyitva megtekintheti és elemezheti az alkalmazásból gyűjtött telemetria. Az erőforrást egy rendszerállapot- *kulcs* (rendszerállapotkulcsot) azonosítja. Ha a Application Insights csomagot az alkalmazás figyelésére telepíti, azt a kialakítási kulccsal konfigurálja, hogy tudja, hová szeretné elküldeni a telemetria.

Minden Application Insights erőforráshoz elérhető metrikák tartoznak. Ha a teljesen különálló összetevők ugyanarra a Application Insights erőforrásra jelentenek jelentést, előfordulhat, hogy ezek a metrikák nem feltétlenül az irányítópulton/riasztáson alapulnak.

### <a name="when-to-use-a-single-application-insights-resource"></a>Mikor kell egyetlen Application Insights erőforrást használni

-   Együtt telepített alkalmazás-összetevők esetén. Általában egyetlen csapat fejleszti, és ugyanazokat a DevOps/ITOps felhasználók kezelik.
-   Ha a fő teljesítménymutatók (KPI-k), például a válaszadási időtartamok, az irányítópulton található meghibásodási arányok és az ezekből származó hibák összevonására van lehetőség, alapértelmezés szerint (a Metrikaböngésző felhasználói felületén választhat a szerepkör neve alapján).
-   Ha az alkalmazás-összetevők között nem szükséges az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) kezelése.
-   Ha nem szükségesek a metrikák riasztási feltételei, amelyek eltérőek az összetevők között.
-   Ha nem kell a folyamatos exportálást a különböző összetevők között kezelni.
-   Ha nem kell a számlázási/kvótákat az összetevőktől eltérő módon kezelni.
-   Ha az API-kulcs rendben van, az összes összetevővel azonos hozzáférése van az adatokhoz. És a 10 API-kulcs elegendő a szükségletek kielégítéséhez.
-   Ha nem szeretné, hogy ugyanazokat az intelligens észlelési és munkaelem-integrációs beállításokat az összes szerepkörbe feldolgozza.

> [!NOTE]
> Ha több Application Insights erőforrást szeretne összevonni, a meglévő alkalmazás-összetevőket egy új, összevont Application Insights-erőforrásra irányíthatja. A régi erőforrásban tárolt telemetria nem kerül át az új erőforrásra, ezért csak akkor törölje a régi erőforrást, ha elegendő telemetria van az új erőforrásban az üzletmenet folytonossága érdekében.

### <a name="other-things-to-keep-in-mind"></a>További tudnivalók a következőkről:

-   Előfordulhat, hogy egyéni kódot kell hozzáadnia ahhoz, hogy a rendszer a [Cloud_RoleName](./app-map.md?tabs=net#set-or-override-cloud-role-name) attribútumban az értelmes értékeket adja meg. Az attribútumhoz beállított értelmes értékek hiányában a portál egyik felhasználói felülete *sem* fog működni.
- Service Fabric-alkalmazások és a klasszikus Cloud Services esetében az SDK automatikusan beolvassa az Azure szerepkör-környezetet, és beállítja ezeket. Minden más típusú alkalmazás esetében valószínűleg ezt explicit módon kell beállítania.
-   Az élő metrikai élmény nem támogatja a szerepkör-név szerinti felosztást.

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Dynamic Instrumentation-kulcs

Annak érdekében, hogy könnyebb legyen módosítani a rendszerállapotkulcsot, mert a kód az éles fázisok között mozog, a kulcsot hardcoded/statikus érték helyett dinamikusan a kódban kell megadni.

Állítsa be a kulcsot egy inicializálási metódusban, például global.aspx.cs egy ASP.NET-szolgáltatásban:

```csharp
protected void Application_Start()
{
  Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = 
      // - for example -
      WebConfigurationManager.AppSettings["ikey"];
  ...
```

Ebben a példában a különböző erőforrások erőforráskulcsot a webes konfigurációs fájl különböző verzióiba helyezi. A webes konfigurációs fájl cseréje – a kiadási parancsfájl részeként is elvégezhető – a cél erőforrást fogja cserélni.

### <a name="web-pages"></a>Weblapok
A Rendszerállapotkulcsot az alkalmazás weblapjain is használják, a gyors üzembe helyezés [panelen található parancsfájlban](../../azure-monitor/app/javascript.md). Ahelyett, hogy a parancsfájlba kellene írnia, azt a kiszolgáló állapotától kell meghoznia. Például egy ASP.NET-alkalmazásban:

```javascript
<script type="text/javascript">
// Standard Application Insights web page script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
  // Generate from server property:
  "@Microsoft.ApplicationInsights.Extensibility.
     TelemetryConfiguration.Active.InstrumentationKey"
  }
 )
//...
```

## <a name="create-additional-application-insights-resources"></a>További Application Insights erőforrások létrehozása

Az Application ininsights-erőforrások létrehozásához kövesse az [Erőforrás-létrehozási útmutatót](./create-new-resource.md).

### <a name="getting-the-instrumentation-key"></a>A kialakítási kulcs beolvasása
A kialakítási kulcs azonosítja a létrehozott erőforrást.

Szüksége lesz az összes olyan erőforrás rendszerállapot-kulcsaira, amelyekhez az alkalmazás elküldi az adatait.

## <a name="filter-on-build-number"></a>Kiépítés számának szűrése
Amikor közzéteszi az alkalmazás új verzióját, a különböző buildek közül választhat, hogy el szeretné-e különíteni a telemetria.

Megadhatja az alkalmazás verzió tulajdonságát, így szűrheti a [keresési](../../azure-monitor/app/diagnostic-search.md) és [metrikai Explorer](../../azure-monitor/essentials/metrics-charts.md) eredményeit.

Az alkalmazás verzió tulajdonságának beállítása több különböző módszerrel is elvégezhető.

* Közvetlen beállítás:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Ezt a sort egy [telemetria inicializáló](../../azure-monitor/app/api-custom-events-metrics.md#defaults) sorba csomagolva biztosíthatja, hogy az összes TelemetryClient-példány konzisztens legyen.
* [ASP.NET] Állítsa be a verziót a alkalmazásban `BuildInfo.config` . A webmodul a BuildLabel csomópontból fogja kiválasztani a verziót. Adja meg ezt a fájlt a projektben, és ne feledje, hogy a másolás mindig tulajdonságot Megoldáskezelő.

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] BuildInfo.config automatikus előállítása az MSBuild-ben. Ehhez adjon hozzá néhány sort a `.csproj` fájlhoz:

    ```XML
    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Ez létrehoz egy *yourProjectName*.BuildInfo.config nevű fájlt. A közzétételi folyamat átnevezi a BuildInfo.configra.

    A Build címke helyőrzőt (AutoGen_...) tartalmaz a Visual Studióval való kiépítés során. Az MSBuild-sel azonban a megfelelő verziószámmal vannak feltöltve.

    Ha engedélyezni szeretné, hogy az MSBuild verziószámokat hozzon létre, állítsa be a verziót a `1.0.*` AssemblyReference.cs-ben

## <a name="version-and-release-tracking"></a>Verzió- és kiadáskövetés
Az alkalmazásverzió nyomon követéséhez győződjön meg arról, hogy a Microsoft Build Engine folyamat létrehozza a `buildinfo.config` fájlt. A `.csproj` fájlban adja hozzá a következőket:  

```XML
<PropertyGroup>
  <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>
  <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
</PropertyGroup>
```

Ha megkapja a verzióinformációkat, az Application Insights webmodul automatikusan hozzáadja az **Alkalmazás verzióját** tulajdonságként a telemetria minden eleméhez. Ez lehetővé teszi a verziók szerinti szűrést, amikor [diagnosztikai kereséseket](../../azure-monitor/app/diagnostic-search.md) végez, illetve [metrikákat vizsgál](../../azure-monitor/essentials/metrics-charts.md).

Figyelje meg azonban, hogy a build verziószámát csak a Microsoft Build motorja hozza létre, nem pedig a Visual studióból származó fejlesztői Build.

### <a name="release-annotations"></a>Kiadási jegyzetek
Ha az Azure DevOps-t használja, [beolvashatja](../../azure-monitor/app/annotations.md) a diagramokhoz hozzáadott jegyzet jelölőket, amikor új verziót ad ki. 

## <a name="next-steps"></a>Következő lépések

* [Több szerepkör megosztott erőforrásai](../../azure-monitor/app/app-map.md)
* [Telemetria inicializáló létrehozása a következő megkülönböztetéséhez | B változatok](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
