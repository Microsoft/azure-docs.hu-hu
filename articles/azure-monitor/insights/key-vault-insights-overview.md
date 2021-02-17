---
title: Key Vault figyelése Azure Monitorekkel Key Vaulthoz | Microsoft Docs
description: Ez a cikk a Key Vaultok Azure Monitor ismerteti.
services: azure-monitor
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: 91aed191e3bb165d6690759426a596df39f8c10f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582301"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault"></a>A Key Vault szolgáltatás figyelése a Azure Monitor for Key Vault
A Key Vault Azure Monitor a Key Vault kérelmek, a teljesítmény, a hibák és a késés egységes áttekintésével biztosítja a kulcstartók átfogó figyelését.
Ez a cikk segít megérteni, hogyan lehet bevezetni és testre szabni a Key Vault Azure Monitor élményét.

## <a name="introduction-to-azure-monitor-for-key-vault"></a>A Key Vault Azure Monitor bemutatása

Mielőtt beugrott a felületre, tisztában kell lennie azzal, hogyan mutatja be és jeleníti meg az információkat.
-    A **skálázási perspektívában** a kérelmeken alapuló, a hibák részletezését, valamint a műveletek és a késés áttekintését bemutató pillanatkép-nézet látható.
-   Egy adott kulcstartó **elemzésének részletezése** részletes elemzés elvégzéséhez.
-    **Testreszabható** , ahol megváltoztathatja, hogy mely metrikákat szeretné megtekinteni, módosítani vagy beállítani a korlátozásokhoz illeszkedő küszöbértékeket, és mentheti a saját munkafüzetét. A munkafüzetben található diagramok az Azure-irányítópultokon rögzíthetők.

A Key Vault Azure Monitor a naplók és a metrikák összeadásával globális figyelési megoldást biztosít. Az összes felhasználó hozzáférhet a metrika-alapú figyelési adatokhoz, azonban a naplókon alapuló vizualizációk belefoglalása esetén a felhasználóknak [engedélyezniük kell a Azure Key Vault naplózását](../../key-vault/general/logging.md).

## <a name="view-from-azure-monitor"></a>Nézet Azure Monitor

A Azure Monitor a kérések, a késések és a hibák részleteit megtekintheti az előfizetésében található több kulcstartóból, és segítséget nyújthat a teljesítménnyel kapcsolatos problémák és a szabályozási helyzetek azonosításához.

Ha meg szeretné tekinteni a kulcstartók kihasználtságát és műveleteit az összes előfizetésében, hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/)

2. Válassza a **figyelő** elemet a Azure Portal bal oldali paneljén, majd az áttekintések szakaszban válassza a **kulcstartók** lehetőséget.

![Képernyőkép az áttekintő élményről több gráfmal](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Áttekintő munkafüzet

A kiválasztott előfizetéshez tartozó áttekintő munkafüzetben a táblázat az előfizetésben csoportosított kulcstartók interaktív kulcstartó-metrikáit jeleníti meg. Az eredményeket az alábbi legördülő listából kiválasztott beállítások alapján szűrheti:

* Előfizetések – csak a kulcstárolókat tartalmazó előfizetések jelennek meg.

* Kulcstartók – alapértelmezés szerint legfeljebb 5 kulcstartó van kijelölve. Ha a hatókör-választóban az összes vagy több kulcstárolót választja, a rendszer legfeljebb 200 kulcstárolót ad vissza. Ha például összesen 573 kulcstartó található a három előfizetésen belül, akkor csak 200 tárolók jelennek meg.

* Időtartomány – alapértelmezés szerint az utolsó 24 órában jeleníti meg az adatokat a megfelelő beállítások alapján.

A számláló csempe a legördülő listában a kijelölt előfizetésekben lévő kulcstartók teljes számát összesíti, és azt is jelzi, hogy hány van kiválasztva. A kérelem, a hibák és a késési metrikák jelentésének a munkafüzet oszlopaihoz feltételes színkódolt intenzitástérképei tartoznak. A legmélyebb szín a legmagasabb és a világosabb szín a legalacsonyabb értékeken alapul.

## <a name="failures-workbook"></a>Hibák munkafüzet

Válassza a **hibák** lehetőséget az oldal tetején, és megnyílik a hibák lap. Megjeleníti az API-találatokat, a gyakoriságot az idő függvényében, valamint az egyes válasz kódok mennyiségét.

![Képernyőfelvétel a hibákról munkafüzetről](./media/key-vaults-insights-overview/failures.png)

A munkafüzetben lévő oszlopok feltételes színkódolással vagy intenzitástérképei rendelkeznek, amely a jelentés API-nak kék értékkel való megjelölésére szolgáló mérőszámokat jelenít meg. A legmélyebb szín a legmagasabb és a világosabb szín a legalacsonyabb értékeken alapul.

A munkafüzet megjeleníti a sikereket (2xx), a hitelesítési hibákat (401/403 állapotkódok), a szabályozást (429 állapotkódok) és más hibákat (4xx).

Ha jobban meg szeretné ismerni az egyes állapotkódok által képviselt kódokat, javasoljuk, hogy olvassa el [Azure Key Vault állapot-és reagálási kódok](../../key-vault/general/authentication-requests-and-responses.md)dokumentációját.

## <a name="view-from-a-key-vault-resource"></a>Megtekintés Key Vault erőforrásból

Azure Monitor Key Vault közvetlenül a Key Vaultból való eléréséhez:

1. A Azure Portal válassza a kulcstartók lehetőséget.

2. A listából válassza ki a kulcstartót. A figyelés szakaszban válassza az áttekintések lehetőséget.

Ezek a nézetek a Azure Monitor szintű munkafüzetből származó kulcstartó erőforrásának kiválasztásával is elérhetők.

![Képernyőfelvétel a Key Vault-erőforrásról](./media/key-vaults-insights-overview/key-vault-resource-view.png)

A Key Vault **áttekintő** munkafüzetében számos teljesítménymutató jelenik meg, amelyek segítségével gyorsan elemezheti a következő adatokat:

- Interaktív teljesítménymutatók, amelyek a Key Vault-tranzakciókkal, a késéssel és a rendelkezésre állással kapcsolatos legfontosabb adatokat mutatják.

- A mérőszámok és az állapotfigyelő elemek kiemelik a szolgáltatás rendelkezésre állását, a Key Vault-erőforráshoz tartozó tranzakciók teljes számát és a teljes késést.

A **hibák** vagy **műveletek** bármelyikének kiválasztásával megnyithatja a megfelelő munkafüzeteket.

![A hibák nézet képernyőképe](./media/key-vaults-insights-overview/resource-failures.png)

A hibák munkafüzete lebontja az összes Key Vault-kérelem eredményeit a kiválasztott időkereten belül, és kategorizálja a sikereket (2xx), a hitelesítési hibákat (401/403), a szabályozást (429) és más hibákat.

![A műveletek nézet képernyőképe](./media/key-vaults-insights-overview/operations.png)

Az operatív munkafüzet lehetővé teszi a felhasználók számára az összes tranzakció részletes ismertetését, amelyet az eredmény állapota alapján a legfelső szintű csempével lehet szűrni.

![Képernyőkép, amely az összes tranzakció teljes részleteit tartalmazó műveleti munkafüzetet jeleníti meg.](./media/key-vaults-insights-overview/info.png)

A felhasználók a felső táblában megadott tranzakciótípusok alapján is kihasználhatják a nézeteket, amelyek dinamikusan frissítik az alsó táblázatot, ahol a felhasználók megtekinthetik a teljes művelet részleteit egy előugró környezet ablaktáblán.

>[!NOTE]
> Vegye figyelembe, hogy a felhasználóknak engedélyezniük kell a diagnosztikai beállításokat a munkafüzet megtekintéséhez. Ha többet szeretne megtudni a diagnosztikai beállítások engedélyezéséről, olvassa el a [Azure Key Vault naplózással](../../key-vault/general/logging.md)kapcsolatos további tudnivalókat.

## <a name="pin-and-export"></a>PIN-kód és exportálás

A metrikus szakaszok bármelyikét rögzítheti egy Azure-irányítópultra, ha a szakasz jobb felső sarkában található gombostű ikonra kattint.

A többszörös előfizetés és a kulcstartók áttekintése vagy meghibásodása esetén a munkafüzetek az eredmények Excel-formátumban való exportálását támogatják a gombostű ikon bal oldalán lévő Letöltés ikonra kattintva.

![Képernyőkép a PIN-kód ikon kiválasztásáról](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Key Vault Azure Monitor testreszabása

Ez a szakasz azokat a gyakori forgatókönyveket ismerteti, amelyekkel a munkafüzet szerkeszthető az adatelemzési igények támogatásához:
*  A munkafüzet hatóköre, hogy mindig egy adott előfizetést vagy kulcstartó (ka) t válasszon ki
* A rács metrikáinak módosítása
* A kérelmek küszöbértékének módosítása
* Szín megjelenítésének módosítása

A testreszabásokat a szerkesztési mód engedélyezésével kezdheti meg, ehhez kattintson a **Testreszabás** gombra a felső eszköztáron.

![Képernyőkép a Testreszabás gombról](./media/key-vaults-insights-overview/customize.png)

A testreszabásokat egy egyéni munkafüzetbe menti a rendszer, hogy megakadályozza a közzétett munkafüzet alapértelmezett konfigurációjának felülírását. A munkafüzetek egy erőforráscsoporthoz lesznek mentve, vagy az Ön számára magánjellegű Saját jelentések szakaszban, vagy a megosztott jelentések szakaszban, amely mindenki számára elérhető az erőforráscsoporthoz való hozzáféréssel. Az Egyéni munkafüzet mentése után nyissa meg a munkafüzet-katalógust, és indítsa el.

![Képernyőfelvétel a munkafüzet-gyűjteményről](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Előfizetés vagy kulcstartó meghatározása

A következő lépések végrehajtásával konfigurálhatja a többszörös előfizetést és a Key Vault áttekintését vagy a hibákra vonatkozó munkafüzetek hatókörét egy adott előfizetéshez vagy kulcstartóhoz (k).

1. Válassza a **figyelő** lehetőséget a portálon, majd a bal oldali ablaktáblán válassza a **kulcstartók** lehetőséget.
2. Az **Áttekintés** munkafüzet parancssáv területén válassza a **Szerkesztés** lehetőséget.
3. Válasszon az **előfizetések** legördülő listából egy vagy több olyan előfizetést, amelyet az alapértelmezettként szeretne használni. Ne feledje, hogy a munkafüzet összesen 10 előfizetés kiválasztását támogatja.
4. Válassza ki a **kulcstárolók** legördülő lista egy vagy több olyan fiókját, amelyet alapértelmezettként szeretne használni. Ne feledje, hogy a munkafüzet összesen 200 Storage-fiók kiválasztását támogatja.
5. A parancssorból válassza a **Mentés másként** lehetőséget, hogy mentse a munkafüzet másolatát a testreszabott beállításokkal, majd kattintson a **Szerkesztés kész** lehetőségre az olvasási módba való visszatéréshez.

## <a name="troubleshooting"></a>Hibaelhárítás

Az általános hibaelhárítási útmutatóért tekintse meg a dedikált munkafüzet-alapú információkkal [kapcsolatos hibaelhárítási cikket](troubleshoot-workbooks.md).

Ez a szakasz a Key Vault Azure Monitorének használatakor felmerülő gyakori problémák diagnosztizálását és hibaelhárítását ismerteti. Az alábbi lista segítségével megkeresheti az adott hibához kapcsolódó információkat.

### <a name="resolving-performance-issues-or-failures"></a>Teljesítménnyel kapcsolatos problémák és hibák elhárítása

A Key Vault Azure Monitor azonosított Key vaulttal kapcsolatos problémák elhárításához tekintse meg a [Azure Key Vault dokumentációját](../../key-vault/index.yml).

### <a name="why-can-i-only-see-200-key-vaults"></a>Miért csak a 200 Key vaultot láthatom

A kiválasztható és megtekinthető 200 kulcstartók száma. A kiválasztott előfizetések számától függetlenül a kiválasztott kulcstartók száma 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Miért nem látom az összes előfizetést az előfizetési választóban

Csak azokat az előfizetéseket mutatjuk be, amelyek a kiválasztott előfizetési szűrőből kiválasztott Key vaultokat tartalmaznak, amelyek a Azure Portal fejlécben a "címtár + előfizetés" lehetőségre vannak kiválasztva.

![Az előfizetés-szűrő képernyőképe](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Szeretném módosítani vagy további vizualizációkat hozzáadni Key Vault az információkhoz

Ha módosítani szeretné a módosításokat, válassza a "szerkesztési mód" lehetőséget a munkafüzet módosításához, majd mentse a munkáját új munkafüzetként, amely egy kijelölt előfizetéshez és erőforráscsoporthoz van kötve.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Mi az az idő, amikor a munkafüzetek bármely részét rögzítjük

Kihasználjuk az "automatikus" időkeretet, ezért attól függ, hogy milyen időtartomány van kiválasztva.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Mi az az időtartomány, amikor a munkafüzet bármely része rögzítve van

Az időtartomány az irányítópult beállításaitól függ.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>Mi a teendő, ha más típusú vagy saját vizualizációkat szeretnék látni? Hogyan módosíthatom a Key Vault

Szerkesztheti a meglévő munkafüzetet a szerkesztési mód használatával, majd mentheti a munkáját új munkafüzetként, amely az összes új módosítást tartalmazni fogja.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a forgatókönyvekkel, amelyek támogatják az új és a meglévő jelentések testreszabását, valamint az [interaktív jelentések Azure monitor-munkafüzetekkel való létrehozását](../visualize/workbooks-overview.md)ismertető áttekintést.
