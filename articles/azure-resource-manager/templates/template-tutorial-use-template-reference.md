---
title: Sablonreferencia használata
description: Sablon létrehozásához használja a Azure Resource Manager sablon (ARM-sablon) hivatkozását.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a44852fb2f491dd949b58217eca3e4f3e392cf17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584136"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Oktatóanyag: az ARM-sablon referenciájának kihasználása

Megtudhatja, hogyan keresheti meg a sablon sémájának adatait, és hogyan hozhatja létre Azure Resource Manager-sablonok (ARM-sablonok) létrehozásához szükséges információkat.

Ebben az oktatóanyagban egy alapszintű sablont használ az Azure Gyorsindítás sablonjaiból. A sablon-referenciák dokumentációjának használatával testreszabhatja a sablont.

![Resource Manager-sablon referenciája a Storage-fiók üzembe helyezése](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon ismertetése
> * A sablonreferencia megkeresése
> * A sablon szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Lásd [a gyors útmutató: ARM-sablonok létrehozása a Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)-ban című témakört.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az [Azure Gyorsindítás sablonjai](https://azure.microsoft.com/resources/templates/) az ARM-sablonok tárháza. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. Az ebben a rövid útmutatóban használt sablon neve a következő: [Standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/). A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio Code-ból válassza a **fájl**  >  **megnyitott** fájl elemet.
1. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
1. Válassza a **fájl**  >  **Mentés másként** lehetőséget, hogy a fájlt _azuredeploy.jsa_ helyi számítógépre mentse.

## <a name="understand-the-schema"></a>A séma bemutatása

1. A Visual Studio Code-ból Összecsukja a sablont a gyökérszintű szintre. Az ekkor előálló legegyszerűbb struktúra a következő elemeket tartalmazza:

    ![Resource Manager-sablon – Legegyszerűbb struktúra](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * `$schema`: Itt adhatja meg a sablon nyelvének verzióját leíró JSON-sémafájl helyét.
    * `contentVersion`: adja meg az elem bármely értékét, hogy dokumentálja a sablon jelentős változásait.
    * `parameters`: Itt adhatja meg az erőforrás-telepítés testreszabásához az üzembe helyezéskor megadott értékeket.
    * `variables`: Itt adhatja meg a sablonban JSON-töredékként használt értékeket a sablon nyelvi kifejezésének egyszerűsítése érdekében.
    * `resources`: határozza meg az erőforráscsoport által üzembe helyezett vagy frissített erőforrástípusok típusát.
    * `outputs`: adja meg az üzembe helyezés után visszaadott értékeket.

1. Bontsa ki a elemet `resources` . Van `Microsoft.Storage/storageAccounts` definiálva erőforrás. Az SKU neve paraméter értéket használ. A paraméter neve `storageAccountType` .

    ![Resource Manager-sablon, tárfiók-definíció](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Kibontással `parameters` megtekintheti a `storageAccountType` definícióját. A paraméternek négy megengedett értéke van. Megkeresi a többi megengedett értéket, majd módosítja a paraméter definícióját.

    ![Resource Manager-sablon Storage-fiók erőforrásainak SKU-je](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>A sablonreferencia megkeresése

1. Tallózással keresse meg az [Azure-sablonok referenciáját](/azure/templates/).
1. A **szűrés cím szerint** mezőben adja meg a **Storage-fiókok** nevet, majd válassza ki az első **Storage-fiókot** a **> Storage** szolgáltatásban.

    ![Resource Manager-sablonreferencia – tárfiók](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Az erőforrás-szolgáltató általában több API-verzióval rendelkezik:

    ![Resource Manager-sablon hivatkozása a Storage-fiók verzióihoz](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. A bal oldali ablaktáblán válassza a **tárterület** alatt található **összes erőforrás** lehetőséget. Ezen az oldalon a Storage erőforrás-szolgáltató erőforrástípusok és verziói láthatók. Ajánlott a sablonban definiált erőforrástípusok legújabb API-verzióinak használata.

    ![Resource Manager-sablonok referenciái a Storage-fiókok típusaihoz](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Válassza ki az erőforrástípus legújabb verzióját `storageAccount` . A legújabb verzió **2019-06-01** , ha a cikk írása megtörténik. Győződjön meg arról, hogy ez a verzió megegyezik a sablonban a Storage-fiók erőforrásához használt verzióval. Ha frissíti az API-verziót, ellenőrizze, hogy az erőforrás-definíció megegyezik-e a sablon hivatkozásával.

1. Ezen a lapon a storageAccount erőforrástípus részletei láthatók. Például felsorolja az **SKU objektum** megengedett értékeit. A korábban megnyitott rövid útmutató sablonban több SKU szerepel. Testreszabhatja a gyors üzembe helyezési sablont, hogy az tartalmazza az összes rendelkezésre álló tárolási típust.

    ![Resource Manager-sablon referenciája a Storage-fiók SKU-ban](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>A sablon szerkesztése

A Visual Studio Code-ból adja hozzá a további Storage-fiókok típusait az alábbi képernyőképen látható módon:

![Resource Manager-sablonok tárolási fiókjának erőforrásai](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Jelentkezzen be a [Azure Cloud Shellba](https://shell.azure.com)

1. Válassza ki a kívánt környezetet a **PowerShell** vagy a **bash** (a CLI esetében) lehetőség kiválasztásával a bal felső sarokban.  A váltáskor a felületet újra kell indítani.

    ![Azure Portal Cloud Shell fájl feltöltése](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre. Lásd az előző képernyőképet. Válassza ki a korábbi szakaszban mentett fájlt. A fájl feltöltése után a `ls` parancs és a parancs használatával ellenőrizheti, `cat` hogy a fájl feltöltése sikeresen megtörtént-e.

1. A Cloud Shell futtassa a következő parancsokat. Válassza ki a megfelelő lapot a PowerShell-kód vagy a parancssori felület kód megjelenítéséhez.

   A sablon telepítésekor adja meg a `storageAccountType` paramétert egy újonnan hozzáadott értékkel, például **Standard_RAGRS**. A telepítés sikertelen lesz, ha az eredeti rövid útmutató sablont használta, mert **Standard_RAGRS** nem volt engedélyezett érték.

    # <a name="cli"></a>[Parancssori felület](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
1. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
1. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
1. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag azt ismertette, hogyan használhatja a sablonreferenciát egy létező sablon testreszabására. Több tárfiókpéldány létrehozása:

> [!div class="nextstepaction"]
> [Több példány létrehozása](./template-tutorial-create-multiple-instances.md)
