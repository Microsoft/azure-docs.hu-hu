---
title: A LocationNotFoundForRoleSize hibaelhárítása felhőszolgáltatás (klasszikus) Azure-beli virtuális | Microsoft Docs
description: Ez a cikk bemutatja, hogyan oldható fel a LocationNotFoundForRoleSize kivétel egy (klasszikus) felhőszolgáltatás Azure-ban való üzembe helyezésekor.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 54af2387ec0ff6c8f86f96821baad17736e8d85b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877965"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>A LocationNotFoundForRoleSize hibaelhárítása felhőszolgáltatás (klasszikus) Azure-ban való üzembe helyezésekor

Ebben a cikkben olyan foglalási hibák elhárítását fogja elhárítani, amelyek miatt a (klasszikus) Azure-felhőszolgáltatás üzembe helyezésekor nem érhető el virtuálisgép-méret.

Amikor példányokat helyez üzembe egy felhőszolgáltatásban (klasszikus), vagy új webes vagy feldolgozói szerepkörpéldányokat ad hozzá, a Microsoft Azure lefoglalja a számítási erőforrásokat.

Előfordulhat, hogy hibaüzenet jelenik meg ezen műveletek során, még az Azure-előfizetés korlátjának elérését megelőzően is.

> [!TIP]
> Ezek az információk a szolgáltatások üzembe helyezésének megtervekor is hasznosak lehetnek.

## <a name="symptom"></a>Hibajelenség

A Azure Portal nyissa meg a Felhőszolgáltatást (klasszikus), majd az oldalsávon válassza a Műveleti napló *(klasszikus)* lehetőséget a naplók megtekintéséhez.

![A képen a Műveleti napló (klasszikus) panel látható.](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

A felhőszolgáltatás (klasszikus) naplóinak vizsgálatakor a következő kivételt fogja látni:

|Kivétel típusa  |Hibaüzenet  |
|---------|---------|
|LocationNotFoundForRoleSize     |A" művelet sikertelen: "A kért virtuálisgép-szint jelenleg nem érhető el az előfizetés `{Operation ID}` régiója ( `{Region ID}` ) esetében. Próbálkozzon egy másik szint vagy üzembe helyezés egy másik helyen."|

## <a name="cause"></a>Ok

Kapacitási probléma van azzal a régióval vagy fürtgal, ahol az üzembe helyezést el kell helyezni. A *LocationNotFoundForRoleSize* kivétel akkor fordul elő, ha a kiválasztott erőforrás-termékváltozat (virtuálisgép-méret) nem érhető el a megadott régióban.

## <a name="solution"></a>Megoldás

Ebben a forgatókönyvben egy másik régiót vagy termékváltozatot kell választania a felhőszolgáltatás (klasszikus) üzembe helyezéséhez. A felhőszolgáltatás (klasszikus) üzembe helyezése vagy frissítése előtt meghatározhatja, hogy mely termékkódok érhetők el egy régióban vagy rendelkezésre állási zónában. Kövesse az [azure cli,](#list-skus-in-region-using-azure-cli) [a PowerShell](#list-skus-in-region-using-powershell) [vagy REST API](#list-skus-in-region-using-rest-api) alábbi folyamatokat.

### <a name="list-skus-in-region-using-azure-cli"></a>A régióban található SKUS-k listái az Azure CLI használatával

Használhatja az [az vm list-skus](/cli/azure/vm?view=azure-cli-latest
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus) parancsot.

- A `--location` paraméterrel szűrheti a kimenetet az Ön által használt helyre.
- A `--size` paraméterrel részleges méretnév alapján kereshet.
- További információért tekintse meg a "Nem érhető el a [termékváltozat"](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) hiba elhárítása útmutatót.

    **Például:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Példaeredmények:** ![ Az "az vm list-skus --location southcentralus --size Standard_F --output table" parancs futtatásának Azure CLI-kimenete, amely az elérhető termékkódokat jeleníti meg.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>A régióban található SKUS-k felsorolása a PowerShell használatával

Használhatja a [Get-AzComputeResourceSku parancsot.](/powershell/module/az.compute/get-azcomputeresourcesku)

- Szűrje az eredményeket hely szerint.
- Ehhez a parancshoz a PowerShell legújabb verziójával kell lennie.
- További információért tekintse meg a "Nem érhető el a [termékváltozat"](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) hiba elhárítása útmutatót.

**Például:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Néhány további hasznos parancs:**

Szűrje ki a méretet tartalmazó helyeket (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Szűrje ki a méretet (V3) tartalmazó összes helyet:

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>List SKUs in region using REST API

Használhatja az [Erőforrás-Skus – List műveletet.](/rest/api/compute/resourceskus/list) Az elérhető SKUs-okat és régiókat a következő formátumban adja vissza:

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

További foglalási hibamegoldások és azok generálása jobb érthetőség érdekében:

> [!div class="nextstepaction"]
> [Foglalási hibák – Felhőszolgáltatás (klasszikus)](cloud-services-allocation-failures.md)

Ha a cikk nem foglalkozik az Azure-ral kapcsolatos probléma megoldásával, látogasson el az [MSDN-ről](https://azure.microsoft.com/support/forums/)az Azure fórumára, és Stack Overflow. A problémát közzétenheti ezeken a fórumokon, vagy közzétenheti a [ @AzureSupport twitteren.](https://twitter.com/AzureSupport) Elküldhet egy Azure-támogatás is. Támogatási kérés elküldését a támogatási [Azure-támogatás](https://azure.microsoft.com/support/options/) oldalon válassza a *Támogatás kérése lehetőséget.*
