---
title: Nem érhető el az SKU-hibák
description: Ismerteti, hogyan lehet elhárítani az SKU nem elérhető hibáját az erőforrások Azure Resource Manager használatával történő telepítésekor.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 5b0bbd653907c109eca526af86979013b3137cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737150"
---
# <a name="resolve-errors-for-sku-not-available"></a>Nem elérhető termékváltozattal kapcsolatos hibák elhárítása

Ez a cikk a **SkuNotAvailable** -hiba elhárítását ismerteti. Ha nem talál megfelelő SKU-t az adott régióban vagy zónában, vagy egy másik régióban vagy zónában, amely megfelel az Ön üzleti igényeinek, küldjön egy [SKU-kérelmet](/troubleshoot/azure/general/region-access-request-process) az Azure támogatási szolgálatának.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Hibajelenség

Egy erőforrás (általában egy virtuális gép) telepítésekor a következő hibakód és hibaüzenet jelenik meg:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Ok

Ez a hiba akkor jelenik meg, ha a kiválasztott erőforrás-SKU (például a virtuális gép mérete) nem érhető el a kiválasztott helyhez.

Ha egy Azure spot VM-vagy direktszín-méretezési csoport példányát telepíti, akkor ezen a helyen nem áll rendelkezésre kapacitás az Azure-hoz. További információ: [spot hibaüzenetek](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>1. megoldás – PowerShell

Egy adott régióban vagy zónában elérhető SKU-ket a [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) parancs használatával állapíthatja meg. Az eredmények kiszűrése hely szerint. Ehhez a parancshoz a PowerShell legújabb verzióját kell megadnia.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Az eredmények közé tartozik a helyhez tartozó SKU-lista és az adott SKU korlátozásai. Figyelje meg, hogy az SKU szerepel a-ben `NotAvailableForSubscription` .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Néhány további minta:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

A "FC" hozzáfűzése a végén további részleteket ad vissza.

## <a name="solution-2---azure-cli"></a>2. megoldás – Azure CLI

Az adott régióban elérhető SKU-ket a paranccsal állapíthatja meg `az vm list-skus` . A `--location` paraméter használatával szűrheti a kimenetet a használt helyre. A paraméter használatával a `--size` részleges méret neve alapján kereshet.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

A parancs a következőhöz hasonló eredményeket ad vissza:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```

## <a name="solution-3---azure-portal"></a>3. megoldás – Azure Portal

Az adott régióban elérhető SKU-ket a [portálon](https://portal.azure.com)állapíthatja meg. Jelentkezzen be a portálra, és adjon hozzá egy erőforrást az interfészen keresztül. Az értékek megadásakor az adott erőforráshoz rendelkezésre álló SKU-ket láthatja. Nem kell végrehajtania az üzembe helyezést.

Megkezdheti például a virtuális gép létrehozásának folyamatát. Ha más elérhető méretet szeretne látni, válassza a **méret módosítása** lehetőséget.

![Virtuális gép létrehozása](./media/error-sku-not-available/create-vm.png)

A rendelkezésre álló méretek szűrése és görgetése lehetséges.

![Elérhető termékváltozatok](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>4. megoldás – REST

A régiókban elérhető SKU-ket a [forrás SKU-List](/rest/api/compute/resourceskus/list) művelettel állapíthatja meg.

Az elérhető SKU-ket és régiókat a következő formátumban adja vissza:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```