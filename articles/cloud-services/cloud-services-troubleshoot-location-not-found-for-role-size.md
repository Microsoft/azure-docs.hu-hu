---
title: A LocationNotFoundForRoleSize hibáinak megoldása a Cloud Service (klasszikus) Azure-ba történő telepítésekor | Microsoft Docs
description: Ez a cikk bemutatja, hogyan oldható fel a LocationNotFoundForRoleSize kivétel a Cloud Service (klasszikus) Azure-ba történő telepítésekor.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 3d1a1135db0421d89d4c6c9f278c86e02cb5cb32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558958"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>A LocationNotFoundForRoleSize hibáinak megoldása a Cloud Service (klasszikus) Azure-ba történő telepítésekor

Ebben a cikkben azokat a foglalási hibákat fogja elhárítani, amelyekben a virtuális gép (VM) mérete nem érhető el az Azure Cloud Service (klasszikus) telepítésekor.

Ha a példányokat egy felhőalapú szolgáltatásba (klasszikus) telepíti, vagy új webes vagy feldolgozói szerepkör-példányokat ad hozzá, Microsoft Azure kioszt egy számítási erőforrást.

Időnként előfordulhat, hogy a műveletek során még az Azure-előfizetési korlát elérése előtt is hibákat fog kapni.

> [!TIP]
> Az információk akkor is hasznosak lehetnek, ha a szolgáltatások üzembe helyezését tervezi.

## <a name="symptom"></a>Hibajelenség

A naplók megtekintéséhez a Azure Portal navigáljon a Cloud Service-be (klasszikus), és az oldalsávon válassza a *műveleti napló (klasszikus)* lehetőséget.

![A képen a művelet napló (klasszikus) panel látható.](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Ha megvizsgálja a Cloud Service naplóit (klasszikus), a következő kivétel jelenik meg:

|Kivétel típusa  |Hibaüzenet  |
|---------|---------|
|LocationNotFoundForRoleSize     |A (z `{Operation ID}` ) "" művelet sikertelen volt: a kért virtuálisgép-platform jelenleg nem érhető el a (z) régióban () ehhez az `{Region ID}` előfizetéshez. Próbálkozzon másik szinten, vagy helyezzen üzembe egy másik helyet.|

## <a name="cause"></a>Ok

Van egy kapacitási probléma azzal a régióval vagy fürttel, amelyet üzembe helyez. A *LocationNotFoundForRoleSize* kivétel akkor fordul elő, ha a kiválasztott erőforrás-SKU (virtuális gép mérete) nem érhető el a megadott régióhoz.

## <a name="solution"></a>Megoldás

Ebben a forgatókönyvben egy másik régiót vagy SKU-t kell kiválasztania a Cloud Service (klasszikus) a rendszerbe való üzembe helyezéséhez. A Cloud Service (klasszikus) üzembe helyezése vagy frissítése előtt meghatározhatja, hogy mely SKU-helyek érhetők el egy adott régióban vagy rendelkezésre állási zónában. Kövesse az alábbi [Azure CLI](#list-skus-in-region-using-azure-cli)-, [PowerShell](#list-skus-in-region-using-powershell)-vagy [REST API](#list-skus-in-region-using-rest-api) -folyamatokat.

### <a name="list-skus-in-region-using-azure-cli"></a>Az Azure CLI-t használó régióban található SKU-lista listázása

Használhatja az az [VM List-SKUs](/cli/azure/vm.html#az_vm_list_skus) parancsot.

- A `--location` paraméter használatával szűrheti a kimenetet a használt helyre.
- A paraméter használatával a `--size` részleges méret neve alapján kereshet.
- További információ: a [hiba elhárítása az SKU-hoz nem elérhető](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) útmutatóban.

    **Például:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Példa eredményei:** ![ Az Azure CLI kimenete az "az VM List-SKUs--Location southcentralus--size Standard_F--output Table" parancs futtatásához, amely megjeleníti az elérhető SKU-ket.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Az SKU listázása a régióban a PowerShell használatával

Használhatja a [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) parancsot.

- Az eredmények kiszűrése hely szerint.
- Ehhez a parancshoz a PowerShell legújabb verzióját kell megadnia.
- További információ: a [hiba elhárítása az SKU-hoz nem elérhető](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) útmutatóban.

**Például:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Más hasznos parancsok:**

A méretet (Standard_DS14_v2) tartalmazó tárolóhelyek kiszűrése:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

A méretet (v3) tartalmazó összes tárolóhely kiszűrése:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>A régióban található SKU-ket REST API használatával listázhatja

Használhatja a [Resource SKU-List](/rest/api/compute/resourceskus/list) műveletet. Az elérhető SKU-ket és régiókat a következő formátumban adja vissza:

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
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Következő lépések

További lefoglalási hibák megoldásához és a generált eszközök jobb megismeréséhez:

> [!div class="nextstepaction"]
> [Foglalási hibák – Cloud Service (klasszikus)](cloud-services-allocation-failures.md)

Ha az Azure-beli probléma nem szerepel ebben a cikkben, látogasson el az MSDN webhelyen található Azure-fórumokra, [és stack overflow](https://azure.microsoft.com/support/forums/). Felteheti a problémát ezekben a fórumokon, vagy közzéteheti a [ @AzureSupport Twitteren](https://twitter.com/AzureSupport). Azure-támogatási kérelmet is küldhet. Ha támogatási kérést szeretne küldeni, az [Azure-támogatás](https://azure.microsoft.com/support/options/) lapon válassza a *támogatás* lekérése lehetőséget.