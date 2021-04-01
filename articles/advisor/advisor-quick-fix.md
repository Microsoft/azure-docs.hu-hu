---
title: Gyors javítási szervizelés az Advisor ajánlásaihoz
description: Csoportos szervizelés végrehajtása a gyors javítással az Advisorban
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: a9c86a7ae510d9657f64c71db2aa8c4e3e558f52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90968592"
---
# <a name="quick-fix-remediation-for-advisor"></a>Gyors javítási szervizelés az Advisorhoz
A **gyors javítás** a több erőforrásra vonatkozó javaslat gyorsabb és egyszerűbb megoldását teszi lehetővé. Lehetővé teszi az erőforrások tömeges szervizelését, és lehetővé teszi az előfizetések gyorsabb optimalizálását az erőforrások nagy mértékű szervizelésével.
A szolgáltatás csak bizonyos ajánlásokhoz érhető el, Azure Portalon keresztül.


## <a name="steps-to-use-quick-fix"></a>A gyors javítás használatának lépései

1. A **gyors javítás** címkével rendelkező javaslatok listájában kattintson a javaslatra.

   :::image type="content" source="./media/quick-fix-1.png" alt-text="{Képernyőfelvétel a Azure Advisorről, amely az ajánlásokban található gyors javítási címkéket mutatja.}":::
   
   *A rendszerkép árai csak példaként szolgálnak.*

2. A javaslat részletei lapon megtekintheti azokat az erőforrásokat, amelyekhez ezt a javaslatot elvégezte. Válassza ki az összes olyan erőforrást, amelyet a javaslathoz szeretne szervizelni.

   :::image type="content" source="./media/quick-fix-2.png" alt-text="Képernyőkép az érintett erőforrások ablakról listaelemek és a gyors javítás gomb kiemelésével.":::
   
   *A rendszerkép árai csak példaként szolgálnak.*

3. Miután kiválasztotta az erőforrásokat, kattintson a **gyors javítás** gombra a tömeges szervizeléshez.

   > [!NOTE]
   > Előfordulhat, hogy a felsorolt erőforrások némelyike le van tiltva, mert nem rendelkezik a megfelelő engedélyekkel a módosításhoz.
   
   > [!NOTE]
   > Ha más következményekkel is jár, az Advisorban említett előnyök mellett a tapasztalatok alapján tájékoztatni fogja Önt a hasznos szervizelési döntések meghozataláról.
   
4. Értesítést fog kapni a szervizelés befejezéséről. A rendszer hibaüzenetet jelenít meg, ha vannak olyan erőforrások, amelyek nem szervizeltek, és az erőforrások a kijelölt módban vannak az erőforrás-lista nézetben.  


## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további információkért lásd:
* [Az Azure Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Advisor – Cost-javaslatok](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
