---
title: 'Oktatóanyag: Jogszabályi megfelelőségi ellenőrzések – Azure Security Center'
description: 'Oktatóanyag: Megtudhatja, hogyan javíthatja a jogszabályi megfelelést a Azure Security Center.'
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
ms.openlocfilehash: 284a7f532ed918397fe1cfcf3458bbc6fb0bdd32
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739007"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Oktatóanyag: Jogszabályi megfelelés javítása

Azure Security Center a jogszabályi megfelelőségi irányítópult segítségével leegyszerűsíti a szabályozási megfelelőségi követelmények **teljesítésének folyamatát.** 

Security Center kiértékeli a hibrid felhőkörnyezetet, hogy elemezze a kockázati tényezőket az előfizetéseire alkalmazott szabványokban található vezérlők és ajánlott eljárások alapján. Az irányítópulton látható, hogy ön megfelel-e ezeknek a szabványoknak. 

Amikor engedélyezi a Security Center azure-előfizetésben, a rendszer automatikusan hozzárendeli az [Azure-biztonsági](https://docs.microsoft.com/security/benchmark/azure/introduction) teljesítménytesztet az előfizetéshez. Ez a széles körben elterjedt teljesítményteszt a [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) és a National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) vezérlőire épül, és a felhőközpontú biztonságra összpontosít.

A jogszabályi megfelelés irányítópultja a környezetben a választott szabványok és szabályozások összes felmérésének állapotát jeleníti meg. Amikor a javaslatok alapján tesz, és csökkenti a környezeti kockázati tényezőket, javul a megfelelőségi helyzet.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A jogszabályi megfelelőség kiértékelése a jogszabályi megfelelési irányítópult használatával
> * A megfelelőségi helyzet javítása javaslatokkal
> * Riasztások beállítása a megfelelőségi rendszer változásairól
> * Megfelelőségi adatok exportálása folyamatos streamként és heti pillanatképekként

Ha nem rendelkezik Azure-előfizetéssel, [](https://azure.microsoft.com/free/) kezdés előtt hozzon létre egy ingyenes fiókot.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban szereplő funkciók végiglépése:

- [Azure Defender](azure-defender.md) engedélyezni kell. A Azure Defender 30 napig ingyenesen kipróbálhatja.
- Olyan fiókkal kell bejelentkeznie, amely olvasói hozzáféréssel rendelkezik a szabályzat megfelelőségi adataihoz (a **biztonsági** olvasó nem elegendő). Az előfizetés **globális** olvasói szerepköre működni fog. Legalább az Erőforrás-szabályzat közreműködője  és a Biztonsági rendszergazda szerepkört **kell hozzárendelni.**

##  <a name="assess-your-regulatory-compliance"></a>A jogszabályi megfelelés felmérése

A jogszabályi megfelelési irányítópult megjeleníti a kiválasztott megfelelőségi szabványokat és azok összes követelményét, ahol a támogatott követelmények a megfelelő biztonsági értékelésekhez vannak hozzárendelve. Ezeknek az értékeléseknek az állapota a szabványnak való megfelelést tükrözi.

A jogszabályi megfelelési irányítópult segítségével a kiválasztott szabványoknak és szabályozásoknak való megfelelés hiányosságaira összpontosíthat. Ez az összpontosított nézet azt is lehetővé teszi, hogy folyamatosan figyelje a megfelelőséget a dinamikus felhőbeli és hibrid környezetekben.

1. A Security Center válassza a Jogszabályi **megfelelőség lehetőséget.**

    A képernyő tetején található egy irányítópult, amely áttekintést nyújt a megfelelőségi állapotról és a támogatott megfelelőségi szabályozások készletről. Itt láthatja a teljes megfelelőségi pontszámot, valamint az egyes standardok által hozzárendelt átadási és sikertelen értékelések számát.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Jogszabályi megfelelési irányítópult" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. Válassza ki az Ön számára releváns megfelelőségi szabvány lapfülét (1). Láthatja, hogy a standard előfizetések mely előfizetésre vonatkoznak (2), és láthatja az erre a standardra vonatkozó összes vezérlő listáját (3). A vonatkozó vezérlők esetén megtekintheti a vezérlőhöz társított átadási és sikertelen értékelések részleteit (4), valamint az érintett erőforrások számát (5). Egyes vezérlők szürkén jelennek meg. Ezekhez a vezérlőkhöz nem tartozik Security Center értékelés. Ellenőrizze a követelményeket, és értékelje őket a környezetében. Ezek némelyike folyamattal kapcsolatos, és nem műszaki jellegű.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Egy adott szabványnak való megfelelés részleteinek feltárása":::

1. Egy adott szabvány aktuális megfelelőségi állapotának összegzését összegző PDF-jelentés létrehozásához válassza a **Jelentés letöltése lehetőséget.**

    A jelentés magas szintű összegzést nyújt a kiválasztott szabvány megfelelőségi állapotáról az értékelések Security Center alapján. A jelentés az adott szabvány vezérlői szerint van rendezve. A jelentés megosztható az érintett felekkel, és bizonyítékokat nyújthat a belső és külső auditorok számára.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Megfelelőségi jelentés letöltése":::

## <a name="improve-your-compliance-posture"></a>A megfelelőségi helyzet javítása

A jogszabályi megfelelőségi irányítópulton található információk használatával javíthatja a megfelelőségi helyzeteket azáltal, hogy közvetlenül az irányítópulton belül feloldja a javaslatokat.

1.  Válassza ki az irányítópulton megjelenő hibás értékeléseket a javaslat részleteinek megtekintéséhez. Minden javaslat tartalmaz néhány javítási lépést a probléma megoldásához.

1.  Válasszon ki egy adott erőforrást a további részletek megtekintéséhez és az erőforrásra vonatkozó javaslat megoldásához. <br>Például az **Azure CIS 1.1.0 szabványban** válassza ki azt a javaslatot, hogy lemeztitkosítást kell alkalmazni **a virtuális gépeken.**

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="Javaslat kiválasztása standard érdeklődőkből közvetlenül a javaslat részleteit tartalmazó oldalra":::

1. Ebben a példában  a Művelet művelet kiválasztásakor a javaslat részleteit tartalmazó lapon a Azure Portal Azure-beli virtuális gép lapjaira érkezik, ahol a Biztonság lapon engedélyezheti a **titkosítást:**

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="A javaslat részleteit tartalmazó oldalon a Művelet gomb a szervizelési lehetőségekhez vezet":::

    A javaslatok alkalmazásával kapcsolatos további információkért lásd: [Implementing security recommendations in Azure Security Center](security-center-recommendations.md).

1.  Miután lépéseket tett a javaslatok megoldásához, az eredmény a megfelelőségi irányítópult jelentésében látható lesz, mivel a megfelelőségi pontszám javul.

    > [!NOTE]
    > Az értékelések körülbelül 12 óránként futnak le, így csak a vonatkozó értékelés következő futtatása után fogja látni a megfelelőségi adatokra gyakorolt hatást.


## <a name="export-your-compliance-status-data"></a>A megfelelőségi állapotadatok exportálása

Ha szeretné nyomon követni a megfelelőségi állapotot a környezet más monitorozási eszközeivel, a Security Center egy exportálási mechanizmussal teszi ezt egyértelművé. Konfigurálja **a folyamatos exportálást** úgy, hogy a kiválasztott adatokat egy Azure Event Hubba vagy egy Log Analytics-munkaterületre küldje.

Adatok folyamatos exportálása Azure-eseményközpontba vagy Log Analytics-munkaterületre:

- Az összes jogszabályi megfelelési adat exportálása folyamatos **streambe:**

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="Szabályozási megfelelőségi adatok streamének folyamatos exportálása" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- Heti **pillanatképek exportálása** a jogszabályi megfelelési adatokról:

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="A jogszabályi megfelelési adatok heti pillanatképének folyamatos exportálása" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

A megfelelőségi **adatokról pdf/CSV-jelentést** közvetlenül a jogszabályi megfelelési irányítópultról is exportálhat:

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="Jogszabályi megfelelési adatok exportálása PDF- vagy CSV-jelentésként" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

További információkért olvassa [el a következőt: Security Center adatok folyamatos exportálása.](continuous-export.md)


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>Munkafolyamat-automatizálások futtatása a megfelelőség módosítása esetén

Security Center munkafolyamat-automatizálási szolgáltatása aktiválhat Logic Apps amikor az egyik jogszabályi megfelelőségi értékelés állapotváltozást vált ki.

Előfordulhat például, hogy Security Center szeretne e-mailt küldeni egy adott felhasználónak, ha egy megfelelőségi értékelés meghiúsul. Először létre kell hoznia a logikai alkalmazást (a Azure Logic Apps [használatával),](../logic-apps/logic-apps-overview.md)majd be kell állítania az eseményindítót egy új [munkafolyamat-automatizálásban](workflow-automation.md)a válaszok automatizálása az eseményindítókra való Security Center leírtak szerint.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="A jogszabályi megfelelési értékelések változásainak használata munkafolyamat-automatizálás aktiválásához" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>GYIK – Jogszabályi megfelelési irányítópult

- [Milyen szabványok támogatottak a megfelelőségi irányítópulton?](#what-standards-are-supported-in-the-compliance-dashboard)
- [Miért jelennek meg szürkén egyes vezérlők?](#why-do-some-controls-appear-grayed-out)
- [Hogyan távolítható el egy beépített szabvány, például a PCI-DSS, az ISO 27001 vagy a SOC2 TSP az irányítópultról?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [A javaslat alapján módosítottam a javasolt módosításokat, de az irányítópulton nem jelenik meg](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [Milyen engedélyekre van szükségem a megfelelőségi irányítópult eléréséhez?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [A jogszabályi megfelelési irányítópult nem töltődik be a számomra](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [Hogyan tudom megtekinteni az irányítópultomon szabványonkénti átadási és sikertelen vezérlőkről szóló jelentést?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [Hogyan tölthetek le olyan jelentést, amely nem PDF formátumú megfelelőségi adatokat tartalmaz?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [Hogyan hozhatok létre kivételeket egyes szabályzatokhoz a jogszabályi megfelelési irányítópulton?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [Milyen Azure Defender vagy licencre van szükségem a jogszabályi megfelelési irányítópulthoz?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>Milyen szabványok támogatottak a megfelelőségi irányítópulton?
Alapértelmezés szerint a jogszabályi megfelelőség irányítópultján megjelenik az Azure biztonsági teljesítményteszt. Az Azure biztonsági teljesítménytesztje a Microsoft által meghozott, a biztonságra és megfelelőségre vonatkozó, általános megfelelőségi keretrendszeren alapuló, Azure-specifikus irányelvek. További információ: [Az Azure biztonsági teljesítményteszt bemutatása.](../security/benchmarks/introduction.md)

A többi szabványnak való megfelelés nyomon követéséhez explicit módon hozzá kell azokat adni az irányítópulthoz.
 
Olyan szabványokat adhat hozzá, mint az Azure CIS 1.1.0 (új), az NIST SP 800-53 R4, NIST SP 800-171 R2, SWIFT CSP CSCF-v2020, UK Official és UK NHS, HIPAA HITRUST, Canada Federal PBMM, ISO 27001, SOC2-TSP és PCI-DSS 3.2.1.  
 
További szabványok lesznek hozzáadva az irányítópulthoz, és szerepelnek a jogszabályi megfelelőségi irányítópult szabványkészletének testreszabásával [kapcsolatos információk között.](update-regulatory-compliance-packages.md)

### <a name="why-do-some-controls-appear-grayed-out"></a>Miért jelennek meg szürkén egyes vezérlők?
Az irányítópulton minden megfelelőségi szabványhoz megjelenik a szabvány vezérlőinek listája. A megfelelő vezérlők esetén megtekintheti az átadások és a sikertelen értékelések részleteit.

Egyes vezérlők szürkén jelennek meg. Ezekhez a vezérlőkhöz nincs Security Center értékelés társítva. Ezek némelyike eljáráshoz vagy folyamathoz köthető, ezért nem ellenőrizhető a Security Center. Vannak, akik még nem valósítanak meg automatizált szabályzatokat vagy értékeléseket, de a jövőben lesznek. Egyes vezérlők pedig a platform felelősségi alá tartoznak, ahogy azt a [Felhőbeli megosztott felelősség részben is ismertetheti.](../security/fundamentals/shared-responsibility.md) 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>Hogyan távolítható el egy beépített szabvány, például a PCI-DSS, az ISO 27001 vagy a SOC2 TSP az irányítópultról? 
Ha testre szeretné szabni a jogszabályi megfelelési irányítópultot, és csak az Ön számára érvényes szabványokra szeretne összpontosítani, eltávolíthatja a szervezet számára nem releváns megjelenített szabályozási szabványokat. A standardok eltávolításához kövesse a Szabványos eltávolítása [az irányítópultról útmutatását.](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>A javaslat alapján módosítottam a javasolt módosításokat, de az irányítópulton nem jelenik meg
Miután végrehajtotta a javaslatok megoldásához szükséges lépéseket, várjon 12 órát a megfelelőségi adatok változásainak stb. Az értékelések körülbelül 12 óránként futnak, így a megfelelőségi adatokra csak az értékelések futtatása után lesz hatással.
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>Milyen engedélyekre van szükségem a megfelelőségi irányítópult eléréséhez?
A megfelelőségi adatok megtekintéséhez legalább  Olvasói hozzáféréssel kell rendelkezik a szabályzat megfelelőségi adataihoz is; így a biztonsági olvasó önmagában nem elegendő. Ha Ön globális olvasó az előfizetésben, az is elég lesz.

Az irányítópult elérésére és a szabványok kezelésére szolgáló szerepkörök minimális készlete az **Erőforrás-házirend közreműködője** és **a Biztonsági rendszergazda.**


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>A jogszabályi megfelelési irányítópult nem töltődik be a számomra
A jogszabályi megfelelési irányítópult használatának Azure Security Center előfizetési szinten Azure Defender kell lennie. Ha az irányítópult nem töltődik be megfelelően, próbálkozzon a következő lépésekkel:

1. Törölje a böngésző gyorsítótárát.
1. Próbálkozzon egy másik böngészővel.
1. Próbálja meg más hálózati helyről megnyitni az irányítópultot.


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>Hogyan tudom megtekinteni az irányítópultomon szabványonkénti átadási és sikertelen vezérlőkről szóló jelentést?
A fő irányítópulton látható egy jelentés, amely az irányítópulton az "első 4" legalacsonyabb megfelelőségi szabvány (1) vezérlőinek átadásával és sikertelenségére vonatkozó jelentéseket tartalmaz. Az átadó/sikertelen vezérlők állapotának ellenőrzéshez válassza a (2) **Show all *x*** (where x is the number of standards you you tracking) (Az összes x megjelenítése (ahol x a nyomon követni kívánt szabványok száma). A környezetsík megjeleníti az összes nyomon követett szabvány megfelelőségi állapotát.

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="A jogszabályi megfelelési irányítópult összefoglaló szakasza":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>Hogyan tölthetek le olyan jelentést, amely nem PDF formátumú megfelelőségi adatokat tartalmaz?
Ha a Jelentés **letöltése lehetőséget választja,** válassza ki a szabványt és a formátumot (PDF vagy CSV). Az eredményül kapott jelentés a portál szűrője által kiválasztott előfizetések aktuális készletét fogja tükrözni.

- A PDF-jelentés a kiválasztott szabvány összegző állapotát jeleníti meg
- A CSV-jelentés részletes eredményeket biztosít erőforrásonként, mivel az egyes vezérlőkhöz társított szabályzatokkal kapcsolatos

Az egyéni szabályzatok jelentéseit jelenleg nem lehet letölteni; csak a megadott szabályozási szabványoknak megfelelően.


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>Hogyan hozhatok létre kivételeket egyes szabályzatokhoz a jogszabályi megfelelési irányítópulton?
Az Security Center beépített és a biztonsági pontszámba foglalt szabályzatok számára kivételeket hozhat létre közvetlenül a portálon egy vagy több erőforráshoz, az erőforrások és a biztonsági pontszám alóli javaslatok mentesítése részben leírtak [szerint.](exempt-resource.md)

Más szabályzatok számára közvetlenül a szabályzatban hozhat létre kivételt a [kivételstruktúrában található Azure Policy követve.](../governance/policy/concepts/exemption-structure.md)


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>Milyen Azure Defender vagy licencre van szükségem a jogszabályi megfelelési irányítópulthoz?
Ha az összes Azure Defender-csomag engedélyezve van bármelyik Azure-erőforrástípuson, akkor az összes adatával együtt hozzáférhet a jogszabályi megfelelőségi irányítópulthoz a Security Center.





## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan felhasználásával Security Center megfelelőségi irányítópultját a következőre:

> [!div class="checklist"]
> * Az Ön számára fontos szabványokra és szabályozásra vonatkozó megfelelőségi állapot megtekintése és figyelése.
> * Javítsa a megfelelőségi állapotot a vonatkozó javaslatok feloldásával és a megfelelőségi pontszám javításának figyelésával.

A jogszabályi megfelelőségi irányítópult jelentősen leegyszerűsítheti a megfelelőségi folyamatot, és jelentősen csökkentheti az Azure-, hibrid- és többfelhős környezet megfelelőségi bizonyítékának gyűjtéséhez szükséges időt.

További tudnivalókért tekintse meg a következő kapcsolódó oldalakat:

- [A szabványok halmazának testreszabása](update-regulatory-compliance-packages.md) a jogszabályi megfelelési irányítópulton – Megtudhatja, hogyan választhatja ki, hogy mely szabványok jelenjenek meg a jogszabályi megfelelési irányítópulton. 
- [Biztonsági javaslatok kezelése a Azure Security Center](security-center-recommendations.md) – Megtudhatja, hogyan használhatja a Security Center az Azure-erőforrások védelméhez.