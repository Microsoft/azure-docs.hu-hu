---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67178855"
---
### <a name="what-is-expressroute-global-reach"></a>Mi a ExpressRoute Global Reach?

A ExpressRoute Global Reach egy olyan Azure-szolgáltatás, amely a Microsoft globális hálózatán keresztül csatlakoztatja a helyszíni hálózatokat a ExpressRoute szolgáltatással. Ha például van egy olyan privát adatközpontja Kaliforniában, amely a ExpressRoute a Szilícium-völgyben található, és egy másik, a Dallasban ExpressRoute-beli privát adatközpont csatlakozik a Dallasban, a ExpressRoute Global Reach segítségével, a két ExpressRoute-kapcsolaton keresztül, a Cross adatközpont-forgalom pedig a Microsoft hálózati gerincén keresztül fog haladni.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Hogyan engedélyezheti vagy letilthatja a ExpressRoute Global Reach?

A ExpressRoute Global Reach a ExpressRoute-áramkörök összekapcsolásával engedélyezheti. A funkciót letilthatja az áramkör leválasztásával. Tekintse meg a [konfigurációt](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Szükségem van a ExpressRoute Global Reach ExpressRoute Premiumra?

Ha a ExpressRoute-áramkörök ugyanabban a geopolitikai régióban találhatók, nincs szükség a prémium szintű ExpressRoute, hogy összekapcsolja őket. Ha két ExpressRoute-áramkör különböző geopolitikai régiókban van, akkor mindkét áramkörhöz prémium szintű ExpressRoute van szükség ahhoz, hogy lehetővé váljon a kapcsolatok közötti kapcsolat. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Hogyan kell fizetnem a ExpressRoute-Global Reachért?

A ExpressRoute lehetővé teszi a helyszíni hálózatról a Microsoft Cloud Serviceshez való kapcsolódást. A ExpressRoute Global Reach lehetővé teszi a saját helyszíni hálózatai közötti kapcsolatot a meglévő ExpressRoute-áramkörök használatával, a Microsoft globális hálózatának kihasználásával. A ExpressRoute Global Reach számlázása a meglévő ExpressRoute-szolgáltatástól függetlenül történik. A szolgáltatás minden ExpressRoute-áramkörön való engedélyezéséhez kiegészítő díj vonatkozik. A ExpressRoute Global Reach által engedélyezett helyszíni hálózatok közötti forgalomért a kilépési arányért kell fizetni a forrásnál, és a belépési arányt a célhelyen. A díjszabás azon a zónán alapul, ahol az áramkör található.

### <a name="where-is-expressroute-global-reach-supported"></a>Hol támogatott a ExpressRoute Global Reach?

A ExpressRoute Global Reach a [Select országok/régiók vagy helyek](../articles/expressroute/expressroute-global-reach.md)esetében támogatott. Az ExpressRoute-áramköröket az ezekben az országokban/régiókban vagy helyeken található, egymáshoz tartozó helyeken kell létrehozni.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Több mint két helyszíni hálózatom van, amelyek mindegyike egy ExpressRoute-áramkörhöz csatlakozik. Engedélyezhető a ExpressRoute Global Reach az összes helyszíni hálózat összekapcsolásához?

Igen, ha az áramkörök a támogatott országokban/régiókban találhatók. Egyszerre két ExpressRoute-áramkört kell összekötnie. Teljes mértékben felhasználható hálózat létrehozásához enumerálnia kell az összes áramköri párt, és ismételje meg a konfigurációt. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Engedélyezhető a ExpressRoute Global Reach két ExpressRoute-áramkör között ugyanazon a társi helyen?

Nem. A két áramkörnek különböző egymástól eltérő helyekről kell származnia. Ha egy támogatott országban/régióban található metró több ExpressRoute-társítási hellyel rendelkezik, összekapcsolhatja az adott metróban a különböző társi helyeken létrehozott ExpressRoute-áramköröket. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Ha a ExpressRoute-Global Reach engedélyezve van az X és az Y áramkör között, és az Y és a (Z) áramköri áramkör között, akkor az X és a Circuit Z áramkörhöz csatlakozó helyszíni hálózatok a Microsoft hálózatán keresztül kommunikálnak egymással?

Nem. A két helyszíni hálózat közötti kapcsolat engedélyezéséhez explicit módon csatlakoztatnia kell a megfelelő ExpressRoute-áramköröket. A fenti példában az X és a Circuit Z áramkört kell összekapcsolni. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Milyen hálózati átviteli sebességre számíthatok a helyszíni hálózatok között a ExpressRoute Global Reach engedélyezése után?

A ExpressRoute Global Reach által engedélyezett helyszíni hálózatok közötti hálózati átviteli sebességet a két ExpressRoute-áramkör kisebb mérete szabja meg. A helyszíni és az Azure közötti adatforgalom, valamint a telephelyek közötti forgalom ugyanazon az áramkörön osztoznak, és ugyanaz a sávszélesség-korlát vonatkozik rájuk. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>A ExpressRoute Global Reach a meghirdethető útvonalak számának és a fogadott útvonalak számának korlátai?

A Microsoft Azure-beli privát kapcsolaton keresztül meghirdethető útvonalak száma 4000-kor, standard szintű áramkörön vagy 10000-on, prémium szintű áramkörön. Az Azure-beli privát hálózatokon a Microsofttól kapott útvonalak száma az Azure-beli virtuális hálózatok és a ExpressRoute Global Reachon keresztül csatlakoztatott más helyszíni hálózatok útvonalának összege lesz. Győződjön meg arról, hogy megfelelő maximális előtag-korlátot állít be a helyszíni útválasztón. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Mi a ExpressRoute-Global Reach SLA-ja?

A ExpressRoute Global Reach ugyanazt a [rendelkezésre állási SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) -t fogja biztosítani, mint a normál ExpressRoute szolgáltatás.
