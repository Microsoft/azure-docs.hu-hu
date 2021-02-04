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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 20a464011e5a8d37a6215b222323ca989e02ac04
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550936"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Oktatóanyag: Jogszabályi megfelelés javítása

Azure Security Center megkönnyíti a szabályozási megfelelőségi követelmények teljesítésének folyamatát a **szabályozási megfelelőségi irányítópult** használatával. 

Security Center folyamatosan vizsgálja a hibrid felhőalapú környezetet, hogy elemezze a kockázati tényezőket az előfizetésekre vonatkozó szabványok és ajánlott eljárások alapján. Az irányítópult megfelel ezeknek a szabványoknak való megfelelőségi állapotának. 

Ha engedélyezi Security Center Azure-előfizetésben, a rendszer automatikusan hozzárendeli az [Azure biztonsági teljesítménytesztet](../security/benchmarks/introduction.md). Ez a széles körben tiszteletben lévő teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) által vezérelt vezérlőkre épül, és a felhő-központú biztonságra összpontosít.

A szabályzatok megfelelőségi irányítópultja a választott szabványok és rendeletek alapján mutatja be a környezetében lévő összes értékelés állapotát. A javaslatok és a környezet kockázati tényezőinek csökkentése érdekében a megfelelőségi testhelyzet javul.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A szabályozás megfelelőségének kiértékelése a szabályzatoknak való megfelelőségi irányítópult használatával
> * A megfelelőségi helyzet javítása a javaslatok alapján
> * Riasztások beállítása a megfelelőségi testtartás változásaira
> * Megfelelőségi adatai folyamatos adatfolyamként és heti pillanatképként exportálhatók

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban tárgyalt funkciók átlépéséhez:

- Az [Azure Defender](azure-defender.md) szolgáltatást engedélyezni kell. 30 napig ingyenesen kipróbálhatja az Azure Defendert.
- Be kell jelentkeznie egy olyan fiókkal, amely rendelkezik olvasói hozzáféréssel a szabályzat megfelelőségi információhoz (a **biztonsági olvasó** nem elegendő). Az előfizetés **globális olvasójának** szerepe fog működni. Legalább az **erőforrás-házirend közreműködői** és a **biztonsági rendszergazdai** szerepkörök hozzárendelésére van szükség.

##  <a name="assess-your-regulatory-compliance"></a>A szabályozás megfelelőségének felmérése

A szabályozási megfelelőségi irányítópult a kiválasztott megfelelőségi szabványokat az összes követelménynek megfelelően mutatja, ahol a támogatott követelmények a megfelelő biztonsági értékelésekre vannak leképezve. Ezen értékelések állapota tükrözi a szabványnak való megfelelést.

A szabályzatok megfelelőségi irányítópultjának használatával a kiválasztott szabványoknak és előírásoknak való megfeleléssel kapcsolatos figyelmet kaphat. Ez a célzott nézet azt is lehetővé teszi, hogy folyamatosan figyelje a megfelelőséget a dinamikus Felhőbeli és a hibrid környezeteken belül.

1. A Security Center menüjében válassza a **jogszabályi megfelelőség** lehetőséget.

    A képernyő tetején található egy irányítópult, amely áttekintést nyújt a megfelelőségi állapotáról a támogatott megfelelőségi szabályozások készletével együtt. Ekkor megjelenik a teljes megfelelőségi pontszám, valamint az egyes standardokhoz tartozó elküldés és a sikertelen értékelések száma.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Szabályozási megfelelőségi irányítópult" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Válassza ki az Önnek megfelelő megfelelőségi szabványhoz tartozó fület (1). Láthatja, hogy mely előfizetések lesznek alkalmazva a standard alkalmazásra (2), valamint az adott standardhoz tartozó összes vezérlő (3) listáját. A megfelelő vezérlők esetében megtekintheti a vezérlőhöz társított átadási és sikertelen értékelések részleteit (4), valamint az érintett erőforrások számát (5). Egyes vezérlők szürkén jelennek meg. Ezekhez a vezérlőkhöz nem tartozik Security Center Értékelés. Vizsgálja meg ezeket a követelményeket, és mérje fel azokat a saját környezetében. Ezek némelyike feldolgozható és nem technikai jellegű lehet.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Egy adott szabványnak való megfelelés részleteinek feltárása":::

1. Ha egy adott szabványhoz tartozó aktuális megfelelőségi állapot összegzésével szeretne PDF-jelentést készíteni, válassza a **jelentés letöltése** lehetőséget.

    A jelentés magas szintű összefoglalást biztosít a kiválasztott standard megfelelőségi állapotáról Security Center felmérési adatok alapján, és az adott szabvány vezérlőelemeinek megfelelően van rendszerezve. A jelentés megosztható az érintett felekkel, és a belső és külső könyvvizsgálók számára is igazolhatja.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Megfelelőségi jelentés letöltése":::

## <a name="improve-your-compliance-posture"></a>A megfelelőségi testhelyzet javítása

A szabályozás megfelelőségi irányítópultján található információk használatával a javaslatok közvetlenül az irányítópulton való feloldásával javíthatja a megfelelőségi testtartást.

1.  Az adott javaslat részleteinek megtekintéséhez kattintson az irányítópulton megjelenő sikertelen értékelések bármelyikére. Mindegyik javaslat olyan szervizelési lépéseket tartalmaz, amelyeket követni kell a probléma megoldásához.

1.  Válasszon ki egy adott erőforrást a további részletek megtekintéséhez és az adott erőforráshoz tartozó javaslat megoldásához. <br>Az **Azure CIS 1.1.0** standard esetében például válassza a **lemezes titkosítást a virtuális gépeken**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="A standard érdeklődők javaslatának kiválasztása közvetlenül a javaslat részleteit tartalmazó oldalra":::

1. Ebben a példában, ha a javaslat részletei lapon a **művelet elvégzése** lehetőséget választja, akkor a Azure Portal Azure-beli virtuálisgép-oldalain érkezik, ahol engedélyezheti a titkosítást a **Biztonság** lapon:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="A javaslat részletei lapon található művelet gomb a Szervizelési beállításokhoz vezet":::

    A javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).

1.  Miután lépéseket tett a javaslatok megoldására, látni fogja a megfelelőségi irányítópult jelentésének hatását, mivel a megfelelőségi pontszám javul.

    > [!NOTE]
    > Az értékelések körülbelül 12 óránként futnak, így a megfelelőségi adatokat csak a megfelelő értékelés következő futtatása után fogja látni.


## <a name="export-your-compliance-status-data"></a>A megfelelőségi állapot adatainak exportálása

Ha nyomon szeretné követni a megfelelőségi állapotát a környezetében lévő többi figyelési eszközzel, Security Center tartalmaz egy exportálási mechanizmust, amely megkönnyíti ezt a megoldást. A **folyamatos exportálás** konfigurálásával kiválaszthatja az Azure Event hub vagy egy log Analytics munkaterület kijelölését.

Folyamatos exportálással az Azure Event hub vagy egy Log Analytics munkaterület használatával:

- Az összes szabályozási megfelelőségi érték exportálása **folyamatos adatfolyamban**:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="A szabályozási megfelelőségi adatstreamek folyamatos exportálása" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- A szabályozási megfelelőségi adatairól szóló **heti Pillanatképek** exportálása:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="A szabályozási megfelelőségi adatok heti pillanatképének folyamatos exportálása" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

A megfelelőségi adatok **PDF/CSV-jelentését** közvetlenül a szabályozási megfelelőségi irányítópultról is exportálhatja:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="A szabályozási megfelelőségi adatai PDF-vagy CSV-jelentésként exportálhatók" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

További információ a [Security Center-adatfeldolgozás folyamatos exportálásáról](continuous-export.md).


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Munkafolyamat-automatizálás futtatása a megfelelőség módosításakor

A Security Center munkafolyamat-automatizálási funkciója kiválthatja Logic Apps, ha az egyik szabályozási megfelelőségi felmérés állapota megváltozik.

Előfordulhat például, hogy egy adott felhasználót szeretne e-mailben elSecurity Center, ha a megfelelőségi értékelés sikertelen. Először létre kell hoznia a logikai alkalmazást (a [Azure Logic apps](../logic-apps/logic-apps-overview.md)használatával), majd az eseményindítót egy új munkafolyamat-automatizálásban kell beállítania, ahogyan az a [Security Center triggerekre adott válaszok automatizálása](workflow-automation.md)című részben is szerepel.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="A szabályozások megfelelőségi vizsgálatának módosításai a Munkafolyamat-automatizálás elindításához" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Security Center szabályozási megfelelőségi irányítópultját a következőre:

- Megtekintheti és figyelheti a megfelelőségi testtartást az Ön számára fontos szabványokkal és előírásokkal kapcsolatban.
- Javítsa a megfelelőségi állapotot a kapcsolódó javaslatok feloldásával és a megfelelőségi pontszám javításával.

A szabályozási megfelelőségi irányítópult nagy mértékben leegyszerűsítheti a megfelelőségi folyamatot, és jelentős mértékben csökkentheti az Azure-, hibrid és többfelhős környezet megfelelőségi igazolásának begyűjtéséhez szükséges időt.

További információkért tekintse meg a következő kapcsolódó lapokat:

- [Szabja testre a szabványok készletét a szabályozási megfelelőségi irányítópulton](update-regulatory-compliance-packages.md) – Ismerje meg, hogy milyen szabványok jelenjenek meg a szabályozási megfelelőségi irányítópulton. 
- A [Azure Security Center biztonsági állapotának monitorozása](security-center-monitoring.md) – megismerheti az Azure-erőforrások állapotának figyelését.
- [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan használhatók a javaslatok a Azure Security Centerban az Azure-erőforrások védelme érdekében.