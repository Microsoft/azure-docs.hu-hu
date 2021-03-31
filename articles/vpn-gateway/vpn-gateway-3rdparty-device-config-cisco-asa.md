---
title: A Cisco ASA-eszközök Azure VPN Gateway-hez való csatlakoztatásának mintája
description: Ez a cikk egy minta konfigurációt biztosít a Cisco ASA-eszközök Azure VPN Gateway-hez való csatlakoztatásához.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 82e00e0b519040c57fdfd798d8992086c61e5481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426173"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Példa konfiguráció: Cisco ASA-eszköz (IKEv2/nincs BGP)
Ez a cikk példákat tartalmaz a Cisco adaptív biztonsági berendezés (ASA) eszközeinek Azure VPN Gateway-hez való csatlakoztatására. Ez a példa a IKEv2 rendszert futtató Cisco ASA-eszközökre vonatkozik a Border Gateway Protocol (BGP) nélkül. 

## <a name="device-at-a-glance"></a>Eszköz dióhéjban

* Eszköz gyártója: **Cisco**
* Eszköz modellje: **ASA**           
* Cél verziója: **8,4 és újabb**
* Tesztelt modell: **ASA 5505**
* Tesztelt verzió: **9,2**             
* IKE-verzió: **IKEv2**                  
* BGP: **nem**      
* Azure VPN Gateway típusa: **Route-alapú VPN Gateway**

> [!NOTE]
> A minta konfigurációja egy Cisco ASA-eszközt csatlakoztat egy Azure **Route-alapú** VPN-átjáróhoz. A kapcsolatok Egyéni IPsec/IKE-házirendet használnak a **UsePolicyBasedTrafficSelectors** beállítással, a [jelen cikkben](vpn-gateway-connect-multiple-policybased-rm-ps.md)leírtak szerint.
>
> A minta megköveteli, hogy az ASA-eszközök a **IKEv2** szabályzatot használják hozzáférés-lista alapú konfigurációkkal, nem VTI. A VPN-eszközök gyártójának specifikációit megkeresve ellenőrizze, hogy a IKEv2 szabályzat támogatott-e a helyszíni VPN-eszközökön.


## <a name="vpn-device-requirements"></a>VPN-eszközre vonatkozó követelmények
Az Azure VPN-átjárók a szabványos IPsec/IKE Protocol Suite-csomagokat használják helyek közötti (S2S) VPN-alagutak létrehozására. A részletes IPsec/IKE protokoll paramétereinek és az Azure VPN-átjárók alapértelmezett titkosítási algoritmusait lásd: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Megadhatja a titkosítási algoritmusok és a kulcsfontosságú erősségek pontos kombinációját egy adott kapcsolatok esetében a [titkosítási követelmények](vpn-gateway-about-compliance-crypto.md)című témakörben leírtak szerint. Ha az algoritmusok és a kulcsfontosságú erősségek pontos kombinációját adja meg, ügyeljen arra, hogy a megfelelő specifikációkat használja a VPN-eszközökön.

## <a name="single-vpn-tunnel"></a>Egyetlen VPN-alagút
Ez a konfiguráció egyetlen S2S VPN-alagutat tartalmaz az Azure VPN Gateway és egy helyszíni VPN-eszköz között. Igény szerint konfigurálhatja a BGP-t a VPN-alagúton keresztül.

![Egyetlen S2S VPN-alagút](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Az Azure-konfigurációk létrehozásával kapcsolatos részletes utasításokért lásd: [önálló VPN-alagút beállítása](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Virtuális hálózat és VPN Gateway-információk
Ez a szakasz a minta paramétereit sorolja fel.

| **Paraméter**                | **Érték**                    |
| ---                          | ---                          |
| Virtuális hálózati címek előtagjai        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN Gateway IP-címe         | Azure_Gateway_Public_IP      |
| Helyszíni címek előtagjai | 10.51.0.0/16<br>10.52.0.0/16 |
| Helyszíni VPN-eszköz IP-címe    | OnPrem_Device_Public_IP     |
| * Virtual Network BGP ASN-es                | 65010                        |
| * Azure BGP-társ IP-címe           | 10.12.255.30                 |
| * Helyszíni BGP ASN-es         | 65050                        |
| * Helyszíni BGP-társ IP-címe     | 10.52.255.254                |
|                              |                              |

\* Nem kötelező paraméter a BGP-hez.

### <a name="ipsecike-policy-and-parameters"></a>IPsec/IKE-házirend és paraméterek
A következő táblázat a mintában használt IPsec/IKE algoritmusokat és paramétereket sorolja fel. A VPN-eszközök és a belső vezérlőprogram verziója által támogatott algoritmusok ellenőrzéséhez forduljon a VPN-eszköz specifikációhoz.

| **IPsec/IKEv2**  | **Érték**                            |
| ---              | ---                                  |
| IKEv2-titkosítás | AES256                               |
| IKEv2-integritás  | SHA384                               |
| DH-csoport         | DHGroup24                            |
| * IPsec-titkosítás | AES256                               |
| * IPsec-integritás  | SHA1                                 |
| PFS-csoport        | PFS24                                |
| Gyorsmódú biztonsági társítás élettartama   | 7 200 másodperc                         |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors $True |
| Előre megosztott kulcs   | PreSharedKey                         |
|                  |                                      |

\* Egyes eszközökön az IPsec-integritásnak null értékűnek kell lennie, ha az IPsec titkosítási algoritmus AES-GCM.

### <a name="asa-device-support"></a>ASA-eszközök támogatása

* A IKEv2-támogatáshoz a 8,4-es és újabb ASA-verzió szükséges.

* Az 5. csoporton túli DH-csoport és PFS-csoport támogatásához az ASA 9-es. x verziója szükséges.

* Az AES-GCM és az IPsec-integritás és az SHA-256, az SHA-384, vagy az SHA-512 használatával történő IPsec-titkosítás támogatása az ASA 9-es. x verzióját igényli. Ez a támogatási követelmény az újabb ASA-eszközökre vonatkozik. A közzététel időpontjában a 5505, 5510, 5520, 5540, 5550 és 5580 ASA-modellek nem támogatják ezeket az algoritmusokat. A VPN-eszközök és a belső vezérlőprogram verziója által támogatott algoritmusok ellenőrzéséhez forduljon a VPN-eszköz specifikációhoz.


### <a name="sample-device-configuration"></a>Minta-eszköz konfigurációja
A parancsfájl egy olyan mintát biztosít, amely az előző szakaszokban leírt konfigurációtól és paraméterektől függ. A S2S VPN-alagút konfigurációja a következő részekből áll:

1. Felületek és útvonalak
2. Hozzáférési listák
3. IKE-házirend és-paraméterek (1. fázis vagy Main Mode)
4. IPsec-házirend és paraméterek (2. fázis vagy gyors mód)
5. Egyéb paraméterek, például TCP MSS-befogó

> [!IMPORTANT]
> A minta parancsfájl használata előtt végezze el a következő lépéseket. Cserélje le a parancsfájlban szereplő helyőrző értékeket a konfiguráció eszközbeállítások értékére.

* A belső és külső felületek felületi konfigurációjának megadása.
* Azonosítsa a belső/magán-és kívüli/nyilvános hálózatokon lévő útvonalakat.
* Győződjön meg arról, hogy az eszközön a nevek és a szabályzatok száma egyedi.
* Győződjön meg arról, hogy az eszközön a titkosítási algoritmusok támogatottak.
* Cserélje le a következő **helyőrző értékeket** a konfiguráció tényleges értékeire:
  - Külső felület neve: **kívül**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Virtuális hálózat és helyi hálózati átjáró neve: **VNetName** és **LNGName**
  - Virtuális hálózat és helyszíni hálózati címek **előtagjai**
  - Megfelelő **netmasks**

#### <a name="sample-script"></a>Példaszkript

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Egyszerű hibakeresési parancsok

Hibakeresési célokra használja az alábbi ASA-parancsokat:

* Az IPsec vagy az IKE biztonsági társítás (SA) megjelenítése:
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Hibakeresési mód megadása:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    A `debug` parancsok jelentős kimenetet hozhatnak ki a konzolon.

* Az eszköz aktuális konfigurációinak megjelenítése:
    ```
    show run
    ```
    `show`Alparancsok használata az eszköz konfigurációjának bizonyos részeinek listázásához, például:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Következő lépések
Az aktív-aktív létesítmények közötti és VNet kapcsolatok konfigurálásával kapcsolatban lásd: [Active-Active VPN Gateway-átjárók konfigurálása](vpn-gateway-activeactive-rm-powershell.md).
