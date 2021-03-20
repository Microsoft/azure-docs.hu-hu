---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b283add6cff1400cc3141f4fba3f0f3939ee34aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97665107"
---
|  | **Pont – hely kapcsolat** | **Helyek közötti** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Azure által támogatott szolgáltatások** |Cloud Services és Virtual Machines |Cloud Services és Virtual Machines |[Szolgáltatáslista](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Jellemző sávszélességek** |Az átjáró termékváltozata alapján |Jellemzően < 1 Gb/s összesített |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Támogatott protokollok** |Secure Sockets Tunneling Protocol (SSTP), OpenVPN és IPsec |IPsec |Közvetlen kapcsolat VLAN-okon, NSP-k VPN technológiáin keresztül (MPLS, VPLS,...) |
| **Útválasztás** |Útvonalalapú (dinamikus) |Támogatjuk a szabályzatalapú (statikus útválasztás) és az útvonalalapú (dinamikus útválasztási VPN) útválasztást |BGP |
| **Kapcsolat rugalmassága** |aktív-passzív |aktív-passzív vagy aktív-aktív |aktív-aktív |
| **Tipikus használati eset** |Biztonságos hozzáférés az Azure-beli virtuális hálózatokhoz távoli felhasználók számára |Fejlesztési/tesztelési/tesztkörnyezet-forgatókönyvek és kis-és közepes méretű éles számítási feladatok a Cloud Services és a Virtual Machines szolgáltatáshoz |Hozzáférés az összes Azure-szolgáltatáshoz (ellenőrzött lista), nagyvállalati szintű és kritikus fontosságú számítási feladatokhoz, biztonsági mentésekhez, big data adatokhoz és az Azure-hoz DR-webhelyként |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Díjszabás** |[Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Díjszabás](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Technikai dokumentáció** |[VPN Gateway dokumentáció](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[VPN Gateway dokumentáció](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[A ExpressRoute dokumentációja](https://azure.microsoft.com/documentation/services/expressroute/) |
| **GYIK** |[VPN Gateway – gyakori kérdések](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[VPN Gateway – gyakori kérdések](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[ExpressRoute – Gyakori kérdések](../articles/expressroute/expressroute-faqs.md) |
