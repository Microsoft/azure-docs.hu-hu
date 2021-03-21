---
title: Azure-Application Insights automatizálása a PowerShell használatával | Microsoft Docs
description: Erőforrások, riasztások és rendelkezésre állási tesztek létrehozása és kezelése a PowerShellben egy Azure Resource Manager sablon használatával.
ms.topic: conceptual
ms.date: 05/02/2020
ms.openlocfilehash: c2e3d33be487b6a92cb7038d814e17fcd5a10064
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589799"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Application Insights-erőforrások kezelése a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk bemutatja, hogyan automatizálható a [Application Insights](./app-insights-overview.md) -erőforrások automatikus létrehozása és frissítése az Azure Resource Management használatával. Előfordulhat például, hogy egy összeállítási folyamat részeként ezt megteheti. Az alapszintű Application Insights erőforrás mellett létrehozhat [rendelkezésre állási webes teszteket](./monitor-web-app-availability.md), [riasztásokat](../alerts/alerts-log.md)állíthat be, és beállíthatja az [árképzési sémát](pricing.md), és más Azure-erőforrásokat is létrehozhat.

Az erőforrások létrehozásának kulcsa a [Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md)JSON-sablonjai. Az alapszintű eljárás: a meglévő erőforrások JSON-definícióinak letöltése; parametrizálja bizonyos értékeket, például neveket; Ezután futtassa a sablont, amikor új erőforrást szeretne létrehozni. Egyszerre több erőforrást is becsomagolhat, így egyetlen lépéssel létrehozhatja őket, például egy alkalmazás-figyelő rendelkezésre állási tesztekkel, riasztásokkal és tárolással a folyamatos exportáláshoz. A parameterizations néhány finomságot talál, amelyeket itt mutatjuk be.

## <a name="one-time-setup"></a>Egyszeri telepítés
Ha még nem használta a PowerShellt az Azure-előfizetéséhez, mielőtt:

Telepítse a Azure PowerShell modult arra a gépre, amelyen futtatni szeretné a parancsfájlokat:

1. Telepítse a [Microsoft webplatform-telepítőt (V5 vagy újabb)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Microsoft Azure PowerShell telepítéséhez használja.

A Resource Manager-sablonok használata mellett számos [Application Insights PowerShell-parancsmagot](/powershell/module/az.applicationinsights)is tartalmaz, amelyek megkönnyítik Application Insights erőforrások programozott módon konfigurálását. A parancsmagok által engedélyezett képességek a következők:

* Application Insights erőforrások létrehozása és törlése
* Application Insights erőforrások és tulajdonságaik listájának beolvasása
* Folyamatos exportálás létrehozása és kezelése
* Alkalmazás-kulcsok létrehozása és kezelése
* A napi korlát beállítása
* Árképzési terv beállítása

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Application Insights-erőforrások létrehozása PowerShell-parancsmag használatával

A [New-AzApplicationInsights](/powershell/module/az.applicationinsights/new-azapplicationinsights) parancsmaggal az alábbi módon hozhat létre új Application Insights-erőforrást az Azure East US Datacenter szolgáltatásban:

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Application Insights erőforrások létrehozása Resource Manager-sablonnal

Ebből a témakörből megtudhatja, hogyan hozhat létre új Application Insights erőforrást egy Resource Manager-sablon használatával.

### <a name="create-the-azure-resource-manager-template"></a>A Azure Resource Manager sablon létrehozása

Hozzon létre egy új. JSON fájlt – hívjuk meg `template1.json` ezt a példát. Tartalom másolása ide:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 0,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Új Application Insights erőforrás létrehozása a Resource Manager-sablonnal

1. A PowerShellben jelentkezzen be az Azure-ba a `$Connect-AzAccount`
2. Környezet beállítása előfizetésre a következővel `Set-AzContext "<subscription ID>"`
2. Új Application Insights-erőforrás létrehozásához futtasson egy új központi telepítést:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` az a csoport, amelyben létre kívánja hozni az új erőforrásokat.
   * `-TemplateFile` az egyéni paraméterek előtt kell történnie.
   * `-appName` A létrehozandó erőforrás neve.

További paramétereket is hozzáadhat – a sablon paraméterek szakaszában megtalálja a leírásokat.

## <a name="get-the-instrumentation-key"></a>A kialakítási kulcs beszerzése

Az alkalmazás-erőforrás létrehozása után a kialakítási kulcsot érdemes megtekinteni: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

A Application Insights erőforrás számos más tulajdonságának megtekintéséhez használja a következőt:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

A további tulajdonságok a parancsmagok használatával érhetők el:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Tekintse át a parancsmagok paramétereinek [részletes dokumentációját](/powershell/module/az.applicationinsights) .  

## <a name="set-the-data-retention"></a>Az adatok megőrzésének beállítása

Az alábbi három módszer használható az adatmegőrzés programozott beállítására egy Application Insights erőforráson.

### <a name="setting-data-retention-using-a-powershell-commands"></a>Az adatmegőrzés beállítása PowerShell-parancsok használatával

Íme egy egyszerű PowerShell-parancs a Application Insights erőforrás adatmegőrzésének beállításához:

```PS
$Resource = Get-AzResource -ResourceType Microsoft.Insights/components -ResourceGroupName MyResourceGroupName -ResourceName MyResourceName
$Resource.Properties.RetentionInDays = 365
$Resource | Set-AzResource -Force
```

### <a name="setting-data-retention-using-rest"></a>Az adatmegőrzés beállítása a REST használatával

A Application Insights erőforrás aktuális adatmegőrzésének lekéréséhez használhatja az OSS eszközt [ARMClient](https://github.com/projectkudu/ARMClient).  (További információ a cikkek ARMClient: [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) és [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Íme egy példa a használatával `ARMClient` az aktuális megőrzés beszerzéséhez:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Az adatmegőrzés beállításához a parancs egy hasonló PUT:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Ha az adatmegőrzést 365 napra szeretné beállítani a fenti sablonnal, futtassa a következőt:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

### <a name="setting-data-retention-using-a-powershell-script"></a>Az adatmegőrzés beállítása PowerShell-parancsfájl használatával

Az adatmegőrzés megváltoztatásához a következő szkript is használható. Másolja ezt a parancsfájlt a Mentés másként értékre `Set-ApplicationInsightsRetention.ps1` .

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Ezt a szkriptet a következőképpen lehet használni:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>A napi korlát beállítása

A napi Cap-tulajdonságok beszerzéséhez használja a [set-AzApplicationInsightsPricingPlan](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) parancsmagot: 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

A napi sapka tulajdonságainak beállításához használja ugyanazt a parancsmagot. Ha például a korlátot 300 GB/nap értékre szeretné beállítani,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

A [ARMClient](https://github.com/projectkudu/ARMClient) -t is használhatja a napi Cap-paraméterek beolvasásához és beállításához.  Az aktuális értékek beszerzéséhez használja a következőt:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>A napi korlát alaphelyzetbe állítási idejének beállítása

A napi korlát alaphelyzetbe állítási idejének megadásához használhatja a [ARMClient](https://github.com/projectkudu/ARMClient). Íme egy példa a használatával `ARMClient` , ha a visszaállítási időt egy új órára szeretné beállítani (ebben a példában a 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Árképzési terv beállítása 

A jelenlegi díjszabási csomag beszerzéséhez használja a [set-AzApplicationInsightsPricingPlan](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) parancsmagot:

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

A díjszabási terv beállításához használja ugyanazt a parancsmagot a megadott értékkel `-PricingPlan` :  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Az árképzési tervet egy meglévő Application Insights erőforráson is beállíthatja a fenti Resource Manager-sablonnal, és kihagyhatja a "Microsoft. megállapítások/összetevők" erőforrást és a `dependsOn` csomópontot a számlázási erőforrásból. Ha például a GB-os csomagra szeretné beállítani (korábbi nevén alapcsomag), futtassa a következőt:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

A a `priceCode` következőképpen van definiálva:

|priceCode|csomag|
|---|---|
|1|GB-onként (korábbi csomag néven)|
|2|/Csomópont (korábban a vállalati csomag neve)|

Végül a [ARMClient](https://github.com/projectkudu/ARMClient) használatával beolvashatja és beállíthatja az árképzési terveket és a napi Cap-paramétereket.  Az aktuális értékek beszerzéséhez használja a következőt:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

Ezen paraméterek mindegyikét a következővel állíthatja be:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Ez a napi korlátot 200 GB/nap értékűre állítja be, konfigurálja a napi korlát visszaállítási idejét 12:00 UTC értékre, és mindkét esetben küldje el az e-maileket, ha eléri a korlátot, és a figyelmeztetési szint teljesül, és állítsa be a figyelmeztetési küszöbértéket a Cap 90%-ában.  

## <a name="add-a-metric-alert"></a>Metrikai riasztás hozzáadása

A metrikai riasztások létrehozásának automatizálásához olvassa el a [metrika riasztások sablonnal kapcsolatos cikket.](../alerts/alerts-metric-create-templates.md#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Rendelkezésre állási teszt hozzáadása

A rendelkezésre állási tesztek automatizálásához tekintse meg a [metrikus riasztások sablonjának cikkét](../alerts/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="add-more-resources"></a>További erőforrások hozzáadása

Bármilyen más erőforrás létrehozásának automatizálásához hozzon létre egy példát manuálisan, majd másolja és parametrizálja a kódját a [Azure Resource Managerból](https://resources.azure.com/). 

1. Nyissa meg [Azure Resource Manager](https://resources.azure.com/). Navigáljon az `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` alkalmazás-erőforráshoz. 
   
    ![Navigálás Azure Erőforrás-kezelő](./media/powershell/01.png)
   
    Az *összetevők* az alkalmazások megjelenítésének alapszintű Application Insights erőforrásai. A kapcsolódó riasztási szabályokhoz és a rendelkezésre állási webes tesztekhez külön erőforrások tartoznak.
2. Másolja az összetevő JSON-fájlját a megfelelő helyére `template1.json` .
3. Törölje a következő tulajdonságokat:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Nyissa meg a `webtests` és a `alertrules` szakaszt, és másolja a JSON-t az egyes elemekre a sablonba. (Ne másoljon a (z `webtests` `alertrules` ) vagy csomópontokból: lépjen be az alatta lévő elemekre.)
   
    Mindegyik webes teszthez tartozik egy riasztási szabály, ezért mindkettőt másolni kell.
   
5. Szúrja be ezt a sort az egyes erőforrásokban:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>A sablon parametrizálja
Most le kell cserélnie az adott neveket paraméterekkel. [Egy sablon parametrizálja](../../azure-resource-manager/templates/template-syntax.md)a kifejezéseket [segítő függvények](../../azure-resource-manager/templates/template-functions.md)használatával írhatja be. 

A karakterláncok csak egy részét parametrizálja, ezért a karakterláncok `concat()` létrehozásához használja.

Íme néhány példa a használni kívánt helyettesítésekre. Az egyes cserék több előfordulása is van. Előfordulhat, hogy a sablonban másokra is szüksége van. Ezek a példák a sablon tetején definiált paramétereket és változókat használják.

| find | Csere erre |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (kisbetűs) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Az erőforrások közötti függőségek beállítása
Az Azure-nak szigorú sorrendben kell beállítania az erőforrásokat. A következő megkezdése előtt győződjön meg arról, hogy az egyik telepítés befejeződik, és adja hozzá a függőségi sorokat:

* A rendelkezésre állási teszt erőforrásban:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Egy rendelkezésre állási teszt esetén a riasztási erőforrásban:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Következő lépések
Egyéb Automation-cikkek:

* [Hozzon létre egy Application Insights erőforrás](./create-new-resource.md#creating-a-resource-automatically) -gyors metódust sablon használata nélkül.
* [Hozzon létre webes teszteket](../alerts/resource-manager-alerts-metric.md#availability-test-with-metric-alert)
* [Az Azure Diagnostics küldése az Application Insights-ba](powershell-azure-diagnostics.md)
* [Kiadási jegyzetek létrehozása](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)