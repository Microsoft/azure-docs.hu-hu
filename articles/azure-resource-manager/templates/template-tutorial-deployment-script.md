---
title: Sablon-telepítési parancsfájlok használata | Microsoft Docs
description: Megtudhatja, hogyan használhatja az üzembe helyezési szkripteket Azure Resource Manager-sablonokban (ARM-sablonok).
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/16/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 36fb54b4b6521d87c7461936c84a644bf22f7e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963963"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate"></a>Oktatóanyag: az üzembe helyezési parancsfájlok használata önaláírt tanúsítvány létrehozásához

Megtudhatja, hogyan használhatja az üzembe helyezési szkripteket Azure Resource Manager-sablonokban (ARM-sablonok). Az üzembe helyezési parancsfájlok olyan egyéni lépések elvégzésére használhatók, amelyeket az ARM-sablonok nem végezhetnek el. Például hozzon létre egy önaláírt tanúsítványt. Ebben az oktatóanyagban létrehoz egy sablont az Azure Key Vault üzembe helyezéséhez, majd egy olyan `Microsoft.Resources/deploymentScripts` erőforrást használ egy sablonban, amely létrehoz egy tanúsítványt, majd hozzáadja a tanúsítványt a kulcstartóhoz. További információ az üzembe helyezési parancsfájlról: [telepítési parancsfájlok használata ARM-sablonokban](./deployment-script-template.md).

> [!IMPORTANT]
> A parancsfájlok végrehajtásához és a hibaelhárításhoz két üzembe helyezési parancsfájl-erőforrás, egy Storage-fiók és egy tároló-példány jön létre ugyanabban az erőforráscsoporthoz. Ezeket az erőforrásokat általában a parancsfájl-szolgáltatás törli, ha a parancsfájl végrehajtása terminál állapotba kerül. Az erőforrások számlázása az erőforrások törlése után történik. További információ: [üzembe helyezési parancsfájl erőforrásainak tisztítása](./deployment-script-template.md#clean-up-deployment-script-resources).

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon szerkesztése
> * A sablon üzembe helyezése
> * A sikertelen parancsfájl hibakeresése
> * Az erőforrások eltávolítása

Az üzembe helyezési parancsfájlokat tartalmazó Microsoft Learn modul esetében lásd: [ARM-sablonok kiterjesztése telepítési parancsfájlok használatával](/learn/modules/extend-resource-manager-template-deployment-scripts/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* **[Visual Studio Code](https://code.visualstudio.com/) a Resource Manager-eszközök bővítménnyel**. Lásd [a gyors útmutató: ARM-sablonok létrehozása a Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md)-ban című témakört.

* **Felhasználó által hozzárendelt felügyelt identitás**. Ez az identitás az Azure-specifikus műveletek végrehajtásához használatos a parancsfájlban. A létrehozáshoz tekintse meg a [felhasználó által hozzárendelt felügyelt identitást](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). A sablon üzembe helyezésekor szüksége lesz az azonosító AZONOSÍTÓra. Az identitás formátuma:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Használja az alábbi CLI-szkriptet az azonosító beszerzéséhez az erőforráscsoport nevének és az identitás nevének megadásával.

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  az identity list -g $resourceGroupName
  ```

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Teljesen új sablon létrehozása helyett megnyithat egy sablont az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) közül. Az Azure Gyorsindítás sablonjai az ARM-sablonok tárháza.

Az ebben a rövid útmutatóban használt sablon [létrehozásához Azure Key Vault és titkos kulcsot](https://azure.microsoft.com/resources/templates/101-key-vault-create/)kell meghívni. A sablon létrehoz egy kulcstartót, majd hozzáadja a titkos kulcsot a kulcstartóhoz.

1. A Visual Studio Code-ból válassza a **fájl**  >  **megnyitott** fájl elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. Válassza a **fájl**  >  **Mentés másként** lehetőséget, hogy a fájlt _azuredeploy.jsa_ helyi számítógépre mentse.

## <a name="edit-the-template"></a>A sablon szerkesztése

Végezze el a következő módosításokat a sablonon:

### <a name="clean-up-the-template-optional"></a>A sablon törlése (nem kötelező)

Az eredeti sablon egy titkos kulcsot helyez el a kulcstartóhoz. Az oktatóanyag leegyszerűsítése érdekében távolítsa el a következő erőforrást:

* `Microsoft.KeyVault/vaults/secrets`

Távolítsa el a következő két paraméter-definíciót:

* `secretName`
* `secretValue`

Ha úgy dönt, hogy nem távolítja el ezeket a definíciókat, meg kell adnia a paraméter értékeit a telepítés során.

### <a name="configure-the-key-vault-access-policies"></a>A Key Vault hozzáférési házirendjeinek konfigurálása

Az üzembe helyezési parancsfájl hozzáadja a tanúsítványt a kulcstartóhoz. Konfigurálja a Key Vault hozzáférési szabályzatait, hogy engedélyt adjanak a felügyelt identitásnak:

1. Adjon hozzá egy paramétert a felügyelt azonosító AZONOSÍTÓjának beolvasásához:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > A Resource Manager-sablon kiterjesztése a Visual Studio Code-hoz még nem képes az üzembe helyezési parancsfájlok formázására. Ne használja a Shift + Alt + F billentyűkombinációt az erőforrások formázásához `deploymentScripts` , például a következőhöz.

1. Adjon hozzá egy paramétert a Key Vault hozzáférési házirendjeinek konfigurálásához, hogy a felügyelt identitás hozzá tudja adni a tanúsítványokat a kulcstartóhoz:

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. A meglévő Key Vault-hozzáférési házirendek frissítése a következőre:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Két házirend van definiálva, egyet a bejelentkezett felhasználó számára, a másik pedig a felügyelt identitáshoz. A bejelentkezett felhasználónak csak a *listázási* engedélyre van szüksége az üzemelő példány ellenőrzéséhez. Az oktatóanyag leegyszerűsítése érdekében ugyanaz a tanúsítvány van hozzárendelve a felügyelt identitáshoz és a bejelentkezett felhasználókhoz is.

### <a name="add-the-deployment-script"></a>Az üzembe helyezési parancsfájl hozzáadása

1. Adja hozzá az üzembe helyezési parancsfájl által használt három paramétert:

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Erőforrás hozzáadása `deploymentScripts` :

    > [!NOTE]
    > Mivel a beágyazott telepítési parancsfájlok idézőjelek közé vannak ágyazva, az üzembe helyezési parancsfájlokban lévő karakterláncokat csak egyszeres idézőjelek közé kell foglalni. A [PowerShell escape-karakter](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#single-and-double-quoted-strings) a kezdő ( `` ` `` ).

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2020-10-01",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    Az `deploymentScripts` erőforrás a Key Vault-erőforrástól és a szerepkör-hozzárendelési erőforrástól függ. Ezek a tulajdonságok a következő tulajdonságokkal rendelkeznek:

    * `identity`: Az üzembe helyezési parancsfájl a felhasználó által hozzárendelt felügyelt identitás használatával hajtja végre a műveleteket a parancsfájlban.
    * `kind`: Adja meg a parancsfájl típusát. Jelenleg csak a PowerShell-parancsfájlok támogatottak.
    * `forceUpdateTag`: Határozza meg, hogy a telepítési parancsfájlt akkor is kell-e végrehajtani, ha a parancsfájl forrása nem változott. Lehet aktuális időbélyeg vagy GUID. További információ: [parancsfájl futtatása](./deployment-script-template.md#run-script-more-than-once)többször.
    * `azPowerShellVersion`: Megadja a használni kívánt Azure PowerShell modul verzióját. Az üzembe helyezési parancsfájl jelenleg a 2.7.0, a 2.8.0 és a 3.0.0 verziót támogatja.
    * `timeout`: Adja meg az [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601)megadott maximálisan engedélyezett parancsfájl-végrehajtási időt. Az alapértelmezett érték a **P1D**.
    * `arguments`: Határozza meg a paraméterek értékeit. Az értékeket szóközök választják el egymástól.
    * `scriptContent`: Adja meg a parancsfájl tartalmát. Külső parancsfájl futtatásához használja `primaryScriptURI` helyette a parancsot. További információ: [külső parancsfájl használata](./deployment-script-template.md#use-external-scripts).
        A deklarálás `$DeploymentScriptOutputs` csak akkor szükséges, ha a parancsfájlt egy helyi gépen teszteli. A változó deklarálása lehetővé teszi, hogy a parancsfájl helyi gépen és erőforráson fusson `deploymentScript` anélkül, hogy módosítani kellene. A hozzárendelt érték az `$DeploymentScriptOutputs` üzemelő példányokban kimenetként érhető el. További információkért lásd a [PowerShell üzembe helyezési parancsfájlokból származó kimenetek használata](./deployment-script-template.md#work-with-outputs-from-powershell-script) , illetve a [CLI üzembe helyezési parancsfájlokból származó kimenetek használata](./deployment-script-template.md#work-with-outputs-from-cli-script)című témakört.
    * `cleanupPreference`: Adja meg a telepítési parancsfájl erőforrásainak törlésére vonatkozó beállítást. Az alapértelmezett érték **mindig**, ami azt jelenti, hogy az üzembe helyezési parancsfájl erőforrásai törlődnek a terminál állapota (sikeres, sikertelen, megszakított) ellenére. Ebben az oktatóanyagban a **OnSuccess** -t használjuk, így a szkriptek végrehajtási eredményei is megtekinthetők.
    * `retentionInterval`: Adja meg azt az intervallumot, ameddig a szolgáltatás megőrzi a parancsfájl erőforrásait, miután elérte a terminál állapotát. Az erőforrások az adott időtartam lejárta után törlődnek. Az időtartam az ISO 8601 mintán alapul. Ez az oktatóanyag **P1D** használ, ami egy napot jelent. Ezt a tulajdonságot akkor használja a rendszer, ha a `cleanupPreference` értéke **OnExpiration**. Ez a tulajdonság jelenleg nincs engedélyezve.

    Az üzembe helyezési parancsfájl három paramétert fogad: `keyVaultName` , `certificateName` és `subjectName` . Létrehoz egy tanúsítványt, majd hozzáadja a tanúsítványt a kulcstartóhoz.

    `$DeploymentScriptOutputs` a kimeneti érték tárolására szolgál. További információért lásd: [a PowerShell telepítési parancsfájljai kimenetének használata](./deployment-script-template.md#work-with-outputs-from-powershell-script) vagy [a parancssori felületi telepítési parancsfájlok kimenetének használata](./deployment-script-template.md#work-with-outputs-from-cli-script).

    Az elkészült sablon [itt](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)található.

1. A hibakeresési folyamat megtekintéséhez helyezzen el egy hibát a kódban a következő sor hozzáadásával az üzembe helyezési parancsfájlba:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    A megfelelő parancs a `Write-Output` helyett `Write-Output1` .

1. A fájl mentéséhez válassza a **fájl**  >  **mentése** lehetőséget.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Jelentkezzen be a [Azure Cloud Shellba](https://shell.azure.com)

1. Válassza ki az előnyben részesített környezetet a **PowerShell** vagy a **bash** (a CLI esetében) lehetőség kiválasztásával a bal felső sarokban. A váltáskor a felületet újra kell indítani.

    ![Azure Portal Cloud Shell fájl feltöltése](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre. Lásd az előző képernyőképet.  Válassza ki a korábbi szakaszban mentett fájlt. A fájl feltöltése után a `ls` parancs és a parancs használatával ellenőrizheti, `cat` hogy a fájl feltöltése sikeresen megtörtént-e.

1. Futtassa a következő PowerShell-szkriptet a sablon telepítéséhez.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
    $identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

    $adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
    $resourceGroupName = "${projectName}rg"
    $keyVaultName = "${projectName}kv"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

    Write-Host "Press [ENTER] to continue ..."
    ```

    A telepítési parancsfájl-szolgáltatásnak további üzembe helyezési parancsfájl-erőforrásokat kell létrehoznia a parancsfájlok végrehajtásához. Az előkészítés és a tisztítás folyamata akár egy percet is igénybe vehet a parancsfájl végrehajtásának tényleges végrehajtási ideje mellett.

    A telepítés nem sikerült, mert az érvénytelen parancs a `Write-Output1` parancsfájlban van használatban. A következő hibaüzenetet kapja:

    ```error
    The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
    program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
    ```

    A telepítési parancsfájl végrehajtási eredménye a hibaelhárítási célból a telepítési parancsfájl erőforrásaiban tárolódik.

## <a name="debug-the-failed-script"></a>A sikertelen parancsfájl hibakeresése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az erőforráscsoportot. Ez a projekt neve, **RG** hozzáfűzéssel. Az erőforráscsoport két további erőforrást fog látni. Ezeket az erőforrásokat *telepítési parancsfájl-erőforrásoknak* nevezzük.

    ![Resource Manager-sablon üzembe helyezési parancsfájl erőforrásai](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Mindkét fájl _azscripts_ utótaggal rendelkezik. Az egyik a Storage-fiók, a másik pedig egy tároló példány.

    Válassza a **rejtett típusok megjelenítése** lehetőséget az `deploymentScripts` erőforrás listázásához.

1. Válassza ki a _azscripts_ utótaggal rendelkező Storage-fiókot.
1. Válassza ki a **fájlmegosztás** csempét. Megjelenik egy _azscripts_ mappa, amely tartalmazza a telepítési parancsfájl végrehajtási fájljait.
1. Válassza a _azscripts_ lehetőséget. Két mappát fog látni a _azscriptinput_ és a _azscriptoutput_. A bemeneti mappa egy PowerShell-parancsfájlt és a felhasználói telepítési parancsfájlokat tartalmaz. A kimeneti mappa tartalmaz egy _executionresult.jst_ és a parancsfájl kimeneti fájlját. A hibaüzenetet _executionresult.jsa_ következő helyen tekintheti meg:. A kimeneti fájl nem létezik, mert a végrehajtás nem sikerült.

Távolítsa el a `Write-Output1` sort, és telepítse újra a sablont.

A második központi telepítés sikeres futtatásakor a rendszer eltávolítja a telepítési parancsfájl erőforrásait a parancsfájl-szolgáltatásból, mivel a `cleanupPreference` tulajdonság értéke **OnSuccess**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Az erőforráscsoport összesen hat erőforrást fog látni.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az üzembe helyezési szkriptet az ARM-sablonokban. Az Azure-erőforrások feltételek alapján való üzembe helyezésével kapcsolatban lásd:

> [!div class="nextstepaction"]
> [Feltételek használata](./template-tutorial-use-conditions.md)
