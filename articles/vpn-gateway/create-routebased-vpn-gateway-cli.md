---
title: 'Route-alapú Azure-VPN Gateway létrehozása: parancssori felület'
description: Az Azure CLI használatával gyorsan létrehozhat egy Route-alapú Azure VPN-átjárót a helyszíni hálózathoz való VPN-kapcsolathoz vagy a virtuális hálózatok összekapcsolásához.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2bbd7e39ee65ba304ec62697b6fcc77bea133b41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94737217"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Route-alapú VPN-átjáró létrehozása a parancssori felület használatával

Ez a cikk segítséget nyújt egy Route-alapú Azure VPN-átjáró gyors létrehozásához az Azure CLI használatával. VPN-átjárót a helyszíni hálózathoz való VPN-kapcsolat létrehozásakor használ a rendszer. VPN-átjárót is használhat a virtuális hálózatok összekapcsolásához.

A cikkben szereplő lépések egy VNet, egy alhálózatot, egy átjáró-alhálózatot és egy Route-alapú VPN-átjárót (virtuális hálózati átjárót) hoznak létre. A virtuális hálózati átjárók létrehozása akár 45 percet is igénybe vehet. Miután az átjáró létrehozása befejeződött, létrehozhat kapcsolatokat. Ezeknek a lépéseknek Azure-előfizetésre van szükségük.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group) paranccsal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az az [Network vnet Create](/cli/azure/network/vnet) paranccsal. A következő példában létrehozunk egy **VNet1** nevű virtuális hálózatot a **EastUS** helyen:

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Átjáróalhálózat hozzáadása

Az átjáró-alhálózat tartalmazza azokat a fenntartott IP-címeket, amelyeket a Virtual Network Gateway Services használ. Átjáró-alhálózat hozzáadásához használja az alábbi példákat:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Nyilvános IP-cím kérése

A VPN-átjárónak dinamikusan lefoglalt nyilvános IP-címmel kell rendelkeznie. A nyilvános IP-címet a rendszer a virtuális hálózathoz létrehozott VPN-átjáróhoz rendeli. Nyilvános IP-cím igényléséhez használja a következő példát:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>A VPN-átjáró létrehozása

Hozza létre a VPN Gateway-t az [az network vnet-gateway create](/cli/azure/group) paranccsal.

Ha ezt a parancsot a `--no-wait` paraméterrel futtatja, nem jelenik meg visszajelzés vagy kimenet. A `--no-wait` paraméter lehetővé teszi, hogy az átjáró a háttérben legyen létrehozva. Ez nem jelenti azt, hogy a VPN-átjárót azonnal létrehozták.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

A VPN-átjáró létrehozása több mint 45 percet is igénybe vehet.

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>A VPN-átjáró megtekintése

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

A válasz a következőhöz hasonlóan néz ki:

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>A nyilvános IP-cím megtekintése

Az átjáróhoz rendelt nyilvános IP-cím megtekintéséhez használja az alábbi példát:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Az **IP** -cím mezőhöz társított érték a VPN-átjáró nyilvános IP-címe.

Példaválasz:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a létrehozott erőforrásokra, az az [Group delete](/cli/azure/group) paranccsal törölheti az erőforráscsoportot. Ez a parancs törli az erőforráscsoportot és a benne lévő összes erőforrást.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Következő lépések

Miután az átjáró elkészült, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet között. Vagy hozzon létre kapcsolatot a virtuális hálózat és a helyszíni hely között.

> [!div class="nextstepaction"]
> [Helyek közötti kapcsolat létrehozása](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Pont–hely kapcsolat létrehozása](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Kapcsolatok létrehozása másik VNet](vpn-gateway-vnet-vnet-rm-ps.md)
