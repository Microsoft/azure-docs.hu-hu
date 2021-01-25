---
title: Biztonsági javaslatok az Azure Security Centerben
description: Ez a dokumentum részletesen ismerteti, hogyan segíti az Azure Security Center az Azure-erőforrások védelmében és a biztonsági szabályzatoknak való megfelelésben.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: dbda0aecdadd81da0f7681a5fc9b140157d5e8f3
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756808"
---
# <a name="security-recommendations-in-azure-security-center"></a>Biztonsági javaslatok az Azure Security Centerben 

Ez a témakör azt ismerteti, hogyan lehet megtekinteni és értelmezni a Azure Security Centerban található javaslatokat az Azure-erőforrások védelmének elősegítése érdekében.


## <a name="what-are-security-recommendations"></a>Mik azok a biztonsági javaslatok?

Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a biztonsági rések megoldására.

A javaslatok olyan műveletek, amelyekkel biztonságossá teheti és megerősítheti az erőforrásait. 

Az egyes javaslatok a következőket biztosítják:

- A probléma rövid leírása
- A javaslat megvalósításának végrehajtásához szükséges szervizelési lépések
- Az érintett erőforrások

## <a name="how-does-microsoft-decide-what-needs-securing-and-hardening"></a>Hogyan dönti el a Microsoft a biztonság és a megerősítés szükségletét?

Security Center javaslatai az Azure biztonsági Teljesítményteszten alapulnak. 

Az Azure biztonsági teljesítményteszt a Microsoft által létrehozott, Azure-specifikus irányelvek a biztonsági és megfelelőségi szabályzatok közös megfelelőségi keretrendszereken alapuló bevált eljárásaihoz. Ez a széles körben tiszteletben lévő teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) által vezérelt vezérlőkre épül, és a felhő-központú biztonságra összpontosít.

További tudnivalók az [Azure-biztonsági teljesítménytesztről](../security/benchmarks/introduction.md).

## <a name="monitor-recommendations"></a>Javaslatok figyelése <a name="monitor-recommendations"></a>

Security Center elemzi az erőforrások biztonsági állapotát, hogy azonosítsa a lehetséges biztonsági réseket. 

1. A Security Center menüjében nyissa meg a **javaslatok** lapot, ahol megtekintheti a környezetére vonatkozó ajánlásokat. A javaslatok biztonsági vezérlőkbe vannak csoportosítva.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Biztonsági ellenőrzés szerint csoportosított javaslatok" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Ha az erőforrás típusára, súlyosságára, környezetre vagy egyéb feltételére vonatkozó ajánlásokat szeretne megkeresni, használja a választható szűrőket a javaslatok listáján.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Azure Security Center javaslatok listájának finomítására szolgáló szűrők":::

1. Bontson ki egy vezérlőelemet, és válasszon egy konkrét javaslatot a javaslat részletei lap megtekintéséhez.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Javaslat részletei lap" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Az oldal tartalma:

    1. A támogatott javaslatok **érvényesítése** és **megtagadása** (lásd: [a helytelen konfiguráció tiltása a kényszerítő/megtagadási javaslatokkal](prevent-misconfigurations.md))
    1. **Súlyossági mutató**
    1. **Frissességi intervallum**  (ahol szükséges) 
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
 
## <a name="next-steps"></a>További lépések

Ebben a dokumentumban a Security Center biztonsági javaslataira került sor. Kapcsolódó információk:

- [Szervizelési javaslatok](security-center-remediate-recommendations.md)– megtudhatja, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és-erőforráscsoportok számára.
- [A helytelen konfiguráció tiltása a kényszerítő/megtagadási javaslatokkal](prevent-misconfigurations.md).
- [Security Center triggerekre adott válaszok automatizálása](workflow-automation.md)– a javaslatokra adott válaszok automatizálása
- [Erőforrás kivonása egy javaslatból](exempt-resource.md)
- [Biztonsági javaslatok – gyorsútmutató](recommendations-reference.md)