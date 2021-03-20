---
title: 'Gyors útmutató: az alkalmazások magas rendelkezésre állását biztosító profil létrehozása – Azure PowerShell – Azure Traffic Manager'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre egy Traffic Manager-profilt egy magasan elérhető webalkalmazás létrehozásához.
services: traffic-manager
author: duongau
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: 9b34a17cc9add0bed4bffb7677aa81bb17f3125b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91631562"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Gyors útmutató: Traffic Manager profil létrehozása egy kiválóan elérhető webalkalmazáshoz Azure PowerShell használatával

Ez a rövid útmutató azt ismerteti, hogyan hozhat létre olyan Traffic Manager-profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányát fogja létrehozni. Mindegyik egy másik Azure-régióban fut. A Traffic Manager-profilt a [végpont prioritása](traffic-manager-routing-methods.md#priority-traffic-routing-method)alapján hozza létre. A profil a webes alkalmazást futtató elsődleges helyre irányítja a felhasználói forgalmat. Traffic Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helyhez.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával.

```azurepowershell-interactive

# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager profilt a [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) használatával, amely a felhasználói forgalmat a végponti prioritás alapján irányítja.

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

## <a name="create-web-apps"></a>Web Apps létrehozása

Ebben a rövid útmutatóban két különböző Azure-régióban (az USA *nyugati* régiójában és az *USA keleti* régiójában) üzembe helyezett webalkalmazás két példánya szükséges. Mindegyik a Traffic Manager elsődleges és feladatátvételi végpontjának fogja szolgálni.

### <a name="create-web-app-service-plans"></a>Webes App Service csomagok létrehozása
Hozzon létre webalkalmazás-szolgáltatási csomagokat a [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) használatával a webalkalmazás két különböző Azure-régióban üzembe helyezett példányain.

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
Hozzon létre két példányt a webalkalmazásban a [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) használatával az *USA nyugati* régiójában és az *usa keleti* régiójában található app Service-csomagokban.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása
Adja hozzá a két Web Apps Traffic Manager-végpontként a [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) használatával a Traffic Manager profiljához a következőképpen:
- Adja hozzá az *USA nyugati* régiója Azure-régióban található webalkalmazást elsődleges végpontként az összes felhasználói forgalom átirányításához. 
- Adja hozzá az *USA keleti* régiója Azure-régióban található webalkalmazást feladatátvételi végpontként. Ha az elsődleges végpont nem érhető el, a forgalom automatikusan átirányítja a feladatátvételi végpontot.

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

Ebben a szakaszban a Traffic Manager profil tartománynevét fogja megtekinteni. Azt is beállíthatja, hogy az elsődleges végpont ne legyen elérhető. Végezetül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek oka, hogy Traffic Manager továbbítja a forgalmat a feladatátvételi végpontnak.

### <a name="determine-the-dns-name"></a>A DNS-név meghatározása

Határozza meg a Traffic Manager profil DNS-nevét a [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)használatával.

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Másolja a **RelativeDnsName** értéket. Traffic Manager profiljának DNS-neve *http://<* relativednsname *>. trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
1. A webböngészőben adja meg a Traffic Manager profil (*http://<* relativednsname *>. trafficmanager.net*) DNS-nevét a webalkalmazás alapértelmezett webhelyének megtekintéséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban az összes kérelem az elsődleges végpontra irányítja át. Az **1-es prioritásra** van beállítva.
2. Ha Traffic Manager feladatátvételt szeretné megtekinteni működés közben, tiltsa le az elsődleges helyet a [disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint)használatával.

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Másolja a Traffic Manager profiljának DNS-nevét (*http://<* relativednsname *>. trafficmanager.net*) a webhely új webböngésző-munkamenetben való megtekintéséhez.
4. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, törölje az erőforráscsoportot, a webalkalmazásokat és az összes kapcsolódó erőforrást a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)használatával.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára. Ha többet szeretne megtudni az útválasztási forgalomról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
