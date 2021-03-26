---
title: Tudnivalók az Azure pont – hely VPN-kapcsolatokról | VPN Gateway
description: Ez a cikk segítséget nyújt a pont – hely kapcsolatok megismeréséhez, és segít eldönteni, hogy a P2S VPN-átjáró milyen hitelesítési típust használjon.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 6caf72884ce2566ad25b7dd6d80b15d737bbe664
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543584"
---
# <a name="about-point-to-site-vpn"></a>Tudnivalók a pont – hely VPN-ről

A pont–hely (P2S) VPN-átjátókapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás főleg távmunkások számára hasznos, akik egy távoli helyről szeretnének csatlakozni egy Azure virtuális hálózatokhoz, például otthonról vagy konferenciáról. A pont–hely VPN emellett akkor is hasznos megoldás lehet a helyek közötti VPN helyett, ha csak néhány ügyfelet szeretne egy virtuális hálózathoz csatlakoztatni. Ez a cikk a Resource Manager-alapú üzemi modell vonatkozik.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Milyen protokollt használ a P2S?

A pont – hely típusú VPN a következő protokollok egyikét használhatja:

* **OpenVPN® protokoll**, SSL/TLS-alapú VPN protokoll. A TLS VPN-megoldás képes behatolni a tűzfalakba, mivel a legtöbb tűzfal a 443-as TCP-portot nyitja meg, amelyet a TLS használ. Az OpenVPN az Android, az iOS (11,0-es és újabb verziók), a Windows, a Linux és a Mac rendszerű eszközök (OSX 10,13-es és újabb verziók) használatával való kapcsolódásra használható.

* Secure Socket Tunneling Protocol (SSTP), egy szabadalmaztatott TLS-alapú VPN-protokoll. A TLS VPN-megoldás képes behatolni a tűzfalakba, mivel a legtöbb tűzfal a 443-as TCP-portot nyitja meg, amelyet a TLS használ. Az SSTP csak Windows-eszközökön támogatott. Az Azure a Windows összes olyan verzióját támogatja, amely SSTP-t (Windows 7 és újabb) tartalmaz.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).


>[!NOTE]
>A IKEv2 és az OpenVPN for P2S csak a Resource Manager-alapú üzemi modellhez érhető el. A klasszikus üzemi modell esetében nem érhetők el.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>Hogyan történik a P2S VPN-ügyfelek hitelesítése?

Mielőtt az Azure elfogadja a P2S VPN-kapcsolatát, előbb hitelesítenie kell a felhasználót. Az Azure két olyan mechanizmust kínál, amely egy csatlakozó felhasználó hitelesítésére szolgál.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Hitelesítés natív Azure tanúsítványalapú hitelesítés használatával

A natív Azure-tanúsítvány hitelesítésének használatakor a rendszer az eszközön lévő ügyféltanúsítványt használja a csatlakozó felhasználó hitelesítéséhez. Az ügyféltanúsítványok egy megbízható főtanúsítványból jönnek létre, majd az egyes ügyfélszámítógépekre települnek. Használhat egy vállalati megoldással létrehozott főtanúsítványt, vagy létrehozhat egy önaláírt tanúsítványt.

Az ügyféltanúsítvány érvényesítését a VPN-átjáró hajtja végre, és a P2S VPN-kapcsolat létrehozása során történik. Az ellenőrzéshez a főtanúsítvány szükséges, és az Azure-ba kell feltölteni.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Hitelesítés natív Azure Active Directory hitelesítés használatával

Az Azure AD-hitelesítés lehetővé teszi a felhasználóknak az Azure-hoz való kapcsolódást a Azure Active Directory hitelesítő adataik használatával. A natív Azure AD-hitelesítés csak az OpenVPN protokoll és a Windows 10 esetében támogatott, és az [Azure VPN-ügyfél](https://go.microsoft.com/fwlink/?linkid=2117554)használatát igényli.

A natív Azure AD-hitelesítéssel kihasználhatja az Azure AD feltételes hozzáférését, valamint a VPN-hez készült Multi-Factor Authentication (MFA) funkciókat is.

Magas szinten az alábbi lépéseket kell elvégeznie az Azure AD-hitelesítés konfigurálásához:

1. [Azure AD-bérlő konfigurálása](openvpn-azure-ad-tenant.md)

2. [Azure AD-hitelesítés engedélyezése az átjárón](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Azure VPN-ügyfél letöltése és konfigurálása](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Hitelesítés Active Directory (AD) tartományi kiszolgáló használatával

Az AD tartományi hitelesítés lehetővé teszi a felhasználóknak az Azure-hoz való kapcsolódást a szervezeti tartományi hitelesítő adataik használatával. Ehhez egy olyan RADIUS-kiszolgáló szükséges, amely integrálva van az AD-kiszolgálóval. A szervezetek a meglévő RADIUS-telepítést is kihasználhatják.

A RADIUS-kiszolgáló a helyszínen vagy az Azure-VNet is üzembe helyezhető. A hitelesítés során az Azure VPN Gateway továbbítja a hitelesítési üzeneteket a RADIUS-kiszolgáló és a csatlakozó eszköz között. Ezért fontos, hogy az átjáró elérhető legyen a RADIUS-kiszolgáló számára. Ha a RADIUS-kiszolgáló a helyszínen található, az Azure-ból a helyszíni helyre létesített VPN-S2S szükséges a rendelkezésre álláshoz.

A RADIUS-kiszolgáló az AD tanúsítványszolgáltatásokkal is integrálható. Ez lehetővé teszi a RADIUS-kiszolgáló és a vállalati tanúsítvány központi telepítésének használatát a P2S-tanúsítvány hitelesítéséhez az Azure-tanúsítvány hitelesítésének alternatívájaként. Ennek az az előnye, hogy a főtanúsítványokat és a visszavont tanúsítványokat nem kell feltölteni az Azure-ba.

A RADIUS-kiszolgálók integrálása más külső identitási rendszerekkel is elvégezhető. Ez számos hitelesítési lehetőséget nyit meg a P2S VPN-hez, beleértve a többtényezős beállításokat is.

![Diagram, amely egy pont – hely típusú VPN-t mutat a helyszíni helyhez.](./media/point-to-site-about/p2s.png)

## <a name="what-are-the-client-configuration-requirements"></a>Milyen követelmények vonatkoznak az ügyfél-konfigurációra?

>[!NOTE]
>Windows-ügyfelek esetén rendszergazdai jogosultságokkal kell rendelkeznie az ügyfélszámítógépen ahhoz, hogy kezdeményezzen VPN-kapcsolat az ügyfél-eszközről az Azure-ba.
>

A felhasználók a natív VPN-ügyfeleket használják a Windows-és Mac-eszközökön a P2S. Az Azure olyan VPN-ügyfél-konfigurációs zip-fájlt biztosít, amely a natív ügyfelek által az Azure-hoz való csatlakozáshoz szükséges beállításokat tartalmazza.

* Windows-eszközök esetén a VPN-ügyfél konfigurációja egy olyan telepítőcsomagot tartalmaz, amelyet a felhasználók az eszközeiket telepítenek.
* Mac-eszközök esetén a a felhasználók által az eszközökön telepített mobileconfig-fájlból áll.

A zip-fájl az Azure-oldal néhány fontos beállításának értékeit is megadja, amelyek segítségével saját profilt hozhat létre ezekhez az eszközökhöz. Néhány érték például a VPN-átjáró címe, a konfigurált bújtatási típusok, az útvonalak és a főtanúsítvány az átjáró érvényesítéséhez.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Mely átjárók támogatják a P2S VPN-t?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Az átjáró SKU-javaslatait itt tekintheti meg: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Az alapszintű termékváltozat nem támogatja az IKEv2- vagy RADIUS-hitelesítést.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Milyen IKE/IPsec-házirendek vannak konfigurálva a P2S VPN-átjárón?


**IKEv2**

| **Titkosítási** | **Integritás** | **PRF** | **DH-csoport** |
|--|--|--|--|
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA384 | GROUP_ECP256 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA384 | SHA384 | GROUP_24 |
| AES256 | SHA384 | SHA384 | GROUP_14 |
| AES256 | SHA384 | SHA384 | GROUP_ECP384 |
| AES256 | SHA384 | SHA384 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA256 | SHA256 | GROUP_2 |

**IPsec**

| **Titkosítási** | **Integritás** | **PFS-csoport** |
|--|--|--|
| GCM_AES256 | GCM_AES256 | GROUP_NONE |
| GCM_AES256 | GCM_AES256 | GROUP_24 |
| GCM_AES256 | GCM_AES256 | GROUP_14 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
| GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256 | SHA256 | GROUP_NONE |
| AES256 | SHA256 | GROUP_24 |
| AES256 | SHA256 | GROUP_14 |
| AES256 | SHA256 | GROUP_ECP384 |
| AES256 | SHA256 | GROUP_ECP256 |
| AES256 | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Milyen TLS-házirendek vannak konfigurálva a P2S VPN-átjárón?
**TLS**

|**Házirendek** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hogyan konfigurálni a P2S-kapcsolatokat?

Egy P2S-konfigurációhoz elég néhány konkrét lépés szükséges. A következő cikkek a P2S konfigurációjának lépéseit és a VPN-ügyféleszközök konfigurálására mutató hivatkozásokat tartalmazzák:

* [P2S-alapú kapcsolatok konfigurálása – RADIUS-hitelesítés](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolatok konfigurálása – Azure natív tanúsítványalapú hitelesítés](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Az OpenVPN konfigurálása](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>P2S-kapcsolatok konfigurációjának eltávolítása

A lépésekért lásd az alábbi [gyakori kérdéseket](#removeconfig).
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>A natív Azure tanúsítványalapú hitelesítéssel kapcsolatos gyakori kérdések

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>A RADIUS-hitelesítéssel kapcsolatos gyakori kérdések

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Következő lépések

* [P2S-alapú kapcsolatok konfigurálása – RADIUS-hitelesítés](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolatok konfigurálása – Azure natív tanúsítványalapú hitelesítés](vpn-gateway-howto-point-to-site-rm-ps.md)

**Az "OpenVPN" az OpenVPN Inc védjegye.**
