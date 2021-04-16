---
title: 'Rövid útmutató: Terv létrehozása a portálon'
description: Ebben a rövid útmutatóban a Azure Blueprints hozhat létre, definiálhat és helyezhet üzembe összetevők a Azure Portal.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 94555b1b73fdac7368c9ac96fa682fb09afc83eb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538973"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Rövid útmutató: Terv meghatározása és hozzárendelése a portálon

Amikor megtanulja, hogyan hozhat létre és rendelhet hozzá terveket, gyakran használt mintákat definiálhat az újrahasználható és gyorsan üzembe helyezhető konfigurációk fejlesztéséhez Azure Resource Manager-sablonok (ARM-sablonok), szabályzatok, biztonság stb. alapján. Ebből az oktatóanyagból elsajátíthatja, hogyan használhatja a Azure Blueprints a tervek létrehozásával, közzétételével és hozzárendelésével kapcsolatos gyakori feladatok elvégzésére a szervezeten belül. Ezek a feladatok többek között a következők:

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-blueprint"></a>Terv létrehozása

A megfelelőségi szabványminták definiálásának első lépése, hogy összeállítunk egy tervet az elérhető erőforrásokból. Ebben a példában létrehozunk egy **MyBlueprint** nevű új tervet, amely konfigurálja az előfizetés szerepkör- és szabályzat-hozzárendeléseit. Ezután adjon hozzá egy új erőforráscsoportot, és hozzon létre Resource Manager új erőforráscsoporthoz egy új sablont és szerepkör-hozzárendelést.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A **bal oldali oldalon** válassza a Tervdefiníciók lehetőséget, majd az oldal tetején található **+** Terv létrehozása gombot.

   Vagy válassza **a Létrehozás** lehetőséget az Első lépések **oldalon,** hogy rögtön a terv létrehozására ássa.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="A Tervdefiníciók oldal Terv létrehozása gombjának képernyőképe." border="false":::

1. A **beépített tervek listájának** tetején található kártyán válassza a Kezdés üres tervvel lehetőséget.

1. Adja meg a **terv nevét,** például **MyBlueprint.** (Legfeljebb 48 betűt és számot használhat, de szóközt vagy speciális karaktert nem). A **Terv leírása mezőt hagyja** üresen.

1. A **Definíció helye mezőben** kattintson a jobb oldalon [](../management-groups/overview.md) található három pontra, válassza ki azt a felügyeleti csoportot vagy előfizetést, ahová a tervet menteni szeretné, majd válassza a **Kijelölés lehetőséget.**

1. Ellenőrizze, hogy az adatok helyesek-e. A **Terv neve és** a Definíció **helye** mezőket később nem lehet módosítani. Ezután válassza **a Tovább: Összetevők** lehetőséget az  oldal alján, vagy az Összetevők lapot az oldal tetején.

1. Szerepkör-hozzárendelés hozzáadása az előfizetés szintjén:

   1. Válassza a **+ Összetevő hozzáadása sort** az Előfizetés **alatt.** Megnyílik **az Összetevő** hozzáadása ablak a böngésző jobb oldalán.

   1. Az **Összetevőtípus mezőben válassza** a **Szerepkör-hozzárendelés lehetőséget.**

   1. A **Szerepkör alatt** válassza a Közreműködő **lehetőséget.** Hagyja meg **a Felhasználó, alkalmazás vagy csoport hozzáadása** jelölőnégyzetet, amely dinamikus paramétert jelez.

   1. Válassza **a Hozzáadás lehetőséget** az összetevő tervhez való hozzáadásához.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="Képernyőkép a tervdefinícióhoz hozzáadni kívánt szerepkör-hozzárendelési összetevő beállításairól." border="false":::

   > [!NOTE]
   > Az összetevők többsége paramétereket is támogat. A terv létrehozása során értékhez rendelt paraméter egy _statikus paraméter._ Ha a paraméter hozzárendelése a terv hozzárendelése során történik, az egy _dinamikus paraméter._ További információ: [Tervparaméterek.](./concepts/parameters.md)

1. Szabályzat-hozzárendelés hozzáadása az előfizetés szintjén:

   1. Válassza a **+ Összetevő hozzáadása** sort a szerepkör-hozzárendelési összetevő alatt.

   1. Az **Összetevőtípus mezőben válassza** a **Szabályzat-hozzárendelés lehetőséget.**

   1. Módosítsa **a Típust** **Beépített típusra.** A **Keresésbe írja** be a **következőt: címke.**

   1. Módosítsa a fókuszt **a Keresésről** a szűrés bekövetkeztéhez. Válassza **a Címke hozzáfűzése és alapértelmezett értéke az erőforráscsoportokhoz lehetőséget.**

   1. Válassza **a Hozzáadás** lehetőséget az összetevő tervhez való hozzáadásához.

1. Válassza ki a szabályzat-hozzárendelés Hozzáfűző címke sorát és **alapértelmezett értékét az erőforráscsoportokhoz.**

1. Megnyílik az ablak, amelyben a tervdefiníció részeként adhatók meg paraméterek az összetevőhöz, és a paraméterek minden ezen a terven alapuló hozzárendeléshez beállíthatók (statikus paraméterek), nem pedig a hozzárendelés során kell megadni azokat (dinamikus paraméterek). Ez a példa dinamikus paramétereket használ a terv hozzárendelése során, ezért hagyja meg az alapértelmezett értékeket, és válassza a **Mégse lehetőséget.**

1. Adjon hozzá egy erőforráscsoportot az előfizetés szintjén:

   1. Válassza a **+ Összetevő hozzáadása sort** az Előfizetés **alatt.**

   1. Az **Összetevő típusa mezőben válassza** az **Erőforráscsoport lehetőséget.**

   1. Hagyja **üresen** az Összetevő megjelenítendő  neve, Az **erőforráscsoport** neve és a Hely mezőket, de győződjön meg arról, hogy a jelölőnégyzet be van jelölve minden paramétertulajdonságnál, hogy dinamikus paramétereket hoz létre.

   1. Válassza **a Hozzáadás** lehetőséget az összetevő tervhez való hozzáadásához.

1. Adjon hozzá egy sablont az erőforráscsoportban:

   1. Válassza az **+ Összetevő hozzáadása sort** a **ResourceGroup bejegyzés** alatt.

   1. Válassza **Azure Resource Manager sablont** **az Összetevő** típusa beállításhoz, állítsa az Összetevő **megjelenített** neve beállítását **StorageAccount** névre, és hagyja üresen **a Leírás** mezőt.

   1. A **szerkesztőmező Sablon** lapján illessze be a következő ARM-sablont. A sablon beillesztése után válassza a **Paraméterek** lapot, és figyelje meg, hogy a rendszer a **storageAccountType** és **a location** sablonparamétereket észlelte. A rendszer minden paramétert automatikusan észlel és feltölt, de dinamikus paraméterként van konfigurálva.

      > [!IMPORTANT]
      > Ha importálja a sablont, győződjön meg arról, hogy a fájl csak JSON formátumú, és nem tartalmaz HTML-t. Amikor egy URL-címre mutat a GitHubon, győződjön meg arról, hogy a **RAW** kódot választotta a tiszta JSON-fájlhoz, és nem azt, amely HTML-be van csomagolva a GitHubon való megjelenítéshez. Ha az importált sablon nem tiszta JSON, hiba történik.

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

   1. Törölje a **storageAccountType** jelölőnégyzet jelölését, és vegye figyelembe, hogy a legördülő lista csak az **allowedValues** alatti ARM-sablonban szereplő értékeket tartalmazza. Válassza ki a jelölőnégyzetet, és állítsa vissza dinamikus paraméterre.

   1. Válassza **a Hozzáadás lehetőséget** az összetevő tervhez való hozzáadásához.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="Képernyőkép a Resource Manager tervdefinícióhoz való hozzáadáshoz használható sablon-összetevő beállításairól." border="false":::

1. Kész tervének az alábbi ábrán láthatóhoz kell hasonlítania. Figyelje meg, hogy minden összetevő **_paraméterekből x_** van kitöltve a **Parameters (Paraméterek) oszlopba.** A dinamikus paraméterek beállítása a terv egyes hozzárendeléseikor történik.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="Képernyőkép egy kész tervdefinícióról az egyes összetevőtípusokkal." border="false":::

1. Most, hogy az összes tervezett összetevő hozzá lett adva, válassza a **Lap** alján található Piszkozat mentése lehetőséget.

## <a name="edit-a-blueprint"></a>Terv szerkesztése

A [Terv létrehozása mezőben](#create-a-blueprint)nem adott meg leírást, és nem is adott hozzá szerepkör-hozzárendelést az új erőforráscsoporthoz. Mindkettőt kijavíthatja az alábbi lépésekkel:

1. A **bal oldali oldalon** válassza a Tervdefiníciók lehetőséget.

1. A tervek listájában kattintson a jobb gombbal a korábban létrehozott tervre, és válassza a **Terv szerkesztése lehetőséget.**

1. A **Terv leírása mezőben** adjon meg néhány információt a tervről és az azt alkotó összetevőkről. Ebben az esetben a következőhöz hasonlót adjon meg: Ez a terv címke-szabályzatot és szerepkör-hozzárendelést állít be az előfizetésen, létrehoz egy ResourceGroup csoportot, és üzembe helyez egy erőforrássablont és szerepkör-hozzárendelést az adott **ResourceGroup számára.**

1. Válassza **a Tovább: Összetevők** lehetőséget az  oldal alján, vagy az Összetevők lapot az oldal tetején.

1. Adjon hozzá egy szerepkör-hozzárendelést az erőforráscsoportban:

   1. Válassza a **+ Összetevő hozzáadása** sort közvetlenül a **ResourceGroup bejegyzés** alatt.

   1. Az **Összetevőtípus mezőben válassza** a **Szerepkör-hozzárendelés lehetőséget.**

   1. A **Szerepkör alatt** válassza a **Tulajdonos** lehetőséget, és törölje a jelölést a Felhasználó, alkalmazás vagy csoport **hozzáadása jelölőnégyzetből.**

   1. Keresse meg és válassza ki a hozzáadni kívánt felhasználót, alkalmazást vagy csoportot. Az összetevő ugyanazt a statikus paraméterkészletet használja a terv minden hozzárendelésében.

   1. Válassza **a Hozzáadás lehetőséget** az összetevő tervhez való hozzáadásához.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="Képernyőkép a tervdefinícióhoz hozzáadni kívánt második szerepkör-hozzárendelési összetevő beállításairól." border="false":::

1. Kész tervének az alábbi ábrán láthatóhoz kell hasonlítania. Figyelje meg, hogy az újonnan hozzáadott szerepkör-hozzárendelésben **az 1 paraméterből 1 ki van töltve.** Ez azt jelenti, hogy ez egy statikus paraméter.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="Képernyőkép a második befejezett tervdefinícióról a további szerepkör-hozzárendelési összetevővel." border="false":::

1. A **frissítés után** válassza a Piszkozat mentése lehetőséget.

## <a name="publish-a-blueprint"></a>Terv közzététele

Most, hogy minden tervezett összetevő hozzá lett adva a tervhez, itt az idő közzétenni azt.
A közzététel lehetővé teszi, hogy a terv hozzá legyen rendelve egy előfizetéshez.

1. A **bal oldali oldalon** válassza a Tervdefiníciók lehetőséget.

1. A tervek listájában kattintson a jobb gombbal a korábban létrehozott tervre, és válassza a **Terv közzététele lehetőséget.**

1. A megnyíló panelen adjon meg egy **Verziót** (betűket, számokat és kötőjeleket, amelyek maximális hossza 20 karakter), például: **v1.** Igény szerint szöveget is beírhat a **Változási megjegyzésekbe,** **például: Első közzététel.**

1. Válassza **a lap** alján található Közzététel lehetőséget.

## <a name="assign-a-blueprint"></a>Terv hozzárendelése

A terv közzététele után hozzárendelhető egy előfizetéshez. Rendelje hozzá a létrehozott tervet a felügyeleti csoport hierarchiájának egyik előfizetéséhez. Ha a terv egy előfizetésbe van mentve, csak az előfizetéshez rendelhető hozzá.

1. A **bal oldali oldalon** válassza a Tervdefiníciók lehetőséget.

1. A tervek listájában kattintson a jobb gombbal arra, amelyet korábban létrehozott (vagy kattintson a három pontra), majd válassza a **Terv hozzárendelése lehetőséget.**

1. A **Terv hozzárendelése** lap  Előfizetés legördülő listájában válassza ki azokat az előfizetéseket, amelyekben üzembe szeretné helyezni a tervet.

   Ha az [Azure](../../cost-management-billing/index.yml)Billingben támogatott nagyvállalati ajánlatok érhetők el, az Előfizetés mezőben egy **Új** létrehozása hivatkozás **lesz aktiválva.** Kövesse az alábbi lépéseket:

   1. Az **Új létrehozása hivatkozásra kattintva** új előfizetést hozhat létre a meglévők kiválasztása helyett.

   1. Adja meg az **új** előfizetés megjelenítendő nevét.

   1. Válassza ki az **elérhető ajánlatot** a legördülő listából.

   1. A három ponttal válassza ki azt a felügyeleti [csoportot,](../management-groups/overview.md) amelybe az előfizetés gyermekként fog tartozik.

   1. A **lap** alján válassza a Létrehozás lehetőséget.

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="Képernyőkép az Előfizetés létrehozása ablakról és az új előfizetés beállításairól." border="false":::

      > [!IMPORTANT]
      > Az új előfizetés közvetlenül a Létrehozás kiválasztása után jön **létre.**

   > [!NOTE]
   > Minden kiválasztott előfizetéshez létrejön egy hozzárendelés. Később anélkül módosíthat egyetlen előfizetés-hozzárendelést, hogy a módosításokat a kiválasztott előfizetések többi részére kényszerítenie.

1. A **Hozzárendelés neve mezőben** adjon meg egy egyedi nevet a hozzárendeléshez.

1. A **Hely területen** válassza ki a felügyelt identitás és az előfizetés üzembe helyezési objektumának régióját, ahol létre lesz hozva. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalókért lásd [az Azure-erőforrások felügyelt identitásait](../../active-directory/managed-identities-azure-resources/overview.md).

1. Hagyja meg **a Tervdefiníció** verziója legördülő menü Közzétett verziók beállítását a **v1 bejegyzésben.**  (Az alapértelmezett verzió a legutóbb közzétett verzió.)

1. A **Hozzárendelés zárolása** mezőben hagyja meg az alapértelmezett **Nem zárolandó** értéket. További információ: [Blueprints-erőforrások zárolása.](./concepts/resource-locking.md)

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="Képernyőkép a terv-hozzárendelés zárolási és felügyelt identitási beállításairól." border="false":::

1. A **Felügyelt identitás alatt** hagyja meg a Rendszer által **hozzárendelt alapértelmezett értéket.**

1. A **[Felhasználó, csoport vagy alkalmazás neve] : Közreműködő** előfizetés-szintű szerepkör-hozzárendeléshez keressen meg vagy jelöljön ki egy felhasználót, alkalmazást vagy csoportot.

1. Az előfizetési szintű szabályzat-hozzárendelésnél a Címke **neve** értékeként adja meg **a CostCenter** értéket, a **Címke értéke mezőben** pedig a **ContosoIT értéket.**

1. A **ResourceGroup (Erőforráscsoport)**  alatt a legördülő listából adja meg **a** **StorageAccount** nevet és az USA **2.** keleti régiója helyet.

   > [!NOTE]
   > Az erőforráscsoportban a tervdefiníció során hozzáadott minden összetevő esetén a rendszer behúzással igazítja az összetevőt az üzembe helyező erőforráscsoporthoz vagy objektumhoz.
   > Azok az összetevők, amelyek nem határozzák meg a paramétereket, vagy amelyek hozzárendeléskor nem határoznak meg paramétereket, csak környezeti információkként vannak felsorolva.

1. A **StorageAccount** ARM-sablonban válassza **Standard_GRS** **storageAccountType paraméter** értékét.

1. Olvassa el az oldal alján található információs mezőt, majd válassza a Hozzárendelés **lehetőséget.**

## <a name="track-deployment-of-a-blueprint"></a>Terv üzembe helyezésének nyomon követése

Miután egy terv hozzá lett rendelve egy vagy több előfizetéshez, két dolog történik:

- A terv hozzá lesz adva az **egyes előfizetések Hozzárendelt** tervek oldalához.
- Megkezdődik a tervben meghatározott összes összetevő üzembe helyezésének folyamata.

Most, hogy a terv hozzá lett rendelve egy előfizetéshez, ellenőrizze az üzembe helyezés előrehaladását:

1. A **bal oldali oldalon** válassza a Hozzárendelt tervek lehetőséget.

1. A tervek listájában kattintson a jobb gombbal arra, amelyet korábban hozzárendelt, majd válassza a **Hozzárendelés részleteinek megtekintése lehetőséget.**

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="A terv-hozzárendelés helyi menüjének képernyőképe a kijelölt &quot;Hozzárendelés részleteinek megtekintése&quot; lehetőséggel." border="false":::

1. A Terv **hozzárendelése lapon** ellenőrizze, hogy az összes összetevő üzembe helyezése sikeres volt-e, és hogy nem történt-e hiba az üzembe helyezés során. Ha hiba történt, tekintse meg [a Tervek](./troubleshoot/general.md) hibaelhárítása témakört a hiba megállapításához szükséges lépésekért.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="unassign-a-blueprint"></a>Terv hozzárendelésének megszüntetése

Ha már nincs szüksége terv-hozzárendelésre, távolítsa el az előfizetésből. Előfordulhat, hogy a tervet egy újabb terv váltotta fel frissített mintákkal, szabályzatokkal és kialakításokkal. Az egyes tervek eltávolításakor az adott tervek keretében hozzárendelt összetevők megmaradnak. A tervek hozzárendelését az alábbi lépések szerint szüntetheti meg:

1. A **bal oldali oldalon** válassza a Hozzárendelt tervek lehetőséget.

1. A tervek listájában válassza ki a hozzárendelni kívánt tervet. Ezután válassza **az Oldal** tetején található Terv hozzárendelésének megszökása gombot.

1. Olvassa el a megerősítő üzenetet, majd kattintson az **OK gombra.**

### <a name="delete-a-blueprint"></a>Terv törlése

1. A **bal oldali oldalon** válassza a Tervdefiníciók lehetőséget.

1. Kattintson a jobb gombbal a törölni kívánt tervre, majd válassza a **Terv törlése lehetőséget.** Ezután válassza **az Igen** lehetőséget a megerősítési párbeszédpanelen.

> [!NOTE]
> Ebben a metódusban a terv törlése a kijelölt terv összes közzétett verzióját is törli.
> Egyetlen verzió törléséhez nyissa meg a tervet, válassza a **Közzétett** verziók lapot, válassza ki a törölni kívánt verziót, majd válassza a **Verzió törlése lehetőséget.** Emellett nem törölhet tervet, amíg nem törli a tervdefiníció összes terv-hozzárendelését.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott, hozzárendelt és eltávolított egy tervet a Azure Portal. Ha többet szeretne megtudni a Azure Blueprints, folytassa a terv életciklusával kapcsolatos cikkel.

> [!div class="nextstepaction"]
> [Tudnivalók a tervek életciklusról](./concepts/lifecycle.md)
