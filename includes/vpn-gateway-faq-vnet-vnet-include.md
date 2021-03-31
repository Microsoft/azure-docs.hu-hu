---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "67178940"
---
A VNet – VNet gyakori kérdések a VPN Gateway-kapcsolatokra vonatkoznak. További információ a VNet-kezeléssel kapcsolatban: [Virtual Network peering](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Felszámol az Azure díjat a virtuális hálózatok közötti adatforgalomért?

Az azonos régión belüli VNet-VNet forgalom a VPN Gateway-kapcsolat használata esetén mindkét irányban ingyenes. A régiók közötti VNet-VNet kimenő forgalomra a VNet alapuló kimenő adatátviteli díjak számítanak. További információ: [VPN Gateway díjszabása oldal](https://azure.microsoft.com/pricing/details/vpn-gateway/). Ha a virtuális hálózatok a VPN-átjáró helyett VNet-társítás használatával csatlakoztatja, tekintse meg a [Virtual Network díjszabását](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Az interneten keresztül VNet a forgalom VNet?

Nem. A VNet-VNet forgalom a Microsoft Azure gerincen halad át, nem az interneten.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Létrehozhatok VNet közötti kapcsolatot Azure Active Directory (HRE) bérlők között?

Igen, az Azure VPN-átjárókat használó VNet-VNet kapcsolatok a HRE-bérlők között működnek.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Biztonságos-e a virtuális hálózatok közötti adatforgalom?

Igen, az IPsec/IKE titkosítás védi.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Szükségem van VPN-eszközre a virtuális hálózatok egymáshoz kapcsolásához?

Nem. Az Azure Virtual Networkök összekapcsolása nem igényel VPN-eszközöket, hacsak nem szükséges a létesítmények közötti kapcsolat.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>A virtuális hálózataimnak ugyanabban a régióban kell lenniük?

Nem. A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Ha a virtuális hálózatok nem ugyanabban az előfizetésben van, akkor az előfizetéseket ugyanahhoz a Active Directory bérlőhöz kell társítani?

Nem.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Összekapcsolhatok egymással különböző Azure-példányokban található virtuális hálózatokat? 

Nem. A virtuális hálózatok közötti kapcsolat az azonos Azure-példányon belüli virtuális hálózatok csatlakoztatását támogatja. Például nem hozható létre kapcsolat a globális Azure és a kínai/német/USA kormányzati Azure-példányok között. Ezekhez a forgatókönyvekhez használjon helyek közötti VPN-kapcsolat használatát.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>A virtuális hálózatok közötti kapcsolatot használhatom többhelyes kapcsolatokhoz?

Igen. A virtuális hálózati kapcsolat használható többhelyes virtuális VPN-ekkel együtt.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Hány helyszíni helyhez és virtuális hálózathoz kapcsolódhat egyetlen virtuális hálózat?

Tekintse meg az [átjáróra vonatkozó követelményeket](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) tartalmazó táblázatot.

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>A virtuális hálózatok közötti kapcsolattal csatlakoztathatok a virtuális hálózaton kívüli virtuális gépeket vagy felhőszolgáltatásokat?

Nem. A virtuális hálózatok közötti kapcsolat támogatja a virtuális hálózatok csatlakoztatását, Nem támogatja a virtuális hálózatokban nem lévő virtuális gépek és felhőalapú szolgáltatások összekapcsolását.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Lehetnek felhőalapú szolgáltatások vagy terheléselosztási végpontok virtuális hálózatok?

Nem. A felhőalapú szolgáltatás vagy egy terheléselosztási végpont nem terjedhet ki a virtuális hálózatok között még akkor sem, ha azok össze vannak kapcsolva.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Használhatok házirendalapú VPN-típust a VNet – VNet vagy a többhelyes kapcsolatokhoz?

Nem. A VNet-to-VNet és a többhelyes kapcsolatokhoz Azure VPN Gateway szükséges a Útvonalalapú (korábbi nevén Dynamic routing) VPN-típusokkal.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Összekapcsolhatok egy RouteBased (útvonalapú) VPN-típussal rendelkező virtuális hálózatot egy házirendalapú VPN-típussal rendelkezővel?

Nem, mindkét virtuális hálózatnak Route-alapú (korábban dinamikus útválasztású) VPN-t kell használnia.

### <a name="do-vpn-tunnels-share-bandwidth"></a>A VPN-alagutak osztoznak a sávszélességen?

Igen. A virtuális hálózat minden VPN-alagútja az Azure VPN Gateway átjárón elérhető sávszélességet használja, és azonos VPN-átjáró üzemidőre vonatkozó SLA-t az Azure-ban.

### <a name="are-redundant-tunnels-supported"></a>Támogatottak a redundáns alagutak?

A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak, amikor a virtuális hálózati átjáró aktív-aktívként van konfigurálva.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Lehetnek átfedő címterek a virtuális hálózatok közötti konfigurációkhoz?

Nem. Nem lehetnek átfedő IP-címtartományok.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Lehetnek-e egymással átfedésben lévő címterek a csatlakoztatott virtuális hálózatok és helyszíni helyek között?

Nem. Nem lehetnek átfedő IP-címtartományok.



