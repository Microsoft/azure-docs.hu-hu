---
title: Template deployment what-if
description: A sablon üzembe helyezése előtt határozza meg, hogy milyen változások történnek az erőforrásokon Azure Resource Manager sablont.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tomfitz
ms.openlocfilehash: 7e300f896bb11ed7c77738836f894cff41cc8bf3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781828"
---
# <a name="arm-template-deployment-what-if-operation"></a>ARM-sablon üzembe helyezése what-if művelettel

A Azure Resource Manager (ARM-sablon) üzembe helyezése előtt megtekintheti a változások előnézetét. Azure Resource Manager biztosítja a what-if műveletet, amely lehetővé teszi az erőforrások változásának áttekintését a sablon üzembe helyezése esetén. A what-if művelet nem módosít meglévő erőforrásokat. Ehelyett előre jelzi a megadott sablon üzembe helyezésével járó változásokat.

A what-if műveletet használhatja a Azure PowerShell, az Azure CLI-hez vagy REST API műveletekhez. A what-if az erőforráscsoport, az előfizetés, a felügyeleti csoport és a bérlői szintű üzemelő példányok esetén támogatott.

## <a name="install-azure-powershell-module"></a>Az Azure PowerShell modul telepítése

A what-if PowerShellben való használatának az Az modul **4.2-es** vagy újabb verziójával kell lennie.

A modul telepítéséhez használja a következőt:

```powershell
Install-Module -Name Az -Force
```

További információ a modulok telepítéséről: [Install Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="install-azure-cli-module"></a>Az Azure CLI modul telepítése

A what-if Azure CLI-ben való használatának az Azure CLI 2.14.0-s vagy újabb használatával kell lennie. Ha szükséges, [telepítse az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Eredmények see (Eredmények)

Ha a what-if parancsot használja a PowerShellben vagy az Azure CLI-ban, a kimenet színkódolt eredményeket tartalmaz, amelyek segítenek a különböző típusú módosításokban.

![Resource Manager üzembe helyezési what-if művelet fullresourcepayload és változástípusok](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A szöveges kimenet a következő:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> A what-if művelet nem tudja feloldani a [referencia-függvényt.](template-functions-resource.md#reference) Minden alkalommal, amikor egy tulajdonságot a referencia-függvényt tartalmazó sablonkifejezésre ad meg egy tulajdonságot, a what-if jelentésekben a tulajdonság megváltozik. Ez a viselkedés akkor fordul elő, ha a what-if összehasonlítja a tulajdonság aktuális értékét (például vagy logikai érték esetén) a feloldatlan `true` `false` sablonkifejezéssel. Ezek az értékek nyilvánvalóan nem egyeznek. A sablon üzembe helyezésekor a tulajdonság csak akkor változik, ha a sablonkifejezés feloldása egy másik értékre történik.

## <a name="what-if-commands"></a>What-if parancsok

### <a name="azure-powershell"></a>Azure PowerShell

A módosítások előnézetének megtekintéséhez a sablon üzembe helyezése előtt használja a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) vagy a [New-AzSubscriptionDeployment függvényt.](/powershell/module/az.resources/new-azdeployment) Adja hozzá `-Whatif` a switch paramétert az üzembe helyezési parancshoz.

* `New-AzResourceGroupDeployment -Whatif` erőforráscsoportok üzembe helyezéséhez
* `New-AzSubscriptionDeployment -Whatif` és `New-AzDeployment -Whatif` előfizetési szintű üzemelő példányok esetén

A switch paraméterrel megtekintheti a módosítások előnézetét, és a rendszer rákérdez az `-Confirm` üzembe helyezés folytatására.

* `New-AzResourceGroupDeployment -Confirm` erőforráscsoportok üzembe helyezéséhez
* `New-AzSubscriptionDeployment -Confirm` és `New-AzDeployment -Confirm` előfizetési szintű üzemelő példányok esetén

Az előző parancsok szöveges összegzést adnak vissza, amelyet manuálisan is megvizsgálhat. A módosításokat programozott módon vizsgálható objektum leához használja a [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) vagy [a Get-AzSubscriptionDeploymentWhatIfResult függvényt.](/powershell/module/az.resources/get-azdeploymentwhatifresult)

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` erőforráscsoportok üzembe helyezéséhez
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` vagy `$results = Get-AzDeploymentWhatIfResult` előfizetés-szintű üzemelő példányok esetén

### <a name="azure-cli"></a>Azure CLI

A sablonok üzembe helyezése előtti módosítások előnézetének megtekintéséhez használja a következőt:

* [az deployment group what-if for](/cli/azure/deployment/group#az_deployment_group_what_if) resource group deployments
* [az deployment sub what-if előfizetési](/cli/azure/deployment/sub#az_deployment_sub_what_if) szintű üzemelő példányok esetén
* [az deployment mg what-if felügyeleti](/cli/azure/deployment/mg#az_deployment_mg_what_if) csoportok üzembe helyezéséhez
* [az deployment tenant what-if bérlői](/cli/azure/deployment/tenant#az_deployment_tenant_what_if) üzembe helyezések esetén

A kapcsolóval (vagy annak rövid formájával) megtekintheti a módosítások előnézetét, és a rendszer felszólítja az üzembe helyezés `--confirm-with-what-if` `-c` folytatására. Adja hozzá ezt a kapcsolót a következő hez:

* [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)
* [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create).
* [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)

Használja például a vagy a `az deployment group create --confirm-with-what-if` `-c` et az erőforráscsoportok üzembe helyezéséhez.

Az előző parancsok szöveges összegzést adnak vissza, amelyet manuálisan is megvizsgálhat. Ha olyan JSON-objektumot keres, amely programozott módon vizsgálhatja a módosításokat, használja a `--no-pretty-print` kapcsolót. Használja például a `az deployment group what-if --no-pretty-print` következőt az erőforráscsoportok üzembe helyezéséhez: .

Ha szín nélkül szeretné visszaadni az eredményeket, nyissa meg [az Azure CLI konfigurációs fájlját.](/cli/azure/azure-cli-configuration) Állítsa **a no_color** **igenre.**

### <a name="azure-rest-api"></a>Azure REST API

A REST API használja a következőt:

* [Üzemelő példányok – What If](/rest/api/resources/deployments/whatif) erőforráscsoportok üzemelő példányai
* [Üzemelő példányok – What If előfizetési hatókörben az](/rest/api/resources/deployments/whatifatsubscriptionscope) előfizetések üzembe helyezéséhez
* [Központi telepítések – What If felügyeleti csoportok központi telepítéséhez a](/rest/api/resources/deployments/whatifatmanagementgroupscope) felügyeleti csoport hatókörében
* [Üzemelő példányok – What If bérlői hatókörben a](/rest/api/resources/deployments/whatifattenantscope) bérlők üzembe helyezéséhez.

## <a name="change-types"></a>Változástípusok

A what-if művelet hat különböző típusú módosítást sorol fel:

- **Létrehozás:** Az erőforrás jelenleg nem létezik, de a sablonban van meghatározva. Az erőforrás létre lesz hozva.

- **Törlés:** Ez a változástípus csak akkor érvényes, ha teljes [módot használ az](deployment-modes.md) üzembe helyezéshez. Az erőforrás létezik, de nincs definiálva a sablonban. Teljes mód esetén az erőforrás törölve lesz. Ez a változástípus csak a [teljes módú](complete-mode-deletion.md) törlést támogató erőforrásokat tartalmazza.

- **Ignore**: Az erőforrás létezik, de nincs definiálva a sablonban. Az erőforrás nem lesz üzembe helyezve vagy módosítva.

- **NoChange:** Az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újra üzembe lesz helyezve, de az erőforrás tulajdonságai nem változnak. Ez a változástípus akkor lesz visszaadva, ha [a ResultFormat](#result-format) értéke `FullResourcePayloads` , ami az alapértelmezett érték.

- **Modify**: Az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újra üzembe lesz helyezve és az erőforrás tulajdonságai megváltoznak. Ez a változástípus akkor lesz visszaadva, ha [a ResultFormat](#result-format) értéke `FullResourcePayloads` , ami az alapértelmezett érték.

- **Üzembe** helyezés: Az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újra üzembe lesz helyezve. Az erőforrás tulajdonságai megváltozhatnak, de nem feltétlenül. A művelet akkor adja vissza ezt a változástípust, ha nem rendelkezik elég információval a tulajdonságok esetleges megváltozásának meghatározásához. Ez a feltétel csak akkor látható, ha [a ResultFormat](#result-format) beállítása `ResourceIdOnly` .

## <a name="result-format"></a>Az eredmény formátuma

Ön szabályozhatja az előrejel jelzett változásokra visszaadott részletességi szintet. Erre két lehetősége van:

* **FullResourcePayloads** – olyan erőforrások listáját adja vissza, amelyek megváltoznak, és részleteket ad a módosító tulajdonságokról
* **ResourceIdOnly** – a módosítandó erőforrások listáját adja vissza

Az alapértelmezett érték **a FullResourcePayloads.**

A PowerShell üzembe helyezési parancsaihoz használja a `-WhatIfResultFormat` paramétert. A programozott objektumparancsok között használja a `ResultFormat` paramétert.

Az Azure CLI-hez használja a `--result-format` paramétert.
 
Az alábbi eredmények a két különböző kimeneti formátumot mutatják:

- Teljes erőforrás-hasznos tartalom

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Csak erőforrás-azonosító

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>What-if művelet futtatása

### <a name="set-up-environment"></a>Környezet beállítása

A what-if működését úgy láthatja, hogy futtatunk néhány tesztet. Először is telepítsen egy [sablont, amely létrehoz egy virtuális hálózatot.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) Ezzel a virtuális hálózattal tesztelni fogja, hogy a what-if hogyan jelenti a módosításokat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Teszt módosítása

Az üzembe helyezés befejezése után készen áll a what-if művelet tesztelésére. Ezúttal üzembe helyez egy [sablont, amely módosítja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)a virtuális hálózatot. Hiányzik egy eredeti címke, egy alhálózat el lett távolítva, és a címelőtag megváltozott.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

A what-if kimenete a következőhöz hasonló:

![Resource Manager üzembe helyezési what-if művelet kimenetének létrehozása](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A szöveges kimenet a következő:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Figyelje meg a kimenet tetején, hogy a módosítások típusát jelző színek vannak definiálva.

A kimenet alján látható, hogy a Tulajdonos címke törölve lett. A címelőtag a 10.0.0.0/16 címelőtagról a 10.0.0.0/15 címre változott. A subnet001 nevű alhálózat törölve lett. Ne feledje, hogy ezeket a módosításokat nem telepítették. Megjelenik a sablon üzembe helyezése esetén végrehajtott módosítások előnézete.

A töröltként felsorolt tulajdonságok némelyike ténylegesen nem változik. A tulajdonságok helytelenül törölhetők, ha nincsenek a sablonban, de az üzembe helyezés során automatikusan alapértelmezett értékként lesznek beállítva. Ez az eredmény "zajnak" minősül a what-if válaszban. A végső üzembe helyezett erőforrás a tulajdonságokhoz beállított értékeket fogja tartalmazni. Ahogy a what-if művelet kiforrott, ezek a tulajdonságok ki lesznek szűrve az eredményből.

## <a name="programmatically-evaluate-what-if-results"></a>What-if eredmények programozott kiértékelése

Most pedig programozott módon értékeljük ki a what-if eredményeket a parancs változóra való beállításával.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Itt láthatja az egyes változások összegzését.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Törlés megerősítése

A what-if művelet támogatja az üzembe [helyezési mód használatának módját.](deployment-modes.md) Ha teljes módra van állítva, a sablonban nem a benne álló erőforrások törlődnek. Az alábbi példa egy olyan [sablont helyez üzembe, amely nem rendelkezik](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) teljes módban definiált erőforrásokkal.

A változásoknak a sablon üzembe helyezése előtti áttekintéséhez használja a jóváhagyást kérő confirm kapcsolót az üzembe helyezési parancsban. Ha a módosítások a vártnak megfelelőek, válaszoljon arra, hogy szeretné, ha az üzembe helyezés befejeződik.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Mivel a sablon nem határoz meg erőforrásokat, és az üzembe helyezési mód befejezettre van állítva, a virtuális hálózat törlődik.

![Resource Manager sablon üzembe helyezésének what-if művelet kimeneti üzembe helyezési módja befejeződött](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

A szöveges kimenet a következő:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001&quot;
      location:        &quot;centralus&quot;
      name:            &quot;vnet-001&quot;
      tags.CostCenter: &quot;12345&quot;
      tags.Owner:      &quot;Team A&quot;
      type:            &quot;Microsoft.Network/virtualNetworks&quot;

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;Y"):
```

Láthatja a várt módosításokat, és megerősítheti, hogy futtatni szeretné az üzembe helyezést.

## <a name="sdks"></a>SDK-k

A what-if műveletet az Azure SDK-kon keresztül használhatja.

* Python esetén használja a [what-if (what-if) et.](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)

* Javához használja a [DeploymentWhatIf osztályt.](/java/api/com.microsoft.azure.management.resources.deploymentwhatif)

* A .NET-hez használja a [DeploymentWhatIf osztályt.](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif)

## <a name="next-steps"></a>Következő lépések

- A what-if művelet folyamatokban való használatával kapcsolatban lásd: ARM-sablonok tesztelése What-If [egy folyamatban.](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)
- Ha helytelen eredményeket ad vissza a what-if műveletből, jelentse a problémákat a következő címen: [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- A what if Microsoft Learn bemutató modult lásd: Preview changes and validate Azure resources by using what-if and the ARM template test toolkit (Módosítások előnézete és [Azure-erőforrások ellenőrzése a what-if és az ARM-sablontesztelőkészlet használatával).](/learn/modules/arm-template-test/)
