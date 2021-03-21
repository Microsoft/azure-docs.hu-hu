---
title: Azure bejárati ajtó standard/prémium SKU összehasonlítása
description: Ez a cikk áttekintést nyújt az Azure bejárati ajtók standard és Premium SKU-ról, valamint a szolgáltatások közötti különbségekről.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 1753f2bb649e73d7a5fe6c1cc32361a418ea7f63
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181805"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Az Azure Door standard/Premium SKU áttekintése (előzetes verzió)

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az Azure bejárati ajtót 3 különböző SKU-ra, az [Azure bejárati ajtóra](../front-door-overview.md), az Azure bejárati ajtó standard (előzetes verzió) és az Azure bejárati Premium (előzetes verzió) csomagra Az Azure bejárati standard/Premium SKU ötvözi az Azure bejárati képességeit, Azure CDN a Microsofttól származó standard szintű Azure WAF egyetlen biztonságos felhőalapú CDN-platformba, intelligens veszélyforrások elleni védelemmel.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [**Microsoft Azure előzetes verziójának kiegészítő használati feltételei**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Az **Azure bejárati ajtó standard SKU** a következőket használja:

    * Tartalom továbbítása optimalizálva
    * Statikus és dinamikus tartalom-gyorsítást is kínál
    * Globális terheléselosztás
    * SSL-kiszervezés
    * Tartományok és tanúsítványok kezelése
    * Továbbfejlesztett Traffic Analytics 
    * Alapszintű biztonsági képességek

* Az **Azure bejárati prémium SKU** a standard SKU képességeire épül, és hozzáadja a következőket:

    * Széles körű biztonsági képességek a WAF-ben
    * BOT-védelem
    * Privát hivatkozás támogatása
    * Integráció a Microsoft Threat Intelligence és a Security Analytics szolgáltatással. 

![Az előtérben lévő SKU-ket összehasonlító diagramot ábrázoló ábra.](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>Szolgáltatások összehasonlítása

| Szolgáltatás |      Standard      |  Prémium |
|----------|:-------------:|------:|
| Egyéni tartományok | Igen | Yes |
| SSL-kiszervezés | Igen | Yes |
| Gyorsítótárazás |  Igen  | Yes |
| Tömörítés | Igen | Yes   |
| Globális terheléselosztás | Igen  | Yes |
| 7. rétegbeli Útválasztás | Igen | Yes |
| URL-átírás | Igen | Yes |
| Szabálymotor | Igen | Yes |
| Privát forrás (privát hivatkozás) | Nem | Igen |
| WAF | Csak egyéni szabályok | Yes |
| Bot-védelem | Nem | Igen |
| Továbbfejlesztett mérőszámok és diagnosztika | Igen | Yes |
| Forgalmi jelentés | Igen | Yes |
| Biztonsági jelentés | Nem | Igen | 

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre bejárati ajtót](create-front-door-portal.md)
