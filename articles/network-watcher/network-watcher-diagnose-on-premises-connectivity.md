---
title: Helyszíni kapcsolatok diagnosztizálása VPN-átjárón keresztül
titleSuffix: Azure Network Watcher
description: Ez a cikk azt ismerteti, hogyan diagnosztizálható a helyszíni kapcsolat a VPN-átjárón keresztül az Azure Network Watcher erőforrás-hibaelhárítással.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3b8b53446799eec3473d63c89672393a35f9787a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670955"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Helyszíni kapcsolatok diagnosztizálása VPN-átjárók használatával

Az Azure VPN Gateway lehetővé teszi, hogy olyan hibrid megoldást hozzon létre, amely a helyszíni hálózat és az Azure-beli virtuális hálózat közötti biztonságos kapcsolat szükségességével foglalkozik. A követelmények egyediek, így a helyszíni VPN-eszköz választható. Az Azure jelenleg [számos olyan VPN-eszközt](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) támogat, amelyek az eszközök forgalmazójával való együttműködésben folyamatosan vannak érvényesítve. A helyszíni VPN-eszköz konfigurálása előtt tekintse át az eszközre vonatkozó konfigurációs beállításokat. Hasonlóképpen, az Azure VPN Gateway a kapcsolatok létesítéséhez használt [támogatott IPsec-paraméterek](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) készletével van konfigurálva. Jelenleg nincs lehetőség az IPsec-paraméterek adott kombinációjának megadására vagy kiválasztására az Azure-VPN Gateway. A helyszíni és az Azure közötti sikeres kapcsolat létrehozásához a helyszíni VPN-eszköz beállításainak összhangban kell lenniük az Azure VPN Gateway által előírt IPsec-paraméterekkel. Ha a beállítások helytelenek, a kapcsolat megszakad, és a hibák elhárítása nem volt triviális, és általában órákig tartott a probléma azonosítása és megoldása érdekében.

Az Azure Network Watcher hibaelhárítási funkciója lehetővé teszi az átjáróval és a kapcsolatokkal kapcsolatos problémák diagnosztizálását, és néhány percen belül elegendő információval szolgál a probléma kijavításáról.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Eset

Helyek közötti kapcsolatot szeretne konfigurálni az Azure és a helyszíni kapcsolat között a FortiGate használatával helyszíni VPN Gatewayként. A forgatókönyv megvalósításához a következő beállításokat kell megkövetelni:

1. Virtual Network Gateway – az Azure-beli VPN Gateway
1. Helyi hálózati átjáró – helyszíni [(FortiGate) VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway) ábrázolás az Azure-felhőben
1. Helyek közötti kapcsolat (útvonalon alapuló) – [a VPN Gateway és a helyszíni útválasztó közötti kapcsolat](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection)
1. [FortiGate konfigurálása](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

A helyek közötti konfiguráció konfigurálásának részletes lépéseit ismertető útmutató a következő címen érhető el: [VNet létrehozása helyek közötti kapcsolattal a Azure Portal használatával](../vpn-gateway/tutorial-site-to-site-portal.md).

A kritikus fontosságú konfigurációs lépések egyike az IPsec-kommunikációs paraméterek konfigurálása, ami bármilyen helytelen konfiguráció a helyszíni hálózat és az Azure közötti kapcsolat elvesztését eredményezi. Az Azure VPN-átjárók jelenleg úgy vannak konfigurálva, hogy támogassák az 1. fázishoz tartozó következő IPsec-paramétereket. Ahogy az alábbi táblázatban is látható, az Azure VPN Gateway által támogatott titkosítási algoritmusok a következők: AES256, AES128 és 3DES.

### <a name="ike-phase-1-setup"></a>IKE 1. fázis beállítása

| **Tulajdonság** | **Házirendalapú** | **Útvonalalapú és standard vagy High-Performance VPN Gateway** |
| --- | --- | --- |
| IKE verziószám |IKEv1 |IKEv2 |
| Diffie-Hellman Group |2. csoport (1024 bites) |2. csoport (1024 bites) |
| Hitelesítési módszer |Előre megosztott kulcs |Előre megosztott kulcs |
| Titkosítási algoritmusok |AES256 AES128 3DES |AES256 3DES |
| Kivonatoló algoritmus |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| 1. fázisú biztonsági társítás (SA) Élettartam (idő) |28 800 másodperc |28 800 másodperc |

Felhasználóként konfigurálnia kell a FortiGate, a [githubon](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt)megtalálhatja a minta konfigurációját. Nem tudtuk, hogy a FortiGate az SHA-512 használatát kivonatoló algoritmusként konfigurálta. Mivel ez az algoritmus nem támogatott a házirend-alapú kapcsolatok algoritmusa számára, a VPN-kapcsolat működik.

Ezek a problémák nehezen orvosolhatók, és a kiváltó okok gyakran nem intuitívek. Ebben az esetben egy támogatási jegyet nyithat meg, hogy segítséget kapjon a probléma megoldásához. Az Azure Network Watcher API-val kapcsolatos hibaelhárítása azonban lehetővé teszi ezeket a problémákat saját maga is azonosítani.

## <a name="troubleshooting-using-azure-network-watcher"></a>Hibaelhárítás az Azure Network Watcher használatával

A kapcsolat diagnosztizálásához kapcsolódjon Azure PowerShellhoz, és indítsa el a `Start-AzNetworkWatcherResourceTroubleshooting` parancsmagot. A parancsmag használatáról a következő cikkben talál további információt: [Virtual Network Gateway and Connections – PowerShell](network-watcher-troubleshoot-manage-powershell.md). Ez a parancsmag akár néhány percet is igénybe vehet.

A parancsmag befejezése után navigáljon a parancsmagban megadott tárolási helyre, ahol részletes információkhoz juthat a probléma és a naplókról. Az Azure Network Watcher egy zip-mappát hoz létre, amely a következő naplófájlokat tartalmazza:

![1][1]

Nyissa meg a IKEErrors.txt nevű fájlt, és a következő hibaüzenetet jeleníti meg, amely a helyszíni IKE-beállítások helytelen konfigurálásával kapcsolatos problémát jelez.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Részletes információkat kaphat a Scrubbed-wfpdiag.txt a hibáról, ahogy ebben az esetben megemlíti, hogy a `ERROR_IPSEC_IKE_POLICY_MATCH` kapcsolat nem működik megfelelően.

Egy másik gyakori hibás konfiguráció a helytelen megosztott kulcsok meghatározása. Ha az előző példában eltérő megosztott kulcsokat adott meg, a IKEErrors.txt a következő hibaüzenetet jeleníti meg: `Error: Authentication failed. Check shared key` .

Az Azure Network Watcher-hibakeresési funkciója lehetővé teszi a VPN Gateway és az egyszerű PowerShell-parancsmagokkal való kapcsolat diagnosztizálását és hibakeresését. Jelenleg támogatjuk a következő feltételek diagnosztizálását, és még több feltétel hozzáadásával dolgozunk.

### <a name="gateway"></a>Átjáró

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nem észlelhető hiba. |Igen|
| GatewayNotFound | Nem található az átjáró vagy az átjáró nincs kiépítve. |Nem|
| PlannedMaintenance |  Az átjárópéldány karbantartása van folyamatban.  |Nem|
| UserDrivenUpdate | Egy felhasználói frissítés folyamatban van. Ez lehet átméretezési művelet. | Nem |
| VipUnResponsive | Az átjáró elsődleges példánya nem érhető el. Ez akkor fordul elő, ha az állapot-ellenőrzés sikertelen. | Nem |
| PlatformInActive | A platformmal kapcsolatos probléma áll fenn. | No|
| ServiceNotRunning | A mögöttes szolgáltatás nem fut. | No|
| NoConnectionsFoundForGateway | Nem található kapcsolat az átjárón. Ez csak egy figyelmeztetés.| No|
| ConnectionsNotConnected | A kapcsolatok egyike sincs csatlakoztatva. Ez csak egy figyelmeztetés.| Yes|
| GatewayCPUUsageExceeded | Az átjáró jelenlegi használati CPU-használata > 95%. | Yes |

### <a name="connection"></a>Kapcsolat

| Hibatípus | Ok | Napló|
|---|---|---|
| NoFault | Ha nem észlelhető hiba. |Igen|
| GatewayNotFound | Nem található az átjáró vagy az átjáró nincs kiépítve. |Nem|
| PlannedMaintenance | Az átjárópéldány karbantartása van folyamatban.  |Nem|
| UserDrivenUpdate | Egy felhasználói frissítés folyamatban van. Ez lehet átméretezési művelet.  | Nem |
| VipUnResponsive | Az átjáró elsődleges példánya nem érhető el. Akkor következik be, amikor az állapot mintavétele sikertelen. | No |
| ConnectionEntityNotFound | Hiányzik a kapcsolatok konfigurációja. | No |
| ConnectionIsMarkedDisconnected | A kapcsolat "leválasztva" jelölésű. |No|
| ConnectionNotConfiguredOnGateway | A mögöttes szolgáltatáshoz nincs konfigurálva a hálózat. | Yes |
| ConnectionMarkedStandby | A mögöttes szolgáltatás készenléti állapotban van megjelölve.| Yes|
| Hitelesítés | Az előmegosztott kulcs nem egyezik. | Yes|
| PeerReachability | A társ-átjáró nem érhető el. | Yes|
| IkePolicyMismatch | A társ-átjáró olyan IKE-szabályzatokkal rendelkezik, amelyeket az Azure nem támogat. | Yes|
| WfpParse hiba | Hiba történt a WFP-napló elemzése során. |Igen|

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan ellenőrizheti VPN Gateway kapcsolatot a PowerShell [Network Watcher](network-watcher-monitor-with-azure-automation.md) Azure Automation-lel

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png