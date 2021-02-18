---
title: Több erőforrás megtekintése az Azure mérőszámok Explorerben
description: Ismerje meg, hogyan jelenítheti meg több erőforrást az Azure metrika-kezelő használatával.
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 176acea34dead929986acb807b465593f9ffda5a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615125"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>Több erőforrás megtekintése az Azure mérőszámok Explorerben

Az erőforrás-hatókör-választó lehetővé teszi, hogy az azonos előfizetésben és régióban található több erőforrás mérőszámait is megtekinthesse. Ez a cikk azt ismerteti, hogyan lehet több erőforrást megtekinteni a Azure Monitor Azure Metrics Explorer szolgáltatásának használatával. 

## <a name="select-a-resource"></a>Erőforrás kiválasztása 

Válassza a **metrikák** lehetőséget a **Azure monitor** menüből vagy az erőforrás menüjének **figyelés** szakaszában. Ezután a hatókör **kiválasztása elemre** kattintva nyissa meg a hatókör-választót. 

A hatókör-választó használatával válassza ki azokat az erőforrásokat, amelyek mérőszámait meg szeretné jeleníteni. A hatókört fel kell tölteni, ha megnyitotta a metrikák Explorert egy erőforrás menüjéből. 

![Az erőforrás hatókör-választójának megnyitását bemutató képernyőkép.](./media/metrics-dynamic-scope/019.png)

## <a name="select-multiple-resources"></a>Több erőforrás kiválasztása 

Néhány erőforrástípus több erőforráson is lekérdezheti a metrikákat. A mérőszámoknak ugyanahhoz az előfizetéshez és helyhez kell esniük. **Az erőforrástípusok menü felső** részén keresse meg ezeket az erőforrásokat.

![Képernyőkép, amely a több erőforrással kompatibilis erőforrások menüjét jeleníti meg.](./media/metrics-dynamic-scope/020.png)

> [!WARNING] 
> A mérőszámok több erőforrás, erőforráscsoport vagy előfizetés alapján történő megjelenítéséhez az előfizetés szintjén kell figyelnie az olvasó engedélyét. További információ: [Azure szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).

Több erőforráshoz tartozó mérőszámok megjelenítéséhez először válassza ki az erőforrás hatókör-választóján belül több erőforrást. 

![A több erőforrás kijelölését bemutató képernyőkép.](./media/metrics-dynamic-scope/021.png)

> [!NOTE]
> A kiválasztott erőforrásoknak ugyanazon az erőforrás-típuson, helyen és előfizetésen belül kell lenniük. Azok az erőforrások, amelyek nem felelnek meg a feltételeknek, nem választhatók. 

Ha végzett, kattintson az **alkalmaz** gombra a beállítások mentéséhez. 

## <a name="select-a-resource-group-or-subscription"></a>Válasszon erőforráscsoportot vagy előfizetést 

> [!WARNING]
> A mérőszámok több erőforrás, erőforráscsoport vagy előfizetés alapján történő megjelenítéséhez az előfizetés szintjén kell figyelnie az olvasó engedélyét. 

Több erőforrással kompatibilis típusok esetén lekérdezheti a metrikákat egy előfizetésben vagy több erőforráscsoport között. Először válasszon ki egy előfizetést, vagy válasszon ki egy vagy több erőforráscsoportot: 

![Képernyőkép, amely bemutatja, hogyan lehet lekérdezni több erőforráscsoport között.](./media/metrics-dynamic-scope/022.png)

Válassza ki az erőforrás típusát és helyét. 

![A kiválasztott erőforráscsoportokat bemutató képernyőkép.](./media/metrics-dynamic-scope/023.png)

A kijelölt hatókörök kibontásával ellenőrizheti, hogy a kiválasztott erőforrások használhatók-e.

![A csoportok kiválasztott erőforrásait bemutató képernyőkép.](./media/metrics-dynamic-scope/024.png)

Amikor befejezte a hatókörök kiválasztását, kattintson az **alkalmaz** gombra. 

## <a name="split-and-filter-by-resource-group-or-resources"></a>Felosztás és szűrés erőforráscsoport vagy erőforrások alapján

Az erőforrások ábrázolása után a felosztás és a szűrés használatával további információkat szerezhet az adatairól. 

A felosztás lehetővé teszi, hogy megjelenítse, hogy a metrika különböző szakaszai hogyan hasonlíthatók össze egymással. Ha például egy mérőszámot több erőforráshoz ábrázol, válassza a **felosztás** az erőforrás-azonosító vagy az erőforráscsoport alapján felosztásra lehetőséget. A felosztás lehetővé teszi egyetlen metrika összehasonlítását több erőforrás vagy erőforráscsoport között.  

Az alábbi diagramon például látható a CPU százalékos aránya kilenc virtuális gépen. Ha erőforrás-azonosító alapján bontja a felosztást, láthatja, hogy a PROCESSZORok százalékos aránya eltér a virtuális gépektől. 

![Képernyőkép, amely bemutatja, hogyan használhatja a felosztást a PROCESSZORok százalékos arányának megtekintéséhez a virtuális gépek között.](./media/metrics-dynamic-scope/026.png)

A felosztással együtt a szűrés használatával csak a megtekinteni kívánt erőforráscsoportok jeleníthetők meg.  Ha például a virtuális gépek százalékos CPU-értékét szeretné megtekinteni egy adott erőforráscsoport esetében, válassza a **szűrő hozzáadása** lehetőséget az erőforráscsoport szerinti szűréshez. 

Ebben a példában a TailspinToysDemo alapján szűrhet. Itt a szűrő eltávolítja az erőforrásokhoz társított metrikákat a keljfeljancsi-ben. 

![Képernyőkép, amely bemutatja, hogyan szűrhető az erőforráscsoport.](./media/metrics-dynamic-scope/027.png)

## <a name="pin-multiple-resource-charts"></a>Több erőforrást tartalmazó diagramok rögzítése 

Az erőforráscsoportok és előfizetések mérőszámait ábrázoló több erőforrásból álló diagramok esetében a felhasználónak az előfizetés szintjén kell figyelnie az *olvasói* engedélyekkel. Győződjön meg arról, hogy az irányítópultok minden olyan felhasználója, amelynél több erőforrást tartalmazó diagramot rögzít, megfelelő engedélyekkel rendelkezik. További információ: [Azure szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).

Ha több erőforrást tartalmazó diagramot szeretne rögzíteni egy irányítópulton, tekintse meg a [rögzítés az irányítópultokon](../essentials/metrics-charts.md#pinning-to-dashboards)című témakört. 

## <a name="next-steps"></a>Következő lépések

* [A metrikák kezelője – problémamegoldás](../essentials/metrics-troubleshoot.md)
* [Az Azure-szolgáltatásokhoz elérhető metrikák listájának megtekintése](../platform/metrics-supported.md)
* [A konfigurált diagramok példáinak megtekintése](../essentials/metric-chart-samples.md)