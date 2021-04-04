---
title: 'Azure ExpressRoute: Kapcsolódás a Microsoft Cloudhoz Global Reach használatával'
description: Ismerje meg, hogy az Azure ExpressRoute Global Reach hogyan kapcsolhat össze ExpressRoute-áramköröket a helyszíni hálózatok közötti magánhálózat létrehozása érdekében.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 50679e11697a4227af69b8568c5f3cd23fe26cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662797"
---
# <a name="expressroute-global-reach"></a>Az ExpressRoute Global Reach
A ExpressRoute egy privát és rugalmas módszer a helyi hálózatok Microsoft Cloudhoz való összekapcsolására. Számos Microsoft Cloud Services-szolgáltatást, például az Azure-t és a Microsoft 365t a privát adatközpontból vagy a vállalati hálózatból érheti el. Előfordulhat például, hogy egy San Francisco-beli fiókirodában egy ExpressRoute áramkör található a Szilícium-völgyben és egy másik fiókirodában Londonban, és egy ExpressRoute-áramkör található ugyanabban a városban. Mindkét fiókirodában nagy sebességű kapcsolat áll fenn az USA nyugati és Egyesült Királyság déli régiója Azure-erőforrásaival. Azonban a fiókirodák nem tudnak közvetlenül kapcsolatba lépni egymással, és nem küldhetnek adatküldést. Más szóval a 10.0.1.0/24 képes az 10.0.3.0/24 és a 10.0.4.0/24 hálózatra irányuló adatküldésre, de nem a 10.0.2.0/24 hálózatra.

![Az Express Route Global Reachsal együtt nem csatolt áramköröket ábrázoló diagram.][1]

A **ExpressRoute Global REACH** összekapcsolhatja a ExpressRoute-áramköröket, hogy a helyi hálózatok között privát hálózatot hozzon fel. A fenti példában a ExpressRoute-Global Reach hozzáadásával a San Francisco-iroda (10.0.1.0/24) közvetlenül az adatok cseréjét a londoni irodával (10.0.2.0/24) a meglévő ExpressRoute-áramkörökkel és a Microsoft globális hálózatán keresztül végezheti el. 

![Az Express Route Global Reachsal együtt összekapcsolt áramköröket ábrázoló diagram.][2]

## <a name="use-case"></a>Használati eset
A ExpressRoute Global Reach úgy lett kialakítva, hogy kiegészítse a szolgáltató WAN-implementációját, és összekösse a fiókirodákat a világ különböző pontjain. Ha például a szolgáltató elsődlegesen a Egyesült Államok működik, és az Egyesült Államokban az összes ágát összekapcsolta, de a szolgáltató nem működik Japánban és Hongkongban, a ExpressRoute Global Reach a helyi szolgáltatóval dolgozhat, és a Microsoft a ExpressRoute és a globális hálózattal is összeköti az ágakat az Egyesült Államokban.

![Az Express Route Global Reach használati esetét bemutató diagram.][3]

## <a name="availability"></a>Rendelkezésre állás 
A ExpressRoute Global Reach a következő helyeken támogatott. 

> [!NOTE] 
> Ahhoz, hogy a ExpressRoute Global Reach a [különböző geopolitikai régiók](expressroute-locations-providers.md#locations)között, az áramkörnek **prémium SKU**-nak kell lennie.

* Ausztrália
* Kanada
* Franciaország
* Németország
* Hongkong (KKT)
* Írország
* Japán
* Dél-Korea
* Hollandia
* Új-Zéland
* Norvégia
* Szingapúr
* Dél-Afrika (csak Johannesburg)
* Svédország
* Svájc
* Egyesült Királyság
* Egyesült Államok

## <a name="next-steps"></a>Következő lépések
- Tekintse meg a [Global REACH gyakori kérdések](expressroute-faqs.md#globalreach)című részt.
- További információ a [Global REACH engedélyezéséről](expressroute-howto-set-global-reach.md).
- Megtudhatja, hogyan [kapcsolhat ExpressRoute-áramkört a virtuális hálózathoz](expressroute-howto-linkvnet-arm.md).

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "globális elérhetőség nélküli diagram"
[2]: ./media/expressroute-global-reach/2.png "globálisan elérhető diagram"
[3]: ./media/expressroute-global-reach/3.png "globális elérési eset használata"
