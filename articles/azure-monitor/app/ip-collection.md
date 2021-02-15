---
title: Azure Application Insights IP-címek gyűjteménye | Microsoft Docs
description: Az IP-címek és a térinformatikai kezelésének ismertetése az Azure Application Insights
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3a8969d5d0df2fdacf78815c6f47c7c6bfa73a37
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521917"
---
# <a name="geolocation-and-ip-address-handling"></a>Térinformatikai és IP-címek kezelése

Ez a cikk azt ismerteti, hogyan működik a térinformatikai keresés és az IP-címek kezelése Application Insights az alapértelmezett viselkedés módosításával együtt.

## <a name="default-behavior"></a>Alapértelmezett viselkedés

Alapértelmezés szerint az IP-címek ideiglenes gyűjtése történik, de a Application Insights nem tárolja őket. A folyamat alapvetően a következőképpen történik:

A telemetria az Azure-ba való küldésekor a rendszer az IP-címet használja a térinformatikai kereséshez a [Maxmind GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)használatával. A keresés eredménye a következő mezők feltöltésére szolgál: `client_City` `client_StateOrProvince` , és `client_CountryOrRegion` . Ezt követően a rendszer elveti a címeket, és `0.0.0.0` a `client_IP` mezőbe írja.

* Böngésző telemetria: ideiglenesen gyűjtjük a küldő IP-címét. Az IP-címet a betöltési végpont számítja ki.
* Kiszolgáló telemetria: a Application Insights telemetria modul átmenetileg gyűjti az ügyfél IP-címét. A fejléc beállításakor a rendszer nem gyűjt helyileg helyi IP-címet `X-Forwarded-For` .

Ez a viselkedés úgy működik, hogy segít elkerülni a személyes adatgyűjtést. Amikor csak lehetséges, javasoljuk a személyes adatgyűjtés elkerülését. 

## <a name="overriding-default-behavior"></a>Az alapértelmezett viselkedés felülbírálása

Az alapértelmezett érték az IP-címek összegyűjtése. Továbbra is rugalmasságot biztosítunk a viselkedés felülbírálásához. Azt javasoljuk azonban, hogy a gyűjtemény ne szüntesse meg az összes megfelelőségi követelményt vagy helyi szabályozást. 

Ha többet szeretne megtudni a Application Insights személyes adatainak kezeléséről, tekintse meg a [személyes adatainak útmutatását](../platform/personal-data-mgmt.md).

## <a name="storing-ip-address-data"></a>IP-címek tárolására szolgáló adattároló

Az IP-gyűjtés és-tárolás engedélyezéséhez a `DisableIpMasking` Application Insights összetevő tulajdonságát be kell állítani `true` . Ez a tulajdonság Azure Resource Manager-sablonokkal vagy a REST API meghívásával állítható be. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portál 

Ha csak egyetlen Application Insights erőforrás viselkedését kell módosítania, használja a Azure Portal. 

1. A Application Insights Resource > **Automation**  >  **exportálási sablonjának** átugrása 

2. **Telepítés** kiválasztása

    ![A "Deploy" (üzembe helyezés) feliratú gomb piros színnel](media/ip-collection/deploy.png)

3. Válassza a **Sablon szerkesztése** lehetőséget.

    ![Gomb a "szerkesztés" szöveggel piros színnel](media/ip-collection/edit-template.png)

4. Végezze el az alábbi módosításokat a JSON-ban az erőforráshoz, majd válassza a **Mentés** lehetőséget:

    ![A képernyőfelvétel egy vesszőt ad hozzá a "IbizaAIExtension" után, és új sort ad hozzá a "DisableIpMasking" kifejezéssel: true](media/ip-collection/save.png)

    > [!WARNING]
    > Ha olyan hibát tapasztal, amely azt mondja: **_az erőforráscsoport olyan helyen található, amelyet a sablon egy vagy több erőforrása nem támogat. Válasszon másik erőforráscsoportot._** Ideiglenesen válasszon egy másik erőforráscsoportot a legördülő listából, majd válassza ki újra az eredeti erőforráscsoportot a hiba elhárításához.

5. Válassza az **Elfogadom** a  >  **vásárlás** lehetőséget. 

    ![Az "Elfogadom a fenti feltételeket és kikötéseket" feliratú jelölőnégyzetet a "vásárlás" feliratú piros színnel kiemelve.](media/ip-collection/purchase.png)

    Ebben az esetben semmi újat nem vásárol ténylegesen. Csak a meglévő Application Insights erőforrás konfigurációját frissíti.

6. Az üzembe helyezés befejezése után a rendszer új telemetria-adatrögzítést végez.

    Ha ismét kiválasztja és szerkeszti a sablont, az újonnan hozzáadott tulajdonság nélkül csak az alapértelmezett sablon jelenik meg. Ha nem látja az IP-címadatokat, és szeretné megerősíteni a `"DisableIpMasking": true` beállítását, futtassa a következő PowerShellt: 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Ennek eredményeképpen a rendszer a tulajdonságok listáját adja vissza. Az egyik tulajdonságnak olvasnia kell `DisableIpMasking: true` . Ha a PowerShellt a Azure Resource Manager új tulajdonságának telepítése előtt futtatja, akkor a tulajdonság nem létezik.

### <a name="rest-api"></a>Rest API

A [REST API](/rest/api/azure/) -adattartalom, amely ugyanezeket a módosításokat hajtja végre, a következőképpen történik:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Telemetria inicializáló

Ha rugalmasabb alternatíva szükséges, mint a `DisableIpMasking` , a [telemetria inicializáló](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) használatával másolhatja át az összes IP-címet vagy annak részét egy egyéni mezőre. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Ha nem tud hozzáférni `ISupportProperties` , ellenőrizze, hogy a Application INSIGHTS SDK legújabb stabil kiadását futtatja-e. `ISupportProperties` a magas fokú kardinális értékekhez készültek, míg a kis-és `GlobalProperties` nagymértékű értékek, például a régió neve, a környezet neve stb. megfelelőbbek. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Telemetria-inicializálás engedélyezése a ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>ASP.NET Core telemetria inicializáló engedélyezése

A telemetria inicializáló ASP.net Core ugyanúgy létrehozhatja, mint a ASP.net, de az inicializálás engedélyezéséhez a következő példát használhatja a hivatkozáshoz:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[Ügyféloldali JavaScript](#tab/javascript)

### <a name="client-side-javascript"></a>Ügyféloldali JavaScript

A kiszolgálóoldali SDK-któl eltérően az ügyféloldali JavaScript SDK nem számítja ki az IP-címet. Az ügyféloldali telemetria alapértelmezett IP-címének kiszámítása az Azure-beli betöltési végponton történik. 

Ha közvetlenül az ügyfélszámítógépen szeretné kiszámítani az IP-címet, akkor hozzá kell adnia a saját egyéni logikáját, és az eredményt kell használnia a `ai.location.ip` címke beállításához. Ha `ai.location.ip` be van állítva, a rendszer nem hajtja végre az IP-címek kiszámítását a betöltési végponton, és a megadott IP-címet használja a térinformatikai kereséshez. Ebben az esetben az IP-cím alapértelmezés szerint nulla marad. 

Ha meg szeretné őrizni a teljes IP-címet az egyéni logikából, használhat olyan telemetria-inicializáló, amely átmásolja a megadott IP-cím adatait `ai.location.ip` egy külön egyéni mezőbe. A kiszolgálóoldali SDK-któl eltérően azonban a harmadik féltől származó kódtárak vagy a saját egyéni gyűjtemények logikája nélkül nem számítja ki a címeket.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

Ha az ügyféloldali adat a betöltési végpontra való továbbítás előtt áthalad egy proxyn, az IP-cím kiszámítása során a proxy IP-címe és nem az ügyfél látható. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>A telemetria inicializáló eredményeinek megtekintése

Ha új forgalmat küld a webhelyére, várjon néhány percet. Ezután futtathat egy lekérdezést, amely megerősíti, hogy a gyűjtemény működik:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Az újonnan összegyűjtött IP-címek az oszlopban fognak megjelenni `customDimensions_client-ip` . Az alapértelmezett `client-ip` oszlop továbbra is mind a négy oktetttel egyenlő lesz. 

Ha a localhost-ról történő tesztelés és a `customDimensions_client-ip` értéke `::1` , ez az érték a várt viselkedés. `::1` a visszacsatolási cím az IPv6-ban. Ez egyenértékű az `127.0.0.1` IPv4-ben.

## <a name="next-steps"></a>Következő lépések

* További információ a Application Insights [személyes adatainak gyűjtéséről](../platform/personal-data-mgmt.md) .

* További információ arról, hogyan működik az [IP-címek gyűjteménye](https://apmtips.com/posts/2016-07-05-client-ip-address/) Application Insights. (Ez a cikk egy korábbi külső blogbejegyzés, amelyet az egyik mérnök írt alá. Ez a beállítás a jelenlegi alapértelmezett viselkedést, az IP-címet pedig az aktuálisan rögzített állapotba `0.0.0.0` helyezi, de a beépített mechanika nagyobb mélységbe kerül `ClientIpHeaderTelemetryInitializer` .)
