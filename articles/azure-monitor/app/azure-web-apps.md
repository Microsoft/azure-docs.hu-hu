---
title: Az Azure app Services teljesítményének figyelése | Microsoft Docs
description: Az alkalmazások teljesítményének figyelése az Azure app Servicesben. A diagram betöltésének és a válaszidő, a függőségi adatok és a riasztások beállítása a teljesítményre.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 74b39219b3b18c8de0214367d141085f6dc5f674
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573999"
---
# <a name="monitor-azure-app-service-performance"></a>Az Azure App Service teljesítményének monitorozása

Az [Azure app Services](../../app-service/index.yml) -on futó ASP.NET és ASP.net Core-alapú webalkalmazások figyelésének engedélyezése mostantól minden eddiginél egyszerűbb. Mivel korábban a hely kiterjesztésének manuális telepítésére volt szükség, alapértelmezés szerint a legújabb bővítmény/ügynök már be van építve az App Service-lemezképbe. Ebből a cikkből megtudhatja, hogyan engedélyezheti Application Insights monitorozását, valamint előzetes útmutatást nyújt a nagyméretű központi telepítések folyamatának automatizálásához.

> [!NOTE]
> Application Insights hely bővítményének manuális hozzáadása a **fejlesztői eszközök**  >  **bővítményein** keresztül elavult. Ez a bővítmény-telepítési módszer az egyes új verziók manuális frissítéseitől függ. A bővítmény legújabb stabil kiadása mostantól a App Service rendszerkép részeként van  [előtelepítve](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) . A fájlok a ben találhatók, `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` és automatikusan frissülnek az egyes stabil kiadásokkal. Ha követi az ügynök-alapú utasításokat az alábbi figyelés engedélyezéséhez, az automatikusan eltávolítja az elavult bővítményt.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az alkalmazások figyelését kétféleképpen engedélyezheti az Azure App Services üzemeltetett alkalmazásaiban:

* **Ügynök-alapú alkalmazás figyelése** (ApplicationInsightsAgent).  
    * Ez a módszer a legkönnyebben engedélyezhető, és nincs szükség speciális konfigurációra. Ezt gyakran "futtatókörnyezet"-figyelőnek nevezzük. Az Azure App Services legalább ezt a monitorozási szintet ajánlott engedélyezni, majd az adott forgatókönyv alapján kiértékelheti, hogy a manuális rendszerállapotra vonatkozó fejlettebb figyelésre van-e szükség.

* Az **alkalmazást a programkódon keresztül manuálisan** , a Application Insights SDK telepítésével végezheti el.

    * Ez a megközelítés sokkal testreszabható, de a [Application INSIGHTS SDK NuGet-csomagokhoz való függőség hozzáadását](./asp-net.md)igényli. Ez a módszer azt is jelenti, hogy a frissítéseket a csomagok legújabb verziójára kell kezelnie.

    * Ha egyéni API-hívásokat kell megadnia az ügynök-alapú figyeléssel alapértelmezés szerint nem rögzített események/függőségek nyomon követéséhez, ezt a metódust kell használnia. További információért tekintse meg az [Egyéni események és mérőszámok API](./api-custom-events-metrics.md) -ját ismertető cikket. Ez jelenleg csak a Linux-alapú számítási feladatok esetében támogatott.

> [!NOTE]
> Ha az ügynök-alapú figyelés és a manuális SDK-alapú kialakítás is észlelhető, a rendszer csak a manuális rendszerállapot-beállításokat fogja figyelembe venni. Ez megakadályozza az ismétlődő adatok küldését. Ha többet szeretne megtudni erről, tekintse meg az alábbi [hibaelhárítási szakaszt](#troubleshooting) .

## <a name="enable-agent-based-monitoring"></a>Ügynök alapú figyelés engedélyezése

# <a name="aspnet"></a>[ASP.NET](#tab/net)

> [!NOTE]
> A APPINSIGHTS_JAVASCRIPT_ENABLED és a urlCompression kombinációja nem támogatott. További információ: a [Hibaelhárítás szakasz](#troubleshooting)magyarázata.


1. Az App Service-hez tartozó Azure Vezérlőpulton **válassza a Application Insights lehetőséget** .

    ![A beállítások területen válassza a Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Válassza az új erőforrás létrehozása lehetőséget, ha már beállított egy Application Insights erőforrást ehhez az alkalmazáshoz. 

     > [!NOTE]
     > Amikor az **OK** gombra kattint az új erőforrás létrehozásához, a rendszer kérni fogja a **figyelési beállítások alkalmazására**. A **Folytatás** elem kiválasztásával összekapcsolja az új Application Insights erőforrást az App Service-be, így az **app Service újraindítását is elindíthatja**. 

     ![Webapp kialakítása](./media/azure-web-apps/create-resource-01.png)

2. Miután meghatározta, hogy melyik erőforrást szeretné használni, kiválaszthatja, hogy az Application-elemzések hogyan gyűjthetnek adatokat egy platformon az alkalmazás számára. A ASP.NET-alkalmazás figyelése alapértelmezés szerint két különböző szintű gyűjteménysel van ellátva.

    ![A képernyőképen a Application Insights site Extensions (új erőforrás létrehozása) lap jelenik meg.](./media/azure-web-apps/choose-options-new.png)
 
 Az alábbiakban az egyes útvonalakon összegyűjtött adatok összegzése látható:
        
| Adatok | ASP.NET alapszintű gyűjtemény | ASP.NET ajánlott gyűjtemény |
| --- | --- | --- |
| Processzor-, memória- és I/O-használati trendek hozzáadása |Igen |Yes |
| Használati trendek gyűjtése, a rendelkezésreállási eredmények és a tranzakciók összevetése | Igen |Yes |
| A gazdafolyamat által nem kezelt kivételek gyűjtése | Igen |Yes |
| Az APM-metrikák pontosságának növelése terhelés alatt, mintavételezés használatakor | Igen |Yes |
| Mikroszolgáltatások összevetése kérési és függőségi határokon keresztül | Nem (csak egypéldányos APM-képességek) |Yes |

3. Ha olyan beállításokat szeretne konfigurálni, mint például a mintavétel, amelyet korábban a applicationinsights.config fájlon keresztül szabályozhat, mostantól a megfelelő előtaggal használhatja ugyanezeket a beállításokat az Alkalmazásbeállítások használatával. 

    * Például a kezdeti mintavételi százalék módosításához hozzon létre egy alkalmazás-beállítást: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` és egy értéket `100` .

    * A támogatott adaptív mintavételi telemetria processzor-beállításainak listájáért tekintse meg a [kódot](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) és a [kapcsolódó dokumentációt](./sampling.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

> [!IMPORTANT]
> A ASP.NET Core következő verziói támogatottak: ASP.NET Core 2,1 és 3,1. A 2,0, 2,2 és 3,0 verziók ki lettek vonva, és már nem támogatottak. A működéséhez frissítsen a .NET Core [támogatott verziójára](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) .

A ASP.NET Core, az önálló üzemelő példányok és a Linux-alapú alkalmazások teljes keretrendszerének megcélzása jelenleg **nem támogatott** az ügynök/bővítmény alapú figyeléssel. (A programkódon keresztüli[manuális](./asp-net-core.md) kialakítás az összes korábbi forgatókönyvben működni fog.)

1. Az App Service-hez tartozó Azure Vezérlőpulton **válassza a Application Insights lehetőséget** .

    ![A beállítások területen válassza a Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Válassza az új erőforrás létrehozása lehetőséget, ha már beállított egy Application Insights erőforrást ehhez az alkalmazáshoz. 

     > [!NOTE]
     > Amikor az **OK** gombra kattint az új erőforrás létrehozásához, a rendszer kérni fogja a **figyelési beállítások alkalmazására**. A **Folytatás** elem kiválasztásával összekapcsolja az új Application Insights erőforrást az App Service-be, így az **app Service újraindítását is elindíthatja**. 

     ![Webapp kialakítása](./media/azure-web-apps/create-resource-01.png)

2. Miután meghatározta, hogy melyik erőforrást szeretné használni, kiválaszthatja, hogy a Application Insights hogyan gyűjtsön adatokat egy platformon az alkalmazás számára. A ASP.NET Core a ASP.NET Core 2,1-es és a 3,1-es **ajánlott gyűjteményt** és **letiltást** kínál.

    ![Beállítások kiválasztása platformon](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A app Service webalkalmazás **Beállítások** területén  >  **válassza a Application Insights**  >  **Engedélyezés** lehetőséget. Node.js ügynök-alapú figyelés jelenleg előzetes verzióban érhető el.

# <a name="java"></a>[Java](#tab/java)

Az [Application Insights java 3,0-ügynökkel](./java-in-process-agent.md) kapcsolatos útmutatást követve engedélyezheti a Java-alkalmazások automatikus rendszerállapot-kialakítását a kód módosítása nélkül.
Az automatikus integráció még nem érhető el App Service számára.

# <a name="python"></a>[Python](#tab/python)

A Python App Service-alapú webalkalmazások jelenleg nem támogatják az automatikus ügynök/bővítmény alapú figyelést. A Python-alkalmazás figyelésének engedélyezéséhez manuálisan kell megadnia [az alkalmazást](./opencensus-python.md).

---

## <a name="enable-client-side-monitoring"></a>Ügyféloldali figyelés engedélyezése

# <a name="aspnet"></a>[ASP.NET](#tab/net)

Az ügyféloldali figyelés ASP.NET. Az ügyféloldali figyelés engedélyezése:

* **Beállítások** **>** **Konfiguráció**
   * Az Alkalmazásbeállítások területen hozzon létre egy **új alkalmazás-beállítást**:

     Név: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Érték: `true`

   * **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.

Az ügyféloldali figyelés letiltásához távolítsa el a társított kulcs értéke párt az Alkalmazásbeállítások közül, vagy állítsa hamis értékre.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

Az ügyféloldali figyelés **alapértelmezés szerint engedélyezve** van ASP.net Core alkalmazások számára az **ajánlott gyűjteménysel**, függetlenül attól, hogy az alkalmazás "APPINSIGHTS_JAVASCRIPT_ENABLED" beállítása megtalálható-e.

Ha valamilyen oknál fogva le szeretné tiltani az ügyféloldali figyelést:

* **Beállítások** **>** **Konfiguráció**
   * Az Alkalmazásbeállítások területen hozzon létre egy **új alkalmazás-beállítást**:

     név: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Érték: `false`

   * **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Az Node.js alkalmazás ügyféloldali figyelésének engedélyezéséhez [manuálisan kell hozzáadnia az ügyféloldali JavaScript SDK-t az alkalmazáshoz](./javascript.md).

# <a name="java"></a>[Java](#tab/java)

A Java-alkalmazás ügyféloldali figyelésének engedélyezéséhez [manuálisan kell hozzáadnia az ügyféloldali JavaScript SDK-t az alkalmazáshoz](./javascript.md).

# <a name="python"></a>[Python](#tab/python)

A Python-alkalmazás ügyféloldali figyelésének engedélyezéséhez [manuálisan kell hozzáadnia az ügyféloldali JavaScript SDK-t az alkalmazáshoz](./javascript.md).

---

## <a name="automate-monitoring"></a>A figyelés automatizálása

Ahhoz, hogy a telemetria-gyűjtést Application Insights használatával engedélyezze, csak az Alkalmazásbeállítások megadása szükséges:

   ![Alkalmazásbeállítások App Service elérhető Application Insights beállításokkal](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Alkalmazásbeállítások definíciói

|Alkalmazás-beállítás neve |  Definíció | Érték |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | A fő bővítmény, amely a futtatókörnyezet figyelését vezérli. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Az alapértelmezett módban csak az alapvető funkciók engedélyezettek az optimális teljesítmény biztosításához. | `default` vagy `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Azt szabályozza, hogy a bináris Újraírási motor `InstrumentationEngine` be legyen-e kapcsolva. Ez a beállítás teljesítménybeli következményekkel jár, és a hatás a hideg indítás/indítás ideje. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Azt szabályozza, hogy az SQL & Azure Table szövege a függőségi hívásokkal együtt rögzítve lesz-e. Teljesítményre vonatkozó figyelmeztetés: a rendszer az alkalmazás hideg indítási idejét fogja érinteni. Ehhez a beállításhoz a szükséges `InstrumentationEngine` . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Alkalmazás-beállítások App Service Azure Resource Manager

A App Services alkalmazás-beállításai kezelhetők és konfigurálhatók Azure Resource Manager- [sablonokkal](../../azure-resource-manager/templates/template-syntax.md). Ez a módszer akkor használható, ha új App Service erőforrásokat telepít Azure Resource Manager automatizálással, vagy a meglévő erőforrások beállításainak módosítására.

Az Alkalmazásbeállítások JSON alapszintű szerkezete az App Service-hez a következő:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Ha például egy Azure Resource Manager sablonra van konfigurálva Application Insightshoz, akkor ez a [sablon](https://github.com/Andrew-MSFT/BasicImageGallery) hasznos lehet, különösen az 238-es [sorból](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)kezdődő szakasz.

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizálhatja egy Application Insights erőforrás létrehozását és az újonnan létrehozott App Servicera mutató hivatkozást.

Ha Azure Resource Manager sablont szeretne létrehozni az összes beállított alapértelmezett Application Insights-beállítással, kezdje el a folyamatot úgy, mintha új webalkalmazást fog létrehozni Application Insights engedélyezve.

**Automatizálási beállítások** kiválasztása

   ![Webalkalmazás-létrehozási menü App Service](./media/azure-web-apps/create-web-app.png)

Ez a beállítás a legújabb Azure Resource Manager sablont hozza létre a konfigurált összes szükséges beállítással.

  ![Webalkalmazás-sablon App Service](./media/azure-web-apps/arm-template.png)

Az alábbi példa az összes példányát lecseréli a  `AppMonitoredSite` hely nevével:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Engedélyezés a PowerShell-lel

Ha engedélyezni szeretné az alkalmazások figyelését a PowerShellen keresztül, csak a mögöttes Alkalmazásbeállítások módosítására van szükség. Az alábbiakban egy minta látható, amely lehetővé teszi az alkalmazások figyelését a "AppMonitoredRG" erőforráscsoport "AppMonitoredSite" nevű webhelyén, és a "012345678-ABCD-ef01-2345-6789abcd" kialakítási kulcsba küldendő adatértékek konfigurálását.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Frissítési figyelési bővítmény/ügynök

### <a name="upgrading-from-versions-289-and-up"></a>Frissítés a verzió 2.8.9 és újabb verziókról

A verzióról történő verziófrissítés automatikusan történik, további műveletek nélkül. 2.8.9. Az új figyelési biteket a rendszer a háttérben továbbítja a cél app Service-be, az alkalmazások újraindításakor pedig a rendszer felveszi őket.

A meglátogatott bővítmény verziójának megkeresése `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Az URL-cím elérési útjának képernyőképe http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Verziófrissítés a következő verziókról: 1.0.0-2.6.5

A verzió 2.8.9 kezdődően az előre telepített hely kiterjesztése van használatban. Ha egy korábbi verziót használ, kétféleképpen frissíthet:

* [Frissítsen a portálon keresztüli engedélyezéssel](#enable-application-insights). (Akkor is, ha telepítve van a Azure App Service Application Insights bővítménye, a felhasználói felület csak az **Engedélyezés** gombot jeleníti meg. A háttérben a régi Private site bővítmény el lesz távolítva.)

* [Frissítés a PowerShell](#enabling-through-powershell)-lel:

    1. Az Alkalmazásbeállítások beállításával engedélyezheti az előre telepített hely kiterjesztése ApplicationInsightsAgent. Lásd: [Engedélyezés a PowerShell](#enabling-through-powershell)-lel.
    2. Távolítsa el manuálisan a Application Insights Extension nevű Private site bővítményt Azure App Service.

Ha a frissítés a 2.5.1-nél korábbi verzióról történik, ellenőrizze, hogy a ApplicationInsigths dll-fájljai el lettek-e távolítva az Application bin mappából, [lásd: hibaelhárítási lépések](#troubleshooting).

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbiakban részletes hibaelhárítási útmutatót talál az Azure App Services-on futó ASP.NET-és ASP.NET Core-alapú alkalmazások bővítmény-és ügynök-alapú figyeléséhez.

> [!NOTE]
> A Java-alkalmazások figyelésének ajánlott módszere az automatikus kiépítés használata a kód módosítása nélkül. Kérjük, kövesse az [Application Insights Java 3,0-ügynökre](./java-in-process-agent.md)vonatkozó irányelveket.


1. Ellenőrizze, hogy az alkalmazást a használatával figyeli-e `ApplicationInsightsAgent` .
    * Győződjön meg arról, hogy az `ApplicationInsightsAgent_EXTENSION_VERSION` alkalmazás beállítása "~ 2" értékre van állítva.
2. Győződjön meg arról, hogy az alkalmazás megfelel a figyelni kívánt követelményeknek.
    * Nyissa meg a következő címet a böngészőben: `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Az https://yoursitename.scm.azurewebsites/applicationinsights eredmények oldal képernyőképe](./media/azure-web-apps/app-insights-sdk-status.png)

    * Győződjön meg arról `Application Insights Extension Status` , hogy a `Pre-Installed Site Extension, version 2.8.12.1527, is running.` 
    * Ha nem fut, kövesse az [Application Insights figyelésének engedélyezése című témakört](#enable-application-insights) .

    * Győződjön meg arról, hogy az állapot forrása létezik, és így néz ki: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Ha nem található hasonló érték, az azt jelenti, hogy az alkalmazás jelenleg nem fut vagy nem támogatott. Az alkalmazás futásának ellenőrzéséhez próbálja meg manuálisan meglátogatni az alkalmazás URL-címét/alkalmazás-végpontját, ami lehetővé teszi, hogy a futásidejű információk elérhetővé váljanak.

    * Erősítse meg, hogy `IKeyExists` a `true`
        * Ha igen `false` , adja hozzá a `APPINSIGHTS_INSTRUMENTATIONKEY` és `APPLICATIONINSIGHTS_CONNECTION_STRING` az rendszerállapotkulcsot GUID azonosítóját az alkalmazás beállításaihoz.

    * Győződjön meg arról, hogy nincsenek bejegyzések a `AppAlreadyInstrumented` , és rendszerhez `AppContainsDiagnosticSourceAssembly` `AppContainsAspNetTelemetryCorrelationAssembly` .
        * Ha bármelyik bejegyzés létezik, távolítsa el a következő csomagokat az alkalmazásból: `Microsoft.ApplicationInsights` , `System.Diagnostics.DiagnosticSource` és `Microsoft.AspNet.TelemetryCorrelation` .
        * Csak ASP.NET Core alkalmazások esetében: abban az esetben, ha az alkalmazás bármely Application Insights csomagra hivatkozik, például ha korábban már telepítette (vagy megkísérelte az eszközt) az [ASP.net Core SDK](./asp-net-core.md)-val, akkor a app Service integrációjának engedélyezése nem lép érvénybe, és az adatai nem jelennek meg Application Insightsban. A probléma megoldásához a portálon kapcsolja be az "együttműködés a Application Insights SDK-val" lehetőséget, és a rendszer elkezdi látni az adatApplication Insights 
        > [!IMPORTANT]
        > Ez a funkció előzetes verzióban érhető el 

        ![A meglévő alkalmazás beállításának engedélyezése](./media/azure-web-apps/netcore-sdk-interop.png)

        Az adatküldés mostantól kód-alapú megközelítéssel történik, még akkor is, ha Application Insights SDK-t eredetileg használták vagy próbálták használni.

        > [!IMPORTANT]
        > Ha az alkalmazás a Application Insights SDK-t használta bármilyen telemetria elküldéséhez, akkor az ilyen telemetria le lesz tiltva – vagyis az egyéni telemetria – ha van ilyen, például a Track * () metódusok és az egyéni beállítások (például a mintavétel), le lesznek tiltva. 


### <a name="php-and-wordpress-are-not-supported"></a>A PHP és a WordPress nem támogatott

A PHP-és a WordPress-webhelyek nem támogatottak. Jelenleg nincs hivatalosan támogatott SDK/ügynök a számítási feladatok kiszolgálóoldali figyeléséhez. A PHP-vagy WordPress-webhelyen lévő ügyféloldali tranzakciók manuális kiépítéséhez azonban az ügyféloldali JavaScriptet a [JavaScript SDK](./javascript.md)használatával lehet elérni a weblapokon.

Az alábbi táblázat részletesen ismerteti, hogy mit jelentenek ezek az értékek, a kiváltó okok és az ajánlott javítások:

|Probléma értéke|Magyarázat|Javítás
|---- |----|---|
| `AppAlreadyInstrumented:true` | Ez az érték azt jelzi, hogy a bővítmény azt észlelte, hogy az SDK bizonyos aspektusai már szerepelnek az alkalmazásban, és a szolgáltatás vissza fog térni. Ennek oka lehet a következőre való hivatkozás `System.Diagnostics.DiagnosticSource` ,  `Microsoft.AspNet.TelemetryCorrelation` vagy `Microsoft.ApplicationInsights`  | Távolítsa el a hivatkozásokat. A hivatkozások némelyike alapértelmezés szerint a Visual Studio-sablonokból adódik hozzá, és a Visual Studio régebbi verziói is hozzáadhatnak hivatkozásokat a alkalmazáshoz `Microsoft.ApplicationInsights` .
|`AppAlreadyInstrumented:true` | Ha az alkalmazás a 2,1-es vagy a 2,2-es ASP.NET Core célozza meg, ez az érték azt jelzi, hogy a bővítmény azt észlelte, hogy az SDK bizonyos aspektusai már szerepelnek az alkalmazásban, és a szolgáltatás vissza fog térni | A .NET Core 2.1-es és 2.2-es ügyfeleinek [ajánlott](https://github.com/aspnet/Announcements/issues/287) a Microsoft. AspNetCore. app meta-Package használata. Emellett kapcsolja be az "együttműködés a Application Insights SDK-val" lehetőséget a portálon (lásd a fenti utasításokat).|
|`AppAlreadyInstrumented:true` | Ezt az értéket is okozhatja, ha a fenti DLL-eket egy korábbi telepítésből az alkalmazás mappájába helyezi. | Törölje az alkalmazás mappáját, és győződjön meg arról, hogy a DLL-fájlok el lesznek távolítva. Győződjön meg arról, hogy a helyi alkalmazás bin-könyvtára és a App Service wwwroot könyvtára is található. (A App Service webalkalmazás wwwroot könyvtárának megkereséséhez: speciális eszközök (kudu) > hibakeresési konzol > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Ez az érték azt jelzi, hogy `Microsoft.AspNet.TelemetryCorrelation` a bővítmény az alkalmazásra hivatkozik, és vissza fog térni. | Távolítsa el a hivatkozást.
|`AppContainsDiagnosticSourceAssembly**:true`|Ez az érték azt jelzi, hogy `System.Diagnostics.DiagnosticSource` a bővítmény az alkalmazásra hivatkozik, és vissza fog térni.| A ASP.NET távolítsa el a hivatkozást. 
|`IKeyExists:false`|Ez az érték azt jelzi, hogy a kialakítási kulcs nem szerepel a Alkalmazásbeállítás `APPINSIGHTS_INSTRUMENTATIONKEY` . Lehetséges okok: Előfordulhat, hogy a rendszer véletlenül eltávolította az értékeket, elfelejtette az értékek beállítását az Automation-parancsfájlban stb. | Győződjön meg arról, hogy a beállítás szerepel a App Service alkalmazás beállításai között.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>A APPINSIGHTS_JAVASCRIPT_ENABLED és a urlCompression nem támogatott

Ha APPINSIGHTS_JAVASCRIPT_ENABLED = True értéket használ a tartalom kódolása esetén, a következőhöz hasonló hibák jelenhetnek meg: 

- 500 URL-Újraírási hiba
- 500,53 URL-írási modul hibája a kimenő üzenetekre vonatkozó Újraírási szabályokkal nem alkalmazható, ha a HTTP-válasz tartalma kódolt ("gzip"). 

Ennek az az oka, hogy a APPINSIGHTS_JAVASCRIPT_ENABLED alkalmazás beállítása true (igaz) értékre van állítva, és a Content-Encoding egyszerre van jelen. Ez a forgatókönyv még nem támogatott. A megkerülő megoldás az alkalmazás beállításaiból való APPINSIGHTS_JAVASCRIPT_ENABLED eltávolítása. Sajnos ez azt jelenti, hogy ha az ügyfél/böngésző oldali JavaScript-rendszerállapotra továbbra is szükség van, a weboldalakhoz manuális SDK-referenciára van szükség. Kövesse a manuális rendszerállapotra vonatkozó [utasításokat](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) a JavaScript SDK-val.

A Application Insights ügynökkel/bővítménnyel kapcsolatos legfrissebb információkért tekintse meg a [kibocsátási megjegyzéseket](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>A Web Apps szolgáltatással telepített alapértelmezett webhely nem támogatja az automatikus ügyféloldali figyelést

Amikor létrehoz egy webalkalmazást az `ASP.NET` Azure-ban vagy a Runtimes szolgáltatásban, `ASP.NET Core` app Services egyetlen statikus HTML-oldalt helyez üzembe kezdő webhelyként. A statikus weblap egy ASP.NET által felügyelt webes kijelzőt is betölt az IIS-ben. Ez lehetővé teszi a kód nélküli kiszolgálóoldali figyelés tesztelését, de nem támogatja az automatikus ügyféloldali figyelést.

Ha szeretné kipróbálni a kód nélküli kiszolgálót és az ügyféloldali figyelést a ASP.NET vagy ASP.NET Core egy Azure App Services-webalkalmazásban, javasoljuk, hogy kövesse az [ASP.net Core-webalkalmazás létrehozásához](../../app-service/quickstart-dotnetcore.md) és a [ASP.NET-keretrendszer webalkalmazás létrehozásához](../../app-service/quickstart-dotnet-framework.md) szükséges hivatalos útmutatókat, majd kövesse az aktuális cikkben található utasításokat a figyelés engedélyezéséhez.

### <a name="connection-string-and-instrumentation-key"></a>A kapcsolatok karakterlánca és a kialakítási kulcsa

Ha kód nélküli figyelést használ, csak a kapcsolódási karakterláncra van szükség. Azonban továbbra is javasoljuk a kialakítási kulcs beállítását, hogy megőrizze a visszamenőleges kompatibilitást az SDK régebbi verzióival, ha manuális rendszerállapotot végez.

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Application Insights vs Azure App Service metrikák standard mérőszámai közötti különbség

Application Insights gyűjti a telemetria az alkalmazásba feltett kérelmekhez. Ha a hiba a WebApps/IIS-ben történt, és a kérés nem érte el a felhasználói alkalmazást, Application Insights nem fog tudni telemetria.

`serverresponsetime`Application Insights alapján számított időtartam nem szükségszerűen egyezik a Web Apps által megfigyelt kiszolgálói válaszidő értékével. Ennek az az oka, hogy Application Insights csak azt az időtartamot számítja fel, amikor a kérés ténylegesen eléri a felhasználói alkalmazást. Ha a kérelem beragadva/várólistán van az IIS-ben, akkor a várakozási idő szerepelni fog a webalkalmazás-mérőszámokban, de nem Application Insights mérőszámokban.

## <a name="release-notes"></a>Kibocsátási megjegyzések

A legújabb frissítések és hibajavítások [olvassa el a kibocsátási megjegyzéseket](./web-app-extension-release-notes.md).

## <a name="next-steps"></a>Következő lépések
* [Futtassa a profilkészítőt a működő alkalmazásán.](./profiler.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – az Azure Functions figyelése az Application Insights segítségével
* [Engedélyezze az Azure Diagnostics](../agents/diagnostics-extension-to-application-insights.md) Application Insightsba való küldését.
* [Figyelje a szolgáltatások állapotával kapcsolatos mérőszámokat](../data-platform.md), így meggyőződhet róla, hogy szolgáltatása elérhető és válaszkész.
* [Riasztási értesítéseket kaphat](../alerts/alerts-overview.md), ha működési események történnek vagy a mérőszámok átlépnek egy küszöbértéket.
* Az [Application Insights JavaScript-alkalmazásokhoz és weblapokhoz](javascript.md) való használatával ügyféltelemetriát kaphat azoktól a böngészőktől, amelyek ellátogatnak egy weblapra.
* [Állítson be rendelkezésre állási webes teszteket](monitor-web-app-availability.md), így riasztást kaphat, ha webhelye nem működik.
