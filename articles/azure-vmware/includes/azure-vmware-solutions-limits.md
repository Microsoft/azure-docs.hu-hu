---
title: Azure VMware-megoldás korlátai
description: Azure VMware-megoldásokra vonatkozó korlátozások.
ms.topic: include
ms.date: 03/24/2021
ms.openlocfilehash: 997a5ae96ff30226d055b7b966b128d7ec0ae5bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582597"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Az alábbi táblázat az Azure VMware-megoldás maximális korlátait ismerteti.

| **Erőforrás** | **Korlátot** |
| :-- | :-- |
| Fürtök/saját felhő | 12 |
| Csomópontok minimális száma egy fürtön | 3 |
| Csomópontok maximális száma egy fürtön | 16 |
| Csomópontok/privát felhő | 96 |
| vCenter/privát felhő | 1  |
| HCX-hely párosítása | 3 a Advanced Edition, 10 Enterprise Edition kiadással |
| AVS ExpressRoute Max csatolt SDDCs | 4 |
| AVS ExpressRoute portspeed | 10 Gbps<br />A használt virtuális hálózati átjáró meghatározza a tényleges sávszélességet. További részletekért lásd: [a ExpressRoute virtuális hálózati átjáróinak](../../expressroute/expressroute-about-virtual-network-gateways.md) ismertetése | 
| A vWAN-on keresztül elérhető nyilvános IP-címek | 100 |
| vSAN kapacitásának korlátai | a teljes felhasználható mennyiség 75%-a (az SLA-hoz 25%-os marad)  |

Más VMware-specifikus korlátok esetén használja a [VMware-konfiguráció maximális eszközét!](https://configmax.vmware.com/).
