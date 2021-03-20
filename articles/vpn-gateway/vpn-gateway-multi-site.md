---
title: 'VNet összekötése több webhelyhez a VPN Gateway: klasszikus használatával'
description: Egy VPN Gateway használatával több helyi helyszíni helyet is összekapcsolhat egy klasszikus virtuális hálózathoz.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 168bb9e06c73ec27ec1304813023889c9549b8e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660695"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Helyek közötti kapcsolat hozzáadása egy VNet meglévő VPN Gateway-kapcsolattal (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasszikus)](vpn-gateway-multi-site.md)
>
>

Ez a cikk végigvezeti a PowerShell használatával, hogy helyek közötti (S2S) kapcsolatokat adjon hozzá egy meglévő kapcsolattal rendelkező VPN-átjáróhoz. Ezt a kapcsolattípust gyakran "többhelyes" konfigurációnak nevezzük. A cikkben ismertetett lépések a klasszikus üzembe helyezési modellel létrehozott virtuális hálózatokra érvényesek (más néven a Service Management). Ezek a lépések nem vonatkoznak a ExpressRoute vagy a helyek közötti egyidejű kapcsolati konfigurációkra.

### <a name="deployment-models-and-methods"></a>Üzemi modellek és módszerek

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ezt a táblázatot úgy frissítjük, ahogy új cikkek és további eszközök válnak elérhetővé ehhez a konfigurációhoz. Ha egy cikk elérhetővé válik, közvetlenül a táblából csatoljuk.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Csatlakozás a csatlakoztatásról

Több helyszíni helyet is összekapcsolhat egyetlen virtuális hálózattal. Ez különösen vonzó a hibrid felhőalapú megoldások létrehozásához. A többhelyes kapcsolat Azure-beli virtuális hálózati átjáróhoz való létrehozása hasonló a helyek közötti kapcsolatok létrehozásához. Valójában használhat meglévő Azure VPN Gateway-átjárót is, ha az átjáró dinamikus (Route-alapú).

Ha már rendelkezik egy statikus átjáróval, amely a virtuális hálózathoz csatlakozik, a virtuális hálózat újraépítésének szükségessége nélkül is beállíthatja a dinamikus átjáró típusát. Az útválasztási típus módosítása előtt győződjön meg arról, hogy a helyszíni VPN-átjáró támogatja az Útválasztás-alapú VPN-konfigurációkat.

![többhelyes diagram](./media/vpn-gateway-multi-site/multisite.png "több hely")

## <a name="points-to-consider"></a>Megfontolandó szempontok

**A portálon nem fogja tudni módosítani a virtuális hálózatot.** A portál használata helyett módosítania kell a hálózati konfigurációs fájlt. Ha módosításokat hajt végre a portálon, a rendszer felülírja a virtuális hálózat többhelyes hivatkozásának beállításait.

A többhelyes eljárás befejezése után érdemes a hálózati konfigurációs fájl használatát használni. Ha azonban több ember dolgozik a hálózati konfigurációban, meg kell győződnie arról, hogy mindenki ismeri ezt a korlátozást. Ez nem jelenti azt, hogy egyáltalán nem használhatja a portált. Minden más számára felhasználható, kivéve az adott virtuális hálózat konfigurációjának módosításait.

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt ellenőrizze, hogy rendelkezik-e a következőkkel:

* Kompatibilis VPN-hardver az egyes helyszíni helyekhez. Tekintse át a [Virtual Network kapcsolat VPN-eszközeit](vpn-gateway-about-vpn-devices.md) annak ellenőrzéséhez, hogy a használni kívánt eszköz a kompatibilis-e.
* Az egyes VPN-eszközök kifelé irányuló, nyilvános IPv4 IP-címei. Az IP-cím nem helyezhető el NAT mögött. Ez a követelmény.
* Valaki, aki jártas a VPN-hardver konfigurálásában. A VPN-eszköz konfigurálásának és a munkát végző személynek is alapos ismeretekkel kell rendelkeznie.
* A virtuális hálózathoz használni kívánt IP-címtartományok (ha még nem hozott létre egyet).
* Az egyes helyi hálózati helyek IP-címtartományok, amelyekhez csatlakozni fog. Győződjön meg arról, hogy az összes olyan helyi hálózati telephely IP-címtartományok, amelyhez csatlakozni szeretne, nem fedik át egymást. Ellenkező esetben a portál vagy a REST API el fogja utasítani a feltöltött konfigurációt.<br>Ha például két olyan helyi hálózati hellyel rendelkezik, amelyek egyaránt tartalmazzák a 10.2.3.0/24 IP-címtartományt, és egy 10.2.3.3 rendelkező csomaggal rendelkezik, az Azure nem tudja, hogy melyik helyen szeretné elküldeni a csomagot, mivel a címtartományok átfedésben vannak. Az útválasztási problémák megelőzése érdekében az Azure nem teszi lehetővé, hogy olyan konfigurációs fájlt töltsön fel, amely átfedésben lévő tartományokkal rendelkezik.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. helyek közötti VPN létrehozása
Ha már van egy helyek közötti VPN dinamikus útválasztási átjáróval, nagyszerű! Folytathatja [a virtuális hálózat konfigurációs beállításainak exportálását](#export). Ha nem, tegye a következőket:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Ha már rendelkezik helyek közötti virtuális hálózattal, de statikus (házirend-alapú) útválasztási átjáróval rendelkezik:
1. Módosítsa az átjáró típusát a dinamikus útválasztásra. A többhelyes VPN-hez dinamikus (más néven Route-alapú) útválasztási átjáró szükséges. Az átjáró típusának módosításához először törölnie kell a meglévő átjárót, majd létre kell hoznia egy újat.
2. Konfigurálja az új átjárót, és hozza létre a VPN-alagutat. Útmutatásért lásd: [az SKU és a VPN típusának megadása](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Győződjön meg arról, hogy az útválasztási típust "dinamikus"-ként adja meg.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Ha nem rendelkezik helyek közötti virtuális hálózattal:
1. Hozza létre a helyek közötti virtuális hálózatot a következő utasítások használatával: [hozzon létre egy Virtual Network helyek közötti VPN-kapcsolattal](./vpn-gateway-howto-site-to-site-classic-portal.md).  
2. Konfigurálja a dinamikus útválasztási átjárót a következő utasítások használatával: [VPN Gateway konfigurálása](./vpn-gateway-howto-site-to-site-classic-portal.md). Ügyeljen arra, hogy a **dinamikus útválasztást** válassza az átjáró típusaként.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. a hálózati konfigurációs fájl exportálása

Nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal. A Service Management szolgáltatásra való váltáshoz használja a következő parancsot:

```powershell
azure config mode asm
```

Csatlakozás a fiókhoz. A következő példa segít a kapcsolódásban:

```powershell
Add-AzureAccount
```

Exportálja az Azure hálózati konfigurációs fájlját a következő parancs futtatásával. Ha szükséges, módosíthatja a fájl helyét egy másik helyre való exportáláshoz.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Nyissa meg a hálózati konfigurációs fájlt
Nyissa meg az utolsó lépésben letöltött hálózati konfigurációs fájlt. Bármilyen hasonló XML-szerkesztőt használhat. A fájlnak a következőhöz hasonlóan kell kinéznie:

```xml
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <LocalNetworkSites>
      <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
          <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
      </LocalNetworkSite>
      <LocalNetworkSite name="Site2">
        <AddressSpace>
          <AddressPrefix>10.2.0.0/16</AddressPrefix>
          <AddressPrefix>10.3.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
        <AddressSpace>
          <AddressPrefix>10.20.0.0/16</AddressPrefix>
          <AddressPrefix>10.21.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FE">
            <AddressPrefix>10.20.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BE">
            <AddressPrefix>10.20.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.20.2.0/29</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

## <a name="4-add-multiple-site-references"></a>4. több hely hivatkozásainak hozzáadása
Ha hely-hivatkozási adatokat ad hozzá vagy távolít el, a konfigurációs módosításokat a ConnectionsToLocalNetwork/LocalNetworkSiteRef kell módosítania. Egy új helyi hely hivatkozásának hozzáadása elindítja az Azure-t egy új alagút létrehozásához. Az alábbi példában a hálózati konfiguráció egyhelyes kapcsolathoz van. Ha befejezte a módosításokat, mentse a fájlt.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

További helykapcsolatok hozzáadásához (többhelyes konfiguráció létrehozása) egyszerűen vegyen fel további "LocalNetworkSiteRef" sorokat az alábbi példában látható módon:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. a hálózati konfigurációs fájl importálása
Importálja a hálózati konfigurációs fájlt. Ha ezt a fájlt a módosításokkal együtt importálja, az új alagutak lesznek hozzáadva. Az alagutak a korábban létrehozott dinamikus átjárót fogják használni. A fájl importálásához használhatja a PowerShellt.

## <a name="6-download-keys"></a>6. kulcsok letöltése
Az új alagutak hozzáadása után a "Get-AzureVNetGatewayKey" PowerShell-parancsmag használatával lekérheti az egyes alagutakhoz tartozó IPsec/IKE előmegosztott kulcsokat.

Például:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Ha szeretné, az előmegosztott kulcsok *beszerzéséhez használja az Virtual Network átjáró megosztott kulcsának Beolvasása* REST API is.

## <a name="7-verify-your-connections"></a>7. a kapcsolatok ellenőrzése
Győződjön meg arról, hogy a többhelyes alagút állapota. Az egyes alagutak kulcsainak letöltése után ellenőrizze a kapcsolatokat. A "Get-AzureVnetConnection" paranccsal lekérheti a virtuális hálózati alagutak listáját, ahogy az alábbi példában is látható. A VNet1 a VNet neve.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Példa vissza:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Következő lépések

További információ a VPN-átjárókkal kapcsolatban: [Tudnivalók a VPN Gateway-ről](vpn-gateway-about-vpngateways.md).