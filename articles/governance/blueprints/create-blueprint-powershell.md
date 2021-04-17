---
title: 'Rövid útmutató: Terv létrehozása a PowerShell használatával'
description: Ebben a rövid útmutatóban a Azure Blueprints, definiálja és üzembe helyezheti az összetevőket a PowerShell használatával.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-api
ms.openlocfilehash: 29c43da0c1467c74ddc85a447a0cf9addb574c7e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538900"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Rövid útmutató: Azure Blueprints-terv definiálás és hozzárendelése a PowerShell használatával

A tervek létrehozási és hozzárendelési módszerének elsajátítása lehetővé teszi a gyakori minták meghatározását, hogy újrahasználható és gyorsan üzembe helyezhető konfigurációkat fejlessze ki Azure Resource Manager-sablonok (ARM-sablonok), szabályzatok, biztonság stb. alapján. Ez az oktatóanyag bemutatja, hogyan hajthatja végre az Azure Blueprints használatával a tervek a szervezeten belüli létrehozásával, közzétételével és hozzárendelésével kapcsolatos olyan általános feladatokat, mint az alábbiak:

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.
- Ha még nincs telepítve, kövesse az [Az.Blueprint](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) modul hozzáadása modul utasításait az **Az.Blueprint** modul telepítéséhez és érvényesítéséhez a PowerShell-galéria.
- Ha még nem használta a Azure Blueprints, regisztrálja az erőforrás-szolgáltatót a Azure PowerShell `Register-AzResourceProvider -ProviderNamespace Microsoft.Blueprint` segítségével.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Terv létrehozása

A megfelelőségi szabványminták definiálásának első lépése, hogy összeállítunk egy tervet az elérhető erőforrásokból. Létrehozzuk a „MyBlueprint” nevű tervet az előfizetés szerepkör- és szabályzat-hozzárendeléseinek konfigurálására. Ezután hozzáadunk egy erőforráscsoportot, egy ARM-sablont és egy szerepkör-hozzárendelést az erőforráscsoporthoz.

> [!NOTE]
> A PowerShell használata esetén először _a tervobjektum_ jön létre. Mindegyik hozzáadott, paraméterekkel rendelkező _összetevő_ esetében a paramétereket előre definiálni kell a kezdeti _terven_.

1. Hozza létre a kezdeti _terv_ objektumot. A **BlueprintFile** paraméter egy JSON-fájlt vesz fel, amely tartalmazza a terv tulajdonságait, a létrehozni kívánt erőforráscsoportokat és az összes tervszintű paramétert. A paraméterek a hozzárendelés során vannak megadva, és a későbbi lépésekben hozzáadott összetevők használják azokat.

   - JSON-fájl – blueprint.jsbe

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
     > A tervdefiníciók _programozottblueprint.js_ létrehozásakor használja a fájlnevet.
     > Ezt a fájlnevet az [Import-AzBlueprintWithArtifact hívásakor használja a rendszer.](/powershell/module/az.blueprint/import-azblueprintwithartifact)

     A tervobjektum alapértelmezés szerint az alapértelmezett előfizetésben jön létre. A felügyeleti csoport megadásához használja a **ManagementGroupId paramétert.** Az előfizetés megadásához használja a **SubscriptionId paramétert.**

1. Szerepkör-hozzárendelés hozzáadása az előfizetésben. Az **ArtifactFile** határozza  meg az összetevő fajtáját, a tulajdonságok igazodnak a szerepkör-definíció azonosítóhoz, és a rendszer értéktömbként adja át a fő identitásokat. Az alábbi példában a megadott szerepkörrel felruházott egyszerű identitások egy olyan paraméterre vannak konfigurálva, amely a tervhozzárendelés során van megadva. Ebben a példában a _Közreműködő_ beépített szerepkört használjuk a GUID `b24988ac-6180-42a0-ab88-20f7382dd24c` azonosítóval.

   - JSON-fájl – \artifacts\roleContributor.jsbe

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

1. Szabályzat-hozzárendelés hozzáadása az előfizetésben. Az **ArtifactFile** határozza  meg az összetevő fajtáját, a szabályzat- vagy kezdeményezési definíciókhoz igazodó tulajdonságokat, és konfigurálja a szabályzat-hozzárendelést a terv hozzárendelése során konfigurált megadott tervparaméterek használatára. Ez a példa az _Apply (Címke alkalmazása) és annak alapértelmezett_ értékét használja a (GUID) GUID azonosítóval megadott beépített erőforráscsoportokra. `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`

   - JSON-fájl – \artifacts\policyTags.jsbe

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

1. Egy másik szabályzat-hozzárendelés hozzáadása egy Storage-címke számára (a _storageAccountType_ paraméter ismételt felhasználásával) az előfizetésen. Ez az újabb szabályzat-hozzárendelési összetevő bemutatja, hogy a terveken definiált paramétereket több összetevő is használhatja. A példában a **storageAccountType** használatával beállítunk egy címkét az erőforráscsoporton. Ez az érték a következő lépésben létrehozott tárfiókkal kapcsolatos információkat szolgáltat. Ez a példa az _Apply (Címke alkalmazása) címkét_ és annak alapértelmezett értékét használja az erőforráscsoportok beépített szabályzatán a GUID azonosítóval. `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`

   - JSON-fájl – \artifacts\policyStorageTags.jsbe

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

1. Sablon hozzáadása az erőforráscsoport alatt. Az **ARM-sablon TemplateFile** fájlja tartalmazza a sablon normál JSON-összetevőjét. A sablon újra felhasználja a **storageAccountType**, a **tagName** és a **tagValue** tervparamétert is, mivel továbbadja azokat a sablonnak. A tervparaméterek **a TemplateParameterFile** paraméterrel érhetők el a sablon számára, a sablon JSON-fájljában pedig a kulcs-érték pár használatával lehet beinjektálni az értéket. A terv- és sablonparaméterek nevei azonosak is lehet.

   - JSON ARM-sablonfájl – \artifacts\templateStorage.jsbe

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

   - JSON ARM-sablon paraméterfájlja – \artifacts\templateStorageParams.jsbe

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

1. Szerepkör-hozzárendelés hozzáadása az erőforráscsoport alatt. Az előző szerepkör-hozzárendelési bejegyzéshez hasonlóan az alábbi példa a **Tulajdonos** szerepkör definíciós azonosítóját használja, és egy másik paramétert ad neki a tervből. Ez a példa a _Tulajdonos_ beépített szerepkört használja a GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` azonosítóval.

   - JSON-fájl – \artifacts\roleOwner.jsbe

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

Miután közzétett egy tervet a PowerShell használatával, az hozzárendelhető egy előfizetéshez. A létrehozott tervet a felügyeleti csoport hierarchiájában rendelheti hozzá az egyik előfizetéshez. Ha a terv egy előfizetésbe van mentve, csak az előfizetéshez rendelhető hozzá. A **Blueprint paraméter** határozza meg a hozzárendelni szükséges tervet. A név, a hely, az identitás, a zárolás és a tervparaméterek kiosztásához használja az egyező PowerShell-paramétereket a parancsmagon, vagy adja meg őket a `New-AzBlueprintAssignment` **AssignmentFile** paraméter JSON-fájljában.

1. A tervpéldányt a futtatásához rendelje hozzá egy előfizetéshez. Mivel  a közreműködők és a tulajdonosok paramétereihez a szerepkör-hozzárendelést kapó rendszerbiztonsági résztvevők objectId-tömbje szükséges, az [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) használatával gyűjtheti össze a **AssignmentFile-ban** való használathoz szükséges objectId-eket a saját felhasználói, csoportjai vagy szolgáltatásnévi számára. 

   - JSON-fájl – blueprintAssignment.jsbe

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

     A terv-hozzárendelések felhasználó által hozzárendelt felügyelt [identitást is használhatnak.](../../active-directory/managed-identities-azure-resources/overview.md)
     Ebben az esetben  a JSON-hozzárendelési fájl identitás része a következőképpen változik. Cserélje le a , , és értékeket `{tenantId}` `{subscriptionId}` a `{yourRG}` bérlőazonosítóra, a subscriptionId azonosítóra, az erőforráscsoport nevére, illetve a felhasználó által hozzárendelt felügyelt identitás `{userIdentity}` nevére.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     A **felhasználó által hozzárendelt felügyelt identitás** bármely előfizetésben és erőforráscsoportban lehet, amelyhez a tervet hozzárendelő felhasználó engedélyekkel rendelkezik.

     > [!IMPORTANT]
     > Azure Blueprints nem a felhasználó által hozzárendelt felügyelt identitást kezeli. A felhasználók felelnek a megfelelő szerepkörök és engedélyek hozzárendeléséért, különben a terv hozzárendelése sikertelen lesz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="unassign-a-blueprint"></a>Terv hozzárendelésének megszüntetése

Eltávolíthatja a terveket az előfizetésekből. Az eltávolítás gyakori művelet az összetevők már szükségtelen erőforrásai esetén. Az egyes tervek eltávolításakor az adott tervek keretében hozzárendelt összetevők megmaradnak. Terv hozzárendelésének eltávolításához használja a `Remove-AzBlueprintAssignment` parancsmagot:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott, hozzárendelt és eltávolított egy tervet a PowerShell használatával. Ha többet szeretne megtudni a Azure Blueprints, folytassa a terv életciklusával kapcsolatos cikkel.

> [!div class="nextstepaction"]
> [Tudnivalók a tervek életciklusról](./concepts/lifecycle.md)
