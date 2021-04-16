---
title: 'Rövid útmutató: Magas rendelkezésre állás beállítása Azure Front Door – Azure PowerShell'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Front Door magas rendelkezésre állású és nagy teljesítményű globális webalkalmazásokat a Azure PowerShell.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: cd439a5931340f56401e5f6ba7a4e09f35ab7c7d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539050"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Rövid útmutató: Front Door létrehozása magas rendelkezésre álló globális webalkalmazáshoz a Azure PowerShell

A Azure Front Door használatának első Azure PowerShell egy magas rendelkezésre álló és nagy teljesítményű globális webalkalmazás létrehozásához.

A Front Door a webes forgalmat egy háttérkészlet adott erőforrásaihoz irányítja. Meghatározta az előteretartományt, erőforrásokat adott hozzá egy háttérkészlethez, és létrehozott egy útválasztási szabályt. Ez a cikk az egyik háttérkészlet egyszerű konfigurációját használja két webalkalmazás-erőforrással és egyetlen útválasztási s szabálysal, amely az alapértelmezett elérésiút-egyeztetést (/*) használja.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell helyileg vagy helyileg telepített Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoporthoz rendeli. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup segítségével:](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Webalkalmazás két példányának létrehozása

Ehhez a rövid útmutatóhoz két példányra van szükség egy webalkalmazásból, amelyek különböző Azure-régiókban futnak. Mindkét webalkalmazáspéldány Aktív/Aktív módban fut, így bármelyik képes forgalmat venni. Ez a konfiguráció eltér az aktív/készenléti konfigurációtól, ahol az egyik feladatátvételként működik.

Ha még nem rendelkezik webalkalmazással, a következő szkript használatával állítson be két példa-webalkalmazást.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>Bejárati ajtó létrehozása

Ez a szakasz részletesen bemutatja, hogyan hozhatja létre és konfigurálhatja a Front Door:
    
* Az előtereobjektum tartalmazza a Front Door alapértelmezett tartományt.
* A háttérkészlet egyenértékű háttérkészletek halmaza, amelyekhez Front Door terheléselosztást az ügyfélkéréshez.
* Az útválasztási szabályok leképezik az előtere gazdagépet és az egyező URL-elérésiút-mintát egy adott háttérkészletre.

### <a name="create-a-frontend-object"></a>Előtereobjektum létrehozása

Az előtereobjektum konfigurálja a gazdagépnevet a Front Door. Alapértelmezés szerint az állomásnév utótagja **.azurefd.net.*

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>A háttérkészlet létrehozása

A háttérkészlet a rövid útmutató elején létrehozott két webalkalmazásból áll. Az ebben a lépésben meghatározott állapot-mintavételi és terheléselosztási beállítások alapértelmezett értékeket használnak.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>Útválasztási szabály létrehozása

Az útválasztási szabály leképezi a háttérkészletet az előteretartományra, és az alapértelmezett elérésiút-egyeztetési értéket a "/*" értékre állítja.

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>A Front Door

Most, hogy létrehozta a szükséges objektumokat, hozza létre a Front Door:

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

Ha az üzembe helyezés sikeres volt, a következő szakaszban található lépéseket követve tesztelheti.

## <a name="test-the-front-door"></a>A Front Door

Futtassa a következő parancsokat a gazdagépnév le Front Door.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Nyisson meg egy webböngészőt, és írja be a parancsokból lekért gazdanevet. A Front Door a kérést az egyik háttérerőforráshoz irányítja. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Front Door tesztoldal":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoporttal létrehozott Front Door, törölje az erőforráscsoportot. Az erőforráscsoport törlésekor törli az erőforráscsoportot Front Door az összes kapcsolódó erőforrást is. 

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőt hozta létre:
* Front Door
* Két webalkalmazás

Ha meg szeretne ismerkedni az egyéni tartomány hozzáadásának Front Door, folytassa a Front Door oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](front-door-custom-domain.md)
