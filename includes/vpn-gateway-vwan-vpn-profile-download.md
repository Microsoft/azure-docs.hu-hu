---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979069"
---
## <a name="extract-the-zip-file"></a>Zip-fájl kibontása

Csomagolja ki a tömörített fájlt. A fájl a következő mappákat tartalmazza:

* AzureVPN
* Általános
* OpenVPN (ha engedélyezte az OpenVPN-t az **Azure-tanúsítvánnyal** vagy **RADIUS-hitelesítési** beállításokkal az átjárón). Válassza ki a megfelelő cikket, amely megfelel a konfigurációnak a bérlő létrehozásához.

  * [VPN Gateway – bérlő létrehozása](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [Virtuális WAN – bérlő létrehozása](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>Adatok lekérése

A **AzureVPN** mappában navigáljon a **_azurevpnconfig.xml_** fájlhoz, és nyissa meg a Jegyzettömbben. Jegyezze fel a következő címkék közötti szöveget.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profil részletei

Kapcsolat hozzáadásakor használja az előző lépésben összegyűjtött adatokat a profil részletei lapon. A mezők a következő adatoknak felelnek meg:

* **Célközönség:** Azonosítja azt a címzett-erőforrást, amelyet a jogkivonat szánt.
* **Kiállító:** Azonosítja a jogkivonatot és az Azure AD-bérlőt kibocsátó biztonságijogkivonat-szolgáltatást (STS).
* **Bérlő:** A jogkivonatot kiállító címtár-bérlő megváltoztathatatlan, egyedi azonosítóját tartalmazza.
* **Teljes tartománynév:** Az Azure VPN Gateway teljes tartományneve (FQDN).
* **ServerSecret:** A VPN-átjáró előmegosztott kulcsa.

## <a name="folder-contents"></a>Mappa tartalma

* Az **általános mappa** tartalmazza a nyilvános kiszolgáló tanúsítványát és a VpnSettings.xml fájlt. A VpnSettings.xml fájl az általános ügyfél konfigurálásához szükséges információkat tartalmazza.

* Előfordulhat, hogy a letöltött zip-fájl **WindowsAmd64** és **WindowsX86** mappákat is tartalmaz. Ezek a mappák tartalmazzák az SSTP és a IKEv2 Windows-ügyfelekhez készült telepítőjét. A telepítéshez rendszergazdai jogosultságok szükségesek az ügyfélen.
