---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3c1e34bb418f9be2e26afc117343f1fa50bd8566
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "76309050"
---
Ez a gyakori kérdések a klasszikus üzemi modellt használó P2S-kapcsolatokra vonatkoznak.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Milyen ügyfél operációs rendszereket használhatok pont–hely kapcsolatokhoz?

A következő ügyféloldali operációs rendszerek támogatottak:

* Windows 7 (32 bites és 64 bites)
* Windows Server 2008 R2 (csak 64 bites)
* Windows 8 (32 bites és 64 bites)
* Windows 8.1 (32 bites és 64 bites)
* Windows Server 2012 (csak 64 bites)
* Windows Server 2012 R2 (csak 64 bites)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Használhatok bármely olyan szoftveres VPN-ügyfelet, amely támogatja az SSTP-t a pont – hely kapcsolatokhoz?

Nem. A támogatás csak a felsorolt Windows operációs rendszerek verzióira korlátozódik.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Hány VPN-ügyfél végpontja létezhet a pont – hely konfigurációban?

A VPN-ügyfél végpontok mennyisége az átjáró SKU-jának és a protokolltól függ.
[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Használhatom a saját PKI legfelső szintű hitelesítésszolgáltatómat a pont–hely kapcsolathoz?

Igen. Korábban csak önaláírt főtanúsítványt lehetett használni. Továbbra is legfeljebb 20 főtanúsítvány tölthető fel.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Átadhatom a proxykat és a tűzfalakat pont – hely kapcsolattal?

Igen. Biztonságos szoftvercsatorna Tunneling Protocol (SSTP) protokollt használunk a tűzfalakon keresztüli bújtatáshoz. Ez az alagút HTTPS-kapcsolatként jelenik meg.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Ha újraindítok egy pont–hely kapcsolat használatára konfigurált ügyfélszámítógépet, a VPN automatikusan újracsatlakozik?

Alapértelmezés szerint az ügyfélszámítógép nem hozza létre automatikusan a VPN-kapcsolatot.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Támogatja a pont – hely kapcsolat az automatikus újracsatlakozást és a DDNS-t a VPN-ügyfeleken?

Nem. Az automatikus Újrakapcsolódás és a DDNS jelenleg nem támogatott a pont – hely VPN-kapcsolatokban.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Használhatom-e a helyek közötti és a pont – hely konfigurációkat ugyanahhoz a virtuális hálózathoz?

Igen. Mindkét megoldás akkor működik, ha Útvonalalapú VPN-típussal rendelkezik az átjáróhoz. A klasszikus üzemi modellhez dinamikus átjáróra van szükség. A pont – hely kapcsolat nem támogatott a **-VpnType házirendalapú** parancsmagot használó statikus útválasztási VPN-átjárók vagy átjárók esetén.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Konfigurálhatok úgy egy pont–hely ügyfelet, hogy több virtuális hálózathoz csatlakozzon egyszerre?

Igen. A virtuális hálózatok azonban nem rendelkezhetnek átfedésben lévő IP-előtagokkal, és a pont – hely címtartomány nem fedik át egymást a virtuális hálózatok között.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Milyen átviteli sebességre számíthatok a helyek közötti és a pont–hely kapcsolatok esetében?

Az átviteli sebesség fenntartása nehéz a VPN-alagutakban. Az IPsec és az SSTP erős titkosítást használó VPN-protokoll. Az átviteli sebességet a telephely és az Internet közötti késés és sávszélesség is korlátozza.
