---
title: 'VNet csatlakoztatása VNet egy VNet-VNet kapcsolat használatával: Azure CLI'
description: Virtuális hálózatokat csatlakoztathat egymáshoz virtuális hálózatok közötti kapcsolat és az Azure CLI használatával.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9502f3fbd50aad756e15daa4db1badda2abf9ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660066"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Virtuális hálózatok közötti VPN Gateway-kapcsolat konfigurálása az Azure CLI használatával

Ez a cikk bemutatja, hogyan lehet virtuális hálózatokat csatlakoztatni virtuális hálózatok közötti kapcsolat használatával. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben. Amikor különböző előfizetésekről csatlakoztat virtuális hálózatokat, az előfizetéseket nem kell társítani ugyanazzal az Active Directory-bérlővel.

A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak, és az Azure CLI-t használják. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a><a name="about"></a>Tudnivalók a virtuális hálózatok csatlakoztatásáról

A virtuális hálózatok többféleképpen is összekapcsolhatók. Az alábbi szakaszok a virtuális hálózatok összekapcsolásának különböző módjait ismertetik.

### <a name="vnet-to-vnet"></a>Virtuális hálózatok közötti kapcsolat

Virtuális hálózatok közötti kapcsolat konfigurálásával könnyedén kapcsolat össze virtuális hálózatokat. Két virtuális hálózat virtuális hálózatok közötti kapcsolattal történő összekapcsolása nagyon hasonlít egy helyek közötti IPsec-kapcsolat helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához, és mindkettő ugyanúgy működik a kommunikáció során. A kapcsolattípusok közötti különbség a helyi hálózati átjáró konfigurálásának módjában rejlik. Virtuális hálózatok közötti kapcsolat létrehozásakor a helyi hálózati átjáró címtere nem látható. Ennek létrehozása és feltöltése automatikusan történik. Amikor azonban frissíti az egyik virtuális hálózat címterét, a másik automatikusan tudni fogja a frissített címtér útvonalát. A virtuális hálózatok közötti kapcsolat létrehozása általában gyorsabb és egyszerűbb, mintha egy helyek közötti kapcsolatot hozna létre a virtuális hálózatok között.

### <a name="connecting-vnets-using-site-to-site-ipsec-steps"></a>Virtuális hálózatok összekapcsolása a helyek közötti (IPsec) kapcsolat konfigurációs lépéseivel

Amikor bonyolult hálózati konfigurációkkal dolgozik, a virtuális hálózatok közötti kapcsolatok konfigurációs lépései helyett érdemesebb lehet a [helyek közötti kapcsolat](vpn-gateway-howto-site-to-site-resource-manager-cli.md) lépéseit használni virtuális hálózatai összekapcsolására. A helyek közötti kapcsolatokra vonatkozó lépésekkel manuálisan hozhatja létre és konfigurálhatja a helyi hálózati átjárókat. Az egyes virtuális hálózatok helyi hálózati átjárója helyi helyként kezeli a többi virtuális hálózatot. Így további címtereket határozhat meg a helyi hálózati átjáróhoz a forgalom irányítása érdekében. Ha egy virtuális hálózat címtere megváltozik, manuálisan frissítenie kell a megfelelő helyi hálózati átjárót a változás tükrözése érdekében. Az átjáró nem frissül automatikusan.

### <a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony

Érdemes megfontolni a virtuális hálózatok virtuális hálózatok közötti társviszony útján történő összekötését. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót, és más korlátozásokkal rendelkezik. Emellett a [virtuális hálózatok közötti társviszony díjszabásának](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a [virtuális hálózatok közötti VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway) esetén. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a><a name="why"></a>Mikor érdemes virtuális hálózatok közötti kapcsolatot létrehozni?

A virtuális hálózatokat a következő okokból érdemes virtuális hálózatok közötti kapcsolattal összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**

  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * Az Azure Traffic Manager és a Load Balancer segítségével magas rendelkezésre állású munkaterhelést állíthat be georedundanciával több Azure-régióban. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Regionális többrétegű alkalmazások elkülönítéssel vagy felügyeleti határral**

  * Egy régión belül beállíthat többrétegű alkalmazásokat több, elkülönítéssel vagy felügyeleti követelményekkel összekapcsolt virtuális hálózatokkal.

A virtuális hálózatok közötti kommunikáció kombinálható többhelyes konfigurációkkal. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

## <a name="which-vnet-to-vnet-steps-should-i-use"></a><a name="steps"></a>Melyik virtuális hálózatok közötti konfigurációs lépést alkalmazzam?

Ebben a cikkben kétféle lépéssorozatot láthat virtuális hálózatok közötti kapcsolat konfigurálására. Az egyik lépéssorozat az [azonos előfizetésben található virtuális hálózatokra](#samesub), a másik az [eltérő előfizetésekben található virtuális hálózatokra](#difsub) vonatkozik. 

Ebben a gyakorlatban igény szerint kombinálhatja a konfigurációkat, vagy csak kiválaszthat egyet, amelyet használni kíván. Az összes konfiguráció a virtuális hálózatok közötti kapcsolattípust használja. A hálózati adatforgalom a közvetlenül egymáshoz csatlakoztatott virtuális hálózatok között zajlik. Ebben a gyakorlatban a TestVNet4 forgalma nem a TestVNet5 felé irányul.

* [Azonos előfizetésben található virtuális hálózatok](#samesub): a konfiguráció lépései a TestVNet1 és TestVNet4 hálózatot használják.

  ![Olyan diagram, amely az ugyanazon előfizetésben található V hálók V net – V hálózati lépéseit mutatja.](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

* [Eltérő előfizetésben található virtuális hálózatok](#difsub): a konfiguráció lépései a TestVNet1 és TestVNet5 hálózatot használják.

  ![v2v ábra](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)


## <a name="connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>Azonos előfizetésben található virtuális hálózatok összekapcsolása

### <a name="before-you-begin"></a>Előkészületek

A folyamat elkezdése előtt telepítse a CLI-parancsok legújabb verzióit (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

### <a name="plan-your-ip-address-ranges"></a><a name="Plan"></a>Az IP-címtartományok megtervezése

A következő lépések során két virtuális hálózatot fog létrehozni, a hozzájuk tartozó átjáróalhálózatokkal és konfigurációkkal együtt. Ezután létrehoz egy VPN-kapcsolatot a két virtuális hálózat között. Fontos, hogy megtervezze a hálózati konfiguráció IP-címtartományát. Ügyeljen arra, hogy a virtuális hálózata tartományai és a helyi hálózata tartományai ne legyenek átfedésben egymással. Ezekben a példákban nem szerepel DNS-kiszolgáló. Ha névfeloldással szeretné ellátni a virtuális hálózatokat, tekintse meg a [Névfeloldás](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) című cikket.

A példákban a következő értékeket használjuk:

**Értékek a TestVNet1-hez:**

* Virtuális hálózat neve: TestVNet1
* Erőforráscsoport: TestRG1
* Hely: East US
* TestVNet1: 10.11.0.0/16 és 10.12.0.0/16
* Előtér: 10.11.0.0/24
* Háttér: 10.12.0.0/24
* Átjáróalhálózat: 10.12.255.0/27
* Átjáró neve: VNet1GW
* Nyilvános IP-cím: VNet1GWIP
* VPN típusa: RouteBased
* Kapcsolat (1–4): VNet1toVNet4
* Kapcsolat (1–5): VNet1toVNet5 (virtuális hálózatokhoz eltérő előfizetésekben)

**Értékek a TestVNet4-hez:**

* Virtuális hálózat neve: TestVNet4
* TestVNet2: 10.41.0.0/16 és 10.42.0.0/16
* Előtér: 10.41.0.0/24
* Háttér: 10.42.0.0/24
* Átjáróalhálózat: 10.42.255.0/27
* Erőforráscsoport: TestRG4
* Hely: West US
* Átjáró neve: VNet4GW
* Nyilvános IP-cím: VNet4GWIP
* VPN típusa: RouteBased
* Kapcsolat: VNet4toVNet1

### <a name="step-1---connect-to-your-subscription"></a><a name="Connect"></a>1. lépés – Csatlakozás az előfizetéshez

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="step-2---create-and-configure-testvnet1"></a><a name="TestVNet1"></a>2. lépés – A TestVNet1 létrehozása és konfigurálása

1. Hozzon létre egy erőforráscsoportot.

   ```azurecli
   az group create -n TestRG1  -l eastus
   ```
2. Hozza létre a TestVNet1-et és az alhálózatait. Ez a példa létrehoz egy TestVNet1 nevű virtuális hálózatot és egy FrontEnd nevű alhálózatot.

   ```azurecli
   az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
   ```
3. Hozzon létre egy új címteret a háttérrendszer alhálózatának. Megfigyelheti, hogy ebben a lépésben a korábban létrehozott címteret és a hozzáadni kívánt új címteret is meghatározzuk. Ennek az az oka, hogy az [az network vnet update](/cli/azure/network/vnet) parancs felülírja az előző beállításokat. Győződjön meg róla, hogy a parancs használatakor az összes címelőtagot meghatározza.

   ```azurecli
   az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
   ```
4. Hozza létre a háttérrendszer alhálózatát.
  
   ```azurecli
   az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
   ```
5. Hozza létre az átjáró-alhálózatot. Figyelje meg, hogy az átjáró alhálózatának neve „GatewaySubnet”. A név megadása kötelező. A példában az átjáróalhálózat /27-es alhálózatot használ. Ugyan létrehozhat kicsi, akár /29-es méretű átjáró-alhálózatot is, a /28-as vagy /27-es lehetőség választásával ajánlott nagyobb alhálózatot létrehozni, amely több címet tartalmaz. Ez elegendő címet biztosít ahhoz, hogy az esetleges további konfigurációkat is elbírják.

   ```azurecli 
   az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
   ```
6. Kérje egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. Vegye figyelembe, hogy az AllocationMethod értéke Dynamic. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása az átjáró számára dinamikusan történik.

   ```azurecli
   az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
   ```
7. Hozza létre a TestVNet1 virtuális hálózati átjáróját. A virtuális hálózatok közötti konfigurációkhoz a VpnType paraméternek RouteBased értékűnek kell lennie. Ha ezt a parancsot a „--no-wait” paraméterrel futtatja, nem jelenik meg visszajelzés vagy kimenet. A „--no-wait” paraméter lehetővé teszi, hogy az átjáró a háttérben jöjjön létre. Ez nem jelenti azt, hogy a VPN-átjáró azonnal elkészült. Az átjáró létrehozása akár 45 percet is igénybe vehet, az átjáró termékváltozatától függően.

   ```azurecli
   az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-3---create-and-configure-testvnet4"></a><a name="TestVNet4"></a>3. lépés – A TestVNet4 létrehozása és konfigurálása

1. Hozzon létre egy erőforráscsoportot.

   ```azurecli
   az group create -n TestRG4  -l westus
   ```
2. Hozza létre a TestVNet4-et.

   ```azurecli
   az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
   ```

3. Hozzon létre további alhálózatokat a TestVNet4-hez.

   ```azurecli
   az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
   az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
   ```
4. Hozza létre az átjáró-alhálózatot.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
   ```
5. Kérjen egy nyilvános IP-címet.

   ```azurecli
   az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
   ```
6. Hozza létre a TestVNet4 virtuális hálózati átjáróját.

   ```azurecli
   az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-4---create-the-connections"></a><a name="createconnect"></a>4. lépés – A kapcsolatok létrehozása

Most már két, VPN-átjáróval rendelkező virtuális hálózata van. A következő lépésként létre kell hoznia a VPN-átjáró kapcsolatait a virtuális hálózat átjárói között. Ha a fenti példákat követte, a virtuális hálózat átjárói eltérő erőforráscsoportokban találhatók. Ha az átjárók eltérő erőforráscsoportokban vannak, a kapcsolatok létrehozásakor meg kell határoznia és azonosítania kell minden átjáró erőforrás-azonosítóját. Ha a virtuális hálózatok ugyanabban az erőforráscsoportban találhatók, követheti a [második útmutatót](#samerg), ugyanis nem kell meghatároznia az erőforrás-azonosítókat.

### <a name="to-connect-vnets-that-reside-in-different-resource-groups"></a><a name="diffrg"></a>Eltérő erőforráscsoportokban található virtuális hálózatok csatlakoztatása

1. Az alábbi parancs kimenetéből olvassa le a VNet1GW erőforrás-azonosítóját:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   A kimenetben keresse meg az „id:” sort. A csatlakozás a következő szakaszban való létrehozásához az idézőjelek közötti értékre lesz szüksége. Másolja ezeket az értékeket egy szövegszerkesztőbe, például a Jegyzettömbbe, így a csatlakozás létrehozásakor könnyen beillesztheti őket.

   Példa a kimenetre:

   ```
   "activeActive": false, 
   "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
   "enableBgp": false, 
   "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
   "gatewayDefaultSite": null, 
   "gatewayType": "Vpn", 
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
   "ipConfigurations":
   ```

   Másolja ki az **„id”:** kifejezés utáni idézőjelek közti értékeket.

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. Olvassa le a VNet4GW erőforrás-azonosítóját, és másolja az értékeket egy szövegszerkesztőbe.

   ```azurecli
   az network vnet-gateway show -n VNet4GW -g TestRG4
   ```

3. Hozza létre a TestVNet1–TestVNet4 kapcsolatot. Ebben a lépésben a TestVNet1 felől a TestVNet4 felé irányuló kapcsolatot hozza létre. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vesz igénybe.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
   ```
4. Hozza létre a TestVNet4–TestVNet1 kapcsolatot. Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet4 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat létrehozása néhány percet vesz igénybe.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
   ```
5. Ellenőrizze a kapcsolatokat. Lásd: [A kapcsolat ellenőrzése](#verify).

### <a name="to-connect-vnets-that-reside-in-the-same-resource-group"></a><a name="samerg"></a>Azonos erőforráscsoportokban található virtuális hálózatok csatlakoztatása

1. Hozza létre a TestVNet1–TestVNet4 kapcsolatot. Ebben a lépésben a TestVNet1 felől a TestVNet4 felé irányuló kapcsolatot hozza létre. Figyelje meg, hogy a példák erőforráscsoportjai megegyeznek. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz saját értékeket is használhat, azonban a kulcsnak mindkét kapcsolat esetében azonosnak kell lennie. A kapcsolat létrehozása egy kis időt vesz igénybe.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
   ```
2. Hozza létre a TestVNet4–TestVNet1 kapcsolatot. Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet4 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat létrehozása néhány percet vesz igénybe.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
   ```
3. Ellenőrizze a kapcsolatokat. Lásd: [A kapcsolat ellenőrzése](#verify).

## <a name="connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>Különböző előfizetésben található virtuális hálózatok összekapcsolása

Ebben a forgatókönyvben csatlakoztatja a TestVNet1 és a TestVNet5 virtuális hálózatot. A virtuális hálózatok eltérő előfizetésben találhatók. Az előfizetéseket nem kell társítani ugyanazzal az Active Directory bérlővel. A konfiguráláshoz használt lépések egy további, virtuális hálózatok közötti kapcsolatot hoznak létre a TestVNet1 és a TestVNet5 összekapcsolásához.

### <a name="step-5---create-and-configure-testvnet1"></a><a name="TestVNet1diff"></a>5. lépés – A TestVNet1 létrehozása és konfigurálása

Ezek az utasítások az előző szakaszok lépéseit folytatják. A TestVNet1 és a TestVNet1 létrehozásához és konfigurálásához el kell végeznie az [1. lépést](#Connect) és VPN Gateway a [2. lépést](#TestVNet1) . Ehhez a konfigurációhoz nem kell létrehoznia az előző szakaszban a TestVNet4-et, azonban ha már megtette, az sem akadályozza az alábbi lépések végrehajtását. Miután elvégezte az 1. lépést és a 2. lépést, folytassa a 6. lépéssel (lásd alább).

### <a name="step-6---verify-the-ip-address-ranges"></a><a name="verifyranges"></a>6. lépés – Az IP-címtartományok ellenőrzése

A további kapcsolatok létrehozásakor fontos ellenőrizni, hogy az új virtuális hálózat IP-címtere ne legyen átfedésben a többi virtuális hálózati vagy hálózati átjárói tartománnyal. A gyakorlatban a TestVNet5 hálózathoz a következő értékeket használhatja:

**Értékek a TestVNet5-höz:**

* Virtuális hálózat neve: TestVNet5
* Erőforráscsoport: TestRG5
* Hely: Japan East
* TestVNet5: 10.51.0.0/16 és 10.52.0.0/16
* Előtér: 10.51.0.0/24
* Háttér: 10.52.0.0/24
* Átjáróalhálózat: 10.52.255.0.0/27
* Átjáró neve: VNet5GW
* Nyilvános IP-cím: VNet5GWIP
* VPN típusa: RouteBased
* Kapcsolat: VNet5toVNet1
* Kapcsolat típusa: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a><a name="TestVNet5"></a>7. lépés – A TestVNet5 létrehozása és konfigurálása

Ezt a lépést az új előfizetés (5. előfizetés) környezetében kell elvégezni. Ezt a részt azon másik szervezet rendszergazdájának kell elvégeznie, amely az előfizetés tulajdonosa. Ha váltani szeretne az előfizetések között a `az account list --all` fiókja számára elérhető előfizetések listázásához, akkor `az account set --subscription <subscriptionID>` a használatával váltson a használni kívánt előfizetésre.

1. Győződjön meg róla, hogy az 5. előfizetéshez csatlakozik, majd hozzon létre egy erőforráscsoportot.

   ```azurecli
   az group create -n TestRG5  -l japaneast
   ```
2. Hozza létre a TestVNet5-öt.

   ```azurecli
   az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
   ```

3. Adjon hozzá alhálózatokat.

   ```azurecli
   az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
   az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
   ```

4. Adja hozzá az átjáró alhálózatát.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
   ```

5. Kérjen egy nyilvános IP-címet.

   ```azurecli
   az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
   ```
6. A TestVNet5 átjárójának létrehozása

   ```azurecli
   az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-8---create-the-connections"></a><a name="connections5"></a>8. lépés – A kapcsolatok létrehozása

Ez a lépés két CLI-munkamenetre van felosztva, amelyek jelölése **[1. előfizetés]** és **[5. előfizetés]**, mivel az átjárók eltérő előfizetésekben találhatók. Ha váltani szeretne az előfizetések között a `az account list --all` fiókja számára elérhető előfizetések listázásához, akkor `az account set --subscription <subscriptionID>` a használatával váltson a használni kívánt előfizetésre.

1. **[1. előfizetés]** Jelentkezzen be, és csatlakozzon az 1. előfizetéshez. Az alábbi parancs kimenetéből olvassa le az átjáró nevét és azonosítóját:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   Másolja ki az „id:” kifejezés kimenetét. Küldje el e-mailben vagy másképp a VNet-átjáró (VNet1GW) azonosítóját és nevét az 5. előfizetés rendszergazdájának.

   Példa a kimenetre:

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. **[5. előfizetés]** Jelentkezzen be, és csatlakozzon az 5. előfizetéshez. Az alábbi parancs kimenetéből olvassa le az átjáró nevét és azonosítóját:

   ```azurecli
   az network vnet-gateway show -n VNet5GW -g TestRG5
   ```

   Másolja ki az „id:” kifejezés kimenetét. Küldje el e-mailben vagy másképp a VNet-átjáró (VNet5GW) azonosítóját és nevét az 1. előfizetés rendszergazdájának.

3. **[1. előfizetés]** Ebben a lépésben a TestVNet1 felől a TestVNet5 felé irányuló kapcsolatot hozza létre. A megosztott kulcshoz saját értékeket is használhat, azonban a kulcsnak mindkét kapcsolat esetében azonosnak kell lennie. A kapcsolat létrehozása egy kis időt vehet igénybe. Kapcsolódjon az 1. előfizetéshez.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```

4. **[5. előfizetés]** Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet5 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Győződjön meg róla, hogy a megosztott kulcsok egyeznek, valamint arról, hogy az 5. előfizetéshez csatlakozik.

   ```azurecli
   az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```

## <a name="verify-the-connections"></a><a name="verify"></a>A kapcsolatok ellenőrzése
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Következő lépések

* Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért tekintse meg a [Virtual Machines-dokumentációt](../index.yml).
* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).