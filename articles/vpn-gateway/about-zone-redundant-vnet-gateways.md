---
title: Tudnivalók a Azure Availability Zones-beli virtuális hálózati átjárók használatáról
description: VPN-és ExpressRoute-átjárók üzembe helyezése Azure Availability Zonesban, a rugalmasság, a méretezhetőség és a virtuális hálózati átjárók magasabb rendelkezésre állásának biztosítása érdekében.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2030469262baf406635fd170af384e154fec6ae6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89401112"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Tudnivalók a Azure Availability Zones-beli virtuális hálózati átjárók használatáról

A VPN-és ExpressRoute-átjárók üzembe helyezhetők a [Azure Availability Zonesban](../availability-zones/az-overview.md). Ez rugalmasságot, méretezhetőséget és magasabb szintű rendelkezésre állást biztosít a virtuális hálózati átjárók számára. Az átjárók Azure-beli rendelkezésre állási zónákban történő üzembe helyezésével fizikailag és logikailag is elválaszthatók a régióban található átjárók, miközben az Azure-ral létesített helyszíni hálózati kapcsolat megvédhető a zónaszintű hibáktól.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Zónaredundáns átjárók

A virtuális hálózati átjárók rendelkezésre állási zónákon keresztüli automatikus üzembe helyezéséhez használhatja a zóna-redundáns virtuális hálózati átjárókat. A Zone-redundáns átjárókkal kihasználhatja az Azure-beli üzleti szempontból alapvető fontosságú, méretezhető szolgáltatások elérését.

<br>
<br>

![zóna – redundáns átjárók ábrája](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Zóna-átjárók

Az átjárók egy adott zónában való üzembe helyezéséhez a zóna-átjárókat használhatja. Ha egy zóna-átjárót helyez üzembe, az átjáró összes példánya ugyanabban a rendelkezésre állási zónában lesz telepítve.

<br>
<br>

![zóna-átjárók ábrája](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>Átjáró-termékváltozatok

A Zone-redundáns és a zóna-átjárók új Gateway SKU-ként érhetők el. Új Virtual Network Gateway SKU-ket adott hozzá az Azure-ban az Regions. Ezek a SKU-azonosítók a ExpressRoute és a VPN Gateway vonatkozó meglévő SKU-hoz hasonlóak, azzal a különbséggel, hogy azok a zóna-redundáns és a zónákhoz tartozó átjárók. Ezeket a SKU-ket az SKU-név "az" részében azonosíthatja.

További információ az átjárók SKU-ról: [VPN Gateway SKU](vpn-gateway-about-vpngateways.md#gwsku) -i és [ExpressRoute Gateway SKU](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku)-i.

## <a name="public-ip-skus"></a><a name="pipskus"></a>Nyilvános IP-SKU

A Zone-redundáns átjárók és a zóna-átjárók egyaránt az Azure nyilvános IP-erőforrás *standard* SKU-ra támaszkodnak. Az Azure nyilvános IP-erőforrásának konfigurációja határozza meg, hogy az Ön által telepített átjáró a zóna redundáns vagy a zónákhoz van-e telepítve. Ha *alapszintű* SKU-val hoz létre egy nyilvános IP-erőforrást, akkor az átjáró nem rendelkezik zónával, és az átjáró erőforrásai regionálisak lesznek.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Zónaredundáns átjárók

Amikor nyilvános IP-címet hoz létre a **szabványos** nyilvános IP-SKU használatával a zóna megadása nélkül, a viselkedés eltér attól függően, hogy az átjáró VPN-átjáró vagy ExpressRoute-átjáró. 

* A VPN-átjárók esetében a két átjáró közül bármelyik kettőt üzembe helyezi a rendszer a következő három zónából a zóna-redundancia biztosítása érdekében. 
* Egy ExpressRoute-átjáró esetében, mivel kettőnél több példány is lehet, az átjáró a három zónára terjedhet.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Zóna-átjárók

Amikor nyilvános IP-címet hoz létre a **standard** nyilvános IP-SKU használatával, és megadja a zónát (1, 2 vagy 3), az összes átjáró-példány ugyanabban a zónában lesz telepítve.

### <a name="regional-gateways"></a><a name="piprg"></a>Regionális átjárók

Amikor nyilvános IP-címet hoz létre az **alapszintű** nyilvános IP-SKU használatával, az átjáró regionális átjáróként van üzembe helyezve, és nem rendelkezik az átjáróba épített összes zónával.

## <a name="faq"></a><a name="faq"></a>GYIK

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Mi változik, ha az új SKU-t üzembe helyezem?

Az átjárók az Ön szemszögéből is üzembe helyezhetők a zóna-redundanciával. Ez azt jelenti, hogy az átjárók összes példánya Azure Availability Zones lesz telepítve, és az egyes rendelkezésre állási zónák egy másik hiba-és frissítési tartomány. Így az átjárók megbízhatóbbak, elérhetők és rugalmasak lesznek a zónák meghibásodása miatt.

### <a name="can-i-use-the-azure-portal"></a>Használhatom a Azure Portal?

Igen, a Azure Portal használatával telepítheti az új SKU-t. Ezeket az új SKU-ket azonban csak azokban az Azure-régiókban fogja látni, amelyek Azure Availability Zones rendelkeznek.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Milyen régiókban érhetők el az új SKU-i használata?

Az új SKU-ket olyan Azure-régiókban lehet elérni, amelyek Azure Availability Zones – Közép-USA, Közép-Franciaország, Észak-Európa, Nyugat-Európa és az USA 2. nyugati régiója, USA keleti régiója, USA 2. keleti régiója, Délkelet-Ázsia, Kelet Egyesült Királyság déli régiója-Japán A jövőben az Azure-beli nyilvános régiókban elérhetővé tesszük a zóna-redundáns átjárókat.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Módosíthatom/telepíthetem vagy frissíthetem a meglévő virtuális hálózati átjárókat a zóna-redundáns vagy a zónák közötti átjáróra?

Jelenleg nem támogatott a meglévő virtuális hálózati átjárók áttelepítése a zóna-redundáns vagy a zónák közötti átjáróra. Azonban törölheti a meglévő átjárót, és újból létrehozhatja a zóna-redundáns vagy a zónákhoz tartozó átjárót.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Telepíthetek VPN-és Express Route Gateway-átjárókat is ugyanazon a virtuális hálózaton?

Az azonos virtuális hálózatban található VPN-és Express Route-átjárók együttes létezése is támogatott. Az átjáró-alhálózat esetében azonban foglaljon le egy/27 IP-címtartományt.

## <a name="next-steps"></a>Következő lépések

[Zónaredundáns virtuális hálózati átjáró létrehozása](create-zone-redundant-vnet-gateway.md)
