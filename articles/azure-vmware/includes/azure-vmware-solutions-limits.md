---
title: Azure VMware-megoldás korlátai
description: Azure VMware-megoldásokra vonatkozó korlátozások.
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622260"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Az alábbi táblázat az Azure VMware-megoldás maximális korlátait ismerteti.

| **Erőforrás** | **Korlátot** |
| :-- | :-- |
| Fürtök/saját felhő | 12 |
| Csomópontok minimális száma egy fürtön | 3 |
| Csomópontok maximális száma egy fürtön | 16 |
| Csomópontok/privát felhő | 64 |
| vCenter/privát felhő | 1  |
| HCX-hely párosítása | 3 a Advanced Edition, 10 Enterprise Edition kiadással |
| AVS ExpressRoute Max csatolt SDDCs | 4 |
| AVS ExpressRoute portspeed | 10 Gbps<br />A használt virtuális hálózati átjáró meghatározza a tényleges sávszélességet. További részletekért lásd: [a ExpressRoute virtuális hálózati átjáróinak](../../expressroute/expressroute-about-virtual-network-gateways.md) ismertetése | 
| A vWAN-on keresztül elérhető nyilvános IP-címek | 100 |
| vSAN kapacitásának korlátai | a teljes felhasználható mennyiség 75%-a (az SLA-hoz 25%-os marad)  |

Más VMware-specifikus korlátok esetén használja a [VMware-konfiguráció maximális eszközét!](https://configmax.vmware.com/).
