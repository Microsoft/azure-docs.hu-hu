---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 7aee201b0419b65c7ea8ddcb6f2d42ffaff4711e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101749986"
---
|  | **Magánhálózati társviszony-létesítés** | **Microsoft-társviszony-létesítés** |  **Nyilvános peering** (új áramköröknél elavult) |
| --- | --- | --- | --- |
| **Max. # a támogatott előtagok száma** |4000 alapértelmezés szerint 10 000 a ExpressRoute Premiummal |200 |200 |
| **Támogatott IP-címtartományok** |Bármely érvényes IP-cím a WAN-on belül. |Az Ön vagy a kapcsolat szolgáltatója által birtokolt nyilvános IP-címek. |Az Ön vagy a kapcsolat szolgáltatója által birtokolt nyilvános IP-címek. |
| **Számokra vonatkozó követelmények** |Magán-és nyilvános számként. Ha az egyiket választja, a nyilvános értéket kell használnia. |Magán-és nyilvános számként. A nyilvános IP-címek tulajdonjogát azonban bizonyítania kell. |Magán-és nyilvános számként. A nyilvános IP-címek tulajdonjogát azonban bizonyítania kell. |
| **Támogatott IP-protokollok**| IPv4, IPv6 (előzetes verzió) |  IPv4, IPv6 | IPv4 |
| **Útválasztási csatoló IP-címei** |RFC1918 és nyilvános IP-címek |Az útválasztási nyilvántartásokban regisztrált nyilvános IP-címek. |Az útválasztási nyilvántartásokban regisztrált nyilvános IP-címek. |
| **MD5-kivonatok támogatása** |Igen |Igen |Yes |