---
title: 'Gyors útmutató: terv létrehozása REST API'
description: Ebben a rövid útmutatóban az Azure-tervezeteket használja az összetevők létrehozásához, definiálásához és üzembe helyezéséhez a REST API használatával.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: eaf6dbb2ff14106ba8d2798d86a8f093855de85e
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915624"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-rest-api"></a>Gyors útmutató: Azure Blueprint definiálása és hozzárendelése REST API

A tervrajzok létrehozásának és hozzárendelésének megismerése lehetővé teszi a közös minták meghatározását, amelyekkel a Azure Resource Manager sablonok (ARM-sablonok), a házirendek, a biztonság és egyebek alapján újrafelhasználható és gyorsan telepíthető konfigurációk fejleszthetők. Ez az oktatóanyag bemutatja, hogyan hajthatja végre az Azure Blueprints használatával a tervek a szervezeten belüli létrehozásával, közzétételével és hozzárendelésével kapcsolatos olyan általános feladatokat, mint az alábbiak:

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.
- Regisztrálja az `Microsoft.Blueprint` erőforrás-szolgáltatót. Az utasításokért lásd: [erőforrás-szolgáltatók és típusok](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Ismerkedés a REST API-val

Ha még nem ismeri a REST API-t, első lépésként tekintse át az [Azure REST API-referenciát](/rest/api/azure/) a REST API általános bemutatásáért, és fordítson különös figyelmet a kérések URI-azonosítójára és törzsére. Ebben a cikkben ezeknek a fogalmaknak a segítségével adunk útmutatást az Azure Blueprints-tervek használatához, és feltételezzük, hogy behatóan ismeri a fogalmakat. Az [ARMClient](https://github.com/projectkudu/ARMClient) és hasonló eszközökkel automatikusan kezelhető az engedélyezés, ezért a kezdők számára mindenképp javasoljuk ezek használatát.

Az Azure-tervezetekhez tartozó specifikációkat lásd: [Azure-tervezetek REST API](/rest/api/blueprints/).

### <a name="rest-api-and-powershell"></a>A REST API és a PowerShell

Ha még nem választott eszközt a REST API-hívások kezeléséhez, ennek az útmutatónak a keretében érdemes a PowerShellt használnia. Az alábbiakban egy mintafejlécet mutatunk be az Azure-beli hitelesítéshez. Hozzon létre egy hitelesítési fejlécet, vagy más néven **tulajdonosi jogkivonatot**, és adja meg a kapcsolódáshoz szükséges REST API URI-t a paraméterekkel vagy egy **kérelemtörzzsel**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

A **$restUri** változóban a `{subscriptionId}` helyére írja be a saját előfizetését az információkérés tárgyaként. A $response változó tárolja az `Invoke-RestMethod` parancsmag által visszaadott eredményt, amely a [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) és hasonló parancsmagok által elemezhető. Ha a REST API szolgáltatásvégpontja **kérelemtörzset** vár, adjon meg egy JSON formátumú változót az `Invoke-RestMethod``-Body` paraméterében.

## <a name="create-a-blueprint"></a>Terv létrehozása

A megfelelőségi szabványminták definiálásának első lépése, hogy összeállítunk egy tervet az elérhető erőforrásokból. Létrehozzuk a „MyBlueprint” nevű tervet az előfizetés szerepkör- és szabályzat-hozzárendeléseinek konfigurálására. Ezután hozzáadunk egy erőforráscsoportot, egy ARM-sablont és egy szerepkör-hozzárendelést az erőforráscsoporthoz.

> [!NOTE]
> A REST API használata esetén a _terv_ objektumot hozzuk először létre. Mindegyik hozzáadott, paraméterekkel rendelkező _összetevő_ esetében a paramétereket előre definiálni kell a kezdeti _terven_.

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{YourMG}` -Csere a felügyeleti csoportjának azonosítójával
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

> [!NOTE]
> A tervrajzok az előfizetés szintjén is létrehozhatók. Ha szeretné megtekinteni egy példát, tekintse meg a [terv létrehozása az előfizetési példában](/rest/api/blueprints/blueprints/createorupdate#subscriptionblueprint)című témakört.

1. Hozza létre a kezdeti _terv_ objektumot. A **kérelemtörzs** a terv, az esetleg létrehozandó erőforráscsoportok, valamint az összes tervszintű paraméter tulajdonságait tartalmazza. A paraméterek a hozzárendelés során vannak megadva, és a későbbi lépésekben hozzáadott összetevők használják azokat.

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - Kérelem törzse

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
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
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
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

1. Szerepkör-hozzárendelés hozzáadása az előfizetésben. A **kérelemtörzs** határozza meg az összetevő _altípusát_, a tulajdonságok a szerepkör-definíció azonosítójához igazodnak, és az egyszerű identitások értéktömbökként vannak továbbadva. Az alábbi példában a megadott szerepkörrel felruházott egyszerű identitások egy olyan paraméterre vannak konfigurálva, amely a tervhozzárendelés során van megadva. Ez a példa a _közreműködő_ beépített szerepkört használja a következő GUID azonosítóval: `b24988ac-6180-42a0-ab88-20f7382dd24c` .

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - Kérelem törzse

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Szabályzat-hozzárendelés hozzáadása az előfizetésben. A **kérelemtörzs** határozza meg az összetevő _altípusát_, a szabályzat- vagy kezdeményezési definíciókhoz igazodó tulajdonságokat, és konfigurálja a szabályzat-hozzárendelést, hogy a tervhozzárendelés során konfigurált definiált tervparamétereket használja. Ez a példa az _Apply címkét és az alapértelmezett értékét_ használja a beépített szabályzathoz a (z) GUID azonosítóval `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - Kérelem törzse

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. Egy másik szabályzat-hozzárendelés hozzáadása egy Storage-címke számára (a _storageAccountType_ paraméter ismételt felhasználásával) az előfizetésen. Ez az újabb szabályzat-hozzárendelési összetevő bemutatja, hogy a terveken definiált paramétereket több összetevő is használhatja. A példában a **storageAccountType** használatával beállítunk egy címkét az erőforráscsoporton. Ez az érték a következő lépésben létrehozott tárfiókkal kapcsolatos információkat szolgáltat. Ez a példa az _Apply címkét és az alapértelmezett értékét_ használja a beépített szabályzathoz a (z) GUID azonosítóval `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - Kérelem törzse

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. Sablon hozzáadása az erőforráscsoport alatt. Az ARM-sablonhoz tartozó **kérelem törzse** tartalmazza a sablon normál JSON összetevőjét, és meghatározza a cél erőforráscsoportot a **Properties. resourceGroup**. A sablon újra felhasználja a **storageAccountType**, a **tagName** és a **tagValue** tervparamétert is, mivel továbbadja azokat a sablonnak. A tervparaméterek a **properties.parameters** definiálásával válnak elérhetővé a sablon számára, és a sablon JSON-fájljában az érték beszúrása ezzel a kulcs–érték párral történik. A terv- és a sablonparaméterek neve egyezhet, itt most azonban megkülönböztettük őket, hogy bemutassuk, hogyan lesznek átadva a tervből a sablonösszetevőbe.

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - Kérelem törzse

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
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
                     "location": "[resourceGroups('storageRG').location]",
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
             },
             "resourceGroup": "storageRG",
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
     }
     ```

1. Szerepkör-hozzárendelés hozzáadása az erőforráscsoport alatt. Az előző szerepkör-hozzárendelési bejegyzéshez hasonlóan az alábbi példa a **Tulajdonos** szerepkör definíciós azonosítóját használja, és egy másik paramétert ad neki a tervből. Ez a példa a _tulajdonos_ beépített szerepkörét használja egy GUID-azonosítóval `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` .

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - Kérelem törzse

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

## <a name="publish-a-blueprint"></a>Terv közzététele

Most, hogy az összetevők hozzá lettek adva a tervhez, itt az idő közzétenni azt. A közzététellel a terv szabadon hozzárendelhető lesz az előfizetésekhez.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

A `{BlueprintVersion}` értéke egy betűket, számokat és kötőjeleket (szóközöket vagy speciális karaktereket nem) tartalmazó, legfeljebb 20 karakter hosszúságú sztring. Használjon egyedi, és jelentéssel bíró értékeket, például: **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Terv hozzárendelése

Miután a REST API használatával közzétett egy tervet, az hozzárendelhető az előfizetésekhez. A létrehozott tervet a felügyeleti csoport hierarchiájában rendelheti hozzá az egyik előfizetéshez. Ha a terv egy előfizetésre lett mentve, akkor csak az adott előfizetéshez rendelhető hozzá. A **kérelemtörzs** megadja a hozzárendelni kívánt tervet, a tervdefinícióban lévő erőforráscsoportok nevét és helyét, valamint a terven definiált és egy vagy több csatolt összetevő által használt összes paramétert.

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{tenantId}` – Cserélje le a-t a bérlői AZONOSÍTÓra
- `{YourMG}` -Csere a felügyeleti csoportjának azonosítójával
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

1. Adja az Azure Blueprints-szolgáltatásnévnek a **Tulajdonos** szerepkört a célelőfizetésen. A AppId statikus ( `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` ), de a szolgáltatás egyszerű azonosítója a bérlőtől függ. A bérlőre vonatkozó adatok a következő REST API használatával kérhetők le. Más engedélyekkel rendelkező [Azure Active Directory Graph APIt](../../active-directory/develop/active-directory-graph-api.md)használ.

   - REST API URI

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. A tervpéldányt a futtatásához rendelje hozzá egy előfizetéshez. Mivel a **contributors** és az **owners** paraméterben a szerepkör-hozzárendelésben részesülő szolgáltatásnevek objektumazonosítóinak egy tömbjét kell megadni, az [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) használatával gyűjtse össze a **kérelemtörzsben** a saját felhasználókra, csoportokra vagy szolgáltatásnevekre használni kívánt objektumazonosítókat.

   - REST API URI

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - Kérelem törzse

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

   - Felhasználó által hozzárendelt felügyelt identitás

     A tervrajz-hozzárendelések [felhasználó által hozzárendelt felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md)is használhatnak.
     Ebben az esetben a kérelem törzsének **identitás** része a következőképpen változik. Cserélje le az és az tulajdonságot az `{yourRG}` `{userIdentity}` erőforráscsoport nevére és a felhasználó által hozzárendelt felügyelt identitás nevére.

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

Eltávolíthatja a terveket az előfizetésekből. Az eltávolítás gyakori művelet az összetevők már szükségtelen erőforrásai esetén. Az egyes tervek eltávolításakor az adott tervek keretében hozzárendelt összetevők megmaradnak. A tervhozzárendelések törléséhez használja a következő REST API-műveletet:

- REST API URI

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

### <a name="delete-a-blueprint"></a>Terv törlése

Maguknak a terveknek a törléséhez használja a következő REST API-műveletet:

- REST API URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott, hozzárendelt és eltávolított egy tervet REST APIsal. Ha többet szeretne megtudni az Azure-tervezetekről, folytassa a terv életciklusával foglalkozó cikkel.

> [!div class="nextstepaction"]
> [A terv életciklusának megismerése](./concepts/lifecycle.md)