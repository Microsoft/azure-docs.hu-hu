---
title: 'Rövid útmutató: terv létrehozása az Azure CLI-vel'
description: Ebben a rövid útmutatóban Azure-tervezeteket használ az összetevők létrehozásához, definiálásához és üzembe helyezéséhez az Azure CLI használatával.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: fbe5c12f1c94d4b59dbdc2a97b6a4cb9af5a2328
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563667"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Gyors útmutató: Azure Blueprint megadása és hozzárendelése az Azure CLI-vel

A tervrajzok létrehozásának és hozzárendelésének megismerése lehetővé teszi a közös minták meghatározását, amelyekkel a Azure Resource Manager sablonok (ARM-sablonok), a házirendek, a biztonság és egyebek alapján újrafelhasználható és gyorsan telepíthető konfigurációk fejleszthetők. Ez az oktatóanyag bemutatja, hogyan hajthatja végre az Azure Blueprints használatával a tervek a szervezeten belüli létrehozásával, közzétételével és hozzárendelésével kapcsolatos olyan általános feladatokat, mint az alábbiak:

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.
- Ha korábban még nem használta az Azure-tervezeteket, regisztrálja az erőforrás-szolgáltatót az Azure CLI-n keresztül a használatával `az provider register --namespace Microsoft.Blueprint` .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>A terv kiterjesztésének hozzáadása

Ha engedélyezni szeretné az Azure CLI-t a terv-definíciók és-hozzárendelések kezeléséhez, a bővítményt hozzá kell adni.
Ez a bővítmény mindenhol működik, ahol az Azure CLI használható, beleértve a [Basht Windows 10-en](/windows/wsl/install-win10), a [Cloud Shellt](https://shell.azure.com) (különállón és portálon belülin egyaránt), az [Azure CLI Docker-rendszerképet](https://hub.docker.com/_/microsoft-azure-cli), vagy akár helyileg telepítve is.

1. Győződjön meg arról, hogy a legújabb Azure CLI telepítve van (legalább **2.0.76**). Ha még nincs telepítve, kövesse [ezeket az utasításokat](/cli/azure/install-azure-cli-windows).

1. A választott Azure CLI környezetben importálja a bővítményt a következő paranccsal:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Ellenőrizze, hogy a bővítmény telepítve van-e, és a várt verzió-e (legalább **0.1.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Terv létrehozása

A megfelelőségi szabványminták definiálásának első lépése, hogy összeállítunk egy tervet az elérhető erőforrásokból. Létrehozzuk a „MyBlueprint” nevű tervet az előfizetés szerepkör- és szabályzat-hozzárendeléseinek konfigurálására. Ezután hozzáadunk egy erőforráscsoportot, egy ARM-sablont és egy szerepkör-hozzárendelést az erőforráscsoporthoz.

> [!NOTE]
> Az Azure CLI használatakor a _terv_ objektum először jön létre. Mindegyik hozzáadott, paraméterekkel rendelkező _összetevő_ esetében a paramétereket előre definiálni kell a kezdeti _terven_.

1. Hozza létre a kezdeti _terv_ objektumot. A **Parameters** paraméter egy JSON-fájlt fog tartalmazni, amely tartalmazza az összes terv szintű paramétert. A paraméterek a hozzárendelés során vannak megadva, és a későbbi lépésekben hozzáadott összetevők használják azokat.

   - JSON-fájl – blueprintparms.jsbekapcsolva

     ```json
     {
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
     }
     ```

   - Azure CLI-parancs

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > A terv definícióinak importálásakor használja a filename _blueprint.js_ .
     > Ezt a fájlnevet a [terv importálásának](/cli/azure/ext/blueprint/blueprint#ext_blueprint_az_blueprint_import)meghívásakor használja a rendszer.

     A terv objektum alapértelmezés szerint az alapértelmezett előfizetésben jön létre. A felügyeleti csoport megadásához használja a **managementgroup** paramétert. Az előfizetés megadásához használja a paraméter- **előfizetést**.

1. Adja hozzá a tárolási összetevőkhöz tartozó erőforráscsoportot a definícióhoz.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Szerepkör-hozzárendelés hozzáadása az előfizetésben. Az alábbi példában a megadott szerepkörrel felruházott egyszerű identitások egy olyan paraméterre vannak konfigurálva, amely a tervhozzárendelés során van megadva. Ez a példa a _közreműködő_ beépített szerepkört használja a következő GUID azonosítóval: `b24988ac-6180-42a0-ab88-20f7382dd24c` .

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Szabályzat-hozzárendelés hozzáadása az előfizetésben. Ez a példa az _Apply címkét és az alapértelmezett értékét_ használja a beépített szabályzathoz a (z) GUID azonosítóval `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - JSON-fájl – artifacts\policyTags.jsbekapcsolva

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLI-parancs

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

     > [!NOTE]
     > Mac gépen történő használata esetén `az blueprint` a helyére `\` írja `/` be az elérési utat tartalmazó paraméterek értékét. Ebben az esetben a **Paraméterek** értéke a következő lesz: `artifacts/policyTags.json` .

1. Egy másik szabályzat-hozzárendelés hozzáadása egy Storage-címke számára (a _storageAccountType_ paraméter ismételt felhasználásával) az előfizetésen. Ez az újabb szabályzat-hozzárendelési összetevő bemutatja, hogy a terveken definiált paramétereket több összetevő is használhatja. A példában a **storageAccountType** használatával beállítunk egy címkét az erőforráscsoporton. Ez az érték a következő lépésben létrehozott tárfiókkal kapcsolatos információkat szolgáltat. Ez a példa az _Apply címkét és az alapértelmezett értékét_ használja a beépített szabályzathoz a (z) GUID azonosítóval `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - JSON-fájl – artifacts\policyStorageTags.jsbekapcsolva

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Azure CLI-parancs

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

     > [!NOTE]
     > Mac gépen történő használata esetén `az blueprint` a helyére `\` írja `/` be az elérési utat tartalmazó paraméterek értékét. Ebben az esetben a **Paraméterek** értéke a következő lesz: `artifacts/policyStorageTags.json` .

1. Sablon hozzáadása az erőforráscsoport alatt. Az ARM-sablonhoz tartozó **template** paraméter tartalmazza a sablon normál JSON-összetevőit. A sablon újra felhasználja a **storageAccountType**, a **tagName** és a **tagValue** tervparamétert is, mivel továbbadja azokat a sablonnak. A terv paraméterei a sablonhoz a paraméter **paramétereinek** használatával és a sablon JSON-ban érhetők el, amelyet a kulcs-érték párok az érték beadására használnak. A terv és a sablon paramétereinek nevei megegyeznek.

   - JSON ARM-sablon fájlja – artifacts\templateStorage.jsbekapcsolva

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

   - JSON ARM-sablon paraméter fájl – artifacts\templateStorageParams.jsbekapcsolva

     ```json
     {
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
     ```

   - Azure CLI-parancs

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

     > [!NOTE]
     > Mac gépen történő használata esetén `az blueprint` a helyére `\` írja `/` be az elérési utat tartalmazó paraméterek értékét. Ebben az esetben a **sablon** értéke lesz `artifacts/templateStorage.json` , és a **Paraméterek** válnak `artifacts/templateStorageParams.json` .

1. Szerepkör-hozzárendelés hozzáadása az erőforráscsoport alatt. Az előző szerepkör-hozzárendelési bejegyzéshez hasonlóan az alábbi példa a **Tulajdonos** szerepkör definíciós azonosítóját használja, és egy másik paramétert ad neki a tervből. Ez a példa a _tulajdonos_ beépített szerepkörét használja egy GUID-azonosítóval `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` .

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>Terv közzététele

Most, hogy az összetevők hozzá lettek adva a tervhez, itt az idő közzétenni azt. A közzététellel a terv szabadon hozzárendelhető lesz az előfizetésekhez.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

A `{BlueprintVersion}` értéke egy betűket, számokat és kötőjeleket (szóközöket vagy speciális karaktereket nem) tartalmazó, legfeljebb 20 karakter hosszúságú sztring. Használjon valami egyedi és tájékoztató jellegű, például **v20200605-135541**.

## <a name="assign-a-blueprint"></a>Terv hozzárendelése

Miután közzétett egy tervet az Azure CLI-vel, hozzárendelhető egy előfizetéshez. A létrehozott tervet a felügyeleti csoport hierarchiájában rendelheti hozzá az egyik előfizetéshez. Ha a terv egy előfizetésre lett mentve, akkor csak az adott előfizetéshez rendelhető hozzá. A **Blueprint-Name** paraméter meghatározza a hozzárendelni kívánt tervet. A név, hely, identitás, zárolás és tervrajz paramétereinek megadásához használja a parancshoz illő Azure CLI-paramétereket, `az blueprint assignment create` vagy adja meg azokat  a JSON-fájlban.

1. A tervpéldányt a futtatásához rendelje hozzá egy előfizetéshez. Mivel a **közreműködők** és a **tulajdonosi** paraméterek a rendszerbiztonsági tag objectIds tömbjét igénylik a szerepkör-hozzárendelés megadásához, használja [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) , hogy összegyűjtse az objectIds a saját felhasználók, csoportok vagy egyszerű szolgáltatások **paramétereinek** használatára.

   - JSON-fájl – blueprintAssignment.jsbekapcsolva

     ```json
     {
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
     ```

   - Azure CLI-parancs

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - Felhasználó által hozzárendelt felügyelt identitás

     A tervrajz-hozzárendelések [felhasználó által hozzárendelt felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md)is használhatnak.
     Ebben az esetben az **Identity-Type** paraméter a _UserAssigned_ értékre van állítva, a **felhasználó által hozzárendelt identitások** paraméter pedig megadja az identitást. Cserélje le a `{userIdentity}` nevet a felhasználó által hozzárendelt felügyelt identitás nevére.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     A **felhasználó által hozzárendelt felügyelt identitás** bármely előfizetésben és erőforráscsoportban lehet, hogy a tervhez hozzárendelt felhasználó rendelkezik jogosultsággal a következőhöz:.

     > [!IMPORTANT]
     > Az Azure-tervrajzok nem kezelik a felhasználó által hozzárendelt felügyelt identitást. A felhasználók feladata a megfelelő szerepkörök és engedélyek kiosztása, vagy a terv hozzárendelése sikertelen lesz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="unassign-a-blueprint"></a>Terv hozzárendelésének megszüntetése

Eltávolíthatja a terveket az előfizetésekből. Az eltávolítás gyakori művelet az összetevők már szükségtelen erőforrásai esetén. Az egyes tervek eltávolításakor az adott tervek keretében hozzárendelt összetevők megmaradnak. A terv-hozzárendelés eltávolításához használja az `az blueprint assignment delete` parancsot:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott, hozzárendelt és eltávolított egy tervet az Azure CLI-vel. Ha többet szeretne megtudni az Azure-tervezetekről, folytassa a terv életciklusával foglalkozó cikkel.

> [!div class="nextstepaction"]
> [A terv életciklusának megismerése](./concepts/lifecycle.md)