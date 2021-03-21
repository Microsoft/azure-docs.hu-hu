---
title: Template deployment mi a teendő
description: A Azure Resource Manager-sablon telepítése előtt határozza meg, hogy milyen változások történnek az erőforrásokban.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tomfitz
ms.openlocfilehash: 3f35f00e5fcd26c7aa4bb685dc80f48eabb88b6d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547934"
---
# <a name="arm-template-deployment-what-if-operation"></a>ARM-sablon üzembe helyezése what-if művelettel

Azure Resource Manager sablon (ARM-sablon) üzembe helyezése előtt megtekintheti a megjelenő módosításokat. A Azure Resource Manager a mi-if művelettel teszi lehetővé, hogy az erőforrások hogyan változnak, ha telepíti a sablont. A what-if művelet nem módosít meglévő erőforrásokat. Ehelyett előre jelzi a megadott sablon üzembe helyezésével járó változásokat.

Az Azure PowerShell, az Azure CLI vagy a REST API műveletekkel használhatja a mi-if műveletet. Mi a teendő, ha az erőforráscsoport, az előfizetés, a felügyeleti csoport és a bérlői szintű üzemelő példányok esetében támogatott.

## <a name="install-azure-powershell-module"></a>Azure PowerShell modul telepítése

A mi-ha a PowerShellben való használatához **az az modul 4,2-es vagy újabb** verziójával kell rendelkeznie.

A modul telepítéséhez használja a következőt:

```powershell
Install-Module -Name Az -Force
```

A modulok telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="install-azure-cli-module"></a>Az Azure CLI-modul telepítése

A what-if Azure CLI-beli használatához az Azure CLI 2.5.0 vagy újabb verziója szükséges. Ha szükséges, [telepítse az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Eredmények megtekintése

Ha a PowerShellben vagy az Azure CLI-ben használ, a kimenet színkódolt eredményeket tartalmaz, amelyek segítségével megtekintheti a különböző típusú módosításokat.

![Resource Manager-sablonok üzembe helyezése – mi a teendő, ha a művelet fullresourcepayload és a változás típusa](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A szöveg kimenete:

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
> A mi – if művelet nem tudja feloldani a [hivatkozási függvényt](template-functions-resource.md#reference). Minden alkalommal, amikor olyan tulajdonságot állít be egy sablon-kifejezésre, amely tartalmazza a hivatkozási függvényt, mi – if jelentések esetén a tulajdonság módosul. Ez a viselkedés azért fordul elő, mert a mi – ha összehasonlítja a tulajdonság aktuális értékét (például `true` vagy `false` logikai érték esetén) a megoldatlan sablon kifejezéssel. Ezek az értékek nyilvánvalóan nem egyeznek. A sablon központi telepítésekor a tulajdonság csak akkor változik, ha a sablon kifejezése egy másik értékre oldódik fel.

## <a name="what-if-commands"></a>Mi – if parancsok

### <a name="azure-powershell"></a>Azure PowerShell

A módosítások előnézetéhez a sablon telepítése előtt használja a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) vagy a [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment). Adja hozzá a `-Whatif` switch paramétert a Deployment parancshoz.

* `New-AzResourceGroupDeployment -Whatif` erőforráscsoport-telepítések esetén
* `New-AzSubscriptionDeployment -Whatif` és `New-AzDeployment -Whatif` előfizetési szintű központi telepítések esetén

A `-Confirm` switch paraméterrel megtekintheti a módosításokat, és a rendszer kéri, hogy folytassa a telepítést.

* `New-AzResourceGroupDeployment -Confirm` erőforráscsoport-telepítések esetén
* `New-AzSubscriptionDeployment -Confirm` és `New-AzDeployment -Confirm` előfizetési szintű központi telepítések esetén

Az előző parancsok olyan szöveges összegzést adnak vissza, amelyet manuálisan lehet megvizsgálni. Ha olyan objektumot szeretne beolvasni, amelyet programozott módon vizsgálhat a változásokhoz, használja a [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) vagy a [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult).

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` erőforráscsoport-telepítések esetén
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` vagy `$results = Get-AzDeploymentWhatIfResult` előfizetési szintű központi telepítések esetén

### <a name="azure-cli"></a>Azure CLI

A következő paranccsal tekintheti meg a módosításokat a sablon telepítése előtt:

* [az üzembe helyezési csoport mi – ha](/cli/azure/deployment/group#az-deployment-group-what-if) az erőforráscsoport-telepítésekhez
* [az üzembe helyezési alárendelt](/cli/azure/deployment/sub#az-deployment-sub-what-if)
* [az Deployment mg mi – ha](/cli/azure/deployment/mg#az-deployment-mg-what-if) a felügyeleti csoport központi telepítései
* [az üzembe helyezési bérlő](/cli/azure/deployment/tenant#az-deployment-tenant-what-if)

A `--confirm-with-what-if` kapcsolót (vagy annak rövid formáját) használva `-c` megtekintheti a módosításokat, és a rendszer kéri, hogy folytassa a telepítést. Adja hozzá ezt a kapcsolót a következőhöz:

* [az üzembe helyezési csoport létrehozása](/cli/azure/deployment/group#az-deployment-group-create)
* [az Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create).
* [az Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create)
* [az Deployment bérlő létrehozása](/cli/azure/deployment/tenant#az-deployment-tenant-create)

Használhatja például `az deployment group create --confirm-with-what-if` `-c` a (z) vagy for erőforráscsoport üzemelő példányait.

Az előző parancsok olyan szöveges összegzést adnak vissza, amelyet manuálisan lehet megvizsgálni. Egy olyan JSON-objektum beszerzéséhez, amelyet programozott módon vizsgálhat a változásokhoz, használja a `--no-pretty-print` kapcsolót. Például `az deployment group what-if --no-pretty-print` az erőforráscsoport-telepítésekhez használható.

Ha színek nélkül szeretné visszaadni az eredményeket, nyissa meg az [Azure CLI konfigurációs](/cli/azure/azure-cli-configuration) fájlját. Állítsa  a no_color **értéket igen** értékre.

### <a name="azure-rest-api"></a>Azure REST API

REST API esetén használja a következőt:

* [Központi telepítések – What if](/rest/api/resources/deployments/whatif) az erőforráscsoportok üzembe helyezéséhez
* [Központi telepítések –](/rest/api/resources/deployments/whatifatsubscriptionscope) az előfizetések hatókörének What if az előfizetés központi telepítéséhez
* [Központi telepítések – What if a felügyeleti csoport hatókörében](/rest/api/resources/deployments/whatifatmanagementgroupscope) a felügyeleti csoportok központi telepítéséhez
* [Központi telepítések – What if a bérlői hatókörben](/rest/api/resources/deployments/whatifattenantscope) a bérlői környezetekben.

## <a name="change-types"></a>Változástípusok

A mi-if művelet hat különböző típusú változást sorol fel:

- **Létrehozás**: az erőforrás jelenleg nem létezik, de definiálva van a sablonban. Az erőforrás létre lesz hozva.

- **Delete (Törlés**): Ez a módosítási típus csak akkor érvényes, ha az üzembe helyezéshez [teljes üzemmódot](deployment-modes.md) használ. Az erőforrás létezik, de nincs definiálva a sablonban. Teljes mód esetén az erőforrás törölve lesz. Ebben a változási típusban csak a [teljes módú törlést támogató](complete-mode-deletion.md) erőforrások szerepelnek.

- **Figyelmen kívül hagyva**: az erőforrás létezik, de nincs definiálva a sablonban. Az erőforrás nem lesz üzembe helyezve vagy módosítva.

- Nincs **változás**: az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újra üzembe lesz helyezve, de az erőforrás tulajdonságai nem változnak. Ezt a változást adja vissza a [ResultFormat](#result-format) beállításakor `FullResourcePayloads` , amely az alapértelmezett érték.

- **Módosítás**: az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újra üzembe lesz helyezve és az erőforrás tulajdonságai megváltoznak. Ezt a változást adja vissza a [ResultFormat](#result-format) beállításakor `FullResourcePayloads` , amely az alapértelmezett érték.

- **Üzembe helyezés**: az erőforrás létezik, és a sablonban van definiálva. Az erőforrás újra üzembe lesz helyezve. Az erőforrás tulajdonságai megváltozhatnak, de nem feltétlenül. A művelet akkor adja vissza ezt a változástípust, ha nem rendelkezik elég információval a tulajdonságok esetleges megváltozásának meghatározásához. Ezt az állapotot csak akkor láthatja, ha a [ResultFormat](#result-format) értéke `ResourceIdOnly` .

## <a name="result-format"></a>Az eredmény formátuma

Szabályozhatja az előre jelzett változásokkal kapcsolatos részletességi szintet. Erre két lehetősége van:

* **FullResourcePayloads** – a módosult erőforrások listáját adja vissza, és megjeleníti a módosítani kívánt tulajdonságok részleteit is
* **ResourceIdOnly** – a módosítandó erőforrások listáját adja vissza.

Az alapértelmezett érték a **FullResourcePayloads**.

PowerShell-telepítési parancsok esetén használja a `-WhatIfResultFormat` paramétert. A programozott objektum parancsaiban használja a (z `ResultFormat` ) paramétert.

Az Azure CLI esetén használja a `--result-format` paramétert.
 
A következő eredmények a két különböző kimeneti formátumot mutatják be:

- Teljes erőforrás-tartalom

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

## <a name="run-what-if-operation"></a>Művelet futtatása

### <a name="set-up-environment"></a>Környezet beállítása

Ha szeretné megtekinteni, hogyan működik a megoldás, hozzon néhány tesztet. Először telepítsen egy olyan [sablont, amely létrehoz egy virtuális hálózatot](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Ezzel a virtuális hálózattal tesztelheti, hogyan történik a módosítások jelentése.

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

Az üzembe helyezés befejezése után készen áll a mi-if művelet tesztelésére. Ezúttal olyan [sablont telepít, amely megváltoztatja a virtuális hálózatot](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Hiányzik egy eredeti címke, egy alhálózat el lett távolítva, és megváltoztak a címek előtagja.

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

![Resource Manager-sablon üzembe helyezése – mi a művelet kimenete](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A szöveg kimenete:

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

Figyelje meg, hogy a kimenet felső részén a színek határozzák meg a változások típusát.

A kimenet alján látható, hogy a címke tulajdonosa törölve lett. A 10.0.0.0/16 értékről 10.0.0.0/15-re módosult a címtartomány. A subnet001 nevű alhálózat törölve lett. Ne feledje, hogy ezek a módosítások nem lettek telepítve. Megjelenik a sablon központi telepítése során megjelenő változtatások előnézete.

A töröltként felsorolt tulajdonságok némelyike valójában nem változik. A tulajdonságokat helytelenül lehet törölni, ha nem a sablonban vannak, de automatikusan be vannak állítva az üzembe helyezés során alapértelmezett értékként. Ez az eredmény "Noise" a mi-if válaszban. A végső központilag telepített erőforrás a tulajdonságoknál beállított értékeket fogja tartalmazni. A mi-if művelet lejáratakor ezek a tulajdonságok kiszűrve lesznek az eredményből.

## <a name="programmatically-evaluate-what-if-results"></a>Az eredmények programozott kiértékelése

Most pedig programozott módon kiértékeljük a mi-if eredményeket úgy, hogy a parancsot egy változóra állítja be.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Megtekintheti az egyes változtatások összegzését.

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

A mi-if művelet támogatja a [telepítési mód](deployment-modes.md)használatát. Ha a befejezési módra van állítva, a sablonban nem szereplő erőforrások törlődnek. A következő példa olyan sablont telepít [, amely nem rendelkezik teljes módban definiált erőforrásokkal](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) .

A változásoknak a sablon üzembe helyezése előtti áttekintéséhez használja a jóváhagyást kérő confirm kapcsolót az üzembe helyezési parancsban. Ha a módosítások a várt módon változnak, válaszoljon arra, hogy a központi telepítés befejeződjön.

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

Mivel a sablonban nincs megadva erőforrás, és a telepítési mód a Befejezés értékre van állítva, a rendszer törli a virtuális hálózatot.

![Resource Manager-sablonok üzembe helyezése – mi a teendő, ha a művelet kimenetének üzembe helyezési módja befejeződött](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

A szöveg kimenete:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Megtekintheti a várt módosításokat, és ellenőrizheti, hogy szeretné-e futtatni a központi telepítést.

## <a name="sdks"></a>SDK-k

Az Azure SDK-k használatával a mi-if művelet is használható.

* Python [esetén használja a](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)következőt:.

* A Javához használja a [DeploymentWhatIf osztályt](/java/api/com.microsoft.azure.management.resources.deploymentwhatif).

* .NET esetén használja a [DeploymentWhatIf osztályt](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif).

## <a name="next-steps"></a>Következő lépések

- Ha a mi-if műveletet egy folyamaton szeretné használni, olvassa el a következő témakört: [ARM-sablonok tesztelése What-If egy folyamaton belül](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/).
- Ha a mi-if művelet helytelen eredményeit észleli, jelentse a hibákat a következő helyen: [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Ha olyan Microsoft Learn modult használ, amely tartalmazza a mi a teendőt, olvassa el az [előzetes verzió módosításait és az Azure-erőforrások érvényesítését a mi és az ARM-sablon tesztelési eszközkészlet használatával](/learn/modules/arm-template-test/).
