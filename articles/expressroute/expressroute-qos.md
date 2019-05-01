---
title: 'A QoS - követelmények: az ExpressRoute: Azure | Microsoft Docs'
description: Ezen a lapon konfigurálásához és kezeléséhez a QoS részletes követelményeit ismerteti. Skype vállalati verzió üzleti és hangvétel szolgáltatások ismertetése.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9bdeb91b145f8c7f31be8c1dcd5c5158d50ff2f6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64712310"
---
# <a name="expressroute-qos-requirements"></a>Az ExpressRoute QoS-követelményei
A Skype Vállalati verzió különböző számítási feladatokat tartalmaz, amelyek különböző QoS-kezelést igényelnek. Ha hangszolgáltatásokat tervez használni az ExpressRoute-on keresztül, teljesítenie kell az alábbiakban ismertetett követelményeket.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> A QoS-követelmények csak a Microsoft társviszony-létesítésre vonatkoznak. Az Azure nyilvános vagy Azure privát társviszony-létesítésen keresztül kapott hálózati forgalom DSCP-értékei 0-ra állnak. 
> 
> 

A következő táblázat felsorolja a vállalati Microsoft Teams és a Skype által használt DSCP-jelöléseket. További információkért lásd: [A QoS kezelése a Skype Vállalati verzióhoz](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS).

| **Forgalomosztály** | **Kezelés (DSCP-jelölés)** | **Microsoft Teams és a Skype vállalati verzió számítási feladata** |
| --- | --- | --- |
| **Hang** |EF (46) |Skype- / Lync-hang |
| **Interaktív** |AF41 (34) |Videó, VBSS |
| |AF21 (18) |Alkalmazásmegosztás | 
| **Alapértelmezett** |AF11 (10) |Fájlátvitel |
| |CS0 (0) |Bármi más |

* Osztályoznia kell a munkaterheléseket, és meg kell jelölnie a megfelelő DSCP-értékeket. A DSCP-jelöléseket az [itt](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) leírt útmutatások alapján állíthatja be a hálózatban.
* Több QoS várakozási sort kell konfigurálnia és támogatnia a hálózaton belül. Beszédfelismerési egy különálló osztálynak kell és meghatározott EF-kezelést [RFC 3246](https://www.ietf.org/rfc/rfc3246.txt). 
* Forgalomosztályonként meghatározhatja a várakozásisor-kezelési mechanizmust, a torlódásészlelési házirendet és a sávszélesség-kiosztást. Skype Vállalati verzió számítási feladatainak DSCP-jelölését azonban meg kell tartania. Ha a fenti listában nem szereplő DSCP-jelöléseket használ, például az AF31 (26) jelölést, ezt a DSCP-értéket 0 értékre kell felülírnia, mielőtt elküldi a csomagot a Microsoftnak. A Microsoft csak a fenti táblázatban szereplő DSCP-értékkel jelölt csomagokat küld. 

## <a name="next-steps"></a>További lépések
* Tekintse meg az [Útválasztás](expressroute-routing.md) és a [NAT](expressroute-nat.md) követelményeit.
* Tekintse meg a következő hivatkozásokat az ExpressRoute-kapcsolat konfigurálásához:
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-classic.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-classic.md)

