---
title: Azure Load Balancer úszó IP-konfiguráció
description: Azure Load Balancer úszó IP-cím áttekintése
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94699406"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer úszó IP-konfiguráció

A Load Balancer számos funkciót biztosít mind az UDP-, mind a TCP-alkalmazásokhoz.

## <a name="floating-ip"></a>Nem fix IP-cím

Egyes alkalmazási forgatókönyvek előnyben részesítettek, vagy megkövetelik, hogy ugyanazt a portot használja több alkalmazás-példány a háttér-készletben lévő egyetlen virtuális gépen. A portok használatának gyakori példái közé tartozik a fürtözés a magas rendelkezésre álláshoz, a hálózati virtuális berendezések, valamint a több TLS-végpont ismételt titkosítás nélküli kimutatása. Ha több szabályban szeretné felhasználni a háttér-portot, akkor a szabály definíciójában engedélyeznie kell a lebegőpontos IP-címet.

A **lebegőpontos IP-cím** az Azure terminológiai része, amely a Direct Server Return (DSR) néven ismert részét képezi. A DSR két részből áll:

- Folyamat topológiája
- IP-cím-hozzárendelési séma

A platform szintjén Azure Load Balancer mindig a DSR-folyamat topológiájában működik, függetlenül attól, hogy engedélyezve van-e a lebegőpontos IP-cím. Ez azt jelenti, hogy a folyamat kimenő részét mindig közvetlenül a forráshoz kell visszaírni a folyamatba.
A lebegő IP-címek nélkül az Azure egy hagyományos terheléselosztási IP-cím-hozzárendelési sémát tesz elérhetővé a könnyű használat érdekében (a virtuálisgép-példányok IP-címe). A lebegőpontos IP-cím engedélyezése a terheléselosztó előtérbeli IP-címére való leképezést a további rugalmasság érdekében módosítja. További információ [itt](load-balancer-multivip-overview.md).

## <a name="limitations"></a><a name = "limitations"></a>Korlátozások

- A lebegőpontos IP-cím jelenleg nem támogatott a másodlagos IP-konfigurációknál a terheléselosztási forgatókönyvek esetében

## <a name="next-steps"></a>Következő lépések

- A Load Balancer használatának első lépéseiért tekintse meg [a nyilvános standard Load Balancer létrehozását](quickstart-load-balancer-standard-public-portal.md) ismertető témakört.
- További információ a [Azure Load Balancer kimenő kapcsolatokról](load-balancer-outbound-connections.md).
- További információ a [Azure Load Balancerról](load-balancer-overview.md).
- Tudnivalók az [állapot](load-balancer-custom-probe-overview.md)-mintavételekről.
- Ismerje meg a [standard Load Balancer diagnosztikát](load-balancer-standard-diagnostics.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/network-security-groups-overview.md).