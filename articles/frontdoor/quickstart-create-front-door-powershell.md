---
title: 'Gyors útmutató: magas rendelkezésre állás beállítása az Azure bejárati ajtóval – Azure PowerShell'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre magas rendelkezésre állású és nagy teljesítményű globális webalkalmazást a Azure PowerShell használatával az Azure bejárati ajtó használatával.
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 8ccc50dfc4c53f1ee207a04d7d7775775f13cb65
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91348932"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Gyors útmutató: bevezető ajtó létrehozása egy nagyszámú globális webes alkalmazáshoz Azure PowerShell használatával

Az Azure bejáratának első lépései a Azure PowerShell használatával magas rendelkezésre állású és nagy teljesítményű globális webalkalmazások létrehozására használhatók.

A bejárati ajtó a webes forgalmat egy háttér-készlet adott erőforrásaira irányítja. Meghatározta a felületi tartományt, erőforrásokat adhat hozzá egy háttér-készlethez, és létrehozhat egy útválasztási szabályt. Ez a cikk egy háttér-készlet egyszerű konfigurációját használja két webalkalmazás-erőforrással és egyetlen útválasztási szabállyal, amely az alapértelmezett elérési utat használja a "/*" értékkel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure PowerShell vagy Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Webalkalmazás két példányának létrehozása

Ez a rövid útmutató egy olyan webalkalmazás két példányát igényli, amely különböző Azure-régiókban fut. Mind a webalkalmazás-példányok aktív/aktív módban futnak, így akár egy forgalom is elvégezhető. Ez a konfiguráció különbözik az aktív/készenléti konfigurációtól, ahol az egyik feladatátvételként működik.

Ha még nem rendelkezik webalkalmazással, a következő parancsfájl használatával állítson be két példa webalkalmazást.

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

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a bejárati ajtó következő összetevőit:
    
* Az előtér-objektum az alapértelmezett tartományt tartalmazza.
* A háttérbeli készlet egyenértékű háttérrendszer, amelybe a betöltési ajtó terhelése kiegyenlíti az ügyfél kérelmét.
* Egy útválasztási szabály leképezi az előtér-gazdagépet és a hozzá tartozó URL-elérésiút-mintát egy adott háttér-készletre.

### <a name="create-a-frontend-object"></a>Előtér-objektum létrehozása

Az előtér-objektum konfigurálja az állomásnevet a bejárati ajtóhoz. Alapértelmezés szerint a hostname a **. azurefd.net* utótagot fogja tartalmazni.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>A háttérkészlet létrehozása

A háttér-készlet a rövid útmutató elején létrehozott két webalkalmazásból áll. Az ebben a lépésben meghatározott állapot-mintavételi és terheléselosztási beállítások az alapértelmezett értékeket használják.

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

Az útválasztási szabály leképezi a háttér-készletet a előtér-tartományba, és az alapértelmezett elérési utat állítja be "/*" értékre.

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
### <a name="create-the-front-door"></a>A bejárati ajtó létrehozása

Most, hogy létrehozta a szükséges objektumokat, hozza létre a bejárati ajtót:

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

Ha az üzembe helyezés sikeres volt, a következő szakaszban ismertetett lépéseket követve tesztelheti azt.

## <a name="test-the-front-door"></a>A bejárati ajtó tesztelése

Futtassa az alábbi parancsokat az állomásnév beszerzéséhez a bejárati ajtóhoz.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Nyisson meg egy webböngészőt, és adja meg az állomásnév beszerzését a parancsokból. A bejárati ajtó irányítja a kérést az egyik háttérbeli erőforráshoz. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Elülső ajtó teszt lapja":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a bejárati ajtóval létrehozott erőforrásokra, törölje az erőforráscsoportot. Az erőforráscsoport törlésekor a bejárati ajtót és az ahhoz kapcsolódó összes erőforrást is törli. 

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a következőket:
* Front Door
* Két webalkalmazás

Ha szeretné megtudni, hogyan adhat hozzá egyéni tartományt a bejárati ajtóhoz, folytassa az előtérben lévő oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](front-door-custom-domain.md)
