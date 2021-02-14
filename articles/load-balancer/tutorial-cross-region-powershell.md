---
title: 'Oktatóanyag: régiók közötti terheléselosztó létrehozása Azure PowerShell használatával'
titleSuffix: Azure Load Balancer
description: Ismerkedjen meg ezzel az Oktatóanyaggal, és telepítsen egy régiók közötti Azure Load Balancer Azure PowerShell használatával.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: b1a249bac4a1a46e52bf52eccd56649153eefe8e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360924"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Oktatóanyag: régiók közötti Azure Load Balancer létrehozása Azure PowerShell használatával

A régiók közötti terheléselosztó biztosítja, hogy a szolgáltatások globálisan elérhetők legyenek több Azure-régió között. Ha az egyik régió meghibásodik, a rendszer átirányítja a forgalmat a legközelebbi, legközelebb egészséges regionális Load balancerbe.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozza létre a régiók közötti Load balancert.
> * Hozzon létre egy terheléselosztó-szabályt.
> * Hozzon létre két regionális terheléselosztó-t tartalmazó háttér-készletet.
> * A terheléselosztó tesztelése.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.
- Két különböző Azure-régióban üzembe helyezett backend-készletekkel rendelkező **standard** SKU Azure Load Balancer.
    - A regionális standard Load Balancer és a backend-készletek virtuális gépei létrehozásával kapcsolatos információkért lásd [: rövid útmutató: nyilvános terheléselosztó létrehozása a virtuális gépek terheléselosztásához Azure PowerShell használatával](quickstart-load-balancer-standard-public-powershell.md).
        - Fűzze hozzá az egyes régiókban található terheléselosztó és virtuális gépek nevét az **-R1** és **-R2** értékkel. 
- Azure PowerShell helyileg vagy Azure Cloud Shell telepítve.


Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-cross-region-load-balancer"></a>Régiók közötti Load Balancer létrehozása


### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Régiók közötti Load Balancer-erőforrások létrehozása

Ebben a szakaszban a régiók közötti terheléselosztó számára szükséges erőforrásokat fogja létrehozni.

Egy globális standard SKU nyilvános IP-címet használ a régiók közötti terheléselosztó előtérbeli felületéhez.

* A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozza létre a nyilvános IP-címet.

* Hozzon létre egy előtér-IP-konfigurációt a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig).

* Hozzon létre egy háttér-címkészletet a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig).

* Hozzon létre egy terheléselosztó-szabályt az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig).

* Hozzon létre egy régiók közötti Load Balancert a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Háttérbeli készlet konfigurálása

Ebben a szakaszban két regionális standard Load balancert fog hozzáadni a régiók közötti terheléselosztó háttér-készletéhez.

> [!IMPORTANT]
> A lépések elvégzéséhez győződjön meg arról, hogy az előfizetésében két regionális terheléselosztó van telepítve a háttér-készletek használatával.  További információ: gyors üzembe helyezés – **[nyilvános terheléselosztó létrehozása a virtuális gépek terheléselosztásához Azure PowerShell használatával](quickstart-load-balancer-standard-public-powershell.md)**.

* A [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) és a [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) használatával tárolhatja a regionális terheléselosztó információit a változókban.

* A Load Balancer háttérbeli címkészlet konfigurációjának létrehozásához használja a [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) .

* A [set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) használatával adja hozzá a regionális terheléselosztó felületet a régiók közötti háttér-készlethez.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Ebben a szakaszban tesztelni fogja a régiók közötti Load balancert. A nyilvános IP-címhez egy böngészőben fog csatlakozni.  A virtuális gépeket a regionális terheléselosztó backend-készletek egyikében állíthatja le, és megfigyelheti a feladatátvételt.

1. A Load Balancer nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) :

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

3. Állítsa le a virtuális gépeket az egyik regionális terheléselosztó háttér-készletében.

4. Frissítse a webböngészőt, és figyelje meg a másik regionális terheléselosztó kapcsolatának feladatátvételét.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és a többi erőforrás.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Létrehozott egy globális IP-címet.
* Létrehozta a régiók közötti Load balancert.
* Létrehozott egy terheléselosztási szabályt.
* Regionális terheléselosztó hozzáadása a régiók közötti terheléselosztó háttér-készletéhez.
* Tesztelte a terheléselosztó.


A következő cikkből megtudhatja, hogyan...
> [!div class="nextstepaction"]
> [Terheléselosztó virtuális gépek a rendelkezésre állási zónák között](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
