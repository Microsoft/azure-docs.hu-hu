---
title: Biztonságos fejlesztés – ajánlott eljárások Microsoft Azure
description: Ajánlott eljárások a biztonságosabb kódok kifejlesztéséhez és a felhőben biztonságosabb alkalmazások üzembe helyezéséhez.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: baaa311620f5c10948aa3494002ce359cc5dab28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517172"
---
# <a name="secure-development-best-practices-on-azure"></a>Biztonságos fejlesztés – ajánlott eljárások az Azure-ban
Ez a cikksorozat azokat a biztonsági tevékenységeket és ellenőrzéseket ismerteti, amelyeket figyelembe kell venni a Felhőbeli alkalmazások fejlesztésekor. A Microsoft biztonsági fejlesztési életciklus (SDL) és a biztonsági kérdések és fogalmak szakaszait az életciklus egyes szakaszaiban figyelembe kell venni. A cél az, hogy segítséget nyújtson olyan tevékenységek és Azure-szolgáltatások definiálásához, amelyeket az életciklus egyes fázisaiban használhat a biztonságosabb alkalmazások tervezéséhez, fejlesztéséhez és üzembe helyezéséhez.

A cikkekben szereplő javaslatok az Azure Security és az ügyfelek tapasztalataiból származnak. Ezeket a cikkeket hivatkozásként használhatja a fejlesztési projekt egy adott fázisában, de javasoljuk, hogy az összes cikket is olvassa az elejétől a végéig, legalább egyszer. Az összes cikk olvasása bemutatja azokat a fogalmakat, amelyek esetleg kimaradtak a projekt korábbi szakaszaiban. Ezen fogalmak alkalmazása a termék kiadása előtt segít a biztonságos szoftverek kiépítésében, a biztonsági megfelelőségi követelmények teljesítésében és a fejlesztési költségek csökkentésében.

Ezeknek a cikkeknek a célja, hogy a szoftverfejlesztők, fejlesztők és tesztelők számára a biztonságos Azure-alkalmazások fejlesztéséhez és üzembe helyezéséhez szükséges erőforrásokat használják.

## <a name="overview"></a>Áttekintés

A biztonság az alkalmazások egyik legfontosabb aspektusa, és ez nem egyszerű dolog. Az Azure Szerencsére számos szolgáltatást kínál, amelyek segítségével biztonságossá teheti alkalmazását a felhőben. Ezek a cikkek a szoftverfejlesztői életciklus egyes szakaszaiban megvalósított tevékenységekkel és Azure-szolgáltatásokkal foglalkoznak, így könnyebben fejleszthető és biztonságosabb alkalmazások helyezhetők üzembe a felhőben.

## <a name="security-development-lifecycle"></a>Biztonságos fejlesztési életciklus

A biztonságos szoftverfejlesztés ajánlott eljárásait követve integrálni kell a biztonságot a szoftverfejlesztői életciklus egyes szakaszaiba, a követelmények elemzését a karbantartásig, a projekt módszertanának megfelelően ([vízesés](https://en.wikipedia.org/wiki/Waterfall_model), [agilis](https://en.wikipedia.org/wiki/Agile_software_development)vagy [DevOps](https://en.wikipedia.org/wiki/DevOps)) függetlenül. A nagy horderejű adatbehatolások és a működési biztonsági hibák kiaknázása miatt a fejlesztők egyre jobban megértették, hogy a fejlesztési folyamat során a biztonságot kell megoldani.

A későbbiekben kijavít egy problémát a fejlesztési életciklusában, annál nagyobb lesz a megoldás. A biztonsági problémák nem kivételek. Ha a szoftverfejlesztés korai fázisaiban figyelmen kívül hagyja a biztonsági problémákat, akkor az alábbi fázisok öröklik az előző fázis biztonsági réseit. A végtermék több biztonsági problémát és a szerződésszegés lehetőségét is felhalmozottan fogja tartalmazni. A fejlesztési életciklus egyes szakaszaiban a biztonság kiépítése segít a korai problémák megoldásában, és segít csökkenteni a fejlesztési költségeket.

A Microsoft [biztonsági fejlesztési életciklus (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) szakaszait követjük olyan tevékenységek és Azure-szolgáltatások bevezetéséhez, amelyek segítségével az életciklus minden fázisában a biztonságos szoftverfejlesztés gyakorlatait lehet teljesíteni.

Az SDL-fázisok a következők:

  - Képzés
  - Követelmények
  - Tervezés
  - Implementálás
  - Ellenőrzés
  - Kiadás
  - Reagálás

![Biztonságos fejlesztési életciklus](./media/secure-dev-overview/01-sdl-phase.png)

Ezekben a cikkekben az SDL fázisokat a tervezés, fejlesztés és üzembe helyezés témakörbe csoportosítjuk.

## <a name="engage-your-organizations-security-team"></a>A szervezet biztonsági csapatának bevonása

A szervezet olyan formális alkalmazás-biztonsági programmal rendelkezhet, amely segítséget nyújt a biztonsági tevékenységek elindításának befejezéséhez a fejlesztési életciklus során. Ha a szervezete biztonsági és megfelelőségi csapatokkal rendelkezik, az alkalmazás fejlesztésének megkezdése előtt mindenképp folytassa őket. Kérdezze meg őket az SDL minden egyes fázisában, hogy vannak-e kihagyott feladatok.

Tisztában vagyunk azzal, hogy számos olvasó nem rendelkezhet biztonsági vagy megfelelőségi csapattal a részvételhez. Ezek a cikkek segítséget nyújtanak az SDL egyes fázisaiban megfontolandó biztonsági kérdések és döntések meghozatalában.

## <a name="resources"></a>Erőforrások

A következő források segítségével többet tudhat meg a biztonságos alkalmazások fejlesztéséről és az Azure-beli alkalmazások biztonságossá tételéről:

[Microsoft biztonsági fejlesztési életciklus (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) – az SDL egy olyan szoftverfejlesztési folyamat a Microsofttól, amely segít a fejlesztőknek biztonságosabb szoftverek készítésében. Ez segít a biztonsági megfelelőségi követelmények kezelésében a fejlesztési költségek csökkentése mellett.

A [Web Application Security Project (OWASP) megnyitása](https://www.owasp.org/index.php/Main_Page) – a OWASP egy online közösség, amely szabadon elérhető cikkeket, módszereket, dokumentációt, eszközöket és technológiákat hoz létre a webalkalmazások biztonsága terén.

A [bal oldali, például a főnök](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – számos online cikk, amely az alkalmazások különböző típusú biztonsági tevékenységeit ismerteti, amelyeket a fejlesztőknek be kell fejezniük a biztonságosabb kód létrehozásához.

[Microsoft Identity platform](../../active-directory/develop/index.yml) – a Microsoft Identity platform az Azure ad Identity Service és a fejlesztői platform fejlődése. Ez egy teljes körű funkcionalitást biztosító platform, amely egy hitelesítési szolgáltatásból, nyílt forráskódú könyvtárakból, alkalmazás-regisztrációból és-konfigurációból, teljes fejlesztői dokumentációból, kód-mintákból és más fejlesztői tartalmakból áll. A Microsoft Identity platform támogatja az iparági szabványnak megfelelő protokollokat, például a OAuth 2,0 és az OpenID connectet.

[Ajánlott biztonsági eljárások az Azure-megoldásokhoz](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – az Azure-ban felhőalapú megoldások tervezésekor, üzembe helyezéséhez és kezeléséhez használt ajánlott biztonsági eljárások gyűjteménye. Ez a dokumentum az informatikai szakemberek számára készült erőforrás. Ilyenek lehetnek a biztonságos Azure-megoldások készítésére és üzembe helyezésére szolgáló tervezők, építészek, fejlesztők és tesztelők.

[Biztonsági és megfelelőségi tervek az Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) -ban – az Azure biztonsági és megfelelőségi tervei olyan erőforrások, amelyek segítségével a szigorú szabályozásoknak és szabványoknak megfelelő felhőalapú alkalmazásokat hozhat létre és indíthat el.

## <a name="next-steps"></a>Következő lépések
A következő cikkekben olyan biztonsági vezérlőket és tevékenységeket ajánlunk, amelyek segíthetnek a biztonságos alkalmazások megtervezésében, fejlesztésében és üzembe helyezésében.

- [Biztonságos alkalmazások tervezése](secure-design.md)
- [Biztonságos alkalmazások fejlesztése](secure-develop.md)
- [Biztonságos alkalmazások telepítése](secure-deploy.md)