---
title: ISO 27001 tervrajzi minta üzembe helyezése
description: Üzembe helyezheti az önálló ISO 27001 tervrajzi minta lépéseit, beleértve a Blueprint-összetevők paraméterének részleteit.
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: 6580c4f8bc66238ad45fe3d969caa7a4b3b4ce82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87006806"
---
# <a name="deploy-the-iso-27001-blueprint-sample"></a>Az ISO 27001 tervrajzi minta üzembe helyezése

Az Azure-tervezetek ISO 27001 tervrajzi minta üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg az **ISO 27001** tervrajz mintát _más minták_ alatt, és válassza a **minta használata**lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet az ISO 27001 terv mintájának másolatához.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a tervmintát alkotó összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

## <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás az ISO 27001 szabványból is áthelyezhető.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint az "első verzió, amely az ISO 27001 Blueprint Sample-ből lett közzétéve." Ezután válassza a lap alján található **Közzététel** lehetőséget.

## <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

Miután a tervezet mintájának **közzététele**sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki a Tervdefiníció oldal tetején található **Terv hozzárendelése** lehetőséget.

1. Adja meg a tervhozzárendelés paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez létrejön egy hozzárendelés a beírt paraméterekkel.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Módosítsa igény szerint, vagy hagyja meg az eredetit.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezetének megfelelő tervzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Tervparaméterek

     Az ebben a szakaszban meghatározott paramétereket a tervdefinícióban lévő számos összetevő használja a konzisztencia érdekében.

     - **Erőforrások és erőforráscsoportok engedélyezett helye**: érték, amely az erőforráscsoportok és az erőforrások megengedett helyét jelzi.

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux-VM Scale sets (VMSS)|Szabályzat-hozzárendelés|Log Analytics a Linux VM Scale Sets (VMSS) munkaterülete|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux-VM Scale sets (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken|Szabályzat-hozzárendelés|A Linux rendszerű virtuális gépek Log Analytics munkaterülete|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|\[Előzetes verzió \] : log Analytics ügynök központi telepítése Windows VM Scale sets (VMSS)|Szabályzat-hozzárendelés|Log Analytics munkaterület a Windows VM Scale Setshoz (VMSS)|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes verzió \] : log Analytics ügynök központi telepítése Windows VM Scale sets (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|\[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken|Szabályzat-hozzárendelés|Log Analytics munkaterület a Windows rendszerű virtuális gépekhez|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics közreműködői" engedélyeket (vagy hasonlókat) a szabályzat-hozzárendelés elsődleges AZONOSÍTÓjának.|
|\[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére: \[\]|
|Engedélyezett tárfiók-termékváltozatok|Szabályzat-hozzárendelés|Engedélyezett tárolási SKU-ket tartalmazó lista|A Storage-fiókok számára megadható SKU-ket tartalmazó lista.|
|Engedélyezett virtuálisgép-termékváltozatok|Szabályzat-hozzárendelés|Engedélyezett virtuálisgép-SKU-ket tartalmazó lista|A virtuális gépekhez megadható SKU-ket tartalmazó lista.|
|Tervezet kezdeményezés ISO 27001|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyeknek engedélyezve kell lennie a diagnosztikai naplóknak|A naplózni kívánt erőforrástípusok listája, ha a diagnosztikai napló beállítása nincs engedélyezve. Elfogadható értékek találhatók [Azure monitor diagnosztikai naplók sémái](../../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas)között.|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az ISO 27001 tervrajzi minta üzembe helyezésének lépéseit, tekintse meg az alábbi cikkeket az architektúra és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [ISO 27001 terv – áttekintés](./index.md) 
>  [ISO 27001 terv – vezérlés leképezése](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
