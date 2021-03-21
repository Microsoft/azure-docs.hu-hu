---
title: 'Azure VPN Gateway: tudnivalók a P2S VPN-ügyfél profiljairól'
description: Ebből a cikkből megtudhatja, hogy milyen információkra van szüksége a VPN-ügyfél profiljához.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979070"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>A P2S VPN-ügyfél profil fájljainak használata

A profil fájljai a VPN-kapcsolat konfigurálásához szükséges információkat tartalmazzák. Ez a cikk segítséget nyújt a VPN-ügyfél profiljához szükséges információk beszerzésében és megismerésében.

## <a name="generate-and-download-profile"></a>Profil készítése és letöltése

Az ügyfél-konfigurációs fájlokat a PowerShell-lel vagy a Azure Portal használatával is létrehozhatja. Bármelyik metódus ugyanazt a zip-fájlt adja vissza.

### <a name="portal"></a>Portál

1. A Azure Portal navigáljon annak a virtuális hálózatnak a virtuális hálózati átjáróhoz, amelyhez csatlakozni szeretne.
1. A virtuális hálózati átjáró lapon válassza a **pont – hely konfiguráció** lehetőséget.
1. A pont – hely konfiguráció lap tetején válassza a **VPN-ügyfél letöltése** lehetőséget. Az ügyfél-konfigurációs csomag létrehozása néhány percet vesz igénybe.
1. A böngésző azt jelzi, hogy az ügyfél-konfiguráció zip-fájlja elérhető. Neve megegyezik az átjáró nevével. A mappák megtekintéséhez bontsa ki a fájlt.

### <a name="powershell"></a>PowerShell

A PowerShell használatával történő létrehozáshoz a következő példát használhatja:

1. A VPN-ügyfél konfigurációs fájljainak létrehozásakor a "-AuthenticationMethod" értéke "EapTls". A következő paranccsal hozhatja elő a VPN-ügyfél konfigurációs fájljait:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Másolja az URL-címet a böngészőjébe a zip-fájl letöltéséhez, majd bontsa ki a fájlt a mappák megtekintéséhez.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Az **OpenVPN-mappa** tartalmazza azt a *ovpn* -profilt, amelyet módosítani kell a kulcs és a tanúsítvány belefoglalásához. További információ: az [OpenVPN-ügyfelek konfigurálása az Azure VPN Gatewayhoz](vpn-gateway-howto-openvpn-clients.md#windows). Ha az Azure AD-hitelesítés be van jelölve a VPN-átjárón, akkor ez a mappa nem szerepel a zip-fájlban. Ehelyett keresse meg a AzureVPN mappát, és keresse meg a azurevpnconfig.xml.

## <a name="next-steps"></a>Következő lépések

További információ a pont – hely kapcsolatról: [Tudnivalók a pont – hely](point-to-site-about.md)kapcsolatról.
