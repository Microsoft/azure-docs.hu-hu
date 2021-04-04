---
title: Azure-beli hálózati kapcsolat
description: Ez az oldal az Azure hálózati szolgáltatásain alapuló, hálózati kapcsolattal és megoldással kapcsolatos alkalmazási forgatókönyvet ismerteti.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: 018afa1b2a31ebd44925a3fc79cbdc729b2d4695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202497"
---
# <a name="cross-network-connectivity"></a>Hálózatok közötti kapcsolatok

A fabrikam Inc. jelentős fizikai jelenléttel és Azure-beli üzembe helyezéssel rendelkezik az USA keleti régiójában. A fabrikam háttérbeli kapcsolatot létesít a helyszíni és az Azure-beli üzemelő példányok között a ExpressRoute-on keresztül. Ehhez hasonlóan a contoso Ltd. rendelkezik az USA nyugati régiójában található jelenléttel és Azure-beli üzembe helyezéssel. A contoso háttérbeli kapcsolatot létesít a helyszíni és az Azure-beli üzemelő példányok között a ExpressRoute-on keresztül.  

A fabrikam Inc. beszerzi a contoso Ltd-t. Az egyesítést követően a fabrikam szeretné összekapcsolni a hálózatokat. Az alábbi ábra a forgatókönyvet szemlélteti:

![Az alkalmazás forgatókönyve](./media/cross-network-connectivity/premergerscenario.png)

A fenti ábrán látható szaggatott nyíl jelzi a kívánt hálózati összekapcsolást. Konkrétan három típusú kapcsolatra van szükség: 1) a fabrikam és a contoso virtuális hálózatok Cross Connect, 2) a helyszíni és a virtuális hálózatok Cross összekapcsolásával (azaz a fabrikam helyszíni hálózat a contoso VNet való csatlakoztatásával, a contoso helyszíni hálózat és a fabrikam VNet közötti kapcsolattal) és 3) a fabrikam és a contoso helyszíni hálózat közötti kapcsolattal. 

Az alábbi táblázat a contoso Ltd. ExpressRoute privát társának útválasztási táblázatát mutatja be az egyesítés előtt.

![Contoso ExpressRoute-útválasztási táblázat az egyesítés előtt](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

Az alábbi táblázat egy virtuális gép tényleges útvonalait mutatja be a contoso-előfizetésben, az egyesítés előtt. A táblázatban a VNet lévő virtuális gép ismeri a VNet és a contoso helyszíni hálózatát az alapértelmezett beállításoktól függetlenül.

![Contoso VM-útvonalak az egyesítés előtt](./media/cross-network-connectivity/contosovm-routes-premerger.png)

A következő táblázat a fabrikam Inc. ExpressRoute privát társának útválasztási táblázatát mutatja be az egyesítés előtt.

![Fabrikam ExpressRoute-útválasztási táblázat az egyesítés előtt](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

Az alábbi táblázat egy virtuális gép tényleges útvonalait mutatja be a fabrikam-előfizetésben, az egyesítés előtt. A táblázatban a VNet lévő virtuális gép ismeri a VNet és a fabrikam helyszíni hálózatát az alapértelmezett beállításoktól függetlenül.

![Fabrikam VM-útvonalak az egyesítés előtt](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

Ebben a cikkben áttekintjük a lépésről lépésre, és megtudhatjuk, hogyan érheti el a kívánt kapcsolatokat a következő Azure hálózati funkciók használatával:

* [Virtuális hálózati társviszony][Virtual network peering] 
* [Virtuális hálózat ExpressRoute-kapcsolatai][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Virtuális hálózatok közötti kapcsolat

A virtuális hálózatok társítása (VNet peering) a legoptimálisabb és a legjobb hálózati teljesítményt biztosítja két virtuális hálózat összekapcsolása esetén. A VNet peering a két virtuális hálózatok egyazon Azure-régión (általában VNet-társításon) belül és két különböző Azure-régióban (általában globális VNet-társításnak nevezik) is támogatja. 

Konfiguráljuk a globális VNet-társítást a contoso és a fabrikam Azure-előfizetések virtuális hálózatok között. A virtuális hálózatok két virtuális hálózat közötti társításának létrehozásával kapcsolatban tekintse meg [a virtuális hálózati társak létrehozása][Configure VNet peering] című cikket.

A következő képen a hálózati architektúra látható a globális VNet-társítás konfigurálása után.

![Az architektúra a VNet után](./media/cross-network-connectivity/vnet-peering.png )

A következő táblázat a contoso előfizetési virtuális gépen ismert útvonalakat mutatja. Figyeljen a tábla utolsó bejegyzésére. Ez a bejegyzés a virtuális hálózatok közötti kapcsolat összekapcsolásának eredménye.

![Contoso VM-útvonalak a VNet-társítás után](./media/cross-network-connectivity/contosovm-routes-peering.png)

A következő táblázat a fabrikam előfizetési virtuális géphez ismert útvonalakat mutatja. Figyeljen a tábla utolsó bejegyzésére. Ez a bejegyzés a virtuális hálózatok közötti kapcsolat összekapcsolásának eredménye.

![Fabrikam VM-útvonalak a VNet-társítás után](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

A VNet-társítás közvetlenül két virtuális hálózatra hivatkozik (lásd a fenti két táblázat *VNetGlobalPeering* bejegyzésének következő ugrását)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Virtuális hálózatok csatlakoztatása a helyszíni hálózatokhoz

ExpressRoute áramkört több virtuális hálózathoz is összekapcsolhat. Tekintse meg a ExpressRoute-áramkörhöz csatlakoztatható virtuális hálózatok maximális számát az [előfizetés és a szolgáltatás korlátaiban][Subscription limits] . 

A virtuális hálózatok és a helyszíni hálózatok közötti kapcsolatok lehetővé tételéhez a fabrikam ExpressRoute áramkört a contoso előfizetés VNet és hasonló contoso ExpressRoute-áramkörhöz csatlakoztathatja a fabrikam előfizetési VNet. Ha egy virtuális hálózatot egy másik előfizetésben lévő ExpressRoute-áramkörhöz szeretne kapcsolni, egy engedélyt kell létrehoznia és használnia.  Tekintse meg a [virtuális hálózat ExpressRoute-áramkörhöz való kapcsolódását][Connect-ER-VNet]ismertető cikket.

Az alábbi képen a hálózati architektúra látható, miután konfigurálta a ExpressRoute kapcsolatát a virtuális hálózatokkal.

![Az architektúra a Expressroute közötti kapcsolatok után](./media/cross-network-connectivity/exr-x-connect.png)

A következő táblázat a contoso Ltd. ExpressRoute privát kapcsolatának útválasztási táblázatát mutatja be, miután a virtuális hálózatokat a helyszíni hálózatokhoz csatlakoztatta a ExpressRoute-n keresztül. Láthatja, hogy az útválasztási táblázat a virtuális hálózatokhoz tartozó útvonalakat is tartalmaz.

![A contoso ExpressRoute útválasztási táblázata az ExR és a virtuális hálózatok közötti csatlakozást követően](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

A következő táblázat a fabrikam Inc. ExpressRoute privát társításának útválasztási táblázatát mutatja be, miután a virtuális hálózatokat a helyszíni hálózatokhoz csatlakoztatta a ExpressRoute-n keresztül. Láthatja, hogy az útválasztási táblázat a virtuális hálózatokhoz tartozó útvonalakat is tartalmaz.

![A fabrikam ExpressRoute útválasztási táblázata az ExR és a virtuális hálózatok közötti kapcsolattal](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

A következő táblázat a contoso előfizetési virtuális gépen ismert útvonalakat mutatja. Figyeljen a tábla *Virtual Network Gateway* bejegyzéseire. A virtuális gép a helyszíni hálózatok útvonalait is látja.

![Contoso VM-útvonalak az ExR és a virtuális hálózatok csatlakoztatása után](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

A következő táblázat a fabrikam előfizetési virtuális géphez ismert útvonalakat mutatja. Figyeljen a tábla *Virtual Network Gateway* bejegyzéseire. A virtuális gép a helyszíni hálózatok útvonalait is látja.

![Fabrikam VM-útvonalak az ExR és a virtuális hálózatok csatlakoztatása után](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>A fabrikam és/vagy contoso-előfizetésekben a megfelelő hub-VNet is küllős virtuális hálózatok (a hub és a küllős kialakítás nem látható a jelen cikk architektúra-diagramjaiban). A hub VNet-átjárók és a ExpressRoute közötti kapcsolatok lehetővé teszik a Kelet-és Nyugat-hubok közötti kommunikációt és a küllőket is.
>

## <a name="cross-connecting-on-premises-networks"></a>Helyszíni hálózatok közötti kapcsolat

A ExpressRoute Global Reach kapcsolatot biztosít a különböző ExpressRoute-áramkörökhöz csatlakozó helyszíni hálózatok között. Konfigurálja Global Reach a contoso és a fabrikam ExpressRoute áramkörök között. Mivel a ExpressRoute-áramkörök különböző előfizetésekben vannak, létre kell hozniuk és használniuk kell az engedélyt. Lásd: a [ExpressRoute Global REACH konfigurálása][Configure Global Reach] című cikk részletes útmutatást nyújt.

Az alábbi képen a Global Reach konfigurálása után a hálózati architektúra látható.

![Az architektúra a Global Reach konfigurálása után](./media/cross-network-connectivity/globalreach.png)

A következő táblázat a contoso Ltd. ExpressRoute privát társának útválasztási táblázatát mutatja Global Reach konfigurálása után. Láthatja, hogy az útválasztási táblázat a helyszíni hálózatokhoz tartozó útvonalakat is tartalmazza. 

![Contoso ExpressRoute-útválasztási táblázat Global Reach után](./media/cross-network-connectivity/contosoexr-rt-gr.png)

A következő táblázat a fabrikam Inc. ExpressRoute privát összevonásának útválasztási táblázatát mutatja Global Reach konfigurálása után. Láthatja, hogy az útválasztási táblázat a helyszíni hálózatokhoz tartozó útvonalakat is tartalmazza.

![Fabrikam ExpressRoute-útválasztási táblázat Global Reach után]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>Következő lépések

A VNet és VNet kapcsolatos további kérdésekért lásd: [Virtual Network – gyakori kérdések][VNet-FAQ]. A ExpressRoute és a virtuális hálózati kapcsolattal kapcsolatos további kérdésekért tekintse meg a [ExpressRoute – gyakori kérdések][ER-FAQ] című témakört.

A Global Reach ország/régió alapján kerül bevezetésre az ország/régió területén. Ha szeretné megtekinteni, hogy Global Reach elérhető-e a kívánt országokban/régiókban, tekintse meg a következőt: [ExpressRoute Global REACH][Global Reach].

<!--Link References-->
[Virtual network peering]: ../virtual-network/virtual-network-peering-overview.md
[connection]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[Global Reach]: ./expressroute-global-reach.md
[Configure VNet peering]: ../virtual-network/create-peering-different-subscriptions.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[Subscription limits]: ../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits
[Connect-ER-VNet]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[ER-FAQ]: ./expressroute-faqs.md
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq