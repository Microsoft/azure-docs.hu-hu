---
title: 'Rövid útmutató: Profil létrehozása az alkalmazások magas rendelkezésre állásának érdekében – Azure PowerShell – Azure Traffic Manager'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Traffic Manager profilt magas rendelkezésre álló webalkalmazás létrehozásához.
services: traffic-manager
author: duongau
ms.author: duau
manager: twooley
ms.date: 10/01/2020
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: 0fd2ae59f62850da75eecd5423ad225e208dca80
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537660"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Rövid útmutató: Traffic Manager profil létrehozása magas rendelkezésre álló webalkalmazáshoz a Azure PowerShell

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Traffic Manager profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányát fogja létrehozni. Mindegyik másik Azure-régióban fut. Létre fog hozni egy Traffic Manager profilt a [végpont prioritása alapján.](traffic-manager-routing-methods.md#priority-traffic-routing-method) A profil a felhasználói forgalmat a webalkalmazást futtató elsődleges webhelyre irányítja. Traffic Manager webalkalmazást folyamatosan figyeli. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helynek.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a parancsot is futtatnia kell az Azure-ral `Connect-AzAccount` való kapcsolat létrehozásához.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup használatával.](/powershell/module/az.resources/new-azresourcegroup)

```azurepowershell-interactive

# Variables
$Location1="WestUS"

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

## <a name="create-web-apps"></a>Új Web Apps

Ebben a rövid útmutatóban egy webalkalmazás két példányára lesz szüksége, amelyek két különböző Azure-régióban (az *USA* nyugati régiójában és az USA keleti *régiójában) üzembe helyezhetők.* Mindegyik elsődleges és feladatátvételi végpontként szolgál a Traffic Manager.

### <a name="create-web-app-service-plans"></a>Webalkalmazás-App Service létrehozása
Hozzon létre Web App [Service-csomagokat a New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) használatával a két különböző Azure-régióban üzembe helyező webalkalmazás két példányához.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Webalkalmazás létrehozása a App Service tervben
Hozzon létre két példányt a webalkalmazáshoz a [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) használatával a App Service az *USA* nyugati régiójában és az *USA* keleti régiójában.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása
Adja hozzá Web Apps két Traffic Manager végpontként a [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) használatával a Traffic Manager profilhoz az alábbiak szerint:
- Adja hozzá az USA  nyugati régiójában található webalkalmazást elsődleges végpontként az összes felhasználói forgalom útválasztásához. 
- Adja hozzá az USA  keleti régiójában található webalkalmazást feladatátvételi végpontként. Ha az elsődleges végpont nem érhető el, a forgalom automatikusan a feladatátvételi végpontra kerül.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban a saját profilja tartománynevét Traffic Manager ellenőrizni. Az elsődleges végpontot is úgy fogja konfigurálni, hogy az ne legyen elérhető. Végül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek az az oka, Traffic Manager a rendszer elküldi a forgalmat a feladatátvételi végpontra.

### <a name="determine-the-dns-name"></a>A DNS-név meghatározása

Határozza meg a profil DNS Traffic Manager a [Get-AzTrafficManagerProfile használatával.](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Másolja a **RelativeDnsName értéket.** A profil DNS-Traffic Manager *http://<* relativednsname *>.trafficmanager.net.* 

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
1. Egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét *(http://<* relativednsname *>.trafficmanager.net*) a webalkalmazás alapértelmezett webhelyének megtekintéséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban minden kérés az elsődleges végpontra lesz irányítva. A prioritása **1.**
2. A feladatátvételi Traffic Manager megtekintéséhez tiltsa le az elsődleges helyet a [Disable-AzTrafficManagerEndpoint használatával.](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint)

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Másolja a Traffic Manager profil DNS-nevét *(http://<* relativednsname *>.trafficmanager.net*) a webhely új webböngésző-munkamenetben való megtekintéséhez.
4. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett, törölje az erőforráscsoportokat, a webalkalmazásokat és az összes kapcsolódó erőforrást a [Remove-AzResourceGroup parancs használatával.](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára. Ha többet szeretne megtudni a forgalomirányításról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
