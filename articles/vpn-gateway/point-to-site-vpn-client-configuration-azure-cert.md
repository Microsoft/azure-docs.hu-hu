---
title: 'A P2S VPN-ügyfél konfigurációs fájljainak létrehozása & telepítése: tanúsítványalapú hitelesítés'
titleSuffix: Azure VPN Gateway
description: Windows, Linux, Linux (alapú strongswan) és macOS X VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése a P2S-tanúsítványok hitelesítéséhez.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/11/2020
ms.author: cherylmc
ms.openlocfilehash: c7b186aa1a6f63b1bc3e9dbefa5001faac967762
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94556164"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>A VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése az Azure natív tanúsítványalapú hitelesítést használó pont–hely kapcsolatokhoz

A VPN-ügyfél konfigurációs fájljai zip-fájlban vannak tárolva. A konfigurációs fájlok biztosítják a natív Windows-, Mac-IKEv2 VPN-vagy Linux-ügyfelek számára, hogy olyan pont – hely kapcsolatokon keresztül csatlakozzanak a virtuális hálózathoz, amelyek natív Azure tanúsítványalapú hitelesítést használnak.

Az ügyfél-konfigurációs fájlok a virtuális hálózat VPN-konfigurációjához vannak jellemzőek. Ha a VPN-ügyfél konfigurációs fájljainak létrehozása után megváltozik a pont – hely VPN-konfiguráció, például a VPN protokoll típusa vagy a hitelesítés típusa, ne felejtsen el új VPN-ügyfél konfigurációs fájlokat készíteni a felhasználói eszközökhöz.

* További információk a pont–hely kapcsolatokról: [Információk a pont–hely VPN-ről](point-to-site-about.md).
* Az OpenVPN-utasításokért lásd: [az OpenVPN konfigurálása a P2S](vpn-gateway-howto-openvpn.md) és az [OpenVPN-ügyfelek konfigurálása](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>VPN-ügyfél konfigurációs fájljainak előállítása

Mielőtt elkezdené, győződjön meg arról, hogy az összes csatlakozó felhasználó rendelkezik a felhasználó eszközére telepített érvényes tanúsítvánnyal. Az ügyféltanúsítványok telepítésével kapcsolatos további információkért lásd: [ügyféltanúsítvány telepítése](point-to-site-how-to-vpn-client-install-azure-cert.md).

Az ügyfél-konfigurációs fájlokat a PowerShell-lel vagy a Azure Portal használatával is létrehozhatja. Bármelyik metódus ugyanazt a zip-fájlt adja vissza. Bontsa ki a fájlt a következő mappák megtekintéséhez:

* **WindowsAmd64** és **WindowsX86**, amelyek tartalmazzák a Windows 32 bites és a 64 bites telepítési csomagokat. A **WindowsAmd64** telepítőcsomag az összes támogatott 64-bites Windows-ügyfélhez használható, nem csak az AMD.
* **Általános, amely** a saját VPN-ügyfél konfigurációjának létrehozásához használt általános információkat tartalmazza. Az általános mappa akkor van megadva, ha a IKEv2 vagy az SSTP + IKEv2 konfigurálva lett az átjárón. Ha csak az SSTP van konfigurálva, akkor az általános mappa nem jelenik meg.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Fájlok előállítása a Azure Portal használatával

1. A Azure Portal navigáljon annak a virtuális hálózatnak a virtuális hálózati átjáróhoz, amelyhez csatlakozni szeretne.
1. A virtuális hálózati átjáró lapon válassza a **pont – hely konfiguráció** lehetőséget.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="A VPN-ügyfél letöltése":::
1. A pont – hely konfiguráció lap tetején válassza a **VPN-ügyfél letöltése** lehetőséget. Az ügyfél-konfigurációs csomag létrehozása néhány percet vesz igénybe.
1. A böngésző azt jelzi, hogy az ügyfél-konfiguráció zip-fájlja elérhető. Neve megegyezik az átjáró nevével. A mappák megtekintéséhez bontsa ki a fájlt.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Fájlok készítése a PowerShell használatával

1. A VPN-ügyfél konfigurációs fájljainak létrehozásakor a "-AuthenticationMethod" értéke "EapTls". A következő paranccsal hozhatja elő a VPN-ügyfél konfigurációs fájljait:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Másolja az URL-címet a böngészőjébe a zip-fájl letöltéséhez, majd bontsa ki a fájlt a mappák megtekintéséhez.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 A natív IKEv2 VPN-ügyfelet manuálisan kell konfigurálnia az Azure-hoz csatlakozó Mac gépek mindegyikén. Az Azure nem biztosít mobilkonfigurációs fájlt a natív Azure-tanúsítványhitelesítéshez. Az **általános** tartalmazza az összes, a konfigurációhoz szükséges információt. Ha nem látja a Generic mappát a letöltésben, valószínűleg nem az IKEv2 lett kiválasztva alagúttípusként. Vegye figyelembe, hogy a VPN Gateway alapszintű SKU nem támogatja a IKEv2. Az IKEv2 kiválasztását követően hozza létre újból a ZIP-fájlt a Generic mappa lekéréséhez.<br>A Generic mappa az alábbi fájlokat tartalmazza:

* **VpnSettings.xml**, amely olyan fontos beállításokat tartalmaz, mint a kiszolgáló címe és az alagút típusa. 
* **VpnServerRoot. cer**, amely tartalmazza az Azure-VPN Gateway ellenőrzéséhez szükséges főtanúsítványt a P2S-kapcsolatok beállítása során.

A következő lépésekkel konfigurálhatja a natív VPN-ügyfelet a Mac számítógépen a tanúsítványalapú hitelesítéshez. Ezeket a lépéseket minden olyan Mac gépen végre kell hajtania, amely csatlakozni fog az Azure-hoz:

1. Importálja a **VpnServerRoot** legfelső szintű tanúsítványát a Mac számítógépére. Ezt úgy teheti meg, hogy átmásolja a fájlt a Mac gépre, és duplán rákattint rá. Válassza a **Hozzáadás** az importáláshoz lehetőséget.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="Képernyőfelvétel a tanúsítványok oldalról":::
  
    >[!NOTE]
    >Ha duplán kattint a tanúsítványra, előfordulhat, hogy nem jeleníti meg a **Hozzáadás** párbeszédpanelt, de a tanúsítvány telepítve van a megfelelő tárolóban. A tanúsítvány kategóriájában a bejelentkezési kulcstartóban tekintheti meg a tanúsítványt.
    >
  
1. Ellenőrizze, hogy telepítette-e az Azure-ba a P2S-beállítások konfigurálásakor feltöltött főtanúsítvány által kiadott ügyféltanúsítványt. Ez eltér az előző lépésben telepített VPNServerRoot. Az ügyféltanúsítvány hitelesítésre szolgál, és kötelező megadni. A tanúsítványok létrehozásával kapcsolatos további információkért lásd: [tanúsítványok létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Az ügyféltanúsítványok telepítésével kapcsolatos információkért lásd: [ügyféltanúsítvány telepítése](point-to-site-how-to-vpn-client-install-azure-cert.md).
1. Nyissa meg a **hálózat** párbeszédpanelt a **hálózati beállítások** területen, és válassza a **"+"** lehetőséget egy új VPN-ügyfél kapcsolati profil létrehozásához az Azure-beli virtuális hálózattal létesített P2S-kapcsolathoz.

   Az **illesztőfelület** értéke "VPN", a **VPN-típus** értéke pedig "IKEv2". Adja meg a profil nevét a **szolgáltatás neve** mezőben, majd válassza a **Létrehozás** lehetőséget a VPN-ügyfél kapcsolati profiljának létrehozásához.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="A képernyőfelvétel megjeleníti a hálózat ablakot, amely lehetőséget ad az interfész kiválasztására, a VPN-típus kiválasztására és a szolgáltatás nevének megadására.":::
1. Az **általános** mappában, a **VpnSettings.xml** fájlból másolja a **VpnServer** címke értékét. Illessze be ezt az értéket a profil **kiszolgáló címe** és a **Távoli azonosító** mezőibe.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="A képernyőképen a kiszolgáló adatai láthatók.":::
1. Válassza a **hitelesítési beállítások** lehetőséget, és válassza a **tanúsítvány** lehetőséget. A **Catalina** esetében válassza a **nincs**, majd a **tanúsítvány** elemet.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="A képernyőképen a hitelesítési beállítások láthatók.":::

   A Catalina esetében válassza a **nincs** , majd a **tanúsítvány** lehetőséget. **Válassza ki** a megfelelő tanúsítványt:
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="Képernyőfelvétel: a hálózati ablak, amely nincs kiválasztva a hitelesítési beállításokra és a tanúsítvány kiválasztására.":::

1. Kattintson a **kijelölés...** lehetőségre. Válassza ki a hitelesítéshez használni kívánt ügyféltanúsítványt. Ez az a tanúsítvány, amelyet a 2. lépésben telepített.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="A képernyőfelvételen a hálózati ablak a hitelesítési beállításokkal látható, ahol kiválaszthat egy tanúsítványt.":::
1. **Válasszon ki egy identitást** a tanúsítványok listájának megjelenítéséhez. Válassza ki a megfelelő tanúsítványt, majd kattintson a **Continue (folytatás**) gombra.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="Képernyőfelvétel: az identitás kiválasztása párbeszédpanel, ahol kiválaszthatja a megfelelő tanúsítványt.":::

1. A **helyi azonosító** mezőben adja meg a tanúsítvány nevét (a 6. lépésből). Ebben a példában ez `ikev2Client.com`. Ezután kattintson az **alkalmaz** gombra a módosítások mentéséhez.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="A képernyőképen az alkalmazás látható.":::
1. A **hálózat** párbeszédpanelen válassza az **alkalmaz** lehetőséget az összes módosítás mentéséhez. Ezután válassza a **Kapcsolódás** lehetőséget, hogy elindítsa a P2S-kapcsolatot az Azure-beli virtuális hálózattal.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (alapú strongswan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>A alapú strongswan telepítése

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Tanúsítványok előállítása

Ha még nem hozott létre tanúsítványokat, kövesse az alábbi lépéseket:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Telepítés és konfigurálás

Az alábbi utasítások az Ubuntu 18.0.4 lettek létrehozva. Az Ubuntu-16.0.10 nem támogatja a alapú strongswan grafikus felhasználói felületét. Ha Ubuntu-16.0.10 szeretne használni, a [parancssort](#linuxinstallcli)kell használnia. Előfordulhat, hogy az alábbi példák nem egyeznek meg a megjelenő képernyőkkel a Linux és a alapú strongswan verziójától függően.

1. A példában a parancs futtatásával nyissa meg a **terminált** a **alapú strongswan** és a hálózati kezelő telepítéséhez.

   ```
   sudo apt install network-manager-strongswan
   ```
1. Válassza a **Beállítások**, majd a **hálózat** lehetőséget. Az **+** új kapcsolatok létrehozásához kattintson a gombra.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="Képernyőfelvétel: a hálózati kapcsolatok lap.":::

1. Válassza az **IPSec/IKEv2 (alapú strongswan)** lehetőséget a menüben, majd kattintson duplán a elemre.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="Képernyőfelvétel: a VPN hozzáadása lap.":::

1. A **VPN hozzáadása** lapon adja meg a VPN-kapcsolat nevét.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="Képernyőfelvétel: válasszon egy kapcsolattípust.":::
1. Nyissa meg a **VpnSettings.xml** fájlt a letöltött ügyfél-konfigurációs fájlokban található **általános** mappából. Keresse meg a **VpnServer** nevű címkét, és másolja a nevet a "azuregateway" kezdetű és a ". cloudapp.net" végződéssel.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="A képernyőképen az Adatmásolás látható.":::
1. Illessze be a nevet az új VPN-kapcsolat **címe** mezőjébe az **átjáró** szakaszban. Ezután válassza ki a mappa ikont a **tanúsítvány** mező végén, keresse meg az **általános** mappát, és válassza ki a **VpnServerRoot** fájlt.
1. A kapcsolatok **ügyfél** szakaszában, a **hitelesítéshez** válassza a **tanúsítvány/titkos kulcs** lehetőséget. A **tanúsítvány** és a **titkos kulcs** esetében válassza ki a korábban létrehozott tanúsítványt és titkos kulcsot. A **Beállítások** területen válassza a **belső IP-cím kérése** lehetőséget. Ezután válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="A képernyőképen egy belső IP-cím kérése látható.":::

1. Kapcsolja **be a következőt:.**

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="A képernyőképen a másolás látható.":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (alapú strongswan CLI)

### <a name="install-strongswan"></a>A alapú strongswan telepítése

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Tanúsítványok előállítása

Ha még nem hozott létre tanúsítványokat, kövesse az alábbi lépéseket:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Telepítés és konfigurálás

1. Töltse le a ügyfele csomagot a Azure Portalról.
1. Bontsa ki a fájlt.
1. Az **általános** mappában másolja vagy helyezze át a **VpnServerRoot. cer fájlba** a **/etc/IPSec.d/cacerts**.
1. A **CP Client. P12** másolása vagy áthelyezése a **/etc/IPSec.d/Private/**-be. Ez a fájl a VPN-átjáróhoz tartozó ügyféltanúsítvány.
1. Nyissa meg a **VpnSettings.xml** fájlt, és másolja az `<VpnServer>` értéket. Ezt az értéket a következő lépésben fogja használni.
1. Módosítsa az alábbi példában szereplő értékeket, majd adja hozzá a példát a **/etc/IPSec.conf** -konfigurációhoz.
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. Adja hozzá a következő értékeket a **/etc/IPSec.Secrets**.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. Futtassa az alábbi parancsot:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Következő lépések

Térjen vissza az eredeti cikkhez, amelyről dolgozott, majd fejezze be a P2S-konfigurációt.

* [PowerShell-konfigurációs lépések](vpn-gateway-howto-point-to-site-rm-ps.md).
* [Azure Portal konfigurációs lépések](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
