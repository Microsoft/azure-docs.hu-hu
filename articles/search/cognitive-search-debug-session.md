---
title: Hibakeresési munkamenetek – fogalmak (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: A Azure Portal keresztül elért hibakeresési munkamenetek olyan IDE-környezetet biztosítanak, ahol azonosíthatja és kijavíthatja a hibákat, ellenőrizheti a módosításokat, és leküldheti a módosításokat a szakértelmével az AI-dúsítási folyamatban. A hibakeresési munkamenetek előzetes verzióban érhetők el.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: f666cc2f9d11b1e05d11ba266897e6fca3cd287e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92057620"
---
# <a name="debug-sessions-in-azure-cognitive-search"></a>Hibakeresési munkamenetek az Azure Cognitive Search

A hibakeresési munkamenetek egy olyan vizualizációs szerkesztő, amely a Azure Portal meglévő készségkészlet működik. A hibakeresési munkameneten belül azonosíthatja és elháríthatja a hibákat, ellenőrizheti a módosításokat, és leküldheti a változtatásokat egy éles készségkészlet az AI-dúsítási folyamat során.

> [!Important]
> A hibakeresési munkamenetek az előzetes verziójú szolgáltatások, amelyek szolgáltatói szerződés nélkül érhetők el, és nem ajánlottak éles számítási feladatokhoz. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="using-debug-sessions"></a>Hibakeresési munkamenetek használata

A munkamenet indításakor a szolgáltatás másolatot készít a készségkészlet, az indexelő és az indexről, ahol egyetlen dokumentum használatával tesztelheti a készségkészlet. A munkameneten belül végrehajtott módosításokat a rendszer menti a munkamenetbe. A hibakeresési munkameneten belül végrehajtott módosítások nem érintik a termelési készségkészlet, kivéve, ha a módosítások véglegesítve lesznek. A módosítások véglegesítése felülírja a termelési készségkészlet.

Egy hibakeresési munkamenet során szerkesztheti a készségkészlet, megvizsgálhatja és érvényesítheti a dúsítási fában lévő egyes csomópontokat egy adott dokumentum kontextusában. A dúsítási folyamat problémáinak feloldása után a módosítások menthetők a munkamenetbe, és elhelyezhetők a készségkészlet az éles környezetben. 

Ha a dúsítási folyamat nem tartalmaz hibát, a rendszer egy hibakeresési munkamenetet is felhasználhat a dokumentumok növekményes dúsítására, a módosítások véglegesítése előtt tesztelheti és érvényesítheti az egyes módosításokat.

## <a name="creating-a-debug-session"></a>Hibakeresési munkamenet létrehozása

A hibakeresési munkamenet elindításához rendelkeznie kell egy meglévő AI-bővítési folyamattal, beleértve a következőket: egy adatforrás, egy készségkészlet, egy indexelő és egy index. A hibakeresési munkamenet konfigurálásához nevezze el a munkamenetet, és adjon meg egy általános célú Storage-fiókot, amelyet a rendszer az indexelő futtatása során a szakértelem-végrehajtás gyorsítótárazásához használ majd. Azt is ki kell választania, hogy melyik indexelő fog futni. Az indexelő az adatforrásra, a készségkészlet és az indexre vonatkozó hivatkozásokat tartalmaz. A hibakeresési munkamenet alapértelmezett értéke az adatforrás első dokumentuma, vagy megadhat egy dokumentumot az adatforrásban a lépéshez.

> :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="Hibakeresési munkamenet létrehozása":::

## <a name="debug-session-features"></a>A munkamenet funkcióinak hibakeresése

A hibakeresési munkamenet a kiválasztott dokumentum készségkészlet végrehajtásával kezdődik. A hibakeresési munkamenet rögzíti a készségkészlet egyes műveleteihez kapcsolódó további metaadatokat is. A folyamathoz tartozó szaktudás-végrehajtások által létrehozott metaadatok a következő, a készségkészlet kapcsolatos problémák azonosításához és javításához használatos funkcióit tájékoztatják.

## <a name="ai-enrichments"></a>AI-bővítések

Ahogy a képességek kihasználják a dokumentumot jelképező dúsítások fáját, növekszik. A képességek és a dúsítások kimenetének megjelenítéséhez egy fa használatával átfogó képet nyújt az összes végrehajtott dúsításról. Megtekintheti a teljes dokumentumot, és megvizsgálhatja a dúsítási fa egyes csomópontjait. Ez a perspektíva megkönnyíti az objektumok formálását. Ez a formátum vizuális mutatókat is biztosít a fa egyes csomópontjainak típusához, elérési útjához és tartalmához.

## <a name="skill-graph"></a>Skill Graph

A **skill Graph** nézet a készségkészlet hierarchikus, vizuális ábrázolását biztosítja. A gráf a képességek végrehajtásának sorrendjét ábrázolja. A diagramon az egyéb képességek kimenete alapján megjelenő képességek alacsonyabbak lesznek. A hierarchia ugyanazon szintjének ismeretei párhuzamosan hajthatók végre. 

A gráfban lévő képességek kiválasztásával kiemelheti a hozzá kapcsolódó képességeket, a bemeneteit létrehozó csomópontokat és a kimeneteit elfogadó csomópontokat. Az egyes képességek csomópontok a típusait, hibáit, figyelmeztetéseit és a végrehajtások számát jelenítik meg. A **skill Graphban** kiválaszthatja, hogy melyik képességet szeretné a hibakereséshez vagy a fejlesztéshez. Ha kiválasztja a megfelelő képzettséget, a részletek ablaktáblán megjelennek a gráftól jobbra.

> :::image type="content" source="media/cognitive-search-debug/skills-graph.png" alt-text="Skill Graph":::

## <a name="skill-details"></a>Szakértelem részletei

A skill Details (szakértelem részletei) ablaktáblán egy adott képességgel rendelkező terület jelenik meg, ha az adott képesség ki van emelve a **skill gráfban**. Áttekintheti és szerkesztheti a képességek beállításainak részleteit. A skill JSON-definíciója van megadva. A szaktudás végrehajtásával, valamint a hibákkal és figyelmeztetésekkel kapcsolatos részletek is megjelennek. A skill **Settings (készségfejlesztés** ) lap & **skill JSON-szerkesztője** lehetővé teszi a képességek közvetlen szerkesztését. A [`</>`](#expression-evaluator) megnyit egy ablakot a szaktudás bemenetek és kimenetek kifejezésének megtekintéséhez és szerkesztéséhez.

A képességek beállítása ablakban beágyazott beviteli vezérlők használatával összetett alakzatokat hozhat létre a kivetítésekhez, a kimeneti mezők hozzárendeléséhez egy komplex típusú mezőhöz, vagy egy adott képességet. A **kifejezés-értékelő** használatával a beágyazott bemenetek egyszerű tesztelési és érvényesítési kifejezést biztosítanak.

## <a name="skill-execution-history"></a>Szaktudás-végrehajtási előzmények

Egy adott képesség több alkalommal is végrehajtható egyetlen dokumentum készségkészlet. Az OCR-képesség például az egyes dokumentumokból kinyert összes rendszerkép esetében egyszer lefut. A szakértelem részletei ablaktáblán a **végrehajtások** lap megjeleníti a szaktudás végrehajtási előzményeit, így mélyebben megtekintheti a szaktudás minden meghívását. 

A végrehajtási előzmények lehetővé teszik egy adott dúsítás visszakövetését az azt létrehozó képességhez. A képzettségi adatokra való kattintás a bemenetet generáló képességre navigál. Ez lehetővé teszi egy olyan probléma kiváltó okának azonosítását, amely egy alsóbb rétegbeli szakértelemben is megnyilvánulhat. 

A lehetséges problémák észlelése esetén a végrehajtási előzmények az adott bemeneteket generáló készségekre mutató hivatkozásokat jelenítenek meg, például egy verem-nyomkövetési funkciót biztosítanak. Ha egy bemenethez tartozó képességre kattint, megkeresi a képességet a hibák javításához, vagy folytathatja az adott probléma nyomon követését.

Egyéni szakértelem létrehozásakor vagy egy egyéni képességgel kapcsolatos hiba hibakeresése esetén lehetőség van arra, hogy a végrehajtás során meghívást Kérjen a végrehajtási előzményekben.

## <a name="enriched-data-structure"></a>Dúsított adatstruktúra

A bővített **adatstruktúra** ablaktábla a dokumentum dúsítását jeleníti meg a készségkészlet keresztül, részletezve az egyes alkoholtartalom-növelés és a kezdeményező képesség kontextusát. A **kifejezés kiértékelése** az egyes dúsítások tartalmának megtekintésére is használható.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-display.png" alt-text="Dúsított adatstruktúra":::

## <a name="expression-evaluator"></a>Kifejezés kiértékelése

A **kifejezés kiértékelése** gyors betekintést biztosít bármely útvonal értékére. Lehetővé teszi az elérési út szerkesztését és az eredmények tesztelését, mielőtt a képességek vagy a leképezés egyik bemenetét vagy környezetét frissíti.

## <a name="errorswarnings"></a>Hibák/figyelmeztetések

Ez az ablak megjeleníti az összes olyan hibát és figyelmeztetést, amelyet a készségkészlet a hibakeresési munkamenetben a dokumentumon végrehajtott végrehajtás során hoz létre.

## <a name="limitations"></a>Korlátozások

A hibakeresési sesisons az összes általánosan elérhető adatforrással, az AMD legtöbb előzetes verziójú adatforrásával működnek. A Cosmos DB MongoDB API (előzetes verzió) és Cassandra API (előzetes verzió) jelenleg nem támogatott.

## <a name="next-steps"></a>Következő lépések

Most, hogy megértette a hibakeresési munkamenetek elemeit, próbálja ki az oktatóanyagot gyakorlati tapasztalatokra.

> [!div class="nextstepaction"]
> [A hibakeresési munkamenetek funkciójának megismerése oktatóanyag](./cognitive-search-tutorial-debug-sessions.md)