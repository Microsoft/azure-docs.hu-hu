---
title: Áttérés az OpenVPN-re vagy a IKEv2-re az SSTP-ből | Azure-VPN Gateway
description: Ez a cikk segít megismerni az SSTP 128 egyidejű kapcsolódási korlátjának leküzdésének módjait.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: e2fa265e580bc0e752498284ed50e398b59423fd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657141"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Áttérés az OpenVPN protokollra vagy az SSTP-IKEv2

A pont–hely (P2S) VPN-átjátókapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a cikk a Resource Manager-alapú üzemi modellre vonatkozik, és arról beszél, hogyan lehet leküzdeni a 128 egyidejű kapcsolati korlátját az OpenVPN protokollra vagy a IKEv2 való áttéréssel.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Milyen protokollt használ a P2S?

A pont – hely típusú VPN a következő protokollok egyikét használhatja:

* **OpenVPN &reg; Protokoll**, SSL/TLS-alapú VPN protokoll. Az SSL VPN-megoldás képes behatolni a tűzfalakba, mivel a legtöbb tűzfal a 443-es TCP-portot nyitja meg, amelyet az SSL használ. Az OpenVPN az Android, az iOS (11,0-es és újabb verziók), a Windows, a Linux és a Mac rendszerű eszközök (OSX 10,13-es és újabb verziók) használatával való kapcsolódásra használható.

* A **Secure Socket Tunneling Protocol (SSTP)** egy saját SSL-alapú VPN-protokoll. Az SSL VPN-megoldás képes behatolni a tűzfalakba, mivel a legtöbb tűzfal a 443-es TCP-portot nyitja meg, amelyet az SSL használ. Az SSTP csak Windows-eszközökön támogatott. Az Azure a Windows összes olyan verzióját támogatja, amely SSTP-t (Windows 7 és újabb) tartalmaz. **Az SSTP legfeljebb 128 egyidejű kapcsolatot támogat, függetlenül az ÁTJÁRÓ SKU**-tól.

* IKEv2 VPN, egy szabványalapú IPsec VPN-megoldás. Az IKEv2 VPN segítségével Macről is lehetségessé válik a csatlakozás (OSX 10.11-es vagy újabb verziók használata esetén).


>[!NOTE]
>A IKEv2 és az OpenVPN for P2S csak a Resource Manager-alapú üzemi modellhez érhető el. A klasszikus üzemi modell esetében nem érhetők el. Az alapszintű átjáró SKU nem támogatja a IKEv2 vagy az OpenVPN protokollokat. Ha alapszintű SKU-t használ, törölnie kell, majd újra létre kell hoznia egy éles SKU Virtual Network átjárót.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migrálás SSTP-ről IKEv2 vagy OpenVPN-re

Előfordulhatnak olyan esetek, amikor több mint 128 egyidejű P2S-kapcsolat használatát szeretné támogatni egy VPN-átjáróhoz, de az SSTP-t használja. Ilyen esetben a IKEv2 vagy az OpenVPN protokollt kell áthelyeznie.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>1. lehetőség – IKEv2 hozzáadása az SSTP-on kívül az átjárón

Ez a legegyszerűbb lehetőség. Az SSTP és a IKEv2 ugyanazon az átjárón létezhetnek, és nagyobb számú egyidejű kapcsolatot biztosít. Egyszerűen engedélyezheti a IKEv2 a meglévő átjárón, és újra letöltheti az ügyfelet.

A IKEv2 meglévő SSTP VPN-átjáróhoz való hozzáadása nem érinti a meglévő ügyfeleket, és beállíthatja, hogy a IKEv2 kis kötegekben használják, vagy csak konfigurálja az új ügyfeleket a IKEv2 használatára. Ha egy Windows-ügyfél az SSTP-hez és a IKEv2-hez is konfigurálva van, először a IKEV2 használatával próbál csatlakozni, és ha ez nem sikerül, a rendszer az SSTP-re lép vissza.

**A IKEv2 nem szabványos UDP-portokat használ, ezért biztosítania kell, hogy ezek a portok ne legyenek letiltva a felhasználó tűzfalán. A használatban lévő portok a következők: UDP 500 és 4500.**

Ha IKEv2 szeretne hozzáadni egy meglévő átjáróhoz, egyszerűen nyissa meg a "pont – hely konfiguráció" lapot a portál Virtual Network Átjárójában, és válassza a **IKEv2 és az SSTP (SSL)** lehetőséget a legördülő listából.

![A "pont – hely konfiguráció" oldalt megjelenítő képernyőkép, amely a "Tunnel Type" legördülő menüből, a "IKEv2 és az SSTP (SSL)" beállítással jelenik meg.](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>2. lehetőség – az SSTP eltávolítása és az OpenVPN engedélyezése az átjárón

Mivel az SSTP és az OpenVPN is TLS-alapú protokoll, nem létezhetnek ugyanazon az átjárón. Ha úgy dönt, hogy az SSTP-ből az OpenVPN-be lép, le kell tiltania az SSTP-t, és engedélyeznie kell az OpenVPN-t az átjárón. Ez a művelet azt eredményezi, hogy a meglévő ügyfelek elveszítik a kapcsolatot a VPN-átjáróval, amíg az új profil be nem lett állítva az ügyfélen.

Ha kívánja, az OpenVPN mellett is engedélyezheti a IKEv2. Az OpenVPN TLS-alapú, és a szabványos TCP 443 portot használja. Az OpenVPN-re való váltáshoz nyissa meg a portál Virtual Network átjárójának "pont – hely konfiguráció" lapját, és válassza az **OpenVPN (SSL)** vagy a **IKEv2 és az OpenVPN (SSL)** lehetőséget a legördülő listából.

![pont – hely kapcsolat](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Az átjáró konfigurálását követően a meglévő ügyfelek nem fognak tudni csatlakozni, amíg nem [telepíti és nem konfigurálja az OpenVPN-ügyfeleket](./vpn-gateway-howto-openvpn-clients.md).

Ha Windows 10 rendszert használ, használhatja a [Windows rendszerhez készült Azure VPN-ügyfelet](./openvpn-azure-ad-client.md#to-download-the-azure-vpn-client) is


## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="what-are-the-client-configuration-requirements"></a>Milyen követelmények vonatkoznak az ügyfél-konfigurációra?

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

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Mely átjárók támogatják a P2S VPN-t?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Az átjáró SKU-javaslatait itt tekintheti meg: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Az alapszintű termékváltozat nem támogatja az IKEv2- vagy RADIUS-hitelesítést.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Milyen IKE/IPsec-házirendek vannak konfigurálva a P2S VPN-átjárón?


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

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Milyen TLS-házirendek vannak konfigurálva a P2S VPN-átjárón?
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

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Hogyan konfigurálni a P2S-kapcsolatokat?

Egy P2S-konfigurációhoz elég néhány konkrét lépés szükséges. A következő cikkek a P2S konfigurációjának lépéseit és a VPN-ügyféleszközök konfigurálására mutató hivatkozásokat tartalmazzák:

* [P2S-alapú kapcsolatok konfigurálása – RADIUS-hitelesítés](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolatok konfigurálása – Azure natív tanúsítványalapú hitelesítés](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Az OpenVPN konfigurálása](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Következő lépések

* [P2S-alapú kapcsolatok konfigurálása – RADIUS-hitelesítés](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolatok konfigurálása – Azure natív tanúsítványalapú hitelesítés](vpn-gateway-howto-point-to-site-rm-ps.md)

**Az "OpenVPN" az OpenVPN Inc védjegye.**