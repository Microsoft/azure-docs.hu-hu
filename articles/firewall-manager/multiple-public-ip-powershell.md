---
title: Azure Firewall üzembe helyezése több nyilvános IP-címmel a Azure PowerShell használatával
description: Tűzfal üzembe helyezése több nyilvános IP-címmel a virtuális központ védetté tételéhez
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 652c7cbfbe63ef2ae9a0d54e05407152ea300f1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87006993"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>Azure Firewall üzembe helyezése több nyilvános IP-címmel

Ha Azure Firewall használatával szeretne védelemmel ellátni egy virtuális központot, a tűzfalat Azure PowerShell használatával több nyilvános IP-címmel is telepítheti.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

A következő Azure PowerShell példa használatával helyezzen üzembe egy Azure Firewall több nyilvános IP-címmel a virtuális központ védetté tételéhez.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="update-a-public-ip-address"></a>Nyilvános IP-cím frissítése

A Azure PowerShell egy Azure Firewall nyilvános IP-címének frissítésére használható. Az alábbi példa egy nyilvános IP-címet töröl egy tűzfallal. Három nyilvános IP-címmel kezdődik.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>Következő lépések

[Mi az a biztonságos virtuális központ?](secured-virtual-hub.md)