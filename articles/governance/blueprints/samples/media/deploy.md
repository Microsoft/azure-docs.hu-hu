---
title: Adathordozó tervének üzembe helyezése minta
description: A Media Blueprint-minta üzembe helyezésének lépései, beleértve a tervrajz-összetevő paraméterének részleteit.
ms.date: 08/13/2020
ms.topic: sample
ms.openlocfilehash: c8ac1726bc5007c1790f5efbc45c72f9712fcdf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88208438"
---
# <a name="deploy-the-media-blueprint-sample"></a>A Media Blueprint minta üzembe helyezése

A Media Blueprint-minta üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg a **Media** Blueprint mintát _más minták_ területen, majd válassza a **minta használata**lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet a tervezet mintájának másolatához.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a tervmintát alkotó összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

## <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a standardtól akár el is helyezhető.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió, amelyet a Media Blueprint-minta tartalmaz." Ezután válassza a lap alján található **Közzététel** lehetőséget.

## <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

Miután a tervezet mintájának **közzététele**sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

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

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken |Szabályzat-hozzárendelés |A Linux rendszerű virtuális gépek Log Analytics munkaterülete |További információ: [log Analytics munkaterület létrehozása a Azure Portalban](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken |Szabályzat-hozzárendelés |Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája |Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére: `[]` |
|\[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken |Szabályzat-hozzárendelés |Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája |Egy üres tömb is felhasználható a nem kötelező paraméterek jelölésére: `[]` |
|\[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken |Szabályzat-hozzárendelés |Log Analytics munkaterület a Windows rendszerű virtuális gépekhez |További információ: [log Analytics munkaterület létrehozása a Azure Portalban](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Előzetes verzió \] : a média vezérlőinek ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Szabályzat-hozzárendelés |Log Analytics munkaterület-azonosító, amelyhez a virtuális gépeket konfigurálni kell |Ez a Log Analytics munkaterület azonosítója (GUID), amelyhez a virtuális gépeket konfigurálni kell. |
|\[Előzetes verzió \] : a média vezérlőinek ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Szabályzat-hozzárendelés |Azon erőforrástípusok listája, amelyeknek engedélyezve kell lennie a diagnosztikai naplóknak |A naplózni kívánt erőforrástípusok listája, ha a diagnosztikai napló beállítása nincs engedélyezve. Elfogadható értékek találhatók [Azure monitor diagnosztikai naplók sémái](../../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas)között. |
|\[Előzetes verzió \] : a média vezérlőinek ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Szabályzat-hozzárendelés |Rendszergazdák csoport |Csoport. Például: `Administrator; myUser1; myUser2` |
|\[Előzetes verzió \] : a média vezérlőinek ellenőrzése és adott virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények támogatásához |Szabályzat-hozzárendelés |A Windows rendszerű virtuális gépek rendszergazdái csoportjának részét képező felhasználók listája |A rendszergazdák helyi csoportba foglalandó tagok pontosvesszővel tagolt listája. Például: `Administrator; myUser1; myUser2` |
|Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon |Szabályzat-hozzárendelés |Hatás |A házirend hatásával kapcsolatos információk a [Azure Policy effektusok megismerése](../../../policy/concepts/effects.md)című témakörben találhatók. |
|Naplózás üzembe helyezése SQL-kiszolgálókon |Szabályzat-hozzárendelés |A megőrzési időtartam napokban megadott értéke (a 0 korlátlan megőrzést jelez) |Megőrzési napok (nem kötelező, _180_ nap, ha nincs megadva) |
|Naplózás üzembe helyezése SQL-kiszolgálókon |Szabályzat-hozzárendelés |Az SQL Server naplózásához használt Storage-fiók erőforráscsoport-neve |A naplózás az adatbázis-eseményeket egy naplóba írja az Azure Storage-fiókban (a Storage-fiók minden régióban létrejön, ahol létrejön egy SQL Server, amelyet az adott régióban lévő összes kiszolgáló közösen használ). Fontos – a naplózás megfelelő működéséhez ne törölje vagy nevezze át az erőforráscsoportot vagy a Storage-fiókokat. |
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése |Szabályzat-hozzárendelés |A hálózati biztonsági csoport diagnosztika Storage-fiókjának előtagja |Ez az előtag a hálózati biztonsági csoport helyével együtt alkotja a létrehozott Storage-fiók nevét. |
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése |Szabályzat-hozzárendelés |A hálózati biztonsági csoport diagnosztikát szolgáló Storage-fiók erőforráscsoport-neve (léteznie kell) |Az az erőforráscsoport, amelyben a Storage-fiók létrejön. Ez az erőforráscsoport már léteznie kell. |

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a Media Sample üzembe helyezésének lépéseit, tekintse meg az alábbi cikkeket az Áttekintés és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [Média-tervezetek – áttekintés](./index.md) 
>  [Adathordozó-tervezetek – vezérlés leképezése](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
