---
title: Alkalmazásonkénti skálázás nagy sűrűségű üzemeltetéshez
description: Az alkalmazásokat a csomagtól App Service skálázhatja, és optimalizálhatja a tervben a horizontálisan felskálált példányokat.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 756117a2a231fcb406fd3e3102a16c318c621aa0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832607"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Nagy sűrűségű üzemeltetés Azure App Service alkalmazásonkénti skálázás használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A App Service skálázhatja az alkalmazásokat a futtatás [App Service méretezésével.](overview-hosting-plans.md) Ha több alkalmazás fut ugyanabban a App Service-csomagban, minden horizontálisan felskálált példány a csomag összes alkalmazását futtatja.

*Az alkalmazásonkénti skálázás* a App Service csomag szintjén engedélyezhető, így az alkalmazás az azt tartalmazó csomagtól függetlenül App Service skálázható. Így a App Service 10 példányra skálázható, de egy alkalmazás beállítható úgy, hogy csak öt példányt használjon.

> [!NOTE]
> Az alkalmazásonkénti skálázás csak a **Standard,** **Prémium,** **Prémium V2** és **Izolált** tarifacsomagok esetében érhető el.
>

Az alkalmazások az elérhető App Service tervhez vannak rendelve, a lehető legjobb megközelítéssel, a példányok közötti egyenletes elosztás érdekében. Bár az egyenletes terjesztés nem garantált, a platform biztosítani fogja, hogy ugyanannak az alkalmazásnak két példánya ne legyen ugyanazon a csomagpéldányon App Service példányon.

A platform nem támaszkodik metrikákra a feldolgozói kiosztásra vonatkozó döntéshez. Az alkalmazások újraegyensúlyozása csak akkor történik meg, ha példányokat adnak hozzá vagy távolítnak el App Service tervből.

## <a name="per-app-scaling-using-powershell"></a>Alkalmazásonkénti skálázás a PowerShell használatával

Az alkalmazásonkénti skálázható csomag létrehozásához a paramétert kell átadva a ```-PerSiteScaling $true``` ```New-AzAppServicePlan``` parancsmagnak.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Engedélyezze az alkalmazásonkénti skálázást egy meglévő App Service Plan paraméterrel a `-PerSiteScaling $true` ```Set-AzAppServicePlan``` parancsmagnak való átadásával.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Az alkalmazás szintjén konfigurálja, hogy az alkalmazás hány példányt használhat a App Service tervben.

Az alábbi példában az alkalmazás két példányra van korlátozva, függetlenül attól, hogy az alapul szolgáló App Service-csomag hány példányra van felskálzva.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` különbözik az `$newapp.MaxNumberOfWorkers` -től. Az alkalmazásonkénti skálázás a használatával határozza meg `$newapp.SiteConfig.NumberOfWorkers` az alkalmazás skálázhatósági jellemzőit.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Alkalmazásonkénti skálázás Azure Resource Manager

Az alábbi Azure Resource Manager sablon a következőket hozza létre:

- Egy App Service 10 példányra horizontálisan felskálált terv
- egy legfeljebb öt példányra való skálázható alkalmazás.

A App Service a **PerSiteScaling** tulajdonságot true (igaz) értékre kell `"perSiteScaling": true` állitja. Az alkalmazás 5-re beszkennen **a dolgozók** `"properties": { "numberOfWorkers": "5" }` számát.

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

## <a name="recommended-configuration-for-high-density-hosting"></a>Nagy sűrűségű üzemeltetéshez ajánlott konfiguráció

Az alkalmazásonkénti skálázás egy olyan funkció, amely a globális Azure-régiókban és az App Service [is engedélyezve van.](environment/app-service-app-service-environment-intro.md) Az ajánlott stratégia azonban az, hogy App Service környezetek használatával kihasználják a speciális funkciókat és a nagyobb App Service kapacitását.  

Az alábbi lépésekkel konfigurálhatja az alkalmazások nagy sűrűségű üzemeltetését:

1. Jelöljön ki App Service terv nagy sűrűségű tervként, és méretezze fel a kívánt kapacitásra.
1. Állítsa a `PerSiteScaling` jelzőt true (igaz) értékre a App Service tervben.
1. Új alkalmazások létrejön és hozzá lesz rendelve a App Service csomaghoz, és a **numberOfWorkers** tulajdonság **beállítása 1.**
   - Ennek a konfigurációnak a használata a lehető legnagyobb sűrűséget eredményez.
1. A dolgozók száma alkalmazásonként függetlenül konfigurálható, hogy szükség szerint további erőforrásokat biztosítsunk. Például:
   - A magas kihasználtságú alkalmazások a **NumberOfWorkers (Feldolgozók száma)** **beállítással 3-ra** állíthatják, hogy több feldolgozási kapacitással rendelkeznek az adott alkalmazáshoz.
   - Az alacsony használatú alkalmazások a **numberOfWorkers (Munkatársak száma)** **1-re állítva.**

## <a name="next-steps"></a>Következő lépések

- [Azure App Service csomagok részletes áttekintése](overview-hosting-plans.md)
- [Az App Service Environment bemutatása](environment/app-service-app-service-environment-intro.md)
