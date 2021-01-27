---
title: 'Rövid útmutató: terv létrehozása a portálon'
description: Ebben a rövid útmutatóban az Azure-tervezeteket használja az összetevők létrehozásához, definiálásához és üzembe helyezéséhez a Azure Portalon keresztül.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 75a4e9deca4859ac8b9f302fcb62ecc1d12dd8c9
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918330"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Rövid útmutató: terv meghatározása és hozzárendelése a portálon

Ha megtudhatja, hogyan hozhat létre és rendelhet hozzá tervrajzokat, meghatározhatja az újrafelhasználható és gyorsan telepíthető konfigurációk fejlesztését Azure Resource Manager sablonok (ARM-sablonok), a házirendek, a biztonság és egyebek alapján. Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure-tervezeteket a tervrajzok létrehozásához, közzétételéhez és a szervezeten belüli hozzárendeléséhez kapcsolódó gyakori feladatok elvégzéséhez. Ezek a feladatok többek között a következők:

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-blueprint"></a>Terv létrehozása

A megfelelőségi szabványminták definiálásának első lépése, hogy összeállítunk egy tervet az elérhető erőforrásokból. Ebben a példában hozzon létre egy **MyBlueprint** nevű új tervet az előfizetés szerepkör-és szabályzat-hozzárendeléseinek konfigurálásához. Ezután vegyen fel egy új erőforráscsoportot, és hozzon létre egy Resource Manager-sablont és egy szerepkör-hozzárendelést az új erőforráscsoporthoz.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldali oldalon válassza a **tervezet-definíciók** elemet, majd a lap tetején kattintson a **+ terv létrehozása** gombra.

   Vagy válassza a **Létrehozás** lehetőséget a **kezdeti lépések** oldalon a terv létrehozásához.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="Képernyőkép a &quot;Create Blueprint&quot; (terv létrehozása) gombra a terv-definíciók oldalon." border="false":::

1. Válassza a **kezdés üres tervezettel** elemet a beépített tervrajzok listájának tetején található kártyán.

1. Adja meg a **tervrajz nevét** , például **MyBlueprint**. (Legfeljebb 48 betűt és számot használhat, de nem tartalmazhat szóközt vagy különleges karaktert). Most hagyja üresen a **terv leírását** .

1. A **definíció helye** mezőben válassza ki a jobb oldali három pontot, válassza ki azt a [felügyeleti csoportot](../management-groups/overview.md) vagy előfizetést, ahol a tervet menteni szeretné, majd válassza a **kiválasztás** lehetőséget.

1. Ellenőrizze, hogy helyesek-e az adatok. A **terv neve** és a **definíció helye** mezők később nem módosíthatók. Ezután válassza a **Tovább:** összetevők elemet az oldal alján, vagy az **összetevők fület az oldal** tetején.

1. Szerepkör-hozzárendelés hozzáadása az előfizetési szinten:

   1. Válassza ki az **előfizetéshez** tartozó **+ összetevő hozzáadása** sort. Megnyílik az összetevő **hozzáadása** ablak a böngésző jobb oldalán.

   1. Válassza ki a **szerepkör-hozzárendelést** az összetevő **típusa mezőben**.

   1. A **szerepkör** területen válassza a **közreműködő** lehetőséget. Hagyja a **felhasználó, alkalmazás vagy csoport hozzáadása** jelölőnégyzetet a dinamikus paramétert jelző jelölőnégyzet bejelölésével.

   1. A **Hozzáadás** gombra kattintva adja hozzá ezt az összetevőt a tervhez.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="Képernyőkép a szerepkör-hozzárendelési összetevőről a terv meghatározásához való hozzáadáshoz." border="false":::

   > [!NOTE]
   > Az összetevők többsége paramétereket is támogat. A terv létrehozásakor egy értékhez rendelt paraméter _statikus paraméter_. Ha a paramétert a terv hozzárendelése során rendeli hozzá, akkor ez egy _dinamikus paraméter_. További információ: [terv paramétereinek](./concepts/parameters.md).

1. Adja hozzá a szabályzat-hozzárendelést az előfizetés szintjén:

   1. A szerepkör-hozzárendelési összetevő alatt válassza a **+ hozzáadási** összetevő elemet.

   1. Válassza ki a **házirend-hozzárendelést** az összetevő **típusaként**.

   1. Módosítsa a **típust** **beépítettre**. A **Search (keresés**) mezőbe írja be a **címkét**.

   1. Módosítsa a szűrést úgy, hogy a **Keresés** megtörténjen. Válassza a **Hozzáfűzés címkét és az alapértelmezett értéket az erőforráscsoportok** elemnél.

   1. A **Hozzáadás** gombra kattintva adja hozzá ezt az összetevőt a tervhez.

1. Válassza ki a szabályzat **-hozzárendelés hozzáfűzése címke sorát és az alapértelmezett értékét az erőforráscsoportok** elemnél.

1. Megnyílik az ablak, amelyben a tervdefiníció részeként adhatók meg paraméterek az összetevőhöz, és a paraméterek minden ezen a terven alapuló hozzárendeléshez beállíthatók (statikus paraméterek), nem pedig a hozzárendelés során kell megadni azokat (dinamikus paraméterek). Ez a példa dinamikus paramétereket használ a terv hozzárendelése során, ezért hagyja meg az alapértelmezett értékeket, és válassza a **Mégse** gombot.

1. Erőforráscsoport hozzáadása az előfizetési szinten:

   1. Válassza ki az **előfizetéshez** tartozó **+ összetevő hozzáadása** sort.

   1. Válassza az **erőforráscsoport** lehetőséget az összetevő **típusához**.

   1. Hagyja üresen a **lelet megjelenítendő nevét**, az **erőforráscsoport nevét** és a **hely** mezőit, de győződjön meg arról, hogy a jelölőnégyzet be van jelölve minden paraméter tulajdonságnál, hogy dinamikus paramétereket adjon meg.

   1. A **Hozzáadás** gombra kattintva adja hozzá ezt az összetevőt a tervhez.

1. Sablon hozzáadása az erőforráscsoport alatt:

   1. Válassza ki az **ResourceGroup** -bejegyzés alatt lévő **+ összetevő hozzáadása** sort.

   1. Válassza ki **Azure Resource Manager sablont** az összetevő **típusához**, állítsa be az összetevő **megjelenítendő nevét** a **StorageAccount** értékre, és hagyja üresen a **leírást** .

   1. A szerkesztő mezőben a **sablon** lapon illessze be a következő ARM-sablont. A sablon beillesztése után válassza a parameters ( **Paraméterek** ) fület, és figyelje meg, hogy a rendszer a sablon paramétereinek **tárfióktípus** és **helyét** észlelte. Minden paraméter automatikusan észlelhető és fel lett töltve, de dinamikus paraméterként van konfigurálva.

      > [!IMPORTANT]
      > Ha importálja a sablont, győződjön meg arról, hogy a fájl csak JSON formátumú, és nem tartalmaz HTML-kódot. Ha egy GitHub URL-címére mutat, győződjön meg arról, hogy a **nyers** elemet választotta a tiszta JSON-fájl beszerzéséhez, és nem a githubon megjelenítendő HTML-kódot. Ha az importált sablon nem tiszta JSON, hiba történik.

      ```json
      {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
              "storageAccountType": {
                  "type": "string",
                  "defaultValue": "Standard_LRS",
                  "allowedValues": [
                      "Standard_LRS",
                      "Standard_GRS",
                      "Standard_ZRS",
                      "Premium_LRS"
                  ],
                  "metadata": {
                      "description": "Storage Account type"
                  }
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                  "name": "[parameters('storageAccountType')]"
              },
              "kind": "StorageV2",
              "properties": {}
          }],
          "outputs": {
              "storageAccountName": {
                  "type": "string",
                  "value": "[variables('storageAccountName')]"
              }
          }
      }
      ```

   1. Törölje a **tárfióktípus** jelölőnégyzet jelölését, és vegye figyelembe, hogy a legördülő lista csak az ARM-sablonban szereplő értékeket tartalmazza a **allowedValues** alatt. Jelölje be a jelölőnégyzetet, ha vissza szeretne állítani egy dinamikus paramétert.

   1. A **Hozzáadás** gombra kattintva adja hozzá ezt az összetevőt a tervhez.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="Képernyőkép: a Resource Manager-sablon összetevő-beállításai a terv meghatározásához való hozzáadáshoz." border="false":::

1. Kész tervének az alábbi ábrán láthatóhoz kell hasonlítania. Figyelje meg, hogy minden összetevő **_x_ paraméterrel** rendelkezik, amely a **Parameters (paraméterek** ) oszlopban van kitöltve. A dinamikus paraméterek beállítása a terv egyes hozzárendeléseikor történik.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="Képernyőkép egy befejezett terv-definícióról az egyes összetevők típusával." border="false":::

1. Most, hogy minden tervezett összetevő hozzá lett adva, válassza a lap alján található **Piszkozat mentése** elemet.

## <a name="edit-a-blueprint"></a>Terv szerkesztése

A [terv létrehozása](#create-a-blueprint)során nem adta meg a leírást, vagy nem adja hozzá a szerepkör-hozzárendelést az új erőforráscsoporthoz. A lépéseket a következő lépésekkel javíthatja:

1. A bal oldali oldalon válassza a **tervezet-definíciók** lehetőséget.

1. A tervrajzok listájában kattintson a jobb gombbal a korábban létrehozott elemre, majd válassza a **terv szerkesztése** lehetőséget.

1. A **terv leírása** területen adjon meg néhány információt a tervről és az azt alkotó összetevőkről. Ebben az esetben írja be a következőt: **Ez a terv beállítja a címkézési szabályzatot és a szerepkör-hozzárendelést az előfizetésben, létrehoz egy ResourceGroup, és üzembe helyezi az erőforrás-sablont és a szerepkör-hozzárendelést az adott ResourceGroup.**

1. Válassza a **Next (tovább** ) lehetőséget: az oldal alján található összetevők lap vagy az oldal tetején található összetevők lapot. 

1. Vegyen fel egy szerepkör-hozzárendelést az erőforráscsoport alá:

   1. Válassza ki a **+ Add** (összetevő hozzáadása) sort közvetlenül a **ResourceGroup** bejegyzés alatt.

   1. Válassza ki a **szerepkör-hozzárendelést** az összetevő **típusa mezőben**.

   1. A **szerepkör** területen válassza a **tulajdonos** lehetőséget, majd törölje a jelölést a **felhasználó, alkalmazás vagy csoport hozzáadása** mezőben.

   1. Keresse meg és válassza ki a hozzáadni kívánt felhasználót, alkalmazást vagy csoportot. Az összetevő ugyanazt a statikus paraméterkészletet használja a terv minden hozzárendelésében.

   1. A **Hozzáadás** gombra kattintva adja hozzá ezt az összetevőt a tervhez.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="Képernyőkép – a második szerepkör-hozzárendelési összetevő lehetőségei a terv meghatározásához való hozzáadáshoz." border="false":::

1. Kész tervének az alábbi ábrán láthatóhoz kell hasonlítania. Figyelje meg, hogy az újonnan hozzáadott szerepkör-hozzárendelés **1 – 1 paraméterrel van feltöltve**. Ez azt jelenti, hogy ez egy statikus paraméter.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="A második befejezett terv definíciójának képernyőképe a további szerepkör-hozzárendelési összetevővel." border="false":::

1. Válassza a **Piszkozat mentése** most, hogy frissítve lett.

## <a name="publish-a-blueprint"></a>Terv közzététele

Most, hogy minden tervezett összetevő hozzá lett adva a tervhez, itt az idő közzétenni azt.
A közzététel lehetővé teszi, hogy a terv elérhető legyen az előfizetéshez rendelve.

1. A bal oldali oldalon válassza a **tervezet-definíciók** lehetőséget.

1. A tervrajzok listájában kattintson a jobb gombbal a korábban létrehozott elemre, majd válassza a **terv közzététele** lehetőséget.

1. A megnyíló panelen adjon meg egy olyan **verziót** (betűket, számokat és kötőjeleket, amelyek legfeljebb 20 karakter hosszúak), például **v1**. Szükség esetén szöveget is megadhat a **módosítási megjegyzésekben**, például az **első közzétételt**.

1. Válassza a **Közzététel** lehetőséget az oldal alján.

## <a name="assign-a-blueprint"></a>Terv hozzárendelése

A terv közzététele után hozzárendelhető egy előfizetéshez. Rendelje hozzá a létrehozott tervet a felügyeleti csoport hierarchiájában lévő egyik előfizetéshez. Ha a terv egy előfizetésre lett mentve, akkor csak az adott előfizetéshez rendelhető hozzá.

1. A bal oldali oldalon válassza a **tervezet-definíciók** lehetőséget.

1. A tervrajzok listájában kattintson a jobb gombbal a korábban létrehozott elemre (vagy válassza a három pontot), majd válassza a **terv kiosztása** elemet.

1. A **terv kiosztása** lap **előfizetés** legördülő listájában válassza ki azokat az előfizetéseket, amelyekre a tervet telepíteni szeretné.

   Ha az [Azure-számlázás](../../cost-management-billing/index.yml)által támogatott nagyvállalati ajánlatok érhetők el, akkor a **Create New** link az **előfizetés** mezőben aktiválva lesz. Kövesse az alábbi lépéseket:

   1. Válassza az **új létrehozása** hivatkozást, és hozzon létre egy új előfizetést a meglévők kiválasztása helyett.

   1. Adja meg az új előfizetés **megjelenítendő nevét** .

   1. Válassza ki a rendelkezésre álló **ajánlatot** a legördülő listából.

   1. A három pont használatával válassza ki azt a [felügyeleti csoportot](../management-groups/overview.md) , amelyhez az előfizetés gyermeke lesz.

   1. A lap alján kattintson a **Létrehozás** gombra.

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="Képernyőkép az előfizetés létrehozása ablakról és az új előfizetés lehetőségeiről." border="false":::

      > [!IMPORTANT]
      > Az új előfizetés létrehozása közvetlenül a **Létrehozás** gombra kattintva történik.

   > [!NOTE]
   > Minden kiválasztott előfizetéshez létrejön egy hozzárendelés. Egy adott előfizetés-hozzárendelést később is módosíthat, anélkül, hogy a kijelölt előfizetések hátralévő részében módosításokat kellene végeznie.

1. A **hozzárendelés neve mezőben** adjon meg egy egyedi nevet ehhez a hozzárendeléshez.

1. A **hely** mezőben válasszon ki egy régiót a-ben létrehozandó felügyelt identitás és előfizetés központi telepítési objektumához. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalókért lásd [az Azure-erőforrások felügyelt identitásait](../../active-directory/managed-identities-azure-resources/overview.md).

1. Hagyja meg a **terv definíciójának verziószámát** a **közzétett** verziók kiválasztásával a **v1** bejegyzésben. (Az alapértelmezett érték a legutóbb közzétett verzió.)

1. A **Hozzárendelés zárolása** mezőben hagyja meg az alapértelmezett **Nem zárolandó** értéket. További információ: [tervrajzok erőforrás-zárolása](./concepts/resource-locking.md).

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="Képernyőkép a tervrajz-hozzárendelés zárolási hozzárendeléséről és a felügyelt identitási lehetőségekről." border="false":::

1. A **felügyelt identitás** területen hagyja meg a **hozzárendelt rendszer** alapértelmezett beállítását.

1. A **[Felhasználó, csoport vagy alkalmazás neve] : Közreműködő** előfizetés-szintű szerepkör-hozzárendeléshez keressen meg vagy jelöljön ki egy felhasználót, alkalmazást vagy csoportot.

1. Az előfizetési szint házirendjének hozzárendeléséhez állítsa a **címke nevét** a **CostCenter** értékre, a **címke értékét** pedig a következőre: **ContosoIT**.

1. A **ResourceGroup** adja meg a **StorageAccount** **nevét** és az **USA 2. keleti** **régiójának helyét** a legördülő listából.

   > [!NOTE]
   > Minden olyan összetevő esetében, amelyet az erőforráscsoport alatt hozzáadott a terv meghatározása során, a rendszer behúzza az összetevőt úgy, hogy az a-vel üzembe helyezni kívánt erőforráscsoporthoz vagy objektumhoz legyen igazítva.
   > Azok az összetevők, amelyek nem rendelkeznek paraméterekkel, vagy nincsenek definiálva paraméterek a hozzárendeléshez, csak a környezetfüggő információkra vannak felsorolva.

1. Az ARM-sablon **StorageAccount** válassza a **Standard_GRS** lehetőséget a **tárfióktípus** paraméternél.

1. Olvassa el az oldal alján található információs mezőt, majd válassza a **hozzárendelés** lehetőséget.

## <a name="track-deployment-of-a-blueprint"></a>Terv üzembe helyezésének nyomon követése

Miután egy terv hozzá lett rendelve egy vagy több előfizetéshez, két dolog történik:

- A terv hozzá lesz adva az egyes előfizetésekhez **rendelt tervrajzok** lapjához.
- A terv által meghatározott összes összetevő üzembe helyezésének folyamata megkezdődik.

Most, hogy a terv hozzá lett rendelve egy előfizetéshez, ellenőrizze az üzemelő példány állapotát:

1. Válassza ki a **kijelölt tervrajzokat** a bal oldali oldalról.

1. A tervrajzok listájában kattintson a jobb gombbal a korábban hozzárendelt elemre, és válassza a **hozzárendelés részleteinek megtekintése** lehetőséget.

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="Képernyőfelvétel a terv-hozzárendelés helyi menüjéről a &quot;hozzárendelés részleteinek megtekintése&quot; lehetőség kiválasztásával." border="false":::

1. A **terv-hozzárendelés** lapon ellenőrizze, hogy az összes összetevő telepítése sikeres volt-e, és hogy nem történt-e hiba a telepítés során. Ha hiba történt, tekintse meg a [tervekkel kapcsolatos hibaelhárítási](./troubleshoot/general.md) lépéseket, amelyek alapján megállapíthatja, hogy mi volt a probléma.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="unassign-a-blueprint"></a>Terv hozzárendelésének megszüntetése

Ha már nincs szüksége terv-hozzárendelésre, távolítsa el az előfizetésből. Lehetséges, hogy a tervet egy újabb terv váltotta fel, amely frissített mintákat, szabályzatokat és terveket tartalmaz. Az egyes tervek eltávolításakor az adott tervek keretében hozzárendelt összetevők megmaradnak. A tervek hozzárendelését az alábbi lépések szerint szüntetheti meg:

1. Válassza ki a **kijelölt tervrajzokat** a bal oldali oldalról.

1. A tervrajzok listájában válassza ki azt a tervet, amelynek hozzárendelését meg szeretné adni. Ezután válassza ki a **terv hozzárendelésének megszüntetése** gombot az oldal tetején.

1. Olvassa el a megerősítő üzenetet, majd kattintson **az OK gombra**.

### <a name="delete-a-blueprint"></a>Terv törlése

1. A bal oldali oldalon válassza a **tervezet-definíciók** lehetőséget.

1. Kattintson a jobb gombbal a törölni kívánt tervre, majd válassza a **terv törlése** lehetőséget. Ezután válassza az **Igen** lehetőséget a megerősítő párbeszédpanelen.

> [!NOTE]
> A terv törlése ebben a metódusban a kiválasztott terv összes közzétett verzióját is törli.
> Egyetlen verzió törléséhez nyissa meg a tervet, válassza a **közzétett verziók** lapot, válassza ki a törölni kívánt verziót, majd válassza a **verzió törlése** lehetőséget. Emellett nem törölheti a tervet, amíg a terv definíciójának összes hozzárendelését törölni nem.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott, hozzárendelt és eltávolított egy tervet Azure Portalsal. Ha többet szeretne megtudni az Azure-tervezetekről, folytassa a terv életciklusával foglalkozó cikkel.

> [!div class="nextstepaction"]
> [A terv életciklusának megismerése](./concepts/lifecycle.md)
