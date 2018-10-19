---
title: Függő erőforrásokkal ellátott Azure Resource Manager-sablonok létrehozása | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre több erőforrással ellátott Azure Resource Manager-sablont, valamint hogyan helyezheti üzembe azt az Azure Portal használatával.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fe6313c059a1dd1050240ead5f7ca8e3e1512aa6
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584513"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Oktatóanyag: Függő erőforrásokkal ellátott Azure Resource Manager-sablonok létrehozása

Ismerje meg, hogyan hozhat létre több erőforrás üzembe helyezésére alkalmas Azure Resource Manager-sablont.  A sablon létrehozását követően az üzembe helyezés az Azure Portal Cloud Shelljének használatával történik.

Az oktatóanyag során egy tárfiókot, egy virtuális gépet, egy virtuális hálózatot és néhány egyéb függő erőforrást fog létrehozni. Bizonyos erőforrások nem helyezhetők üzembe addig, amíg egy másik erőforrás létre nem lett hozva. Nem hozhat létre például virtuális gépet addig, amíg annak tárfiókja és hálózati adaptere létre nem lett hozva. Ezt a kapcsolatot úgy definiáljuk, hogy egy adott erőforrás más erőforrásokkal áll függőségi viszonyban. A Resource Manager kiértékeli az erőforrások közötti függőségeket, majd azokat függőségi sorrendben üzembe helyezi. Ha az erőforrások között nincs függőségi viszony, akkor a Resource Manager párhuzamosan helyezi üzembe azokat. További információkat [az erőforrások Azure Resource Manager-sablonokban történő üzembehelyezési sorrendjének meghatározását](./resource-group-define-dependencies.md) ismertető témakörben talál.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon vizsgálata
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/).
* Resource Manager Tools bővítmény.  Lásd [a bővítmény telepítését](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) ismertető részt.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure-beli gyorsindítási sablonok a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.

## <a name="explore-the-template"></a>A sablon vizsgálata

Amikor ebben a szakaszban a sablont vizsgálja, próbálja megválaszolni a következő kérdéseket:

- Hány Azure-erőforrás van meghatározva ebben a sablonban?
- Az egyik erőforrás egy Azure-tárfiók.  A definíció hasonlít a legutóbbi oktatóanyagban használtra?
- Hogyan tudja megkeresni a sablonban meghatározott erőforrásokhoz a sablonreferenciákat?
- Hogyan tudja megkeresni az erőforrások függőségeit?

1. A Visual Studio Code-ban csukja össze az elemeket mindaddig, amíg csak az első szintű és a **resources** (erőforrások) alatt lévő második szintű elemek lesznek láthatók:

    ![Visual Studio Code – Azure Resource Manager-sablonok](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    A sablon öt erőforrást határoz meg.
2. Bontsa ki az első erőforrást. Ez egy tárfiók. A definíciónak az előző oktatóanyag elején használttal azonosnak kell lennie.

    ![Visual Studio Code – Azure Resource Manager-sablonok, tárfiók-definíciók](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Bontsa ki a második erőforrást. Az erőforrástípus **Microsoft.Network/publicIPAddresses**. A sablonreferencia megkereséséhez lépjen a [sablonreferenciához](https://docs.microsoft.com/azure/templates/), majd a **Filter by title (Szűrés cím alapján)** mezőben adja meg a **nyilvános IP-címet** vagy a **nyilvános IP-címeket**. Hasonlítsa össze az erőforrás-definíciót a sablonreferenciával.

    ![Visual Studio Code – Azure Resource Manager-sablonok, nyilvános IP-cím definíciója](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. A sablonban meghatározott további erőforrások sablonreferenciáinak megkereséséhez ismételje meg az utolsó lépést.  Hasonlítsa össze az erőforrás-definíciókat a referenciákkal.
5. Bontsa ki a negyedik erőforrást:

    ![Visual Studio Code – Azure Resource Manager-sablonok – dependsOn](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    A dependsOn elem lehetővé teszi, hogy egy adott erőforrást egy vagy több erőforrástól függőként lehessen meghatározni. Ebben a példában ez az erőforrás a networkInterface.  Ez az erőforrás a következő két másik erőforrástól függ:

    * publicIPAddress
    * virtualNetwork

6. Bontsa ki az ötödik erőforrást. Ez az erőforrás egy virtuális gép. Ez az erőforrás a következő két másik erőforrástól függ:

    * storageAccount
    * networkInterface

A következő ábrán az erőforrások és az ehhez a sablonhoz tartozó függőségi adatok láthatók:

![Visual Studio Code – Azure Resource Manager-sablonok – függőségi ábra](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

A függőségek megadásával a Resource Manager már hatékonyan képes üzembe helyezni a megoldást. Mivel nincs közöttük függőség, a tárfiók, a nyilvános IP-cím és a virtuális hálózat párhuzamosan lesz létrehozva. A nyilvános IP-cím és a virtuális hálózat után a hálózati adapter üzembe helyezése következik. A Resource Manager azt követően helyezi üzembe helyezve, hogy az összes többi erőforrás üzembe helyezése megtörtént.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablonok üzembe helyezésének számos módszere létezik.  Ebben az oktatóanyagban az Azure Portal Cloud Shelljét fogja használni.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
2. Válassza ki a jobb felső sarokban a **Cloud Shell** elemet az alábbi képen látható módon:

    ![Azure Portal – Cloud Shell](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. Kattintson a Cloud Shell bal felső sarkában található **PowerShell** elemre.  Ebben az oktatóanyagban a PowerShellt fogja használni.
4. Kattintson az **Újraindítás** lehetőségre.
5. A Cloud Shellben kattintson a **Fájl feltöltése** lehetőségre:

    ![Azure Portal – Cloud Shell – Fájl feltöltése](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. Válassza ki az oktatóanyag korábbi részében mentett fájlt. Alapértelmezés szerint a fájl neve a következő: **azuredeploy.json**.  Ha ilyen néven már létezik egy fájl, akkor a rendszer értesítés nélkül felülírja a régi fájlt.
7. A fájl sikeres feltöltésének ellenőrzéséhez futtassa az alábbi parancsot a Cloud Shellben. 

    ```shell
    ls
    ```

    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    A képernyőképen a következő fájlnév látható: azuredeploy.json.

8. A JSON-fájl tartalmának ellenőrzéséhez futtassa az alábbi parancsot a Cloud Shellben:

    ```shell
    cat azuredeploy.json
    ```
9. Futtassa az alábbi PowerShell-parancsokat a Cloud Shellben:

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    Az alábbi képernyőképen egy üzembehelyezési minta látható:

    ![Azure Portal – Cloud Shell – Sablon üzembe helyezése](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    A képernyőképen az alábbi értékek szerepelnek:

    * **$resourceGroupName**: myresourcegroup0710. 
    * **$location**: eastus2
    * **&lt;DeployName>**: mydeployment0710
    * **&lt;TemplateFile>**: azuredeploy.json
    * **Sablonparaméter**ek:

        * **adminUsername**: JohnDole
        * **adminPassword**: Pass@word123
        * **dnsLabelPrefix**: myvm0710

10. Az alábbi PowerShell-parancs futtatásával megjelenítheti az újonnan létrehozott virtuális gépet:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    A sablonban szereplő virtuálisgépnév (**SimpleWinVM**) nem módosítható.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

A jelen oktatóanyagban létrehoz és üzembe helyez egy virtuális gép, egy virtuális hálózat és a függő erőforrások létrehozására szolgáló sablont. Az Azure-erőforrások feltételek alapján való üzembe helyezésével kapcsolatban lásd:


> [!div class="nextstepaction"]
> [Feltételek használata](./resource-manager-tutorial-use-conditions.md)

