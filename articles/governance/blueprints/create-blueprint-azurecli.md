---
title: 'Rövid útmutató: Terv létrehozása az Azure CLI használatával'
description: Ebben a rövid útmutatóban a Azure Blueprints azure cli használatával hozhat létre, definiálhat és helyezhet üzembe összetevőket.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 506877eddd78ce54681bd4870e1d9040b4738c27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877407"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Rövid útmutató: Azure Blueprints definiálás és hozzárendelése az Azure CLI használatával

A tervek létrehozási és hozzárendelési módszerének elsajátítása lehetővé teszi a gyakori minták meghatározását, hogy újrahasználható és gyorsan üzembe helyezhető konfigurációkat fejlessze ki Azure Resource Manager-sablonok (ARM-sablonok), szabályzatok, biztonság stb. alapján. Ez az oktatóanyag bemutatja, hogyan hajthatja végre az Azure Blueprints használatával a tervek a szervezeten belüli létrehozásával, közzétételével és hozzárendelésével kapcsolatos olyan általános feladatokat, mint az alábbiak:

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.
- Ha még nem használta a Azure Blueprints, regisztrálja az erőforrás-szolgáltatót az Azure CLI-val a `az provider register --namespace Microsoft.Blueprint` következővel: .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>A Blueprint bővítmény hozzáadása

Ahhoz, hogy az Azure CLI kezelni tudja a tervdefiníciókat és -hozzárendeléseket, hozzá kell adni a bővítményt.
Ez a bővítmény mindenhol működik, ahol az Azure CLI használható, beleértve a [Basht Windows 10-en](/windows/wsl/install-win10), a [Cloud Shellt](https://shell.azure.com) (különállón és portálon belülin egyaránt), az [Azure CLI Docker-rendszerképet](https://hub.docker.com/_/microsoft-azure-cli), vagy akár helyileg telepítve is.

1. Ellenőrizze, hogy telepítve van-e a legújabb Azure CLI **(legalább 2.0.76).** Ha még nincs telepítve, kövesse [ezeket az utasításokat](/cli/azure/install-azure-cli-windows).

1. A választott Azure CLI környezetben importálja a bővítményt a következő paranccsal:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Ellenőrizze, hogy a bővítmény telepítve van-e, és a várt verzió -e **(legalább 0.1.0):**

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Terv létrehozása

A megfelelőségi szabványminták definiálásának első lépése, hogy összeállítunk egy tervet az elérhető erőforrásokból. Létrehozzuk a „MyBlueprint” nevű tervet az előfizetés szerepkör- és szabályzat-hozzárendeléseinek konfigurálására. Ezután hozzáadunk egy erőforráscsoportot, egy ARM-sablont és egy szerepkör-hozzárendelést az erőforráscsoporthoz.

> [!NOTE]
> Az Azure CLI használata esetén először a _tervobjektum_ jön létre. Mindegyik hozzáadott, paraméterekkel rendelkező _összetevő_ esetében a paramétereket előre definiálni kell a kezdeti _terven_.

1. Hozza létre a kezdeti _terv_ objektumot. A **parameters paraméter** egy JSON-fájlt vesz fel, amely tartalmazza az összes tervszintű paramétert. A paraméterek a hozzárendelés során vannak megadva, és a későbbi lépésekben hozzáadott összetevők használják azokat.

   - JSON-fájl – blueprintparms.jsbe

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
     > A tervdefiníciók _blueprint.jsbe_ a fájlnevet.
     > Ezt a fájlnevet az [az blueprint import hívásához használja](/cli/azure/blueprint#az_blueprint_import)a rendszer.

     A tervobjektum alapértelmezés szerint az alapértelmezett előfizetésben jön létre. A felügyeleti csoport megadásához használja a **paraméterkezelő csoportot.** Az előfizetés megadásához használja a **paraméter-előfizetést.**

1. Adja hozzá a tárolási összetevők erőforráscsoportját a definícióhoz.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Szerepkör-hozzárendelés hozzáadása az előfizetésben. Az alábbi példában a megadott szerepkörrel felruházott egyszerű identitások egy olyan paraméterre vannak konfigurálva, amely a tervhozzárendelés során van megadva. Ez a példa a _Közreműködő_ beépített szerepkört használja a GUID `b24988ac-6180-42a0-ab88-20f7382dd24c` azonosítóval.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Szabályzat-hozzárendelés hozzáadása az előfizetésben. Ez a példa az _Apply (Címke_ alkalmazása) és annak alapértelmezett értékét használja a (GUID) GUID azonosítóval megadott beépített erőforráscsoportokra. `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`

   - JSON-fájl – artifacts\policyTags.jsbe

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
     > Mac gépen a érték helyére az elérési utat is `az blueprint` `\` beleértő `/` paraméterértékeket cserélje le. Ebben az esetben a paraméterek **értéke** `artifacts/policyTags.json` lesz.

1. Egy másik szabályzat-hozzárendelés hozzáadása egy Storage-címke számára (a _storageAccountType_ paraméter ismételt felhasználásával) az előfizetésen. Ez az újabb szabályzat-hozzárendelési összetevő bemutatja, hogy a terveken definiált paramétereket több összetevő is használhatja. A példában a **storageAccountType** használatával beállítunk egy címkét az erőforráscsoporton. Ez az érték a következő lépésben létrehozott tárfiókkal kapcsolatos információkat szolgáltat. Ez a példa az _Apply (Címke alkalmazása) és annak alapértelmezett_ értékét használja az erőforráscsoportok beépített szabályzatán a GUID azonosítóval. `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`

   - JSON-fájl – artifacts\policyStorageTags.jsbe

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
     > Mac gépen a érték helyére az elérési utat is `az blueprint` `\` beleértő `/` paraméterértékeket cserélje le. Ebben az esetben a paraméterek **értéke** `artifacts/policyStorageTags.json` lesz.

1. Sablon hozzáadása az erőforráscsoport alatt. Az **ARM-sablonok** sablonparamétere tartalmazza a sablon normál JSON-összetevőit. A sablon újra felhasználja a **storageAccountType**, a **tagName** és a **tagValue** tervparamétert is, mivel továbbadja azokat a sablonnak. A tervparaméterek paraméterparaméterek használatával érhetők el a sablon számára, és a sablon JSON-ában ez a kulcs-érték pár használható az érték be injektálása érdekében.  A terv- és sablonparaméterek nevei azonosak is lehetnek.

   - JSON ARM-sablonfájl – artifacts\templateStorage.jsbe

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

   - JSON ARM-sablon paraméterfájlja – artifacts\templateStorageParams.jsbe

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
     > Mac gépen a érték helyére az elérési utat is `az blueprint` `\` beleértő `/` paraméterértékeket cserélje le. Ebben az esetben a sablon értéke **lesz,** `artifacts/templateStorage.json` a **paraméterek** pedig `artifacts/templateStorageParams.json` .

1. Szerepkör-hozzárendelés hozzáadása az erőforráscsoport alatt. Az előző szerepkör-hozzárendelési bejegyzéshez hasonlóan az alábbi példa a **Tulajdonos** szerepkör definíciós azonosítóját használja, és egy másik paramétert ad neki a tervből. Ez a példa a _Tulajdonos_ beépített szerepkört használja a GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` azonosítóval.

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

A `{BlueprintVersion}` értéke egy betűket, számokat és kötőjeleket (szóközöket vagy speciális karaktereket nem) tartalmazó, legfeljebb 20 karakter hosszúságú sztring. Használjon valami egyedit és tájékoztató jellegűt, **például: v20200605-135541.**

## <a name="assign-a-blueprint"></a>Terv hozzárendelése

Miután közzétett egy tervet az Azure CLI használatával, az hozzárendelhető egy előfizetéshez. A létrehozott tervet a felügyeleti csoport hierarchiájában rendelheti hozzá az egyik előfizetéshez. Ha a terv egy előfizetésbe van mentve, csak az előfizetéshez rendelhető hozzá. A **tervnév paraméter** határozza meg a hozzárendelni szükséges tervet. A név, hely, identitás, zárolás és tervparaméterek megszabadításához használja az egyező Azure CLI-paramétereket a parancsban, vagy adja meg őket a `az blueprint assignment create` paraméterek JSON-fájljában. 

1. A tervpéldányt a futtatásához rendelje hozzá egy előfizetéshez. Mivel  a közreműködői és tulajdonosi paraméterekhez a szerepkör-hozzárendelést kapó résztvevők objectId-tömbje szükséges, az Azure Active Directory Graph API  használatával gyűjtheti össze az  objectId-eket, hogy használva legyen a paraméterekben a saját felhasználók, csoportok vagy szolgáltatásnév esetében. [](/graph/migrate-azure-ad-graph-planning-checklist)

   - JSON-fájl – blueprintAssignment.jsbe

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

     A terv-hozzárendelések felhasználó által hozzárendelt felügyelt [identitást is használhatnak.](../../active-directory/managed-identities-azure-resources/overview.md)
     Ebben az esetben az **identitástípus paraméter** értéke _UserAssigned,_ a felhasználó által hozzárendelt **identitások** paraméter pedig az identitást határozza meg. Cserélje le a helyére a felhasználó által `{userIdentity}` hozzárendelt felügyelt identitás nevét.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     A **felhasználó által hozzárendelt felügyelt identitás** bármely előfizetésben és erőforráscsoportban lehet, amelyhez a tervet hozzárendelő felhasználó engedélyekkel rendelkezik.

     > [!IMPORTANT]
     > Azure Blueprints nem a felhasználó által hozzárendelt felügyelt identitást kezeli. A felhasználók felelnek a megfelelő szerepkörök és engedélyek hozzárendeléséért, különben a terv hozzárendelése sikertelen lesz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="unassign-a-blueprint"></a>Terv hozzárendelésének megszüntetése

Eltávolíthatja a terveket az előfizetésekből. Az eltávolítás gyakori művelet az összetevők már szükségtelen erőforrásai esetén. Az egyes tervek eltávolításakor az adott tervek keretében hozzárendelt összetevők megmaradnak. Terv hozzárendelésének eltávolításához használja az `az blueprint assignment delete` parancsot:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy tervet hozott létre, rendelt hozzá és távolított el az Azure CLI használatával. Ha többet szeretne megtudni a Azure Blueprints, folytassa a terv életciklusával kapcsolatos cikkel.

> [!div class="nextstepaction"]
> [Tudnivalók a tervek életciklusról](./concepts/lifecycle.md)