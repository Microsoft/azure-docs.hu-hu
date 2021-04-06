---
title: 'Azure ExpressRoute: áramkör-társítások alaphelyzetbe állítása'
description: Megtudhatja, hogyan engedélyezheti és tilthatja le az Azure ExpressRoute áramköri szolgáltatásait Azure PowerShell használatával.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: 0bde96ae5f4a9aff6f4a16a4f1544d9b39e5cb66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97559573"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute-áramköri társítások alaphelyzetbe állítása

Ez a cikk azt ismerteti, hogyan lehet engedélyezni és letiltani egy ExpressRoute-áramkör társításait a PowerShell használatával. A társítások alapértelmezés szerint engedélyezve vannak a létrehozásuk során. Ha letilt egy társítást, a rendszer leállítja a BGP-munkamenetet a ExpressRoute áramkör elsődleges és másodlagos kapcsolatán. A Microsofthoz való kapcsolódás elvész. A társítás engedélyezésekor a BGP-munkamenet a ExpressRoute-áramkör elsődleges és másodlagos kapcsolatán is létrejön. A rendszer visszaállítja a kapcsolatot a Microsofttal ehhez a társhoz. Engedélyezheti és letilthatja a Microsoft-és az Azure-beli privát kapcsolatok társítását a ExpressRoute áramkörtől függetlenül.

A ExpressRoute-társítások alaphelyzetbe állításához a következő két helyzetben hasznos lehet:
* Ha tesztelni szeretné a vész-helyreállítási tervét és megvalósítását. Tegyük fel, hogy két ExpressRoute-áramkörrel rendelkezik. Letilthatja a társításokat egy áramkörön, és kényszerítheti a hálózati forgalmat, hogy átadja a feladatátvételt a másik áramkörnek.
* Engedélyezze a kétirányú továbbítási észlelést (BFD) a ExpressRoute-áramkör Azure-beli privát vagy Microsoft-kapcsolataiban. A BFD alapértelmezés szerint engedélyezve van az Azure-beli privát kapcsolatok esetében, ha a ExpressRoute-áramkört a 2018. augusztus 1. és a Microsoft-partnerek számára a 2020. január 10. után hozta létre. Ha az áramkör a felsorolt dátum előtt lett létrehozva, a BFD engedélyezéséhez alaphelyzetbe kell állítania a társítást. 

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Társítás alaphelyzetbe állítása

1. Ha helyileg futtatja a PowerShellt, nyissa meg emelt szintű jogosultságokkal a PowerShell-konzolt, és kapcsolódjon a fiókjához. A következő példa segít a kapcsolódásban:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Válassza ki a használni kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Futtassa az alábbi parancsokat a ExpressRoute áramkör beolvasásához.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Azonosítsa a letiltani vagy engedélyezni kívánt társítást. A *társak* tömb. A következő példában a [0] társak az Azure privát társ-és társai [1] Microsoft-társak.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Futtassa a következő parancsokat a társítás állapotának módosításához.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   A társításnak beállított állapotban kell lennie. 

## <a name="next-steps"></a>Következő lépések
Ha segítségre van szüksége egy ExpressRoute-probléma elhárításához, tekintse meg a következő cikkeket:
* [Az ExpressRoute-kapcsolat ellenőrzése](expressroute-troubleshooting-expressroute-overview.md)
* [A hálózati teljesítmény hibaelhárítása](expressroute-troubleshooting-network-performance.md)
