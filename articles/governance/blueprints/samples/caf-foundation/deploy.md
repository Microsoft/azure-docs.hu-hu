---
title: A CAF Foundation Blueprint minta üzembe helyezése
description: A CAF Foundation Blueprint-minta üzembe helyezésének lépései, beleértve a Blueprint-összetevők paraméterének részleteit.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: a8d41364451bf2ebf1e48b90fad74f4b24c804e1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470149"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Az Azure Foundation Blueprint minta Microsoft Cloud bevezetési keretrendszerének üzembe helyezése

Az Azure (CAF) Foundation tervhez készült Microsoft Cloud bevezetési keretrendszer üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg a **CAF Foundation** tervezet mintát _más minták_ alatt, és válassza a **minta használata** lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet a CAF Foundation tervezet mintájának másolatához.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a tervmintát alkotó összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

## <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a CAF Foundation tervből is áthelyezhető.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió, amelyet a CAF Foundation Blueprint-minta közzétett." Ezután válassza a lap alján található **Közzététel** lehetőséget.

## <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

Miután a tervezet mintájának **közzététele** sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki a Tervdefiníció oldal tetején található **Terv hozzárendelése** lehetőséget.

1. Adja meg a tervhozzárendelés paraméterértékeit:

   - Alapvető beállítások
       - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez létrejön egy hozzárendelés a beírt paraméterekkel.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Módosítsa igény szerint, vagy hagyja meg az eredetit.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást.
     - Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja.
       További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezetének megfelelő tervzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Válassza ki az alapértelmezett, _rendszer által hozzárendelt_ felügyelt identitás vagy a _felhasználó által hozzárendelt_ felügyelt identitás lehetőséget.

   - Tervparaméterek

     Az ebben a szakaszban meghatározott paramétereket a tervdefinícióban lévő számos összetevő használja a konzisztencia érdekében.

     - **Szervezet**: adja meg a szervezet nevét (például contoso) egyedinek kell lennie.
     - **Azure-régió**: válassza ki az Azure-régiót a központi telepítéshez.
     - **Engedélyezett helyszínek**: mely Azure-régiók engedélyezik az erőforrások beépítését?
     
   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Engedélyezett tárfiók-termékváltozatok|Szabályzat-hozzárendelés|Policy_Allowed – StorageAccount – SKU|A diagnosztikai napló tárolási fiókjaiban használt SKU|
|Engedélyezett virtuális gépek SKU-i|Szabályzat-hozzárendelés|Policy_Allowed – VM-SKU|Engedélyezett virtuális gépek SKU-i|
|CostCenter-címke hozzáfűzése erőforráscsoporthoz|Szabályzat-hozzárendelés|Policy_CostCenter_Tag|CostCenter címke és a hozzá tartozó érték hozzáfűzése az erőforráscsoporthoz|
|A környezetében engedélyezni nem kívánt erőforrástípusok|Szabályzat-hozzárendelés|Házirend _Allowed – erőforrás-típusok|Mely Azure-erőforrásokat szeretné engedélyezni a környezetben|
|A Key Vault üzembe helyezése|Resource Manager-sablon|KV-AccessPolicy|**Zárolva** – az Azure ad-csoport vagy a felhasználó engedélyt ad a <Object ID> Key Vault|
|A Log Analytics üzembe helyezése|Resource Manager-sablon|LogAnalytics_DataRetention|**Zárolt** – a napok számát a rendszer megőrzi log Analytics|
|A Log Analytics üzembe helyezése|Resource Manager-sablon|LogAnalytics_Location|**Zárolt** – A munkaterület létrehozásakor használt régió|

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a CAF Foundation Blueprint-minta üzembe helyezésének lépéseit, tekintse meg a következő cikket az architektúra megismeréséhez:

> [!div class="nextstepaction"]
> [A CAF Foundation tervezete – áttekintés](./index.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
