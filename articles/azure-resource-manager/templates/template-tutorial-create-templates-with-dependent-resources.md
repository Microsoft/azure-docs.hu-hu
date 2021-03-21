---
title: Sablon függő erőforrásokkal
description: Megtudhatja, hogyan hozhat létre Azure Resource Manager sablont (ARM-sablont) több erőforrással, és hogyan helyezheti üzembe a Azure Portal használatával
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a77f64e51a26e1f916f9f96704c55412a870a509
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673936"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Oktatóanyag: ARM-sablonok létrehozása függő erőforrásokkal

Megtudhatja, hogyan hozhat létre Azure Resource Manager sablont (ARM-sablont) több erőforrás üzembe helyezéséhez és a telepítési sorrend konfigurálásához. A sablon létrehozása után a sablont a Azure Portal Cloud Shell használatával helyezheti üzembe.

Az oktatóanyag során egy tárfiókot, egy virtuális gépet, egy virtuális hálózatot és néhány egyéb függő erőforrást fog létrehozni. Bizonyos erőforrások nem helyezhetők üzembe addig, amíg egy másik erőforrás létre nem lett hozva. Nem hozhat létre például virtuális gépet addig, amíg annak tárfiókja és hálózati adaptere létre nem lett hozva. Ezt a kapcsolatot úgy definiáljuk, hogy egy adott erőforrás más erőforrásokkal áll függőségi viszonyban. A Resource Manager kiértékeli az erőforrások közötti függőségeket, majd azokat függőségi sorrendben üzembe helyezi. Ha az erőforrások között nincs függőségi viszony, akkor a Resource Manager párhuzamosan helyezi üzembe azokat. További információ: [erőforrások üzembe helyezési sorrendjének meghatározása ARM-sablonokban](./define-resource-dependency.md).

![Resource Manager-sablon függő erőforrásainak telepítési sorrendi diagramja](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon vizsgálata
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

Az erőforrás-függőségeket lefedi Microsoft Learn modul esetében lásd: [összetett Felhőbeli központi telepítések kezelése speciális ARM-sablonok használatával](/learn/modules/manage-deployments-advanced-arm-template-features/).

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
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. Válassza a **fájl**  >  **Mentés másként** lehetőséget a fájl másolatának mentéséhez a helyi számítógépre _azuredeploy.js_ a következő néven:.

## <a name="explore-the-template"></a>A sablon vizsgálata

Amikor ebben a szakaszban a sablont vizsgálja, próbálja megválaszolni a következő kérdéseket:

* Hány Azure-erőforrás van meghatározva ebben a sablonban?
* Az egyik erőforrás egy Azure-tárfiók. A definíció hasonlít a legutóbbi oktatóanyagban használtra?
* Hogyan tudja megkeresni a sablonban meghatározott erőforrásokhoz a sablonreferenciákat?
* Hogyan tudja megkeresni az erőforrások függőségeit?

1. A Visual Studio Code-ból csukja össze az elemeket, amíg csak az első szintű elemeket és a második szintű elemeket látja `resources` :

    ![Visual Studio Code ARM-sablonok](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    A sablon hat erőforrást definiál:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. számítási/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     A sablon testreszabása előtt célszerű áttekinteni a sablon hivatkozását.

1. Bontsa ki az első erőforrást. Ez egy Storage-fiók. Hasonlítsa össze az erőforrás-definíciót a [sablon hivatkozásával](/azure/templates/Microsoft.Storage/storageAccounts).

    ![Visual Studio Code ARM-sablonok Storage-fiók definíciója](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Bontsa ki a második erőforrást. Az erőforrástípus `Microsoft.Network/publicIPAddresses`. Hasonlítsa össze az erőforrás-definíciót a [sablon hivatkozásával](/azure/templates/microsoft.network/publicipaddresses).

    ![Visual Studio Code ARM-sablonok nyilvános IP-cím definíciója](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Bontsa ki a harmadik erőforrást. Az erőforrástípus `Microsoft.Network/networkSecurityGroups`. Hasonlítsa össze az erőforrás-definíciót a [sablon hivatkozásával](/azure/templates/microsoft.network/networksecuritygroups).

    ![Visual Studio Code ARM-sablonok hálózati biztonsági csoport definíciója](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Bontsa ki a negyedik erőforrást. Az erőforrástípus `Microsoft.Network/virtualNetworks`:

    ![Visual Studio Code ARM-sablonok virtuális hálózati dependsOn](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    Az `dependsOn` elem lehetővé teszi, hogy egy erőforrást egy vagy több erőforrástól függőként definiáljon. Ez az erőforrás egy másik erőforrástól függ:

    * `Microsoft.Network/networkSecurityGroups`

1. Bontsa ki az ötödik erőforrást. Az erőforrástípus `Microsoft.Network/networkInterfaces`. Az erőforrás két másik erőforrástól függ:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Bontsa ki a hatodik erőforrást. Ez az erőforrás egy virtuális gép. Ez az erőforrás a következő két másik erőforrástól függ:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

A következő ábrán az erőforrások és az ehhez a sablonhoz tartozó függőségi adatok láthatók:

![A Visual Studio Code ARM-sablonok függőségi diagramja](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

A függőségek megadásával a Resource Manager már hatékonyan képes üzembe helyezni a megoldást. Mivel nincs közöttük függőség, a tárfiók, a nyilvános IP-cím és a virtuális hálózat párhuzamosan lesz létrehozva. A nyilvános IP-cím és a virtuális hálózat után a hálózati adapter üzembe helyezése következik. A Resource Manager azt követően helyezi üzembe helyezve, hogy az összes többi erőforrás üzembe helyezése megtörtént.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Jelentkezzen be a [Azure Cloud Shellba](https://shell.azure.com)

1. Válassza ki a kívánt környezetet a **PowerShell** vagy a **bash** (a CLI esetében) lehetőség kiválasztásával a bal felső sarokban.  A váltáskor a felületet újra kell indítani.

    ![Azure Portal Cloud Shell fájl feltöltése](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre. Lásd az előző képernyőképet. Válassza ki a korábban mentett fájlt. A fájl feltöltése után a `ls` parancs és a parancs használatával ellenőrizheti, `cat` hogy a fájl feltöltése sikeresen megtörtént-e.

1. Futtassa a következő PowerShell-szkriptet a sablon telepítéséhez.

    # <a name="cli"></a>[Parancssori felület](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

1. RDP-kapcsolattal csatlakozzon a virtuális géphez, hogy ellenőrizze, sikeresen létrejött-e a virtuális gép.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét. Az erőforráscsoport összesen hat erőforrást fog látni.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott és üzembe helyezett egy virtuális gép, egy virtuális hálózat és a függő erőforrások létrehozására szolgáló sablont. A telepítési parancsfájlok az üzembe helyezés előtti és utáni műveletek végrehajtásához való használatáról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Üzembehelyezési szkript használata](./template-tutorial-deployment-script.md)
