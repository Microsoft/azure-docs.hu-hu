---
title: 'Virtuális hálózati átjáró törlése: klasszikus Azure'
description: Virtuális hálózati átjáró törlése a PowerShell használatával a klasszikus üzembehelyezési modellben.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 39585a68c5cddc50cd04e82caca71209270f7b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874118"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Virtuális hálózati átjáró törlése a PowerShell (klasszikus) használatával

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klasszikus – PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Ez a cikk segítséget nyújt a VPN-átjárók törlésében a klasszikus üzemi modellben a PowerShell használatával. A virtuális hálózati átjáró törlése után módosítsa a hálózati konfigurációs fájlt a már nem használt elemek eltávolításához.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>1. lépés: Kapcsolódás az Azure-hoz

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Telepítse a legújabb PowerShell-parancsmagokat.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. kapcsolódjon az Azure-fiókhoz.

Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

1. Nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal.
2. Csatlakozás a fiókhoz. A következő példa segít a kapcsolódásban:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>2. lépés: a hálózati konfigurációs fájl exportálása és megtekintése

Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ezt a fájlt a jelenlegi konfigurációs információk megtekintésére, valamint a hálózati konfiguráció módosítására is használhatja.

Ebben a példában a hálózati konfigurációs fájlt a C:\AzureNet helyre exportálja.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Nyissa meg a fájlt egy szövegszerkesztővel, és tekintse meg a klasszikus VNet nevét. Amikor VNet hoz létre a Azure Portalban, az Azure által használt teljes név nem látható a portálon. Például egy "ClassicVNet1" nevű VNet a Azure Portalban, a hálózati konfigurációs fájlban sokkal több nevet kaphat. A név a következőhöz hasonló lehet: "Group ClassicRG1 ClassicVNet1". A virtuális hálózatok nevei **"VirtualNetworkSite Name ="** néven szerepelnek. A PowerShell-parancsmagok futtatásakor használja a hálózati konfigurációs fájl nevét.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>3. lépés: a virtuális hálózati átjáró törlése

Ha töröl egy virtuális hálózati átjárót, a rendszer az átjárón keresztül az VNet létesített összes kapcsolatot leválasztja. Ha P2S-ügyfelek csatlakoznak a VNet, figyelmeztetés nélkül le lesznek választva.

Ez a példa törli a virtuális hálózati átjárót. Győződjön meg arról, hogy a virtuális hálózat teljes nevét használja a hálózati konfigurációs fájlból.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Ha a művelet sikeres, a visszatérés a következőket mutatja:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>4. lépés: a hálózati konfigurációs fájl módosítása

Ha töröl egy virtuális hálózati átjárót, a parancsmag nem módosítja a hálózati konfigurációs fájlt. A már nem használt elemek eltávolításához módosítania kell a fájlt. A következő fejezetek segítséget nyújtanak a letöltött hálózati konfigurációs fájl módosításához.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Helyi hálózati telephelyek hivatkozásai

A hely hivatkozási adatainak eltávolításához végezze el a konfigurációs módosításokat a **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. A helyi hely hivatkozásának eltávolítása elindítja az Azure-t egy alagút törléséhez. A létrehozott konfigurációtól függően előfordulhat, hogy nem rendelkezik a felsorolt **LocalNetworkSiteRef** .

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Példa:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="local-network-sites"></a><a name="lns"></a>Helyi hálózati helyek

Távolítson el minden olyan helyi helyet, amelyet már nem használ. A létrehozott konfigurációtól függően előfordulhat, hogy nem rendelkezik a felsorolt **LocalNetworkSite** .

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

Ebben a példában csak a Site3 távolítjuk el.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="client-addresspool"></a><a name="clientaddresss"></a>Ügyfél-címkészlete

Ha P2S-kapcsolatban állt a VNet, egy **címtérről** fog rendelkezni. Távolítsa el a törölt virtuális hálózati átjárónak megfelelő ügyféloldali címkészlet-készleteket.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Példa:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

Törölje a VNet megfelelő **GatewaySubnet** .

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Példa:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>5. lépés: a hálózati konfigurációs fájl feltöltése

Mentse a módosításokat, és töltse fel a hálózati konfigurációs fájlt az Azure-ba. Győződjön meg arról, hogy a környezetéhez szükség van a fájl elérési útjának módosítására.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Ha a művelet sikeres, a visszatérés az alábbi példához hasonló módon jelenik meg:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
