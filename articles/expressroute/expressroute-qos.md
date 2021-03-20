---
title: 'Azure ExpressRoute: QoS-követelmények'
description: Ez az oldal részletes követelményeket biztosít a QoS konfigurálásához és kezeléséhez. A Skype for Business/Voice Services ismertetése.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: duau
ms.openlocfilehash: 7f14f43fa341df40ecd35340b7311e1acd18004c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204707"
---
# <a name="expressroute-qos-requirements"></a>Az ExpressRoute QoS-követelményei
A Skype Vállalati verzió különböző számítási feladatokat tartalmaz, amelyek különböző QoS-kezelést igényelnek. Ha hangszolgáltatásokat tervez használni az ExpressRoute-on keresztül, teljesítenie kell az alábbiakban ismertetett követelményeket.

![Az ExpressRoute-on keresztül haladó hangszolgáltatásokat bemutató diagram.](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> A QoS-követelmények csak a Microsoft társviszony-létesítésre vonatkoznak. Az Azure nyilvános vagy Azure privát társviszony-létesítésen keresztül kapott hálózati forgalom DSCP-értékei 0-ra állnak. 
> 
> 

Az alábbi táblázat a Microsoft Teams és a Skype vállalati verzió által használt DSCP-megjelöléseket tartalmazza. További információkért lásd: [A QoS kezelése a Skype Vállalati verzióhoz](/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS).

| **Forgalomosztály** | **Kezelés (DSCP-jelölés)** | **Microsoft-csapatok és Skype vállalati munkaterhelések** |
| --- | --- | --- |
| **Hang** |EF (46) |Skype/Microsoft Teams/Lync Voice |
| **Interaktív** |AF41 (34) |Videó, VBSS |
| |AF21 (18) |Alkalmazásmegosztás | 
| **Alapértelmezett** |AF11 (10) |Fájlátvitel |
| |CS0 (0) |Bármi más |

* Osztályoznia kell a munkaterheléseket, és meg kell jelölnie a megfelelő DSCP-értékeket. A DSCP-jelöléseket az [itt](/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) leírt útmutatások alapján állíthatja be a hálózatban.
* Több QoS várakozási sort kell konfigurálnia és támogatnia a hálózaton belül. A hangnak önálló osztálynak kell lennie, és meg kell kapnia az [RFC 3246](https://www.ietf.org/rfc/rfc3246.txt)-ben megadott EF-kezelést. 
* Forgalomosztályonként meghatározhatja a várakozásisor-kezelési mechanizmust, a torlódásészlelési házirendet és a sávszélesség-kiosztást. Skype Vállalati verzió számítási feladatainak DSCP-jelölését azonban meg kell tartania. Ha a fenti listában nem szereplő DSCP-jelöléseket használ, például az AF31 (26) jelölést, ezt a DSCP-értéket 0 értékre kell felülírnia, mielőtt elküldi a csomagot a Microsoftnak. A Microsoft csak a fenti táblázatban szereplő DSCP-értékkel jelölt csomagokat küld. 

## <a name="next-steps"></a>Következő lépések
* Tekintse meg az [Útválasztás](expressroute-routing.md) és a [NAT](expressroute-nat.md) követelményeit.
* Tekintse meg a következő hivatkozásokat az ExpressRoute-kapcsolat konfigurálásához:
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-classic.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-classic.md)