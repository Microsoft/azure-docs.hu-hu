---
title: Erőforrások zárolása a módosítások megelőzése érdekében
description: Az összes felhasználóra és szerepkörre vonatkozó zárolás alkalmazásával megakadályozhatja, hogy a felhasználók frissítsék vagy törölzék az Azure-erőforrásokat.
ms.topic: conceptual
ms.date: 04/07/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 71637318a60e66bf5000de2f564d740cc101cc60
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768722"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Erőforrások zárolása a váratlan módosítások megelőzése érdekében

Rendszergazdaként zárolhat egy előfizetést, erőforráscsoportot vagy erőforrást, így megakadályozhatja, hogy a szervezet más felhasználói véletlenül törölzék vagy módosítsák a kritikus fontosságú erőforrásokat. A zárolás felülbírálja a felhasználó által esetlegesen szükséges engedélyeket.

A zárolási szintet **CanNotDelete** (nem törölhető) vagy **ReadOnly** (csak olvasható) értékre állíthatja be. A portálon a zárolások neve **Törlés,** illetve **Csak** olvasható.

* **A CanNotDelete azt jelenti,** hogy a jogosult felhasználók továbbra is olvashatják és módosíthatják az erőforrásokat, de nem törölhetik az erőforrást.
* **A ReadOnly** azt jelenti, hogy a jogosult felhasználók olvashatják az erőforrásokat, de nem törölhetik vagy frissíthetik az erőforrást. A zárolás alkalmazása hasonló ahhoz, mint amikor az összes jogosult felhasználót az Olvasó szerepkör által megadott engedélyekre **korlátozza.**

## <a name="how-locks-are-applied"></a>A zárolások alkalmazása

Ha egy szülőhatókörre alkalmaz zárolást, az adott hatókörben lévő összes erőforrás ugyanazt a zárolást örökli. Még a később hozzáadt erőforrások is öröklik a zárolást a szülőtől. Az öröklés legszigorúbb zárolása élvez elsőbbséget.

A felügyeleti zárolás a szerepköralapú hozzáférés-vezérléssel szemben minden felhasználóra és szerepkörre érvényes korlátozásokat alkalmaz. További információ a felhasználók és szerepkörök engedélyeinek beállításával kapcsolatban: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC).](../../role-based-access-control/role-assignments-portal.md)

A Resource Manager zárolásai csak a felügyeleti síkon történő műveletekre érvényesek, ezek pedig a `https://management.azure.com` címre küldött műveletek. A zárolások nem korlátozzák, hogy az erőforrások hogyan végzik saját funkcióikat. Az erőforrás változásai korlátozva vannak, de az erőforrás működése nincs korlátozva. Egy logikai kiszolgálón például a ReadOnly SQL Database megakadályozza a kiszolgáló törlését vagy módosítását. Nem akadályozza meg az adatok létrehozását, frissítését vagy törlését a kiszolgálón található adatbázisokban. Az adattranzakciók engedélyezve vannak, mert ezek a műveletek nem lesznek elküldve a `https://management.azure.com` webhelyre.

## <a name="considerations-before-applying-locks"></a>A zárolások alkalmazása előtt megfontolandó szempontok

A zárolások alkalmazása váratlan eredményekhez vezethet, mert egyes műveletek, amelyek látszólag nem módosítják az erőforrást, valójában zárolás által blokkolt műveleteket igényelnek. A zárolások megakadályozzák az olyan műveleteket, amelyekhez POST kérésre van szükség a Azure Resource Manager API-hoz. Néhány gyakori példa a zárolások által blokkolt műveletekre:

* A tárfiókok csak olvasható **zárolása megakadályozza,** hogy a felhasználók listássának tsaják a fiókkulcsokat. Az Azure Storage [List Keys műveletet](/rest/api/storagerp/storageaccounts/listkeys) egy POST-kéréssel kezeli a fiókkulcsok hozzáférésének védelme érdekében, amelyek teljes hozzáférést biztosítanak a tárfiókban található adatokhoz. Ha egy tárfiókhoz csak olvasható zárolás van konfigurálva, a fiókkulcsokkal nem rendelkezik felhasználók Azure AD-beli hitelesítő adatokkal férhetnek hozzá a blob- vagy üzenetsoradatokhoz. A csak olvasható zárolás emellett megakadályozza a tárfiókra vagy adattárolóra (blobtárolóra vagy üzenetsorra) vonatkozó Azure RBAC-szerepkörök hozzárendelését.

* A tárfiók nem törölhető **zárolása** nem akadályozza meg a fiókon belüli adatok törlését vagy módosítását. Ez a zárolástípus csak magát a tárfiókot védi a törléstől, és nem védi a tárfiókon belüli blob-, üzenetsor-, tábla- vagy fájladatokat. 

* A tárfiókok csak  olvasási zárolása nem akadályozza meg a fiókon belüli adatok törlését vagy módosítását. Ez a zárolástípus csak magát a tárfiókot védi a törléstől vagy módosítástól, és nem védi a tárfiókon belüli blob-, üzenetsor-, tábla- vagy fájladatokat. 

* Egy erőforrás csak olvasható **zárolása App Service** megakadályozza, hogy Visual Studio Server Explorer fájlokat jelenítsen meg az erőforráshoz, mivel ez az interakció írási hozzáférést igényel.

* A tervtervet tartalmazó erőforráscsoport írás **App Service** zárolása megakadályozza **a** csomag fel- vagy [leméretezését.](../../app-service/manage-scale-up.md)

* A virtuális gépet tartalmazó  erőforráscsoport írásható zárolása megakadályozza, hogy **minden** felhasználó el tudja indítani vagy újraindítsa a virtuális gépet. Ezekhez a műveletekhez POST kérésre van szükség.

* Egy erőforráscsoport nem  törölhető zárolása megakadályozza, Azure Resource Manager [automatikusan](../templates/deployment-history-deletions.md) töröljön üzemelő példányokat az előzményekből. Ha az előzményekben eléri a 800 üzemelő példányt, az üzembe helyezések sikertelenek lesznek.

* A szolgáltatás által létrehozott  erőforráscsoport nem törölhető **zárolása** Azure Backup a biztonsági mentések meghiúsulnak. A szolgáltatás legfeljebb 18 visszaállítási pontot támogat. Ha zárolva van, a biztonsági mentési szolgáltatás nem tudja megtisztítani a visszaállítási pontokat. További információkért lásd az [Azure-beli](../../backup/backup-azure-vm-backup-faq.yml)virtuális gépek biztonságimentésről való gyakori kérdéseket.

* Az előfizetések csak olvasható zárolása **megakadályozza,** **Azure Advisor** megfelelően tudjanak működni. Az Advisor nem tudja tárolni a lekérdezések eredményeit.

## <a name="who-can-create-or-delete-locks"></a>Ki hozhat létre vagy törölhet zárolásokat?

Felügyeleti zárolások létrehozásához vagy törléséhez hozzáféréssel kell rendelkezik a vagy `Microsoft.Authorization/*` `Microsoft.Authorization/locks/*` műveletekhez. A beépített szerepkörök esetén ezek a műveletek csak a **Tulajdonosi** és a **Felhasználói hozzáférés rendszergazdájának** vannak engedélyezve.

## <a name="managed-applications-and-locks"></a>Felügyelt alkalmazások és zárolások

Egyes Azure-szolgáltatások, például a Azure Databricks felügyelt alkalmazások [használatával](../managed-applications/overview.md) valósítják meg a szolgáltatást. Ebben az esetben a szolgáltatás két erőforráscsoportot hoz létre. Az egyik erőforráscsoport áttekintést nyújt a szolgáltatásról, és nincs zárolva. A másik erőforráscsoport tartalmazza a szolgáltatás infrastruktúráját, és zárolva van.

Ha megpróbálja törölni az infrastruktúra-erőforráscsoportot, hibaüzenetet kap, amely szerint az erőforráscsoport zárolva van. Ha megpróbálja törölni az infrastruktúra-erőforráscsoport zárolását, hibaüzenetet kap, amely szerint a zárolás nem törölhető, mert az egy rendszeralkalmazás tulajdonában van.

Ehelyett törölje a szolgáltatást, amely az infrastruktúra erőforráscsoportját is törli.

Felügyelt alkalmazások esetén válassza ki az üzembe helyezett szolgáltatást.

![Szolgáltatás kiválasztása](./media/lock-resources/select-service.png)

Figyelje meg, hogy a szolgáltatás tartalmaz egy felügyelt **erőforráscsoportra mutató hivatkozást.** Ez az erőforráscsoport tartalmazza az infrastruktúrát, és zárolva van. Közvetlenül nem törölhető.

![Felügyelt csoport megjelenítése](./media/lock-resources/show-managed-group.png)

Ha törölni szeretne mindent a szolgáltatásról, a zárolt infrastruktúra erőforráscsoportját is beleértve, válassza a **Törlés** lehetőséget a szolgáltatáshoz.

![Szolgáltatás törlése](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Zárolások konfigurálása

### <a name="portal"></a>Portál

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>ARM-sablon

Ha egy Azure Resource Manager sablont (ARM-sablont) használ a zárolás üzembe helyezéséhez, tisztában kell lennie a zárolás hatókörét és az üzembe helyezés hatókörét. Ha az üzembe helyezési hatókörben zárolást (például erőforráscsoport vagy előfizetés zárolását) alkalmazni, ne állítsa be a hatókör tulajdonságot. Amikor zárol egy erőforrást az üzembe helyezési hatókörben, állítsa be a scope tulajdonságot.

Az alábbi sablon zárolást alkalmaz arra az erőforráscsoportra, amelybe üzembe van helyezni. Figyelje meg, hogy nincs hatókörtulajdonság a zárolási erőforráson, mert a zárolás hatóköre megegyezik az üzembe helyezés hatókörének. Ezt a sablont az erőforráscsoport szintjén telepítik.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/locks",
            "apiVersion": "2016-09-01",
            "name": "rgLock",
            "properties": {
                "level": "CanNotDelete",
                "notes": "Resource Group should not be deleted."
            }
        }
    ]
}
```

Erőforráscsoport létrehozásához és zárolásához telepítse az alábbi sablont az előfizetés szintjén.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2019-10-01",
            "name": "[parameters('rgName')]",
            "location": "[parameters('rgLocation')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "lockDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/locks",
                            "apiVersion": "2016-09-01",
                            "name": "rgLock",
                            "properties": {
                                "level": "CanNotDelete",
                                "notes": "Resource group and its resources should not be deleted."
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

Amikor zárolást alkalmaz egy **erőforrásra** az erőforráscsoporton belül, adja hozzá a scope tulajdonságot. Állítsa a hatókört az erőforrás nevére a zároláshoz.

Az alábbi példa egy sablont mutat be, amely létrehoz egy App Service-csomag, egy webhely és egy zárolást a webhelyen. A zárolás hatóköre a webhelyre van beállítva.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2020-06-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2020-06-01",
      "name": "[variables('siteName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "siteLock",
      "scope": "[concat('Microsoft.Web/sites/', variables('siteName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Az üzembe helyezett erőforrásokat a Azure PowerShell [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) paranccsal zárolhatja.

Egy erőforrás zárolása érdekében adja meg az erőforrás nevét, az erőforrás típusát és az erőforráscsoport nevét.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Az erőforráscsoport zárolása érdekében adja meg az erőforráscsoport nevét.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

A zárolásról a [Get-AzResourceLock használatával lekért információ.](/powershell/module/az.resources/get-azresourcelock) Az előfizetés zárolásának leához használja a következőt:

```azurepowershell-interactive
Get-AzResourceLock
```

Egy erőforrás összes zárolásának lekért használhatja a következőt:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Az erőforráscsoport összes zárolásának lekért használhatja a következőt:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Egy erőforrás zárolásának törléséhez használja a következőt:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

Erőforráscsoport zárolásának törléséhez használja a következőt:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Azure CLI

Az üzembe helyezett erőforrásokat az az lock create paranccsal zárolhatja az Azure [CLI-val.](/cli/azure/lock#az_lock_create)

Egy erőforrás zárolása érdekében adja meg az erőforrás nevét, az erőforrás típusát és az erőforráscsoport nevét.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Egy erőforráscsoport zárolása érdekében adja meg az erőforráscsoport nevét.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

A zárolásokkal kapcsolatos információkért használja [az az lock list et.](/cli/azure/lock#az_lock_list) Az előfizetésében az összes zárolás lekért használhatja a következőt:

```azurecli
az lock list
```

Egy erőforrás összes zárolásának lekért használhatja a következőt:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Az erőforráscsoport összes zárolásának lekért használhatja a következőt:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Egy erőforrás zárolásának törléséhez használja a következőt:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

Erőforráscsoport zárolásának törléséhez használja a következőt:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>REST API

Az üzembe helyezett erőforrásokat a felügyeleti zárolások REST API [zárolhatja.](/rest/api/resources/managementlocks) A REST API lehetővé teszi zárolások létrehozására és törlésére, valamint a meglévő zárolások információinak lekérését.

Zárolás létrehozásához futtassa a következőt:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

A hatókör lehet előfizetés, erőforráscsoport vagy erőforrás. A zárolás neve legyen bármi, amit a zárolásnak szeretne hívni. Az API-version esetén használja a **2016-09-01- et.**

A kérelembe foglaljon bele egy JSON-objektumot, amely meghatározza a zárolás tulajdonságait.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Következő lépések

* Az erőforrások logikai rendszerezésének megismerése: Címkék használata [az erőforrások rendszerezéséhez.](tag-resources.md)
* Testreszabott szabályzatokkal korlátozásokat és konvenciókat alkalmazhat az előfizetésében. További információ: [Mi az az Azure Policy?](../../governance/policy/overview.md)
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
