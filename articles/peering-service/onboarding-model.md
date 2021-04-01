---
title: Azure-partneri szolgáltatás bevezetési modellje
description: További tudnivalók az Azure-beli szolgáltatások előkészítéséről
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84872077"
---
# <a name="onboarding-peering-service-model"></a>Bevezetési szolgáltatási modell

A peering Service előkészítési folyamata az alábbiakban felsorolt két modellből áll:

 - A bevezetési szolgáltatással létesített kapcsolatok

 - A társ-szolgáltatási kapcsolatok telemetria

A fenti felsorolt modellekre vonatkozó művelettervek leírása alább látható:

| **Lépés** | **Művelet**| **Amihez jut**|
|-----------|---------|---------|---------|
|1|Az ügyfél a kapcsolat létesítését egy kapcsolati partnerrel (a Microsofttal való együttműködés nélkül). |Egy internetes szolgáltató, aki jól kapcsolódik a Microsofthoz, és megfelel a Microsoft számára a teljesítményre és a megbízható kapcsolatokra vonatkozó technikai követelményeknek.  |
|2 (nem kötelező)|Az ügyfél regisztrálja a telephelyeket a Azure Portal. A helyet a következő határozza meg: ISP/IXP neve, az ügyfél helyének fizikai helye (állami szint), a szolgáltató vagy a vállalat által a helyhez megadott IP-előtag.  |Telemetria: internetes útvonalak figyelése, a Microsoft és a felhasználó legközelebb lévő POP-helye közötti forgalom prioritása. |



## <a name="onboarding-peering-service-connection"></a>A bevezetési szolgáltatással létesített kapcsolatok

A társ-szolgáltatási kapcsolatok bevezetéséhez tegye a következőket:

Az internetszolgáltatóval (ISP) vagy internetes Exchange (IX) partnerrel szerezheti be a hálózat Microsoft-hálózattal való összekapcsolásához szükséges társ-szolgáltatást.

Győződjön meg arról, hogy a [kapcsolati szolgáltató](location-partners.md) partneri kapcsolatban áll a Microsofttal az egyenrangú szolgáltatáshoz. 

## <a name="onboarding-peering-service-connection-telemetry"></a>A társ-szolgáltatási kapcsolatok telemetria

Az ügyfelek választhatják a telemetria, például a BGP Route Analytics szolgáltatást a hálózat késésének és teljesítményének figyeléséhez a Microsoft hálózatának elérésekor. Ezt úgy érheti el, ha regisztrálja a-t a Azure Portal.

A társ-szolgáltatási kapcsolatok telemetria bevezetéséhez az ügyfélnek regisztrálnia kell a társ-szolgáltatási kapcsolatot a Azure Portalba. Az eljárás megismeréséhez tekintse meg a következőt: [Azure Portal regisztrálása](azure-portal.md) .

Ezt követően a telemetria mértékét a következő hivatkozással mérhetővé teheti [.](measure-connection-telemetry.md)

## <a name="next-steps"></a>Következő lépések

Ha szeretne többet megtudni a peering Service-kapcsolatok regisztrálásának lépéseiről, tekintse meg a következő témakört: [peering Service-Azure Portal regisztrálása](azure-portal.md).

A kapcsolatok telemetria mérésével kapcsolatos további információkért lásd: a [kapcsolatok telemetria mérése](measure-connection-telemetry.md).
