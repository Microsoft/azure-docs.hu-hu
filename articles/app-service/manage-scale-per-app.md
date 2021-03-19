---
title: Alkalmazáson belüli méretezés nagy sűrűségű üzemeltetéshez
description: Az alkalmazásokat a App Service terveitől függetlenül méretezheti, és optimalizálhatja a csomagban lévő kibővített példányokat.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74672350"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Nagy sűrűségű üzemeltetés Azure App Service alkalmazáson belüli skálázással

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

App Service használatakor az alkalmazások méretezése a [app Service terv](overview-hosting-plans.md) méretezésével végezhető el. Ha több alkalmazás is fut ugyanabban a App Service-csomagban, minden egyes kibővített példány futtatja a csomag összes alkalmazását.

Az *alkalmazáson belüli méretezés* engedélyezhető a app Servicei csomag szintjén, hogy az alkalmazás az általa üzemeltetett app Service-csomagtól függetlenül is méretezhető legyen. Így egy App Service-csomag 10 példányra méretezhető, de egy alkalmazás csak öt használatára állítható be.

> [!NOTE]
> Az alkalmazáson belüli skálázás csak a **standard**, **Premium**, **Premium v2** és **elszigetelt** díjszabási szinteken érhető el.
>

Az alkalmazások az elérhető App Service-csomaghoz vannak lefoglalva, és a lehető legjobb megközelítéssel rendelkeznek a példányok közötti egyenletes elosztáshoz. Bár az egyenletes eloszlás nem garantált, a platform gondoskodik arról, hogy ugyanazon alkalmazás két példánya ne legyen ugyanazon a App Servicei csomag példányán üzemeltetve.

A platform nem támaszkodik mérőszámokra a munkavégzők kiosztásának eldöntéséhez. Az alkalmazások csak akkor lesznek kiegyensúlyozva, ha a példányok fel vannak véve vagy el lettek távolítva a App Service tervből.

## <a name="per-app-scaling-using-powershell"></a>Alkalmazások skálázása a PowerShell használatával

Hozzon létre egy csomagot az alkalmazáson belüli skálázással, és adja ```-PerSiteScaling $true``` át a paramétert a ```New-AzAppServicePlan``` parancsmagnak.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Az alkalmazáson belüli méretezés engedélyezése meglévő App Servicei csomaggal a `-PerSiteScaling $true` paraméternek a parancsmagba való átadásával ```Set-AzAppServicePlan``` .

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Az alkalmazás szintjén állítsa be, hogy az alkalmazás hány példányban használhatja a App Service-csomagot.

Az alábbi példában az alkalmazás legfeljebb két példányra korlátozódik, függetlenül attól, hogy az alapul szolgáló app Service-csomag hány példányra méretezhető.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` eltér a következőtől: `$newapp.MaxNumberOfWorkers` . Az alkalmazáson belüli méretezés az `$newapp.SiteConfig.NumberOfWorkers` alkalmazás méretezési jellemzőinek meghatározására használja.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Alkalmazáson belüli méretezés Azure Resource Manager használatával

A következő Azure Resource Manager sablon jön létre:

- Egy 10 példányra méretezett App Service-csomag
- egy alkalmazás, amely legfeljebb öt példányra méretezhető.

Az App Service terv a **PerSiteScaling** tulajdonságot igaz értékre állítja `"perSiteScaling": true` . Az alkalmazás az 5 értékre állítja a **feldolgozók számát** `"properties": { "numberOfWorkers": "5" }` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Ajánlott konfiguráció a nagy sűrűségű üzemeltetéshez

Az alkalmazások skálázása egy olyan szolgáltatás, amely globális Azure-régiókban és [app Service környezetekben](environment/app-service-app-service-environment-intro.md)egyaránt engedélyezve van. Az ajánlott stratégia azonban a App Service környezetek használata a fejlett funkciók és a nagyobb App Service kapacitás kihasználása érdekében.  

Az alkalmazások nagy sűrűségű üzemeltetésének konfigurálásához kövesse az alábbi lépéseket:

1. Jelöljön ki egy App Service tervet nagy sűrűségű tervként, és a kívánt kapacitásra méretezheti.
1. Állítsa a `PerSiteScaling` jelzőt True értékre a app Service tervben.
1. A rendszer létrehoz egy új alkalmazást, és hozzárendeli azt a App Service tervhez a **numberOfWorkers** tulajdonsággal **1** értékre állítva.
   - Ennek a konfigurációnak a használata a lehető legnagyobb sűrűséget eredményezi.
1. A feldolgozók száma az alkalmazásoktól függetlenül konfigurálható további erőforrások igény szerinti megadásához. Például:
   - Egy magas rendelkezésre állású alkalmazás beállíthatja a **numberOfWorkers** **, hogy** az alkalmazásnak több feldolgozási kapacitása legyen.
   - Az alacsony használatú alkalmazások **numberOfWorkers** értéke **1**.

## <a name="next-steps"></a>Következő lépések

- [Azure App Service tervek részletes áttekintése](overview-hosting-plans.md)
- [Az App Service Environment bemutatása](environment/app-service-app-service-environment-intro.md)
