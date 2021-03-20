---
title: Privát végpontok hálózati házirendjeinek letiltása az Azure-ban
description: Megtudhatja, hogyan tilthatja le a privát végpontok hálózati házirendjeit.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: a4c04033f3fb5ff523f0d80bb6c978955f4c9ec2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84737512"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Hálózati házirendek letiltása privát végpontokhoz

A hálózati házirendek, például a hálózati biztonsági csoportok (NSG) nem támogatottak privát végpontok esetén. Ahhoz, hogy egy privát végpontot helyezzen üzembe egy adott alhálózaton, az adott alhálózaton explicit letiltási beállításra van szükség. Ez a beállítás csak a privát végpont esetében alkalmazható. Az alhálózatban található egyéb erőforrásokhoz a hozzáférés a hálózati biztonsági csoportok (NSG) biztonsági szabályok definíciója alapján van szabályozva. 
 
Ha a portál használatával hoz létre privát végpontot, a rendszer automatikusan letiltja ezt a beállítást a létrehozási folyamat részeként. A más ügyfelekkel történő üzembe helyezéshez további lépésre van szükség a beállítás módosításához. A beállítást letilthatja a Cloud Shell használatával a Azure Portal vagy Azure PowerShell helyi telepítése, az Azure CLI vagy a Azure Resource Manager sablonok használatával.  
 
Az alábbi példák azt írják le, hogyan lehet letiltani a `PrivateEndpointNetworkPolicies` *myVirtualNetwork* nevű virtuális hálózatot egy *myResourceGroup* nevű erőforráscsoport által üzemeltetett *alapértelmezett* alhálózattal.

## <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ez a szakasz azt ismerteti, hogyan lehet letiltani az alhálózat magánhálózati végpont-házirendjeit a Azure PowerShell használatával.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Ez a szakasz azt ismerteti, hogyan lehet letiltani az alhálózati végpont-házirendeket az Azure CLI-vel.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>Sablon használata
Ez a szakasz azt ismerteti, hogyan lehet letiltani az alhálózati végpont-házirendeket Azure Resource Manager sablon használatával.
```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Következő lépések
- További információ az [Azure Private-végpontról](private-endpoint-overview.md)
 
