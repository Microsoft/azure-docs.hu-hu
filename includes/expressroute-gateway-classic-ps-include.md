---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0c05eb88df27ba9e71c0f61bad75457d893a7ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95558890"
---
> [!NOTE]
> Ezek a példák nem vonatkoznak a S2S/ExpressRoute egyazon konfigurációkra.
> Az átjárók egyidejű konfigurálásával kapcsolatos további információkért lásd: egyidejű [Kapcsolatok konfigurálása.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Átjáró hozzáadása

Ha a klasszikus erőforrás-modell használatával ad hozzá átjárót egy virtuális hálózathoz, a hálózati konfigurációs fájlt közvetlenül az átjáró létrehozása előtt kell módosítania. Az alábbi példákban szereplő értékeknek jelen kell lenniük a fájlban az átjáró létrehozásához. Ha a virtuális hálózata korábban egy átjáróhoz volt társítva, akkor ezen értékek némelyike már jelen van. Módosítsa a fájlt úgy, hogy az az alábbi értékeket tükrözze.

### <a name="download-the-network-configuration-file"></a>A hálózati konfigurációs fájl letöltése

1. Töltse le a hálózati konfigurációs fájlt a [hálózati konfigurációs fájl](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) lépései című cikkben ismertetett lépések segítségével. Nyissa meg a fájlt egy szövegszerkesztő használatával.
2. Adjon hozzá egy helyi hálózati helyet a fájlhoz. Bármilyen érvényes címzési előtagot használhat. A VPN-átjáróhoz bármilyen érvényes IP-címet adhat hozzá. Az ebben a szakaszban szereplő ExpressRoute-műveletekhez nem használhatók, de a fájl érvényesítéséhez szükségesek. A példában a "fiók1" a hely neve. Használhat más nevet is, de ügyeljen arra, hogy ugyanazt az értéket használja a fájl átjáró szakaszában.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Navigáljon a VirtualNetworkSites, és módosítsa a mezőket.

   * Ellenőrizze, hogy az átjáró-alhálózat létezik-e a virtuális hálózathoz. Ha nem, akkor egyszerre adhat hozzá egyet. A névnek "GatewaySubnet" értéknek kell lennie.
   * Ellenőrizze, hogy létezik-e a fájl átjáró szakasza. Ha nem, adja hozzá. Erre azért van szükség, hogy a virtuális hálózatot a helyi hálózati hellyel társítsa (amely azt a hálózatot jelöli, amelyhez csatlakozik).
   * Ellenőrizze, hogy a kapcsolattípus = dedikált-e. Ez a ExpressRoute-kapcsolatokhoz szükséges.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Mentse a fájlt, és töltse fel az Azure-ba.

### <a name="create-the-gateway"></a>Az átjáró létrehozása

Átjáró létrehozásához használja az alábbi parancsot. Helyettesítse be a saját értékeit.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Az átjáró létrehozásának ellenőrzése

Az alábbi parancs használatával ellenőrizheti, hogy létrejött-e az átjáró. Ez a parancs lekéri az átjáró AZONOSÍTÓját is, amelyhez más műveletekhez szüksége van.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Átjáró átméretezése

Számos [átjáró-SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)van. A következő parancs használatával bármikor módosíthatja az átjáró SKU-jának változását.

> [!IMPORTANT]
> Ez a parancs nem működik a UltraPerformance-átjáró esetében. Ha módosítani szeretné az átjárót egy UltraPerformance-átjáróra, először távolítsa el a meglévő ExpressRoute-átjárót, majd hozzon létre egy új UltraPerformance-átjárót. Ha vissza szeretné állítani az átjárót egy UltraPerformance-átjáróról, először távolítsa el az UltraPerformance-átjárót, majd hozzon létre egy új átjárót.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Átjáró eltávolítása

Átjáró eltávolításához használja az alábbi parancsot.

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```