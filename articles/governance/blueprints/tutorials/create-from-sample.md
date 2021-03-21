---
title: 'Oktatóanyag: a terv mintája az új környezethez'
description: Ebben az oktatóanyagban egy tervezet-mintát használ egy olyan tervezet-definíció létrehozásához, amely két erőforráscsoportot állít be, és minden egyes szerepkör-hozzárendelést konfigurál.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: aea3760128221d42e092e4ca45200874d54da54a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915557"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Oktatóanyag: környezet létrehozása tervrajz mintából

A minta tervezetek példákat tartalmaznak arra, hogy mit lehet tenni az Azure-tervrajzokkal. Az egyes minták egy adott szándékkal vagy céllal vannak ellátva, de önmagukban nem hozhatnak létre teljes környezetet. A szolgáltatás kiindulópontként szolgál az Azure-tervezetek használatának megkezdéséhez, amely a tartalmazott összetevők, minták és paraméterek különféle kombinációit tartalmazza.

Az alábbi oktatóanyag az **RBAC** terv minta használatával mutatja be az Azure BluePrints szolgáltatás különböző szempontjait. A következő lépéseket tárgyaljuk:

> [!div class="checklist"]
> - Új terv meghatározásának létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez
> - A hozzárendelés üzembe helyezett erőforrásainak vizsgálata
> - A terv hozzárendelésének megszüntetése a zárolások eltávolításához

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szükség van egy Azure-előfizetésre. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-blueprint-definition-from-sample"></a>Terv definíciójának létrehozása mintából

Először is implementálja a terv mintáját. Az importálás egy új tervet hoz létre a környezetben a minta alapján.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg az RBAC tervvel **ellátott erőforráscsoportokat** _más minták_ alatt, és jelölje ki.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet a tervezet mintájának másolatához. Ebben az oktatóanyagban a _Two-RGS-to-role-hozzárendelések_ nevet fogjuk használni.
   - **Definíció helye**: használja a három pontot, és válassza ki azt a felügyeleti csoportot vagy előfizetést, amelybe menteni szeretné a minta másolatát.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a tervmintát alkotó összetevők listáját. Ez a minta két erőforráscsoportot határoz meg, amelyek a _ProdRG_ és a _PreProdRG_ megjelenítendő nevei. Az egyes erőforráscsoportok végső neve és helye a terv hozzárendelése során van beállítva. A _ProdRG_ erőforráscsoport hozzá van rendelve a _közreműködő_ szerepkörhöz, és a _PreProdRG_ erőforráscsoport hozzá van rendelve a _tulajdonosi_ és az _olvasói_ szerepkörökhöz. A definícióban hozzárendelt szerepkörök statikusak, de a szerepkörhöz hozzárendelt felhasználó, alkalmazás vagy csoport a terv hozzárendelése során be van állítva.

1. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

Ezzel a lépéssel a kiválasztott felügyeleti csoportban vagy előfizetésben létrehozhatja a minta tervezet definíciójának másolatát. A mentett terv definíciója úgy van kezelve, mint a semmiből létrehozott tervrajzok. A mintát igény szerint bármikor mentheti a felügyeleti csoportba vagy előfizetésbe. Az egyes példányokat azonban egyedi névvel kell megadni.

Ha megjelenik a **terv mentése sikeres** portál értesítése, lépjen a következő lépésre.

## <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezetében és igényei szerint. Ebben az oktatóanyagban nem teszünk semmilyen változást.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrők segítségével keresse meg a _két RGS-a-role-hozzárendelései_ terv definícióját, majd jelölje ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldali új ablaktáblán adja meg a _1,0_ -as **verziót** a tervezet mintájának másolatában. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint az "első verzió, amelyet az ERŐFORRÁSCSOPORTOK a RBAC Blueprint minta alapján közzétettek." Ezután válassza a lap alján található **Közzététel** lehetőséget.

Ez a lépés lehetővé teszi a terv hozzárendelését egy előfizetéshez. A közzététel után a módosítások továbbra is elvégezhető. A további módosításokhoz új **verzió** értékkel kell közzétenni, hogy nyomon kövessék a különbségeket az azonos terv definíciójának különböző verziói között.

Ha megjelenik a **közzétételi terv definíciója sikeres** portál értesítése, lépjen a következő lépésre.

## <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

Miután a tervezet mintájának **közzététele** sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrők segítségével keresse meg a _két RGS-a-role-hozzárendelései_ terv definícióját, majd jelölje ki.

1. Válassza ki a Tervdefiníció oldal tetején található **Terv hozzárendelése** lehetőséget.

1. Adja meg a tervhozzárendelés paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez létrejön egy hozzárendelés a beírt paraméterekkel.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv definíciójának neve alapján.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../active-directory/managed-identities-azure-resources/overview.md).
       Ebben az oktatóanyagban válassza az _USA 2. keleti_ régióját.
     - **Tervezet definíciójának verziója**: válassza ki a **közzétett** _1,0_ -es verziót a minta-tervezet definíciójának másolatáról.

   - Hozzárendelés zárolása

     Válassza a _csak olvasható_ terv zárolási módot. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszer-hozzárendelési_ beállítást. További információ: [felügyelt identitások](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. Az egyes összetevőknél állítsa be a paraméter értékét az érték oszlopban definiált **értékre** . `{Your ID}`Válassza ki az Azure-beli felhasználói fiókját.

     |Összetevő neve|Összetevő típusa|Paraméter neve|Érték|Leírás|
     |-|-|-|-|-|
     |ProdRG erőforráscsoport|Erőforráscsoport|Name|ProductionRG|Meghatározza az első erőforráscsoport nevét.|
     |ProdRG erőforráscsoport|Erőforráscsoport|Hely|USA 2. nyugati régiója|Megadja az első erőforráscsoport helyét.|
     |Közreműködő|Szerepkör-hozzárendelés|Felhasználó vagy csoport|{Az Ön azonosítója}|Meghatározza, hogy melyik felhasználó vagy csoport adja meg a _közreműködői_ szerepkör-hozzárendelést az első erőforráscsoporthoz.|
     |PreProdRG erőforráscsoport|Erőforráscsoport|Name|PreProductionRG|Meghatározza a második erőforráscsoport nevét.|
     |PreProdRG erőforráscsoport|Erőforráscsoport|Hely|USA nyugati régiója|Beállítja a második erőforráscsoport helyét.|
     |Tulajdonos|Szerepkör-hozzárendelés|Felhasználó vagy csoport|{Az Ön azonosítója}|Meghatározza, hogy melyik felhasználó vagy csoport adja meg a _tulajdonosi_ szerepkör-hozzárendelést a második erőforráscsoporthoz.|
     |Olvasók|Szerepkör-hozzárendelés|Felhasználó vagy csoport|{Az Ön azonosítója}|Meghatározza, hogy melyik felhasználó vagy csoport adja meg az _olvasók_ szerepkör-hozzárendelést a második erőforráscsoporthoz.|

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget.

Ez a lépés telepíti a definiált erőforrásokat, és konfigurálja a kiválasztott **zárolási hozzárendelést**. A tervrajzi zárolások akár 30 percet is igénybe vehetnek.

Ha megjelenik a **terv kiosztásának meghatározása sikeres** portál értesítés, lépjen a következő lépésre.

## <a name="inspect-resources-deployed-by-the-assignment"></a>A hozzárendelés által üzembe helyezett erőforrások vizsgálata

A terv-hozzárendelés létrehozza és nyomon követi a terv definíciójában meghatározott összetevőket. Az erőforrások állapotát a terv hozzárendelése oldalon tekintheti meg, az erőforrásokat pedig közvetlenül is megtekintheti.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza ki a **kijelölt tervrajzok** lapot a bal oldalon. A szűrők segítségével keresse meg a _hozzárendelés-két-RGS-a-role-hozzárendeléseinek terv-_ hozzárendelést, majd jelölje ki.

   Ezen a lapon láthatjuk a hozzárendelés sikerességét, valamint a létrehozott erőforrások listáját a terv zárolási állapotával együtt. Ha a hozzárendelés frissül, a **hozzárendelési művelet** legördülő lista az egyes definíciós verziók telepítésének részleteit jeleníti meg. Minden létrehozott felsorolt erőforrás kiválasztható, és megnyithatja az erőforrások tulajdonságlapját.

1. Válassza ki a **ProductionRG** erőforráscsoportot.

   Azt láthatjuk, hogy az erőforráscsoport neve **ProductionRG** , és nem az összetevő megjelenítendő neve _ProdRG_. Ez a név megegyezik a terv-hozzárendelés során beállított értékkel.

1. Válassza a bal oldalon a **hozzáférés-vezérlés (iam)** lapot, majd a **szerepkör-hozzárendelések** lapot.

   Itt láthatjuk, hogy a fiókja a _közreműködői_ szerepkört adta meg az _erőforrás_ hatókörén. A _hozzárendelés-két-RGS-a-role-hozzárendelések terv-_ hozzárendelés a _tulajdonosi_ szerepkört használja az erőforráscsoport létrehozásához. Ezek az engedélyek a konfigurált Blueprint-zárolásokkal rendelkező erőforrások kezelésére is használhatók.

1. Az Azure Portal-navigációs panelen válassza a **hozzárendelés-két-RGS-a-role-hozzárendelések** lehetőséget egy lap visszalépéséhez, majd válassza ki a **PreProductionRG** erőforráscsoportot.

1. Válassza a bal oldalon a **hozzáférés-vezérlés (iam)** lapot, majd a **szerepkör-hozzárendelések** lapot.

   Itt láthatjuk, hogy a fiókja a _tulajdonosi_ és a _olvasói_ szerepkört is biztosította az _erőforrás_ hatókörén. A terv hozzárendelése a _tulajdonosi_ szerepkörrel is rendelkezik, mint az első erőforráscsoport.

1. Jelölje be a **megtagadási hozzárendelések** lapot.

   A terv-hozzárendelés [megtagadási hozzárendelést](../../../role-based-access-control/deny-assignments.md) hozott létre a központilag telepített erőforráscsoporthoz, hogy kikényszerítse az _írásvédett_ terv zárolási módját. A megtagadási hozzárendelés megakadályozza, hogy valaki megfelelő jogokkal rendelkezik a _szerepkör-hozzárendelések_ lapon bizonyos műveletek elvégzéséhez. A Megtagadás hozzárendelés az _összes rendszerbiztonsági tagra_ hatással van.

1. Jelölje ki a megtagadási hozzárendelést, majd a bal oldalon válassza a **megtagadott engedélyek** lapot.

   A megtagadási hozzárendelés megakadályozza az összes műveletet a **\* *_ és az _* művelet** konfigurációjában, de a **\* /READ** kizárásával lehetővé teszi az olvasási **hozzáférést.**

1. A Azure Portal navigációs listából válassza a **PreProductionRG-hozzáférés-vezérlés (iam)** lehetőséget. Ezután válassza ki az **Áttekintés** lapot a bal oldalon, majd az **erőforráscsoport törlése** gombot. Írja be a _PreProductionRG_ nevet a törlés megerősítéséhez, majd kattintson a **Törlés** gombra a panel alján.

   A portál értesítésének **törlési erőforráscsoport-PreProductionRG nem sikerült** . A hiba azt jelzi, hogy a fióknak nincs engedélye az erőforráscsoport törlésére, a hozzáférés megtagadva a terv-hozzárendeléssel. Ne feledje, hogy a terv hozzárendelése során a _csak olvasható_ terv zárolási módot jelöltük ki. A terv zárolása megakadályozza, hogy egy olyan fiók, amely nem rendelkezik engedéllyel, akár _tulajdonossal_ is, törölheti az erőforrást. További információkat talál a [terv-erőforrások zárolásáról](../concepts/resource-locking.md) szóló cikkben.

Ezek a lépések azt mutatják, hogy az erőforrások definiálva lettek, és a terv zárolása megakadályozta a nemkívánatos törlést, még az engedéllyel rendelkező fiókból is.

## <a name="unassign-the-blueprint"></a>A terv hozzárendelésének megszüntetése

Az utolsó lépés a terv és az üzembe helyezett erőforrások hozzárendelésének eltávolítása.
A hozzárendelés eltávolítása nem távolítja el az üzembe helyezett összetevőket.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza ki a **kijelölt tervrajzok** lapot a bal oldalon. A szűrők segítségével keresse meg a _hozzárendelés-két-RGS-a-role-hozzárendeléseinek terv-_ hozzárendelést, majd jelölje ki.

1. Válassza ki a **tervezet hozzárendelésének megszüntetése** gombot az oldal tetején. Olvassa el a figyelmeztetést a megerősítő párbeszédpanelen, majd kattintson az **OK gombra**.

   Ha a terv-hozzárendelés el lett távolítva, a terv zárolása is törlődik. A létrehozott erőforrásokat újra törölheti egy olyan fiókkal, amely engedélyekkel rendelkezik.

1. Az Azure menüben válassza az **erőforráscsoportok** lehetőséget, majd válassza a **ProductionRG** lehetőséget.

1. Válassza a bal oldalon a **hozzáférés-vezérlés (iam)** lapot, majd a **szerepkör-hozzárendelések** lapot.

Az egyes erőforráscsoportok biztonsága továbbra is a telepített szerepkör-hozzárendelésekkel rendelkezik, de a terv hozzárendelése már nem rendelkezik _tulajdonosi_ hozzáféréssel.

Ha megjelenik a **terv-hozzárendelés eltávolítása sikeres** portál értesítés, lépjen a következő lépésre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült ezzel az Oktatóanyaggal, törölje a következő erőforrásokat:

- Erőforráscsoport _ProductionRG_
- Erőforráscsoport _PreProductionRG_
- Terv definíciója: _Two-RGS-a-role-hozzárendelései_

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy új tervet egy minta definícióból. Ha többet szeretne megtudni az Azure-tervezetekről, folytassa a terv életciklusával foglalkozó cikkel.

> [!div class="nextstepaction"]
> [A terv életciklusának megismerése](../concepts/lifecycle.md)