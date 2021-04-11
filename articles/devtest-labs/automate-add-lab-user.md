---
title: Tesztkörnyezet hozzáadásának automatizálása a Azure DevTest Labsban | Microsoft Docs
description: Ez a cikk azt mutatja be, hogyan lehet automatizálni a felhasználókat a laborban Azure DevTest Labs a Azure Resource Manager sablonok, a PowerShell és a parancssori felület használatával.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1168e00960c35e2ac1e4a660efba63d30c63a575
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727705"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Tesztkörnyezet hozzáadásának automatizálása a laborban Azure DevTest Labs
Azure DevTest Labs lehetővé teszi az önkiszolgáló fejlesztési és tesztelési környezetek gyors létrehozását a Azure Portal használatával. Ha azonban több csapattal és számos DevTest Labs-példánnyal rendelkezik, akkor a létrehozási folyamat automatizálása időt takaríthat meg. [Azure Resource Manager-sablonok](https://github.com/Azure/azure-devtestlab/tree/master/Environments) lehetővé teszik a laborok, laboratóriumi virtuális gépek, Egyéni rendszerképek és képletek létrehozását, valamint a felhasználók automatikus módon való hozzáadását. Ez a cikk kifejezetten a felhasználók DevTest Labs-példányhoz való hozzáadására koncentrál.

Ha felhasználót szeretne hozzáadni a laborhoz, adja hozzá a felhasználót az **DevTest Labs felhasználói** szerepköréhez a laborhoz. Ez a cikk bemutatja, hogyan lehet automatizálni a felhasználókat a laborhoz a következő módszerek egyikével:

- Azure Resource Manager-sablonok
- Azure PowerShell-parancsmagok 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Használjon Azure Resource Manager-sablonokat
A következő minta Resource Manager-sablon meghatározza a labor **DevTest Labs felhasználói** szerepköréhez hozzáadandó felhasználót. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Ha a szerepkört ugyanabban a sablonban rendeli hozzá, amely a labort hozza létre, ne felejtsen el függőséget hozzáadni a szerepkör-hozzárendelési erőforráshoz és a laborhoz. További információ: [függőségek meghatározása Azure Resource Manager sablonokban](../azure-resource-manager/templates/define-resource-dependency.md) című cikk.

### <a name="role-assignment-resource-information"></a>Szerepkör-hozzárendelési erőforrás adatai
A szerepkör-hozzárendelési erőforrásnak meg kell adnia a típust és a nevet.

A legfontosabb megjegyezni, hogy az erőforrás típusa nem az `Microsoft.Authorization/roleAssignments` erőforráscsoport esetében lenne.  Ehelyett az erőforrástípus követi a mintát `{provider-namespace}/{resource-type}/providers/roleAssignments` . Ebben az esetben az erőforrástípus a következő lesz: `Microsoft.DevTestLab/labs/providers/roleAssignments` .

A szerepkör-hozzárendelés nevének globálisan egyedinek kell lennie.  A hozzárendelés neve a mintát használja `{labName}/Microsoft.Authorization/{newGuid}` . A `newGuid` paraméter értéke a sablonnak. Biztosítja, hogy a szerepkör-hozzárendelés neve egyedi legyen. Mivel a GUID-azonosítók létrehozásához nincs sablon függvény, a GUID létrehozó eszköz használatával létre kell hoznia egy GUID azonosítót.  

A sablonban a szerepkör-hozzárendelés nevét a változó határozza meg `fullDevTestLabUserRoleName` . A sablon pontos sora a következő:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Szerepkör-hozzárendelési erőforrás tulajdonságai
A szerepkör-hozzárendelés maga három tulajdonságot határoz meg. A, a `roleDefinitionId` `principalId` és a szükséges `scope` .

### <a name="role-definition"></a>Szerepkör-definíció
A szerepkör-definíció azonosítója a meglévő szerepkör-definíció karakterlánc-azonosítója. A szerepkör-azonosító az űrlapon található `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}` . 

Az előfizetés AZONOSÍTÓját a Template függvény használatával szerezheti be `subscription().subscriptionId` .  

Be kell szereznie a beépített szerepkör szerepkör-definícióját `DevTest Labs User` . A [DevTest Labs felhasználói](../role-based-access-control/built-in-roles.md#devtest-labs-user) szerepkör GUID azonosítójának lekéréséhez használhatja a szerepkör- [hozzárendelések REST API](/rest/api/authorization/roleassignments) vagy a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsmagot.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

A szerepkör-azonosító a változók szakaszban és a névvel van definiálva `devTestLabUserRoleId` . A sablonban a szerepkör-azonosító a következőre van beállítva: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Résztvevő azonosítója
A rendszerbiztonsági tag azonosítója annak a Active Directory felhasználónak, csoportnak vagy egyszerű szolgáltatásnak az azonosítója, amelyet labor felhasználóként szeretne hozzáadni a laborhoz. A sablon a `ObjectId` paramétert használja.

A ObjectId a [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0&preserve-view=true), a [Get-AzureRMADGroup vagy a [Get-AzureRMADServicePrincipal PowerShell-](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0&preserve-view=true) parancsmagok használatával kérheti le. Ezek a parancsmagok olyan Active Directory-objektumok egy vagy több listáját adják vissza, amelyeknek van azonosító tulajdonsága, ami a szükséges objektumazonosító. Az alábbi példa bemutatja, hogyan kérheti le egy vállalat egyetlen felhasználójának objektumazonosító-AZONOSÍTÓját.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName 'email@company.com').Id
```

Használhatja a [Get-MsolUser](/powershell/module/msonline/get-msoluser?preserve-view=true&view=azureadps-1.0), a [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?preserve-view=true&view=azureadps-1.0)és Azure Active Directory a [Get-MsolServicePrincipal-](/powershell/module/msonline/get-msolserviceprincipal?preserve-view=true&view=azureadps-1.0)t tartalmazó PowerShell-parancsmagokat is.

### <a name="scope"></a>Hatókör
A hatókör határozza meg azt az erőforrást vagy erőforráscsoportot, amelyhez a szerepkör-hozzárendelést alkalmazni kell. Az erőforrások esetében a hatókör a következő formában van: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}` . A sablon a `subscription().subscriptionId` függvény használatával tölti ki a részt `subscription-id` és a `resourceGroup().name` sablon függvényt, hogy kitöltse a `resource-group-name` részt. Ezeknek a függvényeknek a használata azt jelenti, hogy ahhoz a laborhoz kell tartoznia, amelyhez a szerepkört hozzárendeli, meg kell egyeznie az aktuális előfizetésben és azt az erőforráscsoportot, amelybe a sablon üzembe helyezése történik. Az utolsó rész a `resource-name` , a a labor neve. Ez az érték az ebben a példában szereplő sablon paraméteren keresztül érkezik. 

A szerepkör hatóköre a sablonban: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>A sablon telepítése
Először hozzon létre egy paramétert (például: azuredeploy.parameters.js), amely a Resource Manager-sablonban szereplő paraméterek értékeit adja át. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Ezután használja a [New-AzureRmResourceGroupDeployment PowerShell-](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) parancsmagot a Resource Manager-sablon üzembe helyezéséhez. A következő példában a parancs egy személyt, csoportot vagy egyszerű szolgáltatásnevet rendel a DevTest Labs felhasználói szerepkörhöz egy laborhoz.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Fontos megjegyezni, hogy a csoport központi telepítésének neve és a szerepkör-hozzárendelés GUID-azonosítójának egyedinek kell lennie. Ha olyan erőforrás-hozzárendelést próbál meg telepíteni, amely nem egyedi GUID-azonosítóval rendelkezik, hibaüzenet jelenik meg `RoleAssignmentUpdateNotPermitted` .

Ha többször szeretné használni a sablont, hogy több Active Directory objektumot adjon hozzá a DevTest Labs felhasználói szerepköréhez a laborhoz, érdemes lehet dinamikus objektumokat használni a PowerShell-parancsban. Az alábbi példa a [New-GUID](/powershell/module/Microsoft.PowerShell.Utility/New-Guid) parancsmagot használja az erőforráscsoport-telepítési név és a szerepkör-hozzárendelési GUID dinamikus megadásához.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Azure PowerShell használatával
Ahogy az a bevezetésben is szerepel, egy új Azure-szerepkör-hozzárendelést hoz létre, amely hozzáad egy felhasználót a **DevTest Labs felhasználói** szerepköréhez a laborhoz. A PowerShellben ezt a [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) parancsmag használatával teheti meg. A parancsmag számos opcionális paraméterrel rendelkezik, amelyek lehetővé teszik a rugalmasságot. A, vagy megadható, `ObjectId` `SigninName` `ServicePrincipalName` mert az objektum engedélyeket kap.  

Íme egy példa Azure PowerShell parancs, amely felvesz egy felhasználót az DevTest Labs felhasználói szerepkörbe a megadott laborban.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Annak megadásához, hogy melyik erőforrást kell megadnia, a paraméterrel adhatja meg a (z `ResourceName` `ResourceType` ), `ResourceGroup` vagy a `scope` paraméter kombinációját. A paraméterek tetszőleges kombinációját használja, adjon meg elegendő információt a parancsmagnak a Active Directory objektum (felhasználó, csoport vagy egyszerű szolgáltatásnév), hatókör (erőforráscsoport vagy erőforrás) és szerepkör-definíció egyedi azonosításához.

## <a name="use-azure-command-line-interface-cli"></a>Az Azure parancssori felület (CLI) használata
Az Azure CLI-ben a Labs-felhasználó hozzáadása a laborhoz a parancs használatával történik `az role assignment create` . Az Azure CLI-parancsmagokkal kapcsolatos további információkért lásd: [Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure CLI használatával](../role-based-access-control/role-assignments-cli.md).

Az elérni kívánt objektum a `objectId` , `signInName` `spn` paraméterrel adható meg. Az a labor, amelyhez az objektum hozzáférést kap `scope` , az URL-cím vagy a `resource-name` , és paraméterek kombinációja alapján azonosítható `resource-type` `resource-group` .

A következő Azure CLI-példa bemutatja, hogyan adhat hozzá egy személyt a DevTest Labs felhasználói szerepkörhöz a megadott laborhoz.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type "Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Virtuális gépek létrehozása és kezelése az Azure CLI-vel a DevTest Labs használatával](devtest-lab-vmcli.md)
- [Virtuális gép létrehozása a DevTest Labs használatával Azure PowerShell](devtest-lab-vm-powershell.md)
- [Azure DevTest Labs virtuális gépek indítása és leállítása parancssori eszközök használatával](use-command-line-start-stop-virtual-machines.md)

