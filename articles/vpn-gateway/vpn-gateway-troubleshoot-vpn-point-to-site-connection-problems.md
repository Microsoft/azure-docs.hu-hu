---
title: Az Azure pont – hely kapcsolati problémák elhárítása
titleSuffix: Azure VPN Gateway
description: Ismerje meg a pont – hely kapcsolati problémák és az egyéb virtuális magánhálózati hibák és problémák elhárítását és megoldását.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: genli
ms.openlocfilehash: c316aaf02979008b9d2ebc691d54c0fb95a5a52d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994859"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>Hibaelhárítás: Azure pont – hely kapcsolati problémák

Ez a cikk az esetlegesen felmerülő, pont – hely kapcsolatokkal kapcsolatos gyakori problémákat sorolja fel. Emellett a problémák lehetséges okait és megoldásait is tárgyalja.

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN-ügyfél hiba: A tanúsítvány nem található.

### <a name="symptom"></a>Hibajelenség

Amikor a VPN-ügyféllel próbál csatlakozni egy Azure-beli virtuális hálózathoz, a következő hibaüzenet jelenik meg:

**Nem található a bővíthető hitelesítési protokollal használható tanúsítvány. (798-es hiba)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha az ügyféltanúsítvány hiányzik a **tanúsítványokból – aktuális User\Personal\Certificates**.

### <a name="solution"></a>Megoldás

A probléma megoldásához kövesse az alábbi lépéseket:

1. Nyissa meg a Tanúsítványkezelőt: kattintson a **Start** gombra, írja be a **számítógép-tanúsítványok kezelése** kifejezést, majd kattintson a keresési eredmény **számítógép-tanúsítványok kezelése** elemére.

2. Győződjön meg arról, hogy a következő tanúsítványok a megfelelő helyen vannak:

    | Tanúsítvány | Hely |
    | ------------- | ------------- |
    | AzureClient. pfx  | Aktuális User\Personal\Certificates |
    | AzureRoot. cer    | Helyi tárolóba legfelső szintű hitelesítésszolgáltatók|

3. Nyissa meg a C:\Users \<UserName> \AppData\Roaming\Microsoft\Network\Connections\Cm \<GUID> , telepítse manuálisan a tanúsítványt (*. cer fájlt) a felhasználó és a számítógép tárolóján.

Az ügyféltanúsítvány telepítésével kapcsolatos további információkért lásd: [tanúsítványok létrehozása és exportálása pont – hely kapcsolatokhoz](vpn-gateway-certificates-point-to-site.md).

> [!NOTE]
> Az ügyféltanúsítvány importálásakor ne jelölje be a **titkos kulcs erős védelmének engedélyezése** beállítást.

## <a name="the-network-connection-between-your-computer-and-the-vpn-server-could-not-be-established-because-the-remote-server-is-not-responding"></a>Nem lehet létrehozni a számítógép és a VPN-kiszolgáló közötti hálózati kapcsolatot, mert a távoli kiszolgáló nem válaszol

### <a name="symptom"></a>Hibajelenség

Amikor egy Azure-beli virtuális hálózati átjáróhoz próbál csatlakozni a Windows IKEv2 használatával, a következő hibaüzenet jelenik meg:

**Nem lehet létrehozni a számítógép és a VPN-kiszolgáló közötti hálózati kapcsolatot, mert a távoli kiszolgáló nem válaszol**

### <a name="cause"></a>Ok
 
 A probléma akkor fordul elő, ha a Windows verziója nem támogatja az IKE-töredezettséget
 
### <a name="solution"></a>Megoldás

Az IKEv2 Windows 10 és Server 2016 rendszeren támogatott. Ahhoz azonban, hogy használni tudja az IKEv2-t, helyileg telepítenie kell a frissítéseket, és meg kell adnia a beállításkulcs értékét. A Windows 10 előtti operációsrendszer-verziók nem támogatottak, és csak SSTP-t tudnak használni.

A Windows 10 vagy a Server 2016 előkészítése az IKEv2 használatára:

1. Telepítse a frissítést.

   | Operációs rendszer verziója | Date | Szám/hivatkozás |
   |---|---|---|---|
   | Windows Server 2016<br>Windows 10, 1607-es verzió | 2018. január 17. | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10, 1703-as verzió | 2018. január 17. | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 1709-es verzió | Március 22.2018 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |  |

2. Adja meg a beállításkulcs értékét. Hozzon létre vagy állítson be `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload` REG_DWORD kulcsot a beállításjegyzékben 1 értékre.

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN-ügyfél hiba: a kapott üzenet váratlan vagy helytelen formátumú volt.

### <a name="symptom"></a>Hibajelenség

Amikor a VPN-ügyféllel próbál csatlakozni egy Azure-beli virtuális hálózathoz, a következő hibaüzenet jelenik meg:

**A kapott üzenet váratlan vagy helytelen formátumú volt. (Hiba 0x80090326)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha a következő feltételek egyike igaz:

- Helytelenül van beállítva a felhasználó által megadott útvonalak (UDR) használata az átjáró-alhálózat alapértelmezett útvonalával.
- A főtanúsítvány nyilvános kulcsa nincs feltöltve az Azure VPN gatewaybe. 
- A kulcs sérült vagy lejárt.

### <a name="solution"></a>Megoldás

A probléma megoldásához kövesse az alábbi lépéseket:

1. Távolítsa el a UDR az átjáró-alhálózaton. Győződjön meg arról, hogy a UDR megfelelően továbbítja az összes forgalmat.
2. Ellenőrizze a főtanúsítvány állapotát a Azure Portalban, hogy megjelenjen-e a visszavonás. Ha a rendszer nem vonja vissza, törölje a főtanúsítványt, és töltse fel újból a feltöltést. További információt a [tanúsítványok létrehozása](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)című témakörben talál.

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN-ügyfél hiba: A tanúsítványlánc feldolgozva, de leállt 

### <a name="symptom"></a>Hibajelenség 

Amikor a VPN-ügyféllel próbál csatlakozni egy Azure-beli virtuális hálózathoz, a következő hibaüzenet jelenik meg:

**Egy tanúsítványlánc feldolgozva, de a megbízhatósági szolgáltató által nem megbízható főtanúsítványban leállt.**

### <a name="solution"></a>Megoldás

1. Győződjön meg arról, hogy a következő tanúsítványok a megfelelő helyen vannak:

    | Tanúsítvány | Hely |
    | ------------- | ------------- |
    | AzureClient. pfx  | Aktuális User\Personal\Certificates |
    | Azuregateway –*GUID*. cloudapp.net  | Aktuális User\Trusted legfelső szintű hitelesítésszolgáltatók|
    | AzureGateway –*GUID*. Cloudapp.net, AzureRoot. cer    | Helyi tárolóba legfelső szintű hitelesítésszolgáltatók|

2. Ha a tanúsítványok már a helyen vannak, próbálja meg törölni a tanúsítványokat, majd telepítse újra. A **azuregateway-*GUID*. CLOUDAPP.net** tanúsítvány a VPN-ügyfél konfigurációs csomagjában található, amelyet a Azure Portal letöltött. A fájlok archiválásával kinyerheti a fájlokat a csomagból.

## <a name="file-download-error-target-uri-is-not-specified"></a>Fájl letöltési hiba: nincs megadva a cél URI azonosítója.

### <a name="symptom"></a>Hibajelenség

A következő hibaüzenet jelenik meg:

**Fájl letöltési hiba. Nincs megadva a cél URI azonosítója.**

### <a name="cause"></a>Ok 

Ez a probléma helytelen átjáró típusa miatt fordul elő. 

### <a name="solution"></a>Megoldás

A VPN-átjáró típusának **VPN**-nek kell lennie, és a VPN-típusnak **útvonalalapú** kell lennie.

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN-ügyfél hiba: az Azure VPN egyéni parancsfájlja sikertelen 

### <a name="symptom"></a>Hibajelenség

Amikor a VPN-ügyféllel próbál csatlakozni egy Azure-beli virtuális hálózathoz, a következő hibaüzenet jelenik meg:

**Az egyéni parancsfájl (az útválasztási táblázat frissítéséhez) sikertelen volt. (Hiba 8007026f)**

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a helyek közötti VPN-kapcsolat parancsikon használatával próbál megnyitni.

### <a name="solution"></a>Megoldás 

Nyissa meg közvetlenül a VPN-csomagot a parancsikonról való megnyitás helyett.

## <a name="cannot-install-the-vpn-client"></a>Nem lehet telepíteni a VPN-ügyfelet

### <a name="cause"></a>Ok 

Egy további tanúsítványra van szükség a virtuális hálózat VPN-átjárójának megbízhatóságához. A tanúsítvány a Azure Portalból generált VPN-ügyfél konfigurációs csomagja részét képezi.

### <a name="solution"></a>Megoldás

Bontsa ki a VPN-ügyfél konfigurációs csomagját, és keresse meg a. cer fájlt. A tanúsítvány telepítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg mmc.exe.
2. Adja hozzá a **tanúsítványok** beépülő modult.
3. Válassza ki **a helyi számítógép számítógépfiókját.**
4. Kattintson a jobb gombbal a **megbízható legfelső szintű hitelesítésszolgáltatók** csomópontra. Kattintson a **minden tevékenység**  >  **importálása** lehetőségre, és keresse meg a VPN-ügyfél konfigurációs csomagjából kinyert. cer fájlt.
5. Indítsa újra a számítógépet. 
6. Próbálja meg telepíteni a VPN-ügyfelet.

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure Portal hiba: nem sikerült menteni a VPN-átjárót, és az adatértékek érvénytelenek

### <a name="symptom"></a>Hibajelenség

Ha a Azure Portal a VPN-átjáró módosításait próbálja menteni, a következő hibaüzenet jelenik meg:

**Nem sikerült menteni a virtuális hálózati átjáró &lt; *átjárójának nevét* &gt; . A tanúsítvány- &lt; *tanúsítvány azonosítójának azonosítója* &gt; érvénytelen.**

### <a name="cause"></a>Ok 

Ez a probléma akkor fordulhat elő, ha a főtanúsítványban feltöltött nyilvános kulcs érvénytelen karaktert tartalmaz, például szóközt.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a tanúsítványban szereplő adatok nem tartalmaznak érvénytelen karaktereket, például sortöréseket (kocsivissza). A teljes értéknek egy hosszú sornak kell lennie. A következő szöveg a tanúsítvány mintája:

```text
-----BEGIN CERTIFICATE-----
MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
/0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
-----END CERTIFICATE-----
```

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure Portal hiba: nem sikerült menteni a VPN-átjárót, és az erőforrás neve érvénytelen.

### <a name="symptom"></a>Hibajelenség

Ha a Azure Portal a VPN-átjáró módosításait próbálja menteni, a következő hibaüzenet jelenik meg: 

**Nem sikerült menteni a virtuális hálózati átjáró &lt; *átjárójának nevét* &gt; . &lt;A *feltölteni kívánt* erőforrás neve &gt; nem érvényes**.

### <a name="cause"></a>Ok

Ez a probléma azért fordul elő, mert a tanúsítvány neve érvénytelen karaktert tartalmaz, például szóközt. 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure Portal hiba: VPN-csomagfájl letöltése 503-es hiba

### <a name="symptom"></a>Hibajelenség

A VPN-ügyfél konfigurációs csomagjának letöltésekor a következő hibaüzenet jelenik meg:

**Nem sikerült letölteni a fájlt. Hiba részletei: 503-es hiba. A kiszolgáló foglalt.**
 
### <a name="solution"></a>Megoldás

Ezt a hibát egy ideiglenes hálózati probléma okozhatja. Próbálja meg újra letölteni a VPN-csomagot néhány perc múlva.

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Azure VPN Gateway verziófrissítés: az összes pont – hely ügyfél nem tud csatlakozni

### <a name="cause"></a>Ok

Ha a tanúsítvány meghaladja a 50 százalékot a teljes élettartamán keresztül, a rendszer átadja a tanúsítványt.

### <a name="solution"></a>Megoldás

A probléma megoldásához töltse le és telepítse újra a pont és a hely csomagját minden ügyfélen.

## <a name="too-many-vpn-clients-connected-at-once"></a>Túl sok VPN-ügyfél csatlakozik egyszerre

Elérte az engedélyezett kapcsolatok maximális számát. A csatlakoztatott ügyfelek teljes száma a Azure Portalban látható.

## <a name="vpn-client-cannot-access-network-file-shares"></a>A VPN-ügyfél nem fér hozzá a hálózati fájlmegosztás

### <a name="symptom"></a>Hibajelenség

A VPN-ügyfél kapcsolódott az Azure-beli virtuális hálózathoz. Az ügyfél azonban nem fér hozzá a hálózati megosztásokhoz.

### <a name="cause"></a>Ok

Az SMB protokoll a fájlmegosztás elérésére szolgál. A kapcsolat kezdeményezése után a VPN-ügyfél hozzáadja a munkamenet hitelesítő adatait, és a hiba történik. A kapcsolódás után az ügyfélnek a Kerberos-hitelesítéshez tartozó gyorsítótárazási hitelesítő adatok használatára kell kényszeríteni. Ez a folyamat kezdeményezi a kulcsszolgáltató (tartományvezérlő) lekérdezéseit a jogkivonat lekéréséhez. Mivel az ügyfél az internetről csatlakozik, előfordulhat, hogy nem tudja elérni a tartományvezérlőt. Ezért az ügyfél nem végezhet feladatátvételt Kerberos-kapcsolaton keresztül az NTLM-re. 

Az ügyfélnek csak akkor kell megadnia a hitelesítő adatokat, ha érvényes tanúsítvánnyal rendelkezik (SAN = UPN), amelyhez a tartomány csatlakozik. Az ügyfélnek fizikailag is csatlakoztatva kell lennie a tartományi hálózathoz. Ebben az esetben az ügyfél megpróbálja használni a tanúsítványt, és eléri a tartományvezérlőt. Ezután a kulcsszolgáltató "KDC_ERR_C_PRINCIPAL_UNKNOWN" hibát ad vissza. Az ügyfélnek az NTLM-re való feladatátvételre van kényszerítve. 

### <a name="solution"></a>Megoldás

A probléma megoldásához tiltsa le a tartományi hitelesítő adatok gyorsítótárazását a következő beállításkulcs alapján: 

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1`


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>Nem található a pont – hely VPN-kapcsolat a Windowsban a VPN-ügyfél újratelepítése után

### <a name="symptom"></a>Hibajelenség

Távolítsa el a pont – hely VPN-kapcsolatát, majd telepítse újra a VPN-ügyfelet. Ebben az esetben a VPN-kapcsolat konfigurálása nem sikerült. A VPN-kapcsolat nem jelenik meg a Windows **hálózati kapcsolatok** beállításai között.

### <a name="solution"></a>Megoldás

A probléma megoldásához törölje a régi VPN-ügyfél konfigurációs fájljait **a \<VirtualNetworkId> C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections**-ből, majd futtassa újra a VPN-ügyfél telepítőjét.

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>Pont – hely típusú VPN-ügyfél nem tudja feloldani a helyi tartományban lévő erőforrások teljes tartománynevét.

### <a name="symptom"></a>Hibajelenség

Amikor az ügyfél pont – hely VPN-kapcsolat használatával csatlakozik az Azure-hoz, nem tudja feloldani a helyi tartományban lévő erőforrások teljes tartománynevét.

### <a name="cause"></a>Ok

A pont – hely VPN-ügyfél általában az Azure virtuális hálózatban konfigurált Azure DNS-kiszolgálókat használja. A Azure DNS-kiszolgálók elsőbbséget élveznek az ügyfélben konfigurált helyi DNS-kiszolgálókkal szemben (kivéve, ha az Ethernet-adapter metrikája alacsonyabb), így az összes DNS-lekérdezést a rendszer a Azure DNS-kiszolgálókra küldi. Ha a Azure DNS-kiszolgálók nem rendelkeznek a helyi erőforrásokhoz tartozó rekordokkal, a lekérdezés meghiúsul.

### <a name="solution"></a>Megoldás

A probléma megoldásához győződjön meg arról, hogy az Azure virtuális hálózaton használt Azure DNS-kiszolgálók feloldják a helyi erőforrások DNS-rekordjait. Ehhez használhatja a DNS-továbbítókat vagy a feltételes továbbítókat. További információ: [névfeloldás a saját DNS-kiszolgáló használatával](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>A pont – hely VPN-kapcsolat létrejött, de továbbra sem tud csatlakozni az Azure-erőforrásokhoz 

### <a name="cause"></a>Ok

Ez a probléma akkor fordulhat elő, ha a VPN-ügyfél nem kapja meg az útvonalakat az Azure VPN Gateway-től.

### <a name="solution"></a>Megoldás

A probléma megoldásához [állítsa alaphelyzetbe az Azure VPN Gatewayt](./reset-gateway.md). Annak érdekében, hogy az új útvonalakat használják, a pont – hely VPN-ügyfeleket újra le kell tölteni a virtuális hálózati társítás sikeres konfigurálása után.

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>Hiba: "a visszavonás funkció nem tudta megnézni a visszavonást, mert a visszavonási kiszolgáló offline állapotban volt. (Hiba 0x80092013) "

### <a name="causes"></a>Okok
Ez a hibaüzenet akkor jelenik meg, ha az ügyfél nem fér hozzá http://crl3.digicert.com/ssca-sha2-g1.crl és http://crl4.digicert.com/ssca-sha2-g1.crl .  A visszavonási vizsgálathoz hozzáféréssel kell rendelkeznie ehhez a két helyhez.  Ez a probléma általában a proxykiszolgálót futtató ügyfélen történik. Bizonyos környezetekben, ha a kérések nem a proxykiszolgálón keresztül mennek át, a rendszer megtagadja a hozzáférést a peremhálózati tűzfalon.

### <a name="solution"></a>Megoldás

Ellenőrizze a proxykiszolgáló beállításait, és győződjön meg arról, hogy az ügyfél hozzáférhet a és a rendszerhez http://crl3.digicert.com/ssca-sha2-g1.crl http://crl4.digicert.com/ssca-sha2-g1.crl .

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN-ügyfél hiba: a kapcsolat megakadályozása a RAS/VPN-kiszolgálón konfigurált házirend miatt megszakadt. (812-es hiba)

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a VPN-ügyfél hitelesítéséhez használt RADIUS-kiszolgáló helytelen beállításokkal rendelkezik, vagy az Azure Gateway nem tudja elérni a RADIUS-kiszolgálót.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a RADIUS-kiszolgáló megfelelően van konfigurálva. További információ: [a RADIUS-hitelesítés integrálása az Azure ad-val multi-Factor Authentication-kiszolgáló](../active-directory/authentication/howto-mfaserver-dir-radius.md).

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>"Hiba 405" a főtanúsítvány letöltésekor VPN Gateway

### <a name="cause"></a>Ok

A főtanúsítvány nincs telepítve. A főtanúsítvány telepítve van az ügyfél **megbízható tanúsítványok** tárolójába.

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN-ügyfél hiba: a távoli kapcsolat nem történt meg, mert a megkísérelt VPN-alagutak sikertelenek voltak. (800-es hiba) 

### <a name="cause"></a>Ok

A hálózati adapter illesztőprogramja elavult.

### <a name="solution"></a>Megoldás

A hálózati adapter illesztőprogramjának frissítése:

1. Kattintson a **Start** gombra, írja be a **Eszközkezelő** parancsot, majd válassza ki az eredmények listájából. Ha a rendszer rendszergazdai jelszót vagy megerősítést kér, írja be a jelszót, vagy adja meg a megerősítést.
2. A **hálózati adapterek** kategóriájában keresse meg a frissíteni kívánt NIC-t.  
3. Kattintson duplán az eszköz nevére, válassza az **Illesztőprogram frissítése** lehetőséget, majd válassza a **Keresés automatikusan lehetőséget a frissített illesztőprogram-szoftverekhez**.
4. Ha a Windows nem talál új illesztőprogramot, akkor próbáljon meg egyet az eszköz gyártójának webhelyén, és kövesse az utasításokat.
5. Indítsa újra a számítógépet, majd próbálja megismételni a műveletet.

## <a name="vpn-client-error-dialing-vpn-connection-vpn-connection-name-status--vpn-platform-did-not-trigger-connection"></a>VPN-ügyfél hiba: a VPN-kapcsolat tárcsázása <VPN Connection Name> , állapot = a VPN-platform nem aktiválta a kapcsolatot

A következő hibaüzenetet is láthatja Eseménynapló a RasClient: "a felhasználó <User> olyan nevű kapcsolatot tárcsázott, <VPN Connection Name> amely nem sikerült. A hibával visszaadott hibakód 1460. "

### <a name="cause"></a>Ok

Az Azure VPN-ügyfél nem rendelkezik a Windows alkalmazás beállításaiban engedélyezett "háttérbeli alkalmazások" alkalmazás engedéllyel.

### <a name="solution"></a>Megoldás

1. A Windows rendszerben válassza a beállítások-> adatvédelem – > háttérbeli alkalmazások lehetőséget.
2. Az alkalmazások háttérben való futtatásának bekapcsolása a következőre:

## <a name="error-file-download-error-target-uri-is-not-specified"></a>Hiba: nincs megadva a fájl letöltési hibájának cél URI azonosítója.

### <a name="cause"></a>Ok

Ezt az átjáró helytelen típusának beállítása okozza.

### <a name="solution"></a>Megoldás

Az Azure VPN Gateway típusának VPN-nek kell lennie, és a VPN-típusnak **útvonalalapú** kell lennie.

## <a name="vpn-package-installer-doesnt-complete"></a>A VPN-csomag telepítője nem fejeződött be

### <a name="cause"></a>Ok

Ezt a problémát az előző VPN-ügyfél telepítése okozhatta. 

### <a name="solution"></a>Megoldás

Törölje a régi VPN-ügyfél konfigurációs fájljait a **C:\Users\UserName\AppData\Roaming\Microsoft\Network\Connections \<VirtualNetworkId>** -ből, majd futtassa újra a VPN-ügyfél telepítőjét. 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>A VPN-ügyfél egy kis idő elteltével hibernálja vagy alvó állapotba lép

### <a name="solution"></a>Megoldás

Keresse meg a VPN-ügyfelet futtató számítógép alvó és hibernált beállításait.