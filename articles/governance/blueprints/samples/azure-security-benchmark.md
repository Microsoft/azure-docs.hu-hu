---
title: Azure-biztonsági teljesítményteszt tervmintájának áttekintése
description: Az Azure-biztonsági teljesítményteszt tervmintájának áttekintése. Ennek a tervmintának a segítségével az ügyfelek adott vezérlőket mérhetnek fel.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: 38a50970999965b6a86b8ce8882006c169f2dc5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919239"
---
# <a name="azure-security-benchmark-blueprint-sample"></a>Az Azure Security benchmark tervének mintája

Az Azure Security benchmark Blueprint minta az irányítási őr-síneket olyan [Azure Policy](../../policy/overview.md) segítségével biztosítja, amelyek segítenek az [Azure Security benchmark v1](../../../security/benchmarks/overview.md) -vezérlők értékelésében. Ez a tervminta lehetővé teszi az ügyfelek számára, hogy bármely olyan, az Azure által üzembe helyezett architektúrához üzembe helyezzenek egy alapvető szabályzatkészletet, amelyhez implementálni szeretnék az Azure-biztonsági teljesítményteszt vezérlőit.

## <a name="control-mapping"></a>Vezérlőelem-leképezés

A [Azure Policy-vezérlési leképezés](../../policy/samples/azure-security-benchmark.md) részletesen ismerteti a tervben található szabályzat-definíciókat, valamint azt, hogy ezek a szabályzat-definíciók hogyan képezik le az Azure biztonsági teljesítményteszt **megfelelőségi tartományait** és **vezérlőit** . Architektúrához való hozzárendeléskor a rendszer kiértékeli az erőforrásokat a hozzárendelt szabályzat-definíciókkal való meg nem felelés esetén Azure Policy. További információ: [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Üzembe helyezés

Az Azure-tervezetek Azure Security benchmark Blueprint-minta üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

### <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg az **Azure Security teljesítményteszt v1** tervezetének mintáját _más minták_ alatt, és válassza ki a nevet a minta kiválasztásához.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet az Azure Security teljesítményteszt tervezetének mintája számára.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a terv minta részét képező összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

### <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás elmozdulhat az Azure biztonsági teljesítménytesztekkel kapcsolatos javaslatok segítségével.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint az "első verzió, amely az Azure Security teljesítményteszt tervezetének mintájában megjelent." Ezután válassza a lap alján található **Közzététel** lehetőséget.

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
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|A Windows VM-Rendszergazdák csoportból kizárt felhasználók listája|A rendszergazdák helyi csoportba kizárandó tagok pontosvesszővel tagolt listája. Pl.: rendszergazda; myUser1; myUser2|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Azoknak a felhasználóknak a listája, akiknek szerepelniük kell a Windows VM-rendszergazdák csoportban|A rendszergazdák helyi csoportba foglalandó tagok pontosvesszővel tagolt listája. Pl.: rendszergazda; myUser1; myUser2|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Azoknak a felhasználóknak a listája, amelyeket a Windows VM-rendszergazdák csoportnak *csak* tartalmaznia kell|A rendszergazdák helyi csoportjának várt tagjainak pontosvesszővel tagolt listája. Pl.: rendszergazda; myUser1; myUser2|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Azon régiók listája, amelyeken engedélyezni kell a Network Watcher|A régiók teljes listájának megtekintéséhez használja a Get-AzLocation|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Virtuális hálózat, amelyben a virtuális gépek csatlakozni fognak|Példa:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|A virtuális hálózatok által használandó hálózati átjáró|Példa:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Azon munkaterület-azonosítók listája, amelyekben Log Analytics-ügynököknek csatlakozniuk kell|Azon munkaterület-azonosítók pontosvesszővel tagolt listája, amelyekhez a Log Analytics ügynöknek csatlakoznia kell|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyeknek engedélyezve kell lennie a diagnosztikai naplóknak|A kiválasztott erőforrástípusok diagnosztikai beállításainak naplózása|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|A PHP legújabb verziója|A App Services legújabb támogatott PHP-verziója|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Legújabb Java-verzió|A legújabb támogatott Java-verzió a App Services|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Legújabb Windows Python-verzió|A App Services legújabb támogatott Python-verziója|
|Azure Security teljesítményteszt-javaslatok naplózása és konkrét támogató virtuálisgép-bővítmények üzembe helyezése|Szabályzat-hozzárendelés|Legújabb Linux Python-verzió|A App Services legújabb támogatott Python-verziója|

## <a name="next-steps"></a>Következő lépések

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.