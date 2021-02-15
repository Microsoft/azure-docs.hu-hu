---
title: 'Csatlakozás VNet a P2S VPN & tanúsítványalapú hitelesítés használatával: portál'
titleSuffix: Azure VPN Gateway
description: A Windows, a macOS és a Linux rendszerű ügyfelek biztonságos csatlakoztatása egy Azure-beli virtuális hálózathoz P2S és önaláírt vagy HITELESÍTÉSSZOLGÁLTATÓ által kibocsátott tanúsítványok használatával. Ebben a cikkben az Azure Portalon fogunk dolgozni.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 1c6dad28ada14151b9a1cca0da490e38972ad54d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379165"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Pont – hely VPN-kapcsolat konfigurálása VNet natív Azure tanúsítványalapú hitelesítéssel: Azure Portal

Ez a cikk segítséget nyújt a Windows, Linux vagy macOS rendszerű ügyfelek biztonságos csatlakoztatásához egy Azure-VNet. A pont–hely VPN-kapcsolat akkor hasznos, ha távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról. Pont–hely kapcsolatot is használhat helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet. Pont–hely kapcsolat esetén SSTP (Secure Socket Tunneling Protocol) vagy IKEv2-protokoll használatával jön létre a VPN-kapcsolat. További információkat a pont–hely VPN-ről a [pont–hely VPN-t ismertető](point-to-site-about.md) témakör tartalmaz.

:::image type="content" source="./media\vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Kapcsolódás számítógépről egy Azure VNet-pont – hely kapcsolati diagramhoz":::

További információ a pont – hely VPN-ről: [Tudnivalók a pont – hely VPN-ről](point-to-site-about.md). Ha a Azure PowerShell használatával szeretné létrehozni ezt a konfigurációt, tekintse meg [a pont – hely típusú VPN konfigurálása Azure PowerShell használatával](vpn-gateway-howto-point-to-site-rm-ps.md)című témakört.

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

### <a name="example-values"></a><a name="example"></a>Példaértékek

Az alábbi értékek használatával létrehozhat egy tesztkörnyezetet, vagy segítségükkel értelmezheti a cikkben szereplő példákat:

* **Virtuális hálózat neve:** VNet1
* **Címterület:** 10.1.0.0/16<br>Ebben a példában csak egy címteret használunk. Azonban a virtuális hálózatához több címteret is használhat.
* **Alhálózat neve:** FrontEnd
* **Alhálózati címtartomány:** 10.1.0.0/24
* **Előfizetés:** Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* **Erőforráscsoport:** TestRG1
* **Hely:** USA keleti régiója
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Virtuális hálózati átjáró neve:** VNet1GW
* **Átjáró típusa:** VPN
* **VPN típusa:** útvonalalapú
* **Nyilvános IP-cím neve:** VNet1GWpip
* **Kapcsolat típusa:** pont–hely
* **Ügyfél címkészlet:** 172.16.201.0/24<br>Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, az ügyfélcímkészletből kapnak IP-címet.

## <a name="virtual-network"></a><a name="createvnet"></a>Virtuális hálózat

Ebben a szakaszban egy új virtuális hálózatot hozhat létre.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Virtuális hálózati átjáró

Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

>[!NOTE]
>Az alapszintű átjáró SKU nem támogatja a IKEv2 vagy a RADIUS-hitelesítést. Ha azt tervezi, hogy a Mac-ügyfelek csatlakoznak a virtuális hálózathoz, ne használja az alapszintű SKU-t.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generate-certificates"></a><a name="generatecert"></a>Tanúsítványok előállítása

A tanúsítványokat az Azure a virtuális hálózathoz pont–hely VPN-kapcsolaton keresztül csatlakozó ügyfelek hitelesítésére használja. Amint beszerzett egy főtanúsítványt, a nyilvánoskulcs-adatait [feltölti](#uploadfile) az Azure-ba. Az Azure a főtanúsítványt ettől kezdve „megbízhatónak” tekinti a virtuális hálózathoz pont–hely kapcsolaton keresztüli csatlakozás esetén. Létrehoz ügyféltanúsítványokat is a megbízható főtanúsítványból, majd telepíti őket az összes ügyfélszámítógépeken. Az ügyféltanúsítványt a rendszer az ügyfél hitelesítésére használja, amikor az a VNethez próbál csatlakozni.

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>Főtanúsítvány létrehozása

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>Ügyféltanúsítványok előállítása

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Ügyfélcímkészlet

Az ügyfélcímkészlet megadott magánhálózati IP-címek tartománya. A pont–hely VPN-kapcsolattal csatlakozó ügyfelek ebből a tartományból kapnak dinamikusan IP-címet. Olyan magánhálózati IP-címtartományt használjon, amely nincs átfedésben azzal a helyszíni hellyel, amelyről csatlakozik, vagy azzal a virtuális hálózattal, amelyhez csatlakozik. Ha több protokollt konfigurál, és az SSTP a protokollok egyike, akkor a konfigurált címkészlet egyenlően oszlik meg a konfigurált protokollok között.

1. Miután létrehozta a virtuális hálózati átjárót, navigáljon a virtuális hálózati átjáró lapjának **Beállítások** részéhez. A **Beállítások** területen válassza a **pont – hely konfiguráció** lehetőséget. Válassza a **Konfigurálás most** lehetőséget a konfigurációs lap megnyitásához.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Pont – hely konfiguráció lap" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. A **pont – hely konfiguráció** oldalon a **címkészlet** mezőben adja meg a használni kívánt magánhálózati IP-címtartományt. A VPN-ügyfelek dinamikusan kapnak egy IP-címet a megadott tartományból. A minimális alhálózati maszk 29 bites aktív/passzív és 28 bites az aktív/aktív konfigurációhoz.
1. Folytassa a következő szakasszal a hitelesítési és bújtatási típusok konfigurálásához.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Hitelesítési és bújtatási típusok

Ebben a szakaszban a hitelesítés típusát és a bújtatás típusát konfigurálja. Ha nem látja az **alagút típusát** vagy a **hitelesítési típust**, a **pont – hely konfiguráció** lapon az átjáró az alapszintű SKU-t használja. Az alapszintű termékváltozat nem támogatja az IKEv2- vagy RADIUS-hitelesítést. Ha ezeket a beállításokat szeretné használni, törölnie kell, majd újra létre kell hoznia az átjárót egy másik átjáró-SKU használatával.

### <a name="tunnel-type"></a><a name="tunneltype"></a>Alagúttípus

A **pont – hely konfiguráció** lapon válassza ki az alagút típusát. Az alagút beállításai az OpenVPN, az SSTP és a IKEv2.

* Az Android- és Linux-alapú strongSwan-ügyfél, valamint az iOS- és OS X-alapú natív IKEv2 VPN-ügyfél csak IKEv2-alagutat használ a kapcsolódáshoz.
* A Windows-ügyfelek először a IKEv2 próbálják meg, és ha ez nem sikerül, a rendszer az SSTP-re esik vissza.
* Az OpenVPN-ügyfél használatával kapcsolódhat az OpenVPN-alagút típusához.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Hitelesítéstípus

A **Hitelesítés típusa** beállításnál válassza az **Azure-tanúsítvány** lehetőséget.


## <a name="root-certificate-data"></a><a name="uploadfile"></a>Főtanúsítvány-adatértékek

Ebben a szakaszban a nyilvános legfelső szintű tanúsítványokra vonatkozó adatok feltöltése az Azure-ba. Miután feltöltötte a nyilvános tanúsítványadatokat, az Azure felhasználhatja azon ügyfelek hitelesítéséhez, amelyeken telepítve lett egy, a megbízható főtanúsítványból létrehozott ügyféltanúsítvány.

1. A tanúsítványokat a rendszer hozzáadja a **Főtanúsítvány** szakasz **Pont–hely konfiguráció** lapjához.
1. Győződjön meg arról, hogy Base-64 kódolású X.509 (.cer) fájlként exportálta a főtanúsítványt. Ebben a formátumban kell exportálnia a tanúsítványt, hogy szövegszerkesztővel meg tudja azt nyitni.
1. Nyissa megy a tanúsítványt egy szövegszerkesztővel, például a Jegyzettömbbel. A tanúsítványadatok másolásakor a szöveget egy folyamatos sorként másolja kocsivissza vagy új sor nélkül. A kocsivisszák és az új sorok megjelenítéséhez lehet, hogy módosítania kell a nézetet a szövegszerkesztőben a „Szimbólum megjelenítése/Minden karakter megjelenítése” beállításra. Csak a következő szakaszt másolja egy folyamatos sorként:

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="Tanúsítvány-adatértékek" border="false":::
1. Illessze be a tanúsítványadatokat a **Nyilvános tanúsítványadatok** mezőbe. **Nevezze** el a tanúsítványt, majd kattintson a **Mentés** gombra. Legfeljebb 20 megbízható főtanúsítványt adhat hozzá.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png" alt-text="Tanúsítványfájl beillesztése" border="false":::
1. Válassza a **Mentés** lehetőséget az oldal tetején az összes konfigurációs beállítás mentéséhez.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png" alt-text="Konfiguráció mentése" border="false":::

## <a name="client-certificate"></a><a name="installclientcert"></a>Ügyféltanúsítvány

Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra.

Győződjön meg arról, hogy az ügyféltanúsítványt .pfx fájlként exportálta a teljes tanúsítványlánccal együtt (ez az alapértelmezett beállítás). Egyéb esetben a főtanúsítvány adatai nem lesznek jelen az ügyfélszámítógépen, és az ügyfél nem fogja tudni megfelelően elvégezni a hitelesítést.

A telepítés lépései az [ügyféltanúsítvány telepítésével](point-to-site-how-to-vpn-client-install-azure-cert.md) foglalkozó részben találhatók.

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>VPN-ügyfél konfigurációs csomagja

A VPN-ügyfeleket ügyfél-konfigurációs beállításokkal kell konfigurálni. A VPN-ügyfél konfigurációs csomagja olyan fájlokat tartalmaz, amelyek a VPN-ügyfelek konfigurálására szolgáló beállításokat tartalmaznak, hogy P2S-kapcsolaton keresztül csatlakozzanak egy VNet.

A VPN-ügyfél konfigurációs fájljainak létrehozásával és telepítésével kapcsolatos lépésekért lásd: [VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése natív Azure tanúsítvány-hitelesítési P2S-konfigurációkhoz](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect-to-azure"></a><a name="connect"></a>Csatlakozás az Azure szolgáltatáshoz

### <a name="to-connect-from-a-windows-vpn-client"></a>Csatlakozás Windows VPN-ügyfélről

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Csatlakozás Mac VPN-ügyfélről

A hálózat párbeszédpanelen keresse meg a használni kívánt ügyféloldali profilt, adja meg a beállításokat a [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac), majd válassza a **Kapcsolódás** lehetőséget.

Részletes utasításokért lásd: [install-Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) . Ha nem sikerül a csatlakozás, ellenőrizze, hogy a virtuális hálózati átjáró nem alapszintű SKU-t használ-e. A Mac-ügyfelek nem támogatják az alapszintű SKU-t.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png" alt-text="Mac VPN-ügyfél kapcsolata" border="false":::

## <a name="to-verify-your-connection"></a><a name="verify"></a>A kapcsolat ellenőrzése

Ezek az utasítások Windows-ügyfelekre érvényesek.

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

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Csatlakozás virtuális géphez

Ezek az utasítások Windows-ügyfelekre érvényesek.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Ellenőrizze, hogy létrejött-e a VPN-ügyfél konfigurációs csomagja azután, hogy a DNS-kiszolgáló IP-címei meg lettek adva a virtuális hálózathoz. Ha frissítette a DNS-kiszolgáló IP-címeit, hozzon létre és telepítsen egy új VPN-ügyfélkonfigurációs csomagot.

* Az „ipconfig” használatával ellenőrizze annak a számítógépnek az Ethernet-adapteréhez hozzárendelt IPv4-címet, amelyről a kapcsolatot létesíti. Ha az IP-cím azon virtuális hálózat tartományában található, amelyhez csatlakozni kíván, vagy a VPN-ügyfél címkészletének címtartományában, akkor átfedő címtérről beszélünk. Ilyen átfedés esetén a hálózati forgalom nem éri el az Azure-t, és a helyi hálózaton marad.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Megbízható főtanúsítványok hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Főtanúsítvány eltávolításakor az abból a gyökérből létrehozott tanúsítvánnyal rendelkező ügyfelek nem fognak tudni hitelesítést végezni, így csatlakozni sem. Ha azt szeretné, hogy az ügyfelek hitelesítést végezhessenek és csatlakozni tudjanak, telepítenie kell egy olyan új ügyféltanúsítványt, amelyet az Azure által megbízhatónak tartott (feltöltött) főtanúsítványból hoztak létre.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. Útmutatásért lásd a jelen cikk [Megbízható főtanúsítvány feltöltése](#uploadfile) című szakaszát.

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása

1. A megbízható főtanúsítvány eltávolításához lépjen a virtuális hálózati átjáróhoz tartozó **Pont–hely konfiguráció** lapra.
1. Keresse meg az eltávolítani kívánt tanúsítványt a lap **Főtanúsítvány** szakaszában.
1. Válassza ki a tanúsítvány melletti három pontot, majd válassza az Eltávolítás lehetőséget.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a folyamat eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonása esetén a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható hitelesítésre.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkat [a tanúsítványok ujjlenyomatának lekérését ismertető útmutatóban](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate) találhat.
1. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el az összes szóközt, hogy egy folyamatos sztringet kapjon.
1. Lépjen a virtuális hálózati átjáró **Pont–hely konfiguráció** lapjára. Ez ugyanaz a lap, amelyet a [megbízható főtanúsítvány feltöltéséhez](#uploadfile) használt.
1. A **Visszavont tanúsítványok** szakaszban adjon egy rövid nevet a tanúsítványnak (ennek nem kell megegyeznie a tanúsítvány köznapi nevével).
1. Másolja ki és illessze be az ujjlenyomat sztringjét az **Ujjlenyomat** mezőbe.
1. A rendszer ellenőrzi az ujjlenyomatot, és automatikusan hozzáadja a visszavont tanúsítványok listájához. A képernyőn megjelenik egy üzenet, amely szerint a lista frissítése folyamatban van. 
1. A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak majd arról, hogy a tanúsítvány már nem érvényes.

## <a name="point-to-site-faq"></a><a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

Ez a szakasz a pont – hely konfigurációkra vonatkozó GYIK-információkat tartalmaz. A VPN Gatewayról további információt a [VPN Gateway gyakori kérdések](vpn-gateway-vpn-faq.md) című témakörben talál.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](../index.yml). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/network-overview.md).

A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.
