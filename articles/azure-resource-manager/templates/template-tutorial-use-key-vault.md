---
title: Azure Key Vault használata a sablonokban
description: Megtudhatja, hogyan használhatók a Azure Key Vault a biztonságos paraméterek értékének átadására Azure Resource Manager sablon (ARM-sablon) üzembe helyezése során.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 388996dc0054192f6d9f3c87e11ca1d15e8a85e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703885"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Oktatóanyag: Az Azure Key Vault integrálása ARM-sablonalapú telepítésbe

Megtudhatja, hogyan kérhet le titkos kulcsokat egy Azure Key vaultból, és hogyan továbbíthatja a titkokat paraméterekként Azure Resource Manager sablon (ARM-sablon) telepítésekor. A paraméter értéke soha nem érhető el, mert csak a Key Vault-AZONOSÍTÓra hivatkozik. A Key Vault titkos kulcsát statikus azonosító vagy dinamikus azonosító használatával lehet hivatkozni. Ez az oktatóanyag egy statikus azonosítót használ. A statikus azonosító megközelítésével a kulcstároló a sablon paraméterének fájljában hivatkozik, nem a sablonfájl. További információ mindkét megközelítésről: a [Azure Key Vault használata a biztonságos paraméterek értékének](./key-vault-parameter.md)átadására az üzembe helyezés során.

Az [erőforrás-telepítési sorrend beállítása](./template-tutorial-create-templates-with-dependent-resources.md) oktatóanyagban hozzon létre egy virtuális GÉPET (VM). Meg kell adnia a virtuális gép rendszergazdájának felhasználónevét és jelszavát. A jelszó megadása helyett előre tárolhatja a jelszót egy Azure Key vaultban, majd testreszabhatja a sablont a Key vaultból a telepítés során való lekéréséhez.

![Egy Resource Manager-sablon és egy kulcstartó integrálását megjelenítő diagram](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Kulcstartó előkészítése
> * Gyorsindítási sablon megnyitása
> * A paraméterfájl szerkesztése
> * A sablon üzembe helyezése
> * Az üzembe helyezés ellenőrzése
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

A Key vaultból származó biztonságos értéket használó Microsoft Learn modul esetében lásd: [összetett Felhőbeli központi telepítések kezelése speciális ARM-sablon funkcióinak használatával](/learn/modules/manage-deployments-advanced-arm-template-features/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Lásd [a gyors útmutató: ARM-sablonok létrehozása a Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)-ban című témakört.
* A biztonság növeléséhez használjon generált jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa a jelszó létrehozására:

    ```console
    openssl rand -base64 32
    ```

    Ellenőrizze, hogy a generált jelszó megfelel-e a virtuális gép jelszavával kapcsolatos követelményeknek. Az egyes Azure-szolgáltatások különböző jelszókövetelményeket szabnak meg. A virtuális gép jelszavával kapcsolatos követelményekért lásd: [Mik a jelszóra vonatkozó követelmények a virtuális gépek létrehozásakor?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Kulcstartó előkészítése

Ebben a szakaszban létrehoz egy kulcstartót, és hozzáad egy titkos kulcsot a sablon üzembe helyezése során a titkos kulcs lekéréséhez. A Key Vault számos módon hozható létre. Ebben az oktatóanyagban a Azure PowerShell használatával helyezheti üzembe az [ARM-sablonokat](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Ez a sablon két dolgot tesz:

* Létrehoz egy Key vaultot, amelynek a `enabledForTemplateDeployment` tulajdonsága engedélyezve van. Ennek a tulajdonságnak *igaznak* kell lennie ahhoz, hogy a sablon központi telepítési folyamata hozzáférhessen a kulcstartóban definiált titkos kulcsokhoz.
* Titkos kulcsot helyez el a kulcstartóhoz. A titkos kulcs a virtuális gép rendszergazdai jelszavát tárolja.

> [!NOTE]
> Ha a virtuálisgép-sablont telepítő felhasználó nem a kulcstartó tulajdonosa vagy közreműködője, akkor a tulajdonosnak vagy a közreműködőnek hozzáférést kell adnia a `Microsoft.KeyVault/vaults/deploy/action` kulcstartó engedélyéhez. További információ: a [Azure Key Vault használata biztonságos paraméterek értékének](./key-vault-parameter.md)átadására az üzembe helyezés során.

A következő Azure PowerShell parancsfájl futtatásához válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablaktáblára, majd válassza a **Beillesztés** lehetőséget.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * Az erőforráscsoport neve a projekt neve, de **RG** hozzáfűzéssel. Az [oktatóanyagban létrehozott erőforrások](#clean-up-resources)törlésének egyszerűbbé tételéhez használja a projekt nevét és az erőforráscsoport nevét a [következő sablon telepítésekor](#deploy-the-template).
> * A titok alapértelmezett neve **vmAdminPassword**. A sablon hardcoded.
> * Ha engedélyezni szeretné a sablon számára a titkos kulcs lekérését, engedélyeznie kell egy hozzáférési szabályzatot, amelynek neve **: hozzáférés engedélyezése Azure Resource Manager** a Key vaulthoz való központi telepítéshez. Ez a szabályzat engedélyezve van a sablonban. A hozzáférési szabályzattal kapcsolatos további információkért lásd: [kulcstartók és titkos kulcsok üzembe helyezése](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

A sablon egyetlen kimeneti értékkel rendelkezik, a neve `keyVaultId` . Ezt az azonosítót és a titkos nevet is használni fogja a titkos érték lekéréséhez az oktatóanyag későbbi részében. Az erőforrás-azonosító formátuma:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Az azonosító másolásakor és beillesztésekor előfordulhat, hogy több sorba van bontva. Egyesítse a sorokat, és vágja fel a felesleges szóközöket.

A központi telepítés ellenőrzéséhez futtassa a következő PowerShell-parancsot ugyanazon a rendszerhéj-ablaktáblán a titkos kód egyszerű szövegként való lekéréséhez. A parancs csak ugyanabban a rendszerhéj-munkamenetben működik, mert a változót használja `$keyVaultName` , amely az előző PowerShell-parancsfájlban van meghatározva.

```azurepowershell
$secret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Most előkészített egy kulcstartót és egy titkos kulcsot. A következő részben bemutatjuk, hogyan szabhatja testre a meglévő sablonokat a titkos kód beolvasására a telepítés során.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure Gyorsindítás sablonjai az ARM-sablonok tárháza. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. Az oktatóanyagban használt sablon [egy egyszerű Windows-alapú virtuális gép üzembe helyezését](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)hívja meg.

1. A Visual Studio Code-ban válassza a **fájl**  >  **megnyitott** fájl elemet.

1. Illessze be a következő URL-címet a **fájlnév** mezőbe:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt. A forgatókönyv ugyanaz, mint az [oktatóanyagban használt: ARM-sablonok létrehozása függő erőforrásokkal](./template-tutorial-create-templates-with-dependent-resources.md).
   A sablon hat erőforrást definiál:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. számítási/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   A Testreszabás előtt hasznos lehet a sablon alapvető ismerete.

1. Válassza a **fájl**  >  **Mentés másként** lehetőséget, majd mentse a fájl egy másolatát a helyi számítógépre, amelynek a neve *azuredeploy.js*.

1. Ismételje meg a 1-3 lépést a következő URL-cím megnyitásához, majd mentse a fájlt *azuredeploy.parameters.jsként*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>A paraméterfájl szerkesztése

A statikus azonosító módszer használatával semmilyen módosítást nem kell végeznie a sablon fájljában. A titkos érték beolvasása a sablon-paraméter fájljának konfigurálásával végezhető el.

1. A Visual Studio Code-ban nyissa meg *aazuredeploy.parameters.json* , ha még nincs megnyitva.
1. A paraméter frissítése a következőre `adminPassword` :

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
                "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Cserélje le az értékét az `id` előző eljárás során létrehozott kulcstartó erőforrás-azonosítójával. A a `secretName` hardcoded as **vmAdminPassword**.  Lásd: [Key Vault előkészítése](#prepare-a-key-vault).

    ![A Key Vault és a Resource Manager-sablon virtuális gép telepítési paramétereinek fájljának integrálása](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Frissítse a következő értékeket:

    * `adminUsername`: A virtuális gép rendszergazdai fiókjának neve.
    * `dnsLabelPrefix`: Nevezze el az `dnsLabelPrefix` értéket.

    Példák a nevekre: az előző képen látható.

1. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Jelentkezzen be a [Azure Cloud Shellba](https://shell.azure.com)

1. Válassza ki a kívánt környezetet a **PowerShell** vagy a **bash** (a CLI esetében) lehetőség kiválasztásával a bal felső sarokban.  A váltáskor a felületet újra kell indítani.

    ![Azure Portal Cloud Shell fájl feltöltése](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre. Töltse fel a *azuredeploy.jst* és *azuredeploy.parameters.js* a Cloud shellre. A fájl feltöltése után a `ls` parancs és a parancs használatával ellenőrizheti, `cat` hogy a fájl feltöltése sikeresen megtörtént-e.

1. Futtassa a következő PowerShell-szkriptet a sablon telepítéséhez.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
    $location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$HOME/azuredeploy.json" `
        -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    A sablon központi telepítésekor használja ugyanazt az erőforráscsoportot, amelyet a Key vaultban használt. Ezzel a módszerrel könnyebben törölheti az erőforrásokat, mivel kettő helyett csak egy erőforráscsoportot kell törölnie.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A virtuális gép sikeres üzembe helyezését követően tesztelje a bejelentkezési hitelesítő adatokat a Key vaultban tárolt jelszó használatával.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. Válassza az **erőforráscsoportok**  >  **\<*YourResourceGroupName*>**  >  **simpleWinVM** elemet.
1. Kattintson a fent található **kapcsolat** lehetőségre.
1. Válassza az **RDP-fájl letöltése** lehetőséget, majd kövesse az utasításokat a virtuális gépre való bejelentkezéshez a Key vaultban tárolt jelszó használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Azure-erőforrásokra, az erőforráscsoport törlésével törölje az üzembe helyezett erőforrásokat.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy titkos kulcsot adott vissza az Azure Key vaultból. Ezután használta a titkot a sablon üzembe helyezésében. Ha szeretné megtudni, hogyan használhatja a virtuálisgép-bővítményeket az üzembe helyezés utáni feladatok végrehajtásához, olvassa el a következő témakört:

> [!div class="nextstepaction"]
> [Virtuális gépi bővítmények üzembe helyezése](./template-tutorial-deploy-vm-extensions.md)
