---
title: Biztonsági javaslatok az Azure Security Centerben
description: Ez a dokumentum részletesen ismerteti, hogyan segíti az Azure Security Center az Azure-erőforrások védelmében és a biztonsági szabályzatoknak való megfelelésben.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 57760443746e111750e74ef55fc18729f6ba32c4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100338"
---
# <a name="review-your-security-recommendations"></a>Biztonsági javaslatok áttekintése

Ez a témakör azt ismerteti, hogyan lehet megtekinteni és értelmezni a Azure Security Centerban található javaslatokat az Azure-erőforrások védelmének elősegítése érdekében.

## <a name="monitor-recommendations"></a>Javaslatok figyelése <a name="monitor-recommendations"></a>

Security Center elemzi az erőforrások biztonsági állapotát, hogy azonosítsa a lehetséges biztonsági réseket. 

1. A Security Center menüjében nyissa meg a **javaslatok** lapot, ahol megtekintheti a környezetére vonatkozó ajánlásokat. A javaslatok biztonsági vezérlőkbe vannak csoportosítva.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Biztonsági ellenőrzés szerint csoportosított javaslatok" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Ha az erőforrás típusára, súlyosságára, környezetre vagy egyéb feltételére vonatkozó ajánlásokat szeretne megkeresni, használja a választható szűrőket a javaslatok listáján.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Azure Security Center javaslatok listájának finomítására szolgáló szűrők":::

1. Bontson ki egy vezérlőelemet, és válasszon egy konkrét javaslatot a javaslat részletei lap megtekintéséhez.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Javaslat részletei lap" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Az oldal tartalma:

    1. A támogatott javaslatok esetében a felső eszköztár a következő gombok bármelyikét vagy mindegyikét megjeleníti:
        - **Kikényszerítés** és **Megtagadás** (lásd: [a hibás konfigurációk megelőzése a kényszerítő/megtagadási javaslatokkal](prevent-misconfigurations.md))
        - **Megtekintheti a házirend-definíciót** , hogy közvetlenül az alapul szolgáló szabályzat Azure Policy bejegyzéséhez lépjen
    1. **Súlyossági mutató**
    1. **Frissességi intervallum** (ahol szükséges)
    1. A **mentesített erőforrások** száma, ha kivételek vannak a javaslathoz, ez a kivételt képező erőforrások számát mutatja.
    1. **Leírás** – a probléma rövid leírása
    1. **Szervizelési lépések** – az érintett erőforrásokra vonatkozó biztonsági probléma megoldásához szükséges manuális lépések leírása. A "gyors javítással" kapcsolatos javaslatok esetében kiválaszthatja a **szervizelési logika megjelenítése** lehetőséget, mielőtt alkalmazza a javasolt javítást az erőforrásokra. 
    1. **Érintett erőforrások** – az erőforrások lapokra vannak csoportosítva:
        - **Kifogástalan** állapotú erőforrások – azok a kapcsolódó erőforrások, amelyeket nem érint, vagy amelyeken már szervizelte a problémát.
        - Nem megfelelő **állapotú erőforrások** – az azonosított probléma által továbbra is érintett erőforrások.
        - **Nem alkalmazható erőforrások** – olyan erőforrások, amelyekhez az ajánlás nem adhat végleges választ. A nem alkalmazható lap az egyes erőforrások okait is tartalmazza. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Nem alkalmazható erőforrások az okok miatt.":::
    1. Az ajánlás szervizeléséhez vagy logikai alkalmazás aktiválásához szükséges műveletek gombjai.

## <a name="preview-recommendations"></a>Előzetes javaslatok

Az **előzetesként** megjelölt javaslatok nem szerepelnek a biztonságos pontszám számításában.

Ha lehetséges, még mindig szervizelni kell őket, hogy ha az előzetes verzió időtartama lejár, a pontszáma is hozzájárul.

Példa az előzetes verziójú javaslatra:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Javaslat az előnézet jelölővel":::
 
## <a name="next-steps"></a>Következő lépések

Ebben a dokumentumban a Security Center biztonsági javaslataira került sor. Kapcsolódó információk:

- [Szervizelési javaslatok](security-center-remediate-recommendations.md)– megtudhatja, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és-erőforráscsoportok számára.
- [A helytelen konfiguráció tiltása a kényszerítő/megtagadási javaslatokkal](prevent-misconfigurations.md).
- [Security Center triggerekre adott válaszok automatizálása](workflow-automation.md)– a javaslatokra adott válaszok automatizálása
- [Erőforrás kivonása egy javaslatból](exempt-resource.md)
- [Biztonsági javaslatok – gyorsútmutató](recommendations-reference.md)