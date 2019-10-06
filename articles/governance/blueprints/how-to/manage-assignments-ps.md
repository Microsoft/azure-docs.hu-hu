---
title: Hozzárendelések kezelése a PowerShell-lel
description: Megtudhatja, hogyan kezelheti a terv-hozzárendeléseket a hivatalos Azure-tervezetek PowerShell-modullal, az. Blueprint használatával.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/30/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 297c6a51c1f902cf7b5843b2dd47b658ebc705fd
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980988"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Hozzárendelések kezelése a PowerShell-lel

A tervrajzok hozzárendelését az az **. blueprint** Azure PowerShell modullal lehet felügyelni. A modul a hozzárendelések beolvasását, létrehozását, frissítését és eltávolítását támogatja. A modul a meglévő terv-definíciók részleteit is beolvashatja. Ez a cikk ismerteti, hogyan telepítheti a modult, és megkezdheti a használatát.

## <a name="add-the-azblueprint-module"></a>Az az. Blueprint modul hozzáadása

Ha engedélyezni szeretné a Azure PowerShell a terv-hozzárendelések kezeléséhez, a modult hozzá kell adni. Ez a modul helyileg telepített PowerShell-lel, [Azure Cloud Shell](https://shell.azure.com)vagy a [Azure PowerShell Docker-lemezképpel](https://hub.docker.com/r/azuresdk/azure-powershell/)használható.

### <a name="base-requirements"></a>Alapszintű követelmények

Az Azure BluePrints modulhoz a következő szoftverek szükségesek:

- Azure PowerShell 1.5.0 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1-es vagy újabb verzió. Ha nincs telepítve vagy frissítve, kövesse [ezeket az utasításokat](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>A modul telepítése

A PowerShell tervrajz-modulja az **az. Blueprint**.

1. **Rendszergazdai** PowerShell-parancssorból futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Ha az **az. accounts** már telepítve van, szükség lehet a `-AllowClobber` használatára a telepítés kényszerítéséhez.

1. Ellenőrizze, hogy a modul importálása megtörtént-e, és a megfelelő verzió-e (0.2.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Tervezet-definíciók beolvasása

A hozzárendelések használatának első lépése gyakran hivatkozik egy terv meghatározására.
A `Get-AzBlueprint` parancsmag egy vagy több tervezet-definíciót kap. A parancsmag egy felügyeleti csoport tervezet-definícióit `-ManagementGroupId {mgId}` vagy `-SubscriptionId {subId}` előfizetéssel szerezheti be. A **Name** paraméter egy terv definícióját kapja meg, de a **ManagementGroupId** vagy a **SubscriptionId**használatával kell használni. A **verzió** használható a **névvel** , hogy világosabb legyen a terv definíciója. A **verzió**helyett a `-LatestPublished` kapcsoló a legutóbb közzétett verziót fogja megragadni.

A következő példa a `Get-AzBlueprint` értéket használja az "101-BluePrints-definition-Subscription" nevű tervezet-definíció összes verziójának lekéréséhez egy adott előfizetésből, amelyet `{subId}` értékként képvisel:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

A több verzióval rendelkező terv definíciójának kimenete a következőképpen néz ki:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

A terv definíciójában a terv [paramétereinek](../concepts/parameters.md#blueprint-parameters) kiterjeszthetők további információk megadására.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Tervezet-hozzárendelések beolvasása

Ha a terv-hozzárendelés már létezik, akkor a `Get-AzBlueprintAssignment` parancsmaggal hivatkozhat rá. A parancsmag a **SubscriptionId** és a **Name** paramétert választható paraméterekként veszi igénybe. Ha a **SubscriptionId** nincs megadva, a rendszer az aktuális előfizetési környezetet használja.

A következő példa a `Get-AzBlueprintAssignment` értéket használja egy "hozzárendelés-zárolás – erőforrás-csoportok" nevű, egy adott előfizetésből származó, `{subId}` típusú hozzárendeléshez:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

A terv hozzárendelésének kimenete a következőképpen néz ki:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Terv-hozzárendelések létrehozása

Ha a terv hozzárendelése még nem létezik, akkor a `New-AzBlueprintAssignment` parancsmaggal hozható létre. Ez a parancsmag a következő paramétereket használja:

- **Név** [kötelező]
  - Meghatározza a terv-hozzárendelés nevét
  - Egyedinek kell lennie, és még nem létezik a **SubscriptionId**
- **Terv** [kötelező]
  - Meghatározza a hozzárendelni kívánt terv definícióját
  - A Reference objektum lekérése `Get-AzBlueprint` használatával
- **Hely** [kötelező]
  - Itt adható meg a rendszerszintű felügyelt identitás és előfizetés központi telepítési objektumának régiója, amelyet létre kell hozni
- **Előfizetés** (nem kötelező)
  - Meghatározza azt az előfizetést, amelyre a hozzárendelés telepítve van
  - Ha nincs megadva, az alapértelmezett érték az aktuális előfizetési környezet
- **Zárolás** (nem kötelező)
  - Meghatározza a [terv erőforrás-zárolását](../concepts/resource-locking.md) , amelyet az üzembe helyezett erőforrások számára kíván használni
  - Támogatott beállítások: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Ha nincs megadva, az alapértelmezett érték a _none_
- **SystemAssignedIdentity** (nem kötelező)
  - Ezzel a beállítással rendszerszintű felügyelt identitást hozhat létre a hozzárendeléshez, és üzembe helyezheti az erőforrásokat
  - Az "Identity" paraméter alapértelmezett értéke
  - Nem használható a **UserAssignedIdentity**
- **UserAssignedIdentity** (nem kötelező)
  - Meghatározza a hozzárendeléshez és az erőforrások telepítéséhez használt, felhasználó által hozzárendelt felügyelt identitást.
  - Az "Identity" paraméter része beállítva
  - Nem használható a **SystemAssignedIdentity**
- **Paraméter** (nem kötelező)
  - Kulcs/érték párokat tartalmazó [kivonatoló tábla](/powershell/module/microsoft.powershell.core/about/about_hash_tables) , amely a terv-hozzárendelés [dinamikus paramétereit](../concepts/parameters.md#dynamic-parameters) állítja be
  - A dinamikus paraméterek alapértelmezett értéke a definícióban található **defaultValue**
  - Ha nincs megadva paraméter, és nincs **defaultValue**, a paraméter nem választható

    > [!NOTE]
    > A **paraméter** nem támogatja a secureStrings.

- **ResourceGroupParameter** (nem kötelező)
  - Erőforráscsoport-összetevők [kivonatoló táblázata](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - Minden erőforráscsoport-összetevő helyőrzője kulcs/érték párokkal rendelkezik, amelyekkel dinamikusan állíthatja be az erőforráscsoport-összetevő **nevét** és **helyét** .
  - Ha nincs megadva erőforráscsoport-paraméter, és nincs **defaultValue**, az erőforráscsoport paraméter nem választható
- **AssignmentFile** (nem kötelező)
  - A terv-hozzárendelések JSON-fájlhoz való megjelenítésének elérési útja
  - Ez a paraméter egy olyan PowerShell-paraméterérték része, amely csak a **nevet**, a **tervrajzot**és a **SubscriptionId**tartalmazza, valamint a közös paramétereket.

### <a name="example-1-provide-parameters"></a>1\. példa: Paraméterek megadása

Az alábbi példa új hozzárendelést hoz létre a (z) `Get-AzBlueprint` értékkel beolvasott "My-Blueprint" terv definíciójának "1,1" verziójához, a felügyelt identitás és a hozzárendelési objektum helyét a "westus2" értékre állítja, zárolja az erőforrásokat a _AllResourcesReadOnly_, a és a **ResourceGroupParameter** is beállítja az adott előfizetéshez tartozó és a `{subId}` értékkel rendelkező **paraméterekhez** :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

A terv-hozzárendelések létrehozásának példája a következőképpen néz ki:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>2\. példa JSON-hozzárendelés definíciós fájljának használata

A következő példa majdnem ugyanazt a hozzárendelést hozza létre, mint az [1. példa](#example-1-provide-parameters).
A paramétereknek a parancsmaghoz való átadása helyett a példa egy JSON-hozzárendelés definíciós fájljának és a **AssignmentFile** paraméternek a használatát mutatja be. Emellett a **excludedPrincipals** tulajdonság a **zárolások**részeként van konfigurálva. Nincs PowerShell-paraméter a **excludedPrincipals** számára, és a tulajdonság csak úgy konfigurálható, hogy a JSON-hozzárendelés definíciós fájlján keresztül beállítja.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

## <a name="update-blueprint-assignments"></a>Terv-hozzárendelések frissítése

Előfordulhat, hogy egy már létrehozott terv-hozzárendelést kell frissítenie. A `Set-AzBlueprintAssignment` parancsmag kezeli ezt a műveletet. A (z) parancsmag a `New-AzBlueprintAssignment` parancsmag által megegyező paraméterek többségét veszi figyelembe, ami lehetővé teszi, hogy a hozzárendelésen beállított minden adat frissítve legyen. A kivételek a következők: _név_, _terv_és _SubscriptionId_. Csak a megadott értékek frissülnek.

Annak megismeréséhez, hogy mi történik a terv-hozzárendelések frissítésekor, tekintse meg [a hozzárendelések frissítésének szabályait](./update-existing-assignments.md#rules-for-updating-assignments).

- **Név** [kötelező]
  - A frissítendő terv-hozzárendelés nevét adja meg
  - A frissítendő hozzárendelés megkeresésére használatos, a hozzárendelés módosítása nélkül
- **Terv** [kötelező]
  - Meghatározza a terv-hozzárendelés tervezetének definícióját.
  - A Reference objektum lekérése `Get-AzBlueprint` használatával
  - A frissítendő hozzárendelés megkeresésére használatos, a hozzárendelés módosítása nélkül
- **Hely** (nem kötelező)
  - Itt adható meg a rendszerszintű felügyelt identitás és előfizetés központi telepítési objektumának régiója, amelyet létre kell hozni
- **Előfizetés** (nem kötelező)
  - Meghatározza azt az előfizetést, amelyre a hozzárendelés telepítve van
  - Ha nincs megadva, az alapértelmezett érték az aktuális előfizetési környezet
  - A frissítendő hozzárendelés megkeresésére használatos, a hozzárendelés módosítása nélkül
- **Zárolás** (nem kötelező)
  - Meghatározza a [terv erőforrás-zárolását](../concepts/resource-locking.md) , amelyet az üzembe helyezett erőforrások számára kíván használni
  - Támogatott beállítások: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (nem kötelező)
  - Ezzel a beállítással rendszerszintű felügyelt identitást hozhat létre a hozzárendeléshez, és üzembe helyezheti az erőforrásokat
  - Az "Identity" paraméter alapértelmezett értéke
  - Nem használható a **UserAssignedIdentity**
- **UserAssignedIdentity** (nem kötelező)
  - Meghatározza a hozzárendeléshez és az erőforrások telepítéséhez használt, felhasználó által hozzárendelt felügyelt identitást.
  - Az "Identity" paraméter része beállítva
  - Nem használható a **SystemAssignedIdentity**
- **Paraméter** (nem kötelező)
  - Kulcs/érték párokat tartalmazó [kivonatoló tábla](/powershell/module/microsoft.powershell.core/about/about_hash_tables) , amely a terv-hozzárendelés [dinamikus paramétereit](../concepts/parameters.md#dynamic-parameters) állítja be
  - A dinamikus paraméterek alapértelmezett értéke a definícióban található **defaultValue**
  - Ha nincs megadva paraméter, és nincs **defaultValue**, a paraméter nem választható

    > [!NOTE]
    > A **paraméter** nem támogatja a secureStrings.

- **ResourceGroupParameter** (nem kötelező)
  - Erőforráscsoport-összetevők [kivonatoló táblázata](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - Minden erőforráscsoport-összetevő helyőrzője kulcs/érték párokkal rendelkezik, amelyekkel dinamikusan állíthatja be az erőforráscsoport-összetevő **nevét** és **helyét** .
  - Ha nincs megadva erőforráscsoport-paraméter, és nincs **defaultValue**, az erőforráscsoport paraméter nem választható

A következő példa a zárolási mód módosításával frissíti a "saját terv" tervezetének "1,1" verziójának hozzárendelését `Get-AzBlueprint` értékkel.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

A terv-hozzárendelések létrehozásának példája a következőképpen néz ki:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Terv-hozzárendelések eltávolítása

Ha el szeretné távolítani a terv-hozzárendelés eltávolításának idejét, a `Remove-AzBlueprintAssignment` parancsmag kezeli ezt a műveletet. A parancsmag **neve** vagy **inputobject elemnél** alapján határozza meg, hogy melyik tervrajz-hozzárendelést kívánja eltávolítani. A SubscriptionId _megadása kötelező_ , és minden esetben meg kell adni.

Az alábbi példa egy meglévő terv-hozzárendelést olvas be `Get-AzBlueprintAssignment` értékkel, majd eltávolítja azt az adott előfizetésből, amelyet `{subId}`ként képvisel:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Végpontok közötti kód – példa

Az összes lépés összevonásával a következő példa lekéri a terv definícióját, majd létrehozza, frissíti és eltávolítja a terv-hozzárendelést az adott előfizetésben `{subId}` értékkel:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).