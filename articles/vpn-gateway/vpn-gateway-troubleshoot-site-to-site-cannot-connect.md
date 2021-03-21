---
title: 'Azure-helyek közötti VPN-kapcsolat hibáinak megoldása, amely nem tud csatlakozni a titleSuffix: Azure VPN Gateway'
description: Megtudhatja, hogyan végezhet hibakeresést egy helyek közötti VPN-kapcsolaton, amely hirtelen leáll, és nem lehet újracsatlakozni.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 4454afbe543f374ebd745af7cd59be1ab745036f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670887"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Hibaelhárítás: az Azure-helyek közötti VPN-kapcsolat nem tud csatlakozni, és leáll

Miután konfigurált egy helyek közötti VPN-kapcsolatot egy helyszíni hálózat és egy Azure-beli virtuális hálózat között, a VPN-kapcsolat hirtelen leáll, és nem lehet újracsatlakozni. Ez a cikk a probléma megoldásához szükséges hibaelhárítási lépéseket ismerteti. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A probléma megoldásához először próbálja meg [alaphelyzetbe állítani az Azure VPN-átjárót](./reset-gateway.md) , és állítsa alaphelyzetbe az alagutat a helyszíni VPN-eszközről. Ha a probléma továbbra is fennáll, kövesse az alábbi lépéseket a probléma okának meghatározásához.

### <a name="prerequisite-step"></a>Előfeltételi lépés

Keresse meg az Azure VPN Gateway típusát.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. A típussal kapcsolatos információkért tekintse meg a VPN Gateway **Áttekintés** lapját.
    
    ![Az átjáró áttekintése](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>1. lépés Ellenőrizze, hogy a helyszíni VPN-eszköz érvényesítve van-e

1. Ellenőrizze, hogy az [ellenőrzött VPN-eszközt és az operációs rendszer verzióját használja-](vpn-gateway-about-vpn-devices.md#devicetable)e. Ha az eszköz nem ellenőrzött VPN-eszköz, előfordulhat, hogy kapcsolatba kell lépnie az eszköz gyártójával, és meg kell tudnia, hogy van-e kompatibilitási probléma.

2. Győződjön meg arról, hogy a VPN-eszköz megfelelően van konfigurálva. További információ: az [eszköz konfigurációs mintáinak szerkesztése](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>2. lépés A megosztott kulcs ellenőrzése

Hasonlítsa össze a helyszíni VPN-eszköz megosztott kulcsát az Azure Virtual Network VPN-nel, hogy megbizonyosodjon róla, hogy a kulcsok egyeznek. 

Az Azure VPN-kapcsolat megosztott kulcsának megtekintéséhez használja az alábbi módszerek egyikét:

**Azure Portal**

1. Lépjen a létrehozott VPN Gateway-helyek közötti kapcsolathoz.

2. A **Beállítások** szakaszban kattintson a **megosztott kulcs** elemre.
    
    ![Megosztott kulcs](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Resource Manager telepítési modell esetében:

```azurepowershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>
```

A klasszikus üzembe helyezési modell esetében:

```azurepowershell
Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName
```

### <a name="step-3-verify-the-vpn-peer-ips"></a>3. lépés A VPN-társ IP-címeinek ellenőrzése

-   Az Azure-beli **helyi hálózati átjáró** objektum IP-definíciójának meg kell egyeznie a helyszíni eszköz IP-címével.
-   A helyszíni eszközön beállított Azure Gateway IP-definíciónak meg kell egyeznie az Azure Gateway IP-címével.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>4. lépés: A UDR és a NSG megtekintése az átjáró alhálózatán

Ellenőrizze és távolítsa el a felhasználó által megadott útválasztást (UDR) vagy hálózati biztonsági csoportokat (NSG) az átjáró alhálózatán, majd tesztelje az eredményt. Ha a probléma megoldódott, ellenőrizze a UDR vagy NSG alkalmazott beállításokat.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>5. lépés Helyszíni VPN-eszköz külső csatolójának címe

- Ha a VPN-eszköz internet felé irányuló IP-címe az Azure-beli **helyi hálózat** definíciójában szerepel, időnként leválaszthatja a kapcsolatot.
- Az eszköz külső felületének közvetlenül az interneten kell lennie. Nem lehet hálózati címfordítás vagy tűzfal az Internet és az eszköz között.
- Ha úgy szeretné konfigurálni a tűzfal-fürtözést, hogy virtuális IP-címmel rendelkezzen, meg kell szüntetnie a fürtöt, és közvetlenül egy nyilvános felületen kell közzétennie a VPN-berendezést ahhoz, hogy az átjáró társítható legyen.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>6. lépés Győződjön meg arról, hogy az alhálózatok pontosan egyeznek (Azure Policy-alapú átjárók)

-   Ellenőrizze, hogy a virtuális hálózati címtartomány (ok) pontosan egyeznek-e az Azure-beli virtuális hálózat és a helyszíni definíciók között.
-   Győződjön meg arról, hogy az alhálózatok pontosan egyeznek a **helyi hálózati átjáró** és a helyszíni hálózat helyszíni definíciói között.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>7. lépés Az Azure Gateway Health mintavétel ellenőrzése

1. Nyissa meg az állapot-mintavételt a következő URL-címre való tallózással:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Kattintson a tanúsítványon keresztüli figyelmeztetésre.
3. Ha választ kap, a VPN-átjáró kifogástalannak minősül. Ha nem kap választ, előfordulhat, hogy az átjáró nem kifogástalan állapotú, vagy az átjáró alhálózatán lévő NSG okozza a problémát. A következő szöveg egy mintául szolgáló Válasz:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>8. lépés Győződjön meg arról, hogy a helyszíni VPN-eszközön engedélyezve van-e a tökéletes továbbítási titoktartási funkció

A tökéletes továbbítási titoktartási funkció a lekapcsolódási problémákhoz vezethet. Ha a VPN-eszközön engedélyezve van a megfelelő továbbítási titoktartás, tiltsa le a szolgáltatást. Ezután frissítse a VPN Gateway IPsec-házirendjét.

## <a name="next-steps"></a>Következő lépések

-   [Helyek közötti kapcsolat konfigurálása virtuális hálózathoz](./tutorial-site-to-site-portal.md)
-   [IPsec/IKE-házirend konfigurálása a helyek közötti VPN-kapcsolatokhoz](vpn-gateway-ipsecikepolicy-rm-powershell.md)