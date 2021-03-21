---
title: 'Számítógép összekötése virtuális hálózathoz pont – hely kapcsolat és RADIUS-hitelesítés használatával: PowerShell | Azure'
description: A Windows-és OS X-ügyfelek biztonságos csatlakoztatása a virtuális hálózathoz a P2S és a RADIUS-hitelesítés használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: cherylmc
ms.openlocfilehash: 9d962d3a4757b4c7b2d217f91aaf73d6ad4164d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94964847"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Pont – hely kapcsolat konfigurálása VNet a RADIUS-hitelesítés használatával: PowerShell

Ez a cikk bemutatja, hogyan hozhat létre egy olyan pont – hely kapcsolattal rendelkező VNet, amely RADIUS-hitelesítést használ. Ez a konfiguráció csak a Resource Manager-alapú üzemi modell esetében érhető el.

A pont–hely (P2S) VPN-átjáró lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont – hely VPN-kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a VNet, például otthonról vagy konferenciáról. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy VNetre csatlakoztatni.

A pont–hely VPN-kapcsolat indítása a Windows- és Mac-eszközökről történik. A kapcsolódó ügyfelek az alábbi hitelesítési módszereket használhatják: 

* RADIUS-kiszolgáló
* Natív tanúsítványalapú hitelesítés VPN Gateway
* Natív Azure Active Directory hitelesítés (csak Windows 10 esetén)

Ez a cikk segítséget nyújt a P2S-konfiguráció konfigurálásához a RADIUS-kiszolgáló használatával történő hitelesítéssel. Ha ehelyett a generált tanúsítványok és a VPN-átjáró natív tanúsítvány-hitelesítés használatával szeretne hitelesítést végezni, tekintse meg [a pont – hely kapcsolat konfigurálása VNet a VPN Gateway natív tanúsítvány-hitelesítéssel](vpn-gateway-howto-point-to-site-rm-ps.md) vagy Azure Active Directory-bérlő létrehozása a Azure Active Directory hitelesítéshez a [P2S OpenVPN protokoll kapcsolataihoz](openvpn-azure-ad-tenant.md) című témakört.

![Olyan diagram, amely a P2S konfigurációját jeleníti meg a RADIUS-kiszolgáló használatával történő hitelesítéssel.](./media/point-to-site-how-to-radius-ps/p2sradius.png)

A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. A P2S a VPN-kapcsolatokat az SSTP (Secure Socket Tunneling Protocol), az OpenVPN vagy a IKEv2 protokollal hozza létre.

* Az SSTP egy TLS-alapú VPN-alagút, amely csak Windowsos ügyféloldali platformokon támogatott. Képes áthatolni a tűzfalakon, ezért ideális megoldás az Azure-hoz való csatlakozáshoz bármilyen tetszőleges. A kiszolgálói oldalon az SSTP 1.0, 1.1 és 1.2 verziója támogatott. Az ügyfél dönti el, hogy melyik verziót használja. Windows 8.1 és újabb kiadások esetén az SSTP alapértelmezés szerint az 1.2 verziót használja.

* OpenVPN® protokoll, SSL/TLS-alapú VPN protokoll. A TLS VPN-megoldás képes behatolni a tűzfalakba, mivel a legtöbb tűzfal a 443-as TCP-portot nyitja meg, amelyet a TLS használ. Az OpenVPN az Android, az iOS (11,0-es és újabb verziók), a Windows, a Linux és a Mac rendszerű eszközök (OSX 10,13-es és újabb verziók) használatával való kapcsolódásra használható.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).

A pont–hely kapcsolatokhoz a következőkre van szükség:

* Útvonalalapú VPN-átjáró. 
* RADIUS-kiszolgáló a felhasználói hitelesítés kezeléséhez. A RADIUS-kiszolgáló a helyszínen vagy az Azure-VNet is üzembe helyezhető. A magas rendelkezésre állás érdekében két RADIUS-kiszolgálót is beállíthat.
* VPN-ügyfél konfigurációs csomagja azon Windows-eszközökhöz, amelyek csatlakozni fognak a VNet. A VPN-ügyfél konfigurációs csomagja biztosítja azokat a beállításokat, amelyek szükségesek ahhoz, hogy a VPN-ügyfél csatlakozhasson a P2S.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Tudnivalók a P2S VPN-EK Active Directory (AD) tartományi hitelesítéséről

Az AD tartományi hitelesítés lehetővé teszi a felhasználók számára, hogy a szervezeti tartományi hitelesítő adataikkal jelentkezzenek be az Azure-ba. Ehhez egy olyan RADIUS-kiszolgáló szükséges, amely integrálva van az AD-kiszolgálóval. A szervezetek a meglévő RADIUS-telepítést is kihasználhatják.
 
A RADIUS-kiszolgáló a helyszínen vagy az Azure-VNet is elvégezhető. A hitelesítés során a VPN-átjáró áteresztőként működik, és továbbítja a hitelesítési üzeneteket a RADIUS-kiszolgáló és a csatlakozó eszköz között. Fontos, hogy a VPN-átjáró elérje a RADIUS-kiszolgálót. Ha a RADIUS-kiszolgáló a helyszínen található, akkor az Azure-ból a helyszíni helyre történő VPN-helyek közötti kapcsolat szükséges.

A Active Directoryon kívül a RADIUS-kiszolgáló is integrálható más külső identitás-rendszerekkel. Ez számos hitelesítési lehetőséget nyit meg a pont – hely VPN-ek számára, beleértve az MFA-beállításokat is. Tekintse meg a RADIUS-kiszolgáló gyártójának dokumentációját, hogy beolvassa a rendszerbe integrált identitási rendszerek listáját.

![Összekötő diagram – sugár](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Csak a VPN-helyek közötti kapcsolat használható a helyszíni RADIUS-kiszolgálóhoz való csatlakozáshoz. ExpressRoute-kapcsolat nem használható.
>
>

## <a name="before-beginning"></a><a name="before"></a>Mielőtt hozzálát

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Példaértékek

A példaértékek használatával létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat. Megteheti, hogy lépésről lépésre végighalad az eljáráson, és módosítás nélkül ezeket az értékeket használja, de módosíthatja is őket, hogy megfeleljenek a saját környezetének.

* **Név: VNet1**
* **Címtartomány: 192.168.0.0/16** és **10.254.0.0/16**<br>Ez a példa egynél több címtartományt használ annak szemléltetésére, hogy ez a konfiguráció több címtartománnyal is működik. Azonban nem kötelező több címtartományt megadni ehhez a konfigurációhoz.
* **Alhálózat neve: FrontEnd**
  * **Alhálózati címtartomány: 192.168.1.0/24**
* **Alhálózat neve: BackEnd**
  * **Alhálózati címtartomány: 10.254.1.0/24**
* **Alhálózat neve: GatewaySubnet**<br>Ennek az alhálózatnak kötelező a *GatewaySubnet* nevet adni, ellenkező esetben nem működik a VPN-átjáró.
  * **Átjáró-alhálózat címtartománya: 192.168.200.0/24** 
* **VPN-ügyfelek címkészlete: 172.16.201.0/24**<br>Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, a VPN-ügyfél címkészletből kapnak IP-címet.
* **Előfizetés:** Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* **Erőforráscsoport: TestRG**
* **Hely: USA keleti régiója**
* DNS-kiszolgáló: annak a DNS-kiszolgálónak az **IP-címe** , amelyet névfeloldásra kíván használni a VNet. (nem kötelező)
* **Átjáró neve: Vnet1GW**
* **Nyilvános IP-név: VNet1GWPIP**
* **VpnType: Útvonalalapú**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. Állítsa be a változókat

Deklarálja a használni kívánt változókat. Használja a következő példát, és szükség szerint cserélje le az értékeket a sajátjaira. Ha a gyakorlat során bármikor lezárta a PowerShell-vagy Cloud Shell-munkamenetet, csak másolja és illessze be újra az értékeket a változók újbóli bejelentéséhez.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"></a> az erőforráscsoport, a VNet és a nyilvános IP-cím létrehozása

A következő lépésekben hozzon létre egy erőforráscsoportot és egy virtuális hálózatot az erőforráscsoport három alhálózattal. Az értékek behelyettesítése esetén fontos, hogy mindig nevezze el az átjáró-alhálózatot, amely kifejezetten "GatewaySubnet". Ha más néven nevezi el, az átjáró létrehozása meghiúsul;

1. Hozzon létre egy erőforráscsoportot.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Hozza létre a virtuális hálózat alhálózatainak konfigurációit, névként a következő értékeket adja meg: *FrontEnd*, *BackEnd*, illetve *GatewaySubnet*. Ezek az előtagok a deklarált virtuális hálózati címtér részei kell, hogy legyenek.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Hozza létre a virtuális hálózatot.

   Ebben a példában a -DnsServer paramétert nem kötelező megadni. Az érték megadásával nem jön létre új DNS-kiszolgáló. A megadott DNS-kiszolgáló IP-címének olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amelyekkel Ön kapcsolatot fog létesíteni a virtuális hálózatról. Ebben a példában egy magánhálózati IP-címet használtunk, de ez valószínűleg nem az Ön DNS-kiszolgálójának IP-címe. Ügyeljen arra, hogy a saját értékeit használja. A megadott értéket a VNet telepített erőforrások használják, nem a P2S-kapcsolatban.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Egy VPN Gateway-nek rendelkeznie kell nyilvános IP-címmel. Először az IP-cím típusú erőforrást kell kérnie, majd hivatkoznia kell arra, amikor létrehozza a virtuális hálózati átjárót. Az IP-címet a rendszer dinamikusan rendeli hozzá az erőforráshoz a VPN Gateway létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Nem kérheti statikus IP-cím hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

   A dinamikusan hozzárendelt nyilvános IP-cím igénylésére szolgáló változók meghatározása.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"></a> a RADIUS-kiszolgáló beállítása

A virtuális hálózati átjáró létrehozása és konfigurálása előtt a RADIUS-kiszolgálót helyesen kell konfigurálni a hitelesítéshez.

1. Ha nincs telepítve RADIUS-kiszolgáló, telepítsen egyet. A telepítési lépésekért tekintse meg a RADIUS-gyártó által biztosított telepítési útmutatót.  
2. Konfigurálja a VPN-átjárót RADIUS-ügyfélként a RADIUS-kiszolgálón. A RADIUS-ügyfél hozzáadásakor adja meg a létrehozott virtuális hálózati GatewaySubnet. 
3. A RADIUS-kiszolgáló beállítása után szerezze be a RADIUS-kiszolgáló IP-címét, valamint azt a közös titkot, amelyet a RADIUS-ügyfeleknek a RADIUS-kiszolgálóval való kommunikációhoz használniuk kell. Ha a RADIUS-kiszolgáló az Azure VNet található, használja a RADIUS-kiszolgáló virtuális gép HITELESÍTÉSSZOLGÁLTATÓI IP-címét.

A [hálózati házirend-kiszolgáló (NPS)](/windows-server/networking/technologies/nps/nps-top) című cikk útmutatást nyújt a Windows RADIUS-kiszolgáló (NPS) Active Directory tartományi hitelesítéshez való konfigurálásához.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"></a> a VPN-átjáró létrehozása

Konfigurálja és hozza létre a VPN-átjárót a VNet.

* A-GatewayType "VPN" értékűnek kell lennie, a-VpnType pedig "Útvonalalapú" értékűnek kell lennie.
* A VPN-átjáró akár 45 percet is igénybe vehet, attól függően, hogy melyik [átjárói SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)-t   választotta.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"></a> a RADIUS-kiszolgáló és az ügyfél-Címkészlet hozzáadása
 
* A-RadiusServer név vagy IP-cím alapján is megadható. Ha megadja a nevet, és a kiszolgáló a helyszínen található, akkor előfordulhat, hogy a VPN-átjáró nem tudja feloldani a nevet. Ha ez a helyzet, akkor érdemes megadnia a kiszolgáló IP-címét. 
* A-RadiusSecret egyeznie kell azzal, amit a RADIUS-kiszolgálón konfigurált.
* A-Címtérről az a tartomány, amelyből a Connecting VPN-ügyfelek IP-címet kapnak. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik. Ellenőrizze, hogy van-e elég nagy méretű címkészlet konfigurálva.  

1. Hozzon létre egy biztonságos karakterláncot a RADIUS-titok számára.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. A rendszer a RADIUS-titok megadását kéri. A beírt karakterek nem jelennek meg, hanem a "*" karakterrel lesznek lecserélve.

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Adja hozzá a VPN-ügyfél címkészlet és a RADIUS-kiszolgáló adatait.

   SSTP-konfigurációk esetén:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Az OpenVPN® konfigurációk esetén:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   IKEv2-konfigurációk esetén:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   SSTP + IKEv2 esetén

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   **Két** RADIUS-kiszolgáló megadásához használja a következő szintaxist. Szükség szerint módosítsa a **-VpnClientProtocol** értéket

    ```azurepowershell-interactive
    $radiusServer1 = New-AzRadiusServer -RadiusServerAddress 10.1.0.15 -RadiusServerSecret $radiuspd -RadiusServerScore 30
    $radiusServer2 = New-AzRadiusServer -RadiusServerAddress 10.1.0.16 -RadiusServerSecret $radiuspd -RadiusServerScore 1

    $radiusServers = @( $radiusServer1, $radiusServer2 )

    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $actual -VpnClientAddressPool 201.169.0.0/16 -VpnClientProtocol "IkeV2" -RadiusServerList $radiusServers
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"></a> töltse le a VPN-ügyfél konfigurációs csomagját, és állítsa be a VPN-ügyfelet.

A VPN-ügyfél konfigurációja lehetővé teszi, hogy az eszközök P2S-kapcsolaton keresztül csatlakozzanak a VNet. VPN-ügyfél konfigurációs csomagjának létrehozásához és a VPN-ügyfél beállításához lásd: [VPN-ügyfél konfigurációjának létrehozása a RADIUS-hitelesítéshez](point-to-site-vpn-client-configuration-radius.md).

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. kapcsolódás az Azure-hoz

### <a name="to-connect-from-a-windows-vpn-client"></a>Csatlakozás Windows VPN-ügyfélről

1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Adja meg a tartományi hitelesítő adatait, majd kattintson a "kapcsolat" gombra. Megjelenik az emelt szintű jogosultságokat kérő előugró üzenet. Fogadja el, és adja meg a hitelesítő adatokat.

   ![A VPN-ügyfél az Azure-hoz csatlakozik](./media/point-to-site-how-to-radius-ps/client.png)
2. A kapcsolat létrejött.

   ![A kapcsolat létrejött](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Csatlakozás Mac VPN-ügyfélről

A Hálózat párbeszédpanelen keresse meg a használni kívánt ügyfél profilját, majd kattintson a **Csatlakozás** gombra.

  ![Mac-kapcsolat](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>A kapcsolat ellenőrzése

1. Annak ellenőrzéséhez, hogy a VPN-kapcsolat aktív-e, nyisson meg egy rendszergazda jogú parancssort, és futtassa az *ipconfig/all* parancsot.
2. Tekintse meg az eredményeket. Figyelje meg, hogy a kapott IP-cím azok közül a címek közül való, amelyeket a pont–hely VPN-ügyfél konfigurációjának címkészletében megadott. Az eredmények az alábbi példában szereplőkhöz hasonlóak:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

Az P2S-kapcsolatok hibaelhárításáról lásd: az [Azure pont – hely kapcsolatok hibaelhárítása](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Ellenőrizze, hogy létrejött-e a VPN-ügyfél konfigurációs csomagja azután, hogy a DNS-kiszolgáló IP-címei meg lettek adva a virtuális hálózathoz. Ha frissítette a DNS-kiszolgáló IP-címeit, hozzon létre és telepítsen egy új VPN-ügyfélkonfigurációs csomagot.

* Az „ipconfig” használatával ellenőrizze annak a számítógépnek az Ethernet-adapteréhez hozzárendelt IPv4-címet, amelyről a kapcsolatot létesíti. Ha az IP-cím azon virtuális hálózat tartományában található, amelyhez csatlakozni kíván, vagy a VPN-ügyfél címkészletének címtartományában, akkor átfedő címtérről beszélünk. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad.

## <a name="faq"></a><a name="faq"></a>GYIK

Ez a gyakori kérdések a RADIUS-hitelesítést használó P2S vonatkoznak

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Következő lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](../index.yml). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/network-overview.md).
