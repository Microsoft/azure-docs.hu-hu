---
title: 'Csatlakozás VNet egy számítógép P2S VPN és natív Azure-tanúsítvány hitelesítése: PowerShell'
description: A Windows-és macOS-ügyfelek biztonságos csatlakoztatása az Azure Virtual networkhez P2S és önaláírt vagy HITELESÍTÉSSZOLGÁLTATÓ által kibocsátott tanúsítványok használatával. Ebben a cikkben a PowerShellt fogjuk használni.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed0a60c88c33af70b7d780d6c4735c5f8e65b35b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660406"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Pont – hely VPN-kapcsolat konfigurálása VNet natív Azure tanúsítványalapú hitelesítéssel: PowerShell

Ez a cikk segítséget nyújt a Windows, Linux vagy macOS rendszerű ügyfelek biztonságos csatlakoztatásához egy Azure-VNet. A pont – hely VPN-kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a VNet, például otthonról vagy konferenciáról. Pont–hely kapcsolatot is használhat helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni. A pont – hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. Pont–hely kapcsolat esetén SSTP (Secure Socket Tunneling Protocol) vagy IKEv2-protokoll használatával jön létre a VPN-kapcsolat.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="Kapcsolódás számítógépről egy Azure VNet-pont – hely kapcsolati diagramhoz":::

További információ a pont – hely VPN-ről: [Tudnivalók a pont – hely VPN-ről](point-to-site-about.md). Ha a Azure Portal használatával szeretné létrehozni ezt a konfigurációt, tekintse meg a [pont – hely típusú VPN konfigurálása a Azure Portal használatával](vpn-gateway-howto-point-to-site-resource-manager-portal.md)című témakört.

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> A cikkben szereplő lépések többsége használhatja a Azure Cloud Shell. Azonban a Cloud Shell nem használhatók tanúsítványok létrehozásához. Emellett a főtanúsítvány nyilvános kulcsának feltöltéséhez Azure PowerShell helyileg vagy a Azure Portal kell használnia.
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. bejelentkezés

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. változók deklarálása

Ehhez a cikkhez változókat használunk, így egyszerűen módosíthatja a saját környezetére érvényes értékeket anélkül, hogy a példákat módosítani kellene. Deklarálja a használni kívánt változókat. A következő mintát használhatja, ha szükséges, a saját értékeit is helyettesítheti. Ha a gyakorlat során bármikor lezárta a PowerShell-vagy Cloud Shell-munkamenetet, csak másolja és illessze be újra az értékeket a változók újbóli bejelentéséhez.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. VNet konfigurálása

1. Hozzon létre egy erőforráscsoportot.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. Hozza létre a virtuális hálózat alhálózati konfigurációit, és nevezze el a *felületet* és a *GatewaySubnet*. Ezek az előtagok a deklarált virtuális hálózati címtér részei kell, hogy legyenek.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. Hozza létre a virtuális hálózatot.

   Ebben a példában a -DnsServer paramétert nem kötelező megadni. Az érték megadásával nem jön létre új DNS-kiszolgáló. A megadott DNS-kiszolgáló IP-címének olyan DNS-kiszolgálónak kell lennie, amely fel tudja oldani azoknak az erőforrásoknak a nevét, amelyekkel Ön kapcsolatot fog létesíteni a virtuális hálózatról. Ez a példa egy magánhálózati IP-címet használ, de ez valószínűleg nem az Ön DNS-kiszolgálójának IP-címe. Ügyeljen arra, hogy a saját értékeit használja. A megadott értéket a virtuális hálózaton üzembe helyezett erőforrások használják, nem a pont–hely kapcsolat vagy a VPN-ügyfél.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. Adja meg a most létrehozott virtuális hálózat változóit.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. Egy VPN Gateway-nek rendelkeznie kell nyilvános IP-címmel. Először az IP-cím típusú erőforrást kell kérnie, majd hivatkoznia kell arra, amikor létrehozza a virtuális hálózati átjárót. Az IP-címet a rendszer dinamikusan rendeli hozzá az erőforráshoz a VPN Gateway létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Nem kérheti statikus IP-cím hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gatewayhez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

   Kérjen egy dinamikusan hozzárendelt nyilvános IP-címet.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. a VPN-átjáró létrehozása

Ebben a lépésben a VNet virtuális hálózati átjáróját konfigurálja és hozza létre.

* A -GatewayType csak **Vpn** lehet, a -VpnType pedig csak **RouteBased** lehet.
* A -VpnClientProtocol paraméterrel adhatja meg az engedélyezni kívánt alagutak típusát. Az alagút beállításai az **OpenVPN, az SSTP** és a **IKEv2**. Dönthet úgy, hogy engedélyezi az egyiket vagy bármely támogatott kombinációt. Ha több típust szeretne engedélyezni, adja meg a neveket vesszővel elválasztva. Az OpenVPN és az SSTP együttes használata nem engedélyezhető. Az Android- és Linux-alapú strongSwan-ügyfél, valamint az iOS- és OS X-alapú natív IKEv2 VPN-ügyfél csak IKEv2-alagutat használ a kapcsolódáshoz. A Windows-ügyfél először az IKEv2-vel próbálkozik, majd ha azzal nem sikerült, visszavált SSTP-re. Az OpenVPN-ügyfél használatával kapcsolódhat az OpenVPN-alagút típusához.
* A Virtual Network Gateway "Basic" SKU nem támogatja a IKEv2, az OpenVPN vagy a RADIUS-hitelesítést. Ha azt tervezi, hogy a Mac-ügyfelek csatlakoznak a virtuális hálózathoz, ne használja az alapszintű SKU-t.
* Egy VPN-átjáró létrehozása akár 45 percet is igénybe vehet a kiválasztott [átjáró termékváltozatától](vpn-gateway-about-vpn-gateway-settings.md) függően. Ez a példa az IKEv2-t használja.

1. Konfigurálja és hozza létre a virtuális hálózati átjárót a virtuális hálózat számára. Az átjáró létrehozása körülbelül 45 percet vesz igénybe.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. Az átjáró létrehozása után megtekintheti az alábbi példát. Ha bezárta a PowerShellt, vagy túllépte az időkorlátot az átjáró létrehozása közben, akkor ismét [deklarálhatja a változókat](#declare) .

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. a VPN-ügyfélhez tartozó Címkészlet hozzáadása

Miután befejeződött a VPN-átjáró létrehozása, hozzáadhatja a VPN-ügyfélcímkészletet. A VPN-ügyfélcímkészlet az a tartomány, amelyből a VPN-ügyfelek IP-címet kapnak csatlakozáskor. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik.

Ebben a példában a VPN-ügyfél-címkészlet egy korábbi lépésben [változóként](#declare) van deklarálva.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. tanúsítványok előállítása

>[!IMPORTANT]
> Azure Cloud Shell használatával nem lehet tanúsítványokat előállítani. Az ebben a szakaszban leírt módszerek egyikét kell használnia. Ha a PowerShellt szeretné használni, helyileg kell telepítenie.
>

A tanúsítványokat az Azure a VPN-ügyfelek hitelesítésére használja pont – hely VPN-kapcsolatokhoz. A főtanúsítvány nyilvánoskulcs-adatait feltölti az Azure-ba. A nyilvános kulcs ezután „megbízhatónak” tekinthető. Az ügyféltanúsítványokat a megbízható főtanúsítványból kell létrehozni, majd telepíteni kell az összes számítógépen a Certificates-Current User/Personal tanúsítványtárolóban. A tanúsítványt a rendszer az ügyfél hitelesítésére használja, amikor az a virtuális hálózathoz próbál csatlakozni. 

Önaláírt tanúsítványok használata esetén azokat megadott paraméterekkel kell létrehozni. Önaláírt tanúsítványt a [PowerShell és Windows 10](vpn-gateway-certificates-point-to-site.md), vagy ha nem rendelkezik Windows 10 rendszerrel, a [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) című cikkekben leírt utasítások alapján hozhat létre. Fontos, hogy az önaláírt legfelső szintű tanúsítványok és az ügyféltanúsítványok generálása során lépésről lépésre betartsa ezeket az utasításokat. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és hibaüzenetet eredményeznek kapcsolódáskor.

### <a name="root-certificate"></a><a name="cer"></a>Főtanúsítvány

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. A főtanúsítvány létrehozása után [exportálja](vpn-gateway-certificates-point-to-site.md#cer) a nyilvános tanúsítvány (nem a titkos kulcs) adatkészletét Base64 kódolású X. 509. cer fájlként.

### <a name="client-certificate"></a><a name="generate"></a>Ügyféltanúsítvány

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. Az ügyféltanúsítvány létrehozása után [exportálja](vpn-gateway-certificates-point-to-site.md#clientexport) a tanúsítványt. Az ügyféltanúsítványt a rendszer a csatlakozni kívánó ügyfélszámítógépekhez továbbítja.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. Töltse fel a főtanúsítvány nyilvánoskulcs-adatait

Ellenőrizze, hogy a VPN-átjáró létrehozása befejeződött-e. Ha befejeződött, töltse fel a megbízható főtanúsítványhoz tartozó .cer fájlt (amely a nyilvános kulcsot tartalmazza) az Azure-ba. Miután feltöltötte a .cer fájlt, az Azure felhasználhatja azt azon ügyfelek hitelesítéséhez, amelyeken telepítve lett egy, a megbízható főtanúsítványból létrehozott ügyféltanúsítvány. Szükség szerint később további megbízhatófőtanúsítvány-fájlokat is feltölthet (legfeljebb 20-at).

>[!NOTE]
> A. cer fájl nem tölthető fel Azure Cloud Shell használatával. Használhatja a PowerShellt helyileg a számítógépen, vagy használhatja a [Azure Portal lépéseket](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
>

1. Deklarálja a tanúsítványnév változóját, és cserélje le az értékeket a saját értékeire.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. Helyettesítse a fájl elérési útját a sajátjával, majd futtassa a parancsmagokat.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Töltse fel a nyilvánoskulcs-adatokat az Azure-ba. A tanúsítvány adatainak feltöltése után az Azure megbízható főtanúsítványnak tekinti. A feltöltéskor győződjön meg arról, hogy a PowerShell helyileg fut a számítógépen, vagy inkább a [Azure Portal lépéseket](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)használhatja. Azure Cloud Shell használatával nem tölthető fel.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. exportált ügyféltanúsítvány telepítése

A következő lépések segítségével telepítheti a Windows-ügyfeleket. További ügyfelek és további információk: [ügyféltanúsítvány telepítése](point-to-site-how-to-vpn-client-install-azure-cert.md).

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

Győződjön meg arról, hogy az ügyféltanúsítványt .pfx fájlként exportálta a teljes tanúsítványlánccal együtt (ez az alapértelmezett beállítás). Egyéb esetben a főtanúsítvány adatai nem lesznek jelen az ügyfélszámítógépen, és az ügyfél nem fogja tudni megfelelően elvégezni a hitelesítést.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. a VPN-ügyfél konfigurálása

Ebben a szakaszban a számítógép natív ügyfelét konfigurálja a virtuális hálózati átjáróhoz való kapcsolódáshoz. Ha például a VPN-beállításokat a Windows rendszerű számítógépen látja, hozzáadhat VPN-kapcsolatokat. A pont – hely kapcsolathoz konkrét konfigurációs beállítások szükségesek. Ezek a lépések segítséget nyújtanak egy olyan csomag létrehozásához, amelyben a natív VPN-ügyfélnek képesnek kell lennie csatlakozni a virtuális hálózathoz pont – hely kapcsolaton keresztül.

Az ügyfél-konfigurációs csomag létrehozásához és telepítéséhez a következő gyors példákat használhatja. A csomagok tartalmával és a VPN-ügyfél konfigurációs fájljainak létrehozásával és telepítésével kapcsolatos további tudnivalókat lásd: [VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése](point-to-site-vpn-client-configuration-azure-cert.md).

Ha újra be kell jelentenie a változókat, [itt](#declare)találhatja meg őket.

### <a name="to-generate-configuration-files"></a>Konfigurációs fájlok előállítása

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>Az ügyfél-konfigurációs csomag telepítése

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. kapcsolódás az Azure-hoz

### <a name="windows-vpn-client"></a>Windows VPN-ügyfél

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Mac VPN-ügyfél

A Hálózat párbeszédpanelen keresse meg a használni kívánt ügyfél profilját, majd kattintson a **Csatlakozás** gombra.
Részletes utasításokért lásd: [install-Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) . Ha nem sikerül a csatlakozás, ellenőrizze, hogy a virtuális hálózati átjáró nem alapszintű SKU-t használ-e. A Mac-ügyfelek nem támogatják az alapszintű SKU-t.

  ![Mac-kapcsolat](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>A kapcsolatok ellenőrzése

Ezek az utasítások Windows-ügyfelekre érvényesek.

1. Annak ellenőrzéséhez, hogy a VPN-kapcsolat aktív-e, nyisson meg egy rendszergazda jogú parancssort, és futtassa az *ipconfig/all* parancsot.
2. Tekintse meg az eredményeket. Figyelje meg, hogy a kapott IP-cím a konfigurációban megadott pont – hely VPN-ügyfél címkészlet egyik címe. Az eredmények az alábbi példában szereplőkhöz hasonlóak:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Csatlakozás virtuális géphez

Ezek az utasítások Windows-ügyfelekre érvényesek.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Ellenőrizze, hogy létrejött-e a VPN-ügyfél konfigurációs csomagja azután, hogy a DNS-kiszolgáló IP-címei meg lettek adva a virtuális hálózathoz. Ha frissítette a DNS-kiszolgáló IP-címeit, hozzon létre és telepítsen egy új VPN-ügyfélkonfigurációs csomagot.

* Az „ipconfig” használatával ellenőrizze annak a számítógépnek az Ethernet-adapteréhez hozzárendelt IPv4-címet, amelyről a kapcsolatot létesíti. Ha az IP-cím azon virtuális hálózat tartományában található, amelyhez csatlakozni kíván, vagy a VPN-ügyfél címkészletének címtartományában, akkor átfedő címtérről beszélünk. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Főtanúsítvány hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Főtanúsítvány eltávolításakor a főtanúsítványból létrehozott tanúsítvánnyal rendelkező ügyfelek nem fognak tudni hitelesítést végezni, így csatlakozni sem. Ha azt szeretné, hogy az ügyfelek hitelesítést végezhessenek és csatlakozni tudjanak, telepítenie kell egy olyan új ügyféltanúsítványt, amelyet az Azure által megbízhatónak tartott (feltöltött) főtanúsítványból hoztak létre. Ezeknek a lépéseknek a használatához helyileg telepített Azure PowerShell parancsmagok szükségesek a számítógépen (nem Azure Cloud Shell). A főtanúsítványok hozzáadásához használhatja a Azure Portal is.

**Hozzáadás:**

Az Azure-ba legfeljebb 20 főtanúsítványt tölthet fel .cer fájl formájában. A következő lépések segítséget nyújtanak a főtanúsítvány hozzáadásához. 

1. Készítse elő a .cer fájlt feltöltésre:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Töltse fel a fájlt. Egyszerre csak egy fájlt tölthet fel.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. A tanúsítványfájl sikeres feltöltésének ellenőrzése:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**Eltávolítás:**

1. Deklarálja a változókat. Módosítsa a példában szereplő változókat úgy, hogy az megfeleljen az eltávolítani kívánt tanúsítványnak.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. Távolítsa el a tanúsítványt.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. A következő példával meggyőződhet arról, hogy sikeresen megtörtént a tanúsítvány eltávolítása.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>Ügyféltanúsítvány visszavonása vagy visszaállítása

Az ügyféltanúsítványokat vissza lehet vonni. A tanúsítvány-visszavonási lista lehetővé teszi a pont – hely kapcsolat szelektív megtagadását az egyes Ügyféltanúsítványok alapján. Ez a folyamat eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonása esetén a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható hitelesítésre.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

**Visszavonás:**

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkat [a tanúsítványok ujjlenyomatának lekérését ismertető útmutatóban](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate) találhat.

1. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el az összes szóközt, hogy egy folyamatos sztringet kapjon. Ez a sztring a következő lépésben változóként van deklarálva.

1. Deklarálja a változókat. Győződjön meg róla, hogy az előző lépésben lekért ujjlenyomatot deklarálja.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára. Az ujjlenyomat hozzáadása után a „Sikeres” üzenet jelenik meg.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. Ellenőrizze, hogy az ujjlenyomat bekerült-e a visszavont tanúsítványok listájába.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. Az ujjlenyomat hozzáadását követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak majd arról, hogy a tanúsítvány már nem érvényes.

**A következő visszaállítása:**

Vissza is állíthatja az ügyféltanúsítványok érvényességét. Ehhez törölni kell az ujjlenyomatukat a visszavont ügyféltanúsítványok listájából.

1. Deklarálja a változókat. Győződjön meg arról, hogy a megfelelő, a visszaállítani kívánt tanúsítványhoz tartozó ujjlenyomatot deklarálta.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Törölje a tanúsítvány ujjlenyomatát a visszavont tanúsítványok listájából.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. Ellenőrizze, hogy megtörtént-e az ujjlenyomat eltávolítása a listából.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

További pont – hely információk: [VPN Gateway pont – hely típusú GYIK](vpn-gateway-vpn-faq.md#P2S)

## <a name="next-steps"></a>Következő lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](../index.yml). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/network-overview.md).

A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.