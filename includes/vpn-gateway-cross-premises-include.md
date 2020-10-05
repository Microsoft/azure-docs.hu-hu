---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/16/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c82e3d2c978a7980d02bc686346acdcb21dd591
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "66425742"
---
|  | **Pont – hely kapcsolat** | **Helyek közötti** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Azure által támogatott szolgáltatások** |Cloud Services és Virtual Machines |Cloud Services és Virtual Machines |[Szolgáltatáslista](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Jellemző sávszélességek** |Az átjáró termékváltozata alapján |Jellemzően < 1 Gb/s összesített |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Támogatott protokollok** |Secure Sockets Tunneling Protocol (SSTP), OpenVPN és IPsec |IPsec |Közvetlen kapcsolat VLAN-okon, NSP-k VPN technológiáin keresztül (MPLS, VPLS,...) |
| **Útválasztás** |Útvonalalapú (dinamikus) |Támogatjuk a szabályzatalapú (statikus útválasztás) és az útvonalalapú (dinamikus útválasztási VPN) útválasztást |BGP |
| **Kapcsolat rugalmassága** |aktív-passzív |aktív-passzív vagy aktív-aktív |aktív-aktív |
| **Tipikus használati eset** |Prototípus-készítés, fejlesztői / tesztelői / laboratóriumi forgatókönyvek felhőszolgáltatásokhoz és virtuális gépekhez |Fejlesztői / tesztelői / laboratóriumi forgatókönyvek és kis léptékű termelési számítási feladatok felhőszolgáltatásokhoz és virtuális gépekhez |Hozzáférés az összes Azure-szolgáltatáshoz (ellenőrzött lista), nagyvállalati szintű és kritikus fontosságú számítási feladatokhoz, biztonsági mentésekhez, big data adatokhoz és az Azure-hoz DR-webhelyként |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Díjszabás** |[Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Díjszabás](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Műszaki dokumentáció** |[A VPN Gateway dokumentációja](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[A VPN Gateway dokumentációja](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[A ExpressRoute dokumentációja](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Gyakori kérdések** |[VPN Gateway – gyakori kérdések](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[VPN Gateway – gyakori kérdések](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[ExpressRoute – Gyakori kérdések](../articles/expressroute/expressroute-faqs.md) |
