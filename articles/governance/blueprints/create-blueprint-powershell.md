---
title: 'Rövid útmutató: terv létrehozása a PowerShell-lel'
description: Ebben a rövid útmutatóban az Azure-tervezeteket használja az összetevők létrehozásához, definiálásához és üzembe helyezéséhez a PowerShell használatával.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 65d573d0aec7d5f292bc985483e1f12c350ae03a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918279"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Gyors útmutató: Azure Blueprint megadása és hozzárendelése a PowerShell-lel

A tervrajzok létrehozásának és hozzárendelésének megismerése lehetővé teszi a közös minták meghatározását, amelyekkel a Azure Resource Manager sablonok (ARM-sablonok), a házirendek, a biztonság és egyebek alapján újrafelhasználható és gyorsan telepíthető konfigurációk fejleszthetők. Ez az oktatóanyag bemutatja, hogyan hajthatja végre az Azure Blueprints használatával a tervek a szervezeten belüli létrehozásával, közzétételével és hozzárendelésével kapcsolatos olyan általános feladatokat, mint az alábbiak:

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.
- Ha még nincs telepítve, kövesse az [az. Blueprint modul hozzáadása](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) című témakör utasításait az az **. Blueprint** modul telepítéséhez és ellenőrzéséhez a PowerShell-Galéria.
- Ha korábban még nem használta az Azure-tervezeteket, regisztrálja az erőforrás-szolgáltatót Azure PowerShell a használatával `Register-AzResourceProvider -ProviderNamespace Microsoft.Blueprint` .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Terv létrehozása

A megfelelőségi szabványminták definiálásának első lépése, hogy összeállítunk egy tervet az elérhető erőforrásokból. Létrehozzuk a „MyBlueprint” nevű tervet az előfizetés szerepkör- és szabályzat-hozzárendeléseinek konfigurálására. Ezután hozzáadunk egy erőforráscsoportot, egy ARM-sablont és egy szerepkör-hozzárendelést az erőforráscsoporthoz.

> [!NOTE]
> A PowerShell használatakor a _terv_ objektum először jön létre. Mindegyik hozzáadott, paraméterekkel rendelkező _összetevő_ esetében a paramétereket előre definiálni kell a kezdeti _terven_.

1. Hozza létre a kezdeti _terv_ objektumot. A **BlueprintFile** paraméter egy JSON-fájlt hoz létre, amely a tervre, a létrehozandó erőforráscsoportok és az összes tervrajzi szintű paraméterre vonatkozó tulajdonságokat tartalmaz. A paraméterek a hozzárendelés során vannak megadva, és a későbbi lépésekben hozzáadott összetevők használják azokat.

   - JSON-fájl – blueprint.jsbekapcsolva

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Ha programozott módon hozza létre a terv-definíciókat, használja a filename _blueprint.js_ .
     > Ez a fájlnév az [import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact)hívásakor használatos.

     A terv objektum alapértelmezés szerint az alapértelmezett előfizetésben jön létre. A felügyeleti csoport megadásához használja a **ManagementGroupId** paramétert. Az előfizetés megadásához használja a **SubscriptionId** paramétert.

1. Szerepkör-hozzárendelés hozzáadása az előfizetésben. A **ArtifactFile** meghatározza az összetevő _típusát_ , a tulajdonságokat a szerepkör-definíciós azonosítóhoz igazítva, a résztvevő identitásokat pedig értékek tömbje adja át. Az alábbi példában a megadott szerepkörrel felruházott egyszerű identitások egy olyan paraméterre vannak konfigurálva, amely a tervhozzárendelés során van megadva. Ez a példa a _közreműködő_ beépített szerepkört használja a következő GUID azonosítóval: `b24988ac-6180-42a0-ab88-20f7382dd24c` .

   - JSON-fájl – \artifacts\roleContributor.jsbekapcsolva

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Szabályzat-hozzárendelés hozzáadása az előfizetésben. A **ArtifactFile** határozza meg az összetevő _típusát_ , a házirendhez vagy kezdeményezési definícióhoz igazított tulajdonságokat, és konfigurálja a szabályzat-hozzárendelést a terv-hozzárendelés során a definiált terv paramétereinek használatára. Ez a példa az _Apply címkét és az alapértelmezett értékét_ használja a beépített szabályzathoz a (z) GUID azonosítóval `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - JSON-fájl – \artifacts\policyTags.jsbekapcsolva

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Egy másik szabályzat-hozzárendelés hozzáadása egy Storage-címke számára (a _storageAccountType_ paraméter ismételt felhasználásával) az előfizetésen. Ez az újabb szabályzat-hozzárendelési összetevő bemutatja, hogy a terveken definiált paramétereket több összetevő is használhatja. A példában a **storageAccountType** használatával beállítunk egy címkét az erőforráscsoporton. Ez az érték a következő lépésben létrehozott tárfiókkal kapcsolatos információkat szolgáltat. Ez a példa az _Apply címkét és az alapértelmezett értékét_ használja a beépített szabályzathoz a (z) GUID azonosítóval `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - JSON-fájl – \artifacts\policyStorageTags.jsbekapcsolva

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Sablon hozzáadása az erőforráscsoport alatt. Az ARM-sablonok **TemplateFile** a sablon normál JSON összetevőjét tartalmazza. A sablon újra felhasználja a **storageAccountType**, a **tagName** és a **tagValue** tervparamétert is, mivel továbbadja azokat a sablonnak. A terv paramétereinek elérhetők a sablonhoz a **TemplateParameterFile** paraméterrel és a sablon JSON-ban, amelyet a kulcs-érték párok az érték beadására használnak. A terv és a sablon paramétereinek nevei megegyeznek.

   - JSON ARM-sablon fájlja – \artifacts\templateStorage.jsbekapcsolva

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON ARM-sablon paraméter fájl – \artifacts\templateStorageParams.jsbekapcsolva

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Szerepkör-hozzárendelés hozzáadása az erőforráscsoport alatt. Az előző szerepkör-hozzárendelési bejegyzéshez hasonlóan az alábbi példa a **Tulajdonos** szerepkör definíciós azonosítóját használja, és egy másik paramétert ad neki a tervből. Ez a példa a _tulajdonos_ beépített szerepkörét használja egy GUID-azonosítóval `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` .

   - JSON-fájl – \artifacts\roleOwner.jsbekapcsolva

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Terv közzététele

Most, hogy az összetevők hozzá lettek adva a tervhez, itt az idő közzétenni azt. A közzététellel a terv szabadon hozzárendelhető lesz az előfizetésekhez.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

A `{BlueprintVersion}` értéke egy betűket, számokat és kötőjeleket (szóközöket vagy speciális karaktereket nem) tartalmazó, legfeljebb 20 karakter hosszúságú sztring. Használjon egyedi, és jelentéssel bíró értékeket, például: **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Terv hozzárendelése

Miután közzétett egy tervet a PowerShell használatával, hozzárendelhető egy előfizetéshez. A létrehozott tervet a felügyeleti csoport hierarchiájában rendelheti hozzá az egyik előfizetéshez. Ha a terv egy előfizetésre lett mentve, akkor csak az adott előfizetéshez rendelhető hozzá. A **terv** paraméter határozza meg a hozzárendelni kívánt tervet. A név, hely, identitás, zárolás és tervrajz paramétereinek megadásához használja a parancsmag megfelelő PowerShell-paramétereit, `New-AzBlueprintAssignment` vagy adja meg azokat a **ASSIGNMENTFILE** paraméter JSON-fájljában.

1. A tervpéldányt a futtatásához rendelje hozzá egy előfizetéshez. Mivel a **közreműködők** és a **tulajdonosi** paraméterek a rendszerbiztonsági tag objectIds tömbjét igénylik a szerepkör-hozzárendelés megadásához, használja [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) , hogy összegyűjtse a saját felhasználók, csoportok vagy egyszerű szolgáltatások **AssignmentFile** használt objectIds.

   - JSON-fájl – blueprintAssignment.jsbekapcsolva

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - PowerShell-parancs

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Felhasználó által hozzárendelt felügyelt identitás

     A tervrajz-hozzárendelések [felhasználó által hozzárendelt felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md)is használhatnak.
     Ebben az esetben a JSON-hozzárendelés fájljának **identitás** része a következőképpen változik. Cserélje le a,, `{tenantId}` `{subscriptionId}` `{yourRG}` , és a- `{userIdentity}` t a tenantId, a subscriptionId, az erőforráscsoport nevére és a felhasználó által hozzárendelt felügyelt identitás nevére.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     A **felhasználó által hozzárendelt felügyelt identitás** bármely előfizetésben és erőforráscsoportban lehet, hogy a tervhez hozzárendelt felhasználó rendelkezik jogosultsággal a következőhöz:.

     > [!IMPORTANT]
     > Az Azure-tervrajzok nem kezelik a felhasználó által hozzárendelt felügyelt identitást. A felhasználók feladata a megfelelő szerepkörök és engedélyek kiosztása, vagy a terv hozzárendelése sikertelen lesz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="unassign-a-blueprint"></a>Terv hozzárendelésének megszüntetése

Eltávolíthatja a terveket az előfizetésekből. Az eltávolítás gyakori művelet az összetevők már szükségtelen erőforrásai esetén. Az egyes tervek eltávolításakor az adott tervek keretében hozzárendelt összetevők megmaradnak. A tervrajzok hozzárendelésének eltávolításához használja a következő `Remove-AzBlueprintAssignment` parancsmagot:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta, hozzárendelte és eltávolította a tervet a PowerShell-lel. Ha többet szeretne megtudni az Azure-tervezetekről, folytassa a terv életciklusával foglalkozó cikkel.

> [!div class="nextstepaction"]
> [A terv életciklusának megismerése](./concepts/lifecycle.md)