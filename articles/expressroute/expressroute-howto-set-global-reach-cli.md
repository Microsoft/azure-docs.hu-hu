---
title: 'Azure-ExpressRoute: ExpressRoute konfigurálása Global Reach: parancssori felület'
description: Megtudhatja, hogyan kapcsolhatja össze a ExpressRoute-áramköröket, hogy magánhálózat legyen a helyszíni hálózatok között, és hogyan engedélyezheti Global Reach az Azure CLI használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55f1e4c030dc0e7c8d0011dcff0cc1c9f8fec06e
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260231"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>ExpressRoute-Global Reach konfigurálása az Azure CLI használatával

Ez a cikk segítséget nyújt az Azure ExpressRoute Global Reach konfigurálásához az Azure CLI használatával. További információ: [ExpressRoute Global Reach](expressroute-global-reach.md).
 
A konfigurálás megkezdése előtt végezze el a következő követelményeket:

* Telepítse az Azure CLI legújabb verzióját. További információk: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).
* Ismerje meg a ExpressRoute Circuit – kiépítési [munkafolyamatokat](expressroute-workflows.md).
* Győződjön meg arról, hogy a ExpressRoute-áramkör kiépített állapotban van.
* Győződjön meg arról, hogy az Azure-beli privát kapcsolatok konfigurálva vannak a ExpressRoute-áramkörökben.  

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

A konfigurálás megkezdéséhez jelentkezzen be az Azure-fiókjába. A következő parancs megnyitja az alapértelmezett böngészőt, és felszólítja az Azure-fiók bejelentkezési hitelesítő adatainak megadására:  

```azurecli
az login
```

Ha több Azure-előfizetéssel rendelkezik, ellenőrizze a fiók előfizetéseit:

```azurecli
az account list
```

A használni kívánt előfizetés meghatározása:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>A ExpressRoute-áramkörök azonosítása a konfigurációhoz

A ExpressRoute Global Reach bármely két ExpressRoute-áramkör között engedélyezhető. Az áramköröket a támogatott országokban/régiókban kell megadni, és különböző, egymástól eltérő helyeken hozták létre. Ha az előfizetés mindkét áramkört birtokolja, akkor a konfiguráció futtatásához bármelyik áramkört kiválaszthatja. Ha azonban a két áramkör különböző Azure-előfizetésekben található, létre kell hoznia egy engedélyezési kulcsot az egyik áramkörből. Az első áramkörből generált engedélyezési kulcs használatával engedélyezheti Global Reach a második áramkörön.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>A helyszíni hálózatok közötti kapcsolat engedélyezése

Ha a parancsot a kapcsolat engedélyezéséhez futtatja, jegyezze fel a következő követelményeket a paraméterek értékeire:

* a *társ-áramkörnek* a teljes erőforrás-azonosítónak kell lennie. Például:

  > /Subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering

* a címnek "/29" IPv4 *-* alhálózatnak kell lennie (például "10.0.0.0/29"). Ebben az alhálózatban az IP-címeket használjuk a két ExpressRoute-áramkör közötti kapcsolat létesítéséhez. Ebben az alhálózatban nem használhat címeket az Azure-beli virtuális hálózatokban vagy a helyszíni hálózatokban.

A két ExpressRoute-áramkör összekapcsolásához futtassa a következő CLI-parancsot:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

A CLI kimenete a következőképpen néz ki:

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Ha ez a művelet befejeződik, a két ExpressRoute-áramkörön keresztül mindkét oldalon kapcsolatot fog létesíteni a helyszíni hálózatok között.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>A ExpressRoute-áramkörök közötti kapcsolat engedélyezése különböző Azure-előfizetésekben

Ha a két áramkör nem ugyanabban az Azure-előfizetésben található, akkor engedélyre van szüksége. A következő konfigurációban a 2. áramkör előfizetésében létrehozhatja az engedélyt. Ezután adja át az engedélyezési kulcsot az 1. áramkörnek.

1. Engedélyezési kulcs létrehozása:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   A CLI kimenete a következőképpen néz ki:

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Jegyezze fel az erőforrás-azonosítót és a 2. áramkör engedélyezési kulcsát is.

1. Futtassa a következő parancsot az 1. áramkörön, átadva a Circuit 2 erőforrás-AZONOSÍTÓját és engedélyezési kulcsát:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Ha ez a művelet befejeződik, a két ExpressRoute-áramkörön keresztül mindkét oldalon kapcsolatot fog létesíteni a helyszíni hálózatok között.

## <a name="get-and-verify-the-configuration"></a>A konfiguráció beolvasása és ellenőrzése

A következő paranccsal ellenőrizheti a konfigurációt azon az áramkörön, amelyen a konfiguráció létrejött (1. áramkör az előző példában):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

A CLI-kimenetben megjelenik a *CircuitConnectionStatus*. Megtudhatja, hogy létrejött-e a kapcsolat a két áramkör között ("csatlakoztatva") vagy nincs-e létrehozva ("leválasztva"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>A helyszíni hálózatok közötti kapcsolat letiltása

A kapcsolat letiltásához futtassa a következő parancsot a konfigurációt tartalmazó áramkörön (1. áramkör a korábbi példában).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Az ```show``` állapot ellenőrzéséhez használja az parancsot.

Ha ez a művelet befejeződik, már nem lesz kapcsolata a helyszíni hálózatok között a ExpressRoute-áramköröken keresztül.

## <a name="next-steps"></a>Következő lépések

* [További információ a ExpressRoute Global Reach](expressroute-global-reach.md)
* [ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [ExpressRoute-áramkör összekapcsolása egy virtuális hálózattal](expressroute-howto-linkvnet-arm.md)
