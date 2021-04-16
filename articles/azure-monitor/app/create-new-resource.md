---
title: Új Azure Application Insights-erőforrás létrehozása | Microsoft Docs
description: Manuálisan állítsa be Application Insights új élő alkalmazás monitorozását.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 6158b5604046897e20053c67321f26d650c21b7f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566222"
---
# <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Az Azure Application Insights az alkalmazással kapcsolatos adatokat jeleníti meg egy Microsoft Azure *erőforrásban.* Ezért az új erőforrások létrehozása az új alkalmazások figyelése [Application Insights beállításának része.][start] Miután létrehozta az új erőforrást, le tudja szerezni az eszközkulcsát, és ezzel konfigurálhatja a Application Insights SDK-t. Az eszközkulcs a telemetriát az erőforráshoz kapcsolódik.

> [!IMPORTANT]
> [A klasszikus Application Insights elavult.](https://azure.microsoft.com/updates/we-re-retiring-classic-application-insights-on-29-february-2024/) Kövesse ezeket az [utasításokat a munkaterület-alapú](convert-classic-resource.md)alkalmazásra való Application Insights.

## <a name="sign-in-to-microsoft-azure"></a>Jelentkezzen be a Microsoft Azure

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Jelentkezzen be a [Azure Portal,](https://portal.azure.com)és hozzon létre egy Application Insights erőforrást:

![Kattintson a "+" jelre a bal felső sarokban. Válassza Fejlesztői eszközök, majd a Application Insights](./media/create-new-resource/new-app-insights.png)

   | Beállítások        |  Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Név**      | `Unique value` | A figyelt alkalmazást azonosító név. |
   | **Erőforráscsoport**     | `myResourceGroup`      | Az App Insights-adatokat kezelő új vagy meglévő erőforráscsoport neve. |
   | **Régió** | `East US` | Válasszon egy közeli helyet, vagy egy olyan helyet, ahol az alkalmazás üzemeltetve van. |
   | **Erőforrás mód** | `Classic` vagy `Workspace-based` | A munkaterület-alapú erőforrások lehetővé teszik, hogy a Application Insights egy közös Log Analytics-munkaterületre küldjön telemetriai adatokat. További információért tekintse meg [a munkaterület-alapú erőforrásokról való cikket.](create-workspace-resource.md)

> [!NOTE]
> Bár ugyanazt az erőforrásnevet használhatja különböző erőforráscsoportokban, előnyös lehet globálisan egyedi nevet használni. Ez akkor lehet hasznos, ha több erőforrásra vonatkozó [lekérdezéseket tervez végrehajtani,](../logs/cross-workspace-query.md#identifying-an-application) mivel ez leegyszerűsíti a szükséges szintaxist.

Adja meg a megfelelő értékeket a kötelező mezőkben, majd válassza a **Felülvizsgálat + létrehozás lehetőséget.**

> [!div class="mx-imgBorder"]
> ![Adja meg az értékeket a kötelező mezőkben, majd válassza a "felülvizsgálat + létrehozás" lehetőséget.](./media/create-new-resource/review-create.png)

Az alkalmazás létrehozása után megnyílik egy új panel. Ezen a panelen láthatja a figyelt alkalmazás teljesítmény- és használati adatait. 

## <a name="copy-the-instrumentation-key"></a>A eszközkulcs másolása

Az eszközkulcs azonosítja azt az erőforrást, amellyel társítani szeretné a telemetriai adatokat. Ki kell másolnia a rendszeregységkulcsot, és hozzá kell adni az alkalmazás kódjához.

> [!IMPORTANT]
> Az új **Azure-régiókhoz** kapcsolati sztringek használata szükséges a rendszerhasználati kulcsok helyett. [A kapcsolati](./sdk-connection-string.md?tabs=net) sztring azonosítja azt az erőforrást, amellyel társítani szeretné a telemetriai adatokat. Azt is lehetővé teszi, hogy módosítsa azokat a végpontokat, amelyekre az erőforrás a telemetria célhelyeként fog használni. Ki kell másolnia a kapcsolati sztringet, és hozzá kell adni az alkalmazás kódjához vagy egy környezeti változóhoz.

## <a name="install-the-sdk-in-your-app"></a>Az SDK telepítése az alkalmazásban

Telepítse a Application Insights SDK-t az alkalmazásba. Ez a lépés nagy mértékben függ az alkalmazás típusától.

A rendszerelemkulcs használatával konfigurálhatja az alkalmazásban telepítenie [kell az SDK-t.][start]

Az SDK olyan szabványos modulokat tartalmaz, amelyek további kód írása nélkül küldenek telemetriai adatokat. A felhasználói műveletek nyomon követéséhez vagy a problémák részletesebb diagnosztizálásához használja az [API-t][api] a saját telemetria elküldését.

## <a name="creating-a-resource-automatically"></a>Erőforrás automatikus létrehozása

### <a name="powershell"></a>PowerShell

Új erőforrás Application Insights létrehozása

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

A parancsmag teljes PowerShell-dokumentációjért és a rendszerkulcs lekérésének elsajátításért tekintse meg a Azure PowerShell [dokumentációját.](/powershell/module/az.applicationinsights/new-azapplicationinsights)

### <a name="azure-cli-preview"></a>Azure CLI (előzetes verzió)

Az Azure CLI-Application Insights eléréséhez először futtatnia kell a következőt:

```azurecli
 az extension add -n application-insights
```

Ha nem futtatja a parancsot, a következő hibaüzenet `az extension add` jelenik meg: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Most már futtathatja a következőt a Application Insights létrehozásához:

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

A parancs teljes Azure CLI-dokumentációjért és a rendszerkulcs lekérésének elsajátításért tekintse meg az [Azure CLI dokumentációját.](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create)

## <a name="next-steps"></a>Következő lépések
* [Diagnosztikai keresés](./diagnostic-search.md)
* [Metrikák böngészése](../essentials/metrics-charts.md)
* [Analytics-lekérdezések](../logs/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../essentials/metrics-charts.md
[start]: ./app-insights-overview.md

