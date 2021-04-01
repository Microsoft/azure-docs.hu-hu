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
ms.date: 02/10/2021
ms.author: memildin
ms.openlocfilehash: fb8dc22c923b7b53a6263baa43046862af4d2f04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370264"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Oktatóanyag: Jogszabályi megfelelés javítása

Azure Security Center megkönnyíti a szabályozási megfelelőségi követelmények teljesítésének folyamatát a **szabályozási megfelelőségi irányítópult** használatával. 

Security Center folyamatosan vizsgálja a hibrid felhőalapú környezetet, hogy elemezze a kockázati tényezőket az előfizetésekre vonatkozó szabványok és ajánlott eljárások alapján. Az irányítópult megfelel ezeknek a szabványoknak való megfelelőségi állapotának. 

Ha engedélyezi Security Center Azure-előfizetésben, az [Azure biztonsági teljesítményteszt](../security/benchmarks/introduction.md) automatikusan hozzá lesz rendelve az adott előfizetéshez. Ez a széles körben tiszteletben lévő teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) által vezérelt vezérlőkre épül, és a felhő-központú biztonságra összpontosít.

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

1. Válassza ki az Önnek megfelelő megfelelőségi szabványhoz tartozó fület (1). Láthatja, hogy mely előfizetések lesznek alkalmazva a standard alkalmazásra (2), valamint az adott standardhoz tartozó összes vezérlő (3) listáját. A megfelelő vezérlők esetében megtekintheti a vezérlőhöz társított átadási és sikertelen értékelések részleteit (4), valamint az érintett erőforrások számát (5). Egyes vezérlők szürkén jelennek meg. Ezekhez a vezérlőkhöz nem tartozik Security Center Értékelés. Győződjön meg a követelményekről, és mérje fel őket a környezetében. Ezek némelyike feldolgozható és nem technikai jellegű lehet.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Egy adott szabványnak való megfelelés részleteinek feltárása":::

1. Ha egy adott szabványhoz tartozó aktuális megfelelőségi állapot összegzésével szeretne PDF-jelentést készíteni, válassza a **jelentés letöltése** lehetőséget.

    A jelentés magas szintű összefoglalást biztosít a kiválasztott standard megfelelőségi állapotáról Security Center értékelések adatai alapján. A jelentés az adott szabvány vezérlőelemeinek megfelelően van rendszerezve. A jelentés megosztható az érintett felekkel, és a belső és külső könyvvizsgálók számára is igazolhatja.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Megfelelőségi jelentés letöltése":::

## <a name="improve-your-compliance-posture"></a>A megfelelőségi testhelyzet javítása

A szabályozás megfelelőségi irányítópultján található információk használatával a javaslatok közvetlenül az irányítópulton való feloldásával javíthatja a megfelelőségi testtartást.

1.  Válassza ki az irányítópulton megjelenő sikertelen értékelések bármelyikét, és tekintse meg az adott javaslat részleteit. Mindegyik javaslat a probléma megoldásához szükséges szervizelési lépéseket tartalmazza.

1.  Válasszon ki egy adott erőforrást a további részletek megtekintéséhez és az adott erőforráshoz tartozó javaslat megoldásához. <br>Az **Azure CIS 1.1.0** standard esetében például válassza a **lemezes titkosítást a virtuális gépeken**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="A standard érdeklődők javaslatának kiválasztása közvetlenül a javaslat részleteit tartalmazó oldalra":::

1. Ebben a példában, ha a javaslat részletei lapon a **művelet elvégzése** lehetőséget választja, akkor a Azure Portal Azure-beli virtuálisgép-oldalain érkezik, ahol engedélyezheti a titkosítást a **Biztonság** lapon:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="A javaslat részletei lapon található művelet gomb a Szervizelési beállításokhoz vezet":::

    A javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).

1.  Miután lépéseket tett a javaslatok megoldására, látni fogja a megfelelőségi irányítópult jelentés eredményét, mert a megfelelőségi pontszáma javul.

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

Előfordulhat például, hogy egy adott felhasználót szeretne e-mailben elSecurity Center, ha a megfelelőségi értékelés sikertelen. Először létre kell hoznia a logikai alkalmazást (a [Azure Logic apps](../logic-apps/logic-apps-overview.md)használatával), majd az eseményindítót egy új munkafolyamat-automatizálásban kell beállítania, ahogyan az a [Security Center triggerekre adott válaszok automatizálása](workflow-automation.md)című cikkben leírtak szerint.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="A szabályozások megfelelőségi vizsgálatának módosításai a Munkafolyamat-automatizálás elindításához" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>GYIK – Jogszabályi megfelelési irányítópult

- [Milyen szabványok támogatottak a megfelelőségi irányítópulton?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Miért jelenik meg néhány vezérlő szürkén?](#why-do-some-controls-appear-grayed-out)
- [Hogyan távolíthatók el a beépített standard, például a PCI-DSS, az ISO 27001 vagy a SOC2 TSP az irányítópultról?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [Elvégeztem a javasolt módosítást a javaslat alapján, de az irányítópulton nem jelenik meg](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Milyen engedélyekre van szükségem a megfelelőségi irányítópult eléréséhez?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [A szabályozási megfelelőségi irányítópult nem töltődik be nekem](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Hogyan tekinthetem meg az irányítópulton szabványos átadási és sikertelen vezérlési jelentéseket?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Hogyan tölthetők le a megfelelőségi információkkal rendelkező jelentések a PDF-fájltól eltérő formátumban?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Hogyan hozhatok létre kivételeket a szabályzatoknak való megfelelőségi irányítópult egyes házirendjeihez?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Milyen Azure Defender-csomagokat vagy-licenceket kell használni a szabályozási megfelelőségi irányítópulton?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Milyen szabványok támogatottak a megfelelőségi irányítópulton?
Alapértelmezés szerint a szabályozás megfelelőségi irányítópultja az Azure biztonsági Teljesítménytesztét jeleníti meg. Az Azure biztonsági teljesítményteszt a Microsoft által létrehozott, az Azure-specifikus biztonsági irányelvek, valamint a közös megfelelőségi keretrendszereken alapuló megfelelőségi ajánlott eljárások. További információ az [Azure Security teljesítményteszt bemutatása](../security/benchmarks/introduction.md)című témakörben található.

A megfelelőség más szabványoknak való nyomon követéséhez explicit módon hozzá kell adnia őket az irányítópulthoz.
 
Olyan szabványokat adhat hozzá, mint például az Azure CIS 1.1.0 (új), NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-v2020, Egyesült Királyság hivatalos és egyesült királysági NHS, HIPAA HITRUST, Kanada szövetségi PBMM, ISO 27001, SOC2-TSP és PCI-DSS 3.2.1.  
 
További szabványok lesznek hozzáadva az irányítópulthoz, és szerepelnek a [szabályzat megfelelőségi irányítópultján található szabványok testreszabásával](update-regulatory-compliance-packages.md)kapcsolatos információkban.

### <a name="why-do-some-controls-appear-grayed-out"></a>Miért jelenik meg néhány vezérlő szürkén?
Az irányítópult minden megfelelőségi szabványa esetében megjelenik a standard vezérlők listája. A megfelelő vezérlők esetében megtekintheti az átadási és sikertelen értékelések részleteit.

Egyes vezérlők szürkén jelennek meg. Ezekhez a vezérlőkhöz nem tartozik Security Center Értékelés. Előfordulhat, hogy néhány eljárás vagy folyamat kapcsolódik, ezért nem ellenőrizhető Security Center. Néhány nem rendelkezik olyan automatizált szabályzatokkal vagy értékelésekkel, amelyek még nem lettek megvalósítva, de a jövőben is lesznek. A [felhőben a megosztott felelősséggel](../security/fundamentals/shared-responsibility.md)kapcsolatos egyes vezérlők a platform felelősségi körét is felhasználhatják. 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Hogyan távolíthatók el a beépített standard, például a PCI-DSS, az ISO 27001 vagy a SOC2 TSP az irányítópultról? 
Ha testre szeretné szabni a megfelelőségi irányítópultot, és csak az Önre vonatkozó szabványokra koncentrál, akkor a szervezete számára nem releváns, megjelenített szabályozási szabványok bármelyikét eltávolíthatja. A standard eltávolításához kövesse a [standard eltávolítása az irányítópultról](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)című témakör utasításait.

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>Elvégeztem a javasolt módosítást a javaslat alapján, de az irányítópulton nem jelenik meg
Miután lépéseket tett a javaslatok feloldására, várjon 12 órát, hogy megtekintse a megfelelőségi adataikat. Az értékelések körülbelül 12 óránként futnak, így a megfelelőségi adataira csak az értékelések futtatása után kerül sor.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Milyen engedélyekre van szükségem a megfelelőségi irányítópult eléréséhez?
A megfelelőségi információ megtekintéséhez legalább **olvasói** hozzáféréssel kell rendelkeznie a szabályzat megfelelőségi információhoz. így a biztonsági olvasónak önmagában nem lesz elég. Ha Ön az előfizetés globális olvasója, akkor az már elég lesz.

Az irányítópult eléréséhez és a szabványok kezeléséhez szükséges szerepkörök minimális készlete az **erőforrás-házirend közreműködője** és a **biztonsági rendszergazda**.


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>A szabályozási megfelelőségi irányítópult nem töltődik be nekem
A jogszabályi megfelelőségi irányítópult használatához Azure Security Center az előfizetés szintjén engedélyezni kell az Azure Defender használatát. Ha az irányítópult nem töltődik be megfelelően, próbálkozzon a következő lépésekkel:

1. Törölje a böngésző gyorsítótárát.
1. Próbáljon ki egy másik böngészőt.
1. Próbálja meg megnyitni az irányítópultot különböző hálózati helyről.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Hogyan tekinthetem meg az irányítópulton szabványos átadási és sikertelen vezérlési jelentéseket?
A fő irányítópulton megtekintheti az áthaladó és sikertelen vezérlők jelentését (1) az irányítópulton az "első 4" legalacsonyabb megfelelőségi szabványt. Az összes átadott/sikertelen vezérlőelem állapotának megtekintéséhez válassza a (2) az **összes *x*** (ahol x a nyomon követett szabványok száma) elemet. A környezeti sík megjeleníti a nyomon követett szabványok megfelelőségi állapotát.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="A szabályozási megfelelőségi irányítópult összefoglalás szakasza":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Hogyan tölthetők le a megfelelőségi információkkal rendelkező jelentések a PDF-fájltól eltérő formátumban?
Amikor kiválasztja a **jelentés letöltése** lehetőséget, válassza ki a szabványos és a FORMÁTUMOT (PDF vagy CSV). Az eredményül kapott jelentés a portál szűrője által kiválasztott előfizetések aktuális készletét fogja tükrözni.

- A PDF-jelentés a kiválasztott standard összegzési állapotát jeleníti meg
- A CSV-jelentés erőforrások részletes eredményeit jeleníti meg, mivel az egyes vezérlőkhöz társított szabályzatokhoz kapcsolódik.

Jelenleg nem támogatott egy jelentés letöltése egyéni házirendre. csak a megadott szabályozási szabványok esetében.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Hogyan hozhatok létre kivételeket a szabályzatoknak való megfelelőségi irányítópult egyes házirendjeihez?
A Security Centerba beépített és a biztonságos pontszámban foglalt szabályzatok esetében a biztonsági [pontszámban szereplő erőforrások és javaslatok](exempt-resource.md)kizárásával egy vagy több erőforrásra vonatkozóan is létrehozhat kivételeket közvetlenül a portálon.

Más szabályzatok esetén a kivételt közvetlenül a szabályzatban hozhatja létre [Azure Policy kivételek struktúrájának](../governance/policy/concepts/exemption-structure.md)utasításait követve.


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Milyen Azure Defender-csomagokat vagy-licenceket kell használni a szabályozási megfelelőségi irányítópulton?
Ha bármelyik Azure Defender-csomaggal engedélyezte az összes Azure-erőforrást, hozzáférhet a szabályozási megfelelőségi irányítópulthoz az összes adattal együtt Security Center.





## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Security Center szabályozási megfelelőségi irányítópultját a következőre:

> [!div class="checklist"]
> * Megtekintheti és figyelheti a megfelelőségi testtartást az Ön számára fontos szabványokkal és előírásokkal kapcsolatban.
> * Javítsa a megfelelőségi állapotot a kapcsolódó javaslatok feloldásával és a megfelelőségi pontszám javításával.

A szabályozási megfelelőségi irányítópult nagy mértékben leegyszerűsítheti a megfelelőségi folyamatot, és jelentős mértékben csökkentheti az Azure-, hibrid és többfelhős környezet megfelelőségi igazolásának begyűjtéséhez szükséges időt.

További információkért tekintse meg a következő kapcsolódó lapokat:

- [Szabja testre a szabványok készletét a szabályozási megfelelőségi irányítópulton](update-regulatory-compliance-packages.md) – Ismerje meg, hogy milyen szabványok jelenjenek meg a szabályozási megfelelőségi irányítópulton. 
- [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan használhatók a javaslatok a Security Centerban az Azure-erőforrások védelme érdekében.