---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9734859c0bf22201c146e5d8a220f3146f6051c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179009"
---
Az alábbi táblázatban az átjárótípusok és azok becsült összesített átviteli sebessége tekinthető meg az átjárók termékváltozata alapján. Ez a tábla a Resource Manager és a klasszikus üzembe helyezési modellekre vonatkozik. 

A díjszabás a különböző átjáró-termékváltozatok esetében nem változik. További információkért lásd: [VPN Gateway-díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway).

Vegye figyelembe, hogy az UltraPerformance átjáró-termékváltozat nem szerepel ebben a táblázatban. Az UltraPerformance termékváltozattal kapcsolatos információkért lásd az [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) dokumentációját.

|  | **VPN Gateway teljesítménye (1)** | **VPN Gateway IPsec-alagútjainak maximális száma (2)** | **ExpressRoute-átjáró teljesítménye** | **VPN Gateway és ExpressRoute párhuzamos használata** |
| --- | --- | --- | --- | --- |
| **Alapszintű termékváltozat (3)(5)(6)** |100 Mbps |10 |500 Mbps (6) |No |
| **Standard termékváltozat (4)(5)** |100 Mbps |10 |1000 Mbps |Yes |
| **Nagy teljesítményű termékváltozat (4)** |200 Mbit/s |30 |2000 Mbps |Yes |


(1)A VPN teljesítménye hozzávetőleges becslés az ugyanazon Azure-régióban található virtuális hálózatok közötti mérések alapján. Nem garantált, hogy ezt a teljesítményt fogja tapasztalni a létesítmények közötti internetes kapcsolatok során. Ez a maximális mérhető teljesítmény.

(2)Az alagutak száma az útvonalalapú VPN-ekre utal. A házirendalapú VPN csak egyetlen, helyek közötti VPN-alagutat támogat.

(3) a BGP az alapszintű termékváltozathoz nem támogatott.

(4) Ez a termékváltozat nem támogatja a házirendalapú VPN-eket. Ezek csak az alapszintű termékváltozat esetén támogatottak.

(5) Ez a termékváltozat nem támogatja az aktív/aktív módú S2S VPN Gateway-kapcsolatokat. Az aktív/aktív mód csak a nagy teljesítményű termékváltozat esetén támogatott.

(6) az alapszintű SKU elavult a ExpressRoute-sel való használathoz.
