---
title: Biztonságos alkalmazások telepítése Microsoft Azure
description: Ez a cikk a webalkalmazás-projekt kiadási és reagálási fázisaiban megfontolandó ajánlott eljárásokat ismerteti.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4c71ddbf1d2b435697b2707acf0b1262f2c5dc31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517189"
---
# <a name="deploy-secure-applications-on-azure"></a>Biztonságos alkalmazások üzembe helyezése az Azure-ban
Ebben a cikkben bemutatjuk azokat a biztonsági tevékenységeket és ellenőrzéseket, amelyeket érdemes figyelembe venni a Felhőbeli alkalmazások telepítésekor. A biztonsági kérdéseket és fogalmakat a Microsoft [biztonsági fejlesztési életciklus (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) kiadási és reagálási fázisaiban figyelembe kell venni. A cél az, hogy segítséget nyújtson olyan tevékenységek és Azure-szolgáltatások definiálásához, amelyeket a biztonságosabb alkalmazások telepítéséhez használhat.

Ebben a cikkben a következő SDL-fázisokat tárgyaljuk:

- Kiadás
- Reagálás

## <a name="release"></a>Kiadás
A kiadási fázis fókusza egy projekt nyilvános kiadásra való használatra kész.
Ez magában foglalja a kiadás utáni karbantartási feladatok hatékony végrehajtásának és a később esetlegesen előforduló biztonsági rések megtervezésének módját.

### <a name="check-your-applications-performance-before-you-launch"></a>A Kezdés előtt tekintse meg az alkalmazás teljesítményét

Mielőtt elindítja vagy üzembe helyezi a frissítéseket az éles környezetben, tekintse meg az alkalmazás teljesítményét. Futtasson felhőalapú [terhelési teszteket](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) a Visual Studióval az alkalmazásban felmerülő teljesítményproblémák kereséséhez, a központi telepítés minőségének javításához, győződjön meg arról, hogy az alkalmazás mindig fel van töltve vagy elérhető, és hogy az alkalmazás képes kezelni az indítási forgalmat.

### <a name="install-a-web-application-firewall"></a>Webalkalmazási tűzfal telepítése

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen kihasználók között az SQL-injektálási támadások és a helyek közötti parancsfájlok elleni támadások is előfordulnak. A támadásoknak az alkalmazás kódjában való megelőzése kihívást jelenthet. Az alkalmazás topológiájának számos rétegében szigorú karbantartási, javítási és figyelési funkciókra lehet szükség. A központosított WAF megkönnyíti A biztonsági felügyeletet. A WAF-megoldás a biztonsági fenyegetésekre is reagálhat egy ismert biztonsági rés központi helyen történő javításával, illetve az egyes webalkalmazások biztonságossá tételével.

Az [Azure Application Gateway WAF](../../web-application-firewall/ag/ag-overview.md) a webalkalmazások központosított védelmét nyújtja a gyakori támadások és biztonsági rések ellen. A WAF az 3,0-es és a 2.2.9-es [OWASP-alapszabállyal](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) kapcsolatos szabályokon alapul.

### <a name="create-an-incident-response-plan"></a>Incidensmegoldási terv létrehozása

Az incidensekre adott válasz tervének előkészítése elengedhetetlen az olyan új fenyegetések kezeléséhez, amelyek idővel felmerülhetnek. Az incidensekre vonatkozó válaszadási terv előkészítése magában foglalja a megfelelő biztonsági vészhelyzeti kapcsolatok azonosítását és a szervezet más csoportjaitól örökölt, illetve a licenccel rendelkező harmadik féltől származó kódokra vonatkozó biztonsági karbantartási tervek létrehozását.

### <a name="conduct-a-final-security-review"></a>Végső biztonsági felülvizsgálat végrehajtása

Az összes elvégzett biztonsági tevékenység szándékos áttekintése segíti a szoftveres kiadás vagy alkalmazás készültségének biztosítását. Az utolsó biztonsági felülvizsgálat (FSR) általában magában foglalja a veszélyforrások modelljeinek, eszközeinek kimenetének és teljesítményének vizsgálatát a követelmények szakaszban meghatározott minőségi kapuk és hibák terén.

### <a name="certify-release-and-archive"></a>Kiadás és archiválás tanúsítása

A szoftverek kiadása előtti minősítéssel biztosítható, hogy a biztonsági és adatvédelmi követelmények teljesüljenek. A kiadás utáni karbantartási feladatok elvégzéséhez elengedhetetlen az összes releváns adat archiválása. Az archiválás emellett segít csökkenteni a tartós szoftverfejlesztés kapcsán felmerülő hosszú távú költségeket.

## <a name="response"></a>Reagálás
A kiadás utáni fázis középpontja a fejlesztői csapat számára, amely képes és elérhető, hogy megfelelően reagáljon a feltörekvő szoftveres fenyegetések és biztonsági rések jelentéseire.

### <a name="execute-the-incident-response-plan"></a>Az incidensre adott válasz tervének végrehajtása

A kiadási fázisban megjelenő incidens-válaszintézkedések megvalósítása elengedhetetlen ahhoz, hogy segítse az ügyfeleket a szoftveres biztonság vagy az esetlegesen felmerülő adatvédelmi rések védelmében.

### <a name="monitor-application-performance"></a>Alkalmazások teljesítményének figyelése

Az alkalmazás az üzembe helyezése után folyamatosan figyelemmel kísérheti a teljesítménnyel kapcsolatos problémák észlelését és a biztonsági réseket.
Az alkalmazások figyelését támogató Azure-szolgáltatások a következők:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

A [Application Insights](../../azure-monitor/app/app-insights-overview.md) egy bővíthető Application Performance Management-(APM-) szolgáltatás, amely több platformon is használható webfejlesztőknek. Az élő webalkalmazásának figyelésére használhatja. Application Insights automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket. Hatékony elemzési eszközöket tartalmaz, amelyekkel diagnosztizálhatja a problémákat, és megtudhatja, hogy a felhasználók mit tesznek ténylegesen az alkalmazással. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

#### <a name="azure-security-center"></a>Azure Security Center

A [Azure Security Center](../../security-center/security-center-introduction.md) a fenyegetések megelőzését, észlelését és reagálását segíti az Azure-erőforrások, például a webalkalmazások biztonságának jobb láthatóságával (és szabályozásával). Azure Security Center segít felderíteni a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek. Különböző biztonsági megoldásokkal működik.

A Security Center ingyenes szintje korlátozott biztonságot nyújt az Azure-erőforrások számára. A [standard szintű Security Center](../../security-center/security-center-get-started.md) kiterjeszti ezeket a képességeket a helyszíni erőforrásokra és más felhőkre.
A Security Center Standard a következőket teszi lehetővé:

  - Megkeresheti és elháríthatja a biztonsági réseket.
  - Hozzáférés-és alkalmazás-vezérlőelemek alkalmazása a kártékony tevékenységek blokkolására.
  - Az elemzések és az intelligencia használatával észlelheti a fenyegetéseket.
  - Gyors reagálás a támadás alatt.

## <a name="next-steps"></a>Következő lépések
A következő cikkekben olyan biztonsági vezérlőket és tevékenységeket ajánlunk, amelyek segíthetnek a biztonságos alkalmazások tervezésében és fejlesztésében.

- [Biztonságos alkalmazások tervezése](secure-design.md)
- [Biztonságos alkalmazások fejlesztése](secure-develop.md)