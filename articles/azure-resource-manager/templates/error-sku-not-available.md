---
title: Nem elérhető termékváltozatokkal kapcsolatos hibák
description: Ismerteti, hogyan háríthatja el a nem elérhető termékváltozatokkal kapcsolatos hibákat, amikor erőforrásokat helyez üzembe a Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.openlocfilehash: 3baedf6a5c9f2dbfd3ddf666b458fac649fce2ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503898"
---
# <a name="resolve-errors-for-sku-not-available"></a>Nem elérhető termékváltozattal kapcsolatos hibák elhárítása

Ez a cikk az **SkuNotAvailable** hiba megoldását ismerteti. Ha nem talál megfelelő termékváltozatot abban a régióban/zónában vagy az üzleti igényeinek megfelelő [](/troubleshoot/azure/general/region-access-request-process) alternatív régiót/zónát, küldjön egy termékváltozat-kérelmet a Azure ügyfélszolgálata.

## <a name="symptom"></a>Hibajelenség

Erőforrás (általában virtuális gép) üzembe helyezésekor a következő hibakódot és hibaüzenetet kapja:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Ok

Ez a hiba akkor jelenik meg, ha a kiválasztott erőforrás-termékváltozat (például a virtuális gép mérete) nem érhető el a kiválasztott helyen.

Ha azure-beli kihasznált vm-et vagy kihasznál kihasznált méretezésikészlet-példányt helyez üzembe, ezen a helyen nincs kapacitás az Azure Spot számára. További információ: Spot [hibaüzenetek.](../../virtual-machines/error-codes-spot.md)

## <a name="solution-1---powershell"></a>1. megoldás – PowerShell

A régióban/zónában elérhető termékváltozatok meghatározásához használja a [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) parancsot. Szűrje az eredményeket hely szerint. Ehhez a parancshoz a PowerShell legújabb verziójával kell lennie.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Az eredmények között szerepel a hely termékváltozatai és az adott termékváltozatra vonatkozó korlátozások listája. Figyelje meg, hogy egy termékváltozat a következőként lehet listázva: `NotAvailableForSubscription` .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

A hely és a termékváltozat alapján való szűréshez használja a következőt:

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

A parancs a következő eredményeket adja vissza:

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>2. megoldás – Azure CLI

A régióban elérhető SKUS-k meghatározásához használja [az az vm list-skus](/cli/azure/vm#az_vm_list_skus) parancsot. A `--location` paraméterrel hely szerint szűrheti a kimenetet. A `--size` paraméterrel részleges méretnév alapján kereshet. A paraméterrel az összes információt meg lehet jelenni, beleértve az aktuális előfizetéshez `--all` nem elérhető méreteket is.

Az Azure CLI 2.15.0-s vagy újabb verziójával kell lennie. A verzió ellenőrzéshez használja a `az --version` et. Ha szükséges, [frissítse a telepítést.](/cli/azure/update-azure-cli)

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

A parancs a következő eredményeket adja vissza:

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
...
```

## <a name="solution-3---azure-portal"></a>3. megoldás – Azure Portal

Annak megállapításához, hogy egy régióban mely SKUS-k érhetők el, használja a [portált.](https://portal.azure.com) Jelentkezzen be a portálra, és adjon hozzá egy erőforrást a felületen keresztül. Az értékek beállítása után láthatja az erőforráshoz elérhető SKUS-okat. Az üzembe helyezést nem kell befejeznie.

Elindíthatja például egy virtuális gép létrehozásának folyamatát. Az egyéb elérhető méreteket a **Méret módosítása lehetőség kiválasztásával láthatja.**

![Virtuális gép létrehozása](./media/error-sku-not-available/create-vm.png)

Az elérhető méreteket szűrheti és görgetheti.

![Elérhető termékváltozatok](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>4. megoldás – REST

Annak megállapításához, hogy egy régióban mely SKUS-k érhetők el, használja az [Erőforrás-Skus – List műveletet.](/rest/api/compute/resourceskus/list)

Az elérhető SKUs-okat és régiókat a következő formátumban adja vissza:

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