---
title: Nagy sűrűségű üzemeltetés alkalmazásonkénti használatával méretezés – az Azure App Service |} A Microsoft Docs
description: Az Azure App Service nagy sűrűségű üzemeltetés
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 824abbdfd1b3980b419e6d6c46814bb0318adf13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602326"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Az Azure App Service alkalmazás skálázással nagy sűrűségű üzemeltetés

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

App Service-ben használatakor méretezhetők az alkalmazások méretezése a [App Service-csomag](overview-hosting-plans.md) számítógépen futnak. Amikor több alkalmazás futtatása az App Service-csomag, horizontálisan felskálázott példányonként minden alkalmazás fut a terv.

*Alkalmazásonkénti skálázás* , hogy egymástól függetlenül az App Service-csomag, amelyen ez az alkalmazás méretezése App Service csomag szintjén engedélyezhető. Ezzel a módszerrel App Service-csomag 10 példányra skálázhatók, de az alkalmazás csak öt beállítható.

> [!NOTE]
> Alkalmazásonkénti skálázás csak érhető el **Standard**, **prémium**, **prémium V2** és **elkülönített** díjcsomagok árából.
>

Alkalmazások kiosztott elérhető App Service-csomag megközelítéssel ajánlott beavatkozást az egyenletes eloszlás a példányok között. Az egyenletes eloszlás nem garantált, amíg a platform gondoskodik, hogy ugyanazt az alkalmazást két példánya nem lehet üzemeltethetők App Service-csomag ugyanazon.

A platform nem szükséges feldolgozó foglalási vonatkozó metrikákat. Alkalmazások vannak egyensúlyozva olyan események csak példányok hozzáadásakor vagy eltávolításakor az App Service-csomag.

## <a name="per-app-scaling-using-powershell"></a>Az egyes alkalmazások méretezése a PowerShell használatával

Hozzon létre egy csomagot az alkalmazásonkénti ad méretezés a ```-PerSiteScaling $true``` paramétert a ```New-AzAppServicePlan``` parancsmagot.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Alkalmazásonkénti ad egy meglévő App Service-csomag a skálázás engedélyezése a `-PerSiteScaling $true` paramétert a ```Set-AzAppServicePlan``` parancsmagot.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Az alkalmazás szintjén konfigurálhatja az alkalmazás használhatja az App Service-csomagban lévő példányok száma.

Az alábbi példában az alkalmazás két példánya, függetlenül az alapul szolgáló app service-csomag elvégzi a horizontális felskálázást, hány példányt korlátozva.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` eltérő `$newapp.MaxNumberOfWorkers`. Alkalmazásonkénti skálázásához használt `$newapp.SiteConfig.NumberOfWorkers` meghatározni az alkalmazás méretezési jellemzőit.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Alkalmazásonkénti méretezése az Azure Resource Managerrel

Az alábbi Azure Resource Manager-sablont hoz létre:

- App Service-csomag, amely 10 példányra horizontálisan felskálázott
- egy alkalmazást, amely konfigurálva van egy legfeljebb öt példányban méretezhető.

Az App Service-csomag beállítása a **PerSiteScaling** tulajdonság igaz értékre `"perSiteScaling": true`. Az alkalmazás beállítása a **feldolgozók száma** 5 segítségével `"properties": { "numberOfWorkers": "5" }`.

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

## <a name="recommended-configuration-for-high-density-hosting"></a>Ajánlott konfiguráció, a nagy sűrűségű üzemeltetés

Egy alkalmazás méretezése funkciója engedélyezve van, mindkét globális Azure-régióban és [App Service Environment-környezetek](environment/app-service-app-service-environment-intro.md). A javasolt stratégia azonban az App Service Environment-környezetek segítségével kihasználhatja a speciális szolgáltatások és az App Service-ben a csomag nagyobb kapacitás.  

Kövesse az alábbi lépéseket az alkalmazások nagy sűrűségű üzemeltetés konfigurálása:

1. App Service-csomag megjelölni a nagy sűrűségű tervet, és skálázza fel horizontálisan a kívánt kapacitást.
1. Állítsa be a `PerSiteScaling` jelzőt igaz értékre az App Service-csomag.
1. Új alkalmazások létrehozása és az App Service-csomag rendelve a **numberOfWorkers** tulajdonság **1**.
   - A lehetséges legnagyobb kapacitású használatával Ez a konfiguráció alapján.
1. Feldolgozók száma egymástól függetlenül konfigurálhatók az egyes alkalmazások igény szerint további erőforrások megadását. Példa:
   - Egy sokak által használt alkalmazás állíthatja **numberOfWorkers** való **3** rendelkeznie, amelyet az alkalmazás további feldolgozási kapacitás.
   - Alacsony használható alkalmazások állíthatja **numberOfWorkers** való **1**.

## <a name="next-steps"></a>További lépések

- [Az Azure App Service díjcsomagjainak részletes áttekintése](overview-hosting-plans.md)
- [Az App Service Environment bemutatása](environment/app-service-app-service-environment-intro.md)
