---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55fa01d100c60c6411774373428ff4bbd9a56822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80986714"
---
## <a name="windows-clients"></a><a name="windows"></a>Windows-ügyfelek

1. Töltse le és telepítse az OpenVPN-ügyfelet (2,4-es vagy újabb verzió) a hivatalos [OpenVPN webhelyről](https://openvpn.net/index.php/open-source/downloads.html).
2. Töltse le az átjáró VPN-profilját. Ezt a Azure Portal pont – hely konfiguráció lapján teheti meg, vagy a PowerShellben a "New-AzVpnClientConfiguration".
3. Csomagolja ki a profilt. Ezután nyissa meg a *vpnconfig. ovpn* konfigurációs fájlt az OpenVPN mappából a Jegyzettömb használatával.
4. Exportálja a létrehozott pont – hely ügyféltanúsítványt, és töltse fel a P2S-konfigurációba az átjárón. Használja a következő cikk hivatkozásait:

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) utasítások
   
   * [Virtuális WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport) -utasítások
5. Bontsa ki a titkos kulcsot és a Base64 ujjlenyomatot a *. pfx* fájlból. Ezt többféleképpen megteheti. Az OpenSSL használata a gépen egy módszer. A *profileinfo.txt* fájl tartalmazza a titkos kulcsot, valamint a hitelesítésszolgáltató és az ügyféltanúsítvány ujjlenyomatát. Ügyeljen arra, hogy az ügyféltanúsítvány ujjlenyomatát használja.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Nyissa meg *profileinfo.txt* a Jegyzettömbben. Az ügyfél (gyermek) tanúsítványának ujjlenyomatának lekéréséhez jelölje ki a "-----BEGIN CERTIFICATe-----" és a "-----END CERTIFICATe-----" szöveget a gyermek tanúsítványhoz, és másolja azt. A gyermek tanúsítvány azonosításához tekintse meg a subject =/sort.
7. Váltson arra a *vpnconfig. ovpn* fájlra, amelyet a 3. lépésben megnyitott a Jegyzettömbben. Keresse meg az alább látható szakaszt, és cserélje le a "CERT" és a "/CERT" közötti mindent.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Nyissa meg a *profileinfo.txt* a Jegyzettömbben. A titkos kulcs lekéréséhez válassza ki a "-----BEGIN PRIVATE KEY-----" és a "-----END titkos kulcs-----" szöveget, és másolja azt.
9. Térjen vissza a vpnconfig. ovpn fájlhoz a Jegyzettömbben, és keresse meg ezt a szakaszt. Illessze be a titkos kulcsot, és cserélje le a "Key" és a "/Key" közötti mindent.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
11. Másolja a vpnconfig.ovpn fájlt a C:\Program Files\OpenVPN\config mappába.
12. Kattintson a jobb gombbal az OpenVPN ikonjára a rendszertálcán, majd kattintson a Csatlakozás parancsra.

## <a name="mac-clients"></a><a name="mac"></a>Mac-ügyfelek

1. Töltse le és telepítsen egy OpenVPN-ügyfelet, például [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Töltse le az átjáró VPN-profilját. Ez a Azure Portal pont – hely konfiguráció lapján, vagy a PowerShellben a "New-AzVpnClientConfiguration" paranccsal végezhető el.
3. Csomagolja ki a profilt. Nyissa meg a vpnconfig. ovpn konfigurációs fájlt az OpenVPN mappából egy szövegszerkesztőben.
4. Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. A következő cikk hivatkozásait követve tájékozódhat arról, hogyan exportálhat tanúsítványokat a kódolt nyilvános kulcs lekéréséhez:

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) utasítások 
   
   * [Virtuális WAN](../articles/virtual-wan/certificates-point-to-site.md#cer) -utasítások
5. Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. A titkos kulcs kibontásával kapcsolatos információkért tekintse meg a [titkos kulcs exportálása](https://openvpn.net/community-resources/how-to/#pki) az OpenVPN-webhelyen című témakört.
6. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
7. A profil Tunnelblick való létrehozásához kattintson duplán a profil fájlra.
8. Indítsa el a Tunnelblick az alkalmazások mappából.
9. Kattintson a Tunnelblick ikonra a tálcán, és válassza a kapcsolat menüpontot.

> [!IMPORTANT]
>Az OpenVPN protokoll csak az iOS 11,0 és újabb, valamint a MacOS 10,13 és újabb verziókat támogatja.
>
## <a name="ios-clients"></a><a name="iOS"></a>iOS-ügyfelek

1. Telepítse az OpenVPN-ügyfelet (2,4-es vagy újabb verzió) az App Store-ból.
2. Töltse le az átjáró VPN-profilját. Ez a Azure Portal pont – hely konfiguráció lapján, vagy a PowerShellben a "New-AzVpnClientConfiguration" paranccsal végezhető el.
3. Csomagolja ki a profilt. Nyissa meg a vpnconfig. ovpn konfigurációs fájlt az OpenVPN mappából egy szövegszerkesztőben.
4. Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. A következő cikk hivatkozásait követve tájékozódhat arról, hogyan exportálhat tanúsítványokat a kódolt nyilvános kulcs lekéréséhez:

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) utasítások 
   
   * [Virtuális WAN](../articles/virtual-wan/certificates-point-to-site.md#cer) -utasítások
5. Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. A titkos kulcs kibontásával kapcsolatos információkért lásd: [a titkos kulcs exportálása](https://openvpn.net/community-resources/how-to/#pki) az OpenVPN-helyen.
6. Ne módosítson semmilyen egyéb mezőt.
7. Küldje el az e-mail-fiókjának a profil fájlját (. ovpn) az iPhone-on található posta alkalmazásban konfigurált e-mail fiókjába. 
8. Nyissa meg az e-mail üzenetet az iPhone alkalmazásban, és koppintson a csatolt fájlra.

    ![E-mail megnyitása](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Ha nem látja a **Másolás az OpenVPN-be** lehetőséget, koppintson a **továbbiak** elemre.

    ![Kibontás](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Koppintson a **Másolás az OpenVPN-** re lehetőségre 

    ![Másolás az OpenVPN-be](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Koppintson a **Hozzáadás** lehetőségre a **profil importálása** oldalon

    ![Hozzáadás](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Koppintson a **Hozzáadás** elemre az **importált profil** oldalon

    ![Koppintson a Hozzáadás gombra.](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Indítsa el az OpenVPN-alkalmazást, és csúsztassa a kapcsolót a **profil** oldalon a kapcsolódáshoz

    ![Csatlakozás](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linux-ügyfelek

1. Nyisson meg egy új terminál-munkamenetet. Egy új munkamenet megnyitásához nyomja le a "CTRL + ALT + t" billentyűkombinációt egy időben.
2. A szükséges összetevők telepítéséhez írja be a következő parancsot:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Töltse le az átjáró VPN-profilját. Ezt a Azure Portal pont – hely konfiguráció lapján teheti meg.
4. Exportálja a létrehozott és a P2S-konfigurációba az átjárón feltöltött P2S-ügyféltanúsítványt. Használja a következő cikk hivatkozásait:

   * [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) utasítások 
   
   * [Virtuális WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport) -utasítások
5. Bontsa ki a titkos kulcsot és a Base64 ujjlenyomatot a. pfx fájlból. Ezt többféleképpen megteheti. Az OpenSSL használata a számítógépen egy módszer.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   A *profileinfo.txt* fájl tartalmazni fogja a titkos kulcsot és a hitelesítésszolgáltató ujjlenyomatát, valamint az ügyféltanúsítványt. Ügyeljen arra, hogy az ügyféltanúsítvány ujjlenyomatát használja.

6. *profileinfo.txt* megnyitása egy szövegszerkesztőben. Az ügyfél (gyermek) tanúsítványának ujjlenyomatának lekéréséhez válassza a "-----BEGIN CERTIFICATe-----" és a "-----END CERTIFICATe-----" szöveget, és másolja azt. A gyermek tanúsítvány azonosításához tekintse meg a subject =/sort.

7. Nyissa meg az *vpnconfig. ovpn* fájlt, és keresse meg az alább látható szakaszt. Cserélje le a és a "CERT" és a "/CERT" közötti mindent.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Nyissa meg a profileinfo.txt egy szövegszerkesztőben. A titkos kulcs beszerzéséhez válassza ki a "-----BEGIN PRIVATE KEY-----" és a "-----END titkos kulcs-----" szöveget, és másolja azt.

9. Nyissa meg a vpnconfig. ovpn fájlt egy szövegszerkesztőben, és keresse meg ezt a szakaszt. Illessze be a titkos kulcsot, és cserélje le a "Key" és a "/Key" közötti mindent.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
11. A parancssor használatával történő kapcsolódáshoz írja be a következő parancsot:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. A grafikus felhasználói felülettel való kapcsolódáshoz lépjen a rendszerbeállítások elemre.
13. **+** Új VPN-kapcsolat hozzáadásához kattintson ide.
14. A **VPN hozzáadása** területen válassza **az importálás fájlból lehetőséget..** .
15. Tallózással keresse meg a profil fájlt, és kattintson rá duplán, vagy válassza a **Megnyitás** lehetőséget.
16. A **VPN hozzáadása** ablakban kattintson a **Hozzáadás** gombra.
  
    ![Importálás fájlból](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. A csatlakozáshoz **kapcsolja be a VPN-** t a **hálózati beállítások** lapon, vagy a rendszertálcán a hálózat ikon alatt.
