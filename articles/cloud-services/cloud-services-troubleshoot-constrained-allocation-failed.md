---
title: ConstrainedAllocationFailed hibáinak megoldása felhőalapú szolgáltatás Azure-beli üzembe helyezése során | Microsoft Docs
description: Ez a cikk bemutatja, Hogyan oldhatók fel a ConstrainedAllocationFailed-kivételek egy felhőalapú szolgáltatás Azure-beli üzembe helyezése során.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521068"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>ConstrainedAllocationFailed hibáinak megoldása felhőalapú szolgáltatás Azure-beli üzembe helyezése során

Ebben a cikkben azokat a foglalási hibákat fogja elhárítani, amelyekben az Azure Cloud Services korlátozások miatt nem telepíthető.

Microsoft Azure foglalása:

- Cloud Services-példányok frissítése

- Új webes vagy feldolgozói szerepkör-példányok hozzáadása

- Példányok üzembe helyezése egy felhőalapú szolgáltatásban

Időnként előfordulhat, hogy a műveletek során még az Azure-előfizetési korlát elérése előtt is hibákat fog kapni.

> [!TIP]
> Az információk akkor is hasznosak lehetnek, ha a szolgáltatások üzembe helyezését tervezi.

## <a name="symptom"></a>Hibajelenség

A Azure Portalban navigáljon a Cloud Service-hez, és az oldalsávon válassza ki a *műveleti naplókat (klasszikus)* a naplók megtekintéséhez.

A felhőalapú szolgáltatás naplóinak vizsgálatakor a következő kivétel jelenik meg:

|Kivétel típusa  |Hibaüzenet  |
|---------|---------|
|ConstrainedAllocationFailed     |A (z) "" Azure-művelet `{Operation ID}` végrehajtása sikertelen volt, kód: számítás. ConstrainedAllocationFailed. Részletek: a foglalás nem sikerült; nem lehet kielégíteni a megkötéseket a kérelemben. A kért új szolgáltatás üzembe helyezése egy affinitáscsoporthoz van kötve, egy virtuális hálózatot céloz, vagy már van egy üzemelő példány az üzemeltetett szolgáltatásban. Ezen feltételek bármelyike korlátozza az új üzembe helyezést bizonyos Azure-erőforrásokra. Próbálkozzon újra később, vagy próbálja meg csökkenteni a virtuális gép méretét vagy a szerepkör-példányok számát. Ha lehetséges, alternatív lehetőségként távolítsa el a fentebb említett korlátozásokat, vagy végezze el az üzembe helyezést egy másik régión.|

## <a name="cause"></a>Ok

Van egy kapacitási probléma azzal a régióval vagy fürttel, amelyet üzembe helyez. Akkor következik be, amikor a kiválasztott erőforrás-SKU nem érhető el a megadott helyen.

> [!NOTE]
> A felhőalapú szolgáltatás első csomópontjának telepítésekor a rendszer egy erőforráskészletet *rögzít* . Az erőforráskészlet lehet egyetlen fürt vagy fürtök csoportja.
>
> Idővel az erőforráskészlet erőforrásai teljes mértékben kihasználhatók lesznek. Ha egy felhőalapú szolgáltatás kiosztási kérést biztosít a további erőforrásokhoz, ha nem áll rendelkezésre elegendő erőforrás a rögzített erőforráskészlet-készletben, akkor a kérés [foglalási hibát](cloud-services-allocation-failures.md)eredményez.

## <a name="solution"></a>Megoldás

Ebben az esetben válasszon egy másik régiót vagy SKU-t a felhőalapú szolgáltatás üzembe helyezéséhez. A felhőalapú szolgáltatás üzembe helyezése vagy frissítése előtt meghatározhatja, hogy mely SKU-ket lehet elérni egy adott régióban vagy rendelkezésre állási zónában. Kövesse az alábbi [Azure CLI](#list-skus-in-region-using-azure-cli)-, [PowerShell](#list-skus-in-region-using-powershell)-vagy [REST API](#list-skus-in-region-using-rest-api) -folyamatokat.

### <a name="list-skus-in-region-using-azure-cli"></a>Az Azure CLI-t használó régióban található SKU-lista listázása

Használhatja az az [VM List-SKUs](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) parancsot.

- A `--location` paraméter használatával szűrheti a kimenetet a használt helyre.
- A paraméter használatával a `--size` részleges méret neve alapján kereshet.
- További információ: a [hiba elhárítása az SKU-hoz nem elérhető](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) útmutatóban.

    **Például:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Példa eredményei:** ![ Az Azure CLI kimenete az "az VM List-SKUs--Location southcentralus--size Standard_F--output Table" parancs futtatásához, amely megjeleníti az elérhető SKU-ket.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Az SKU listázása a régióban a PowerShell használatával

Használhatja a [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) parancsot.

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

Használhatja a [Resource SKU-List](https://docs.microsoft.com/rest/api/compute/resourceskus/list) műveletet. Az elérhető SKU-ket és régiókat a következő formátumban adja vissza:

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
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>Következő lépések

További lefoglalási hibák megoldásához és a generált eszközök jobb megismeréséhez:

> [!div class="nextstepaction"]
> [Foglalási hibák (Cloud Services)](cloud-services-allocation-failures.md)

Ha az Azure-beli probléma nem szerepel ebben a cikkben, látogasson el az MSDN webhelyen található Azure-fórumokra, [és stack overflow](https://azure.microsoft.com/support/forums/). Felteheti a problémát ezekben a fórumokon, vagy közzéteheti a [ @AzureSupport Twitteren](https://twitter.com/AzureSupport). Azure-támogatási kérelmet is küldhet. Ha támogatási kérést szeretne küldeni, az [Azure-támogatás](https://azure.microsoft.com/support/options/) lapon válassza a *támogatás* lekérése lehetőséget.
