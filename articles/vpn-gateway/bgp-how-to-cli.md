---
title: 'A BGP konfigurálása az Azure VPN Gatewayban: parancssori felület'
description: Ez a cikk bemutatja, hogyan konfigurálhatja a BGP-t egy Azure-beli VPN-átjáróval a Azure Resource Manager és a parancssori felület használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: a69ce0592b79be0868dd7c15ac054910eee75fc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89393598"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>A BGP konfigurálása Azure-beli VPN-átjárón a parancssori felület használatával

Ebből a cikkből megtudhatja, hogyan engedélyezheti a BGP-t a telephelyek közötti helyek közötti (S2S) VPN-kapcsolaton és a VNet közötti kapcsolaton (azaz a virtuális hálózatok közötti kapcsolaton) a Azure Resource Manager üzemi modell és az Azure CLI használatával.

## <a name="about-bgp"></a>A BGP ismertetése

A BGP a szabványos útválasztási protokoll, amelyet általában az interneten használnak az útválasztási és elérhetőségi információk két vagy több hálózat közötti cseréjére. A BGP lehetővé teszi, hogy a VPN-átjárók és a helyszíni VPN-eszközök BGP-társak vagy szomszédok legyenek az Exchange-útvonalakon. Az útvonalak tájékoztatják mindkét átjárót az előtagok rendelkezésre állásáról és elérhetőségéről, hogy átmenjenek az érintett átjárók vagy útválasztók számára. A BGP a több hálózat között is engedélyezheti a tranzit útválasztást, ha propagálja azokat az útvonalakat, amelyekkel a BGP-átjáró egy BGP-társtól megtanulja az összes többi BGP-társat.

A BGP előnyeiről, valamint a BGP-vel kapcsolatos technikai követelményekről és megfontolásokról a [BGP és az Azure VPN Gateways szolgáltatás áttekintése](vpn-gateway-bgp-overview.md)című témakörben olvashat bővebben.

Ez a cikk a következő feladatokhoz nyújt segítséget:

* [A BGP engedélyezése a VPN-átjáróhoz](#enablebgp) (kötelező)

  Ezután elvégezheti a következő két rész vagy mindkettő betöltését:

* [Létesítmények közötti kapcsolat létrehozása BGP-vel](#crossprembgp)
* [VNet-VNet kapcsolat létrehozása a BGP-vel](#v2vbgp)

Mindhárom rész egy alapszintű építőelemet képez, amely lehetővé teszi a BGP hálózati kapcsolaton belüli engedélyezését. Ha mindhárom szakaszt elvégezte, a következő ábrán látható módon kell felépíteni a topológiát:

![BGP-topológia](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Ezeket a fejezeteket kombinálva összetettebb multihop-átviteli hálózatot hozhat létre, amely megfelel az igényeinek.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>A BGP engedélyezése a VPN-átjáróhoz

Ez a szakasz a másik két konfigurációs szakasz lépéseinek elvégzése előtt szükséges. A következő konfigurációs lépések bemutatják az Azure VPN Gateway BGP-paramétereit az alábbi ábrán látható módon:

![BGP-átjáró](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Előkészületek

Telepítse a CLI-parancsok legújabb verzióját (2,0 vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>1. lépés: a TestVNet1 létrehozása és konfigurálása

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. kapcsolódás az előfizetéshez

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. erőforráscsoport létrehozása

A következő példában létrehozunk egy TestRG1 nevű erőforráscsoportot a "eastus" helyen. Ha már van olyan erőforráscsoport abban a régióban, ahol létre szeretné hozni a virtuális hálózatot, ezt a helyet használhatja.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 létrehozása

A következő példa egy TestVNet1 nevű virtuális hálózatot és három alhálózatot hoz létre: GatewaySubnet, FrontEnd és BackEnd. Az értékek behelyettesítése esetén fontos, hogy mindig az átjáró-alhálózatot nevezze el, amely kifejezetten GatewaySubnet. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

Az első parancs létrehozza az előtér-címterület és a FrontEnd alhálózatot. A második parancs további címtartományt hoz létre a háttér-alhálózathoz. A harmadik és a negyedik parancs létrehozza a háttérbeli alhálózatot és a GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. lépés: a TestVNet1 VPN Gateway létrehozása BGP-paraméterekkel

#### <a name="1-create-the-public-ip-address"></a>1. a nyilvános IP-cím létrehozása

Kérjen egy nyilvános IP-címet. A nyilvános IP-címet a rendszer a virtuális hálózathoz létrehozott VPN-átjáróhoz rendeli.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. hozza létre a VPN-átjárót az AS-számmal

Hozza létre a TestVNet1 virtuális hálózati átjáróját. A BGP használatához Route-Based VPN-átjáró szükséges. Emellett a további paraméterre is szükség van a `-Asn` TestVNet1 autonóm rendszer számának (ASN) beállításához. Az átjáró létrehozása hosszabb időt is igénybe vehet (45 perc vagy több). 

Ha ezt a parancsot a `--no-wait` paraméterrel futtatja, nem jelenik meg visszajelzés vagy kimenet. A `--no-wait` paraméter lehetővé teszi, hogy az átjáró a háttérben legyen létrehozva. Ez nem jelenti azt, hogy a VPN-átjárót azonnal létrehozták.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. az Azure BGP-társ IP-címének beszerzése

Az átjáró létrehozása után be kell szereznie a BGP-társ IP-címét az Azure VPN-átjárón. Ez a címe szükséges ahhoz, hogy a VPN-átjárót BGP-társként konfigurálja a helyszíni VPN-eszközökhöz.

Futtassa a következő parancsot, és keresse meg a `bgpSettings` kimenet tetején található szakaszt:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Az átjáró létrehozása után ezt az átjárót használhatja egy létesítmények közötti kapcsolat vagy egy VNet-VNet kapcsolat létrehozására a BGP használatával.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Létesítmények közötti kapcsolat létrehozása BGP-vel

Létesítmények közötti kapcsolat létrehozásához létre kell hoznia egy helyi hálózati átjárót, amely a helyszíni VPN-eszközt jelöli. Ezután kapcsolja össze az Azure VPN-átjárót a helyi hálózati átjáróval. Bár ezek a lépések hasonlóak más kapcsolatok létrehozásához, a BGP konfigurációs paramétereinek megadásához szükséges további tulajdonságokat is tartalmazzák.

![BGP a létesítmények közötti](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>1. lépés: a helyi hálózati átjáró létrehozása és konfigurálása

Ez a gyakorlat továbbra is a diagramon látható konfiguráció összeállítását mutatja be. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni. Ha helyi hálózati átjárókkal dolgozik, vegye figyelembe a következőket:

* A helyi hálózati átjáró a VPN-átjáróval megegyező helyen és erőforráscsoporthoz is lehet, vagy más helyen és erőforráscsoporthoz is lehet. Ez a példa különböző telephelyeken lévő különböző erőforráscsoportok átjáróit jeleníti meg.
* A helyi hálózati átjáróhoz szükséges minimális előtag a VPN-eszközön található BGP-társ IP-címének állomásneve. Ebben az esetben ez a 10.51.255.254/32 előtag/32.
* Emlékeztetőként különböző BGP-ASN kell használnia a helyszíni hálózatok és az Azure virtuális hálózat között. Ha ezek megegyeznek, akkor módosítania kell a VNet ASN-t, ha a helyszíni VPN-eszközök már használják az ASN-t más BGP-szomszédokkal.

A folytatás előtt győződjön meg arról, hogy befejezte a [BGP engedélyezése a VPN-átjáróhoz](#enablebgp) című szakaszát, és hogy továbbra is csatlakozik az 1. előfizetéshez. Figyelje meg, hogy ebben a példában egy új erőforráscsoportot hoz létre. Figyelje meg a helyi hálózati átjáró két további paraméterét is: `Asn` és `BgpPeerAddress` .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés: a VNet-átjáró és a helyi hálózati átjáró összekötése

Ebben a lépésben létrehozza a kapcsolódást a TestVNet1 és a site5 között. Meg kell adnia a `--enable-bgp` paramétert, amely engedélyezi a BGP-t ehhez a kapcsolódáshoz. 

Ebben a példában a virtuális hálózati átjáró és a helyi hálózati átjáró különböző erőforráscsoportok. Ha az átjárók különböző erőforráscsoportok, a virtuális hálózatok közötti kapcsolat beállításához meg kell adnia a két átjáró teljes erőforrás-AZONOSÍTÓját.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. a VNet1GW erőforrás-AZONOSÍTÓjának beolvasása

Használja a következő parancs kimenetét a VNet1GW erőforrás-AZONOSÍTÓjának lekéréséhez:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

A kimenetben keresse meg a `"id":` sort. A következő szakaszban a kapcsolatok létrehozásához az idézőjelek között kell lennie.

Példa a kimenetre:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Az értékeket `"id":` egy szövegszerkesztőbe (például a Jegyzettömbbe) másolhatja, így egyszerűen beillesztheti őket a csatlakozás létrehozásakor. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. a site5 erőforrás-AZONOSÍTÓjának beolvasása

Használja a következő parancsot a site5 erőforrás-AZONOSÍTÓjának lekéréséhez a kimenetből:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. hozza létre a TestVNet1-to-site5-kapcsolatokat

Ebben a lépésben létrehozza a kapcsolódást a TestVNet1 és a site5 között. Ahogy korábban már említettük, lehetséges, hogy a BGP és a nem BGP kapcsolatok is ugyanahhoz az Azure VPN-átjáróhoz tartoznak. Ha a BGP engedélyezve van a kapcsolatok tulajdonságban, az Azure nem engedélyezi a BGP-t ehhez a hálózathoz, bár a BGP-paraméterek mindkét átjárón már konfigurálva vannak. Cserélje le az előfizetés-azonosítókat a saját adataira.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Ebben a gyakorlatban a következő példa azokat a paramétereket sorolja fel, amelyeket a helyszíni VPN-eszköz BGP-konfiguráció szakaszában kell megadnia:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolat néhány perc alatt létrejön. A BGP-társítási munkamenet az IPsec-kapcsolat létrejötte után indul el.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>VNet-VNet kapcsolat létrehozása a BGP-vel

Ez a szakasz a BGP-vel való VNet-VNet, az alábbi ábrán látható módon: 

![BGP VNet – VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Az alábbi utasítások az előző részekben ismertetett lépésekkel folytatódnak. A TestVNet1 és a VPN Gateway BGP-vel való létrehozásához és konfigurálásához be kell fejeznie a [BGP engedélyezése a VPN-átjáróhoz](#enablebgp) szakaszt.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>1. lépés: a TestVNet2 és a VPN-átjáró létrehozása

Fontos annak biztosítása, hogy az új virtuális hálózat (TestVNet2) IP-címének mérete ne legyen átfedésben a VNet-tartományokkal.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartoznak. A különböző előfizetések közötti VNet-VNet kapcsolatokat is beállíthat. További információ: [VNet-to-VNet-kapcsolatok konfigurálása](vpn-gateway-howto-vnet-vnet-cli.md). `-EnableBgp $True`A BGP engedélyezéséhez vegye fel a kapcsolatot a kapcsolatok létrehozásakor.

#### <a name="1-create-a-new-resource-group"></a>1. hozzon létre egy új erőforráscsoportot

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. hozzon létre TestVNet2 az új erőforráscsoporthoz

Az első parancs létrehozza az előtér-címterület és a FrontEnd alhálózatot. A második parancs további címtartományt hoz létre a háttér-alhálózathoz. A harmadik és a negyedik parancs létrehozza a háttérbeli alhálózatot és a GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. a nyilvános IP-cím létrehozása

Kérjen egy nyilvános IP-címet. A nyilvános IP-címet a rendszer a virtuális hálózathoz létrehozott VPN-átjáróhoz rendeli.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. hozza létre a VPN-átjárót a AS számmal

Hozza létre a TestVNet2 virtuális hálózati átjáróját. Az alapértelmezett ASN-t felül kell bírálnia az Azure VPN Gateway-átjárón. A csatlakoztatott virtuális hálózatok ASN eltérőnek kell lennie a BGP és az átvitel útválasztásának engedélyezéséhez.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>2. lépés: a TestVNet1 és a TestVNet2 átjárók összekötése

Ebben a lépésben létrehozza a kapcsolódást a TestVNet1 és a site5 között. Ha engedélyezni szeretné a BGP-t ehhez a kapcsolódáshoz, meg kell adnia a `--enable-bgp` paramétert.

A következő példában a virtuális hálózati átjáró és a helyi hálózati átjáró különböző erőforráscsoportok. Ha az átjárók különböző erőforráscsoportok, a virtuális hálózatok közötti kapcsolat beállításához meg kell adnia a két átjáró teljes erőforrás-AZONOSÍTÓját. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. a VNet1GW erőforrás-AZONOSÍTÓjának beolvasása 

Szerezze be a VNet1GW erőforrás-AZONOSÍTÓját a következő parancs kimenetében:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. a VNet2GW erőforrás-AZONOSÍTÓjának beolvasása

Szerezze be a VNet2GW erőforrás-AZONOSÍTÓját a következő parancs kimenetében:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. a kapcsolatok létrehozása

Hozza létre a TestVNet1 és a TestVNet2 közötti kapcsolatokat, valamint a TestVNet2 és a TestVNet1 közötti kapcsolatokat. Cserélje le az előfizetés-azonosítókat a saját adataira.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Engedélyezze a BGP-t *mindkét* kapcsolathoz.
> 
> 

A lépések elvégzése után a rendszer néhány percen belül megkezdi a kapcsolatok létrehozását. A BGP-társas munkamenet a VNet-VNet kapcsolat befejezése után fog létrejönni.

## <a name="next-steps"></a>Következő lépések

A kapcsolódás befejezése után virtuális gépeket adhat hozzá a virtuális hálózatokhoz. A lépéseket lásd: [virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md).
