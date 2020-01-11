---
title: Az Azure-helyek közötti VPN-kapcsolat hibáinak megoldása időnként leszakad
description: Ismerje meg, hogy miként lehet elhárítani a két hálózat közötti pont-pont típusú VPN-kapcsolat által rendszeresen leválasztott problémát.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 82054099a5a496e99c49135ab98ee1163af19784
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75862560"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Hibaelhárítás: az Azure helyek közötti VPN-kapcsolata időnként megszakad

Előfordulhat, hogy a probléma az, hogy egy új vagy meglévő Microsoft Azure helyek közötti VPN-kapcsolat nem stabil, vagy rendszeresen bontja a kapcsolatot. Ez a cikk hibaelhárítási lépéseket tartalmaz a probléma okának azonosításához és megoldásához. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

### <a name="prerequisite-step"></a>Előfeltételi lépés

Az Azure-beli virtuális hálózati átjáró típusának ellenõrzése:

1. Lépjen [Azure Portal](https://portal.azure.com).
2. A típussal kapcsolatos információkért tekintse meg a virtuális hálózati átjáró **Áttekintés** lapját.
    
    ![Az átjáró áttekintése](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1\. lépés ellenőrizze, hogy a helyszíni VPN-eszköz ellenőrzése megtörtént-e

1. Ellenőrizze, hogy az [ellenőrzött VPN-eszközt és az operációs rendszer verzióját használja-](vpn-gateway-about-vpn-devices.md#devicetable)e. Ha a VPN-eszköz nincs érvényesítve, előfordulhat, hogy kapcsolatba kell lépnie az eszköz gyártójával, és látnia kell, hogy van-e kompatibilitási probléma.
2. Győződjön meg arról, hogy a VPN-eszköz megfelelően van konfigurálva. További információ: az [eszköz konfigurációs mintáinak szerkesztése](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>2\. lépés a biztonsági társítás beállításainak megtekintése (házirend alapú Azure-beli virtuális hálózati átjárók esetén)

1. Győződjön meg arról, hogy a virtuális hálózat, az alhálózatok és a **helyi hálózati átjáró** definíciójának tartománya Microsoft Azure azonos a helyszíni VPN-eszköz konfigurációjával.
2. Ellenőrizze, hogy a biztonsági társítás beállításai egyeznek-e.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>3\. lépés: a felhasználó által megadott útvonalak vagy hálózati biztonsági csoportok keresése az átjáró alhálózatán

Az átjáró-alhálózat felhasználó által megadott útvonala korlátozhatja a forgalmat, és engedélyezheti a többi forgalmat. Így úgy tűnik, hogy a VPN-kapcsolat nem megbízható a forgalom számára, és mások számára is jó. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>4\. lépés: az "egy VPN-alagút alhálózati pár" beállítás használata (házirend alapú virtuális hálózati átjárók esetén)

Győződjön meg arról, hogy a helyszíni VPN-eszköz a házirend-alapú virtuális hálózati átjárók esetében **egyetlen VPN-alagúttal** rendelkezik.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>5\. lépés: a biztonsági társítás korlátozásának keresése (házirend-alapú virtuális hálózati átjárók esetén)

A házirend-alapú virtuális hálózati átjáró 200 alhálózati biztonsági társítási párral rendelkezik. Ha az Azure-beli virtuális hálózati alhálózatok száma a helyi alhálózatok számának szorzata (200), akkor a szórványos alhálózatok leválasztását láthatja.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>6\. lépés a helyszíni VPN-eszköz külső csatolójának keresése

- Ha a VPN-eszköz internet felé irányuló IP-címe az Azure-beli **helyi hálózati átjáró** definíciójában szerepel, időnként leválaszthatja a kapcsolatot.
- Az eszköz külső felületének közvetlenül az interneten kell lennie. Nem lehet hálózati címfordítás (NAT) vagy tűzfal az Internet és az eszköz között.
-  Ha úgy konfigurálja a tűzfal-fürtözést, hogy virtuális IP-címmel rendelkezzen, meg kell szüntetnie a fürtöt, és a VPN-berendezést közvetlenül egy olyan nyilvános interfészre kell tenni, amelyhez az átjáró képes.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>7\. lépés azt vizsgálja, hogy a helyszíni VPN-eszközön engedélyezve van-e a megfelelő továbbítási titoktartás

A **tökéletes továbbítási titoktartási** funkció a leválasztási problémákhoz vezethet. Ha a VPN-eszközön engedélyezve van a megfelelő **továbbítási titoktartás** , tiltsa le a szolgáltatást. Ezután [frissítse a virtuális hálózati átjáró IPsec-házirendjét](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Következő lépések

- [Helyek közötti kapcsolat konfigurálása virtuális hálózathoz](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [IPsec/IKE-szabályzat konfigurálása két hálózat közötti pont-pont típusú VPN-kapcsolatokhoz](vpn-gateway-ipsecikepolicy-rm-powershell.md)

