---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/23/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 685160e1075a0d4192157200ee4f5d981ca5f472
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953475"
---
### <a name="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus"></a>Minden Azure VPN-átjáróhoz tartozó termékváltozat támogatja az egyéni IPsec/IKE-házirendet?

Az Egyéni IPsec/IKE-házirendet az alapszintű SKU kivételével az összes Azure-beli SKU támogatja.

### <a name="how-many-policies-can-i-specify-on-a-connection"></a>Hány házirendeket adhatok meg egy kapcsolathoz?

Egy adott kapcsolathoz csak ***egy*** házirendet adhat meg.

### <a name="can-i-specify-a-partial-policy-on-a-connection-for-example-only-ike-algorithms-but-not-ipsec"></a>Megadhatok részleges házirendet egy kapcsolathoz? (például csak IKE-algoritmusokat, IPsec nélkül)

Nem, minden algoritmust és paramétert meg kell adnia mind az IKE (Elsődleges mód), mind az IPsec (Gyors mód) esetében. A részleges házirend-megadás nem engedélyezett.

### <a name="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy"></a>Milyen algoritmusokat és milyen erősségű kulcsokat támogat az egyéni házirend?

Az alábbi táblázatban megtekintheti az ügyfelek által konfigurálható, támogatott titkosítási algoritmusokat és kulcserősségeket. Minden mezőhöz választania kell egy lehetőséget.

| **IPsec/IKEv2**  | **Beállítások**                                                                   |
| ---              | ---                                                                           |
| IKEv2-titkosítás | AES256, AES192, AES128, DES3, DES                                             |
| IKEv2-integritás  | SHA384, MD5, SHA1, SHA256                                                     |
| DH-csoport         | DHGroup24, ECP384, ECP256, DHGroup14 (DHGroup2048), DHGroup2, DHGroup1, Nincs  |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs      |
| IPsec-integritás  | GCMAES256, GCMAES192, GCMAES128, SHA-256, SHA1, MD5                            |
| PFS-csoport        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nincs                              |
| Gyorsmódú biztonsági társítás élettartama   | Másodperc (egész szám; **min. 300**/alapértelmezett érték: 27000 másodperc)<br>KB (egész szám; **min. 1024**/alapértelmezett érték: 102400000 KB)           |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors ($True/$False; alapértelmezett: $False)                 |
|                  |                                                                               |

> [!IMPORTANT]
> *  A DHGroup2048 és PFS2048 megegyeznek az IKE és IPsec PFS **14-es** Diffie-Hellman csoportjával. A teljes leképezések megtekintéséhez lásd: [Diffie-Hellman csoport](#DH).
> * GCMAES-algoritmusok esetében ugyanazt a GCMAES-algoritmust és kulcshosszt kell megadnia az IPsec-titkosítás és -integritás esetében.
> * A IKEv2 fő módú SA élettartama 28 800 másodpercen belül megoldódott az Azure VPN Gateway-átjárók esetében.
> * A gyorsmódú biztonsági társítás élettartama paraméter megadása opcionális. Ha nem ad meg értéket, a rendszer az alapértelmezett értékeket használja: 27 000 másodperc (7,5 óra) és 102 400 000 kB (102 GB).
> * A UsePolicyBasedTrafficSelector a kapcsolat egy opcionális paramétere. Tekintse meg a "UsePolicyBasedTrafficSelectors" következő GYIK-elemét.

### <a name="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations"></a>Mindennek egyeznie kell az Azure VPN-átjáró házirendjében és a helyszíni VPN-eszközkonfigurációkban?

A helyszíni VPN-eszköz konfigurációjának meg kell egyezniük velük, vagy tartalmazniuk kell az alábbi, az Azure IPsec/IKE-házirendben megadott algoritmusokat és paramétereket:

* IKE titkosítási algoritmus
* IKE integritási algoritmus
* DH-csoport
* IPsec titkosítási algoritmus
* IPsec integritási algoritmus
* PFS-csoport
* Forgalomválasztó (*)

AZ SA-k élettartamai helyi szinten definiáltak, így azoknak nem kell megegyezniük.

Ha engedélyezi az **UsePolicyBasedTrafficSelectors** lehetőséget, meg kell győződnie arról, hogy a VPN-eszköz megegyező forgalomválasztóihoz a helyszíni hálózat az Azure virtuális hálózatától (helyi hálózati átjáró), illetve onnan érkező előtagjainak minden kombinációja van definiálva a bármely elemek közötti definíció helyett. Például ha a helyszíni hálózati előtagok a 10.1.0.0/16 és a 10.2.0.0/16, a virtuális hálózati előtagok pedig 192.168.0.0/16 és 172.16.0.0/16, az alábbi forgalomválasztókat kell megadnia:
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

További információ: [Több helyszíni házirendalapú VPN-eszköz csatlakoztatása](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name="which-diffie-hellman-groups-are-supported"></a><a name ="DH"></a>Mely Diffie-Hellman csoportok támogatottak?

Az alábbi táblázat az IKE (DHGroup) és IPsec (PFSGroup) esetében támogatott Diffie-Hellman csoportokat tartalmazza:

| **Diffie-Hellman Group**  | **DH-csoport**              | **PFS-csoport** | **A kulcs hossza** |
| ---                       | ---                      | ---          | ---            |
| 1                         | DHGroup1                 | PFS1         | 768 bites MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 bites MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 bites MODP  |
| 19                        | ECP256                   | ECP256       | 256 bites ECP    |
| 20                        | ECP384                   | ECP384       | 384 bites ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 bites MODP  |
|                           |                          |              |                |

További információ: [RFC3526](https://tools.ietf.org/html/rfc3526) és [RFC5114](https://tools.ietf.org/html/rfc5114).

### <a name="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways"></a>Az egyéni házrend helyettesíti az alapértelmezett IPsec/IKE-házirendet az Azure VPN-átjárókon?

Igen. Miután megadott egy egyéni házirendet egy kapcsolathoz, az Azure VPN-átjáró csak a kapcsolat házirendjét használja, mind IKE-kezdeményezőként, mind IKE-válaszadóként.

### <a name="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected"></a>Ha eltávolítok egy egyéni IPsec/IKE-házirendet, azzal a kapcsolat nem védetté válik?

Nem, a kapcsolatot továbbra is védi az IPsec/IKE. Miután eltávolítja a kapcsolat egyéni házirendjét, az Azure VPN-átjáró visszaáll [az IPsec/IKE-javaslatok alapértelmezett listájára](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md), és újraindítja az IKE-kézfogást a helyszíni VPN-eszközzel.

### <a name="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection"></a>Az IPsec/IKE-házirend hozzáadása vagy frissítése megszakítja a VPN-kapcsolatot?

Igen, ez okozhat rövid (néhány másodperces) megszakítást, mivel az Azure VPN-átjáró bontja a meglévő kapcsolatot, és újraindítja az IKE-kézfogást, így újra létrehozza az IPsec-alagutat az új titkosítási algoritmusokkal és paraméterekkel. Győződjön meg róla, hogy a helyszíni VPN-eszközt is konfigurálta ugyanazokkal az algoritmusokkal és kulcserősségekkel, így minimálisra csökkentheti a megszakítások időtartamát.

### <a name="can-i-use-different-policies-on-different-connections"></a>Használhatok különböző házirendeket különböző kapcsolatokhoz?

Igen. Az egyéni házirendeket kapcsolatonként hozza létre. A különböző kapcsolatokhoz különböző IPsec/IKE-házirendeket hozhat létre és alkalmazhat. Emellett a kapcsolatok egy részhalmazára is alkalmazhat egyéni házirendeket. A fennmaradó kapcsolatok az Azure alapértelmezett IPsec/IKE-házirendjét használják.

### <a name="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well"></a>Használhatok egyéni házirendet a virtuális hálózatok közötti kapcsolatokhoz is?

Igen, mind az IPsec létesítmények közötti kapcsolataihoz, mind a virtuális hálózatok közötti kapcsolatokhoz alkalmazhat egyéni házirendet.

### <a name="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources"></a>Ugyanazt a házirendet kell megadnom mindkét, virtuális hálózatok közötti kapcsolat erőforrásaihoz?

Igen. A virtuális hálózatok közötti alagút két kapcsolati erőforrásból áll az Azure-ban, amelyek a két különböző irányba mutatnak. Győződjön meg róla, hogy mindkét kapcsolati erőforrás azonos házirenddel rendelkezik, ellenkező esetben a virtuális hálózatok közötti kapcsolat nem jön létre.

### <a name="what-is-the-default-dpd-timeout-value-can-i-specify-a-different-dpd-timeout"></a>Mi a DPD alapértelmezett időtúllépési értéke? Megadhatok egy másik DPD-időtúllépést is?

Az alapértelmezett DPD-időkorlát 45 másodperc. Megadhat egy másik DPD időtúllépési értéket minden IPsec-vagy VNet-VNet kapcsolathoz 9 másodperc és 3600 másodperc között.

### <a name="does-custom-ipsecike-policy-work-on-expressroute-connection"></a>Működik az egyéni IPsec/IKE-házirend az ExpressRoute-kapcsolatokkal?

Nem. Az IPsec/IKE-házirend csak az S2S VPN- és a virtuális hálózatok közötti kapcsolatokkal, az Azure VPN-átjárókon keresztül működik.

### <a name="how-do-i-create-connections-with-ikev1-or-ikev2-protocol-type"></a>Hogyan kapcsolatok létrehozása IKEv1 vagy IKEv2 protokoll-típussal?

Az IKEv1-kapcsolatok az összes Útvonalalapú VPN-típuson hozhatók létre, kivéve az alapszintű SKU, standard SKU és más [örökölt SKU](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#gwsku)-ket. Kapcsolatok létrehozásakor megadhatja a IKEv1 vagy a IKEv2 kapcsolati protokoll típusát is. Ha nem adja meg a kapcsolati protokoll típusát, a rendszer a IKEv2 alapértelmezett beállításként használja, ahol alkalmazható. További információt a [PowerShell-parancsmag](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) dokumentációjában talál. Az SKU-típusok és a IKEv1/IKEv2 támogatásával kapcsolatban lásd: [átjárók csatlakoztatása házirend alapú VPN-eszközökhöz](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md).

### <a name="is-transit-between-between-ikev1-and-ikev2-connections-allowed"></a>Engedélyezett a IKEv1 és a IKEv2 közötti kapcsolatok közötti átvitel?

Igen. A IKEv1 és a IKEv2 kapcsolatok közötti átvitel támogatott.

### <a name="can-i-have-ikev1-site-to-site-connections-on-basic-skus-of-routebased-vpn-type"></a>Használhatok IKEv1 helyek közötti kapcsolatokat a Útvonalalapú VPN-típusának alapszintű SKU-ban?

Nem. Az alapszintű SKU nem támogatja ezt.

### <a name="can-i-change-the-connection-protocol-type-after-the-connection-is-created-ikev1-to-ikev2-and-vice-versa"></a>Módosíthatom a kapcsolati protokoll típusát a kapcsolat létrehozása után (IKEv1 a IKEv2 és fordítva)?

Nem. A létrehozást követően a IKEv1/IKEv2 protokollok nem módosíthatók. Törölje, majd hozza létre újra a kívánt protokolltípus új kapcsolatát.

### <a name="where-can-i-find-more-configuration-information-for-ipsec"></a>Hol találhatok további konfigurációs adatokat az IPsec szolgáltatáshoz?

Lásd: [az IPSec/IKE-szabályzat konfigurálása S2S vagy VNet – VNet kapcsolatokhoz](../articles/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md).