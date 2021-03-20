---
title: Azure ExpressRoute nyilvános társának létrehozása és kezelése
description: A nyilvános Azure-alapú társ-kezelés ismertetése és kezelése
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: duau
ms.openlocfilehash: 477145619e1b4d8b41c422389b57a46615597478
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202548"
---
# <a name="create-and-manage-expressroute-public-peering"></a>ExpressRoute nyilvános társának létrehozása és kezelése

> [!div class="op_single_selector"]
> * [Cikk – nyilvános társítás](about-public-peering.md)
> * [Videó – nyilvános peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Cikk – Microsoft-társ](expressroute-circuit-peerings.md#microsoftpeering)
>

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet nyilvános társ-útválasztási konfigurációt egy ExpressRoute-áramkörhöz. A társítások állapotát, frissítését vagy törlését és megszüntetését is megtekintheti. Ez a cikk olyan Resource Manager-áramkörökre vonatkozik, amelyeket a rendszer a nyilvános társítás érvénytelenítése előtt hozott létre. Ha már rendelkezik egy korábban már meglévő, a nyilvános társítások levonása előtt létrehozott munkaterülettel, akkor a [Azure PowerShell](#powershell), az [Azure CLI](#cli)és a [Azure Portal](#portal)használatával felügyelheti/konfigurálhatja a nyilvános társításokat.

>[!NOTE]
>A nyilvános társítás elavult. Az új ExpressRoute-áramkörökben nem hozhatók létre nyilvános társak. Ha egy új ExpressRoute-áramkört használ, használja a [Microsoft-partnert](expressroute-circuit-peerings.md#microsoftpeering) az Azure-szolgáltatásokhoz.
>

## <a name="connectivity"></a>Kapcsolat

A kapcsolat mindig a WAN-ból Microsoft Azure szolgáltatások felé kezdeményezhető. Microsoft Azure szolgáltatás nem tud kapcsolatot létesíteni a hálózattal ezen az útválasztási tartományon keresztül. Ha a ExpressRoute-áramkör engedélyezve van az Azure-beli nyilvános kapcsolatok esetében, az [Azure-ban használt nyilvános IP-tartományokat](../virtual-network/public-ip-addresses.md#public-ip-addresses) az áramkörön keresztül érheti el.

A nyilvános társítás engedélyezése után a legtöbb Azure-szolgáltatáshoz csatlakozhat. Nem engedélyezzük szelektíven olyan szolgáltatások kivételezését, amelyekhez útvonalakat hirdetünk.

* Az Azure Storage, az SQL Database és a websites szolgáltatás nyilvános IP-címeken elérhető.
* A nyilvános partneri útválasztási tartományon keresztül privát módon csatlakozhat a nyilvános IP-címeken üzemeltetett szolgáltatásokhoz, beleértve a Cloud Services VIP-címét is.
* A nyilvános kiszolgáló tartományát a DMZ-hez is összekapcsolhatja, és az interneten keresztüli kapcsolódás nélkül csatlakozhat az összes Azure-szolgáltatáshoz a nyilvános IP-címein.

## <a name="services"></a><a name="services"></a>Szolgáltatások

Ez a szakasz a nyilvános társításon keresztül elérhető szolgáltatásokat mutatja be. Mivel a nyilvános társítás elavult, nem lehet új vagy további szolgáltatásokat hozzáadni a nyilvános társak számára. Ha nyilvános társítást használ, és a használni kívánt szolgáltatás csak Microsoft-partneri kapcsolaton keresztül támogatott, akkor a Microsoft-társra kell váltania. A támogatott szolgáltatások listáját lásd: [Microsoft-partnerek](expressroute-faqs.md#microsoft-peering) .

**Támogatott**

* Power BI
* Az Azure-szolgáltatások többsége támogatott. Jelölje be a közvetlenül a támogatás ellenőrzéséhez használni kívánt szolgáltatást.

**Nem támogatott:**
  * Tartalomkézbesítési hálózat (CDN)
  * Azure Front Door
  * Multi-Factor Authentication kiszolgáló (örökölt)
  * Traffic Manager

Egy adott szolgáltatás rendelkezésre állásának ellenőrzéséhez ellenőrizheti a szolgáltatás dokumentációját, és ellenőrizheti, hogy van-e fenntartott tartomány közzétéve az adott szolgáltatás számára. Ezután megkeresheti a cél szolgáltatás IP-tartományait, és összehasonlíthatja az [Azure IP-tartományok és szolgáltatások címkék – nyilvános felhő XML-fájljában](https://www.microsoft.com/download/details.aspx?id=56519)felsorolt tartományokkal. Azt is megteheti, hogy megnyit egy támogatási jegyet a kérdéses szolgáltatáshoz a tisztázás érdekében.

## <a name="peering-comparison"></a><a name="compare"></a>Egyenrangú összehasonlítás

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Az Azure nyilvános társítása 1 NAT IP-címmel rendelkezik, amely az egyes BGP-munkamenetekhez van társítva. Ha több mint 2 NAT IP-címet szeretne, lépjen a Microsoft-partneri hálózatra. A Microsoft-partnerek lehetővé teszik saját NAT-foglalások konfigurálását, valamint az útválasztási szűrők használatát a szelektív előtaggal kapcsolatos hirdetményekhez. További információ: [áttérés a Microsoft-partneri](./how-to-move-peering.md)hálózatra.
>

## <a name="custom-route-filters"></a>Egyéni útválasztási szűrők

A hálózaton belül egyéni útválasztási szűrőket is meghatározhat, hogy csak a szükséges útvonalakat használja. Az útválasztási konfigurációval kapcsolatos részletes információkért tekintse meg az [Útválasztás](expressroute-routing.md) lapot.

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure PowerShell lépések


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Mivel a nyilvános társítás elavult, nem lehet új ExpressRoute-áramkörön konfigurálni a nyilvános társítást.

1. Győződjön meg arról, hogy rendelkezik egy kiépített és engedélyezett ExpressRoute-áramkörrel. Használja a következő példát:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A válasz a következő példához hasonló:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. A folytatás előtt győződjön meg arról, hogy rendelkezik a következő információkkal.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Nem kötelező:
   * Egy MD5-kivonat, ha használni kívánja.

   A következő példa futtatásával konfigurálhatja az Azure-beli nyilvános társítást az áramkörhöz

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Ha MD5-kivonatot szeretne használni, használja a következő példát:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Az Azure-beli nyilvános partneri kapcsolat részleteinek beszerzése

A konfigurációs adatokat a következő parancsmaggal kérheti le:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure nyilvános társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármely részét frissítheti a következő példán keresztül. Ebben a példában az áramkör VLAN-azonosítója 200 és 600 között frissül.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure nyilvános társviszony-létesítés törlése

A következő példa futtatásával távolíthatja el a társ-konfigurációt:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Azure CLI-lépések


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Ellenőrizze a ExpressRoute áramkört, és győződjön meg arról, hogy az üzembe helyezése és engedélyezése is megtörténik. Használja a következő példát:

   ```azurecli-interactive
   az network express-route list
   ```

   A válasz a következő példához hasonló:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. A folytatás előtt győződjön meg arról, hogy rendelkezik a következő információkkal.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Nem **kötelező –** Egy MD5-kivonat, ha úgy dönt, hogy használ egyet.

   A következő példa futtatásával konfigurálhatja az Azure-beli nyilvános társítást az áramkörhöz:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Ha MD5-kivonatot szeretne használni, használja a következő példát:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Az AS-számot mindenképp társviszony-létesítési ASN-ként, és ne ügyfél ASN-ként adja meg.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Azure nyilvános társviszony-létesítés részleteinek megtekintése

A konfigurációs adatokat a következő példa használatával szerezheti be:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

A kimenet a következő példához hasonló:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure nyilvános társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármely részét frissítheti a következő példán keresztül. Ebben a példában az áramkör VLAN-azonosítója 200 és 600 között frissül.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure nyilvános társviszony-létesítés törlése

A következő példa futtatásával távolíthatja el a társ-konfigurációt:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure Portal lépések

A társítás konfigurálásához használja a cikkben található PowerShell vagy parancssori felület lépéseit. A társítások kezeléséhez az alábbi szakaszt használhatja. Ezek a lépések a [Microsoft-partnerek a portálon](expressroute-howto-routing-portal-resource-manager.md#msft)való kezeléséhez hasonló módon jelennek meg.

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Azure nyilvános társviszony-létesítés részleteinek megtekintése

Tekintse meg az Azure-beli nyilvános társak tulajdonságait a portálon való társítás kiválasztásával.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Azure nyilvános társviszony-létesítés konfigurációjának frissítése

Válassza ki a társításhoz tartozó sort, majd módosítsa a társítási tulajdonságokat.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Azure nyilvános társviszony-létesítés törlése

A Törlés ikonra kattintva távolítsa el a társ-konfigurációt.

## <a name="next-steps"></a>Következő lépések

A következő lépés [a virtuális hálózat összekapcsolása egy ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md).

* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).