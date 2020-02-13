---
title: Oktatóanyag – Azure-költségvetések létrehozása és kezelése | Microsoft Docs
description: Ez az oktatóanyag segít megtervezni és elszámolni a felhasznált Azure-szolgáltatások költségeit.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/22/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: bb02c4903348a3b8c1d129f02be64109ec0f48eb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76769795"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Oktatóanyag: Azure-költségvetések létrehozása és kezelése

A Cost Management költségvetései segítenek a tervezésben, és elősegítik a vállalaton belüli elszámolhatóságot. A költségvetések segítségével elszámolhat az egy adott időszak alatt használt vagy előfizetett Azure-szolgáltatásokkal. Segítséget nyújtanak mások tájékoztatásához a kiadásaikról a költségek proaktív módon történő kezelése, és a kiadások adott időszakban való alakulásának monitorozása érdekében. A létrehozott költségvetési küszöbértékek túllépése esetén a rendszer csak értesítéseket aktivál. A túllépés egyik erőforrásra sincs hatással, és a felhasználást sem állítja le a rendszer. A költségek elemzése során a költségvetésekkel összehasonlíthatja és nyomon követheti a kiadásokat.

A költségek és a használati adatok jellemzően 12–16 órán belül érhetők el, a költségvetések kiértékelése pedig négy óránként történik. Az e-mail-értesítések általában 12–16 órán belül megérkeznek.

A költségvetések egy adott időszak (hónap, negyedév vagy év) lejártával automatikusan alaphelyzetbe állnak ugyanazzal a költségvetési összeggel, ha jövőbeni lejárati dátumot választ. Mivel a költségvetések ugyanazzal az összeggel állnak alaphelyzetbe, külön költségvetést kell létrehoznia, ha a jövőbeli időszakokban a költségvetés aktuális összegei változnak.

Az oktatóanyagban található példák bemutatják, hogyan hozhat létre és szerkeszthet költségvetést egy Azure Nagyvállalati Szerződéshez (EA) tartozó előfizetés esetén.

A [költségvetések előfizetésekre az Azure Portal használatával történő alkalmazását](https://www.youtube.com/watch?v=UrkHiUx19Po) ismertető videóban bemutatjuk, hogyan hozhat létre költségvetéseket az Azure-ban a kiadások monitorozásához.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Költségvetés létrehozása az Azure Portalon
> * Költségvetés szerkesztése

## <a name="prerequisites"></a>Előfeltételek

A költségvetések számos különféle Azure-fióktípus esetén támogatottak. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](understand-cost-mgt-data.md). A költségvetés megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához.

 Az Azure EA-előfizetések esetében a költségvetések megtekintéséhez olvasási jogosultsággal kell rendelkeznie. A költségvetések létrehozásához és kezeléséhez közreműködői jogosultsággal kell rendelkeznie. Létrehozhat külön költségvetéseket EA-előfizetésekhez és -erőforráscsoportokhoz. EA-számlázási fiókokhoz azonban nem hozhat létre költségvetéseket.

Az alábbi Azure-engedélyek, vagy -hatókörök, támogatottak az egyes előfizetésekben a költségvetések felhasználó vagy csoport általi létrehozásához. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

- Tulajdonos – Költségvetéseket hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő és Cost Management-közreműködő – A saját költségvetéseit hozhatja létre, módosíthatja vagy törölheti. Módosíthatja a mások által létrehozott költségvetések költségvetési összegét.
- Olvasó és Cost Management-olvasó – Megtekintheti azokat a költségvetéseket, amelyekhez engedéllyel rendelkezik.

További információ a Cost Management adataihoz való hozzáférés hozzárendeléséről: [A Cost Management adataihoz való hozzáférés hozzárendelése](../../cost-management/assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-budget-in-the-azure-portal"></a>Költségvetés létrehozása az Azure Portalon

Azure-előfizetési költségvetést havi, negyedéves vagy éves időszakra hozhat létre. Az Azure Portal navigációs tartalma attól függ, hogy előfizetéshez vagy felügyeleti csoporthoz hozza létre a költségvetést.

Költségvetés létrehozásához vagy megtekintéséhez nyissa meg a kívánt hatókört az Azure Portalon, majd válassza ki a **Költségvetések** lehetőséget a menüben. Lépjen például az **Előfizetések** pontra, válasszon ki egy előfizetést a listában, majd válassza a **Költségvetések** menüpontot. A **Hatókör** elemmel másik hatókörre, például felügyeleti csoportra válthat a Költségvetésekben. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

A költségvetések létrehozása után egy egyszerű nézetben tekintheti meg a hozzájuk tartozó jelenlegi kiadásait.

Kattintson a **Hozzáadás** parancsra.

![A példa a már létrehozott költségvetések listáját mutatja](./media/tutorial-acm-create-budgets/budgets01.png)

Győződjön meg róla, hogy a **Költségvetés létrehozása** ablakban látható hatókör helyes. Válassza ki a hozzáadni kívánt szűrőket. A szűrők lehetővé teszik, hogy adott költségekre, például az előfizetésben lévő erőforráscsoportokkal, illetve egy szolgáltatással, például a virtuális gépekkel járó költségekre vonatkozóan hozzon létre költségvetést. A költségelemzések során használt szűrőket alkalmazhatja a költségvetésekre is.

Miután azonosította a hatókört és a szűrőket, írja be a költségvetés nevét. Ezt követően válassza ki a havi, negyedéves vagy éves alaphelyzetbe állítási időtartamot a költségvetéshez. Ez az alaphelyzetbe állítási időtartam határozza meg azt az időszakot, amelyet a költségvetés vizsgál. A költségvetés által kiértékelt költség minden új periódus elején nulláról kezdődik. A negyedéves költségvetés ugyanúgy működik, mint a havi költségvetés. A különbség az, hogy a negyedévre vonatkozó költségvetési összeg egyenlően oszlik meg a negyedév három hónapja között. Az éves költségvetési összeg a naptári év mind a 12 hónapja között oszlik meg egyenlően.

Ha használatalapú, MSDN- vagy Visual Studio-előfizetéssel rendelkezik, a számlázási időszak nem feltétlenül esik egybe a naptári hónapokkal. Az ilyen típusú előfizetésekhez és erőforráscsoportokhoz olyan költségvetést is létrehozhat, amely a számlázási időszakhoz vagy a naptári hónapokhoz igazodik. Ha a számlázási időszakhoz igazított költségvetést szeretne létrehozni, a **Számlázási hónap**, **Számlázási negyedév** vagy **Számlázási év** alaphelyzetbe állítási időtartamot válassza. Ha a naptári hónaphoz igazított költségvetést szeretne létrehozni, a **Havi**, **Negyedéves** vagy **Éves** alaphelyzetbe állítási időtartamot válassza.

Ezután határozza meg a lejárati dátumot, amikortól a költségvetés érvénytelenné válik, és leáll a költségek kiértékelése.

A költségvetésben eddig kiválasztott mezők alapján egy grafikon jelenik meg, amely segítséget nyújt a költségvetéshez használandó küszöbérték kiválasztásában. A javasolt költségvetés a jövőbeli időszakokban potenciálisan felmerülő legmagasabb becsült költségen alapul. Igény esetén módosíthatja a költségvetési összeget.

![Példa költségvetés létrehozására havi költségadatokkal ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

A költségvetés összegének konfigurálása után kattintson a **Következő** lehetőségre a költségvetési riasztások konfigurálásához. A költségvetésekhez meg kell adnia legalább egy költségküszöbértéket (a költségvetés %-ában) és egy hozzá tartozó e-mail-címet. Igény szerint akár öt küszöbértéket és öt e-mail-címet is megadhat egyetlen költségvetéshez. Ha elér egy költségvetési küszöbértéket, az e-mail-értesítések általában 20 órán belül megérkeznek. Az értesítésekkel kapcsolatban a [költségriasztások használatát](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md) ismertető cikkben tekinthet meg további információt. Az alábbi példában a rendszer egy e-mail-riasztást hoz létre a költségvetés 90%-ának az elérésekor. Ha a Budgets API-val hoz létre költségvetést, szerepköröket is rendelhet adott személyekhez, hogy riasztásokat kaphassanak. A szerepkörök személyekhez rendelése nem támogatott az Azure Portalon. További információ a Budgets API-ról: [Budgets API](/rest/api/consumption/budgets).

![Példa a riasztási feltételekkel](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Miután létrehozta a költségvetést, a rendszer megjeleníti annak költségelemzését. A [költségek és kiadások elemzésének](../../cost-management/quick-acm-cost-analysis.md) egyik első lépése a költségvetés és a kiadási tendenciák összehasonlítása.

![Példa költségelemzésben kimutatott költségvetésre és kiadásokra](./media/tutorial-acm-create-budgets/cost-analysis.png)

Az előző példában létrehozott egy költségvetést egy előfizetéshez. Költségvetést azonban erőforráscsoportokhoz is létrehozhat. Ha erőforráscsoporthoz szeretne költségvetést létrehozni, lépjen a **Költségkezelés + számlázás** &gt; **Előfizetések** területre &gt; válasszon ki egy előfizetést > **Erőforráscsoportok** > válasszon ki egy erőforráscsoportot > **Költségvetések** > végül a **Hozzáadás** lehetőséggel adjon hozzá egy költségvetést.

## <a name="costs-in-budget-evaluations"></a>Költségek a költségvetések kiértékelésében

A költségvetési költségek kiértékelései már a fenntartott példányok és vásárlások adatait is tartalmazzák. Ha a díjakat fizetnie kell, riasztásokat kaphat, amikor a díjakat a rendszer beépíti a kiértékelésekbe. Javasoljuk, hogy jelentkezzen be az [Azure Portalra](https://portal.azure.com), és ellenőrizze, hogy a költségvetés küszöbértékei az új költségeket is figyelembe véve, megfelelően lettek-e konfigurálva. Az Azure-ban számlázott díjak nem változnak. A költségvetések kiértékelése a költségek teljesebb köre alapján történik. Ha a díjakat nem kell fizetnie, a költségvetés működése változatlan marad.

Ha úgy szeretné szűrni az új költségeket, hogy a költségvetések kiértékelése csak az első fél Azure-használati díjai alapján történjen, adja hozzá a következő szűrőket a költségvetéshez:

- Közzétevő típusa: Azure
- Díj típusa: Használat

A költségvetési költségek kiértékelése a tényleges költségek alapján történik. Nem tartalmazzák az amortizációt. A költségvetésekben elérhető szűrési lehetőségekkel kapcsolatban a [Csoportosítási és szűrési lehetőségek ismertetése](quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) című szakaszban tekinthet meg további információt.


## <a name="trigger-an-action-group"></a>Műveletcsoport aktiválása

Amikor költségvetést hoz létre vagy módosít egy előfizetés vagy erőforráscsoport hatókörére vonatkozóan, konfigurálhatja úgy, hogy meghívjanak egy műveletcsoportot. A műveletcsoport különböző műveleteket hajthat végre a költségvetési küszöbérték elérésekor. A műveletcsoportok jelenleg csak az előfizetés és erőforráscsoport hatókör esetén támogatottak. A műveletcsoportokkal kapcsolatban a [műveletcsoportok Azure Portalon történő létrehozását és kezelését](../../azure-monitor/platform/action-groups.md) ismertető cikkben talál további információt. A költségvetés-alapú automatizálás műveletcsoportok segítségével történő használatáról a [Költségek kezelése az Azure Budgetsszel](../manage/cost-management-budget-scenario.md) című cikkben tekinthet meg további információt.



A műveletcsoportok létrehozásához vagy frissítéséhez kattintson a **Műveletcsoportok kezelése** elemre a költségvetés létrehozásakor vagy szerkesztésekor.

![Példa egy költségvetés létrehozására a Műveletcsoportok kezelése megjelenítéséhez](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Ezután a kattintson a **Műveletcsoport hozzáadása** elemre a műveletcsoport létrehozásához.


![A Műveletcsoport hozzáadása panel képe](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

A műveletcsoport létrehozása után zárja be a panelt a költségvetéshez való visszatéréshez.

Konfigurálja a költségvetést a műveletcsoport használatára egy adott küszöbérték elérése esetén. Legfeljebb öt különböző küszöbérték támogatott.

![Példa egy műveletcsoport beállításaival a riasztás feltételre vonatkozóan](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

A következő példában az 50%, 75% és 100% értékre beállított költségvetés-küszöbértékek láthatók. Mindegyik úgy lett konfigurálva, hogy a megadott műveleteket aktiválja a kijelölt műveletcsoportban.

![Példa a különböző műveletcsoportokkal és a művelettípussal konfigurált riasztási feltételekre](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

A költségvetés műveletcsoportokkal való integrációja csak olyan műveletcsoportok esetén működik, amelyeken le van tiltva a gyakori riasztási séma. A séma letiltásával kapcsolatban a [gyakori riasztási séma engedélyezését](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) ismertető szakaszban tekinthet meg további információt.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Költségvetés létrehozása az Azure Portalon
> * Költségvetés szerkesztése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhatja létre a Cost Management-adatok ismétlődő exportálását.

> [!div class="nextstepaction"]
> [Exportált adatok létrehozása és kezelése](tutorial-export-acm-data.md)
