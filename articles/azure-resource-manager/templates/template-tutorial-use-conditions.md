---
title: Feltétel használata a sablonokban
description: Megtudhatja, hogyan helyezhet üzembe Azure-erőforrásokat feltételek alapján. Bemutatja, hogyan helyezhet üzembe egy új erőforrást, vagy használjon egy meglévő erőforrást.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4affc2add2822702c1d5395f81efe01eeedf448b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98696024"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Oktatóanyag: Feltétel használata ARM-sablonokban

Megtudhatja, hogyan helyezhet üzembe Azure-erőforrásokat egy Azure Resource Manager-sablon (ARM-sablon) feltételei alapján.

Az [erőforrások üzembehelyezési sorrendjének beállítását](./template-tutorial-create-templates-with-dependent-resources.md) ismertető oktatóanyagban egy virtuális gépet, egy virtuális hálózatot és egyéb függő erőforrásokat fog létrehozni, például egy tárfiókot. Ahelyett, hogy minden alkalommal egy új tárfiókot kellene létrehozni, megengedheti a felhasználóknak, hogy maguk döntsék el, új tárfiókot hoznak létre vagy egy meglévőt használnak. Ehhez egy további paramétert kell meghatároznia. Ha a paraméter értéke **új**, a rendszer új Storage-fiókot hoz létre. Ellenkező esetben a rendszer a megadott nevű meglévő Storage-fiókot használja.

![Resource Manager-sablon használati feltételeinek diagramja](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon módosítása
> * A sablon üzembe helyezése
> * Az erőforrások eltávolítása

Ez az oktatóanyag csak a feltételek használatának alapvető forgatókönyvét tárgyalja. További információkért lásd:

* [Sablonfájl szerkezete: feltétel](conditional-resource-deployment.md).
* [Erőforrás feltételes üzembe helyezése ARM-sablonban](/azure/architecture/guide/azure-resource-manager/advanced-templates/conditional-deploy).
* [Sablon függvény: if](./template-functions-logical.md#if).
* [ARM-sablonok összehasonlító függvények](./template-functions-comparison.md)

A feltételekkel foglalkozó Microsoft Learn modul esetében lásd: [összetett Felhőbeli központi telepítések kezelése speciális ARM-sablonok használatával](/learn/modules/manage-deployments-advanced-arm-template-features/).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Lásd [a gyors útmutató: ARM-sablonok létrehozása a Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)-ban című témakört.
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```console
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információ: [oktatóanyag: Azure Key Vault integrálása az ARM-sablonok üzembe helyezésével](./template-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure Gyorsindítás sablonjai az ARM-sablonok tárháza. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio Code-ból válassza a **fájl**  >  **megnyitott** fájl elemet.
1. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
1. A sablon hat erőforrást definiál:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. számítási/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    A sablon testreszabása előtt hasznos áttekinteni a sablon hivatkozását.

1. Válassza a **fájl**  >  **Mentés másként** lehetőséget a fájl másolatának mentéséhez a helyi számítógépre _azuredeploy.js_ a következő néven:.

## <a name="modify-the-template"></a>A sablon módosítása

A meglévő sablont két helyen kell módosítania:

* Adjon hozzá egy tárfióknév paramétert. A felhasználók megadhatnak egy új tárfióknevet vagy egy meglévő tárfiók nevét is.
* Adjon hozzá egy nevű új paramétert `newOrExisting` . A központi telepítés ezzel a paraméterrel határozza meg, hogy létre kell-e hozni egy új Storage-fiókot, vagy egy meglévő Storage-fiókot kell használnia.

A következő eljárással hajthatja végre a módosításokat:

1. Nyissa meg az _azuredeploy.json_ fájlt a Visual Studio Code-ban.
1. Cserélje le a hármat a `variables('storageAccountName')` `parameters('storageAccountName')` teljes sablonra.
1. Távolítsa el az alábbi változódefiníciót:

    ![Képernyőkép, amely kiemeli az eltávolítandó változók definícióit.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Adja hozzá a következő két paramétert a paraméterek szakasz elejéhez:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Nyomja le az ALT + SHIFT + F billentyűkombinációt a sablon formázásához a Visual Studio Code-ban.

    A frissített paraméterdefiníció a következőképpen néz ki:

    ![Feltétel használata a Resource Managerben](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Adja hozzá a következő kódsort a tárfiók-definíció elejéhez.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    A feltétel ellenőrzi a paraméter értékét `newOrExisting` . Ha a paraméter értéke **new** (új), az üzemelő példány létrehozza a tárfiókot.

    A frissített tárfiók-definíció a következőképpen néz ki:

    ![A Storage-fiók frissített definícióját megjelenítő képernyőkép.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Frissítse a `storageUri` virtuális gép erőforrás-definíciójának tulajdonságát a következő értékkel:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Ez a módosítás szükséges, ha egy eltérő erőforráscsoport alá tartozó másik tárfiókot használ.

1. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Jelentkezzen be a [Azure Cloud Shellba](https://shell.azure.com)

1. Válassza ki a kívánt környezetet a **PowerShell** vagy a **bash** (a CLI esetében) lehetőség kiválasztásával a bal felső sarokban. A váltáskor a felületet újra kell indítani.

    ![Azure Portal Cloud Shell fájl feltöltése](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre. Lásd az előző képernyőképet. Válassza ki a korábbi szakaszban mentett fájlt. A fájl feltöltése után a `ls` parancs és a parancs használatával ellenőrizheti, `cat` hogy a fájl feltöltése sikeresen megtörtént-e.

1. Futtassa a következő PowerShell-szkriptet a sablon telepítéséhez.

    > [!IMPORTANT]
    > A tárfiók nevének egyedinek kell lennie az egész Azure rendszerben. A névnek csak kisbetűvel vagy számmal kell rendelkeznie. Nem lehet hosszabb 24 karakternél. A Storage-fiók neve a projekt neve, a **tároló** hozzáfűzve. Győződjön meg arról, hogy a projekt neve és a létrehozott Storage-fiók neve megfelel a Storage-fiók nevének.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > Az üzembe helyezés meghiúsul `newOrExisting` , ha az **új**, de a megadott Storage-fiókkal rendelkező Storage-fiók már létezik.

Próbáljon meg egy másik központi telepítést `newOrExisting` beállítani **meglévőre** , és adjon meg egy meglévő Storage-fiókot. Ha a művelet előtt szeretne létrehozni egy tárfiókot, tekintse meg a [tárfiók létrehozását](../../storage/common/storage-account-create.md) ismertető útmutatót.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Az erőforráscsoport törléséhez válassza a **kipróbálás** lehetőséget a Cloud Shell megnyitásához. A PowerShell-parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablaktáblára, majd válassza a **Beillesztés** lehetőséget.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy olyan sablont állított össze, amellyel a felhasználók maguk dönthetik el, hogy új tárfiókot hoznak létre, vagy egy meglévőt használnak. Ha szeretné megtudni, hogyan kérheti le az Azure Key Vaultban tárolt titkos kódokat, és hogyan használhatja azokat jelszóként sablonok üzembe helyezésekor, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A Key Vault integrálása a sablonok üzembe helyezésekor](./template-tutorial-use-key-vault.md)
