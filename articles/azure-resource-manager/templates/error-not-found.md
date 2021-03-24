---
title: Az erőforrás nem található hibák
description: Ismerteti, Hogyan oldhatók meg a hibák, ha egy erőforrás nem található. A hiba akkor fordulhat elő, ha Azure Resource Manager sablont telepít, vagy a felügyeleti műveletek végrehajtásakor.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b80c32683190167d5c0d6e0a7f75acce8bbdb833
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950875"
---
# <a name="resolve-resource-not-found-errors"></a>Nem található az erőforrás hibáinak elhárítása

Ez a cikk azt a hibát ismerteti, amikor egy erőforrás nem található egy művelet során. Ez a hiba általában az erőforrások telepítésekor jelenik meg. Ez a hiba akkor is megjelenik, ha a felügyeleti feladatok végrehajtásakor a Azure Resource Manager nem találja a szükséges erőforrást. Ha például olyan erőforráshoz próbál címkéket hozzáadni, amely nem létezik, akkor ezt a hibaüzenetet kapja.

## <a name="symptom"></a>Hibajelenség

Két hibakód jelenik meg, amelyek jelzik, hogy az erőforrás nem található. A **NOTFOUND** hiba a következőhöz hasonló eredményt ad vissza:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

A **ResourceNotFound** hiba a következőhöz hasonló eredményt ad vissza:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Ok

A Resource Managernek le kell kérnie egy erőforrás tulajdonságait, de nem találja az erőforrást az előfizetésekben.

## <a name="solution-1---check-resource-properties"></a>1. megoldás – erőforrás-tulajdonságok keresése

Ha ezt a hibaüzenetet egy felügyeleti feladat végrehajtása közben kapja meg, tekintse meg az erőforráshoz megadott értékeket. Az ellenőrzési három érték a következő:

* Erőforrás neve
* Erőforráscsoport neve
* Előfizetés

Ha a PowerShellt vagy az Azure CLI-t használja, ellenőrizze, hogy a parancsot az erőforrást tartalmazó előfizetésben futtatja-e. Az előfizetést [beállíthatja a set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) vagy [az az Account set értékkel](/cli/azure/account#az-account-set). Számos parancs olyan előfizetési paramétert is biztosít, amely lehetővé teszi, hogy az aktuális környezettől eltérő előfizetést adjon meg.

Ha nem sikerül ellenőrizni a tulajdonságokat, jelentkezzen be a [portálra](https://portal.azure.com). Keresse meg a használni kívánt erőforrást, és vizsgálja meg az erőforrás nevét, az erőforráscsoportot és az előfizetést.

## <a name="solution-2---set-dependencies"></a>2. megoldás – függőségek beállítása

Ha ezt a hibaüzenetet kapja a sablon telepítésekor, lehet, hogy hozzá kell adnia egy függőséget. A Resource Manager optimalizálja az üzembe helyezést az erőforrások párhuzamos létrehozásával, ha lehetséges. Ha egy erőforrást egy másik erőforrás után kell telepíteni, akkor a sablon **dependsOn** elemét kell használnia. Egy webalkalmazás telepítésekor például a App Service tervnek léteznie kell. Ha nem adta meg, hogy a webalkalmazás a App Service-csomagtól függ, a Resource Manager mindkét erőforrást egyszerre hozza létre. Hibaüzenet jelenik meg arról, hogy a App Service-csomag erőforrása nem található, mert még nem létezik, amikor a webalkalmazásban tulajdonságot próbál beállítani. Ezt a hibát a webalkalmazás függőségének beállításával megakadályozhatja.

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

Azonban érdemes elkerülni a nem szükséges függőségek beállítását. Ha szükségtelen függőségekkel rendelkezik, akkor meghosszabbítja a telepítés időtartamát azáltal, hogy megakadályozza, hogy azok az erőforrások, amelyek nem függnek egymástól, párhuzamosan legyenek telepítve. Emellett körkörös függőségeket is létrehozhat, amelyek letiltják az üzemelő példányt. A [hivatkozási](template-functions-resource.md#reference) függvény és a [lista *](template-functions-resource.md#list) függvények implicit függőséget hoznak létre a hivatkozott erőforráson, ha az adott erőforrás ugyanabban a sablonban van telepítve, és a neve (nem erőforrás-azonosító) hivatkozik rá. Ezért előfordulhat, hogy több függősége van, mint a **dependsOn** tulajdonságban megadott függőségek. A [resourceId](template-functions-resource.md#resourceid) függvény nem hoz létre implicit függőséget, vagy ellenőrzi, hogy az erőforrás létezik-e. A [hivatkozási](template-functions-resource.md#reference) függvény és a [lista *](template-functions-resource.md#list) függvények nem hoznak létre implicit függőséget, ha az erőforrást az erőforrás-azonosítója hivatkozik rá. Implicit függőség létrehozásához adja meg az ugyanabban a sablonban üzembe helyezett erőforrás nevét.

Ha megtekinti a függőségi problémákat, meg kell ismernie az erőforrás-telepítés sorrendjét. Az üzembe helyezési műveletek sorrendjének megtekintése:

1. Válassza ki az erőforráscsoport üzembe helyezési előzményeit.

   ![telepítési előzmények kiválasztása](./media/error-not-found/select-deployment.png)

2. Válasszon ki egy központi telepítést az előzmények közül, és válassza az **események** lehetőséget.

   ![telepítési események kiválasztása](./media/error-not-found/select-deployment-events.png)

3. Vizsgálja meg az egyes erőforrások eseményeinek sorozatot. Ügyeljen az egyes műveletek állapotára. Az alábbi képen például három, párhuzamosan telepített Storage-fiók látható. Figyelje meg, hogy a három Storage-fiók egyszerre van elindítva.

   ![párhuzamos üzembe helyezés](./media/error-not-found/deployment-events-parallel.png)

   A következő képen három olyan Storage-fiók látható, amelyek nincsenek párhuzamosan telepítve. A második Storage-fiók az első Storage-fióktól függ, és a harmadik Storage-fiók a második Storage-fióktól függ. Az első Storage-fiók elindítva, elfogadva és befejeződött a következő elindítása előtt.

   ![szekvenciális üzembe helyezés](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>3. megoldás – külső erőforrás beolvasása

Sablon telepítésekor és egy másik előfizetésben vagy erőforráscsoporthoz található erőforrás beszerzéséhez használja a [resourceId függvényt](template-functions-resource.md#resourceid). Ez a függvény visszaadja az erőforrás teljes nevét.

Az előfizetés és az erőforráscsoport paramétereinek megadása a resourceId függvényben nem kötelező. Ha nem adja meg őket, alapértelmezés szerint a jelenlegi előfizetést és erőforráscsoportot használják. Ha más erőforráscsoporthoz vagy előfizetéshez tartozó erőforrással dolgozik, győződjön meg arról, hogy megadja ezeket az értékeket.

A következő példa egy másik erőforráscsoport erőforrás-AZONOSÍTÓját kéri le.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>4. megoldás – felügyelt identitás beszerzése az erőforrásból

Ha olyan erőforrást helyez üzembe, amely implicit módon létrehoz egy [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md), meg kell várnia, amíg az erőforrás telepítve lesz, mielőtt beolvassa az értékeket a felügyelt identitáson. Ha átadja a felügyelt identitás nevét a [hivatkozási](template-functions-resource.md#reference) függvénynek, a Resource Manager megkísérli a hivatkozás feloldását az erőforrás és az identitás üzembe helyezése előtt. Ehelyett adja meg annak az erőforrásnak a nevét, amelyre az identitás vonatkozik. Ez a megközelítés biztosítja az erőforrás és a felügyelt identitás üzembe helyezését, mielőtt a Resource Manager feloldja a hivatkozási funkciót.

A Reference függvényben használja az `Full` parancsot az összes tulajdonság beolvasásához, beleértve a felügyelt identitást is.

A minta a következő:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Ne használja a mintát:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> A sablon sikertelen lesz.

Ha például egy virtuális gépre alkalmazott felügyelt identitás elsődleges AZONOSÍTÓját szeretné lekérni, használja a következőt:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Vagy egy virtuálisgép-méretezési csoportra alkalmazott felügyelt identitás bérlői AZONOSÍTÓjának lekéréséhez használja a következőt:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>5. megoldás – függvények keresése

Sablon telepítésekor keresse meg azokat a kifejezéseket, amelyek a [hivatkozási](template-functions-resource.md#reference) vagy a [listkeys műveletének beolvasása](template-functions-resource.md#listkeys) függvényt használják. Az Ön által megadott értékek attól függően változnak, hogy az erőforrás ugyanabban a sablonban, erőforráscsoporthoz és előfizetésben található-e. Győződjön meg arról, hogy megadja a forgatókönyvhöz szükséges paramétereket. Ha az erőforrás egy másik erőforráscsoporthoz esik, adja meg a teljes erőforrás-azonosítót. Ha például egy másik erőforráscsoport Storage-fiókjára szeretne hivatkozni, használja a következőt:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>6. megoldás – erőforrás törlése után

Ha töröl egy erőforrást, akkor előfordulhat, hogy az erőforrás továbbra is megjelenik a portálon, de valójában nem érhető el. Ha kijelöli az erőforrást, hibaüzenet jelenik meg, amely szerint az erőforrás nem található. Frissítse a portált a legújabb nézet beszerzéséhez.

Ha a probléma rövid várakozás után is fennáll, [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/options/).
