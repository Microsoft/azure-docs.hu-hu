---
title: Az Azure Application Insights Felhasználókövetés elemzi a navigációs folyamatokat
description: Elemezheti, hogy a felhasználók hogyan navigálnak a webalkalmazás oldalai és funkciói között.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a31ab24b96fa44787d08801a0680f94ff98bb5a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87309269"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Felhasználói navigációs mintázatok elemzése Felhasználókövetésekkel Application Insights

![Application Insights Felhasználókövetés eszköz](./media/usage-flows/flows.png)

A Felhasználókövetés eszköz megjeleníti, hogyan navigálnak a felhasználók a webhely oldalai és funkciói között. A következő kérdésekre nagyszerű választ kaphat:

* Hogyan navigálnak a felhasználók a webhelyén lévő lapokról?
* Hogyan kattintanak a felhasználók a webhelyén lévő lapokra?
* Hol vannak a felhasználók az Ön webhelyéről leginkább átesett helyek?
* Vannak olyan helyek, ahol a felhasználók újra és újra megismétlik a műveletet?

A Felhasználókövetés eszköz a megadott kezdeti oldal nézetből, egyéni eseményből vagy kivételből indul el. A kezdeti esemény miatt Felhasználókövetés a felhasználói munkamenetek előtt és után megjelenő eseményeket jeleníti meg. A különböző vastagságú vonalak azt mutatják, hogy az egyes útvonalakat hány alkalommal követték a felhasználók. A speciális **munkamenet-elindított** csomópontok azt mutatják be, hogy a következő csomópontok mikor kezdték meg a munkamenetet A **munkamenet-befejező** csomópontok megmutatják, hány felhasználó nem adott meg oldalletöltések vagy egyéni eseményt az előző csomópont után, kiemelve, hogy a felhasználók valószínűleg otthagyták a helyet.

> [!NOTE]
> A Application Insights-erőforrásnak az Felhasználókövetés eszköz használatára vonatkozó oldalletöltések vagy egyéni események között kell szerepelnie. [Megtudhatja, hogyan állíthatja be az alkalmazást úgy, hogy automatikusan összegyűjtse a Application Insights JavaScript SDK-val](./javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>Kezdés: válasszon ki egy kezdeti eseményt

![Válasszon ki egy kezdeti eseményt Felhasználókövetés](./media/usage-flows/initial-event.png)

Ha meg szeretné kezdeni a Felhasználókövetés eszközzel kapcsolatos kérdések megválaszolását, válassza ki a kezdeti nézet, az egyéni esemény vagy a kivétel kiindulási pontként való kiszolgálását:

1. Kattintson a mit csinál a **felhasználók számára...?** cím hivatkozásra, vagy kattintson a **Szerkesztés** gombra.
2. Válassza ki az oldal nézetét, az egyéni eseményt vagy a kivételt a **kezdeti esemény** legördülő menüjéből.
3. Kattintson a **Graph létrehozása** elemre.

A vizualizáció "1. lépés" oszlopa azt mutatja, hogy a kezdeti esemény után legtöbbször a legtöbbet a legtöbbet a legtöbbet a legtöbbet a legtöbbet a legtöbbször megrendelt A "2. lépés" és az azt követő oszlopok azt mutatják be, hogy mit tettek a felhasználók, és létrehoztak egy képet arról, hogy a felhasználók milyen módon navigáltak a webhelyen.

Alapértelmezés szerint a Felhasználókövetés eszköz véletlenszerűen mintákat csak az elmúlt 24 órában, a webhelyről származó egyéni eseményt. Megnövelheti az időtartományt, és megváltoztathatja a véletlenszerű mintavétel teljesítményének és pontosságának egyenlegét a szerkesztési menüben.

Ha az oldalletöltések, az egyéni események és a kivételek nem relevánsak, kattintson a elrejteni kívánt csomópontok **X** elemére. Miután kiválasztotta az elrejteni kívánt csomópontokat, kattintson a **diagram létrehozása** gombra a vizualizáció alatt. Ha meg szeretné tekinteni az összes rejtett csomópontot, kattintson a **Szerkesztés** gombra, majd tekintse meg a **kizárt események** szakaszt.

Ha olyan oldalletöltések vagy egyéni események hiányoznak, amelyeket a vizualizációban várhatóan látni fog:

* A **Szerkesztés** menüben keresse meg a **kizárt események** szakaszt.
* A **többi** csomóponton található plusz gombokkal a vizualizációban ritkábban előforduló eseményeket is tartalmazhat.
* Ha a felhasználók nem ritkán küldik el az oldal nézetét vagy a várt egyéni eseményt, próbálja meg növelni a vizualizáció időtartományát a **szerkesztési** menüben.
* Győződjön meg arról, hogy a várt oldal nézet, egyéni esemény vagy kivétel úgy van beállítva, hogy az Application Insights SDK gyűjtsön a hely forráskódjában. [További információ az egyéni események összegyűjtéséről.](./api-custom-events-metrics.md)

Ha további lépéseket szeretne látni a vizualizációban, használja az **előző lépéseket** és a **következő lépések** legördülő listát a vizualizáció felett.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Egy oldal vagy szolgáltatás meglátogatása után hová mennek a felhasználók, és mire kattintanak?

![A Felhasználókövetés használatával megtudhatja, hogy a felhasználók hol kattintanak](./media/usage-flows/one-step.png)

Ha a kezdeti esemény egy oldal nézet, a vizualizáció első oszlopa ("1. lépés") egy gyors módszer annak megismerésére, hogy a felhasználók Mikor voltak azonnal meglátogatva az oldalt. Próbálja meg megnyitni a webhelyet az Felhasználókövetés vizualizáció melletti ablakban. Hasonlítsa össze elvárásaival, hogy a felhasználók hogyan használják a lapot az "1. lépés" oszlopban szereplő események listájára. Gyakran előfordul, hogy a lap egyik felhasználói felületi eleme az oldal leggyakrabban használt része. Nagyszerű kiindulási pontként használható a webhelyének fejlesztése terén.

Ha a kezdeti esemény egy egyéni esemény, az első oszlop azt mutatja, hogy a felhasználók mit tettek a művelet végrehajtása után. Akárcsak az oldalletöltések esetében, gondolja át, hogy a felhasználók megfigyelt viselkedése megfelel-e a csapat céljainak és elvárásainak. Ha a kiválasztott kezdeti esemény "hozzáadott elem a bevásárlókocsi számára" (például: "Ugrás a pénztárhoz" és "befejezett vásárlás") nem sokkal később jelenik meg a vizualizációban. Ha a felhasználói viselkedés eltér az elvárásaitól, használja a vizualizációt, hogy megtudja, hogyan kezdik a felhasználók a hely aktuális tervének betöltését.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Hol vannak a felhasználók az Ön webhelyéről leginkább átesett helyek?

Figyelje meg, hogy a **munkamenet véget ért** -e olyan csomópontok, amelyek a vizualizáció egy oszlopában jelennek meg, különösen a folyamat elején. Ez azt jelenti, hogy sok felhasználó valószínűleg a webhelyről, a lapok és a felhasználói felület interakcióinak előző elérési útját követve lett kihasználva. Az adatforgalom időnként várható – miután a vásárlást egy e-kereskedelmi webhelyen elvégezte, például az adatforgalom a tervezési problémák, a gyenge teljesítmény vagy a webhellyel kapcsolatos egyéb problémák jele lehet.

Ne feledje, hogy a **munkamenet-befejező** csomópontok csak a Application Insights erőforrás által gyűjtött telemetria alapulnak. Ha Application Insights nem kap telemetria az egyes felhasználói interakciók esetében, akkor a felhasználók továbbra is felhasználhatták a webhelyét azokon a módokon, amelyeket a Felhasználókövetés eszköz a munkamenet befejezését követően adott meg.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Vannak olyan helyek, ahol a felhasználók újra és újra megismétlik a műveletet?

Megkeresheti a vizualizációban a többi felhasználó által többször megismételhető oldal nézetet vagy egyéni eseményt. Ez általában azt jelenti, hogy a felhasználók ismétlődő műveleteket végeznek a webhelyén. Ha úgy találja, hogy ismétlődést talál, gondolja át a webhely kialakítását, vagy adjon hozzá új funkciókat az ismétlődés csökkentése érdekében. Ha például olyan felhasználókat talál, akik ismétlődő műveleteket végeznek a Table elem minden egyes sorában, a tömeges szerkesztési funkció hozzáadásával.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>A kezdeti esemény az esemény első indításakor jelenik meg egy munkamenetben, vagy amikor megjelenik egy munkamenetben?

A vizualizáció kezdeti eseménye csak azt jelöli, hogy a felhasználó első alkalommal adta meg az adott nézetet vagy az egyéni eseményt egy munkamenet során. Ha a felhasználók többször is elküldhetik a kezdeti eseményt egy munkamenetben, akkor az "1. lépés" oszlop csak azt mutatja meg, hogy a felhasználók hogyan viselkedjenek a kezdeti esemény *első* példánya után, nem minden példánynál.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>A vizualizáció egyes csomópontjai túl magas szintűek. Például egy olyan csomópontot, amely csak azt mondja, hogy "gomb kattintott." Hogyan oszthatom le részletesebb csomópontokra?

Használja a **Szerkesztés** menü **felosztás** lehetőségeit:

1. Válassza ki azt az eseményt, amelyet le szeretne bontani az **esemény** menüjében.
2. Válasszon egy dimenziót a **dimenzió** menüben. Ha például egy "gomb rákattintáskor" nevű eseményre van szüksége, próbálja ki a "gomb neve" nevű egyéni tulajdonságot.

## <a name="next-steps"></a>Következő lépések

* [A használat áttekintése](usage-overview.md)
* [Felhasználók, munkamenetek és események](usage-segmentation.md)
* [Visszatartás](usage-retention.md)
* [Egyéni események hozzáadása az alkalmazáshoz](./api-custom-events-metrics.md)

