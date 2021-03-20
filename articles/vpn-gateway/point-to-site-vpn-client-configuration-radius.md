---
title: 'Azure VPN Gateway: létrehozás & VPN-ügyfél konfigurációs fájljainak telepítése – P2S RADIUS-kapcsolatok'
description: Windows, OS X és Linux VPN-ügyfél konfigurációs fájljainak létrehozása a RADIUS-hitelesítést használó kapcsolatokhoz.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e6d811e19bb19c8c8bf96764cfcca2b1294f4a85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91440066"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>VPN-ügyfél konfigurációs fájljainak létrehozása és telepítése P2S RADIUS-hitelesítéshez

Ha pont – hely (P2S) használatával szeretne csatlakozni egy virtuális hálózathoz, konfigurálnia kell azt az eszközt, amelyhez kapcsolódni fog. P2S VPN-kapcsolatokat hozhat létre Windows, OS X és Linux rendszerű eszközökről. 

RADIUS-hitelesítés használatakor több hitelesítési lehetőség is létezik: felhasználónév/jelszó hitelesítés, tanúsítványalapú hitelesítés és egyéb hitelesítési típusok. A VPN-ügyfél konfigurációja különbözik az egyes hitelesítések típusaitól. A VPN-ügyfél konfigurálásához olyan ügyfél-konfigurációs fájlokat kell használnia, amelyek tartalmazzák a szükséges beállításokat. Ez a cikk segítséget nyújt a VPN-ügyfél konfigurációjának létrehozásához és telepítéséhez a használni kívánt RADIUS-hitelesítési típushoz.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

A P2S RADIUS-hitelesítés konfigurációs munkafolyamata a következő:

1. [Állítsa be az Azure VPN Gatewayt a P2S-kapcsolathoz](point-to-site-how-to-radius-ps.md).
2. [A RADIUS-kiszolgáló beállítása a hitelesítéshez](point-to-site-how-to-radius-ps.md#radius). 
3. **Szerezze be a VPN-ügyfél konfigurációját a választott hitelesítési lehetőséghez, és használja a VPN-ügyfél beállításához** (ez a cikk).
4. [Fejezze be a P2S konfigurációját, és kapcsolódjon](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Ha a VPN-ügyfél konfigurációs profiljának létrehozása után megváltozik a pont – hely VPN-konfiguráció, például a VPN protokoll típusa vagy a hitelesítés típusa, új VPN-ügyfél-konfigurációt kell előkészítenie és telepítenie a felhasználói eszközökön.
>
>

A cikk fejezeteinek használatához először döntse el, hogy milyen típusú hitelesítést kíván használni: felhasználónév/jelszó, tanúsítvány vagy más típusú hitelesítés. Mindegyik szakasz a Windows, az OS X és a Linux rendszerre vonatkozó lépésekkel rendelkezik (ebben az időben csak korlátozott lépések érhetők el).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Felhasználónév/jelszó hitelesítése

A Felhasználónév/jelszó hitelesítést a Active Directory használatára vagy nem a Active Directory használatára is konfigurálhatja. Mindkét esetben győződjön meg arról, hogy az összes csatlakozó felhasználónak van felhasználóneve/jelszó hitelesítő adatai, amelyek a RADIUS használatával hitelesíthetők.

A Felhasználónév/jelszó hitelesítés konfigurálásakor csak a EAP-MSCHAPv2 username/Password hitelesítési protokoll konfigurációját lehet létrehozni. A parancsokban a `-AuthenticationMethod` `EapMSChapv2` .

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a> 1. VPN-ügyfél konfigurációs fájljainak előállítása

A VPN-ügyfél konfigurációs fájljait a Azure Portal használatával vagy Azure PowerShell használatával hozhatja meg.

#### <a name="azure-portal"></a>Azure Portal

1. Navigáljon a virtuális hálózati átjáróhoz.
2. Kattintson **a pont – hely konfiguráció** elemre.
3. Kattintson a **VPN-ügyfél letöltése** elemre.
4. Válassza ki az ügyfelet, és töltse ki a kért adatokat.
5. Kattintson a **Letöltés** gombra a. zip fájl létrehozásához.
6. A. zip-fájl letöltése, jellemzően a letöltések mappájába történik.

#### <a name="azure-powershell"></a>Azure PowerShell

VPN-ügyfél konfigurációs fájljainak előállítása Felhasználónév/jelszó hitelesítéssel való használatra. A VPN-ügyfél konfigurációs fájljait a következő parancs használatával hozhatja meg:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
A parancs futtatása egy hivatkozást ad vissza. A **VpnClientConfiguration.zip** letöltéséhez másolja és illessze be a webböngészőre mutató hivatkozást. Bontsa ki a fájlt a következő mappák megtekintéséhez: 
 
* **WindowsAmd64** és **WindowsX86**: ezek a mappák a Windows 64 bites és a 32 bites telepítési csomagokat tartalmazzák. 
* **Általános**: Ez a mappa általános információkat tartalmaz, amelyeket a saját VPN-ügyfél konfigurációjának létrehozásához használ. Ehhez a mappához nem szükséges felhasználónevet és jelszót használó hitelesítési konfiguráció.
* **Mac**: Ha a virtuális hálózati átjáró létrehozásakor beállította a IKEv2-t, egy **Mac** nevű mappát fog látni, amely egy **mobileconfig** -fájlt tartalmaz. Ezt a fájlt használja a Mac-ügyfelek konfigurálásához.

Ha már létrehozott ügyfél-konfigurációs fájlokat, a parancsmag használatával kérheti le őket `Get-AzVpnClientConfiguration` . Ha azonban módosítja a P2S VPN-konfigurációját, például a VPN protokoll típusát vagy a hitelesítési típust, a konfiguráció nem frissül automatikusan. `New-AzVpnClientConfiguration`Új konfigurációs Letöltés létrehozásához a parancsmagot kell futtatnia.

A korábban létrehozott ügyfél-konfigurációs fájlok lekéréséhez használja a következő parancsot:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. VPN-ügyfelek konfigurálása

A következő VPN-ügyfeleket állíthatja be:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux alapú strongswan használatával](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Windows VPN-ügyfél beállítása

Ugyanazt a VPN-ügyfél-konfigurációs csomagot használhatja minden Windows-ügyfélszámítógépen, ha a verzió megfelel az ügyfél architektúrájának. A támogatott ügyféloldali operációs rendszerek listáját lásd a [Gyakori kérdések](vpn-gateway-vpn-faq.md#P2S)című témakörben.

A következő lépésekkel konfigurálhatja a natív Windows VPN-ügyfelet a tanúsítványalapú hitelesítéshez:

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzor-architektúra esetén válassza a **VpnClientSetupAmd64** telepítőcsomagot. 32 bites processzor-architektúra esetén válassza a **VpnClientSetupX86** telepítőcsomagot. 
2. A csomag telepítéséhez kattintson rá duplán. Ha egy SmartScreen előugró ablak jelenik meg, válassza a **További információ**  >  **futtatása egyébként** lehetőséget.
3. Az ügyfélszámítógépen keresse meg a **hálózati beállítások** elemet, és válassza a **VPN** lehetőséget. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Mac (OS X) VPN-ügyfél beállítása

1. Válassza ki a **VpnClientSetup mobileconfig** fájlt, és küldje el az egyes felhasználók számára. E-mailt vagy más módszert használhat.

2. Keresse meg a **mobileconfig** -fájlt a Mac számítógépen.

   ![A mobileconfig fájljának helye](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Választható lépés – ha egyéni DNS-t szeretne megadni, adja hozzá a következő sorokat a **mobileconfig** -fájlhoz:

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. A telepítéséhez kattintson duplán a profilra, majd válassza a **Folytatás** lehetőséget. A profil neve megegyezik a virtuális hálózat nevével.

   ![Telepítési üzenet](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Válassza a **tovább** lehetőséget, hogy megbízzon a profil küldője számára, és folytassa a telepítést.

   ![Megerősítő üzenet](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. A profil telepítése során lehetősége van a VPN-hitelesítés felhasználónevének és jelszavának megadására. Ezt az információt nem kötelező megadni. Ha így tesz, a rendszer menti az adatokat, és automatikusan használja a kapcsolatok indításakor. A folytatáshoz válassza a **telepítés** lehetőséget.

   ![A VPN-hez tartozó Felhasználónév és jelszó mezők](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Adja meg a profil telepítéséhez szükséges jogosultságok felhasználónevét és jelszavát a számítógépén. Válassza az **OK** lehetőséget.

   ![A profil telepítéséhez használt Felhasználónév és jelszó mezők](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. A profil telepítése után ez látható a **profilok** párbeszédpanelen. Ezt a párbeszédpanelt később is megnyithatja a **Rendszerbeállítások** közül.

   !["Profilok" párbeszédpanel](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. A VPN-kapcsolat eléréséhez nyissa meg a **hálózat** párbeszédpanelt a **Rendszerbeállítások** területen.

   ![Rendszerbeállítások ikonjai](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. A VPN **-kapcsolat IkeV2-VPN-** ként jelenik meg. A nevet a **mobileconfig** fájl frissítésével módosíthatja.

    ![A VPN-kapcsolat részletei](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Válassza a **hitelesítési beállítások** lehetőséget. A listában válassza a **username (Felhasználónév** ) lehetőséget, és adja meg a hitelesítő adatait. Ha korábban adta meg a hitelesítő adatokat, a rendszer automatikusan kiválasztja a **felhasználónevet** a listában, a Felhasználónév és a jelszó pedig előre fel van töltve. A beállítások mentéséhez kattintson **az OK gombra** .

    ![Képernyőkép, amely a "username" beállítással a "hitelesítő beállítások" legördülő listát jeleníti meg.](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. A **hálózat** párbeszédpanelen a módosítások mentéséhez kattintson az **alkalmaz** gombra. A kapcsolat kezdeményezéséhez válassza a **Kapcsolódás** lehetőséget.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Linux VPN-ügyfél beállítása a alapú strongswan-on keresztül

Az alábbi utasítások a alapú strongswan 5.5.1 használatával készültek az Ubuntu-17.0.4. A Linux-és a alapú strongswan-verziótól függően a tényleges képernyők különbözőek lehetnek.

1. A példában a parancs futtatásával nyissa meg a **terminált** a **alapú strongswan** és a hálózati kezelő telepítéséhez. Ha a szolgáltatáshoz kapcsolódó hibaüzenetet kap `libcharon-extra-plugins` , cserélje le a következőre: `strongswan-plugin-eap-mschapv2` .

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Válassza a **Network Manager** ikont (felfelé mutató nyíl/lefelé mutató nyíl), majd válassza a **Kapcsolatok szerkesztése** lehetőséget.

   !["Kapcsolatok szerkesztése" kijelölés a hálózatkezelő](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Új kapcsolatok létrehozásához kattintson a **Hozzáadás** gombra.

   ![A "Hozzáadás" gomb a kapcsolatok számára](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Válassza ki az **IPSec/IKEv2 (alapú strongswan)** elemet a legördülő menüből, majd válassza a **Létrehozás** lehetőséget. Ebben a lépésben átnevezheti a kapcsolatokat.

   ![A kapcsolattípus kiválasztása](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Nyissa meg a **VpnSettings.xml** fájlt a letöltött ügyfél-konfigurációs fájlok **általános** mappájából. Keresse meg a nevű címkét `VpnServer` , és másolja a nevet a (z) és a (z `azuregateway` ) végződéssel kezdődően `.cloudapp.net` .

   ![A VpnSettings.xml fájl tartalma](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Illessze be ezt a nevet az új VPN-kapcsolat **címe** mezőbe az **átjáró** szakaszban. Ezután válassza ki a mappa ikont a **tanúsítvány** mező végén, keresse meg az **általános** mappát, és válassza ki a **VpnServerRoot** fájlt.
7. A kapcsolatok **ügyfél** szakaszában válassza az **EAP** **hitelesítéshez** lehetőséget, majd adja meg felhasználónevét és jelszavát. Előfordulhat, hogy a jobb oldalon a Zárolás ikonra kell kiválasztania az adatok mentéséhez. Ezután válassza a **Mentés** lehetőséget.

   ![Kapcsolatok beállításainak szerkesztése](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Válassza a **Network Manager** ikont (felfelé mutató nyíl vagy lefelé mutató nyíl), és vigye a kurzort a **VPN-kapcsolatokra**. Megjelenik a létrehozott VPN-kapcsolat. A kapcsolódás kezdeményezéséhez válassza ki azt.

   !["VPN RADIUS" kapcsolat a Network Managerben](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Tanúsítványhitelesítés
 
Az EAP-TLS protokollt használó RADIUS-tanúsítvány hitelesítéséhez VPN-ügyfél konfigurációs fájljait is létrehozhat. A vállalat által kiállított tanúsítvány általában a VPN-felhasználók hitelesítésére szolgál. Győződjön meg arról, hogy az összes csatlakozó felhasználónak van egy tanúsítványa telepítve az eszközeire, és hogy a RADIUS-kiszolgáló érvényesítheti a tanúsítványt.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

A parancsokban a `-AuthenticationMethod` `EapTls` . A tanúsítvány hitelesítése során az ügyfél érvényesíti a RADIUS-kiszolgálót a tanúsítványának ellenőrzésével. `-RadiusRootCert` a a RADIUS-kiszolgáló ellenőrzéséhez használt főtanúsítványt tartalmazó. cer fájl.

Minden VPN-ügyfél eszközön telepítve kell lennie egy telepített ügyféltanúsítványt. Előfordulhat, hogy egy Windows-eszköz több ügyféltanúsítványt is tartalmaz. A hitelesítés során ez egy előugró párbeszédpanelt is eredményezhet, amely felsorolja az összes tanúsítványt. A felhasználónak ezután ki kell választania a használni kívánt tanúsítványt. A megfelelő tanúsítvány kiszűrhető úgy, hogy megadja azt a főtanúsítványt, amelyhez az ügyféltanúsítványnek láncot kell használnia. 

`-ClientRootCert` a főtanúsítványt tartalmazó. cer fájl. Ez egy opcionális paraméter. Ha a-ból csatlakozni kívánó eszköz csak egy ügyféltanúsítványt tartalmaz, nem kell megadnia ezt a paramétert.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. VPN-ügyfél konfigurációs fájljainak előállítása

VPN-ügyfél konfigurációs fájljainak előállítása tanúsítványalapú hitelesítéssel való használatra. A VPN-ügyfél konfigurációs fájljait a következő parancs használatával hozhatja meg:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

A parancs futtatása egy hivatkozást ad vissza. A VpnClientConfiguration.zip letöltéséhez másolja és illessze be a webböngészőre mutató hivatkozást. Bontsa ki a fájlt a következő mappák megtekintéséhez:

* **WindowsAmd64** és **WindowsX86**: ezek a mappák a Windows 64 bites és a 32 bites telepítési csomagokat tartalmazzák. 
* **GenericDevice**: Ez a mappa a saját VPN-ügyfél konfigurációjának létrehozásához használt általános információkat tartalmazza.

Ha már létrehozott ügyfél-konfigurációs fájlokat, a parancsmag használatával kérheti le őket `Get-AzVpnClientConfiguration` . Ha azonban módosítja a P2S VPN-konfigurációját, például a VPN protokoll típusát vagy a hitelesítési típust, a konfiguráció nem frissül automatikusan. `New-AzVpnClientConfiguration`Új konfigurációs Letöltés létrehozásához a parancsmagot kell futtatnia.

A korábban létrehozott ügyfél-konfigurációs fájlok lekéréséhez használja a következő parancsot:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. VPN-ügyfelek konfigurálása

A következő VPN-ügyfeleket állíthatja be:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (támogatott, a cikkeknek még nincsenek lépései)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Windows VPN-ügyfél beállítása

1. Válasszon ki egy konfigurációs csomagot, és telepítse azt az ügyfélszámítógépen. 64 bites processzor-architektúra esetén válassza a **VpnClientSetupAmd64** telepítőcsomagot. 32 bites processzor-architektúra esetén válassza a **VpnClientSetupX86** telepítőcsomagot. Ha egy SmartScreen előugró ablak jelenik meg, válassza a **További információ**  >  **futtatása egyébként** lehetőséget. A csomagot mentheti is, így más ügyfélszámítógépekre is telepítheti.
2. Minden ügyfélhez Ügyféltanúsítvány szükséges a hitelesítéshez. Telepítse az ügyféltanúsítványt. További információ az Ügyféltanúsítványok használatáról: [Ügyféltanúsítványok a pont – hely](vpn-gateway-certificates-point-to-site.md)kapcsolatokhoz. A létrehozott tanúsítvány telepítéséhez tekintse [meg a tanúsítvány telepítése Windows-ügyfeleken](point-to-site-how-to-vpn-client-install-azure-cert.md)című témakört.
3. Az ügyfélszámítógépen keresse meg a **hálózati beállítások** elemet, és válassza a **VPN** lehetőséget. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Mac (OS X) VPN-ügyfél beállítása

Minden olyan Mac-eszközhöz létre kell hoznia egy külön profilt, amely csatlakozik az Azure-beli virtuális hálózathoz. Ennek az az oka, hogy ezeknek az eszközöknek a felhasználói tanúsítványt kell megadniuk a profilban való hitelesítéshez. Az **általános** mappa a profilok létrehozásához szükséges összes információt tartalmazhatja:

* **VpnSettings.xml** olyan fontos beállításokat tartalmaz, mint a kiszolgáló címe és az alagút típusa.
* A **VpnServerRoot. cer** tartalmazza a P2S-kapcsolat beállítása során a VPN-átjáró ellenőrzéséhez szükséges főtanúsítványt.
* A **RadiusServerRoot. cer** tartalmazza azt a főtanúsítványt, amely a RADIUS-kiszolgáló hitelesítéshez való ellenőrzéséhez szükséges.

A következő lépésekkel konfigurálhatja a natív VPN-ügyfelet Mac számítógépeken a tanúsítványalapú hitelesítéshez:

1. Importálja a **VpnServerRoot** és a **RadiusServerRoot** főtanúsítványokat a Mac számítógépére. Másolja az egyes fájlokat a Mac számítógépére, és kattintson rá duplán, majd válassza a **Hozzáadás** lehetőséget.

   ![Az VpnServerRoot-tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Az RadiusServerRoot-tanúsítvány hozzáadása](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Minden ügyfélhez Ügyféltanúsítvány szükséges a hitelesítéshez. Telepítse az ügyféltanúsítványt az ügyfél-eszközön.
3. Nyissa meg a **hálózat** párbeszédpanelt a **hálózati beállítások** területen. Ezzel a beállítással **+** új VPN-ügyfélkapcsolati profilt hozhat létre az Azure-beli virtuális hálózattal létesített P2S-kapcsolathoz.

   A **csatoló** értéke **VPN**, a **VPN-típus** értéke pedig **IKEv2**. Adja meg a profil nevét a **szolgáltatásnév** mezőben, majd válassza a **Létrehozás** lehetőséget a VPN-ügyfél kapcsolati profiljának létrehozásához.

   ![Csatoló és szolgáltatásnév adatai](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. Az **általános** mappában, a **VpnSettings.xml** fájlból másolja a **VpnServer** címke értékét. Illessze be ezt az értéket a profil **kiszolgálói címe** és **Távoli azonosító** mezőibe. Hagyja üresen a **helyi azonosító** mezőt.

   ![Kiszolgáló adatai](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Válassza a **hitelesítési beállítások**, majd a **tanúsítvány** elemet. 

   ![Hitelesítési beállítások](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Kattintson a **kiválasztás** elemre a hitelesítéshez használni kívánt tanúsítvány kiválasztásához.

   ![Tanúsítvány kiválasztása hitelesítéshez](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Válasszon ki egy identitást** a tanúsítványok listájának megjelenítéséhez. Válassza ki a megfelelő tanúsítványt, majd kattintson a **Continue (folytatás**) gombra.

   !["Identitás kiválasztása" lista](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. A **helyi azonosító** mezőben adja meg a tanúsítvány nevét (a 6. lépésből). Ebben a példában ez a **ikev2Client.com**. Ezután kattintson az **Apply (alkalmaz** ) gombra a módosítások mentéséhez.

   !["Helyi azonosító" mező](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. A **hálózat** párbeszédpanelen válassza az **alkalmaz** lehetőséget az összes módosítás mentéséhez. Ezután válassza a **Kapcsolódás** lehetőséget, hogy elindítsa a P2S-kapcsolatot az Azure-beli virtuális hálózattal.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Más hitelesítési típusok vagy protokollok használata

Ha más hitelesítési típust (például OTP) szeretne használni, vagy más hitelesítési protokollt szeretne használni (például az EAP-MSCHAPv2 helyett az PEAP-MSCHAPv2-t), létre kell hoznia a saját VPN-ügyfél konfigurációs profilját. A profil létrehozásához olyan információra van szüksége, mint például a virtuális hálózati átjáró IP-címe, az alagút típusa és a felosztott alagút útvonala. Ezt az információt a következő lépésekkel érheti el:

1. A `Get-AzVpnClientConfiguration` EapMSChapv2 VPN-ügyfél konfigurációjának létrehozásához használja a parancsmagot.

2. Bontsa ki a VpnClientConfiguration.zip fájlt, és keresse meg a **GenericDevice** mappát. Hagyja figyelmen kívül a 64 bites és a 32 bites architektúrák Windows-telepítőit tartalmazó mappákat.
 
3. A **GenericDevice** mappa tartalmaz egy **VpnSettings** nevű XML-fájlt. Ez a fájl tartalmazza az összes szükséges információt:

   * **VpnServer**: az Azure VPN Gateway teljes tartományneve. Ez az a címe, amelyhez az ügyfél csatlakozik.
   * **VpnType**: a kapcsolódáshoz használt bújtatási típus.
   * **Útvonalak**: a profilban konfigurálni kívánt útvonalak, hogy csak az Azure-beli virtuális hálózathoz kötött forgalom legyen elküldve a P2S-alagúton keresztül.
   
   A **GenericDevice** mappában található egy **VpnServerRoot** nevű. cer fájl is. Ez a fájl tartalmazza az Azure VPN Gateway P2S-kapcsolat beállítása során történő ellenőrzéséhez szükséges főtanúsítványt. Telepítse a tanúsítványt minden olyan eszközre, amely csatlakozni fog az Azure-beli virtuális hálózathoz.

## <a name="next-steps"></a>Következő lépések

A [P2S-konfiguráció befejezéséhez](point-to-site-how-to-radius-ps.md)térjen vissza a cikkhez.

A P2S hibaelhárítási információit lásd: az [Azure pont – hely kapcsolatok hibaelhárítása](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
