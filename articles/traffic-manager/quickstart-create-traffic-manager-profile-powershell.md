---
title: 'Rövid útmutató: Profil létrehozása az alkalmazások magas rendelkezésre állásának érdekében – Azure PowerShell – Azure Traffic Manager'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre egy Traffic Manager-profilt egy magas rendelkezésre álló webalkalmazás létrehozásához.
services: traffic-manager
author: duongau
ms.author: duau
manager: kumud
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: 96580a56abaffcc11180a406e00aaabb1cb1e2e7
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727852"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Rövid útmutató: Traffic Manager profil létrehozása magas rendelkezésre álló webalkalmazáshoz a Azure PowerShell

Ez a rövid útmutató ismerteti, hogyan hozhat létre olyan Traffic Manager profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányát fogja létrehozni. Mindegyik másik Azure-régióban fut. A végpont prioritása Traffic Manager [létre.](traffic-manager-routing-methods.md#priority-traffic-routing-method) A profil a felhasználói forgalmat a webalkalmazást futtató elsődleges webhelyre irányítja. Traffic Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helynek.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Az üzembe Traffic Manager környezet Azure PowerShell." border="false":::

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a parancsot is futtatnia kell az Azure-ral `Connect-AzAccount` való kapcsolat létrehozásához.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup használatával.](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive

# Variables
$Location1="EastUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre Traffic Manager profilt a [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) használatával, amely a felhasználói forgalmat a végpont prioritása alapján irányítja.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Create Web Apps

Ebben a rövid útmutatóban egy webalkalmazás két példányára lesz szüksége, amelyek két különböző Azure-régióban (az *USA* nyugati régiójában és az USA *keleti régiójában) üzembe helyezhetők.* Mindegyik elsődleges és feladatátvételi végpontként szolgál majd a Traffic Manager.

### <a name="create-web-app-service-plans"></a>Webes App Service csomagok létrehozása
Hozzon létre Web App [Service-csomagokat a New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) használatával a két különböző Azure-régióban üzembe helyezni kívánt webalkalmazás két példányához.

```azurepowershell-interactive

# Variables
$Location1="EastUS"
$Location2="WestEurope"

# Create an App service plan
New-AzAppservicePlan -Name "myAppServicePlanEastUS" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "myAppServicePlanEastUS" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Webalkalmazás létrehozása a App Service tervben
Hozzon létre két példányt a webalkalmazáshoz a [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) használatával a App Service az *USA* keleti régiójában és a nyugat-európai *Azure-régiókban.*

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name myWebAppEastUS -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "myAppServicePlanEastUS").Id
$App2ResourceId=(New-AzWebApp -Name myWebAppWestEurope -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "myAppServicePlanWestEurope").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása
Adja hozzá Web Apps két Traffic Manager végpontként a [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) használatával a Traffic Manager profilhoz az alábbiak szerint:
- Adja hozzá az USA  keleti régiója Azure-régióban található webalkalmazást elsődleges végpontként az összes felhasználói forgalom útválasztásához. 
- Adja hozzá a nyugat-európai *Azure-régióban* található webalkalmazást feladatátvételi végpontként. Ha az elsődleges végpont nem érhető el, a forgalom automatikusan a feladatátvételi végpontra kerül.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "myPrimaryEndpoint" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "myFailoverEndpoint" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban a saját profilja tartománynevét Traffic Manager ellenőrizni. Emellett úgy konfigurálja az elsődleges végpontot, hogy az ne legyen elérhető. Végül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek az az oka, Traffic Manager elküldi a forgalmat a feladatátvételi végpontra.

### <a name="determine-the-dns-name"></a>A DNS-név meghatározása

Határozza meg a profil DNS Traffic Manager a [Get-AzTrafficManagerProfile használatával.](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Másolja ki **a RelativeDnsName értéket.** A profil DNS-Traffic Manager *http://<* relativednsname *>.trafficmanager.net.* 

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
1. Egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét *(http://<* relativednsname *>.trafficmanager.net*) a webalkalmazás alapértelmezett webhelyének megtekintéséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban minden kérés az elsődleges végpontra lesz irányítva. A prioritása **1.**
2. A feladatátvételi Traffic Manager megtekintéséhez tiltsa le az elsődleges helyet a [Disable-AzTrafficManagerEndpoint használatával.](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint)

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name "myPrimaryEndpoint" `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Másolja a saját Traffic Manager *(http://<* relativednsname *>.trafficmanager.net*) DNS-nevét a webhely új webböngésző-munkamenetben való megtekintéséhez.
4. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett, törölje az erőforráscsoportokat, a webalkalmazásokat és az összes kapcsolódó erőforrást a [Remove-AzResourceGroup parancs használatával.](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager, amely magas rendelkezésre állást biztosít a webalkalmazás számára. Ha többet szeretne megtudni a forgalom útválasztásról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
