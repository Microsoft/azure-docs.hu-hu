---
title: Az erőforrás nem található hibák
description: Ismerteti, hogyan háríthatja el a hibákat, ha egy erőforrás nem található. Ez a hiba akkor fordulhat elő, ha üzembe helyez egy Azure Resource Manager sablont, vagy felügyeleti műveleteket futtat.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: 5e3a72eaad99721cec9500956179a3ae9d9cf8d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762136"
---
# <a name="resolve-resource-not-found-errors"></a>Az erőforrás nem található hibáinak elhárítása

Ez a cikk azt a hibát ismerteti, amely akkor jelenik meg, ha egy erőforrás nem található egy művelet során. Ez a hiba általában az erőforrások üzembe helyezésekor jelenik meg. Ez a hiba akkor is használhatja, ha felügyeleti feladatokat Azure Resource Manager nem találja a szükséges erőforrást. Ha például nem létező erőforráshoz próbál címkéket hozzáadni, ezt a hibaüzenetet kapja.

## <a name="symptom"></a>Hibajelenség

Két hibakód jelzi, hogy az erőforrás nem található. A **NotFound** hiba a következő eredményhez hasonló eredményt ad vissza:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

A **ResourceNotFound** hiba a következő hasonló eredményt adja vissza:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Ok

Resource Manager erőforrás tulajdonságait kell lekérni, de nem találja az erőforrást az előfizetésében.

## <a name="solution-1---check-resource-properties"></a>1. megoldás – erőforrás-tulajdonságok ellenőrzése

Amikor egy felügyeleti feladat során ezt a hibaüzenetet kapja, ellenőrizze az erőforráshoz megadott értékeket. A három ellenőrizve a következő értékeket kell ellenőrizni:

* Erőforrás neve
* Erőforráscsoport neve
* Előfizetés

Ha PowerShellt vagy Azure CLI-t használ, ellenőrizze, hogy az erőforrást tartalmazó előfizetésben futtatja-e a parancsot. Az előfizetést a [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) vagy [az az account set beállítással módosíthatja.](/cli/azure/account#az_account_set) Számos parancs egy előfizetési paramétert is biztosít, amellyel az aktuális környezettől eltérő előfizetést is megadhat.

Ha nem tudja ellenőrizni a tulajdonságokat, jelentkezzen be a [portálra.](https://portal.azure.com) Keresse meg a használni próbált erőforrást, és vizsgálja meg az erőforrás nevét, az erőforráscsoportot és az előfizetést.

## <a name="solution-2---set-dependencies"></a>2. megoldás – függőségek beállítása

Ha a sablon üzembe helyezésekor ez a hiba jelenik meg, előfordulhat, hogy hozzá kell adni egy függőséget. Resource Manager optimalizálja az üzembe helyezést az erőforrások párhuzamos létrehozásával, amikor csak lehetséges. Ha egy erőforrást egy másik erőforrás után kell üzembe helyezni, a **dependsOn** elemet kell használnia a sablonban. Webalkalmazás üzembe helyezésekor például léteznie kell a App Service tervnek. Ha nem adott meg, hogy a webalkalmazás a App Service-csomagtól függ, Resource Manager mindkét erőforrást egyszerre hozza létre. Hibaüzenetet kap, amely szerint a App Service-csomag erőforrása nem található, mert a webalkalmazás tulajdonságának beállításakor még nem létezik. Ezt a hibát a webalkalmazás függőségének beállításával előzheti meg.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Azonban szeretné elkerülni a szükségtelen függőségek beállítását. Ha szükségtelen függőségekkel rendelkezik, az üzembe helyezés időtartamának meghosszabbítása azzal történik, hogy megakadályozza az egymástól nem függő erőforrások párhuzamos üzembe helyezését. Emellett körkörös függőségeket is létrehozhat, amelyek blokkolják az üzembe helyezést. A [reference](template-functions-resource.md#reference) függvény és a [list*](template-functions-resource.md#list) függvény implicit függőséget hoz létre a hivatkozott erőforrástól, ha az adott erőforrás ugyanabban a sablonban van üzembe állítva, és a neve hivatkozik rá (nem az erőforrás-azonosító). Ezért előfordulhat, hogy több függősége van, mint a **dependsOn tulajdonságban megadottaknak.** A [resourceId](template-functions-resource.md#resourceid) függvény nem hoz létre implicit függőséget, és nem ellenőrzi, hogy az erőforrás létezik-e. A [reference](template-functions-resource.md#reference) függvény [és a list*](template-functions-resource.md#list) függvény nem hoz létre implicit függőséget, ha az erőforrásra az erőforrás-azonosítója hivatkozik. Implicit függőség létrehozásához adja át az ugyanabban a sablonban üzembe helyezett erőforrás nevét.

Ha függőségi problémákat tapasztal, betekintést kell nyernie az erőforrások üzembe helyezésének sorrendjébe. A telepítési műveletek sorrendjének megtekintése:

1. Válassza ki az erőforráscsoport üzembe helyezési előzményeit.

   ![üzembe helyezési előzmények kiválasztása](./media/error-not-found/select-deployment.png)

2. Válasszon ki egy üzemelő példányt az előzmények közül, majd válassza az **Események lehetőséget.**

   ![telepítési események kiválasztása](./media/error-not-found/select-deployment-events.png)

3. Vizsgálja meg az egyes erőforrások eseménysorozatát. Figyelje meg az egyes műveletek állapotát. Az alábbi képen például három párhuzamosan üzembe helyezett tárfiók látható. Figyelje meg, hogy a három tárfiók egyszerre indult el.

   ![párhuzamos üzembe helyezés](./media/error-not-found/deployment-events-parallel.png)

   A következő képen három tárfiók látható, amelyek nincsenek párhuzamosan üzembe helyezni. A második tárfiók az első tárfióktól, a harmadik pedig a második tárfióktól függ. Az első tárfiókot a következő elindulás előtt elindítják, elfogadják és befejezik.

   ![szekvenciális üzembe helyezés](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>3. megoldás – külső erőforrás lekért

Sablon üzembe helyezésekor, ha egy másik előfizetésben vagy erőforráscsoportban található erőforrást kell lekérte, használja a [resourceId függvényt.](template-functions-resource.md#resourceid) Ez a függvény visszatér, és lekérte az erőforrás teljes nevét.

A resourceId függvény előfizetési és erőforráscsoport-paraméterei nem kötelezők. Ha nem adja meg őket, alapértelmezés szerint az aktuális előfizetést és erőforráscsoportot adja meg. Ha egy másik erőforráscsoportban vagy előfizetésben dolgozik egy erőforrással, ügyeljen arra, hogy ezeket az értékeket adja meg.

Az alábbi példa egy másik erőforráscsoportban található erőforrás erőforrás-azonosítóját lekérte.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>4. megoldás – felügyelt identitás lekért erőforrásból

Ha olyan erőforrást helyez üzembe, amely [](../../active-directory/managed-identities-azure-resources/overview.md)implicit módon létrehoz egy felügyelt identitást, meg kell várnia az erőforrás üzembe helyezését, mielőtt értékeket adna le a felügyelt identitáson. Ha a felügyelt identitás nevét [](template-functions-resource.md#reference) adja át a referencia függvénynek, a Resource Manager az erőforrás és az identitás üzembe helyezése előtt megkísérli feloldani a hivatkozást. Ehelyett adja meg annak az erőforrásnak a nevét, amelyre az identitás vonatkozik. Ez a megközelítés biztosítja, hogy az erőforrás és a felügyelt identitás üzembe helyezése a Resource Manager feloldja a referencia-függvényt.

A reference függvényben a használatával `Full` lekérte az összes tulajdonságot, beleértve a felügyelt identitást is.

A minta a következő:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Ne használja a következő mintát:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> A sablon sikertelen lesz.

Például egy virtuális gépre alkalmazott felügyelt identitás rendszerbiztonsági tagazonosítójának lekért használhatja a következőt:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Vagy egy virtuálisgép-méretezési készletre alkalmazott felügyelt identitás bérlőazonosítójának lekértéhez használja a következőt:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>5. megoldás – függvények ellenőrzése

Sablon üzembe helyezésekor keresse meg a [](template-functions-resource.md#reference) hivatkozási vagy [listKeys függvényt](template-functions-resource.md#listkeys) használni kívánt kifejezéseket. A megadott értékek attól függően változnak, hogy az erőforrás ugyanabban a sablonban, erőforráscsoportban és előfizetésben van-e. Ellenőrizze, hogy a forgatókönyvéhez szükséges paraméterértékeket biztosítja-e. Ha az erőforrás egy másik erőforráscsoportban van, adja meg a teljes erőforrás-azonosítót. Ha például egy másik erőforráscsoportban van egy tárfiókra hivatkozni, használja a következőt:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>6. megoldás – az erőforrás törlése után

Amikor töröl egy erőforrást, előfordulhat, hogy egy rövid idő alatt az erőforrás továbbra is megjelenik a portálon, de valójában nem érhető el. Ha kiválasztja az erőforrást, egy hibaüzenet jelenik meg, amely szerint az erőforrás nem található. A legújabb nézet megtekintéséhez frissítse a portált.

Ha a probléma rövid várakozás után is fennáll, forduljon az [ügyfélszolgálathoz.](https://azure.microsoft.com/support/options/)
