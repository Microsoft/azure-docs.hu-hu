---
title: 'Azure VPN Gateway: átjáró törlése: PowerShell'
description: Virtuális hálózati átjáró törlése a PowerShell használatával a Resource Manager-alapú üzemi modellben.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 09/03/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 48e0998719ea19c0b360b50d8c9171d2b448ad19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89440762"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Virtuális hálózati átjáró törlése a PowerShell használatával
> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasszikus)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

A VPN-átjáró konfigurációjának virtuális hálózati átjárójának törléséhez több különböző módszer is rendelkezésre áll.

- Ha mindent törölni szeretne, és a tesztelési környezethez hasonlóan szeretné elindulni, akkor törölheti az erőforráscsoportot. Ha töröl egy erőforráscsoportot, az törli a csoporton belüli összes erőforrást. Ez a módszer csak akkor ajánlott, ha nem szeretné megőrizni az erőforráscsoport egyik erőforrását sem. Ezzel a módszerrel nem törölhet szelektíven néhány erőforrást.

- Ha szeretné megőrizni az erőforráscsoport egyes erőforrásait, a virtuális hálózati átjáró törlése valamivel bonyolultabb lesz. A virtuális hálózati átjáró törléséhez először törölnie kell minden olyan erőforrást, amely az átjárótól függ. A követett lépések a létrehozott kapcsolatok típusától és az egyes kapcsolatok függő erőforrásaitól függenek.

## <a name="before-beginning"></a>Mielőtt hozzálát



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Töltse le a legújabb Azure Resource Manager PowerShell-parancsmagokat.

Töltse le és telepítse a Azure Resource Manager PowerShell-parancsmagok legújabb verzióját. További információ a PowerShell-parancsmagok letöltéséről és telepítéséről: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).

### <a name="2-connect-to-your-azure-account"></a>2. kapcsolódjon az Azure-fiókhoz.

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

```powershell
Connect-AzAccount
```

Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzSubscription
```

Ha egynél több előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Helyek közötti VPN-átjáró törlése

Ha törölni szeretne egy virtuális hálózati átjárót egy S2S-konfigurációhoz, először törölnie kell minden olyan erőforrást, amely a virtuális hálózati átjáróra vonatkozik. A függőségek miatt bizonyos sorrendben törölni kell az erőforrásokat. Ha az alábbi példákkal dolgozik, bizonyos értékeket meg kell adni, míg más értékek a kimeneti eredmények. A példákban a következő konkrét értékeket használjuk a bemutató céljára:

VNet neve: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjáró neve: GW1<br>

A következő lépések a Resource Manager-alapú üzemi modellre vonatkoznak.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. szerezze be a törölni kívánt virtuális hálózati átjárót.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Ellenőrizze, hogy a virtuális hálózati átjáró rendelkezik-e kapcsolatokkal.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. törölje az összes kapcsolatot.

A rendszer kérheti, hogy erősítse meg az egyes kapcsolatok törlését.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. törölje a virtuális hálózati átjárót.

A rendszer kérheti, hogy erősítse meg az átjáró törlését. Ha a S2S-konfiguráción kívül P2S-konfigurációval rendelkezik ehhez a VNet, a virtuális hálózati átjáró törlésével a rendszer figyelmeztetés nélkül automatikusan leválasztja az összes P2S-ügyfelet.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjárót törölték. A következő lépésekkel törölheti a már nem használt erőforrásokat.

### <a name="5-delete-the-local-network-gateways"></a>5 törölje a helyi hálózati átjárókat.

A megfelelő helyi hálózati átjárók listájának beolvasása.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Törölje a helyi hálózati átjárókat. A rendszer kérheti, hogy erősítse meg az egyes helyi hálózati átjárók törlését.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. törölje a nyilvános IP-cím erőforrásait.

Szerezze be a virtuális hálózati átjáró IP-konfigurációit.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Az ehhez a virtuális hálózati átjáróhoz használt nyilvános IP-cím erőforrások listájának beolvasása. Ha a virtuális hálózati átjáró aktív-aktív, két nyilvános IP-címet fog látni.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Törölje a nyilvános IP-erőforrásokat.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. törölje az átjáró-alhálózatot, és állítsa be a konfigurációt.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>VNet-VNet VPN-átjáró törlése

Ha törölni szeretne egy virtuális hálózati átjárót egy V2V-konfigurációhoz, először törölnie kell minden olyan erőforrást, amely a virtuális hálózati átjáróra vonatkozik. A függőségek miatt bizonyos sorrendben törölni kell az erőforrásokat. Ha az alábbi példákkal dolgozik, bizonyos értékeket meg kell adni, míg más értékek a kimeneti eredmények. A példákban a következő konkrét értékeket használjuk a bemutató céljára:

VNet neve: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjáró neve: GW1<br>

A következő lépések a Resource Manager-alapú üzemi modellre vonatkoznak.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. szerezze be a törölni kívánt virtuális hálózati átjárót.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Ellenőrizze, hogy a virtuális hálózati átjáró rendelkezik-e kapcsolatokkal.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Előfordulhat, hogy más kapcsolat van egy másik erőforráscsoport részét képező virtuális hálózati átjáróval. További kapcsolatok keresése minden további erőforráscsoporthoz. Ebben a példában a RG2-kapcsolaton keresztül ellenőrzi a kapcsolatokat. Futtassa ezt minden olyan erőforráscsoport esetében, amelyhez kapcsolódhat a virtuális hálózati átjáróhoz.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. a kapcsolatok listájának beolvasása mindkét irányban.

Mivel ez egy VNet-VNet konfiguráció, mindkét irányban szükség van a kapcsolatok listájára.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Ebben a példában a RG2-kapcsolaton keresztül ellenőrzi a kapcsolatokat. Futtassa ezt minden olyan erőforráscsoport esetében, amelyhez kapcsolódhat a virtuális hálózati átjáróhoz.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. törölje az összes kapcsolatot.

A rendszer kérheti, hogy erősítse meg az egyes kapcsolatok törlését.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. törölje a virtuális hálózati átjárót.

A rendszer kérheti, hogy erősítse meg a virtuális hálózati átjáró törlését. Ha a V2V-konfiguráción kívül P2S-konfigurációval rendelkezik a virtuális hálózatok, a virtuális hálózati átjárók törlésével a rendszer figyelmeztetés nélkül automatikusan leválasztja az összes P2S-ügyfelet.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjárót törölték. A következő lépésekkel törölheti a már nem használt erőforrásokat.

### <a name="6-delete-the-public-ip-address-resources"></a>6. a nyilvános IP-cím erőforrásainak törlése

Szerezze be a virtuális hálózati átjáró IP-konfigurációit.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Az ehhez a virtuális hálózati átjáróhoz használt nyilvános IP-cím erőforrások listájának beolvasása. Ha a virtuális hálózati átjáró aktív-aktív, két nyilvános IP-címet fog látni.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Törölje a nyilvános IP-erőforrásokat. A rendszer kérheti, hogy erősítse meg a nyilvános IP-cím törlését.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. törölje az átjáró-alhálózatot, és állítsa be a konfigurációt.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Pont – hely típusú VPN-átjáró törlése

Ha törölni szeretne egy virtuális hálózati átjárót egy P2S-konfigurációhoz, először törölnie kell minden olyan erőforrást, amely a virtuális hálózati átjáróra vonatkozik. A függőségek miatt bizonyos sorrendben törölni kell az erőforrásokat. Ha az alábbi példákkal dolgozik, bizonyos értékeket meg kell adni, míg más értékek a kimeneti eredmények. A példákban a következő konkrét értékeket használjuk a bemutató céljára:

VNet neve: VNet1<br>
Erőforráscsoport neve: RG1<br>
Virtuális hálózati átjáró neve: GW1<br>

A következő lépések a Resource Manager-alapú üzemi modellre vonatkoznak.


>[!NOTE]
> Ha törli a VPN-átjárót, a rendszer figyelmeztetés nélkül leválasztja az összes csatlakoztatott ügyfelet a VNet.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. szerezze be a törölni kívánt virtuális hálózati átjárót.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. törölje a virtuális hálózati átjárót.

A rendszer kérheti, hogy erősítse meg a virtuális hálózati átjáró törlését.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Ezen a ponton a virtuális hálózati átjárót törölték. A következő lépésekkel törölheti a már nem használt erőforrásokat.

### <a name="3-delete-the-public-ip-address-resources"></a>3. a nyilvános IP-cím erőforrásainak törlése

Szerezze be a virtuális hálózati átjáró IP-konfigurációit.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

A virtuális hálózati átjáróhoz használt nyilvános IP-címek listájának beolvasása. Ha a virtuális hálózati átjáró aktív-aktív, két nyilvános IP-címet fog látni.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Törölje a nyilvános IP-címeket. A rendszer kérheti, hogy erősítse meg a nyilvános IP-cím törlését.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. törölje az átjáró-alhálózatot, és állítsa be a konfigurációt.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>VPN-átjáró törlése az erőforráscsoport törlésével

Ha nem érdekli az erőforráscsoport erőforrásainak megtartása, és csak azt szeretné kezdeni, törölheti a teljes erőforráscsoportot. Ez egy gyors módszer, amellyel mindent eltávolíthat. A következő lépések csak a Resource Manager-alapú üzemi modellre vonatkoznak.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. az előfizetésben szereplő összes erőforráscsoport listájának beolvasása.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Keresse meg a törölni kívánt erőforráscsoportot.

Keresse meg a törölni kívánt erőforráscsoportot, és tekintse meg az erőforráscsoport erőforrásainak listáját. A példában az erőforráscsoport neve RG1. Módosítsa úgy a példát, hogy lekérje az összes erőforrás listáját.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. Ellenőrizze a listában szereplő erőforrásokat.

A lista visszaadásakor ellenőrizze, hogy szeretné-e törölni az erőforráscsoport összes erőforrását, valamint magát az erőforráscsoportot. Ha meg szeretné őrizni az erőforráscsoport egyes erőforrásait, a cikk korábbi szakaszaiban ismertetett lépések segítségével törölje az átjárót.

### <a name="4-delete-the-resource-group-and-resources"></a>4. törölje az erőforráscsoportot és az erőforrásokat.

Az erőforráscsoport és az erőforráscsoport összes erőforrásának törléséhez módosítsa a példát, és futtassa a parancsot.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. vizsgálja meg az állapotot.

Némi időbe telik, amíg az Azure törölni kívánja az összes erőforrást. Az erőforráscsoport állapotát a parancsmag használatával tekintheti meg.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

A visszaadott eredmény a következőt jeleníti meg: "sikeres".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
