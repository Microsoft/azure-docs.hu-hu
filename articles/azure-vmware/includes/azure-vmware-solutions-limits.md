---
title: Azure VMware-megoldás korlátai
description: Azure VMware-megoldásokra vonatkozó korlátozások.
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102194084"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

Az alábbi táblázat az Azure VMware-megoldás maximális korlátait ismerteti.

| **Erőforrás** | **Korlátot** |
| :-- | :-- |
| Fürtök/saját felhő | 4 |
| Csomópontok minimális száma egy fürtön | 3 |
| Csomópontok maximális száma egy fürtön | 16 |
| Csomópontok/privát felhő | 64 |
| vCenter/privát felhő | 1  |
| HCX-hely párosítása | 3 a Advanced Edition, 10 Enterprise Edition kiadással |
| AVS ExpressRoute Max csatolt SDDCs | 4 |
| AVS ExpressRoute portspeed | 10 Gbps | 
| A vWAN-on keresztül elérhető nyilvános IP-címek | 100 |
| vSAN kapacitásának korlátai | a teljes felhasználható mennyiség 75%-a (az SLA-hoz 25%-os marad)  |

Más VMware-specifikus korlátok esetén használja a [VMware-konfiguráció maximális eszközét!](https://configmax.vmware.com/).
