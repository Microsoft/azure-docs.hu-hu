---
title: Új Azure Application Insights-erőforrás létrehozása | Microsoft Docs
description: Application Insights figyelésének manuális beállítása egy új élő alkalmazáshoz.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 3233aed895eac269bd34a961728b3302581ff360
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104586"
---
# <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Az Azure Application Insights Microsoft Azure *erőforrásban* jeleníti meg az alkalmazással kapcsolatos információkat. Egy új erőforrás létrehozása ezért a [Application Insights beállításának része, amely egy új alkalmazás figyelésére][start]szolgál. Miután létrehozta az új erőforrást, megszerezheti a kialakítási kulcsát, és használhatja azt az Application Insights SDK konfigurálásához. A kialakítási kulcs a telemetria az erőforráshoz csatolja.

> [!IMPORTANT]
> A klasszikus Application Insights elavult. Kövesse ezeket az [utasításokat a munkaterületen alapuló Application Insights való frissítéshez](convert-classic-resource.md).

## <a name="sign-in-to-microsoft-azure"></a>Bejelentkezés Microsoft Azure

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és hozzon létre egy Application Insights erőforrást:

![Kattintson a "+" jelre a bal felső sarokban. Válassza a Fejlesztői eszközök, majd a Application Insights](./media/create-new-resource/new-app-insights.png)

   | Beállítások        |  Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Név**      | `Unique value` | A figyelt alkalmazást azonosító név. |
   | **Erőforráscsoport**     | `myResourceGroup`      | Az új vagy meglévő erőforráscsoport neve az alkalmazás-elemzési adatforrások üzemeltetéséhez. |
   | **Régió** | `East US` | Válasszon egy Önhöz közeli helyet, vagy a közelében, ahol az alkalmazás üzemeltetve van. |
   | **Erőforrás mód** | `Classic` vagy `Workspace-based` | A munkaterület-alapú erőforrások jelenleg nyilvános előzetes verzióban érhetők el, és lehetővé teszik, hogy egy közös Log Analytics munkaterületre küldje el Application Insights telemetria. További információt a [munkaterület-alapú erőforrások című cikkben](create-workspace-resource.md)talál.

> [!NOTE]
> Habár ugyanazt az erőforrást használhatja a különböző erőforráscsoportok között, hasznos lehet globálisan egyedi nevet használni. Ez akkor lehet hasznos, ha [több erőforrás-lekérdezést kell végrehajtania](../log-query/cross-workspace-query.md#identifying-an-application) , mivel leegyszerűsíti a szükséges szintaxist.

Adja meg a megfelelő értékeket a kötelező mezőkben, majd válassza a **felülvizsgálat + létrehozás** elemet.

> [!div class="mx-imgBorder"]
> ![Adja meg az értékeket a kötelező mezők mezőben, majd válassza a "felülvizsgálat + létrehozás" lehetőséget.](./media/create-new-resource/review-create.png)

Az alkalmazás létrehozása után megnyílik egy új panel. Ebben az ablaktáblában láthatja a figyelt alkalmazás teljesítmény-és használati adatait. 

## <a name="copy-the-instrumentation-key"></a>A kialakítási kulcs másolása

A kialakítási kulcs azonosítja azt az erőforrást, amelyhez a telemetria-adatait társítja. A kialakítási kulcsot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához.

> [!IMPORTANT]
> Az új Azure-régiókban a rendszerállapot-kulcsok helyett a kapcsolatok sztringjét **kell** használnia. A [kapcsolódási karakterlánc](./sdk-connection-string.md?tabs=net) azonosítja azt az erőforrást, amelyhez hozzá szeretné rendelni a telemetria-adatait. Azt is lehetővé teszi, hogy módosítsa az erőforrás által a telemetria célként használt végpontokat. A kapcsolódási karakterláncot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához vagy egy környezeti változóhoz.

## <a name="install-the-sdk-in-your-app"></a>Az SDK telepítése az alkalmazásban

Telepítse az Application Insights SDK-t az alkalmazásban. Ez a lépés nagymértékben függ az alkalmazás típusától.

Az [alkalmazásban telepített SDK][start]konfigurálásához használja a kialakítási kulcsot.

Az SDK olyan szabványos modulokat tartalmaz, amelyek telemetria küldenek anélkül, hogy további kódokat kellene írnia. A felhasználói műveletek nyomon követéséhez vagy a problémák részletesebb diagnosztizálásához [használja az API][api] -t a saját telemetria küldéséhez.

## <a name="creating-a-resource-automatically"></a>Erőforrás automatikus létrehozása

### <a name="powershell"></a>PowerShell

Új Application Insights-erőforrás létrehozása

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Példa

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Results (Eredmények)

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

A parancsmag teljes PowerShell-dokumentációját és a kialakítási kulcs beolvasásának megismeréséhez tekintse meg a [Azure PowerShell dokumentációját](/powershell/module/az.applicationinsights/new-azapplicationinsights).

### <a name="azure-cli-preview"></a>Azure CLI (előzetes verzió)

Az előzetes verzió Application Insights Azure CLI-parancsokhoz való hozzáféréshez először futtatnia kell a következőt:

```azurecli
 az extension add -n application-insights
```

Ha nem futtatja a `az extension add` parancsot, a következő hibaüzenet jelenik meg: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Most futtathatja a következőt a Application Insights erőforrás létrehozásához:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Példa

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Results (Eredmények)

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

A parancs teljes Azure CLI-dokumentációja, valamint a rendszerállapot-kulcs beolvasásának megismeréséhez tekintse meg az [Azure CLI dokumentációját](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Következő lépések
* [Diagnosztikai keresés](./diagnostic-search.md)
* [Metrikák böngészése](../platform/metrics-charts.md)
* [Analytics-lekérdezések](../log-query/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../platform/metrics-charts.md
[start]: ./app-insights-overview.md

