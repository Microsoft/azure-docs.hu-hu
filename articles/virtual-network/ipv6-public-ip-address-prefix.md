---
title: Nyilvános IPv6-cím előtagja az Azure Virtual Networkben
titlesuffix: Azure Virtual Network
description: Ismerje meg a nyilvános IPv6-cím előtagját az Azure Virtual Networkben.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3e3d3c01ba1190871da39ce27de6a95952d09be1
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538206"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Fenntartott nyilvános IPv6-cím előtagja

Az Azure-ban a kettős verem (IPv4 és IPv6) virtuális hálózatok (VNet) és a virtuális gépek (VM-EK) alapértelmezés szerint biztonságosak, mivel nem rendelkeznek internetkapcsolattal. Az Azure-ból beszerezhető nyilvános IPv6-címekkel könnyedén hozzáadhat IPv6-alapú internetkapcsolatot az Azure Load Balancerhez és a virtuális gépekhez.

A fenntartott nyilvános IP-címek a választott Azure-régióval és az Azure-előfizetéssel vannak társítva. A fenntartott (statikus) IPv6 nyilvános IP-címet áthelyezheti az előfizetésében található bármely Azure Load Balancer vagy virtuális gép között. Az IPv6-alapú nyilvános IP-címet teljes mértékben leválaszthatja, és a használatra kerül, amikor elkészült.

> [!WARNING]
> A nyilvános IP-címek véletlen törléséhez használja a figyelmeztetést. Egy nyilvános IP-cím törlése eltávolítja az előfizetésből, és nem fogja tudni visszaállítani (még az Azure-támogatás segítségével sem).

Az egyes IPv6-címek lefoglalása mellett az Azure IPv6-címek (más néven IP-előtag) összefüggő tartományait is fenntarthatja a használatra.  Az egyes IP-címekhez hasonlóan a fenntartott előtagok egy Ön által választott Azure-régióhoz, valamint az Azure-előfizetéshez kapcsolódnak. Egy előre jelezhető, összefüggő címtartomány megőrzése számos felhasználási területtel rendelkezik. Nagymértékben leegyszerűsítheti például az Azure által üzemeltetett alkalmazások IP- *szűrését* vállalata és ügyfelei számára, mivel a statikus IP-címtartományok a helyszíni tűzfalakra is könnyen beprogramozhatók.  Igény szerint létrehozhat egyéni nyilvános IP-címeket az IP-előtagból, és törölheti azokat az egyes nyilvános IP-címeket, amelyeket a fenntartott tartományba *visszaadottak* , így később újra felhasználhatja őket. Az IP-előtagban lévő összes IP-cím a kizárólagos használatra van fenntartva, amíg el nem távolítja az előtagot.



## <a name="ipv6-prefix-sizes"></a>IPv6-előtag mérete
A következő nyilvános IP-előtag-méretek érhetők el:

-  IPv6-előtag minimális mérete:/127 = 2 cím
-  IPv6-előtag maximális mérete:/124 = 16 cím

Az előtag mérete osztály nélküli Inter-Domain útválasztási (CIDR) maszk méretként van megadva. Például a/128 maszkja egy egyedi IPv6-címet jelöl, mivel az IPv6-címek a 128 BITS-ből állnak.

## <a name="pricing"></a>Díjszabás
 
Az Azure-beli nyilvános IP-címek, az egyedi IP-címek és az IP-címtartományok használatával kapcsolatos költségekért lásd: [nyilvános IP-cím díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Korlátozások
Az IPv6 az alapszintű nyilvános IP-címeknél csak a "dinamikus" kiosztással támogatott, ami azt jelenti, hogy az IPv6-cím akkor változik, ha törli és újból üzembe helyezi az alkalmazást (virtuális gép vagy terheléselosztó) az Azure-ban. A standard szintű IPv6 nyilvános IP-címek kizárólag statikus (fenntartott) foglalást támogatnak, bár a standard belső terheléselosztó a hozzájuk rendelt alhálózaton belül is támogatja a dinamikus kiosztást.  

Ajánlott eljárásként javasoljuk, hogy szabványos nyilvános IP-címeket és standard Load Balancereket használjon IPv6-alkalmazásaihoz.

## <a name="next-steps"></a>Következő lépések
- Foglaljon le egy nyilvános [IPv6-cím előtagot](ipv6-reserve-public-ip-address-prefix.md).
- További információ az [IPv6-címekről](ipv6-overview.md).
- Ismerje meg, [hogyan hozhat létre és használhat nyilvános IP-címeket](virtual-network-public-ip-address.md) (IPv4-és IPv6-alapú) az Azure-ban.
