---
title: Biztonsági riasztások kezelése az Azure Security Centerben | Microsoft Docs
description: Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 490f94c71611e07e765f5882cb4e3eec13033a6a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099335"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Biztonsági riasztások kezelése és reagálás Azure Security Center

Ebből a témakörből megtudhatja, hogyan tekintheti meg és dolgozhatja fel Security Center riasztásait, és hogyan védhető az erőforrásai.

A biztonsági riasztásokat kiváltó speciális észlelések csak az Azure Defenderben érhetők el. Ennek létezik egy ingyenesen elérhető próbaverziója. A frissítéshez tekintse meg az [Azure Defender engedélyezése](enable-azure-defender.md)című témakört.

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek.

A különböző típusú riasztásokkal kapcsolatos további tudnivalókért lásd: [biztonsági riasztások – útmutató](alerts-reference.md).

A Security Center riasztások létrehozási módjának áttekintését lásd: [how Azure Security Center észleli és reagál a fenyegetésekre](security-center-alerts-overview.md).


## <a name="manage-your-security-alerts"></a>Biztonsági riasztások kezelése

1. A Security Center áttekintése lapon válassza a **biztonsági riasztások** csempét az oldal tetején, vagy az oldalsávon található hivatkozást.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="A biztonsági riasztások oldalának beolvasása a Azure Security Center áttekintés lapjáról":::

    Megnyílik a biztonsági riasztások lap.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center biztonsági riasztások listája":::

1. A riasztások listájának szűréséhez válassza ki a megfelelő szűrők bármelyikét. Opcionálisan további szűrőket is hozzáadhat a **szűrő hozzáadása** lehetőséggel.

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Szűrők hozzáadása a riasztások nézethez" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    A lista a kiválasztott szűrési beállításoknak megfelelően frissül. A szűrés nagyon hasznos lehet. Például olyankor, ha az elmúlt 24 órában történt biztonsági riasztásokat szeretné kezelni, mert egy, a rendszerbe történő lehetséges behatolást vizsgál.


## <a name="respond-to-security-alerts"></a>Válaszadás a biztonsági riasztásokra

1. A **biztonsági riasztások** listából válassza ki a riasztást. Megnyílik egy oldalsó ablaktábla, amely a riasztás leírását és az összes érintett erőforrást megjeleníti. 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="Biztonsági riasztások mini részleteinek megtekintése":::

    > [!TIP]
    > Az oldalsó ablaktábla megnyitásával gyorsan áttekintheti a riasztások listáját a billentyűzet fel és le nyíllal.

1. További információért válassza a **teljes részletek megtekintése** lehetőséget.

    A biztonsági riasztás oldal bal oldali ablaktáblája a biztonsági riasztással kapcsolatos magas szintű információkat jeleníti meg: cím, súlyosság, állapot, tevékenység ideje, a gyanús tevékenység leírása és az érintett erőforrás. Az érintett erőforrás mellett az erőforráshoz tartozó Azure-címkék is szerepelnek. Ezek használatával következtetheti ki az erőforrás szervezeti környezetét a riasztás kivizsgálásakor.

    A jobb oldali ablaktábla tartalmazza a riasztás **részletei** lapot, amely a riasztás további részleteit tartalmazza a probléma kivizsgálásához: IP-címek, fájlok, folyamatok és sok más.
     
    ![Javaslatok a biztonsági riasztásokkal kapcsolatos teendők](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    A jobb oldali ablaktáblán a **Take Action (művelet** ) lap is látható. Ezen a lapon további műveleteket végezhet a biztonsági riasztással kapcsolatban. Műveletek, például:
    - *A fenyegetés enyhítése* – a biztonsági riasztás manuális szervizelési lépéseit nyújtja
    - A *jövőbeli támadások megelőzése* – biztonsági ajánlásokat biztosít a támadási felület csökkentéséhez, a biztonsági helyzet növeléséhez, és ezáltal a jövőbeli támadások megelőzése érdekében
    - *Automatizált válasz indítása* – lehetővé teszi, hogy a logikai alkalmazás a biztonsági riasztásra adott válaszként legyen aktiválva
    - *Hasonló riasztások letiltása* – lehetővé teszi a hasonló tulajdonságokkal rendelkező jövőbeli riasztások mellőzését, ha a riasztás nem releváns a szervezet számára

    ![Művelet elvégzése lap](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Lásd még

Ebből a dokumentumból megtudhatta, hogyan tekintheti meg a biztonsági riasztásokat. A kapcsolódó anyagokkal kapcsolatban lásd a következő lapokat:

- [Riasztás-elnyomási szabályok konfigurálása](alerts-suppression-rules.md)
- [Security Center eseményindítóra adott válaszok automatizálása](workflow-automation.md)
- [Biztonsági riasztások - – referencia-útmutató](alerts-reference.md)
