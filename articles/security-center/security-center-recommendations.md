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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 0efb2c26a63418835dd2b7561a1fb9d3a9d5fee8
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945557"
---
# <a name="security-recommendations-in-azure-security-center"></a>Biztonsági javaslatok az Azure Security Centerben 
Ez a témakör azt ismerteti, hogyan lehet megtekinteni és értelmezni a Azure Security Centerban található javaslatokat az Azure-erőforrások védelmének elősegítése érdekében.


## <a name="what-are-security-recommendations"></a>Mik azok a biztonsági javaslatok?

A javaslatok olyan műveletek, amelyekkel biztonságossá teheti az erőforrásokat.

Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a biztonsági rések megoldására.

Az egyes javaslatok a következőket biztosítják:

- A probléma rövid leírása
- A javaslat megvalósításának végrehajtásához szükséges szervizelési lépések
- Az érintett erőforrások

## <a name="monitor-recommendations"></a>Javaslatok figyelése <a name="monitor-recommendations"></a>

Security Center elemzi az erőforrások biztonsági állapotát, hogy azonosítsa a lehetséges biztonsági réseket. 

1. A Security Center menüjében nyissa meg a **javaslatok** lapot, ahol megtekintheti a környezetére vonatkozó ajánlásokat. A javaslatok biztonsági vezérlőkbe vannak csoportosítva.

      ![Biztonsági ellenőrzés szerint csoportosított javaslatok](./media/security-center-recommendations/view-recommendations.png)

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

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Javaslat részletei lap":::
    1. Az ajánlás szervizeléséhez vagy logikai alkalmazás aktiválásához szükséges műveletek gombjai.

## <a name="preview-recommendations"></a>Előzetes javaslatok

Az **előzetesként** megjelölt javaslatok nem szerepelnek a biztonságos pontszám számításában.

Ha lehetséges, még mindig szervizelni kell őket, hogy ha az előzetes verzió időtartama lejár, a pontszáma is hozzájárul.

Példa az előzetes verziójú javaslatra:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Javaslat részletei lap":::
 
## <a name="next-steps"></a>További lépések

Ebben a dokumentumban a Security Center biztonsági javaslataira került sor. További információ a javaslatok megoldásáról:

- [Javaslatok szervizelése](security-center-remediate-recommendations.md) – Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és-erőforráscsoportok biztonsági szabályzatait.
- [A helytelen konfiguráció tiltása a kényszerítő/megtagadási javaslatokkal](prevent-misconfigurations.md).
