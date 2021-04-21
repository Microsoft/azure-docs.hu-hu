---
title: 'PowerShell: Privát végpont üzembe helyezése webalkalmazáshoz a PowerShell használatával'
description: Megtudhatja, hogyan helyezhet üzembe privát végpontot a webalkalmazáshoz a PowerShell használatával
author: ericgre
ms.assetid: e1cc08d5-91cf-49d7-8d0a-c0e7bd2046ac
ms.topic: sample
ms.date: 07/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bc4fb20521c1d5d1f61fb411f9e6688e422d7ae6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832985"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-powershell"></a>Alkalmazás létrehozása App Service és privát végpont üzembe helyezése a PowerShell használatával

Ez a példaszkprogram létrehoz egy alkalmazást App Service a kapcsolódó erőforrásokkal, majd üzembe helyez egy privát végpontot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]


## <a name="sample-script"></a>Példaszkript

```azurepowershell-interactive
# Parameters
$sitename="mywebapp-$(Get-Random)"
$appserviceplanname="ASP-"+$sitename
$resourcegroupname="RG-"+$sitename
$VNetname="VNet-$(Get-Random)"
$location="francecentral"
$privateendpointsubnetname = "privateEndpointSubnet"

# Create a resource group.
New-AzResourceGroup -Name $resourcegroupname -Location $location

# Create an App Service plan in PremiumV2 tier.
$asp = New-AzAppServicePlan -Name $appserviceplanname `
-Location $location `
-ResourceGroupName $resourcegroupname `
-Tier PremiumV2 `
-NumberofWorkers 1 `
-WorkerSize Small

# Create a web app.
$webApp = New-AzWebApp -Name $sitename `
-Location $location `
-AppServicePlan $appserviceplanname `
-ResourceGroupName $resourcegroupname

# Create a Virtual Network with two subnets
$integrationsubnet = New-AzVirtualNetworkSubnetConfig -Name "integrationSubnet" `
-AddressPrefix "10.8.1.0/24"

$privateendpointsubnet = New-AzVirtualNetworkSubnetConfig -Name $privateendpointsubnetname `
-AddressPrefix "10.8.2.0/24" `
-PrivateEndpointNetworkPoliciesFlag Disabled

$virtualNetwork = New-AzVirtualNetwork -Name $VNetname `
-ResourceGroupName $resourcegroupname `
-Location $location -AddressPrefix "10.8.0.0/16" `
-Subnet $integrationsubnet,$privateendpointsubnet

# Configure the Private Endpoint
$privateEndPointConnection = New-AzPrivateLinkServiceConnection -Name "myPrivateEndpointconnection" `
-PrivateLinkServiceID $webApp.Id `
-GroupId sites

$subnet = $virtualNetwork | select -ExpandProperty subnets | Where-Object {$_.Name -eq $privateendpointsubnetname}

$privateEndpoint = New-AzPrivateEndpoint -Name "myPrivateEndpoint" `
-ResourceGroupName $resourcegroupname `
-Location $location `
-Subnet $subnet `
-PrivateLinkServiceConnection $privateEndPointConnection

# Configure the Private DNS zone
$dnsZone = New-AzPrivateDnsZone -Name "privatelink.azurewebsites.net" `
-ResourceGroupName $resourcegroupname

$dnsLink = New-AzPrivateDnsVirtualNetworkLink -Name "myDNSLink" `
-ResourceGroupName $resourcegroupname `
-ZoneName "privatelink.azurewebsites.net" `
-VirtualNetworkId $virtualNetwork.Id

$dnsConfig = New-AzPrivateDnsZoneConfig -Name "privatelink.azurewebsites.net" `
-PrivateDnsZoneId $dnsZone.ResourceId

$dnsZoneGroup = New-AzPrivateDnsZoneGroup -Name "myZoneGroup" `
-ResourceGroupName $resourcegroupname `
-PrivateEndpointName $privateEndpoint.Name `
-PrivateDnsZoneConfig $dnsConfig
```


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport, a webalkalmazás és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Létrehoz egy App Service-csomagot. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Webalkalmazást hoz létre. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Létrehoz egy virtuális hálózat alhálózati konfigurációját. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre. |
| [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection) | Létrehoz egy privát kapcsolati szolgáltatás kapcsolatkonfigurációt. |
| [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) | Létrehoz egy privát végpontot. |
| [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone) | Létrehoz egy új privát DNS-zónát. |
| [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink) | Létrehoz egy új privát DNS virtuális hálózati kapcsolatot. |
| [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig) | Létrehozza a privát DNS-zónacsoport DNS-zónakonfigurációját. |
| [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup) | Létrehoz egy privát DNS-zónacsoportot a megadott privát végponton. |

## <a name="next-steps"></a>Következő lépések

- Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).
- További Azure PowerShell példákat Azure App Service Web Apps a mintaminták között [Azure PowerShell talál.](../samples-powershell.md)
