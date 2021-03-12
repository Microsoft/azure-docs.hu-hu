---
title: CIS Microsoft Azure alapítványok teljesítményteszt v 1.1.0 Blueprint minta
description: A CIS Microsoft Azure alapítványok benchmark v 1.1.0 Blueprint minta áttekintése. Ennek a tervmintának a segítségével az ügyfelek adott vezérlőket mérhetnek fel.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: b6188d96971329a83bc82042a1f840bd893354eb
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103197807"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v110-blueprint-sample"></a>CIS Microsoft Azure alapítványok teljesítményteszt v 1.1.0 Blueprint minta

A CIS Microsoft Azure alapítványok teljesítményteszt v 1.1.0 tervrajzi mintája az irányítási őr-síneket olyan [Azure Policy](../../policy/overview.md) segítségével biztosítja, amelyek segítenek felmérni az egyes CIS Microsoft Azure alapítványok teljesítményteszt-javaslatait. Ez a terv segít az ügyfeleknek az olyan Azure-beli üzembe helyezett architektúrák alapkészletének üzembe helyezésében, amelyeknek be kell vezetniük a CIS Microsoft Azure Founds benchmark v 1.1.0 javaslatait.

## <a name="recommendation-mapping"></a>Az ajánlások leképezése

A [Azure Policy ajánlás-hozzárendelés](../../policy/samples/cis-azure-1-1-0.md) részletesen ismerteti a tervben szereplő szabályzat-definíciókat, valamint azt, hogy ezek a szabályzat-definíciók hogyan képezhetők le a CIS **-ben Microsoft Azure** founds benchmark v 1.1.0-ben. Architektúrához való hozzárendeléskor a rendszer kiértékeli az erőforrásokat a hozzárendelt szabályzat-definíciókkal való meg nem felelés esetén Azure Policy. További információ: [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Üzembe helyezés

Az Azure-Microsoft Azure tervekkel kapcsolatban az alábbi lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

### <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg a **CIS Microsoft Azure founds benchmark v 1.1.0** Blueprint mintát _más minták_ alatt, és válassza **a minta használata** lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet a CIS Microsoft Azure alapítványok teljesítményteszt-tervezetének mintájának.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a terv minta részét képező összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

### <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a CIS Microsoft Azure Founds benchmark v 1.1.0 ajánlásainak megfelelően áthelyezhető.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió a CIS Microsoft Azure az alapítók teljesítményteszt-tervezet mintája." Ezután válassza a lap alján található **Közzététel** lehetőséget.

### <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

Miután a tervezet mintájának **közzététele** sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki a Tervdefiníció oldal tetején található **Terv hozzárendelése** lehetőséget.

1. Adja meg a tervhozzárendelés paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez létrejön egy hozzárendelés a beírt paraméterekkel.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Módosítsa igény szerint, vagy hagyja meg az eredetit.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezetének megfelelő tervzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

### <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|A CIS Microsoft Azure alapítványok teljesítményteszt v 1.1.0 javaslatainak és a speciális támogató virtuálisgép-bővítmények üzembe helyezésének naplózása|Szabályzat-hozzárendelés|Azon régiók listája, amelyeken engedélyezni kell a Network Watcher|Régiók pontosvesszővel tagolt listája. A régiók teljes listájának megtekintéséhez használja a Get-AzLocation. Pl.: eastus; eastus2|
|A CIS Microsoft Azure alapítványok teljesítményteszt v 1.1.0 javaslatainak és a speciális támogató virtuálisgép-bővítmények üzembe helyezésének naplózása|Szabályzat-hozzárendelés|A használatra jóváhagyott virtuálisgép-bővítmények listája|A bővítmények pontosvesszővel tagolt listája. A virtuálisgép-bővítmények teljes listájának megtekintéséhez használja a Get-AzVMExtensionImage. Pl.: AzureDiskEncryption; IaaSAntimalware|

## <a name="next-steps"></a>Következő lépések

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.