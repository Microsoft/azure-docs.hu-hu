---
title: 'Oktatóanyag: jogszabályi megfelelőségi ellenőrzések – Azure Security Center'
description: 'Oktatóanyag: Ismerje meg, hogyan javíthatja a szabályozás megfelelőségét Azure Security Center használatával.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2021
ms.author: memildin
ms.openlocfilehash: 523b081b59bd2f4c45c1ceeb9f39c58f4e3b02b1
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986904"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Oktatóanyag: Jogszabályi megfelelés javítása

Azure Security Center megkönnyíti a szabályozási megfelelőségi követelmények teljesítésének folyamatát a **szabályozási megfelelőségi irányítópult** használatával. 

Security Center a hibrid felhőalapú környezet folyamatos értékelését hajtja végre, hogy elemezze a kockázati tényezőket az előfizetésekre vonatkozó szabványok és ajánlott eljárások alapján. Az irányítópult megfelel ezeknek a szabványoknak való megfelelőségi állapotának. 

Ha engedélyezi Security Center Azure-előfizetéshez, a rendszer automatikusan hozzárendeli az [Azure biztonsági teljesítménytesztet](../security/benchmarks/introduction.md). Ez a széles körben tiszteletben lévő teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) által vezérelt vezérlőkre épül, és a felhő-központú biztonságra összpontosít.

A szabályozási megfelelőségi irányítópulton egy adott szabvány vagy szabályozás kontextusában tekintheti meg az összes értékelés állapotát a környezetében. A javaslatok és a környezet kockázati tényezőinek csökkentése érdekében a megfelelőségi testhelyzet javul.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A szabályozás megfelelőségének kiértékelése a szabályzatoknak való megfelelőségi irányítópult használatával
> * A megfelelőségi helyzet javítása a javaslatok alapján

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban tárgyalt funkciók átlépéséhez:

- Az [Azure Defender](azure-defender.md) szolgáltatást engedélyezni kell. 30 napig ingyenesen kipróbálhatja az Azure Defendert.
- Be kell jelentkeznie egy olyan fiókkal, amely rendelkezik olvasói hozzáféréssel a szabályzat megfelelőségi információhoz (a **biztonsági olvasó** nem elegendő). Az előfizetés **globális olvasójának** szerepe fog működni. Legalább az **erőforrás-házirend közreműködői** és a **biztonsági rendszergazdai** szerepkörök hozzárendelésére van szükség.

##  <a name="assess-your-regulatory-compliance"></a>A szabályozás megfelelőségének felmérése

A szabályozási megfelelőségi irányítópult a kiválasztott megfelelőségi szabványokat az összes követelménynek megfelelően mutatja, ahol a támogatott követelmények a megfelelő biztonsági értékelésekre vannak leképezve. Ezen értékelések állapota tükrözi a szabványnak való megfelelést.

A jogszabályi megfelelőségi irányítópult segítségével a követelményekkel és az Önt megillető előírásokkal összhangba helyezheti a hiányosságokat. Ez a célzott nézet azt is lehetővé teszi, hogy folyamatosan figyelje a megfelelőséget a dinamikus Felhőbeli és a hibrid környezeteken belül.

1. A Security Center menüjében válassza a **jogszabályi megfelelőség** lehetőséget.

    A képernyő tetején található egy irányítópult, amely áttekintést nyújt a megfelelőségi állapotáról a támogatott megfelelőségi szabályozások készletével együtt. Megtekintheti a teljes megfelelőségi pontszámot, valamint az egyes standardokhoz kapcsolódó elvégzési és sikertelen értékelések számát.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Szabályozási megfelelőségi irányítópult" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Válassza ki az Önnek megfelelő megfelelőségi szabványhoz tartozó fület (1). Láthatja, hogy mely előfizetések lesznek alkalmazva a standard alkalmazásra (2), valamint az adott standardhoz tartozó összes vezérlő (3) listáját. A megfelelő vezérlők esetében megtekintheti a vezérlőhöz társított átadási és sikertelen értékelések részleteit (4), valamint az érintett erőforrások számát (5). Egyes vezérlők szürkén jelennek meg. Ezekhez a vezérlőkhöz nem tartozik Security Center Értékelés. Vizsgálja meg ezeket a követelményeket, és mérje fel azokat a saját környezetében. Ezek némelyike feldolgozható és nem technikai jellegű lehet.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Egy adott szabványnak való megfelelés részleteinek feltárása":::

1. Ha egy adott szabványhoz tartozó aktuális megfelelőségi állapot összegzésével szeretne PDF-jelentést készíteni, válassza a **jelentés letöltése** lehetőséget.

    A jelentés magas szintű összefoglalást biztosít a kiválasztott standard megfelelőségi állapotáról Security Center felmérési adatok alapján, és az adott szabvány vezérlőelemeinek megfelelően van rendszerezve. A jelentés megosztható az érintett felekkel, és a belső és külső könyvvizsgálók számára is igazolhatja.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Megfelelőségi jelentés letöltése":::

## <a name="improve-your-compliance-posture"></a>A megfelelőségi testhelyzet javítása

A szabályozás megfelelőségi irányítópultján található információk alapján a megfelelőségi állapotot a közvetlenül az irányítópulton belüli javaslatok megoldásával javíthatja.

1.  Az adott javaslat részleteinek megtekintéséhez kattintson az irányítópulton megjelenő sikertelen értékelések bármelyikére. Mindegyik javaslat olyan szervizelési lépéseket tartalmaz, amelyeket követni kell a probléma megoldásához.

1.  Kiválaszthat egy adott erőforrást a további részletek megtekintéséhez és az adott erőforráshoz tartozó javaslat megoldásához. <br>Az **Azure CIS 1.1.0** standard verziójában például kiválaszthatja, hogy a rendszer hogyan **alkalmazza a lemez titkosítását a virtuális gépekre**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="A standard érdeklődők javaslatának kiválasztása közvetlenül a javaslat részleteit tartalmazó oldalra":::

1. Ebben a példában, ha a javaslat részletei lapon a **művelet elvégzése** lehetőséget választja, akkor a Azure Portal Azure-beli virtuális gépek oldalain érkeznek, ahol megnyithatja a **Biztonság** lapot, és engedélyezheti a titkosítást:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="A javaslat részletei lapon található művelet gomb a Szervizelési beállításokhoz vezet":::

    A javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).

1.  Miután lépéseket tett a javaslatok megoldására, látni fogja a megfelelőségi irányítópult jelentésének hatását, mivel a megfelelőségi pontszám javul.

    > [!NOTE]
    > Az értékelések körülbelül 12 óránként futnak, így a megfelelőségi adatokat csak a megfelelő értékelés következő futtatása után fogja látni.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Security Center szabályozási megfelelőségi irányítópultját a következőre:

-   Megtekintheti és figyelheti a megfelelőségi testtartást az Ön számára fontos szabványokhoz és előírásokhoz képest.
-   Javítsa a megfelelőségi állapotot a kapcsolódó javaslatok feloldásával és a megfelelőségi pontszám javításával.

A szabályozási megfelelőségi irányítópult nagy mértékben leegyszerűsítheti a megfelelőségi folyamatot, és jelentős mértékben csökkentheti az Azure-és hibrid környezetek megfelelőségi igazolásának begyűjtéséhez szükséges időt.

További információt a következő kapcsolódó cikkekben talál:

-   [Frissítés dinamikus megfelelőségi csomagokra a szabályozási megfelelőségi irányítópulton (előzetes verzió)](update-regulatory-compliance-packages.md) – Ismerje meg ezt az előzetes funkciót, amely lehetővé teszi, hogy frissítse a szabályozási megfelelőségi irányítópulton látható szabványokat az új *dinamikus* csomagokra. Ugyanezt az előzetes verziójú szolgáltatást is használhatja új megfelelőségi csomagok hozzáadásához és a megfelelőség további szabványokkal való monitorozásához. 
-   A [Azure Security Center biztonsági állapotának monitorozása](security-center-monitoring.md) – megismerheti az Azure-erőforrások állapotának figyelését.
-   [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan használhatók a javaslatok a Azure Security Centerban az Azure-erőforrások védelme érdekében.
-   [Javítsa a Azure Security Center biztonságos pontszámát](secure-score-security-controls.md) – Ismerje meg, hogyan rangsorolhatja a biztonsági réseket és a biztonsági javaslatokat, hogy a leghatékonyabban javítsa a biztonságot.
