---
title: 'Klasszikus virtuális hálózatok összekötése Azure Resource Manager virtuális hálózatok: portál | Microsoft Docs'
description: A klasszikus virtuális hálózatok Resource Manager-virtuális hálózatok való összekapcsolásának lépései a VPN Gateway és a portál használatával
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 9d31bcaad01b9b762e57bd619d45c1f53ffb201e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376802"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Virtuális hálózatok összekötése különböző üzemi modellekkel a portál használatával

Ez a cikk bemutatja, hogyan csatlakoztathatók a klasszikus virtuális hálózatok a Resource Manager-virtuális hálózatok, hogy a különálló üzemi modellekben található erőforrások kommunikálni tudjanak egymással. A cikkben ismertetett lépések elsősorban a Azure Portal használják, de ezt a konfigurációt a PowerShell használatával is létrehozhatja, ehhez a listából válassza ki a cikket.

> [!div class="op_single_selector"]
> * [Portál](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

A klasszikus VNet Resource Manager-VNet való csatlakoztatása hasonló ahhoz, hogy egy VNet egy helyszíni helyhez kapcsolódjon. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. Létrehozhat egy kapcsolatot a különböző előfizetésekben és különböző régiókban található virtuális hálózatok között. Olyan virtuális hálózatok is csatlakoztathatók, amelyek már rendelkeznek kapcsolattal a helyszíni hálózatokkal, feltéve, hogy az átjárók dinamikus vagy Route-alapúak. A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, a [Virtuális hálózatok közötti kapcsolat – gyakori kérdések](#faq) című részben talál. 

Ha még nem rendelkezik virtuális hálózati átjáróval, és nem szeretne létrehozni egyet, érdemes lehet inkább a virtuális hálózatok csatlakoztatni a VNet-társ használatával. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Előkészületek



* Ezek a lépések azt feltételezik, hogy mindkét virtuális hálózatok már létre lett hozva. Ha gyakorlatként használja ezt a cikket, és nem rendelkezik virtuális hálózatok, a következő lépésekben talál hivatkozásokat a létrehozásához.
* Ellenőrizze, hogy a virtuális hálózatok címtartományok nem fedik-e egymást egymással, vagy átfedésben van-e az átjárók által a csatlakozáshoz használt egyéb kapcsolatok tartományával.
* Telepítse a legújabb PowerShell-parancsmagokat a Resource Manager és a Service Management (klasszikus) szolgáltatáshoz. Ebben a cikkben a Azure Portal és a PowerShellt is használjuk. A PowerShell szükséges a klasszikus VNet és a Resource Manager-VNet közötti kapcsolatok létrehozásához. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/) foglalkozó témakörben talál. 

### <a name="example-settings"></a><a name="values"></a>Példabeállítások

Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

**Klasszikus VNet**

VNet neve = ClassicVNet <br>
Címterület = 10.0.0.0/24 <br>
Alhálózat neve = alhálózat-1 <br>
Alhálózat-címtartomány = 10.0.0.0/27 <br>
Előfizetés = a használni kívánt előfizetés <br>
Erőforráscsoport = ClassicRG <br>
Location = USA nyugati régiója <br>
GatewaySubnet = 10.0.0.32/28 <br>
Helyi hely = RMVNetLocal <br>

**Resource Manager-VNet**

VNet neve = RMVNet <br>
Címterület = 192.168.0.0/16 <br>
Erőforráscsoport = RG1 <br>
Hely = USA keleti régiója <br>
Alhálózat neve = alhálózat-1 <br>
Címtartomány = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Virtuális hálózati átjáró neve = RMGateway <br>
Átjáró típusa = VPN <br>
VPN-típus = útvonal-alapú <br>
SKU = VpnGw1 <br>
Hely = USA keleti régiója <br>
Virtuális hálózat = RMVNet <br> (a VPN-átjáró hozzárendelése ehhez a VNet) Első IP-konfiguráció = rmgwpip <br> (átjáró nyilvános IP-címe) Helyi hálózati átjáró = ClassicVNetLocal <br>
Kapcsolatok neve = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>A kapcsolatok áttekintése

Ehhez a konfigurációhoz létre kell hoznia egy VPN Gateway-kapcsolatot egy IPsec/IKE VPN-alagúton a virtuális hálózatok között. Győződjön meg róla, hogy egyik VNet sem fedi egymást egymással, sem pedig azokkal a helyi hálózatokkal, amelyekhez csatlakoznak.

Az alábbi táblázat egy példát mutat be a példa virtuális hálózatok és helyi helyeinek meghatározására:

| Virtual Network | Címtartomány | Region | Kapcsolódás helyi hálózati helyhez |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA nyugati régiója | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |USA keleti régiója |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>1. szakasz – a klasszikus VNet beállításainak konfigurálása

Ebben a szakaszban a klasszikus VNet, a helyi hálózatot (helyi helyet) és a virtuális hálózati átjárót hozza létre. A képernyőképek csak példaként szolgálnak. Ügyeljen arra, hogy a saját értékeit cserélje le, vagy használja a [példában](#values) szereplő értékeket.

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"></a> klasszikus VNet létrehozása

Ha nem rendelkezik klasszikus VNet, és gyakorlatként futtatja ezeket a lépéseket, létrehozhat egy VNet [ehhez a cikkhez](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) , és a fenti [példában](#values) szereplő beállítások értékeit.

Ha már rendelkezik egy VPN-átjáróval rendelkező VNet, ellenőrizze, hogy az átjáró dinamikus-e. Ha statikus, először törölnie kell a VPN-átjárót, mielőtt folytatná [a helyi hely konfigurálását](#local).

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. Kattintson az **+ erőforrás létrehozása** elemre az új lap megnyitásához.
3. A "keresés a piactéren" mezőbe írja be a következőt: "Virtual Network". Ha ehelyett a hálózatkezelés-> Virtual Network lehetőséget választja, nem fogja tudni klasszikus VNet létrehozni.
4. Keresse meg a "Virtual Network" elemet a visszaadott listából, és kattintson rá a Virtual Network lap megnyitásához. 
5. Klasszikus VNet létrehozásához a virtuális hálózat lapon válassza a klasszikus lehetőséget. Ha az alapértelmezett értéket használja, a rendszer felvesz egy Resource Manager-VNet.

### <a name="2-configure-the-local-site"></a>2. <a name="local"></a> a helyi hely konfigurálása

1. Keresse meg az **összes erőforrást** , és keresse meg a **ClassicVNet** a listában.
2. Kattintson a menü **Beállítások** szakaszában az **átjáró** elemre, majd a szalagcímre kattintva hozzon létre egy átjárót.
  ![VPN-átjáró konfigurálása](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "VPN-átjáró konfigurálása")
3. Az **új VPN-kapcsolat** lapon, a **kapcsolat típusa** területen válassza a **helyek közötti** lehetőséget.
4. **Helyi hely** esetén kattintson a **kötelező beállítások konfigurálása** elemre. Ekkor megnyílik a **helyi webhely** oldal.
5. A **helyi hely** lapon hozzon létre egy nevet, amely a Resource Manager VNet hivatkozik. Például: "RMVNetLocal".
6. Ha a Resource Manager-VNet VPN-átjárója már rendelkezik nyilvános IP-címmel, használja a **VPN Gateway IP-cím** mező értékét. Ha gyakorlatként hajtja végre ezeket a lépéseket, vagy még nem rendelkezik virtuális hálózati átjáróval a Resource Manager-VNet, létrehozhat egy helyőrző IP-címet. Győződjön meg arról, hogy a helyőrző IP-címe érvényes formátumot használ. Később lecseréli a helyőrző IP-címet a Resource Manager virtuális hálózati átjárójának nyilvános IP-címére.
7. Az **ügyfél címtartomány mezőben** használja a Resource Manager-VNet virtuális hálózati IP-címeinek [értékeit](#connectoverview) . Ezzel a beállítással adhatja meg a Resource Manager-beli virtuális hálózathoz való átirányításhoz használandó címeket. A példában a 192.168.0.0/16, a RMVNet címtartományt használjuk.
8. Az értékek mentéséhez és az **új VPN-kapcsolat** lapra való visszatéréshez kattintson **az OK** gombra.

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. hozza létre a virtuális hálózati átjárót

1. Az **új VPN-kapcsolat** lapon jelölje be az **átjáró azonnali létrehozása** jelölőnégyzetet.
2. Kattintson a **Választható átjárókonfiguráció** elemre a **Átjáró konfigurációja** lap megnyitásához.

   ![Átjáró konfigurációjának megnyitása lap](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Átjáró konfigurációjának megnyitása lap")
3. Kattintson az **alhálózat – kötelező beállítások konfigurálása** elemre az **alhálózat hozzáadása** lap megnyitásához. A **név** már konfigurálva van a szükséges értékkel: **GatewaySubnet**.
4. A **címtartomány** az átjáró-alhálózat tartományára vonatkozik. Bár az átjáró-alhálózatot egy/29 címtartomány (3 cím) használatával is létrehozhatja, javasoljuk, hogy hozzon létre több IP-címet tartalmazó átjáró-alhálózatot. Ez a beállítás olyan jövőbeli konfigurációkat fog kielégíteni, amelyek több elérhető IP-címet igényelhetnek. Ha lehetséges, használja a/27 vagy/28. Ha gyakorlatként használja ezeket a lépéseket, tekintse át a [példában szereplő értékeket](#values). Ebben a példában a következőt használjuk: "10.0.0.32/28". Az átjáró-alhálózat létrehozásához kattintson **az OK** gombra.
5. Az **átjáró konfigurációja** lapon a **méret** az átjáró SKU-ra hivatkozik. Válassza ki a VPN-átjáróhoz tartozó átjáró SKU-t.
6. Ellenőrizze, hogy az **útválasztási típus** **dinamikus**-e, majd kattintson az **OK** gombra az **új VPN-kapcsolat** lapra való visszatéréshez.
7. Az **új VPN-kapcsolat** lapon kattintson **az OK** gombra a VPN-átjáró létrehozásának megkezdéséhez. Egy VPN-átjáró létrehozása akár 45 percet is igénybe vehet.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. a virtuális hálózati átjáró nyilvános IP-címének másolása

A virtuális hálózati átjáró létrehozása után megtekintheti az átjáró IP-címét. 

1. Navigáljon a klasszikus VNet, és kattintson az **Áttekintés** elemre.
2. Kattintson a **VPN-kapcsolatok** elemre a VPN-kapcsolatok lap megnyitásához. A VPN-kapcsolatok oldalon megtekintheti a nyilvános IP-címet. Ez a virtuális hálózati átjáróhoz hozzárendelt nyilvános IP-cím. Jegyezze fel az IP-címet. A későbbi lépésekben a Resource Manager helyi hálózati átjáró konfigurációs beállításainak használatával végezheti el. 
3. Megtekintheti az átjáró kapcsolatainak állapotát. Figyelje meg, hogy a létrehozott helyi hálózati telephely "Csatlakozás" néven jelenik meg. Az állapot a kapcsolatok létrehozása után módosul. Ezt a lapot akkor is lezárhatja, ha befejezte az állapot megtekintését.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>2. szakasz – a Resource Manager VNet beállításainak konfigurálása

Ebben a szakaszban létrehozza a virtuális hálózati átjárót és a helyi hálózati átjárót a Resource Manager-VNet. A képernyőképek csak példaként szolgálnak. Ügyeljen arra, hogy a saját értékeit cserélje le, vagy használja a [példában](#values) szereplő értékeket.

### <a name="1-create-a-virtual-network"></a>1. virtuális hálózat létrehozása

**Példa értékei:**

* VNet neve = RMVNet <br>
* Címterület = 192.168.0.0/16 <br>
* Erőforráscsoport = RG1 <br>
* Hely = USA keleti régiója <br>
* Alhálózat neve = alhálózat-1 <br>
* Címtartomány = 192.168.1.0/24 <br>

Ha nem rendelkezik Resource Manager-VNet, és gyakorlatként futtatja ezeket a lépéseket, hozzon létre egy virtuális hálózatot a [virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)című témakör lépéseit követve a example Values használatával.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. virtuális hálózati átjáró létrehozása

Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Példa értékei:**

* Virtuális hálózati átjáró neve = RMGateway <br>
* Átjáró típusa = VPN <br>
* VPN-típus = útvonal-alapú <br>
* SKU = VpnGw1 <br>
* Hely = USA keleti régiója <br>
* Virtuális hálózat = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Első IP-konfiguráció = rmgwpip <br>

[!INCLUDE [Add gateway](../../includes/vpn-gateway-add-gw-rm-portal-empty.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. helyi hálózati átjáró létrehozása

**Példa értékei:** Helyi hálózati átjáró = ClassicVNetLocal

| Virtual Network | Címtartomány | Region | Kapcsolódás helyi hálózati helyhez |Átjáró nyilvános IP-címe|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA nyugati régiója | RMVNetLocal (192.168.0.0/16) |Az ClassicVNet-átjáróhoz hozzárendelt nyilvános IP-cím|
| RMVNet | (192.168.0.0/16) |USA keleti régiója |ClassicVNetLocal (10.0.0.0/24) |A RMVNet-átjáróhoz hozzárendelt nyilvános IP-cím.|

A helyi hálózati átjáró megadja a címtartomány és a klasszikus VNet és a virtuális hálózati átjáróhoz társított nyilvános IP-címet. Ha gyakorlatként hajtja végre ezeket a lépéseket, tekintse meg a példában szereplő értékeket.

[!INCLUDE [Add local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-ip-empty.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>3. szakasz – a klasszikus VNet helyi hely beállításainak módosítása

Ebben a szakaszban lecseréli azt a helyőrző IP-címet, amelyet a helyi hely beállításainak megadásakor használt a Resource Manager VPN Gateway IP-címével. Ez a szakasz a klasszikus (SM) PowerShell-parancsmagokat használja.

1. A Azure Portal navigáljon a klasszikus virtuális hálózathoz.
2. A virtuális hálózat lapján kattintson az **Áttekintés** elemre.
3. A **VPN-kapcsolatok** szakaszban kattintson a helyi webhely nevére a grafikában.

   ![VPN – kapcsolatok](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN-kapcsolatok")
4. A **helyek közötti VPN-kapcsolatok** lapon kattintson a hely nevére.

   ![Hely neve](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Helyi hely neve")
5. A helyi webhely kapcsolat lapján kattintson a helyi hely nevére a **helyi hely** lap megnyitásához.

   ![Megnyitás – helyi hely](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Helyi webhely megnyitása")
6. A **helyi hely** lapon cserélje le a **VPN-átjáró IP-címét** a Resource Manager-átjáró IP-címére.

   ![Átjáró – IP-cím](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Átjáró IP-címe")
7. Az IP-cím frissítéséhez kattintson **az OK** gombra.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>4. szakasz – erőforrás-kezelő létrehozása a klasszikus kapcsolódáshoz

Ezekben a lépésekben a Azure Portal segítségével konfigurálja a (z) Resource Manager-VNet származó kapcsolatokat a klasszikus VNet.

1. A **minden erőforrás** területen keresse meg a helyi hálózati átjárót. A példánkban a helyi hálózati átjáró **ClassicVNetLocal**.
2. Kattintson a **konfiguráció** elemre, és ellenőrizze, hogy az IP-cím értéke a klasszikus VNET tartozó VPN-átjáró. Szükség esetén frissítse a frissítést, majd kattintson a **Mentés** gombra. Zárja be az oldalt.
3. Az **összes erőforrás** területen kattintson a helyi hálózati átjáróra.
4. Kattintson a **kapcsolatok** elemre a kapcsolatok lap megnyitásához.
5. A **kapcsolatok** lapon kattintson a **+** kapcsolat hozzáadásához.
6. A **kapcsolatok hozzáadása** lapon nevezze el a kapcsolatokat. Például: "RMtoClassic".
7. Ezen a lapon már ki van választva a **helyek közötti** kapcsolat.
8. Válassza ki azt a virtuális hálózati átjárót, amelyet hozzá szeretne rendelni a helyhez.
9. Hozzon létre egy **megosztott kulcsot**. Ezt a kulcsot a klasszikus VNet a Resource Manager-VNet létrehozott kapcsolatban is használja a rendszer. Létrehozhatja a kulcsot, vagy létrehozhat egyet. A példánkban a "abc123"-t használjuk, de az is előfordulhat, hogy a (és a) valamivel összetettebb.
10. A kapcsolódás létrehozásához kattintson **az OK** gombra.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>5. szakasz – klasszikus – Resource Manager-alapú kapcsolatok létrehozása

Ezekben a lépésekben a klasszikus VNet a Resource Manager-VNet való kapcsolódást konfigurálja. Ehhez a lépésekhez PowerShell szükséges. Ezt a kapcsolatokat nem lehet létrehozni a portálon. Győződjön meg arról, hogy letöltötte és telepítette a klasszikus (SM) és a Resource Manager (RM) PowerShell-parancsmagokat.

### <a name="1-connect-to-your-azure-account"></a>1. kapcsolódás Azure-fiókhoz

Nyissa meg emelt szintű jogosultságokkal a PowerShell-konzolt, és jelentkezzen be az Azure-fiókjába. A bejelentkezést követően a rendszer letölti a fiók beállításait, hogy elérhetők legyenek Azure PowerShell számára. A következő parancsmag a Resource Manager-alapú üzemi modellhez tartozó Azure-fiók bejelentkezési hitelesítő adatait kéri:

```powershell
Connect-AzAccount
```

Olvassa be az Azure-előfizetések listáját.

```powershell
Get-AzSubscription
```

Ha egynél több előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Ezután jelentkezzen be a klasszikus PowerShell-parancsmagok (Service Management) használatára. Használja az alábbi parancsot az Azure-fiók hozzáadásához a klasszikus üzemi modellhez:

```powershell
Add-AzureAccount
```

Az előfizetések listájának beolvasása. Erre a lépésre akkor lehet szükség, ha a Service Management-parancsmagokat az Azure-modul telepítésének függvényében adja hozzá.

```powershell
Get-AzureSubscription
```

Ha egynél több előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. a hálózati konfigurációs fájl értékeinek megtekintése

Amikor VNet hoz létre a Azure Portalban, az Azure által használt teljes név nem látható a Azure Portalban. Előfordulhat például, hogy a Azure Portal "ClassicVNet" nevű VNet a hálózati konfigurációs fájlban sokkal több nevet tartalmaz. A név a következőhöz hasonló lehet: "Group ClassicRG ClassicVNet". Ezekben a lépésekben letölti a hálózati konfigurációs fájlt, és megtekinti az értékeket.

Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ebben a példában a hálózati konfigurációs fájlt a C:\AzureNet helyre exportálja.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Nyissa meg a fájlt egy szövegszerkesztővel, és tekintse meg a klasszikus VNet nevét. A PowerShell-parancsmagok futtatásakor használja a hálózati konfigurációs fájl nevét.

- A VNet nevei a **VirtualNetworkSite neve =**
- A helyek nevei a **LocalNetworkSite neve =**

### <a name="3-create-the-connection"></a>3. a kapcsolatok létrehozása

Állítsa be a megosztott kulcsot, és hozza létre a kapcsolódást a klasszikus VNet a Resource Manager-VNet. A megosztott kulcs nem állítható be a portál használatával. Győződjön meg arról, hogy ezeket a lépéseket a PowerShell-parancsmagok klasszikus verziójának használatával naplózza. Ehhez használja az **Add-AzureAccount**. Ellenkező esetben nem fogja tudni beállítani a "-AzureVNetGatewayKey" beállítást.

- Ebben a példában a **-VNetName** a hálózat konfigurációs fájljában található klasszikus VNet neve. 
- A **-LocalNetworkSiteName** a helyi helyhez megadott név, amelyet a hálózati konfigurációs fájlban talál.
- A **-SharedKey** egy létrehozott és megadott érték. Ebben a példában a *abc123*-t használtuk, de összetettebbek is létrehozhatók. A lényeg az, hogy az itt megadott értéknek meg kell egyeznie az erőforrás-kezelő klasszikus kapcsolódáskor megadott értékével.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>6. szakasz – a kapcsolatok ellenőrzése

A kapcsolatokat a Azure Portal vagy a PowerShell használatával ellenőrizheti. Az ellenőrzés során előfordulhat, hogy várnia kell egy percet vagy kettőt a kapcsolódás létrehozásakor. Ha a kapcsolat sikeres, a kapcsolati állapot "Connecting" értékről "Connected" értékűre változik.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>A klasszikus VNet létesített kapcsolatok ellenőrzése a Resource Manager-VNet

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>A kapcsolódás ellenőrzése a Resource Manager-VNet a klasszikus VNet

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
