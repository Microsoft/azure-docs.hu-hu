---
title: 'VPN Gateway: VPN-ügyfél az OpenVPN protokoll P2S kapcsolataihoz: Azure AD-hitelesítés'
description: Megtudhatja, hogyan használhatja a P2S VPN-t a VNet való csatlakozásra az Azure AD-hitelesítés használatával.
services: vpn-gateway
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 3dbdfb461bc2ae79439f39df3efbc2c564782110
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548393"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>VPN-ügyfél konfigurálása P2S OpenVPN protokollt használó kapcsolatokhoz: Azure AD-hitelesítés

Ebből a cikkből megtudhatja, hogyan konfigurálhat VPN-ügyfelet egy virtuális hálózathoz pont – hely VPN és Azure Active Directory hitelesítés használatával való csatlakozáshoz. Az Azure AD-vel való kapcsolat és hitelesítés előtt először konfigurálnia kell az Azure AD-bérlőt. További információ: [Azure ad-bérlő konfigurálása](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN® protokoll-kapcsolatok esetén támogatott.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Ügyféloldali profilok használata

A csatlakozáshoz le kell töltenie az Azure VPN-ügyfelet, és konfigurálnia kell egy VPN-ügyféloldali profilt minden olyan számítógépen, amely csatlakozni szeretne a VNet. Létrehozhat egy ügyféloldali profilt a számítógépen, exportálhatja, majd importálhatja további számítógépekre.

### <a name="to-download-the-azure-vpn-client"></a>Az Azure VPN-ügyfél letöltése

Ezzel a [hivatkozással](https://go.microsoft.com/fwlink/?linkid=2117554) töltheti le az Azure VPN-ügyfelet. Győződjön meg arról, hogy az Azure VPN-ügyfél jogosult a háttérben való futtatásra. Az engedély megadásához/engedélyezéséhez kövesse az alábbi lépéseket:

1. Lépjen a Start menüre, majd válassza a beállítások > adatvédelem > háttérbeli alkalmazások lehetőséget.
2. A háttérben futó alkalmazások területen győződjön meg arról, hogy **az alkalmazások háttérben való futtatása** be van kapcsolva.
3. Az válassza ki, hogy mely alkalmazások futhatnak a háttérben, kapcsolja be az Azure VPN-ügyfél beállításait a **be** értékre.

  ![engedéllyel](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Tanúsítvány alapú ügyféloldali profil létrehozása

Tanúsítványalapú profillal való munka esetén győződjön meg arról, hogy a megfelelő tanúsítványok telepítve vannak az ügyfélszámítógépen. További információ a tanúsítványokról: [Ügyféltanúsítványok telepítése](certificates-point-to-site.md).

  ![tanúsítvány](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>RADIUS-ügyfél profiljának létrehozása

  ![RADIUS-](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> A kiszolgáló titkos kulcsa exportálható a P2S VPN-ügyfél profiljában.  Az ügyféloldali profilok exportálásával kapcsolatban [itt](about-vpn-profile-download.md)talál útmutatást.
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Ügyféloldali profil exportálása és terjesztése

Ha van egy működő profilja, és el kell terjesztenie más felhasználóknak, a következő lépésekkel exportálhatja:

1. Jelölje ki az exportálni kívánt VPN-ügyféloldali profilt, majd válassza a **...**, majd az **Exportálás** lehetőséget.

    ![A képernyőképen a menüből kiválasztott exportálás látható.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Válassza ki azt a helyet, ahová a profilt menteni szeretné, hagyja meg a fájl nevét, majd válassza a **Mentés** lehetőséget az XML-fájl mentéséhez.

    ![Képernyőfelvétel: a Save as (Mentés másként) párbeszédpanel, amelyen megadhatja a fájl nevét.](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Ügyféloldali profil importálása

1. A lapon válassza az **Importálás** lehetőséget.

    ![A képernyőképen a plusz menüből kiválasztott importálás látható.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Keresse meg a profil XML-fájlját, és jelölje ki. A fájl kijelölése után válassza a **Megnyitás** lehetőséget.

    ![Képernyőfelvétel: megnyílik egy megnyitott párbeszédpanel, ahol kiválaszthat egy fájlt.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Adja meg a profil nevét, majd válassza a **Mentés** lehetőséget.

    ![Képernyőfelvétel: a hozzáadott kapcsolatok neve és a Mentés gomb kiválasztva.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Válassza a **Csatlakozás** lehetőséget a VPN-hez való csatlakozáshoz.

    ![A képernyőképen az imént létrehozott kapcsolathoz tartozó csatlakoztatás gomb látható.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. A csatlakozás után az ikon zöldre vált, és a rendszer a **csatlakozást**.

    ![A képernyőfelvételen a kapcsolat bontása lehetőséggel összekapcsolt állapotban látható.](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Ügyféloldali profil törlése

1. Válassza ki a törölni kívánt ügyfél-profil melletti ellipsziseket. Ezután válassza az **Eltávolítás** lehetőséget.

    ![A képernyőképen látható a kijelölés eltávolítása a menüből.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Válassza az **Eltávolítás** elemet a törléshez.

    ![A képernyőképen egy megerősítő párbeszédpanel jelenik meg, amelyen törölhető vagy megszakítható a lehetőség.](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Kapcsolat létrehozása

1. Az oldalon válassza a **+** , majd a **+ Hozzáadás** elemet.

    ![A képernyőképen a Hozzáadás lehetőség látható a plusz menüből.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Adja meg a kapcsolatok adatait. Ha nem tudja biztosan az értékeket, forduljon a rendszergazdához. Az értékek kitöltése után válassza a **Mentés** lehetőséget.

    ![Képernyőfelvétel: ablaktábla, ahol megadhatja a szükséges értékeket.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Válassza a **Csatlakozás** lehetőséget a VPN-hez való csatlakozáshoz.

    ![Képernyőfelvétel: a kapcsolathoz tartozó csatlakoztatás gomb.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Válassza ki a megfelelő hitelesítő adatokat, majd kattintson a **Continue (folytatás**) gombra.

    ![A képernyőképen a bejelentkezés párbeszédpanel látható.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. A sikeres csatlakozás után az ikon zöldre vált, és a rendszer a **csatlakozást** fogja mondani.

    ![Képernyőfelvétel: a kapcsolat egy csatlakoztatott állapotban látható.](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Automatikus kapcsolódás

Ezekkel a lépésekkel konfigurálhatja a kapcsolatot úgy, hogy automatikusan kapcsolódjon az Always-on.

1. A VPN-ügyfél kezdőlapján válassza a **VPN-beállítások** lehetőséget.

    ![Képernyőfelvétel: V P N kapcsolatok, ahol a V P N beállítások közül választhat.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Válassza az **Igen** lehetőséget az alkalmazások váltása párbeszédablakban.

    ![A képernyőképen az alkalmazások váltására vonatkozó ellenőrzési üzenet látható.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Győződjön meg arról, hogy a beállítani kívánt kapcsolat még nincs csatlakoztatva, majd jelölje ki a profilt, majd jelölje be az **Automatikus csatlakozás** jelölőnégyzetet.

    ![Képernyőfelvétel: a beállítások párbeszédpanel, ahol kiválaszthatja az automatikus kapcsolat lehetőséget.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. A VPN-kapcsolat indításához válassza a **Csatlakozás** lehetőséget.

    ![A képernyőfelvétel a kapcsolat gombra mutat.](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Kapcsolatok problémáinak diagnosztizálása

1. A kapcsolódási problémák diagnosztizálásához használhatja a **Diagnosztizálás** eszközt. Válassza a **...** elemet a diagnosztizálni kívánt VPN-kapcsolat mellett a menü megjelenítéséhez. Ezután válassza a **Diagnosztizálás** lehetőséget.

    ![A képernyőképen a menüből kiválasztott diagnosztika látható.](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. A **kapcsolatok tulajdonságai** lapon válassza a **diagnosztika futtatása** lehetőséget.

    ![A következő képernyőfelvételen látható a kapcsolatok futtatásának diagnosztizálása gomb.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Jelentkezzen be a hitelesítő adataival.

    ![Képernyőfelvétel: a művelet bejelentkezési párbeszédpanelje.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. A diagnosztika eredményeinek megtekintése.

    ![Képernyőkép: a diagnosztika eredményeit jeleníti meg.](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>GYIK

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Hogyan DNS-utótagokat hozzáadni a VPN-ügyfélhez?

Megváltoztathatja a letöltött profil XML-fájlját, **\<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes>** és hozzáadhatja a címkéket

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Hogyan egyéni DNS-kiszolgálókat hozzáadni a VPN-ügyfélhez?

Megváltoztathatja a letöltött profil XML-fájlját, **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** és hozzáadhatja a címkéket

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> Az OpenVPN Azure AD-ügyfél DNS-névfeloldási házirend-tábla (NRPT) bejegyzéseket használ, ami azt jelenti, hogy a DNS-kiszolgálók nem lesznek felsorolva a kimenetében `ipconfig /all` . A használaton kívüli DNS-beállítások megerősítéséhez tekintse meg a [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy) a PowerShellben című részt.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Hogyan egyéni útvonalakat hozzáadni a VPN-ügyfélhez?

Megváltoztathatja a letöltött profil XML-fájlját, **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** és hozzáadhatja a címkéket

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```
### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-force-tunnel"></a>Hogyan a VPN-alagút felé irányuló összes forgalmat (kényszerített alagút)?

Megváltoztathatja a letöltött profil XML-fájlját, **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** és hozzáadhatja a címkéket

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>0.0.0.0</destination><mask>1</mask>
        </route>
        <route>
            <destination>128.0.0.0</destination><mask>1</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Hogyan blokk (kizárás) útvonalakat a VPN-ügyféltől?

Megváltoztathatja a letöltött profil XML-fájlját, **\<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes>** és hozzáadhatja a címkéket

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```
### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Importálható a profil parancssori parancssorból?

A profilt parancssorból is importálhatja, ha a letöltött **azurevpnconfig.xml** fájlt a **%USERPROFILE%\appdata\local\packages\ Microsoft.AzureVpn_8wekyb3d8bbwe \localstate** mappába helyezi, és az alábbi parancsot futtatja:

```
azurevpn -i azurevpnconfig.xml 
```
az importálás kényszerítéséhez használja az **-f** kapcsolót is


## <a name="next-steps"></a>Következő lépések

További információ: [Azure Active Directory-bérlő létrehozása az Azure ad-hitelesítést használó P2S nyitott VPN-kapcsolatokhoz](openvpn-azure-ad-tenant.md).